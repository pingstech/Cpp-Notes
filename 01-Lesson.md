# Ders-1

## Genel C++ Kavramları, Pointer'lar ve Referanslar

### Bilinmesi Gereken C Konuları

C++'a geçmeden önce C dilinden bazı temel konuların hatırlanması önemlidir:

  * **Ternary Operatör (`?:`)**: Koşullu ifadeler için kullanılır.
  * **Ön İşlemci Komutları**: `#include`, `#define` gibi direktifler.
  * **Tür Dönüşümleri (Type Casts)**: Bir veri tipini başka bir veri tipine dönüştürme.
  * **Typedef Bildirimleri**: Mevcut tiplere yeni isimler atama.
  * **`sizeof` Operatörü**: Bir değişkenin veya tipin bellekte kapladığı alanı bayt cinsinden verir.
  * **`undefined behavior`**: Tanımsız davranış. Programın standart dışı veya öngörülemeyen bir şekilde davranması.
  * **`implementation defined`**: Uygulamaya bağlı davranış. Derleyicinin davranışının derleyiciye göre değişebileceği durumlar.
  * **Mantıksal Operatörler**: C'deki mantıksal operatörler (`&&`, `||`, `!`) `int` tipinde `bool` benzeri sonuçlar üretir (0 veya 1).
  * **Pointer ve Aritmetik Türler Arasında Dönüşüm Yok**: Pointer'lar ve doğrudan aritmetik türler arasında örtük dönüşüm yoktur.
  * **Farklı Pointer Türleri Arasında Dönüşüm Yok**: Farklı pointer türleri arasında doğrudan örtük dönüşüm yoktur (örn. `int*` ile `char*` arasında).

### Referanslar (Reference)

Referanslar, mevcut bir değişkene alternatif bir isim (takma ad) vermek için kullanılır.

```cpp
int x = 10;   // Bir L-value değişken oluşturuluyor.
int &r = x;   // r, x'e bir referans değişken oluşturuluyor.
              // r'ye yapılan atamalar aslında x'in adresini tuttuğu için x'i değiştirir.
int *p = &r;  // p, r'nin adres bilgisiyle atanıyor, yani x'in adresini tutuyor.
```

### Dizi Pointer'ları

Bir dizi adı (`int a[5]`), genellikle ilk elemanın adresine otomatik olarak bozularak (decay) bir pointer gibi davranır.

```cpp
int a[5] = {1, 2, 3, 4, 5};

int *p = a;    // a bir pointer gibi davranır ama gerçek bir pointer değildir.
               // p ise gerçek bir pointer'dır.

// Dizi elemanlarına erişim:
a[i]        // Dizi notasyonu
*(a + i)    // Pointer aritmetiği ile aynı
p[i]        // Pointer notasyonu
*(p + i)    // Pointer aritmetiği ile aynı
```

**Tanım ve Açıklama Tablosu:**

| Tanım              | Açıklama            | Tipi           |
| :----------------- | :------------------ | :------------- |
| `int a[5];`        | 5 elemanlı dizi.    | `int [5]`      |
| `int *p = a;`      | `a[0]`'ı işaret eder. | `int *`        |
| `int *p = &a[0];`  | `a[0]`'ı işaret eder. | `int *`        |
| `int (*p)[5] = &a;`| Dizinin tamamını işaret eder. | `int (*)[5]` (Array Pointer) |
| `int *p = &a[0];`  | İlk elemanı işaret eder. | `int *`        |

**`a` ve `&a` Arasındaki Fark:**

| İfade | Anlamı                 | Tipi          |
| :---- | :--------------------- | :------------ |
| `a`   | `a[0]`'ın adresi       | `int *`       |
| `&a`  | Dizinin tamamının adresi | `int (*)[5]`  |

-----

## Semantik ve Sık Geçen Semantik Türleri

### `int a[10];` Dizisinin Fonksiyon Dönüş Tipi

Sol taraftaki fonksiyonun dönüş tipi `int a[10]` gibi bir dizi olmamalıdır. Bunun yerine, dizi adresini döndürmek için pointer kullanılır.

`&a`: `a` dizisinin tamamının adresi, tipi: `int (*)[10]` yani 10 elemanlı `int` dizisi işaret eden pointer.

Bundan dolayı `foo()` fonksiyonu aşağıdaki gibi tanımlanmalıdır (ilkelli bir durum olmamasına rağmen):

```cpp
int (*foo())[10] // Bir pointer dizisi döndüren fonksiyon
{
    int a[10]; // Yerel dizi
    // ...
    return &a; // Dizinin adresini döndürme
}
```

**Not**: Bu örnekteki `foo` fonksiyonu yerel dizinin adresini döndürdüğü için `dangling pointer` problemi yaratır. Genellikle bu tür durumlardan kaçınılmalı veya dinamik bellek tahsisi (veya `std::array` gibi yapılar) kullanılmalıdır.

### Semantik: Bir dil öğesinin (değişken, ifade vb.) gerçek davranışını tanımlar.

### C++'ta Sık Geçen Semantik Türleri

| Terim               | Anlamı                    | Açıklama                                       |
| :------------------ | :------------------------ | :--------------------------------------------- |
| `Value semantics`   | Kopyalama davranışıdır.    | `x = y;` $\\rightarrow$ `x`'in `y`'nin kopyası olur. |
| `Reference semantics` | Aynı belleği paylaşır (alias gibi). | `int &r = x;` $\\rightarrow$ `r` `x`'in takma adıdır. |
| `Copy semantics`    | Kopyalama işlemi yapar.   | Kopyalama kurucusu (copy constructor) çağrılır. |
| `Move semantics`    | Kaynak çalma (`T&&`).     | Geçici nesnelerden kaynak aktarımı yapar. |
| `Pointer semantics` | İşaret edilen değerleri kapsar. | `*p`, `p->`, `p[i]` gibi. |

  * **Call by value (Değer ile Çağırma)**: Fonksiyona değişkenin kopyası gönderilir. Bu kopya üzerinde işlem yapılır. Orijinal değişken etkilenmez.
  * **Call by reference (Referans ile Çağırma)**: Fonksiyona değişkenin adresi (bir referansı) gönderilir. Fonksiyon orijinal değişken üzerinde işlem yapar.
  * **`const` ile güvenli `call by reference`**: Referans aktarımı maliyetli veri kopyalamayı gerektirmez. Büyük nesneleri kopyalamadan, güvenli şekilde fonksiyonlara geçiririz. Örnek: `void foo(const std::string &s);`

### `std::move`: Geçici nesnelerden kaynakları çalmak veya verimli nesne transferi yapmak.

`T&&`: `R-value` referansıdır.

Bu gösterimin amacı `move semantics`'i kullanmaktır.

**Not**: Referanslar `rebindable` değildir. Yani bir kere atama yapılabilirler (başka bir nesneyi referans gösteremezler).

```cpp
int x = 3;
int &r = x;

// Hata! Referanslar yeniden bağlanamaz.
// int &r2 = x; // Bu da hata verir
// int const *ptr = &x; // Eğer referans mantığını kullanmak isteseydik, bu şekilde kullanım yapmalıydık.
```

Bu kullanımın anlamı şuna benzer: `NULL` değer alabilmesi, bellek adresi değiştirmesi, değiştirilebilir olması gibi farklılıklar vardır.

### Pointer ve Referans Karşılaştırması:

  * **Pointer (`void set(T* p)`):**
      * `p` değişebilir.
      * C kullanışlıdır, `NULL` kontrolü gereklidir.
      * `set(&x)` şeklinde çağrılır, hatalara daha açıktır.
  * **Referans (`void set(T& p)`):**
      * `p` değişebilir.
      * C++ kullanışlıdır, `NULL` karakter kontrolü gerekmez, `set(x)` şeklinde çağrılabilir, daha güvenlidir.
  * **Sabit Referans (`void get(const T& p)`):**
      * `p` değişemez.

-----

## `const` Niteleyicisi ve Referansların Davranışı

### Top Level `const` (Üst Seviye `const`)

Değişkenin kendisi sabittir, içeriği değişmez.

```cpp
int x = 10;
const int *p = &x; // p hayatı boyunca x değerini gösterir ancak atama yapılmak istenmez.
                 // Syntax hatası oluşturur.
                 // Bu örnekte p bir pointer olduğu için syntax hatası yoktur. Pointer'ın gösterdiği değer sabittir.
                 // Yani *p = 20; yapılamaz. Ancak p'nin kendisi değişebilir: p = &y; yapılabilir.
```

```cpp
int &const r = x; // Referans semantiğinde buradaki const gerekmezdir.
                 // Referansın tanımı gereği başka bir değere bağlanması mümkün değildir.
```

### Low Level `const` (Alt Seviye `const`)

Değişkenin işaret ettiği veri, içeriği değişebilir.

```cpp
const int *p = &x; // p, sabit bir değeri işaretlemez. p'ye yeni bir atama yapılabilir.
                  // Yani *p = 20; yapılamaz ama p = &y; yapılabilir.
```

### Fonksiyonlarda Referans Dönüşü

Aşağıdaki yapı, bir fonksiyondan referans döndürmenin hem içeriği değiştirmeyi hem de performansı artırmayı sağladığını gösterir.

```cpp
struct Data { int x, y, z; };

Data &bar(Data &r) {
    ++r.x;
    ++r.y;
    ++r.z;
    return r;
}

int main() {
    Data mydata {1, 2, 3};
    Data &dr = bar(mydata); // bar fonksiyonu ile Data nesnesinin içeriği değiştiriliyor.
                                  // Hem de değiştirilen objeyi referans olarak geri dönüyor.
                                  // Bu sayede:
                                  // + Kopya oluşmaz (performanslı bir işlem).
                                  // + Geri dönen referansla işlem yapmaya devam edilebilir.
}
```

**Not**: Eğer referans, `const lvalue` referans ise `R-value` expression ile de ilk değer verilebilir.

### Referans Türleri ve Bağlanma

  * **Non-const referanslar (`T &`)**:
      * Sadece `lvalue` uyumlu türlerle bağlanabilir.
      * Geçici (`r-value`) ya da tür dönüşümü ile bağlanamaz.
  * **`const` referanslar (`const T &`)**:
      * Farklı türlerle (örn. `int` $\\rightarrow$ `double`) bağlanabilir.
      * Geçici değerlerle (`r-value`) bağlanabilir.

C++'da `const T &` kullanımı, referans bağlamada daha esnek, hem de daha güvenli bir yoldur.

### Pointer ve Referans Semantiği:

  * Pointer, adrese ilgilenir: "Nerede"
  * Referans, doğrudan nesneyle ilgilenir: "Bu"

-----

## Scope Kaçağı, Tip Çıkarımı ve `using`

### Scope Kaçağı (Scope Leakage)

Sınıf türünden olmayan değerleri `initialize` etmeyin. Bunun yerine `scope` mantığını kullanın.

```cpp
int foo();

int main() {
    int x = 10;
    int &r = x;
    int &&rr = x++; // R-value referansıdır. Yani geçici değerlere (R-value) bağlanabilir.
    int &&rr2 = foo(); // Geçici değerlere (R-value) bağlanabilir.
}
```

### Değer Kategorileri (Value Categories)

Bir ifadelerin davranışı ve bellekteki temsili, "değer kategorileri" kavramıyla belirlenir. Bu kavram, hem dilin semantiğini anlamak hem de derleyicinin optimizasyonlarını kavrayabilmek için kritik öneme sahiptir.

C++11 ile birlikte daha da zenginleşen bu yapı, modern C++'ın temel taşı olan **move semantics**, **perfect forwarding**, **std::move** gibi kavramların temelini oluşturur.

### 1. Temel Kavramlar

C++ ifadeleri, temel olarak iki ana kategoriye ayrılır:

#### **1.1 glvalue (generalized lvalue)**
Bir nesnenin bellekteki yerini temsil eder. Bu kategoriye hem klasik **lvalue** hem de **xvalue** dahildir.

#### **1.2 prvalue (pure rvalue)**
Geçici, isimlendirilmemiş değerleri temsil eder. Literaller, döndürülüp hemen kullanılan değerler bu sınıftadır.

Bunlardan türeyen asıl kategoriler şunlardır:

| Kategori  | Açıklama                                                       | Örnekler                          |
|----------|----------------------------------------------------------------|-----------------------------------|
| lvalue   | Bellekte bir adresi olan ve ismi olan değer                    | `x`, `foo() = 5`, `arr[i]`        |
| xvalue   | Taşınabilir kaynaklar, belleği serbest bırakmaya uygun değerler| `std::move(x)`, `baz()`           |
| prvalue  | Geçici değer, isimlendirilmemiş literal                        | `10`, `x + y`, `foo()`            |

**Not:** Tüm lvalue'lar aynı zamanda glvalue'dir. xvalue'lar da glvalue'dir, ancak taşınabilirliğe uygundurlar.

### 2. Fonksiyonlara Göre İnceleme

```cpp
int foo() { return 10; }                // prvalue döndürür
int& bar() { static int x = 20; return x; } // lvalue döndürür
int&& baz() { return 30; }              // prvalue döndürür, rvalue referansa bağlanabilir
```

- `foo()` çağrısı bir geçici değer döner: **prvalue**
- `bar()` çağrısı bir isimli değişkenin referansını döner: **lvalue**
- `baz()` fonksiyonu prvalue döner ama rvalue referans döndüğü için çağrı sonucu **xvalue** gibi işlenebilir.

### 3. Uygulamalı Örnek Kod

```cpp
int main() {
    int x = 5;                 // lvalue
    int y = x;                 // x, lvalue olarak kullanılır
    int z = 10;                // 10, prvalue

    int& r1 = x;               // lvalue referansı, geçerli
    // int& r2 = 10;           // HATA! prvalue, non-const lvalue referansa bağlanamaz

    const int& r3 = 10;        // Geçerli. const lvalue referansı prvalue'ya bağlanabilir

    int&& rr1 = 20;            // prvalue -> rvalue referans (rr1 = lvalue)
    int&& rr2 = foo();         // foo() -> prvalue

    int&& rr4 = std::move(x);  // std::move(x) -> xvalue

    std::cout << "Değer Kategorileri\n";
    std::cout << "-------------------\n";
    std::cout << "İfade        | Kategori\n";
    std::cout << "-------------|-----------\n";
    std::cout << "x            | lvalue\n";
    std::cout << "10           | prvalue\n";
    std::cout << "x + 10       | prvalue\n";
    std::cout << "++x          | lvalue\n";
    std::cout << "x++          | prvalue\n";
    std::cout << "bar()        | lvalue\n";
    std::cout << "foo()        | prvalue\n";
    std::cout << "std::move(x) | xvalue\n";

    return 0;
}
```

### 4. Değer Kategorisi Tablosu

| İfade         | Kategori |
|---------------|----------|
| `x`           | lvalue   |
| `10`          | prvalue  |
| `x + 10`      | prvalue  |
| `++x`         | lvalue   |
| `x++`         | prvalue  |
| `bar()`       | lvalue   |
| `foo()`       | prvalue  |
| `std::move(x)`| xvalue   |

### 5. Profesyonel İpuçları

- **prvalue** değerleri doğrudan rvalue referansına bağlayabilirsin (`int&& r = foo();`)
- **lvalue**'lar sadece `T&` türüyle bağlanabilirken, **xvalue**'lar `T&&` ile uyumludur.
- `std::move()` asla taşımayı garanti etmez — yalnızca "taşınabilir" olduğunu belirtir.
- Bir değişkenin türü `T&&` olsa da, kendisi **lvalue** kabul edilir. Bu nedenle forwarding işlemlerinde `std::forward` tercih edilir.

### 6. Uygulamalı Analoji

- **lvalue**: Evdeki koltuk. Sabit bir yerleşimi vardır. Onu tanır ve tekrar kullanabilirsin.
- **prvalue**: Pizza kuryesinden gelen tek kullanımlık pizza kutusu. Geçicidir.
- **xvalue**: Taşınmak üzere hazırlanmış koltuk. Yeri bellidir ama artık elden çıkarılmak üzeredir.

**NOT:**

* **L-Value**: Bellekte adı olan, yeniden erişilebilir değerlerdir. Örnek: `x`, `++x`, `arr[]`
* **R-Value**: Geçici, adı olmayan, tek kullanımlık değerlerdir. Örnek: `x++`, `42`, `foo()`
---

### Type Deduction (Tip Çıkarımı)

`auto` anahtar kelimesi, değişkenin tipini derleyicinin otomatik olarak belirlemesini sağlar. Sağ tarafındaki değere göre değişkenin tipini çıkarır.

`decltype` anahtar kelimesi, bir ifadenin tipini almak için kullanılır. Aynı zamanda fonksiyonların dönüş türlerini tespit etmek için de kullanılır.

```cpp
const int x = 5;
auto y = x; // y, int olur. const olmaz!
auto &z = x; // z, const int & olur. const korunur.

int y = 10;
int &r = y;
auto v = r; // y'nin türü int olduğu için v, int olur.
            // int y-r bir referans tır ama r'nin türü int olduğu için v değişkeni sadece int türündedir.

int func(int) {} // Fonksiyon tanımı
int (*p)(int); // Pointer to function
auto p2 = func; // p2, int (*)(int) olur (fonksiyon pointer'ı).

auto x = 10; // int
auto val = x > 1 ? 3 : 2.5; // ternary operatöründe 1. ve 2. operandları arasında tür dönüşümü yapılıyor.
                            // 3. operand double olduğu için bu ifadenin türü double olur.
                            // Koşulun doğru veya yanlış olmasıyla ilgili bir durum yoktur.
                            // val'in tipi double olacaktır.
```

**`auto` ve `const` İlişkisi:**

```cpp
const int x = 5;
auto &r_x = x; // auto anahtar kelimesi & operatörü ile birlikte kullanıldığında, başlangıç değerin const niteliğini korur.

const int y = x;
auto &r_y = y; // r_y, const int& olur.
```

```cpp
int a[5];
auto o_arr = a; // auto burada int* olur.

auto &ref_arr = a; // auto burada int (&)[5] olur.

auto &x = "ercan"; // auto burada const char[6] olur.
```

### `Using`: `typedef` Alternatifi

`Using`: C'deki `typedef` anahtar sözcüğüyle aynı görevi görür. Daha modern, kısa ve okunabilirdir.

```cpp
typedef const int *ciptr; // Eski typedef kullanımı
using ciptr = const int*; // Modern using kullanımı

// ciptr adıyla, const int* türüne alias (takma ad) tanımlanıyor.
```

-----

## Referans Katlama (Reference Collapsing) ve Universal Referanslar

### Reference Collapsing (Referans Katlama)

C++11 ile birlikte, `T&&` (r-value referans) ve `T&` (l-value referans) türlerini birbirine dönüşmesi ve şablonlarda doğru türlerin çıkarılması için `reference collapsing` işlemi kullanılır. Özellikle `Perfect Forwarding` kullanılırken, referanslar arasında birleştirme yaparak doğru tür elde etmek için `Reference Collapsing` işlemi yapılır.

**Dönüşüm Kuralları:**

  * `T&` + `T&` $\\rightarrow$ `T&` (İki l-value referansı birleştiğinde, l-value referansı olur.)
  * `T&` + `T&&` $\\rightarrow$ `T&` (l-value ve r-value referanslarının birleşmesinde, l-value referansı kullanılır.)
  * `T&&` + `T&` $\\rightarrow$ `T&` (l-value ve r-value referanslarının birleşmesinde, l-value referansı kullanılır.)
  * `T&&` + `T&&` $\\rightarrow$ `T&&` (İki r-value referansı birleştiğinde, yine r-value referansı kullanılır.)
