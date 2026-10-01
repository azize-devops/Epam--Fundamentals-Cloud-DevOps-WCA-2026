<div align="center">

# 💻 Linux Shell Basics

[🇬🇧 English](./Linux%20Shell%20Basics.md) · **🇹🇷 Türkçe**

![Questions](https://img.shields.io/badge/soru-11-blue?style=for-the-badge) ![Topic](https://img.shields.io/badge/konu-Linux%20Shell%20Basics-success?style=for-the-badge)

[⬅️ DevOps BootCamp: Linux](./)

</div>

✅ = doğru, ❌ = yanlış. Her şıkkın yanında kısa bir açıklama var.

---

### Soru 1

**This file is invoked as interactive non-login shell**

| | Şık | Neden |
|:-:|---|---|
| ❌ | `.bash_profile` | Login shell'de (giriş yaparken) okunur, etkileşimli non-login shell'de okunmaz. |
| ✅ | `.bashrc` | Terminal penceresi açtığında ya da bash içinde yeni bir bash başlattığında okunur. Bu tam olarak interactive non-login shell. |
| ❌ | `.bash_history` | Sadece komut geçmişini tutar, çalıştırılan bir dosya değil. |
| ❌ | `.bash_logout` | Oturum kapanırken çalışır. |

> [!TIP]
> **Doğru cevap:** `.bashrc`

---

### Soru 2

**Select answers where environments are defined properly**

| | Şık | Neden |
|:-:|---|---|
| ✅ | `KEY=value1` | Eşittirin iki yanında boşluk yok, doğru tanım. |
| ❌ | `KEY2 = value2` | Boşluklar yüzünden shell `KEY2`'yi komut sanır, hata verir. |
| ❌ | `KEY 3="value3"` | Değişken adında boşluk olamaz. |
| ✅ | `KEY_4=value4:value4_1` | Alt çizgi ve `:` değerde sorun değil, geçerli. |
| ❌ | `KEY5=value 5` | Boşluktan sonra gelen `5` ayrı bir komut gibi çalıştırılmaya çalışılır. Boşluklu değer tırnak ister. |
| ✅ | `_KEY6="value 6"` | Ad alt çizgiyle başlayabilir, boşluklu değer tırnak içinde, doğru. |

> [!TIP]
> **Doğru cevap:** `KEY=value1`, `KEY_4=value4:value4_1`, `_KEY6="value 6"`

---

### Soru 3

**This env var contains a list of directories to be searched when user executing commands**

| | Şık | Neden |
|:-:|---|---|
| ❌ | `INSTALL` | Böyle standart bir değişken yok. |
| ❌ | `DIRS` | Bu da standart değil. |
| ✅ | `PATH` | Bir komut yazınca shell bu listedeki dizinlere sırayla bakar (`/usr/bin:/bin:...`). |
| ❌ | `SHELLPATH` | Yok böyle bir şey. |
| ❌ | `SYSTEM` | Standart bir değişken değil. |

> [!TIP]
> **Doğru cevap:** `PATH`

---

### Soru 4

**This command is used to change current directory to user home dir**

| | Şık | Neden |
|:-:|---|---|
| ❌ | `cd /` | Kök dizine gider. |
| ❌ | `cd ..` | Bir üst dizine çıkar. |
| ✅ | `cd ~` | `~` ev dizini demek. (Sadece `cd` yazmak da aynı işi yapar.) |
| ❌ | `cd -` | Bir önceki bulunduğun dizine döner. |

> [!TIP]
> **Doğru cevap:** `cd ~`

---

### Soru 5

**Command to list files in directory**

| | Şık | Neden |
|:-:|---|---|
| ❌ | `cat` | Dosya içeriğini gösterir. |
| ❌ | `man` | Komutların kılavuz sayfasını açar. |
| ✅ | `ls` | Dizindeki dosya ve klasörleri listeler. |
| ❌ | `pwd` | Hangi dizinde olduğunu yazar. |

> [!TIP]
> **Doğru cevap:** `ls`

---

### Soru 6

**Using this command with -f key you could track live events in a log file**

| | Şık | Neden |
|:-:|---|---|
| ❌ | `head` | Dosyanın başını gösterir. |
| ❌ | `less` | Dosyayı sayfa sayfa okutur, `-f` ile canlı takip yapmaz. |
| ❌ | `more` | Sayfa sayfa gösterir, canlı takip yok. |
| ✅ | `tail` | `tail -f` dosyanın sonunu gösterir ve yeni satırlar geldikçe ekrana basar. Log izlemede en çok kullanılan yöntem. |

> [!TIP]
> **Doğru cevap:** `tail`

---

### Soru 7

**What command is used to exit vi/vim without saving changes?**

| | Şık | Neden |
|:-:|---|---|
| ❌ | `:wq` | Kaydedip çıkar. |
| ✅ | `:q!` | Kaydetmeden zorla çıkar, değişiklikler gider. |
| ❌ | `:q` | Değişiklik varsa çıkmaz, "kaydedilmemiş değişiklik var" uyarısı verir. |
| ❌ | `:!wq` | Geçerli bir vim komutu değil (başındaki `!` komutu dış shell komutu olarak çalıştırır). |

> [!TIP]
> **Doğru cevap:** `:q!`

---

### Soru 8

**Which commands allow you to find files, that contain a word "ping" in the current directory?**

| | Şık | Neden |
|:-:|---|---|
| ❌ | `find . -n ping -type f` | `-n` geçerli bir `find` seçeneği değil. Ayrıca `find` dosya adına bakar, içine bakmaz. |
| ✅ | `grep -r ping .` | Mevcut dizindeki tüm dosyaların içinde "ping" arar. |
| ✅ | `grep -r ping` | GNU grep'te yol verilmezse `-r` ile mevcut dizinde arar, yani aynı işi yapar. |
| ❌ | `find -name ping -t f` | `-t` geçerli değil (doğrusu `-type`), üstelik `-name` dosya adına bakar, içeriğe değil. |

> [!TIP]
> **Doğru cevap:** `grep -r ping .`, `grep -r ping`

---

### Soru 9

**Which command you need to execute in order to safely eding /etc/sudoers file?**

| | Şık | Neden |
|:-:|---|---|
| ❌ | `vi` | Doğrudan açarsan hatalı bir kayıt seni sudo'dan kilitleyebilir. |
| ❌ | `nano` | Aynı sorun, sözdizimi kontrolü yok. |
| ❌ | `vim` | Aynı şekilde kontrol yok. |
| ✅ | `visudo` | Dosyayı kilitler ve kaydederken sözdizimini kontrol eder. Hata varsa uyarır, böylece sistemi bozmazsın. |

> [!TIP]
> **Doğru cevap:** `visudo`

---

### Soru 10

**What is true about xargs utility?**

| | Şık | Neden |
|:-:|---|---|
| ✅ | Can convert lines into single line | Girdideki satırları tek bir komutun argümanları haline getirir. |
| ✅ | Can convert single line into multiple lines | `-n 1` gibi seçeneklerle tek satırı parçalayıp komutu her seferinde tek argümanla çalıştırabilir. |
| ❌ | Can substitute an argument in a command only once | `-I {}` ile aynı argümanı komutta birden çok yere koyabilirsin. |
| ❌ | Can run only one command at a time | `sh -c '...'` ile birden fazla komut çalıştırılabilir, `-P` ile paralel de çalışır. |

> [!TIP]
> **Doğru cevap:** İlk iki şık

---

### Soru 11

**Select commands that will create an archive**

| | Şık | Neden |
|:-:|---|---|
| ✅ | `tar -cvf file.tar path/to/` | `-c` create demek, arşiv oluşturur. |
| ❌ | `tar -xvf file.tar` | `-x` extract, yani arşivi açar. |
| ❌ | `tar -tf file.tar` | `-t` list, içeriği listeler. |
| ✅ | `tar -cvzf file.tar.gz path/to/` | `-c` ile arşiv oluşturur, `-z` ile gzip sıkıştırır. |

> [!TIP]
> **Doğru cevap:** `tar -cvf file.tar path/to/`, `tar -cvzf file.tar.gz path/to/`

---

## 🗺️ Özet kartı

**Bash başlangıç dosyaları**

| Dosya | Ne zaman okunur |
|---|---|
| `.bash_profile` | login shell |
| `.bashrc` | interactive non-login shell (yeni terminal penceresi) |
| `.bash_logout` | oturum kapanırken |
| `.bash_history` | çalıştırılmaz, sadece geçmişi tutar |

**Değişkenler:** `KEY=value` (`=` çevresinde boşluk yok), boşluklu değer tırnak ister: `KEY="a b"`.

**`tar` bayrakları**

| Bayrak | Anlamı |
|:-:|---|
| `c` | oluştur (create) |
| `x` | aç (extract) |
| `t` | listele |
| `z` | gzip |
| `v` | ayrıntılı çıktı |
| `f` | arkasından dosya adı gelir |

**Işe yarayan komutlar**

| İhtiyaç | Komut |
|---|---|
| Logu canlı izle | `tail -f dosya` |
| vim'den kaydetmeden çık | `:q!` |
| sudoers'ı güvenle düzenle | `visudo` |
| Dosya içinde ara | `grep -r kelime .` |
| Home'a git | `cd ~` veya `cd` |
