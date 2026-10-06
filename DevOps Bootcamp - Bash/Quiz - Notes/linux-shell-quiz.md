<div align="center">

# 🐚 Linux Shell Quiz

### Sorular · Doğru Cevaplar · Neden Doğru / Neden Yanlış

![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Bash](https://img.shields.io/badge/Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white)
![Sorular](https://img.shields.io/badge/Soru-5-blue?style=for-the-badge)
![Durum](https://img.shields.io/badge/Cevaplar-Hazır-success?style=for-the-badge)

**✅ Doğru seçenek &nbsp;·&nbsp; ❌ Yanlış seçenek**

</div>

---

## 📑 İçindekiler

- [⚡ Hızlı Özet](#-hızlı-özet)
- [1️⃣ Bilinen shell'lerin listesi](#1️⃣-bilinen-shelllerin-listesi)
- [2️⃣ Shell değiştirme](#2️⃣-shell-değiştirme)
- [3️⃣ Kullanıcıya özel başlangıç dosyaları](#3️⃣-kullanıcıya-özel-başlangıç-dosyaları)
- [4️⃣ Shell ne zaman kullanılmamalı](#4️⃣-shell-ne-zaman-kullanılmamalı)
- [5️⃣ Shell ne zaman kullanılabilir](#5️⃣-shell-ne-zaman-kullanılabilir)
- [🧠 Akılda Kalsın](#-akılda-kalsın)

---

## ⚡ Hızlı Özet

| # | 📝 Soru | ✅ Doğru Cevap |
|:-:|---------|----------------|
| 1 | Bilinen shell'leri listeleyen dosya | `/etc/shells` |
| 2 | Aktif terminalde shell değiştirme | Yeni shell'in adını yazmak |
| 3 | Kullanıcıya özel başlangıç dosyaları | `~/.profile` · `~/.bashrc` |
| 4 | Shell **kullanılmamalı** | Veri yapıları · Karmaşık uygulamalar · Kritik sistemler |
| 5 | Shell **kullanılabilir** | Çoğunlukla başka araçları çağırırken |

---

## 1️⃣ Bilinen shell'lerin listesi

> **❓ Linux sistemindeki bilinen shell'lerin genel görünümünü hangi dosya verir?**

| | Seçenek | Neden? |
|:-:|---------|--------|
| ✅ | **`/etc/shells`** | Sistemde geçerli kabul edilen login shell'leri listeler (`/bin/bash`, `/bin/sh`, `/usr/bin/zsh` ...). `chsh` komutu da bu dosyayı kontrol eder. |
| ❌ | `/etc/passwords` | Böyle bir dosya yok. Kullanıcılar `/etc/passwd`, parola özetleri `/etc/shadow` içindedir. |
| ❌ | `/etc/known_shells` | Standart bir Linux dosyası değil, uydurma isim. |
| ❌ | `/etc/shells.sh` | `.sh` uzantısı betik içindir; liste dosyası böyle adlandırılmaz. |

> [!TIP]
> Listeyi görmek için:
> ```bash
> cat /etc/shells
> ```

---

## 2️⃣ Shell değiştirme

> **❓ Aktif terminalde bir shell'den diğerine nasıl geçilir?**

| | Seçenek | Neden? |
|:-:|---------|--------|
| ✅ | **Yeni shell'in adını yazmak** | Shell de sıradan bir programdır. `zsh` veya `bash` yazınca yeni shell **alt süreç** olarak açılır. `exit` ile eskisine dönülür. |
| ❌ | `/etc/shells` içindeki adı güncellemek | Dosya yalnızca izinli shell'lerin listesidir, çalışan shell'i değiştirmez. |
| ❌ | `~/.bashrc` içindeki adı güncellemek | Yeni bash oturumu açılırken çalışır; mevcut terminalde geçiş yapmaz. |
| ❌ | OS yeniden başlatılmalı | Alakasız, yeniden başlatma gerekmez. |

```bash
$ zsh        # zsh'e geç
$ exit       # önceki shell'e dön
```

> [!NOTE]
> Varsayılan shell'i **kalıcı** olarak değiştirmek için `chsh -s /bin/zsh` kullanılır. Seçilen shell `/etc/shells` içinde olmalıdır.

---

## 3️⃣ Kullanıcıya özel başlangıç dosyaları

> **❓ Kullanıcıya özel (user-specific) başlangıç dosyalarının hepsini seçin.**

| | Seçenek | Neden? |
|:-:|---------|--------|
| ❌ | `/etc/profile` | **Sistem geneli** dosya, tüm kullanıcılar için çalışır. |
| ❌ | `/etc/.profile` | Standart bir dosya değil. |
| ✅ | **`~/.profile`** | Home dizinindedir, login shell'de sadece o kullanıcı için çalışır. |
| ✅ | **`~/.bashrc`** | Home dizinindedir, interaktif bash oturumlarında sadece o kullanıcı için çalışır. |

```text
/etc/profile   →  🌍 herkes için (sistem geneli)
~/.profile     →  👤 sadece sen için
~/.bashrc      →  👤 sadece sen için
```

> [!IMPORTANT]
> **Kural:** Başında `~/` (home dizini) olanlar kullanıcıya özeldir, `/etc/` altındakiler sistem geneldir.

---

## 4️⃣ Shell ne zaman kullanılmamalı

> **❓ Shell şu durumlarda kullanılmamalıdır (doğru olanların hepsini seçin).**

| | Seçenek | Neden? |
|:-:|---------|--------|
| ✅ | **Bağlı liste, ağaç gibi veri yapıları gerekiyorsa** | Shell'de gelişmiş veri yapısı desteği yok (sadece değişken ve dizi). Python, C, Java daha uygun. |
| ✅ | **Yapılandırılmış programlama şart olan karmaşık uygulamalar** | Tip denetimi, fonksiyon prototipi gibi özellikler yok; büyük projede hata yapmak kolay. |
| ❌ | Çoğunlukla başka araçları çağırıyor, az veri işliyorsanız | Tam tersi: shell'in **en güçlü olduğu** alan bu (bkz. Soru 5). |
| ✅ | **Şirketin geleceğini bağladığınız kritik uygulamalar** | Betikler kırılgan, hata yönetimi ve performans sınırlı; kritik sistemler sağlam dillerle yazılmalı. |

> [!WARNING]
> Bu soruda 3 doğru cevap var. Üçüncü madde tuzaktır, o madde 5. sorunun cevabıdır.

---

## 5️⃣ Shell ne zaman kullanılabilir

> **❓ Shell şu durumlarda kullanılabilir (doğru olanların hepsini seçin).**

| | Seçenek | Neden? |
|:-:|---------|--------|
| ❌ | Veri yapıları gerekiyorsa | Shell bunun için uygun değil. |
| ❌ | Yapılandırılmış programlama gerektiren karmaşık uygulamalar | Tip denetimi vb. yok. |
| ✅ | **Çoğunlukla başka araçları çağırıyor, az veri işliyorsanız** | Shell'in asıl işi: komutları birleştirmek (pipe, yönlendirme), otomasyon, yedekleme, dosya işlemleri, sistem yönetimi betikleri. |
| ❌ | Mission-critical uygulamalar | Riskli, yanlış tercih. |

---

## 🧠 Akılda Kalsın

<div align="center">

### Shell = 🔗 "Yapıştırıcı" dil

| 👍 İyi olduğu işler | 👎 Kötü olduğu işler |
|---------------------|----------------------|
| Komutları birleştirmek | Veri yapıları (liste, ağaç) |
| Otomasyon, yedekleme | Karmaşık, büyük uygulamalar |
| Küçük sistem betikleri | Kritik, şirketin geleceğini bağlayan sistemler |

</div>

<details>
<summary><b>🔁 4. ve 5. soru farkı (tıkla)</b></summary>

<br>

Bu iki soru birbirinin tersidir:

- **4. soru:** 1., 2. ve 4. madde seçilir (shell'in zayıf olduğu yerler).
- **5. soru:** Sadece 3. madde seçilir (shell'in güçlü olduğu yer).

</details>

---

<div align="center">

⭐ Faydalı olduysa kaydetmeyi unutma · Hazırlayan: **Claude** 🤖 · 06.10.2026

</div>
