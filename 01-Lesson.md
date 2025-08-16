# Ders-1

## General C++ Concepts, Pointers, and References (Genel C++ Kavramları, Pointer'lar ve Referanslar)

### Essential C Topics (Bilinmesi Gereken C Konuları)

C++'a geçmeden önce, C dilinden bazı temel konuların hatırlanması önemlidir.

  * **Ternary Operator (`?:`)**: Koşullu ifadeler için kullanılır.
  * **Preprocessor Directives (Ön İşlemci Komutları)**: `#include`, `#define` gibi direktifler.
  * **Type Conversions (Tür Dönüşümleri)**: Bir veri tipini başka bir veri tipine dönüştürme.
  * **Typedef Declarations (Typedef Bildirimleri)**: Mevcut tiplere yeni isimler atama.
  * **`sizeof` Operator**: Bir değişkenin veya tipin bellekte kapladığı alanı bayt cinsinden verir.
  * **`undefined behavior`**: Tanımsız davranış. Programın standart dışı veya öngörülemeyen bir şekilde davranması.
  * **`implementation defined`**: Uygulamaya bağlı davranış. Derleyicinin davranışının derleyiciye göre değişebileceği durumlar.
  * **Logical Operators (Mantıksal Operatörler)**: C'deki mantıksal operatörler (`&&`, `||`, `!`) `int` tipinde `bool` benzeri sonuçlar üretir (0 veya 1).
  * **No Implicit Conversion Between Pointers and Arithmetic Types (Pointer ve Aritmetik Türler Arasında Dönüşüm Yok)**: Pointer'lar ve doğrudan aritmetik türler arasında örtük dönüşüm yoktur.
  * **No Implicit Conversion Between Different Pointer Types (Farklı Pointer Türleri Arasında Dönüşüm Yok)**: Farklı pointer türleri arasında doğrudan örtük dönüşüm yoktur (örn. `int*` ile `char*` arasında).

### References (Referanslar)

Referanslar, mevcut bir değişkene alternatif bir isim (takma ad) vermek için kullanılır.

```cpp
int x = 10;   // Bir L-value değişken oluşturuluyor.
int &r = x;   // r, x'e bir referans değişken oluşturuluyor.
              // r'ye yapılan atamalar aslında x'in adresini tuttuğu için x'i değiştirir.
int *p = &r;  // p, r'nin adres bilgisiyle atanıyor, yani x'in adresini tutuyor.
```

### Array Pointers (Dizi Pointer'ları)

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

**Definition and Explanation Table (Tanım ve Açıklama Tablosu):**

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

## Semantics and Common Semantic Types (Semantik ve Sık Geçen Semantik Türleri)

### Function Return Type of an Array (Bir Dizinin Fonksiyon Dönüş Tipi)

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

### Semantics (Semantik):

Bir dil öğesinin (değişken, ifade vb.) gerçek davranışını tanımlar.

### Common Semantic Types in C++ (C++'ta Sık Geçen Semantik Türleri)

| Terim               | Anlamı                    | Açıklama                                       |
| :------------------ | :------------------------ | :--------------------------------------------- |
| `Value semantics`   | Kopyalama davranışıdır.    | `x = y;` → `x`'in `y`'nin kopyası olur. |
| `Reference semantics` | Aynı belleği paylaşır (alias gibi). | `int &r = x;` → `r` `x`'in takma adıdır. |
| `Copy semantics`    | Kopyalama işlemi yapar.   | Kopyalama kurucusu (copy constructor) çağrılır. |
| `Move semantics`    | Kaynak çalma (`T&&`).     | Geçici nesnelerden kaynak aktarımı yapar. |
| `Pointer semantics` | İşaret edilen değerleri kapsar. | `*p`, `p->`, `p[i]` gibi. |

  * **Call by value (Değer ile Çağırma)**: Fonksiyona değişkenin kopyası gönderilir. Bu kopya üzerinde işlem yapılır. Orijinal değişken etkilenmez.
  * **Call by reference (Referans ile Çağırma)**: Fonksiyona değişkenin adresi (bir referansı) gönderilir. Fonksiyon orijinal değişken üzerinde işlem yapar.
  * **`const` ile güvenli `call by reference`**: Referans aktarımı maliyetli veri kopyalamayı gerektirmez. Büyük nesneleri kopyalamadan, güvenli şekilde fonksiyonlara geçiririz. Örnek: `void foo(const std::string &s);`.

### `std::move`:

Geçici nesnelerden kaynakları çalmak veya verimli nesne transferi yapmak.

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

### Pointer vs. Reference Comparison (Pointer ve Referans Karşılaştırması):

  * **Pointer (`void set(T* p)`):**
      * `p` değişebilir.
      * C'de kullanışlıdır, `NULL` kontrolü gereklidir.
      * `set(&x)` şeklinde çağrılır, hatalara daha açıktır.
  * **Reference (`void set(T& p)`):**
      * `p` değişebilir.
      * C++'da kullanışlıdır, `NULL` karakter kontrolü gerekmez, `set(x)` şeklinde çağrılabilir, daha güvenlidir.
  * **Constant Reference (`void get(const T& p)`):**
      * `p` değişemez.

-----

## The `const` Qualifier and Reference Behavior (`const` Niteleyicisi ve Referansların Davranışı)

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

### Returning References from Functions (Fonksiyonlardan Referans Dönüşü)

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

### Reference Types and Binding (Referans Türleri ve Bağlanma)

  * **Non-const references (`T &`)**:
      * Sadece `lvalue` uyumlu türlerle bağlanabilir.
      * Geçici (`r-value`) ya da tür dönüşümü ile bağlanamaz.
  * **`const` references (`const T &`)**:
      * Farklı türlerle (örn. `int` → `double`) bağlanabilir.
      * Geçici değerlerle (`r-value`) bağlanabilir.

C++'da `const T &` kullanımı, referans bağlamada daha esnek, hem de daha güvenli bir yoldur.

### Pointer and Reference Semantics (Pointer ve Referans Semantiği):

  * Pointer, adrese ilgilenir: "Nerede".
  * Referans, doğrudan nesneyle ilgilenir: "Bu".

-----

## Scope, Type Deduction, and `using` (Scope, Tip Çıkarımı ve `using`)

### Scope Leakage (Scope Kaçağı)

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

**`auto` and `const` Relationship (`auto` ve `const` İlişkisi):**

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

### `using`: `typedef` Alternative (`using`: `typedef` Alternatifi)

`Using`: C'deki `typedef` anahtar sözcüğüyle aynı görevi görür. Daha modern, kısa ve okunabilirdir.

```cpp
typedef const int *ciptr; // Eski typedef kullanımı
using ciptr = const int*; // Modern using kullanımı

// ciptr adıyla, const int* türüne alias (takma ad) tanımlanıyor.
```

-----

## Value Categories (Değer Kategorileri)

C++ dilinde her ifade (expression) **değer kategorileri** ile sınıflandırılır. Bu kategoriler, ifadelerin nasıl davranacağını ve bellekte nasıl temsil edileceğini belirler.

**Temel Soru:** Bir ifade bellekte sabit bir yere mi sahip, yoksa geçici mi?

-----

### Category Hierarchy (Kategori Hiyerarşisi)

```
           expression
          /           \
     glvalue           rvalue
    /       \         /      \
lvalue     xvalue  xvalue   prvalue
```

**Main Categories (Ana Kategoriler):**

  - **lvalue**: Bellekte sabit adresi olan
  - **prvalue**: Geçici, kimliği olmayan
  - **xvalue**: Kimliği olan ama taşınacak

-----

### 1\. lvalue (Left-hand Value)

**Simple Definition (Basit Tanım):** Bellekte kalıcı adresi olan, isimlendirilmiş değerler.

#### Features (Özellikleri):

  - ✅ Adres alınabilir (`&` operatörü)
  - ✅ Sol tarafta kullanılabilir (atama)
  - ✅ Birden fazla kez erişilebilir

#### Examples (Örnekler):

```cpp
int x = 10;           // x → lvalue
x = 20;               // Sol tarafta kullanım OK

int arr[5];
arr[0] = 100;         // arr[0] → lvalue

std::string name = "Ali";
name = "Veli";        // name → lvalue

++x;                  // ++x → lvalue (kendini döndürür)
```

#### Analogy (Analoji):

**Evindeki koltuk** - Sabit bir yeri var, adresini biliyorsun, istediğin zaman kullanabilirsin.

-----

### 2\. prvalue (Pure rvalue)

**Simple Definition (Basit Tanım):** Geçici, kimliği olmayan değerler.

#### Features (Özellikleri):

  - ❌ Adres alınamaz
  - ❌ Sol tarafta kullanılamaz
  - ✅ const lvalue reference'a bağlanabilir

#### Examples (Örnekler):

```cpp
42                    // prvalue literal
3.14                  // prvalue literal
x + y                 // prvalue (hesaplama sonucu)
x++                   // prvalue (eski değeri döndürür)
getString()           // prvalue (fonksiyon değer döndürürse)

// const reference'a bağlama
const int& ref = 42;  // OK - prvalue'yu uzatır
```

#### Analogy (Analoji):

**Pizza kuryesinin kutusu** - Geçici, kullan-at, adresini bilmiyorsun.

-----

### 3\. xvalue (eXpiring Value)

**Simple Definition (Basit Tanım):** Kimliği olan ama taşınmak üzere olan değerler.

#### Features (Özellikleri):

  - ✅ Kimlik sahibi (glvalue özelliği)
  - ✅ Taşınabilir (rvalue özelliği)
  - ❌ Direkt adres alınamaz

#### Examples (Örnekler):

```cpp
std::move(x)          // x'i xvalue yapar
static_cast<T&&>(x)   // Cast ile xvalue

// Fonksiyon rvalue& döndürürse
T&& getRvalue() { return std::move(obj); }
getRvalue()           // xvalue döner
```

#### Analogy (Analoji):

**Taşınmaya hazır koltuk** - Hâlâ koltuk ama artık başka yere gidecek.

-----

### 4\. Hybrid Categories (Karma Kategoriler)

#### glvalue (Generalized lvalue)

**lvalue + xvalue** = Kimlik sahibi ifadeler.

```cpp
int x = 10;           // x → lvalue → glvalue
std::move(x)          // xvalue → glvalue
```

#### rvalue (Right-hand value)

**prvalue + xvalue** = Geçici veya taşınabilir ifadeler.

```cpp
42                    // prvalue → rvalue
std::move(x)          // xvalue → rvalue
```

-----

#### Practical Determination Table (Pratik Belirleme Tablosu)

| İfade | Kategori | Neden |
|-------|----------|--------|
| `x` | lvalue | Değişken ismi |
| `42` | prvalue | Literal |
| `x + y` | prvalue | Hesaplama sonucu |
| `++x` | lvalue | Değişkenin kendisini döndürür |
| `x++` | prvalue | Eski değeri geçici olarak döndürür |
| `func()` | prvalue | Değer dönen fonksiyon |
| `getRef()` | lvalue | lvalue& dönen fonksiyon |
| `std::move(x)` | xvalue | Taşıma için hazırlanmış |
| `"string"` | lvalue | String literal (özel durum) |

-----

### Code Examples (Kod Örnekleri)

#### Basic Examples (Temel Örnekler):

```cpp
#include <iostream>
#include <utility>

int getValue() { return 42; }
int& getRef() { static int x = 10; return x; }

int main() {
    int x = 5;
    
    // lvalue örnekleri
    std::cout << "=== lvalue Examples ===" << std::endl;
    x = 10;                    // x sol tarafta - OK
    int* ptr = &x;             // Adres alma - OK
    getRef() = 20;             // Fonksiyon lvalue döndürüyor - OK
    
    // prvalue örnekleri  
    std::cout << "=== prvalue Examples ===" << std::endl;
    const int& ref1 = 42;      // const ref'a bağlama - OK
    const int& ref2 = x + 5;   // Hesaplama sonucu - OK
    // int& ref3 = 42;         // HATA! non-const ref'a bağlanamaz
    
    // xvalue örnekleri
    std::cout << "=== xvalue Examples ===" << std::endl;
    int&& rref = std::move(x); // Move - OK
    std::cout << "x after move: " << x << std::endl; // x hâlâ kullanılabilir
    
    return 0;
}
```

#### Reference Binding Rules (Reference Bağlama Kuralları):

```cpp
#include <string>

int main() {
    std::string str = "Hello";
    
    // lvalue binding
    std::string& lref = str;              // lvalue -> lvalue&
    const std::string& cref1 = str;      // lvalue -> const lvalue&
    
    // rvalue binding  
    std::string&& rref1 = "World";       // prvalue -> rvalue&
    std::string&& rref2 = std::move(str);// xvalue -> rvalue&
    const std::string& cref2 = "Temp";   // prvalue -> const lvalue&
    
    // HATALAR
    // std::string& lref2 = "Error";     // HATA: prvalue -> lvalue&
    // std::string&& rref3 = str;        // HATA: lvalue -> rvalue&
    
    return 0;
}
```

-----

### Summary Table (Özet Tablo)

| Kategori | Kimlik | Geçici | Adres | Sol Taraf | Örnek |
|----------|--------|--------|-------|-----------|--------|
| **lvalue** | ✅ | ❌ | ✅ | ✅ | `x`, `++x` |
| **prvalue** | ❌ | ✅ | ❌ | ❌ | `42`, `x+y` |
| **xvalue** | ✅ | ✅ | ❌ | ❌ | `std::move(x)` |

-----

### Quick Reminder (Hızlı Hatırlatma)

#### Simple Test Questions (Basit Test Soruları):

1.  **Adresini alabilir miyim?** → Evet ise **lvalue** olabilir.
2.  **Sol tarafta kullanabilir miyim?** → Evet ise **lvalue**.
3.  **Geçici mi?** → Evet ise **rvalue** (prvalue veya xvalue).
4.  **`std::move` kullandım mı?** → Evet ise **xvalue**.

#### Practical Tips (Pratik İpuçları):

  - **Değişken isimleri**: Hep **lvalue**.
  - **Literaller**: Genelde **prvalue** (string literal hariç).
  - **Fonksiyon sonuçları**: Dönüş türüne bağlı.
  - **`std::move` sonucu**: Her zaman **xvalue**.
  - **Hesaplamalar**: Genelde **prvalue**.

-----

## Reference Collapsing and Universal References (Referans Katlama ve Universal Referanslar)

### Reference Collapsing (Referans Katlama)

C++11 ile birlikte, `T&&` (r-value referans) ve `T&` (l-value referans) türlerinin birbirine dönüşmesi ve şablonlarda doğru türlerin çıkarılması için `reference collapsing` işlemi kullanılır. Özellikle `Perfect Forwarding` kullanılırken, referanslar arasında birleştirme yaparak doğru tür elde etmek için `Reference Collapsing` işlemi yapılır.

**Conversion Rules (Dönüşüm Kuralları):**

  * `T&` + `T&` → `T&` (İki l-value referansı birleştiğinde, l-value referansı olur.)
  * `T&` + `T&&` → `T&` (l-value ve r-value referanslarının birleşmesinde, l-value referansı kullanılır.)
  * `T&&` + `T&` → `T&` (l-value ve r-value referanslarının birleşmesinde, l-value referansı kullanılır.)
  * `T&&` + `T&&` → `T&&` (İki r-value referansı birleştiğinde, yine r-value referansı kullanılır.)