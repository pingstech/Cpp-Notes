# 2.Ders

## Decltype

"```decltype```" operatörü, operandının türünü almak için kullanılır. Bu, bir isim veya bir ifade üzerinden tür çıkarımı yapılmasına olanak sağlar. İki farklı kural seti vardır:

### 1. Kural Seti:
Eğer ```decltype```'ın operandı bir **isim** ise, bu kural seti geçerlidir.

```cpp
decltype(c)
decltype(a.b)
decltype(ptr->c)

int main()
{
    int x = 120;
    decltype(x) x_d;  // 'x_d', int türünde bir değişken olur çünkü 'x' int türündedir.

    const int y = 100;
    decltype(y) y_d = 25; // 'y_d' const int türünde olur çünkü 'y' const int türündedir.

    int z = 5;
    int& z_r{ z };
    decltype(z_r) z_d = z; // 'z_r' bir l-value referansıdır, bu yüzden 'z_d' de int& türünde bir referans olur. 
                             // 'decltype(z_r) z_d = z;' -> int& z_d = z;
                             // Referanslar default olarak initialize edilmediği için 'z_d' doğru bir şekilde 
                             // bir değerle başlatılmalıdır.

    int&& v = 10;
    decltype(v) v_d = 56; // 'v' bir r-value referansı olduğu için 'v_d' da int&& türünde bir değişken olur. 
                           // 'decltype(v) v_d = 56;' -> int&& v_d = 56;

    int&& w = 10;
    decltype(w)& w_d = 56;  // Burada 'w_d' 'decltype(w)' ile int&& türünde tanımlanmışken,
                            // '&' ile sağdaki r-value'yu bir l-value referansına çeviririz.
                            // Ancak, **reference collapsing** nedeniyle bu ifade hata verir: L-value referansları
                            // r-value referanslarıyla birleştirildiğinde bir tür uyuşmazlığı oluşur. 

    decltype(w)&& w_d_d = 56;  // Bu ifade legal olacaktır çünkü 'decltype(w)' r-value referansı döndürür ve
                                // '&&' ile yine bir r-value referansı elde edilir, böylece 'w_d_d' doğru bir şekilde
                                // bir r-value referansı olur.

    int a[5]{};
    decltype(a) a_d = a;  // Burada 'a' bir dizi olduğundan 'decltype(a)' türü 'int[5]' olur.
                           // Fakat dizilere bir diziyi atamak mümkün değildir (syntax hatası verir). 

    decltype(a) a_d_t = {1, 2, 3, 4, 5}; // Ancak diziyi doğrudan bir başlangıç listesiyle başlatmak mümkündür.

    int b[20]{};
    decltype(b)* b_p = nullptr; // Burada 'decltype(b)' ile 'int[20]' türü elde edilir ve 'b_p' bir pointer olarak
                                 // doğru şekilde tanımlanabilir.

    int c = 56;
    decltype(c) *c_p = &c;  // Burada 'decltype(c)' ile 'int' türü elde edilir, bu yüzden 'c_p' 'int*' türünde
                            // doğru şekilde tanımlanabilir.
}
```


### 2. Kural Seti:

Eğer **decltype** operandı bir **ifade** ise, bu kural geçerlidir. Operandın türü, ifade türünün **değer kategorisine** (value category) bağlıdır. Değer kategorisi, **prvalue**, **lvalue** ve **xvalue**'lardan birini ifade eder.

**PRvalue** (Pure rvalue, saf sağ değer) ise elde edilen tür ```T``` olur.

**Lvalue** (sol değer) ise elde edilen tür ```T&``` olur.

**Xvalue** (expiring value, tükenmiş değer) ise elde edilen tür ```T&&``` olur.

```cpp
decltype(10)        // int  türü elde edilir
decltype(x+5)       // int türü elde edilir
decltype((a))       // int& türü elde edilir

int foo();
int& bar();
int&& baz();

int main() 
{
    int x = 10;
    decltype(x + 5) x_result; // 'x + 5' bir PRvalue ifadesidir, dolayısıyla 'int' türü elde edilir.

    int y[5]{};
    decltype(a[2]) x; // 'a[2]' bir l-value referansıdır, dolayısıyla 'int& x' türü elde edilir.
                       // Ayrıca, 'decltype(a[2]) x;' yazıldığında 'int& x' olarak anlaşılır ve 'x' başlatılmadan önce
                       // bir değer verilmelidir.

    int ival{};
    decltype(a[2]) x_2 = ival; // Burada bir syntax hatası oluşmaz, çünkü 'ival' bir l-value'dur.

    int z{435};
    int *z_p{ &z };
    decltype(*z_p) z_d;  // 'decltype(*z_p)' ifadesi 'int&' türünü döndürür, bu nedenle 'z_d' 'int&' türünde olur.
    decltype(z++) z_d2 = 10; // 'decltype(z++)' ifadesi 'int' türünde bir değer döndürecektir çünkü postfix increment
                              // bir r-value oluşturur.
    decltype(++z) z_d3 = 10; // 'decltype(++z)' ifadesi 'int&' türünü döndürür. Ancak burada bir syntax hatası oluşur
                              // çünkü pre-increment ile oluşturulan bir referans türü, doğru şekilde başlatılamaz.

    foo(); // PRvalue
    bar(); // Lvalue döndüren bir fonksiyon
    baz(); // Xvalue döndüren bir fonksiyon

    decltype(foo());    // int türü elde edilir
    decltype(bar());    // int& türü elde edilir
    decltype(baz());    // int&& türü elde edilir

    decltype(bar()) b_d = 5; // L-value expression'a r-value atandığında syntax hatası alırız.

    decltype(baz()) f_d = 6; // int&& türü ile r-value assign edilirse legal olur.
}
```

### Karışık örnek:
```cpp
int main() 
{
    int x = 10;
    int y = 20;

    decltype(x) a = y;       // 'a' 'int' türünde olur, çünkü x bir int'tir.
    decltype((x)) b = y;     // 'b' bir l-value referansıdır, bu nedenle 'int& b = y' anlamına gelir.
}

```

---

## Unevaluated context(İşlem Kodu Üretilmemiş Bağlam)
Derleyici tarafından değerlendirilmeden önce analiz edilmesi gerektiği durumları belirtir. **decltype operandı** buna büyük bir örnektir.

```cpp
int main()
{
    int x = 10;
    decltype(++x) x_d = x;      // 'x_d', 'x'e bağlanmış L-value bir referans.
                                // "decltype" operatörü bir ifade olduğunda, operandın değeri derleme zamanında hesaplanmadan türü çıkarılır.
                                // Bu nedenle, burada 'x'in değeri değişmeyecek! (x++) gibi yan etkiler göz ardı edilir)

    std::cout << "x = " << x << '\n';

    ++x_d;                      // Burada x'in değeri artar, çünkü x_d bir l-value referansı

    std::cout << "x = " << x << '\n';    
}
```

**NOT-1:** 
```decltype(++x)``` burada bir **unevaluated context**'te çalışır. **Yan etkiler (yani, ++x)** göz ardı edilir ve yalnızca tür çıkarımı yapılır.

```cpp
int foo()
{
    std::cout << "foo fonksiyonu çağırıldı\n";
    return 1;
}

int main()
{
    int x = 10;

    decltype(foo()) y = x;  // "foo()" fonksiyonu "decltype" operandı olarak kullanıldığı için                    fonksiyonun içindeki yan etkiler çalışmaz.
                            // Yani, "foo()" fonksiyonu çağrılsa da, içindeki kod derleme zamanında etkili olmaz. 
}
```

**NOT-2:**  ```decltype(foo())``` ile ```foo()``` fonksiyonunun **derleme zamanında** yan etkilerinin uygulanmadığını görürüz.

---

## Default Argument (Varsayılan Argüman)
Bir fonksiyonun parametrelerine **varsayılan değerler** atayarak, fonksiyon çağrıldığında bu parametrelerin belirtilmediği durumlarda otomatik olarak bu değerlerin kullanılmasını sağlar.

```cpp
void foo(int, ...)      // Variyadik bir fonksiyondur ("..." elipsis token )

int main()
{
    foo();                  // Bu şekilde çağırılması geçersizdir çünkü "elipsis    token"dan önce bir int parametresi beklenir.
    foo(10);                // Tanımlıdır.
    foo(10, 11, 12, 13);    // Tanımlıdır.
}
```

**NOT-1:** **Elipsis token (```...```), variadic** fonksiyonlarda kullanılır ve belirli bir türdeki parametrelerin sayısının önceden bilinmediği durumlarda kullanılır.

### Default Argument bildirimi:

```cpp
void t_func(int, int, int, int = 0);    // Fonksiyonun 4. argümanı "default argument" bildirimiyle tanımlanmıştır.
void f_func(int, int = 0, int);         // "default argument" bildirimi yapıldıktan sonra, sonraki parametre de varsayılan değer almalıdır. Aksi takdirde hata oluşur.
void x_func(int, int = 1, int = 2);     // Fonksiyonun 2. ve 3. argümanı "default argument" bildirimiyle tanımlanmıştır.

int main()
{
    t_func(1, 2, 3);    // Tanımlıdır. (t_func(1, 2, 3) olarak çağrılır)
    t_func(1, 2);       // Tanımlıdır. t_func(1, 2, 0) olarak çağrılmış olur.
    x_func(1);          // Tanımlıdır. x_func(1, 1, 2) olarak çağrılır.
}

void x_func(int x, int y, int z)    // Fonksiyon bildirimi genellikle ".h" dosyasında yapılmalı, burada ise ".cpp" dosyasına yazılmalıdır!
{
    std::cout << "x = " << x << "| y = " << y << "| z = " << z << '\n';
}

```

**NOT-1:** **Varsayılan değerlerin** sağdan sola doğru verilmesi gerektiğini unutmayın.

#### Kullanım örneği-1:

```cpp
int y = 10;

void func(int = ++y)  // Varsayılan argüman ++y ile sağlanır

int p_func()
{
    std::cout << '|';
    return 1;
}

void s_func(int = p_func())  // p_func() fonksiyonu çağrılacak.
{

}

int main()
{
    func();     // func(++y); -> y = 11
    func();     // func(++y); -> y = 12
    func();     // func(++y); -> y = 13

    std::cout << "y = " << y<< '\n';    // "default argument" direkt çağırıldığı yerde kullanıldığı için her fonksiyonun çağırılmasında "++y" gerçekleşecektir.

    s_func();   // "p_func()" fonksiyonu çağırılır.
    s_func();   // "p_func()" fonksiyonu çağırılır.
    s_func();   // "p_func()" fonksiyonu çağırılır.
}
```

**NOT-2:** **Varsayılan argümanlar**, fonksiyon çağrıldığında her defasında **yeniden hesaplanır** (örneğin ```++y```).

#### Kullanım örneği-2:

```cpp
int g = 10;

void a_func(int *p = &g);   // Pointer parametreler varsayılan argüman alabilir. 

void b_func(int &p = g);    // Referans parametreler varsayılan argüman alabilir. "void b_func(int & = g);" şeklinde de yazılabilir.

void c_func(const char * p = "Furkan"); // Syntax hatası yoktur. 
void c_func(const char *= "Furkan");    // Syntax hatası vardır, Bu şekilde bir tanımlama legal değildir. "Maximal munch" kuralından dolayı bu şekilde bir tanımlama yapılamaz!
void c_func(const char * = "Furkan");   // Syntax hatası yoktur. '*' tokenı boşluk ile ayrılarak "Maximal munch" kuralına uyulmuştur.

void d_func(int x, int y = x);          // "Illegal Code" hatası alınır. Bir parametre kendinden önceki bir parametreyi "Varsayılan argüman" olarak kullanamaz.

int foo(int x = 5)
int bar(int x = foo())

int main()
{
    bar();  // Fonksiyonun varsayılan parametresi "foo()" fonksiyonu ve bu fonksiyonun varsayılan parametresi '5'tir. Bundan dolayı bu fonksiyon "bar(foo(5));" şeklinde çağrılır.
}
```

**NOT-2:** Bir fonksiyon bildiriminde "**function redeclaration**" yapılarak birden fazla "**default argument**" bildirimi yapılabilir.

```cpp
/* api.h dosyası*/
void foo(int, int, int);
/*
void foo(int, int, int = 10); // "Function re-declaration" yapılmıştır.
void foo(int, int, int = 10); // Daha önceden aynı şekilde bir "redeclaration" yapılmışsa "Syntax hatası" oluşur!
*/

/* api.c dosyası*/
#include "api.h"

//Not: Bu işlem sürekli sabit bir parametre girilmesi gerekiyorsa yapılabilir.
void foo(int, int, int = 10);   // "Function redeclaration" kullanılarak "Varsayılan argüman" özelliği kullanılabilir. 

void foo(int, int = 8, int);    // Bir "Syntax hatası" oluşmaz, fakat "foo(int, int = 8, int = 10)" yapılsaydı "Syntax hatası" oluşurdu. Varsayılan argüman ikinci kez deklere edilemez!

//Not-2: Fonksiyonlar kümülatif olarak çağırıldığı için "foo()" fonksiyonunu tekrardan tanımlamamız bir hataya sebep olmaz.

int main()
{
    foo(2, 5);  // fonksiyon foo(2, 5, 10); şeklinde çağırılmış olur.
    foo(2);     // fonksiyon foo(2, 8, 10); şeklinde çağırılmış olur.
}
```

### "constexpr" Anahtar Sözcüğü

```constexpr``` belirli değişkenlerin veya fonksiyonların derleme zamanında hesaplanabilmesini sağlayan bir anahtar kelimedir.

- Bu tür değişkenler veya fonksiyonlar, **derleyici tarafından** hesaplanır ve bu hesaplama sırasında **herhangi bir runtime (çalışma zamanı) işlemine** ihtiyaç duyulmaz.
- ```constexpr``` değişkenlerin veya fonksiyonların değeri **derleme zamanında sabit** olduğu için, bu değer programın **çalışma zamanı** sırasında kullanılabilir ve optimize edilebilir.

```cpp
int main()
{
    const int x = 10;  // "const" olarak tanımlanmış bir nesne sabittir fakat çalışma zamanında bu değerin sabit olup olmadığı kontrol edilir.

    constexpr int y = 20; // "constexpr" anahtar kelimesi ile tanımlandığında, o değişkenin değeri derleme zamanında sabit olur.

    int r = 222;
    constexpr int z = r; // Bu ifade hatalıdır çünkü 'z'ye verilen ilk değer sabit bir ifade değildir! 
                          // "const int r = 222;" olarak tanımlansaydı, syntax hatası olmazdı.
}
```
