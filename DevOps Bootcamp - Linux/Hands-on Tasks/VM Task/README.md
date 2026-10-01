# VM Task

Ubuntu 20.04 Server sanal makinesi üzerinde 12 Linux yapılandırma görevi: kullanıcı yönetimi, LVM, swap, Docker, NGINX, cron, logrotate, dosya özellikleri ve ağ.

**Sonuç: 27 / 27 test geçti (%100)**

## Dokümanlar

| Dosya | İçerik |
|---|---|
| [TASK.md](./TASK.md) | Görevin özeti: ne isteniyor, gereksinimler, puanlar, ön koşullar |
| [SOLUTION.md](./SOLUTION.md) | Çözüm: her görev için komutlar, kısa gerekçe, ekran görüntüleri, karşılaşılan sorunlar |
| [Vagrantfile](./Vagrantfile) | VM'i ve 1 GB'lik ikinci diski oluşturan Vagrant yapılandırması |
| [screenshots/](./screenshots) | Çözüm adımlarının ekran görüntüleri (görev numarasına göre adlandırılmıştır) |

## Görevler

| # | Görev | Konu |
|---|-------|------|
| 1 | Change of VM hostname | hostname, `/etc/hosts`, cloud-init |
| 2 | User Creation | `useradd`, `groupadd`, UID/GID |
| 3 | LVM Configuration | PV, VG, LV, ext4, fstab (UUID) |
| 4 | Sudo Rights | `/etc/sudoers.d/`, `visudo`, `NOPASSWD` |
| 5 | Swap Creation | swap dosyası, fstab |
| 6 | Docker Installation | Docker repository, sürüm sabitleme, docker grubu |
| 7 | NGINX Installation | port 8080, statik JSON sayfası |
| 8 | Cron Job | crontab, Unix timestamp |
| 9 | NGINX Logrotate | `logrotate` |
| 10 | Immutable file | `chattr +i`, izinler |
| 11 | Bash Alias Creating | `.bashrc`, alias |
| 12 | Static Route | netplan, kalıcı route |

## Hızlı başlangıç

Gereksinimler: [VirtualBox](https://www.virtualbox.org/) ve [Vagrant](https://www.vagrantup.com/).

```powershell
# Bu klasörde (PowerShell). Mac/Linux için: export VAGRANT_EXPERIMENTAL="disks"
$env:VAGRANT_EXPERIMENTAL="disks"   # ikinci disk (disks özelliği) için gerekli
vagrant up
vagrant ssh
```

VM içinde hazırlık:

```bash
sudo apt update && sudo apt install -y jq
cp /vagrant/checker ~ && chmod +x ~/checker      # checker dosyasını bu klasöre koymanız gerekir
./checker -course 'linux' -course-version 'v1.0' -test-suite 'vm_task'
```

Görevleri sırayla çözmek için [SOLUTION.md](./SOLUTION.md) dosyasına bakın.

## Notlar

- **Checker dosyası repoya eklenmemiştir.** Kurs sayfasından VM'in mimarisine uygun sürümü (Linux x86_64 için **AMD64**) indirip bu klasöre koyun. `.gitignore` bu dosyayı dışarıda tutar.
- **Secret phrase'ler repoda yer almaz.** Checker'ın ürettiği bu ifadeler görev cevabıdır, kurs sayfasındaki alanlara girilir.
- VM'de kullanılan komutların bir kısmı (ör. Docker sürümü, `yq`) sürüm ve yayın adreslerine bağlıdır. Zamanla değişebilirler.
- `Vagrantfile` kurs materyalindeki dosyayla aynıdır.

## Klasör yapısı

```
VM Task/
├── README.md
├── TASK.md
├── SOLUTION.md
├── Vagrantfile
├── .gitignore
└── screenshots/
    ├── 00-prereq-*.png          # VM ön hazırlık
    ├── 01-hostname-*.png
    ├── 02-user-creation.png
    ├── 03-lvm-*.png
    ├── 04-sudo-*.png
    ├── 05-swap-create.png
    ├── 06-docker-*.png
    ├── 07-nginx-*.png
    ├── 08-cron-*.png
    ├── 09-logrotate-after.png
    ├── 10-immutable-file.png
    ├── 11-alias-*.png
    └── 12-route-*.png
```
