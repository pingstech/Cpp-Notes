# 4.Ders

## Scope Enum

Enum türlerini **daha güvenli ve anlaşılır** hale getirmek için C++11 ile eklenmiş bir özelliktir. **```enum class```**, enum türlerinin **global alanda birbirine çakışmasını engeller** ve güçlü tür denetimi sağlar. Bu, büyük projelerde kodun okunabilirliğini artırır ve hata olasılıklarını azaltır.

```cpp
enum class traffic_light {Red, Yellow, Green};
```

### Scope Enum ve Tür Belirtme:

**```enum```** elemanlarının türünü de belirleyebilmemizi sağlar.

```cpp
// "column(:) token"ının sağına yazdığımız tür o enum'ın "underlying type"ı olur.

enum i_Pos : int {On, Off, Hold, Standby};  // "underlying type"ı int'tir.
enum c_Pos : char {On, Off, Hold, Standby}; // "underlying type"ı char'dır.
enum l_Pos : long {On, Off, Hold, Standby}; // "underlying type"ı long'tur.
enum uc_Pos : unsigned char {On, Off, Hold, Standby}; // "underlying type"ı unsigned char'dır.
```

**NOT-1:** **```i_Pos```** olarak tanımlanan scoped enum örnek aldığımızda, burada:
- ```i_Pos```, scoped enum türünün adıdır.
- ```: int``` kısmı, enum'un temel türünün ```int``` olduğunu belirtir. Yani, enum'un her elemanı aslında bir ```int``` türünde saklanacaktır.
- ```On, Off, Hold, Standby``` ise enum elemanlarıdır.

⚠️**NOT-2:**
1) Eğer tür belirtmezseniz, enum elemanlarının **varsayılan türü ```int```** olur.

2) Ancak, tür belirtmek, enum'un değerlerinin türünü daha kontrol edilebilir ve uygun hale getirebilir, örneğin:

    - ```int```, ```char```, ```unsigned int```, ```short``` gibi farklı veri türlerini belirleyebilirsiniz.
    - Bu, hafıza yönetimi açısından faydalı olabilir, özellikle sınırlı bellek alanında çalışırken.

---

## Name Lookup, Context Control ve Access Control'ün birbirleri arasındaki bağlantı:

1) **Name Lookup**, bir ismin hangi öğeye (değişken, fonksiyon, sınıf vb.) karşılık geldiğini bulma işlemidir.
2) **Context Control**, bir ismin hangi kapsamda geçerli olduğunu belirler.
3) **Access Control**, bir isme erişimin mümkün olup olmadığını (örneğin, ```private```, ```public```, ```protected``` gibi kurallara göre) kontrol eder.

    Bu üç kavram, birbirini tamamlar. **Context control** hangi ismin hangi kapsamda bulunduğunu belirler, **name lookup** doğru öğeyi bulur, ve **access control** ise bu öğeye erişimin olup olmadığını denetler. Bu bağlantı, C++ dilinin kapsam yönetimi, isim çakışmalarının önlenmesi ve güvenli erişim için kritik öneme sahiptir.

---

## ```::``` - Scope Resolution Operator(Çözünürlük Operatörü)

Bir isim alanı, sınıf, fonksiyon veya değişken gibi öğelere erişimi sağlayan bir operatördür. Bu operatör, bir öğenin **hangi kapsamda** (scope) tanımlandığını belirtmek için kullanılır. Yani, programda aynı isme sahip farklı öğeler olduğunda, hangi öğeye erişileceğini belirtmek için kullanılır.

Temel anlamda:
- **Global scope**'ta tanımlanan bir öğeye erişim sağlar.
- **Sınıf üyeleri** ve **global değişkenler** arasındaki çakışmalarda kullanılır.

```cpp
int val = 56;

int main()
{
    int val = 10;

    std::cout << "val =" << val << '\n';    // Yereldeki değeri alacaktır yani "val = 10" çıktısını verir
    std::cout << "val =" << ::val << '\n';  // Globaldeki değeri alacaktır yani "val = 56" çıktısını verir    
}
```

**NOT-1:** 
- ```val``` değişkeni **yerel alanda** tanımlandığında, yerel ```val``` kullanılır.
- ```::val``` ise **global** ```val```'i ifade eder.

```cpp
void func(int x)    // Fonksiyon ismi olan "func" global namespace içerisindedir.
{
    int func = 40;  // Block içindeki "func" ise block scope içerisinde.

    //...
    ::func(func)    // Recursive bir çağrı olur(syntax gösterilmesi için anlatılmıştır.)
    //...
}
```

---

## Qualified ve Unqualified Name

### Qualified Name:

Bir ismin hangi kapsama ait olduğunu belirtmek için **namespace adı**, **sınıf adı** veya başka nitelikler kullanır.

```::``` (scope resolution operator) kullanılarak, belirli bir kapsamdan isim alınır.

```cpp
int x = 5;  // Global scope

namespace my_name_space 
{
    int x = 10;  // my_name_space scope
}

void func() 
{
    std::cout << my_name_space::x << std::endl;  // Nitelikli isim kullanılarak, my_name_space'teki x'e erişilir
}

int main() 
{
    func();
    std::cout << x << std::endl;  // Global x'yi kullanır
    return 0;
}
```

**NOT-1:** Burada, ```my_name_space::x``` **qualified name** kullanılarak, belirli bir namespace'deki ```x```'e erişilmiştir.

### Unqualified Name:

Bir isim alanı (namespace), sınıf veya başka bir kapsam belirlemeden kullanılan **basit isimlerdir**. Yalnızca o isim **o kapsam içinde** geçerli olduğu sürece kullanılabilir.

Eğer bir isim **global kapsamda** veya **yerel kapsamda** tanımlanmışsa, o ismi kullanmak için ek bir nitelik (namespace veya sınıf adı) eklemeye gerek yoktur.

```cpp
int x = 5;  // Global scope

void func() 
{
    int x = 10;  // Local scope
    std::cout << x << std::endl;  // Burada niteliksiz isim kullanılır, yerel x'e referans verir
}

int main() 
{
    func();
    std::cout << x << std::endl;  // Global x'yi kullanır
    return 0;
}
```

**NOT-2:** Burada, x'in **niteliksiz ismi** kullanılmıştır çünkü hiçbir sınıf veya namespace adı belirtilmeden doğrudan erişilmiştir.

### Qualified ve Unqualified Name Örnek

```cpp
enum class color {Blue, Red, Purple, White, Black};
enum class traffic_light {Red, Yellow, Green};  // "color" enum scope'u ile aynı isimler olması rağmen çakışma olmadı.

int main()
{
    color::Blue // Sadece "Blue" şeklinde bir arama yapılamaz. Bundan dolayı artık niteliyerek yani Qualified Name olarak kullanabiliriz.
}
```

**NOT-3:** Eğer geçmişten gelen kod(Legacy kod) söz konusu değilse, artık **unscope enum** yerine **scope enum** kullanılmalıdır!

```cpp
enum class color {Blue, Red, Purple, White, Black};

void func()
{
    color c1{ color::Blue };
    c1 = color::White;

    // C++20'den önce bir araç olmadığı için yukarıdaki gibi tanım yapılması gerekilirdi.

    // ----------------------------------------
    
    // C++20 ile birlikte aşağıdaki gibi "using enum" bildirimi yapılabilir.

    using enum color;   // Bu tanımlama ile bu kapsamda, sadece "color" ifadesini kullanarak "scope enum"a erişim sağlayabiliriz.

    color c1 = Red;
    c1 = Blue;

    using color::Blue, color::Red;

    color c2 = Blue;
    color c3 = Red;
    color c4 = White; // Bu satır "Syntax hatası" oluşturur. Çünkü using bildirimi ile sadece "Blue" ve "Red" tanımlanmıştır.

}
```

```cpp
enum class base_color {Blue, Red, Purple, White, Black};
enum class screen_color {Blue, Red};

void func()
{
    using enum base_color;
    using enum screen_color;    // Syntax hatası oluşur.

    // iki "scope enum"ın içeriklerinde benzer değerler bulundurduğu için bu şekilde kullanımı mümkün değildir. "Syntax hatası" oluşur!
}
```

---

## If with initialization

C++11 ile tanıtılan bir özelliktir ve if koşulu içinde **değişken başlatma** yapmayı mümkün kılar. Bu özellik, kodunuzu daha temiz ve okunabilir hale getirebilir, çünkü if bloğu içine bir değişkenin hem **başlatılmasını** hem de **koşulun kontrol edilmesini** aynı anda yapabilirsiniz.

C++'da, bir ```if``` koşulunun içine **değişken başlatma** eklemek için aşağıdaki kod snippetleri gibi kullanılır:

```cpp
int foo();

int main()
{
    int x = foo();

    if(x ! = -1)
    {
        // "x" kullanılabilir
    }
    else
    {
        // "x" kullanılabilir
    }

    // "x" kullanılabilir

// --------------------------------------------

    {
        int y = foo();

        if(y ! = -1)
        {
            // "y" kullanılabilir
        }
        else
        {
            // "y" kullanılabilir
        }

        // "y" kullanılabilir
    }

    // "y" kullanılamaz

// --------------------------------------------    

    if(z = foo())
    {
        // "z" kullanılabilir
    }
    else
    {
        // "z" kullanılabilir
    }

    // "z" kullanılamaz

}
```

```cpp
 int *get_ptr();

 int main()
 {
    if(int* ptr = get_ptr())    // "get_ptr()", "NULL" döndürmediğinde bu scope içerisinde "ptr" değişkeni "get_ptr" ile verilen adresi tutacak
    {
        // ...
    }

    // Yukarıdaki gösterim C'de de çalışmaktadır ama aşağıdaki gösterim C++ ile hayatımıza girmiştiştir.

    if(auto prt = get_ptr())
    {

    }

 }
```

Ancak, **if with initialization** ile daha da esnek hale gelir:


```cpp
int foo();
int bar();
int *get_ptr();

int main()
{
    if(int x = foo(); x)                    // 'x'in başlangıç değeri 0'dan farklı ise bu döngü çalışır.
    {
        // x kullanılabilir.
    }

    if(int x = foo(); x != 0)               // 'x'in başlangıç değeri 0'dan farklı ise bu döngü çalışır.
    {
        // x kullanılabilir.
    }

    if(int * p = get_ptr(); p)              // 'p', nullptr değilse döngü çalışır.
    {
        // p kullanılabilir.
    }

    if(int * p = get_ptr(); p != nullptr)   // 'p', nullptr değilse döngü çalışır.
    {
        // p kullanılabilir.
    }

    if(int x = foo(), y = bar; x + y > 10)  // "x+y" işleminin sonucu, 10'dan büyükse döngü çalışır.
    {
        // x ve y kullanılabilir.
    }
}
```

---

## Type-Cast Operators(Tür Değişim Operatörleri)

Bir türden başka bir türe dönüştürme işlemini yapabilmek için kullanılan özel operatörlerdir. C++'ta tür dönüşümleri, verilerin bir türünden başka bir türe dönüştürülmesini sağlar. Bu, özellikle farklı türlerdeki verileri birbirine uyumlu hale getirmek için önemlidir.

| **Operatör**       | **Kullanım Alanı**                             | **Güvenlik**                                    | **Çalışma Zamanı Kontrolü** |
| ------------------ | ---------------------------------------------- | ----------------------------------------------- | --------------------------- |
| `static_cast`      | Uyumlu türler arasında dönüşüm                 | Güvenli                                         | Derleme Zamanı              |
| `dynamic_cast`     | Polimorfik sınıflar arasında dönüşüm           | Güvenli, çalışma zamanı hatalarını kontrol eder | Çalışma Zamanı              |
| `const_cast`       | `const` veya `volatile` nitelikleri değiştirme | Dikkatli kullanılmalı                           | Derleme Zamanı              |
| `reinterpret_cast` | Düşük seviyeli tür dönüşümleri (işaretçi, vb.) | Güvensiz, dikkatli kullanılmalı                 | Derleme Zamanı              |


### C-Style Cast(C Tarzı Cast)

C++ dilinde, tür değiştirme işlemi **C tarzı** cast ile yapılabilir. Ancak, bu yöntem güvenli değildir ve dönüşüm sırasında hata yapma olasılığını artırabilir.

```cpp
int x = 10;
double y = (double)x;  // C tarzı cast
```

Burada, int türündeki x değişkeni double türüne dönüştürülmüştür.

### ```static_cast``` (Statik Cast)

C++'ta, ```static_cast``` daha güvenli bir tür dönüşümünü sağlar. Bu operatör, birbirine uyumlu türler arasında dönüşüm yapmak için kullanılır. ```static_cast```, **derleme zamanı** dönüşümü sağlar ve genellikle matematiksel ve mantıksal tür dönüşümleri için uygundur.

**Tam sayı** türleri ile **gerçek sayı** türleri arasında yapılacaksa kesinlikle **```static_cast```** kullanılır!

```cpp
    int x = 10;
    int y = 30;

    double d_val = static_cast<double>(x) / y;  // static_cast kullanımı
```

```static_cast``` ile, ```x``` değişkeni güvenli bir şekilde ```double``` türüne dönüştürülür

**NOT-1:** ```static_cast```, **yalnızca uyumlu türler arasında dönüşüm yapılmasına izin verir. Örneğin, türetilmiş sınıf nesneleri ile temel sınıf nesneleri arasında geçiş yapılabilir.**

```cpp
enum color {blue, black, green};

int main()
{
    int i_val = 2; // Bu değer, 'green' enumerator'üne karşılık gelir.
    color my_color{ black };

    // C++, tür güvenliği nedeniyle bir tamsayıdan enum türüne doğrudan atamaya izin vermez.
    // my_color = i_val; // Bu satır derleme hatası verir.

    // Atama yapabilmek için 'static_cast' ile açık (explicit) tür dönüşümü gereklidir.
    my_color = static_cast<color>(i_val); 

    // Artık 'my_color' değişkeninin değeri 'green' olmuştur.
}
```

Yukarıdaki örnekte, ```int``` türünden ```enum``` türüne doğrudan atama yapılamayacağını ve bu nedenle ```static_cast``` kullanmanın neden gerekli olduğunu vurguluyoruz.

```cpp
#include <iostream>

// C-tarzı, kapsamsız (unscoped) enum
enum x_color { x_blue, x_black, x_green };

// Modern C++, kapsamlı (scoped) enum
enum class y_color { y_blue, y_black, y_green };

// Enum değerlerini string'e çevirmek için kullanılan dizi
const char* const p_colors[] = {"blue", "black", "green"};

int main()
{
    // --- 1. C-tarzı 'enum' ile kullanım ---
    x_color my_x_color{ x_green };

    // Geçerli: C-tarzı 'enum', dizi indeksi olarak kullanılabilen tamsayılara örtülü olarak dönüşür.
    std::cout << p_colors[my_x_color] << '\n'; // Çıktı: green


    // --- 2. 'enum class' ile kullanım ---
    y_color my_y_color{ y_color::y_green };

    // HATA: 'enum class', tür güvenliği nedeniyle tamsayıya örtülü olarak DÖNÜŞMEZ.
    // Bu nedenle, dizi indeksi olarak doğrudan kullanılamaz ve derleme hatası verir.
    // std::cout << p_colors[my_y_color] << '\n'; // Hata: "array subscript is not an integer"

    // DOĞRU KULLANIM: 'enum class' değerini dizi indeksi olarak kullanmak için
    // 'static_cast' ile tamsayıya açıkça dönüştürmek gerekir.
    std::cout << p_colors[static_cast<int>(my_y_color)] << '\n'; // Çıktı: green
}
```

Yukarıdaki örnek, C-tarzı ```enum``` ile modern ```enum class``` arasındaki en temel farklardan birini gösterir: **tamsayıya örtülü (implicit)** dönüşüm.

### ```const_cast ``` (Const  Cast)

```const_cast```, bir nesnenin const özelliğini değiştirmek için kullanılır. Yani, bir **const** nesneyi **değiştirilebilir** hale getirebiliriz. Ancak, const bir nesne üzerinde değişiklik yapmak hatalı olabilir ve undefined behavior (tanımlanmamış davranış) ile sonuçlanabilir.



```cpp
void modify(const int& x) 
{
    int& y = const_cast<int&>(x);  // const_cast ile const olan nesneye referans alınır
    y = 20;  // const nesne değiştirilmeye çalışılabilir, ancak dikkatli olunmalıdır
}

int main() 
{
    const int a = 10;
    modify(a);  // const nesneyi değiştirmeye çalışıyoruz
    std::cout << "Değiştirilen değer: " << a << std::endl;  // Bu, undefined behavior olabilir
    return 0;
}

```
- Pointer türüne ```const_cast``` uygulamak:

```cpp
#include <iostream>

int main()
{
    // --- GÜVENLİ KULLANIM ---
    // Orijinal değişken 'const' değil.
    int x = 56;
    
    // 'const' pointer, 'const' olmayan veriye işaret ediyor.
    const int* ptr_to_non_const = &x; 
    
    // Güvenli: Orijinal veri 'const' olmadığı için 'const_cast' kullanılabilir.
    int* non_const_ptr = const_cast<int*>(ptr_to_non_const); 
    
    *non_const_ptr = 100; // Tamamen geçerli bir işlem. x'in değeri 100 olur.
    std::cout << "x'in yeni degeri: " << x << std::endl; // Çıktı: 100


    // --- TANIMSIZ DAVRANIŞ (TEHLİKELİ KULLANIM) ---
    // Orijinal değişken 'const' olarak tanımlandı.
    const int const_x = 56;

    // 'const' pointer, 'const' veriye işaret ediyor.
    const int* ptr_to_const = &const_x;

    // 'const_cast' ile const'luk kaldırılıyor.
    int* dangerous_ptr = const_cast<int*>(ptr_to_const); 

    // TANIMSIZ DAVRANIŞ: Orijinali 'const' olan bir veriyi değiştirmeye çalışmak!
    // Bu satır programın çökmesine veya beklenmedik şekilde çalışmasına neden olabilir.
    // *dangerous_ptr = 200; 
}
```

⚠️⚠️⚠️**NOT-2**: Kendisi ```const``` olan bir nesnenin adresini kesinlikle **operatör vasıtası ile dönüşüm yapılmaz!** **Syntax** olarak problemli olmasada **Undefined behavior**'dır.

- Referans türüne ```const_cast``` uygulamak:

```cpp
int main()
{
    // --- GÜVENLİ KULLANIM ---
    // Orijinal değişken 'const' değil.
    int y = 120;
    
    // 'const' referans, 'const' olmayan veriye bağlanıyor.
    const int& ref_to_non_const = y;

    // Güvenli: Orijinal veri 'const' olmadığı için 'const_cast' kullanılabilir.
    // DİKKAT: Kodunuzdaki 'const_char' yazımı 'const_cast' olarak düzeltildi.
    int& non_const_ref = const_cast<int&>(ref_to_non_const);

    non_const_ref = 300; // Tamamen geçerli bir işlem. y'nin değeri 300 olur.
    std::cout << "y'nin yeni degeri: " << y << std::endl; // Çıktı: 300


    // --- TANIMSIZ DAVRANIŞ (TEHLİKELİ KULLANIM) ---
    // Orijinal değişken 'const' olarak tanımlandı.
    const int const_y = 120;

    // 'const' referans, 'const' veriye bağlanıyor.
    const int& ref_to_const = const_y;

    // 'const_cast' ile const'luk kaldırılıyor.
    int& dangerous_ref = const_cast<int&>(ref_to_const);

    // TANIMSIZ DAVRANIŞ: Orijinali 'const' olan bir veriyi değiştirmeye çalışmak!
    // Bu atama derlenir ancak çalışma zamanında beklenmedik sonuçlara yol açar.
    // dangerous_ref = 400; 
}
```

### ```reinterpret_cast``` (Yorumlama Castı)

```reinterpret_cast```, iki tür arasında **bit düzeyinde** dönüşüm yapar. Bu, en düşük seviyeli tür dönüşümünü sağlar ve genellikle işaretçi veya referansların dönüştürülmesinde kullanılır.

- ```reinterpret_cast```, türler arasında güvenli olmayan, düşük seviyeli dönüşümlere izin verir.
- Bu tür dönüşümler genellikle **platform bağımlıdır** ve dikkatlice kullanılmalıdır.

```cpp
using namespace std;

int main() {
    int a = 10;
    char* p = reinterpret_cast<char*>(&a);  // int işaretçisi char işaretçisine dönüştürülür
    
    cout << "Bit düzeyindeki değer: " << *p << endl;  // Dönüştürülen işaretçinin değerini yazdırır
    return 0;
}
```

```reinterpret_cast``` ile ```int``` türündeki işaretçi, ```char*``` türüne dönüştürülür. Bu işlem, bellek üzerinde ```a``` değişkeninin bit düzeyindeki temsilini değiştirmez, ancak türler arasında **uyumsuz bir dönüşüm** yapar.

**NOT-3:** ```reinterpret_cast``` oldukça güçlüdür ancak güvenli olmayan dönüşümlere yol açabilir. Çok *dikkatli kullanılmalıdır**.

### Type-Cast Operators ÖZET:

C++'ta Tür Değişim Operatörleri (Type-Cast Operators) dört ana türde gelir: ```static_cast```, ```dynamic_cast```, ```const_cast```, ve ```reinterpret_cast```. Her biri farklı amaçlar için kullanılır:

- ```static_cast``` uyumlu türler arasında güvenli dönüşüm sağlar.

- ```dynamic_cast``` sınıf hiyerarşileri içinde güvenli dönüşüm yapar.

- ```const_cast```, ```const``` veya ```volatile``` niteliklerini değiştirir.

-  ```reinterpret_cast```, bit düzeyinde tür dönüşümleri yapar ve dikkatle kullanılmalıdır.


## ```[[nodiscard]]``` Attribute

C++17'de tanıtılan ```[[nodiscard]]``` özniteliği, bir fonksiyonun geri dönüş değerinin **göz ardı edilmemesini** sağlamak için kullanılır. 

- ```[[nodiscard]]```, dönüş değeri önemli olan fonksiyonların çağrıldığında bu değerin göz ardı edilmesini önlemek için kullanılır.

```cpp
[[nodiscard]] int foo() 
{
    return 42;
}

int main() 
{
    foo();  // Uyarı: 'foo' fonksiyonunun dönüş değeri kullanılmamış
    return 0;
}
```

**NOT-1:** ```[[nodiscard]]``` eklenen fonksiyonlar, dönüş değeri kullanılmazsa derleyici tarafından uyarılır. Bu, genellikle **hata kodları** veya **önemli geri dönüş değerleri** içeren fonksiyonlar için kullanılır.