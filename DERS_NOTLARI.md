# 📚 Biçimsel Diller ve Otomata Teorisi
### Yazılım Mühendisliği Perspektifiyle Teori ve Uygulamalar

![Biçimsel Diller ve Otomata Teorisi - Halid Hacbekkur](kapak_yatay.jpg)

---

## 📑 İçindekiler
- [BÖLÜM 1: Sonlu Özdevinirler (Finite Automata - FA)](#bölüm-1-sonlu-özdevinirler-finite-automata---fa)
  - [1.1. Temel Kavramlar](#11-temel-kavramlar)
    - [Parça 1: Dört Temel Yapı Taşı](#parça-1-dört-temel-yapı-taşı)

---

# BÖLÜM 1: Sonlu Özdevinirler (Finite Automata - FA)

## 1.1. Temel Kavramlar

### Parça 1: Dört Temel Yapı Taşı

Biçimsel diller teorisinde tüm mimari 4 temel kavramın hiyerarşisi üzerine kuruludur:

```mermaid
flowchart LR
    A["1. Simge (Symbol)\nÖrn: a, 0"] -->|Bir araya gelir| B["2. Alfabe (Sigma - Σ)\nÖrn: Σ = {0, 1}"]
    B -->|Sıralı dizilir| C["3. Dizgi (String - w)\nÖrn: w = 0101"]
    C -->|Küme oluşturur| D["4. Dil (Language - L)\nÖrn: L = {0, 10, 100}"]
```

#### 1. Simge (Symbol)
* **Tanım:** En küçük, bölünemez atomik karakterdir.
* **Örnek:** `a`, `b`, `0`, `1`
* **Yazılım Mühendisliği Karşılığı:** Kodundaki tek bir `char` veri tipidir.

#### 2. Alfabe — Sigma (Σ)
* **Tanım:** Boş olmayan, **sonlu** sayıdaki simgelerin oluşturduğu kümedir. Büyük Yunan harfi **Sigma (Σ)** ile gösterilir.
* **Örnekler:**
  * İkili (Binary) alfabe: **Σ = {0, 1}**
  * Harf alfabesi: **Σ = {a, b}**
  * İngiliz alfabesi: **Σ = {a, b, c, ..., z}**
* **Yazılım Mühendisliği Karşılığı:** Programında izin verdiğin geçerli karakter setidir (ASCII veya UTF-8 alt kümesi).

#### 3. Dizgi / Kelime / Sözcük (String — w)
* **Tanım:** Alfabedeki simgelerin sonlu sırada yan yana gelmesiyle oluşan karakter dizisidir.
* **Örnek:** Alfabe Σ = {a, b} ise:
  * `w = aba`
  * `w = bbabb`
* **Dizgi Uzunluğu (|w|):** Dizgideki toplam simge sayısıdır.
  * Örneğin `w = abba` ise uzunluğu **|w| = 4** olur.
* **Özel Durum: Boş Dizgi — Lambda (λ) veya Epsilon (ε):**
  * İçinde hiç simge bulunmayan boş dizgidir.
  * Uzunluğu her zaman sıfırdır: **|λ| = 0**
  * **Yazılım Karşılığı:** Boş metin `""` (empty string).

#### 4. Dil (Language — L)
* **Tanım:** Bir alfabeden üretilebilecek anlamlı dizgilerin oluşturduğu kümedir (yani alfabenin oluşturabileceği tüm dizgiler evreni olan Σ* kümesinin bir alt kümesidir: L ⊆ Σ*).
* **Örnek:** Alfabe Σ = {0, 1} olsun.
  * Çift sayılar dili: **L = {0, 10, 100, 110, ...}** (sonu 0 ile biten tüm ikili dizgiler).
* **Yazılım Mühendisliği Karşılığı:** Geçerli bir sözdizimi kuralıdır (Syntax Validation).
  * Örneğin: *"Geçerli e-posta adresleri kümesi"* veya *"Python'daki geçerli değişken adları"*.

#### 📝 Not Defterine Yazılacaklar (Hap Bilgiler)
1. **Simge (Symbol):** Bölünemez en küçük karakter (`0`, `1`, `a`).
2. **Alfabe (Σ):** Boş olmayan, sonlu simgeler kümesi (Örn: `Σ = {0, 1}`).
3. **Dizgi (w):** Alfabedeki simgelerin dizilimi. Uzunluğu `|w|` ile ifade edilir.
4. **Boş Dizgi (λ veya ε):** Karakter içermeyen dizgidir. `|λ| = 0`.
5. **Dil (L):** Alfabeden türetilen geçerli dizgiler kümesidir (`L ⊆ Σ*`).

---

### Parça 2: Sonlu Özdevinir (FA) Nedir, Şerit Modeli ve Karar Mekanizması

#### 1. Soyut Şerit Modeli (Tape Model)
Matematiksel olarak bir Sonlu Özdevinir (FA) şu 3 ana bileşenden oluşur:

```mermaid
flowchart LR
    subgraph Girdi["1. Girdi Şeridi"]
        direction LR
        S1["[ a ]"] --- S2["[ b ]"] --- S3["[ b ]"] --- S4["[ a ]"]
    end

    subgraph Denetim["2. Sonlu Denetim"]
        Q["Durum Havuzu: {q0, q1, q2}<br>Aktif Durum: q0"]
    end

    subgraph Karar["3. Karar Mekanizması"]
        direction TB
        Accept["KABUL (Dizi Dile Ait)"]
        Reject["RED (Hatalı Dizi)"]
    end

    S4 -->|"Okuma Kafası"| Q
    Q -->|"Final State"| Accept
    Q -->|"Diğer Durum"| Reject
```

#### 2. Bileşenlerin Görevleri
* **1. Girdi Şeridi (Input Tape):** Üzerinde okunacak simgeler sırayla yer alır. Okuma kafası her adımda sadece bir simge okur ve yalnızca soldan sağa ilerler (geri dönemez).
* **2. Sonlu Denetim Birimi (Finite Control):** Makinenin durumlarını barındırır. Makine tek bir başlangıç durumundan (`q₀`) başlar. Harici belleği (RAM/Stack) yoktur; tek hafızası o anda bulunduğu aktif durumdur.
* **3. Karar Mekanizması:** Girdi dizgisi tamamen okunduktan sonra makinenin durduğu durum bir **Kabul Durumu (Final State)** ise dizgi **KABUL** edilir; aksi halde **RED** edilir.

#### 📝 Not Defterine Yazılacaklar (Hap Bilgiler)
1. **Sonlu Özdevinir (FA):** Belleği yalnızca sonlu durumlarla sınırlı, harici hafızasız soyut durum makinesidir.
2. **Şerit Modeli 3 Parçadan Oluşur:** Girdi Şeridi (tek yönlü okuma), Okuma Kafası ve Sonlu Denetim Birimi.
3. **Kabul / Red Kuralı:** Dizgi bittiğinde varılan durum Final State ise KABUL, değilse RED.

---

