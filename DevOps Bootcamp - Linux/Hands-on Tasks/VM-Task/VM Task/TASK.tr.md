<div align="center">

# 📋 VM Task — Görev Tanımı

[🇬🇧 English](./TASK.md) · **🇹🇷 Türkçe**

[⬅️ README'ye dön](./README.tr.md) · [🛠️ Çözüm](./SOLUTION.tr.md)

</div>

Bu dosya görevin **ne istediğini** özetler. Nasıl çözüldüğü için bkz. [SOLUTION.tr.md](./SOLUTION.tr.md).

> [!NOTE]
> Görev metni kurs materyalinden özetlenmiştir, birebir kopya değildir.

## İçindekiler

- [Genel bakış](#genel-bakış)
- [Ön koşullar](#ön-koşullar)
- [Doğrulama (checker)](#doğrulama-checker)
- [Görevler](#görevler)
- [Temel kavramlar](#temel-kavramlar)

## Genel bakış

Yerel makinede bir **Ubuntu 20.04 Server** sanal makinesi (VM) hazırlanır ve üzerinde 12 yapılandırma görevi yapılır. Görevler kullanıcı yönetimi, depolama (LVM, swap), yazılım kurulumu (Docker, NGINX), otomasyon (cron, logrotate), dosya özellikleri ve ağ (static route) konularını kapsar.

| 🧮 Görev | 🧪 Test | 🔁 Görev başına deneme |
|:-:|:-:|:-:|
| **12** | **27** | **3** |

## Ön koşullar

**VM şartları**

- 🐧 İşletim sistemi: **Ubuntu 20.04 Server**
- 👤 Varsayılan kullanıcı: `ubuntu` (Vagrant imajında `vagrant`)
- 🚫 **Swap bölümü yok**
- 💽 **2 disk**: sistem diski ve LVM görevi için ikinci disk (örn. 1 GB)
- 🔧 `jq` kurulu

**VM'i hazırlamanın iki yolu**

1. **Vagrant** *(burada kullanılan)* — `Vagrantfile` VM'i ve ikinci diski oluşturur. `disks` özelliği deneysel olduğu için `VAGRANT_EXPERIMENTAL="disks"` gerekir.
2. **Elle kurulum** — Oracle VirtualBox'ta VM oluşturup ikinci diski kendiniz eklersiniz.

> [!NOTE]
> Static Route görevi için ayrıca **`yq`** kurulu olmalıdır (checker YAML okumak için kullanır).

## Doğrulama (checker)

Checker, platformunuza göre indirdiğiniz (AMD64 veya ARM64) bir çalıştırılabilir dosyadır. VM içinde çalıştırılır:

```bash
./checker -course 'linux' -course-version 'v1.0' -test-suite 'vm_task'
```

Geçen her test bir **secret phrase** yazdırır. Bunlar kurs sayfasındaki ilgili alana girilir.

> [!WARNING]
> Checker'ı **VM'in** işlemci mimarisine uygun indirin. VM Linux x86_64 ise bilgisayarınız ARM Mac olsa bile **AMD64** sürümünü kullanın. Bkz. SOLUTION.tr.md'deki [Karşılaşılan sorunlar](./SOLUTION.tr.md#karşılaşılan-sorunlar).

## Görevler

### 1. Change of VM hostname

| ⭐ Puan | 🧪 Test | 🏷️ Konu |
|:-:|:-:|---|
| 2 | 1–2 | hostname, `/etc/hosts` |

VM'in hostname'ini `student` yap. İsim `127.0.0.1` adresine çözülmeli ve değişiklik **kalıcı** olmalı (reboot sonrası da geçerli).

- `hostname -s`, `-f` ve `-i` çıktıları `student` ve `127.0.0.1` göstermeli.
- `sudo: unable to resolve host` uyarısı görüyorsanız görev tamam değildir.

### 2. User Creation

| ⭐ Puan | 🧪 Test | 🏷️ Konu |
|:-:|:-:|---|
| 6 | 3–8 | kullanıcı, grup |

Yeni kullanıcı oluştur:

| Özellik | Değer |
|---|---|
| Kullanıcı adı | `student` |
| UID | `1040` |
| Ana grup | `student_group` (GID `1050`) |
| Home dizini | `/home/student_home` |
| Ek grup | `cdrom` |

### 3. LVM Configuration

| ⭐ Puan | 🧪 Test | 🏷️ Konu |
|:-:|:-:|---|
| 4 | 9–12 | LVM, ext4, fstab |

İkinci diskte LVM yapılandır:

- Boş bir cihazda **physical volume** oluştur.
- **Volume group** adı `student`.
- **Logical volume** adı `student`, boyutu boş alanın **%50'si**, dosya sistemi **ext4**.
- `/lvm/student` dizinine **UUID kullanarak kalıcı** olarak mount et.

### 4. Sudo Rights

| ⭐ Puan | 🧪 Test | 🏷️ Konu |
|:-:|:-:|---|
| 1 | 13 | sudoers |

`student` kullanıcısına şu sudo hakları verilsin:

- `sudo apt install` ve `sudo apt-get install` **şifresiz** çalışsın.
- Diğer tüm sudo komutları için **şifre sorulsun**.
- Varsayılan `/etc/sudoers` dosyasına **dokunma** (düzenlenirse test başarısız olur). Best practice'e uygun alternatifi bul.

> [!TIP]
> `sudo -k` sudo şifre önbelleğini temizler. Test ederken kullanışlıdır.

### 5. Swap Creation

| ⭐ Puan | 🧪 Test | 🏷️ Konu |
|:-:|:-:|---|
| 2 | 14–15 | swap |

`swapfile` adında bir swap dosyası oluştur:

- Boyut: **1 GB**
- **Kalıcı**: reboot sonrası da aktif olsun.

### 6. Docker Installation

| ⭐ Puan | 🧪 Test | 🏷️ Konu |
|:-:|:-:|---|
| 1 | 16 | Docker, apt repo |

Docker'ı **Docker repository'sinden** kur:

- Sürüm: **19.03.15**
- `student` kullanıcısıyla **sudo olmadan** çalışabilmeli.

> [!NOTE]
> `permission denied ... docker.sock` hatası, kullanıcının henüz Docker'ı çalıştıramadığı anlamına gelir.

### 7. NGINX Installation

| ⭐ Puan | 🧪 Test | 🏷️ Konu |
|:-:|:-:|---|
| 5 | 17–21 | NGINX, JSON |

NGINX'i kur ve çalıştır:

- Port: **8080**
- Statik bir HTML sayfası aşağıdaki alanlarla **JSON** döndürsün (değerler elle yazılabilir):

```json
{
  "name": "student",
  "os": "<ubuntu sürümü>",
  "ip": "<vm özel ip>",
  "cpu": "<cpu sayısı>",
  "memory": "<Gi cinsinden bellek>"
}
```

Test paketi sayfayı `jq` ile okuduğu için `jq` kurulu olmalı. Testler servisin 8080'de çalıştığını ve `name`, `os`, `ip`, `cpu` alanlarını kontrol eder.

### 8. Cron Job

| ⭐ Puan | 🧪 Test | 🏷️ Konu |
|:-:|:-:|---|
| 1 | 22 | cron |

`/etc/nginx/nginx.conf` dosyasının yedeğini alan bir cron işi oluştur:

- Yedek adı kalıbı: `<dosya_adı>-<timestamp>.conf.bak`
- Timestamp: 1970-01-01 00:00:00 UTC'den bu yana geçen **saniye**
- Yedekler `/etc/nginx/conf.d/` dizinine kaydedilsin.
- **Ek script yok**: komut doğrudan crontab'a yazılır.

### 9. NGINX Logrotate

| ⭐ Puan | 🧪 Test | 🏷️ Konu |
|:-:|:-:|---|
| 1 | 23 | logrotate |

NGINX loglarını `logrotate` ile **haftalık** döndür (`rotate 14` korunur).

Beklenen `logrotate -d` çıktısı: `rotating pattern: /var/log/nginx/*.log  weekly (14 rotations)`

### 10. Make an immutable file /immutable.txt

| ⭐ Puan | 🧪 Test | 🏷️ Konu |
|:-:|:-:|---|
| 1 | 24 | `chattr`, izinler |

`/immutable.txt` dosyasını oluştur:

- Sahip `student`, grup `cdrom`
- Herkese tüm izinler (**rwx**, yani 777)
- **Kimse silememeli** (root dahil)

### 11. Bash Alias Creating

| ⭐ Puan | 🧪 Test | 🏷️ Konu |
|:-:|:-:|---|
| 1 | 25 | Bash, alias |

`student` kullanıcısı için `eip` adında bir Bash alias'ı oluştur:

- **Dış** (external) IP adresini göstersin.
- **Kalıcı** olsun (reboot sonrası da çalışsın).

### 12. Static Route

| ⭐ Puan | 🧪 Test | 🏷️ Konu |
|:-:|:-:|---|
| 2 | 26–27 | netplan, routing |

`8.8.8.8` adresine giden paketlerin `4.4.4.4` üzerinden gitmesini sağlayan bir **static route** oluştur (ileri seviye: **kalıcı** olsun).

- Route aktifken `8.8.8.8`'e bağlanılamamalı, diğer adresler (örn. `www.google.com`) etkilenmemeli.
- Testler: route'un route tablosunda olması ve kalıcı olması.

## Temel kavramlar

Görev metni bazı kavramları sorgular. Kısa cevapları:

<details>
<summary><b>📄 <code>/etc/netplan/50-cloud-init.yaml</code> nedir?</b></summary>

<br>

Ubuntu'nun **netplan** ağ yapılandırma dosyasıdır. Adındaki `cloud-init`, dosyanın cloud-init tarafından üretildiğini gösterir ve cloud-init her açılışta bu dosyayı yeniden yazabilir. Elle yapılan kalıcı değişikliklerin silinmemesi için cloud-init'in ağ yapılandırması kapatılır.

</details>

<details>
<summary><b>🔐 Neden <code>/etc/sudoers</code> yerine başka bir yol?</b></summary>

<br>

`/etc/sudoers` paket güncellemeleriyle değişebilir ve hatalı düzenleme sudo'yu bozabilir. Best practice, `/etc/sudoers.d/` altında ayrı bir dosya oluşturup `visudo -f` ile düzenlemektir.

</details>

<details>
<summary><b>🆔 Neden mount için UUID?</b></summary>

<br>

`/dev/sdX` gibi aygıt adları yeniden başlatmada değişebilir. UUID değişmez, bu yüzden kalıcı mount'ta güvenilir yoldur.

</details>
