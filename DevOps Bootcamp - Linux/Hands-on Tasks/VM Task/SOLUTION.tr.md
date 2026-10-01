<div align="center">

<img src="./assets/banner.svg" alt="VM Task banner" width="100%">

# 🛠️ VM Task — Çözüm

[🇬🇧 English](./SOLUTION.md) · **🇹🇷 Türkçe**

[⬅️ README](./README.tr.md) · [📋 Görev](./TASK.tr.md)

</div>

Her görev için yapılan işlem, komutlar, kısa gerekçe ve ekran görüntüsü. Görevin kendisi için bkz. [TASK.tr.md](./TASK.tr.md).

## İçindekiler

- [Ortam](#ortam)
- [Ön hazırlık](#ön-hazırlık)
- [1. Change of VM hostname](#1-change-of-vm-hostname)
- [2. User Creation](#2-user-creation)
- [3. LVM Configuration](#3-lvm-configuration)
- [4. Sudo Rights](#4-sudo-rights)
- [5. Swap Creation](#5-swap-creation)
- [6. Docker Installation](#6-docker-installation)
- [7. NGINX Installation](#7-nginx-installation)
- [8. Cron Job](#8-cron-job)
- [9. NGINX Logrotate](#9-nginx-logrotate)
- [10. Make an immutable file /immutable.txt](#10-make-an-immutable-file-immutabletxt)
- [11. Bash Alias Creating](#11-bash-alias-creating)
- [12. Static Route](#12-static-route)
- [Sonuç](#sonuç)
- [Karşılaşılan sorunlar](#karşılaşılan-sorunlar)

## Ortam

|  |  |
|---|---|
| Ana makine | Windows, PowerShell |
| Sanallaştırma | VirtualBox 7.2 + Vagrant |
| Box | `ubuntu/focal64` |
| VM işletim sistemi | Ubuntu 20.04.6 LTS (x86_64) |
| VM kaynakları | 1 CPU, 2 GB RAM |
| Diskler | `sda` 40 GB (sistem), `sdb` 10 MB (cloud-init), `sdc` 1 GB (LVM için) |

## Ön hazırlık

VM, bu klasördeki [Vagrantfile](./Vagrantfile) ile oluşturuldu (PowerShell):

```powershell
$env:VAGRANT_EXPERIMENTAL="disks"   # required for the disks feature
vagrant up
vagrant ssh
```

VM içinde başlangıç durumu ve gerekli araçlar:

```bash
lsblk                                   # the 1G disk sdc must be visible
swapon --show                           # must be empty (no swap)
sudo apt update && sudo apt install -y jq
cp /vagrant/checker ~ && chmod +x ~/checker
```

`/vagrant`, ana makinedeki proje klasörünün VM içindeki paylaşımıdır. Checker dosyası buradan kopyalandı.

<p align="center"><img src="./screenshots/00-prereq-lsblk.png" alt="lsblk çıktısı: sdc 1G ikinci disk" width="85%"><br><sub><em>lsblk çıktısı: sdc 1G ikinci disk</em></sub></p>
<p align="center"><img src="./screenshots/00-prereq-no-swap.png" alt="Başlangıçta swap yok" width="85%"><br><sub><em>Başlangıçta swap yok</em></sub></p>

---

## 1. Change of VM hostname

| ⭐ Puan | 🧪 Test |
|---|---|
| 2 | 1–2 |

**🎯 Hedef:** Hostname `student`, `127.0.0.1`'e çözülsün, kalıcı olsun.

```bash
echo '127.0.0.1 student' | sudo tee -a /etc/hosts
sudo hostnamectl set-hostname student
sudo sed -i 's/^preserve_hostname: false/preserve_hostname: true/' /etc/cloud/cloud.cfg
grep preserve_hostname /etc/cloud/cloud.cfg
```

**💡 Neden böyle?**

- Önce `/etc/hosts`'a yeni ismi ekliyoruz. Aksi halde sonraki `sudo` komutları `unable to resolve host` uyarısı verir.
- `hostnamectl set-hostname` hostname'i kalıcı olarak `/etc/hostname`'e yazar.
- Vagrant/cloud-init imajlarında `cloud-init` açılışta hostname'i sıfırlayabilir. `preserve_hostname: true` bunu engeller.

**🔍 Kontrol:** `hostname -s`, `-f` → `student`; `hostname -i` → `127.0.0.1`.

<p align="center"><img src="./screenshots/01-hostname-hosts-before.png" alt="Başlangıçtaki `/etc/hosts`" width="85%"><br><sub><em>Başlangıçtaki `/etc/hosts`</em></sub></p>

<p align="center"><img src="./screenshots/01-hostname-after.png" alt="Değişiklikten sonra" width="85%"><br><sub><em>Değişiklikten sonra</em></sub></p>

---

## 2. User Creation

| ⭐ Puan | 🧪 Test |
|---|---|
| 6 | 3–8 |

**🎯 Hedef:** `student`, UID 1040, ana grup `student_group` (GID 1050), home `/home/student_home`, ek grup `cdrom`.

```bash
sudo groupadd -g 1050 student_group
sudo useradd -u 1040 -g student_group -G cdrom -d /home/student_home -m -s /bin/bash student
sudo passwd student
id student
```

**💡 Neden böyle?**

- Ana grup, kullanıcıdan **önce** var olmalı. Bu yüzden `groupadd` önce çalışır.
- `-g` ana grup, `-G` ek gruplar, `-d` home dizini, `-m` home dizinini oluşturur.
- Şifre, sonraki görevlerde (`su`, `sudo` testi) lazım olacağı için verildi.

**🔍 Kontrol:** Beklenen çıktı: `uid=1040(student) gid=1050(student_group) groups=1050(student_group),24(cdrom)`

<p align="center"><img src="./screenshots/02-user-creation.png" alt="Kullanıcı oluşturma ve `id` çıktısı" width="85%"><br><sub><em>Kullanıcı oluşturma ve `id` çıktısı</em></sub></p>

---

## 3. LVM Configuration

| ⭐ Puan | 🧪 Test |
|---|---|
| 4 | 9–12 |

**🎯 Hedef:** `sdc` üzerinde PV, VG `student`, LV `student` (boş alanın %50'si), ext4, `/lvm/student`'e UUID ile kalıcı mount.

```bash
sudo pvcreate /dev/sdc
sudo vgcreate student /dev/sdc
sudo lvcreate -l 50%FREE -n student student
sudo mkfs.ext4 /dev/student/student
sudo mkdir -p /lvm/student

UUID=$(sudo blkid -s UUID -o value /dev/student/student)
echo "UUID=$UUID /lvm/student ext4 defaults 0 2" | sudo tee -a /etc/fstab
sudo mount -a
```

**💡 Neden böyle?**

- LVM katmanları sırayla kurulur: **PV** (fiziksel disk) → **VG** (disk havuzu) → **LV** (kullanılan mantıksal bölüm) → dosya sistemi.
- `-l 50%FREE`: volume group'taki boş alanın yarısı (yaklaşık 508 MiB).
- fstab'a **UUID** yazıyoruz çünkü `/dev/sdX` adları yeniden başlatmada değişebilir, UUID değişmez.
- `mount -a` fstab'daki tüm kayıtları dener. Hata vermemesi, satırın doğru olduğunu gösterir.

```mermaid
flowchart LR
    D["💽 /dev/sdc<br/>1 GB"] --> PV["PV"] --> VG["VG<br/>student"] --> LV["LV student<br/>50%FREE ≈ 508 MiB"] --> FS["ext4"] --> M["📁 /lvm/student<br/>(fstab, UUID)"]
```

**🔍 Kontrol:** `lsblk | grep student-student`, `mount | grep student-student`, `sudo lvdisplay`.

<p align="center"><img src="./screenshots/03-lvm-create.png" alt="PV, VG, LV oluşturma ve mkfs" width="85%"><br><sub><em>PV, VG, LV oluşturma ve mkfs</em></sub></p>

<p align="center"><img src="./screenshots/03-lvm-verify.png" alt="fstab kaydı, mount ve lvdisplay" width="85%"><br><sub><em>fstab kaydı, mount ve lvdisplay</em></sub></p>

---

## 4. Sudo Rights

| ⭐ Puan | 🧪 Test |
|---|---|
| 1 | 13 |

**🎯 Hedef:** `student`, `apt install` ve `apt-get install`'ı şifresiz, diğer sudo komutlarını şifreyle çalıştırsın. Varsayılan `/etc/sudoers` değişmesin.

```bash
which apt apt-get                          # /usr/bin/apt, /usr/bin/apt-get
sudo visudo -f /etc/sudoers.d/student
```

**📄 /etc/sudoers.d/student:**

```text
student ALL=(ALL) ALL
student ALL=(ALL) NOPASSWD: /usr/bin/apt install *, /usr/bin/apt-get install *
```

**💡 Neden böyle?**

- **Best practice:** `/etc/sudoers`'a dokunmak yerine `/etc/sudoers.d/` altında kullanıcıya özel dosya kullanılır. Paket güncellemeleri ana dosyayı değiştirse bile ayar korunur.
- `visudo -f` dosyayı kaydederken sözdizimini kontrol eder. Hatalı kayıt sudo'yu bozabilir, `visudo` bunu engeller.
- Sudoers'ta **son eşleşen kural kazanır**. Genel kural (`ALL`, şifreli) üstte, özel kural (`NOPASSWD`, sadece apt install) altta olmalı.
- Komutların tam yolu (`/usr/bin/apt`) yazılır, böylece başka bir `apt` çalıştırılamaz.
- Dosya izni `440` (`-r--r-----`) olmalı. `visudo` bunu kendisi ayarlar.

> [!NOTE]
> `/etc/sudoers.d/` dizinini sadece root okuyabilir. Normal kullanıcıyla `ls -l /etc/sudoers.d/student` yazınca "Permission denied" gelmesi normaldir, başına `sudo` gerekir.

<p align="center"><img src="./screenshots/04-sudo-sudoers-file.png" alt="sudoers.d dosyası ve içeriği" width="85%"><br><sub><em>sudoers.d dosyası ve içeriği</em></sub></p>

**Test** (`su - student` ile, her adım arasında `sudo -k`):

- `sudo echo` → şifre **sorar**
- `sudo apt install tree` → şifre **sormaz**
- `sudo apt update` → şifre **sorar**

<p align="center"><img src="./screenshots/04-sudo-test-nopasswd.png" alt="sudo echo şifre sorar, apt install sormaz" width="85%"><br><sub><em>sudo echo şifre sorar, apt install sormaz</em></sub></p>

<p align="center"><img src="./screenshots/04-sudo-test-apt-update.png" alt="sudo apt update şifre sorar" width="85%"><br><sub><em>sudo apt update şifre sorar</em></sub></p>

---

## 5. Swap Creation

| ⭐ Puan | 🧪 Test |
|---|---|
| 2 | 14–15 |

**🎯 Hedef:** 1 GB `/swapfile`, reboot sonrası da aktif.

```bash
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
sudo swapon --show
free -h | grep -i swap
```

**💡 Neden böyle?**

- `fallocate` istenen boyutta dosyayı hızlıca ayırır.
- `chmod 600`: swap dosyası sadece root'a açık olmalı, aksi halde bellek içeriği başkalarınca okunabilir.
- `mkswap` dosyayı swap alanı olarak biçimlendirir, `swapon` etkinleştirir.
- fstab kaydı, swap'ın her açılışta otomatik etkinleşmesini sağlar.

**🔍 Kontrol:** `swapon --show` → `/swapfile file 1024M`, `free -h` → `Swap: 1.0Gi`.

<p align="center"><img src="./screenshots/05-swap-create.png" alt="Swap dosyası oluşturma ve doğrulama" width="85%"><br><sub><em>Swap dosyası oluşturma ve doğrulama</em></sub></p>

---

## 6. Docker Installation

| ⭐ Puan | 🧪 Test |
|---|---|
| 1 | 16 |

**🎯 Hedef:** Docker repository'sinden Docker **19.03.15**, `student` kullanıcısı çalıştırabilsin.

Başlangıçta Docker kurulu değil:

<p align="center"><img src="./screenshots/06-docker-before.png" alt="Başlangıçta Docker kurulu değil" width="85%"><br><sub><em>Başlangıçta Docker kurulu değil</em></sub></p>

```bash
# 1. Prerequisites
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release

# 2. Docker GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# 3. Docker repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list
sudo apt update

# 4. Find the version and install it
apt-cache madison docker-ce | grep 19.03.15
VER="5:19.03.15~3-0~ubuntu-focal"
sudo apt install -y docker-ce=$VER docker-ce-cli=$VER containerd.io

# 5. Add users to the docker group
sudo usermod -aG docker student
sudo usermod -aG docker vagrant
```

**💡 Neden böyle?**

- Ubuntu'nun kendi `docker.io` paketi yerine **resmi Docker repository'si** isteniyor, bu yüzden repo ve GPG anahtarı eklenir. `signed-by` ile paketlerin imzası doğrulanır.
- `apt-cache madison` repoda hangi sürümlerin olduğunu listeler, tam sürüm adı (`5:19.03.15~3-0~ubuntu-focal`) buradan alınır.
- Belirli bir sürüm istendiği için `docker-ce` ve `docker-ce-cli` aynı sürüme sabitlenir.
- `docker` grubundaki kullanıcılar `/var/run/docker.sock`'a erişebilir, yani `sudo` olmadan Docker çalıştırabilir. Grup değişikliği **yeni oturumda** geçerli olur (`exit` ve tekrar giriş).

**🔍 Kontrol:** Hem Client hem Server `19.03.15` göstermeli.

<p align="center"><img src="./screenshots/06-docker-version-student.png" alt="`student` kullanıcısı ile docker version" width="85%"><br><sub><em>`student` kullanıcısı ile docker version</em></sub></p>

<p align="center"><img src="./screenshots/06-docker-version-vagrant.png" alt="`vagrant` kullanıcısı ile docker version" width="85%"><br><sub><em>`vagrant` kullanıcısı ile docker version</em></sub></p>

---

## 7. NGINX Installation

| ⭐ Puan | 🧪 Test |
|---|---|
| 5 | 17–21 |

**🎯 Hedef:** NGINX 8080 portunda çalışsın, statik sayfa `name`, `os`, `ip`, `cpu`, `memory` alanlarıyla JSON döndürsün.

```bash
sudo apt install -y nginx

# Change the port from 80 to 8080
sudo sed -i 's/listen 80 default_server;/listen 8080 default_server;/; s/listen \[::\]:80 default_server;/listen [::]:8080 default_server;/' /etc/nginx/sites-available/default
grep listen /etc/nginx/sites-available/default

# JSON page (values are filled in automatically)
sudo tee /var/www/html/index.html > /dev/null <<EOF
{
  "name": "student",
  "os": "$(. /etc/os-release && echo $PRETTY_NAME)",
  "ip": "$(hostname -I | awk '{print $1}')",
  "cpu": "$(nproc)",
  "memory": "$(free -m | awk '/Mem:/ {printf "%.1f", $2/1024}')"
}
EOF

sudo nginx -t
sudo systemctl restart nginx
sudo systemctl enable nginx
```

**💡 Neden böyle?**

- Varsayılan site `listen 80` ile gelir. IPv4 (`listen 8080`) ve IPv6 (`listen [::]:8080`) satırlarının ikisi de değiştirilir.
- `index.html` NGINX'in varsayılan sayfa dizinindedir (`/var/www/html`). Dosya içindeki `$(...)` ifadeleri, dosya yazılırken çalıştırılıp gerçek değerlerle değiştirilir (heredoc'ta `EOF` tırnaksız olduğu için).
- `nginx -t` yapılandırma hatalarını servisi yeniden başlatmadan yakalar.
- `enable` servisin açılışta otomatik başlamasını sağlar.

**🔍 Kontrol:**

```bash
sudo ss -lntp | grep 8080
curl student:8080
curl -s student:8080 | jq .
```

<p align="center"><img src="./screenshots/07-nginx-install.png" alt="NGINX kurulumu" width="85%"><br><sub><em>NGINX kurulumu</em></sub></p>

<p align="center"><img src="./screenshots/07-nginx-port-and-page.png" alt="Port değişikliği ve JSON sayfasının oluşturulması" width="85%"><br><sub><em>Port değişikliği ve JSON sayfasının oluşturulması</em></sub></p>

<p align="center"><img src="./screenshots/07-nginx-test-and-port.png" alt="nginx -t, restart ve 8080 portu dinliyor" width="85%"><br><sub><em>nginx -t, restart ve 8080 portu dinliyor</em></sub></p>

<p align="center"><img src="./screenshots/07-nginx-curl.png" alt="curl ve jq ile JSON çıktısı" width="85%"><br><sub><em>curl ve jq ile JSON çıktısı</em></sub></p>

---

## 8. Cron Job

| ⭐ Puan | 🧪 Test |
|---|---|
| 1 | 22 |

**🎯 Hedef:** `/etc/nginx/nginx.conf` dosyasını `/etc/nginx/conf.d/nginx-<timestamp>.conf.bak` olarak yedekle. Ek script kullanma.

Başlangıçta `conf.d` boş:

<p align="center"><img src="./screenshots/08-cron-before.png" alt="Başlangıçta `conf.d` boş" width="85%"><br><sub><em>Başlangıçta `conf.d` boş</em></sub></p>

```bash
systemctl is-active cron
(sudo crontab -l 2>/dev/null; echo '* * * * * cp /etc/nginx/nginx.conf /etc/nginx/conf.d/nginx-$(date +\%s).conf.bak') | sudo crontab -
sudo crontab -l
sleep 70
ls /etc/nginx/conf.d/
```

**💡 Neden böyle?**

- `/etc/nginx/conf.d/` dizinine sadece root yazabilir. Bu yüzden iş **root'un crontab'ına** (`sudo crontab`) eklenir.
- `date +%s`, 1970-01-01'den bu yana geçen saniyeyi verir (Unix timestamp).
- Crontab'ta `%` karakteri özel anlam taşıdığı için `\%` yazılır.
- Yedek dosyaları `.conf.bak` ile bittiği için NGINX bunları yapılandırmaya dahil etmez (`conf.d/*.conf` sadece `.conf` ile bitenleri okur).
- Test için `* * * * *` (her dakika) kullanıldı, bu yüzden bir dakika içinde sonuç görülür.

<p align="center"><img src="./screenshots/08-cron-after-and-logrotate-before.png" alt="Crontab satırı, oluşan yedekler" width="85%"><br><sub><em>Crontab satırı, oluşan yedekler</em></sub></p>

> [!TIP]
> Üretim ortamında her dakika yedek almak yerine saatlik/günlük sıklık (`0 * * * *`) daha mantıklıdır, aksi halde dizin hızla dolar.

---

## 9. NGINX Logrotate

| ⭐ Puan | 🧪 Test |
|---|---|
| 1 | 23 |

**🎯 Hedef:** NGINX logları günlük yerine **haftalık** döndürülsün.

Önceki ayar (`daily`, `rotate 14`) bir önceki görevin son görselinin alt kısmında görünüyor.

```bash
sudo sed -i 's/\bdaily\b/weekly/' /etc/logrotate.d/nginx
grep -E 'daily|weekly|rotate' /etc/logrotate.d/nginx
sudo logrotate -d /etc/logrotate.d/nginx 2>&1 | grep -i rotating
```

**💡 Neden böyle?**

- `logrotate` ayarları `/etc/logrotate.d/` altındaki dosyalardadır. NGINX için `daily` ifadesi `weekly` ile değiştirilir.
- `\b` kelime sınırıdır, sadece `daily` kelimesini hedefler.
- `logrotate -d` (debug) hiçbir şeyi değiştirmeden kuralın nasıl yorumlandığını gösterir.

**🔍 Kontrol:** Beklenen: `rotating pattern: /var/log/nginx/*.log  weekly (14 rotations)`

<p align="center"><img src="./screenshots/09-logrotate-after.png" alt="logrotate haftalık yapıldı" width="85%"><br><sub><em>logrotate haftalık yapıldı</em></sub></p>

---

## 10. Make an immutable file /immutable.txt

| ⭐ Puan | 🧪 Test |
|---|---|
| 1 | 24 |

**🎯 Hedef:** Sahibi `student`, grubu `cdrom`, herkese tüm izinler, kimse silemesin.

```bash
sudo touch /immutable.txt
sudo chown student:cdrom /immutable.txt
sudo chmod 777 /immutable.txt
sudo chattr +i /immutable.txt

ls -l /immutable.txt
lsattr /immutable.txt
sudo rm /immutable.txt        # Operation not permitted
```

**💡 Neden böyle?**

- `chattr +i`, dosyaya **immutable** (değiştirilemez) bayrağı koyar. Bu durumda dosya **root dahil kimse tarafından** silinemez, yeniden adlandırılamaz veya değiştirilemez.
- Bayrak verildikten sonra sahip ve izinler de değiştirilemez, bu yüzden `chattr +i` **en son** çalıştırılır.
- Bayrağı kaldırmak için `sudo chattr -i /immutable.txt` gerekir.

<p align="center"><img src="./screenshots/10-immutable-file.png" alt="Immutable dosya: izinler, lsattr ve silme denemesi" width="85%"><br><sub><em>Immutable dosya: izinler, lsattr ve silme denemesi</em></sub></p>

---

## 11. Bash Alias Creating

| ⭐ Puan | 🧪 Test |
|---|---|
| 1 | 25 |

**🎯 Hedef:** `student` için `eip` alias'ı dış IP'yi göstersin, kalıcı olsun.

Alias başlangıçta yok:

<p align="center"><img src="./screenshots/11-alias-before.png" alt="Alias başlangıçta yok" width="85%"><br><sub><em>Alias başlangıçta yok</em></sub></p>

```bash
echo "alias eip='curl -s ifconfig.me; echo'" | sudo tee -a /home/student_home/.bashrc
grep -n eip /home/student_home/.bashrc
su - student
eip
```

**💡 Neden böyle?**

- `student`'ın home dizini `/home/student_home` olduğu için alias oraya yazılır (`/home/student/.bashrc` değil).
- `.bashrc` her interaktif Bash oturumunda okunur, bu yüzden alias reboot sonrası da çalışır.
- `curl -s ifconfig.me` dış IP'yi döndürür. Servis satır sonu vermediği için sonuna `echo` eklenir.
- Alias'lar sadece **interaktif** oturumda çalışır, bu yüzden test `su - student` ile yapılır.

<p align="center"><img src="./screenshots/11-alias-after.png" alt="eip dış IP adresini gösterir" width="85%"><br><sub><em>eip dış IP adresini gösterir</em></sub></p>

---

## 12. Static Route

| ⭐ Puan | 🧪 Test |
|---|---|
| 2 | 26–27 |

**🎯 Hedef:** `8.8.8.8`'e giden paketler `4.4.4.4` üzerinden gitsin, kalıcı olsun. Diğer adresler etkilenmesin.


Önce `yq` kuruldu ve route öncesi durum kontrol edildi:

```bash
sudo wget -qO /usr/local/bin/yq https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64
sudo chmod a+x /usr/local/bin/yq
yq --version
ping 8.8.8.8
```

<p align="center"><img src="./screenshots/12-route-before-yq-ping.png" alt="yq kurulumu ve route öncesi 8.8.8.8 ping" width="85%"><br><sub><em>yq kurulumu ve route öncesi 8.8.8.8 ping</em></sub></p>
<p align="center"><img src="./screenshots/12-route-before-google.png" alt="Route öncesi www.google.com ping" width="85%"><br><sub><em>Route öncesi www.google.com ping</em></sub></p>

Arayüz adı (`enp0s3`) ve mevcut netplan dosyası:

```bash
ip route show default
sudo cat /etc/netplan/50-cloud-init.yaml
```

<p align="center"><img src="./screenshots/12-route-netplan-before.png" alt="Mevcut netplan yapılandırması" width="85%"><br><sub><em>Mevcut netplan yapılandırması</em></sub></p>

Netplan dosyasına `routes` bloğu eklenir:

```bash
sudo cp /etc/netplan/50-cloud-init.yaml ~/50-cloud-init.yaml.bak
sudo tee /etc/netplan/50-cloud-init.yaml > /dev/null <<'EOF'
network:
    ethernets:
        enp0s3:
            dhcp4: true
            dhcp6: true
            match:
                macaddress: 02:1a:a2:1e:9d:b7
            set-name: enp0s3
            routes:
              - to: 8.8.8.8/32
                via: 4.4.4.4
                on-link: true
    version: 2
EOF
```

<p align="center"><img src="./screenshots/12-route-netplan-edit.png" alt="Netplan dosyasının yeni içeriği" width="85%"><br><sub><em>Netplan dosyasının yeni içeriği</em></sub></p>

cloud-init'in dosyayı yeniden yazmasını engelle ve güvenli uygula:

```bash
echo 'network: {config: disabled}' | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
sudo netplan generate
sudo netplan try
```

<p align="center"><img src="./screenshots/12-route-netplan-try.png" alt="cloud-init ağ yapılandırması kapatıldı, netplan try" width="85%"><br><sub><em>cloud-init ağ yapılandırması kapatıldı, netplan try</em></sub></p>

**💡 Neden böyle?**

- **Kalıcılık için netplan:** `ip route add` ile eklenen route reboot sonrası kaybolur (geçici çözümdür). Netplan yapılandırması kalıcıdır ve uygulandığında route'u aynı anda route tablosuna da ekler, bu yüzden iki test de bu yöntemle geçer.
- `to: 8.8.8.8/32` sadece o tek adresi hedefler. `/32` tek bir IP demektir, bu yüzden diğer adresler etkilenmez.
- `on-link: true`: `4.4.4.4` yerel ağda olmadığı için ağ geçidinin doğrudan bağlı olduğunu söylemek gerekir. Aksi halde "invalid gateway" hatası alınır.
- `4.4.4.4` yerel ağdaki bir ağ geçidi değildir, ARP isteğine cevap vermez. Bu yüzden `8.8.8.8`'e giden paketler ulaşamaz. Bu, görevin beklenen sonucudur (`Destination Host Unreachable`).
- `netplan try`: yeni ayarı dener, onaylanmazsa **otomatik geri alır**. Hatalı bir ayarla SSH bağlantısını kaybetme riskini azaltır.
- `99-disable-network-config.cfg`, cloud-init'in `50-cloud-init.yaml` dosyasını her açılışta yeniden üretmesini engeller.

**Paket hangi yoldan gider?**

```mermaid
flowchart TD
    P["📦 Packet"] --> Q{"Hedef?"}
    Q -->|"8.8.8.8/32"| R["via 4.4.4.4 (on-link)"]
    R --> U["❌ ARP cevabı yok → Destination Host Unreachable"]
    Q -->|"diğer her şey"| G["default gateway"]
    G --> K["✅ çalışır (örn. www.google.com)"]
```


**🔍 Kontrol:**

```bash
ip route | grep 8.8.8.8
ping -c 2 8.8.8.8            # Destination Host Unreachable
ping -c 2 www.google.com     # works
yq '.network.ethernets.enp0s3.routes' /etc/netplan/50-cloud-init.yaml
```

<p align="center"><img src="./screenshots/12-route-after.png" alt="Route tabloda, 8.8.8.8 ulaşılamıyor, google.com çalışıyor" width="85%"><br><sub><em>Route tabloda, 8.8.8.8 ulaşılamıyor, google.com çalışıyor</em></sub></p>

---

## Sonuç

Checker sonucu: **27 / 27 test geçti (%100).**

| # | Görev | Testler | Sonuç |
|---|---|---|---|
| 1 | Change of VM hostname | 1–2 | ✅ |
| 2 | User Creation | 3–8 | ✅ |
| 3 | LVM Configuration | 9–12 | ✅ |
| 4 | Sudo Rights | 13 | ✅ |
| 5 | Swap Creation | 14–15 | ✅ |
| 6 | Docker Installation | 16 | ✅ |
| 7 | NGINX Installation | 17–21 | ✅ |
| 8 | Cron Job | 22 | ✅ |
| 9 | NGINX Logrotate | 23 | ✅ |
| 10 | Make an immutable file /immutable.txt | 24 | ✅ |
| 11 | Bash Alias Creating | 25 | ✅ |
| 12 | Static Route | 26–27 | ✅ |

## Karşılaşılan sorunlar

| Sorun | Neden | Çözüm |
|---|---|---|
| `./checker: cannot execute binary file: Exec format error` | Yanlış platformun checker dosyası indirilmişti. `file ~/checker` çıktısı `Mach-O 64-bit arm64` (macOS ARM) gösteriyordu, VM ise Linux x86_64. | **AMD64 (Linux)** sürümü indirilince çözüldü. Kontrol: `uname -m` ve `file ~/checker` (`ELF 64-bit ... x86-64` görülmeli). |
| Hostname değişti ama prompt hâlâ `ubuntu-focal` gösteriyor | Prompt, oturum açıldığında okunan ismi gösterir. | `hostname` komutu `student` döndürüyorsa değişiklik tamamdır. Yeni oturumda prompt da güncellenir. |
| `ls /etc/sudoers.d/student` → Permission denied | Dizin sadece root tarafından okunabilir. | `sudo ls -l` ve `sudo cat` ile bakılır, sorun yoktur. |
| Docker'da `permission denied ... docker.sock` | Kullanıcı docker grubuna eklendi ama oturum yenilenmemişti. | `exit` ile çıkıp tekrar girince çözüldü. |
| `.bashrc`'de aynı alias iki kez | Alias ekleme komutu iki kez çalıştırıldı. İşlevi bozmaz ama gereksizdir. | `sudo -u student sed -i '/alias eip=/d' /home/student_home/.bashrc` ile silinip alias **bir kez** eklendi. `>>` veya `tee -a` her çalıştırmada satır ekler, tekrar çalıştırmadan önce `grep -n eip` ile kontrol edin. |
| Cron yedekleri birikiyor | Her dakika yedek alındığından `conf.d` hızla dolar. | Testler geçtikten sonra sıklığı saatliğe çekin: `0 * * * *`. |
