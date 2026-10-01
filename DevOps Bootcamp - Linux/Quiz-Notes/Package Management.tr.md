<div align="center">

# 📦 Package Management

[🇬🇧 English](./Package%20Management.md) · **🇹🇷 Türkçe**

![Questions](https://img.shields.io/badge/soru-5-blue?style=for-the-badge) ![Topic](https://img.shields.io/badge/konu-Package%20Management-success?style=for-the-badge)

[⬅️ DevOps BootCamp: Linux](./)

</div>

✅ = doğru, ❌ = yanlış. Her şıkkın yanında kısa bir açıklama var.

---

### Soru 1

**This package managers are used in Debian-like distributives**

| | Şık | Neden |
|:-:|---|---|
| ✅ | `apt` | Debian, Ubuntu gibi sistemlerin ana paket yöneticisi. Bağımlılıkları kendisi çözer. |
| ❌ | `rpm` | Red Hat tabanlı sistemlerin paket yöneticisi/formatı. |
| ✅ | `dpkg` | Debian'ın alt seviye paket aracı, `.deb` dosyalarını kurar. `apt` aslında arkada bunu kullanır. |
| ❌ | `yum` | Red Hat tabanlı sistemlerin (CentOS, eski RHEL) paket yöneticisi. |

> [!TIP]
> **Doğru cevap:** `apt`, `dpkg`

---

### Soru 2

**Which package could be installed in RedHat-based distributives?**

| | Şık | Neden |
|:-:|---|---|
| ❌ | `apk` | Alpine Linux'un paket formatı. |
| ❌ | `deb` | Debian/Ubuntu'nun paket formatı. |
| ✅ | `rpm` | Red Hat, Fedora, CentOS gibi sistemlerin paket formatı. |
| ❌ | `pkg` | FreeBSD'nin paket sistemi. |

> [!TIP]
> **Doğru cevap:** `rpm`

---

### Soru 3

**Using this program you could manage several versions of different programming languages**

| | Şık | Neden |
|:-:|---|---|
| ✅ | `alternatives` | `update-alternatives` aynı komutun birden fazla sürümü arasında geçiş yapmanı sağlar (java, python, gcc gibi farklı diller ve araçlar için). |
| ❌ | `pyenv` | Sadece Python sürümlerini yönetir, "farklı programlama dilleri" ifadesine uymaz. |
| ❌ | `nvenv` | Böyle bir araç yok. (Node için gerçek araç `nvm`.) |
| ❌ | `versions` | Bu isimde standart bir araç yok. |

> [!TIP]
> **Doğru cevap:** `alternatives`

---

### Soru 4

**Please select what is true about systemd**

| | Şık | Neden |
|:-:|---|---|
| ❌ | a single package | systemd tek bir program değil, bir yazılım paketleri bütünü. |
| ✅ | a bundle of sofware with journald, networkd, | İçinde journald (log), networkd (ağ), logind gibi birçok bileşen var. |
| ✅ | an alternative to init.d | Eski SysV init.d'nin yerini alan modern init/servis yöneticisi. |
| ❌ | less flexible than init.d | Tam tersi, daha esnek. |
| ✅ | more flexible than init.d | Paralel servis başlatma, bağımlılık yönetimi, zamanlayıcılar gibi özellikler sunar. |

> [!TIP]
> **Doğru cevap:** 2., 3. ve 5. şıklar

---

### Soru 5

**Select tasks, that are applicable to be managed by crond**

| | Şık | Neden |
|:-:|---|---|
| ✅ | backup | Yedeği her gece belli saatte çalıştırmak için ideal. |
| ✅ | disk cleanup | Geçici dosyaları ya da eski logları düzenli temizlemek için kullanılır. |
| ✅ | data sync | Düzenli aralıklarla senkronizasyon (örneğin `rsync`) çalıştırılabilir. |
| ✅ | restart | Belirli zamanda servisi ya da sunucuyu yeniden başlatmak için zamanlanabilir. |
| ✅ | check if service is running | Servisin ayakta olup olmadığını her dakika/saatte kontrol edip gerekirse başlatan script yazılabilir. |

> [!TIP]
> **Doğru cevap:** Hepsi, çünkü cron zamanlanmış her türlü komut/script için uygundur.

---

## 🗺️ Özet kartı

| Aile | Paket formatı | Alt seviye araç | Üst seviye yönetici |
|---|:-:|:-:|:-:|
| Debian / Ubuntu | `.deb` | `dpkg` | `apt` |
| Red Hat / Fedora / CentOS | `.rpm` | `rpm` | `yum` / `dnf` |
| Alpine | `.apk` | `apk` | `apk` |
| FreeBSD | `pkg` | | `pkg` |

**cron zamanlama formatı**

```text
┌───────── dakika (0-59)
│ ┌─────── saat (0-23)
│ │ ┌───── ayın günü (1-31)
│ │ │ ┌─── ay (1-12)
│ │ │ │ ┌─ haftanın günü (0-7)
* * * * *  komut
```
