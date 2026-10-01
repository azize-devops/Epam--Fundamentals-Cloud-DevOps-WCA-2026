# VM Task: Görev Tanımı

Bu dosya görevin **ne istediğini** özetler. Nasıl çözüldüğü için bkz. [SOLUTION.md](./SOLUTION.md).

> Görev metni kurs materyalinden özetlenmiştir, birebir kopya değildir.

## İçindekiler

- [Genel bakış](#genel-bakış)
- [Ön koşullar](#ön-koşullar)
- [Doğrulama (checker)](#doğrulama-checker)
- [Görevler](#görevler)
- [Görevlerde sorulan kavramlar](#görevlerde-sorulan-kavramlar)

## Genel bakış

Yerel makinede bir **Ubuntu 20.04 Server** sanal makinesi (VM) hazırlanır ve üzerinde 12 yapılandırma görevi yapılır. Görevler kullanıcı yönetimi, depolama (LVM, swap), yazılım kurulumu (Docker, NGINX), otomasyon (cron, logrotate), dosya özellikleri ve ağ (static route) konularını kapsar.

| | |
|---|---|
| Toplam görev | 12 |
| Toplam test | 27 |
| Her görev için deneme hakkı | 3 |

## Ön koşullar

VM şartları:

- İşletim sistemi: **Ubuntu 20.04 Server**
- Varsayılan kullanıcı: `ubuntu` (Vagrant imajında `vagrant`)
- **Swap bölümü yok**
- **2 disk**: ana disk (işletim sistemi için) ve LVM görevi için ikinci disk (örn. 1 GB)
- `jq` kurulu

Yerel makine hazırlığı için iki yol var:

1. **Vagrant** (kullanılan yol): `Vagrantfile` ile VM ve ikinci disk otomatik oluşur. `disks` özelliği deneysel olduğu için `VAGRANT_EXPERIMENTAL="disks"` ortam değişkeni gerekir.
2. **Elle kurulum**: Oracle VirtualBox'ta VM oluşturup ikinci diski kendiniz eklersiniz.

Static Route görevi için ayrıca **`yq`** aracı kurulu olmalıdır (checker bunu YAML okumak için kullanır).

## Doğrulama (checker)

Checker, platforma göre indirilen (AMD64 veya ARM64) bir çalıştırılabilir dosyadır. VM içinde şu komutla çalıştırılır:

```bash
./checker -course 'linux' -course-version 'v1.0' -test-suite 'vm_task'
```

Test geçerse her testin yanında bir **secret phrase** yazdırılır. Kurs sayfasındaki ilgili alana bu ifadeler girilir.

> Checker'ı VM'in işlemci mimarisine uygun indirmek gerekir. VM Linux x86_64 ise **AMD64** sürümü kullanılmalıdır (bilgisayarınız Mac ARM olsa bile, VM ne ise o). Ayrıntı için SOLUTION.md'deki [Karşılaşılan sorunlar](./SOLUTION.md#karşılaşılan-sorunlar) bölümüne bakın.

## Görevler

### 1. Change of VM hostname (2 puan)

VM'in hostname'ini `student` yap. Bu isim `127.0.0.1` adresine çözülmeli ve değişiklik **kalıcı** olmalı (reboot sonrası da geçerli).

- `hostname -s`, `-f` ve `-i` çıktıları `student` ve `127.0.0.1` göstermeli.
- `sudo: unable to resolve host` uyarısı görüyorsanız görev tamam değildir.

### 2. User Creation (6 puan)

Yeni kullanıcı oluştur:

| Özellik | Değer |
|---|---|
| Kullanıcı adı | `student` |
| UID | `1040` |
| Ana grup | `student_group` (GID `1050`) |
| Home dizini | `/home/student_home` |
| Ek grup | `cdrom` |

### 3. LVM Configuration (4 puan)

İkinci diskte LVM yapılandır:

- Boş bir cihazda **physical volume** oluştur.
- **Volume group** adı `student`.
- **Logical volume** adı `student`, boyutu boş alanın **%50'si**, dosya sistemi **ext4**.
- Logical volume, `/lvm/student` dizinine **UUID kullanılarak kalıcı** olarak mount edilsin.

### 4. Sudo Rights (1 puan)

`student` kullanıcısına şu sudo hakları verilsin:

- `sudo apt install` ve `sudo apt-get install` komutları **şifresiz** çalışsın.
- Diğer tüm sudo komutları için **şifre sorulsun**.
- Varsayılan `/etc/sudoers` dosyasına **dokunulmasın** (eklenirse test başarısız olur). Bunun için best practice'e uygun başka bir yol bulunmalı.

İpucu: `sudo -k` sudo şifre önbelleğini temizler, test ederken kullanışlıdır.

### 5. Swap Creation (2 puan)

`swapfile` adında bir swap dosyası oluştur:

- Boyut: **1 GB**
- Reboot sonrasında da aktif kalacak şekilde **kalıcı** olsun.

### 6. Docker Installation (1 puan)

Docker'ı **Docker repository'sinden** kur:

- Sürüm: **19.03.15**
- Docker, `student` kullanıcısıyla (sudo olmadan) çalıştırılabilmeli.

`permission denied ... docker.sock` hatası, kullanıcının Docker'ı çalıştıramadığı anlamına gelir.

### 7. NGINX Installation (5 puan)

NGINX'i kur ve çalıştır:

- Port: **8080**
- Statik bir HTML sayfası, aşağıdaki alanlarla **JSON** döndürsün (değerler elle yazılabilir):

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

### 8. Cron Job (1 puan)

`/etc/nginx/nginx.conf` dosyasının yedeğini alan bir cron işi oluştur:

- Yedek adı kalıbı: `<dosya_adı>-<timestamp>.conf.bak`
- Timestamp: 1970-01-01 00:00:00 UTC'den bu yana geçen **saniye** sayısı
- Yedekler `/etc/nginx/conf.d/` dizinine kaydedilsin.
- Yedekleme için **ek script kullanılmasın** (komut doğrudan crontab'a yazılmalı).

### 9. NGINX Logrotate (1 puan)

NGINX log dosyalarının `logrotate` ile **haftalık** döndürülmesini sağla (`rotate 14` korunur).

Beklenen `logrotate -d` çıktısı: `rotating pattern: /var/log/nginx/*.log  weekly (14 rotations)`

### 10. Make an immutable file /immutable.txt (1 puan)

`/immutable.txt` dosyası oluştur:

- Sahip: `student`, grup: `cdrom`
- Herkese tüm izinler (**rwx**, yani 777)
- **Kimse silememeli** (root dahil)

### 11. Bash Alias Creating (1 puan)

`student` kullanıcısı için `eip` adında bir Bash alias'ı oluştur:

- Dış (external) IP adresini göstersin.
- **Kalıcı** olsun (reboot sonrası da çalışsın).

### 12. Static Route (2 puan)

`8.8.8.8` adresine giden paketlerin `4.4.4.4` üzerinden gönderilmesini sağlayan bir **static route** oluştur (ileri seviye: **kalıcı** olsun).

- Route aktifken `8.8.8.8`'e bağlanılamamalı, diğer adresler (örn. `www.google.com`) etkilenmemeli.
- Testler: route'un route tablosunda olması ve kalıcı olması.

## Görevlerde sorulan kavramlar

Görev metni bazı kavramları sorgular. Kısa cevapları:

**`/etc/netplan/50-cloud-init.yaml` nedir?**
Ubuntu'nun ağ yapılandırmasını tanımlayan **netplan** dosyasıdır. Adındaki `cloud-init`, dosyanın cloud-init tarafından üretildiğini gösterir. cloud-init her açılışta bu dosyayı yeniden yazabilir; bu yüzden elle yapılan kalıcı değişikliklerin silinmemesi için cloud-init'in ağ yapılandırması kapatılır.

**Neden `/etc/sudoers` yerine başka bir yol?**
`/etc/sudoers` paket güncellemeleriyle değişebilir ve hatalı düzenleme sudo'yu bozabilir. Best practice, `/etc/sudoers.d/` altında ayrı bir dosya oluşturup `visudo -f` ile düzenlemektir.

**Neden mount için UUID?**
`/dev/sdX` gibi aygıt adları yeniden başlatmada değişebilir. UUID değişmez, bu yüzden kalıcı mount'ta güvenilir yoldur.
