# 3. Ders

## ```constexpr``` Functions (```constexpr``` Fonksiyonlar)

```constexpr``` anahtar kelimesi, sadece sabitler (constants) ile değil, fonksiyon tanımlarında da kullanılabilir. Bu tür fonksiyonlar, yalnızca **derleme zamanında** hesaplanabilen işlemler içerir.

- ```constexpr``` ile işaretlenmiş fonksiyonlar, **derleme zamanında** hesaplanabilir. Bu, programın çalışması sırasında değil, derleme aşamasında hesaplanan sonuçların kullanılmasını sağlar.
- ```constexpr``` fonksiyonlar yalnızca **sabit ifadelerle** çalışabilir. Fonksiyonun tamamı **sabit değerlere** dayanmalıdır.
- ```constexpr``` fonksiyonlar, ```const``` fonksiyonlardan farklıdır. Bir `const` fonksiyon yalnızca bir nesnenin sabit olduğunu belirtirken, ```constexpr``` fonksiyonun değeri derleme zamanında hesaplanabilir.

```cpp
constexpr int x_func(int x)
{
    return x * x;
}

constexpr int y_func(int x)
{
    // Bu şekilde tanımlanmış bir fonksiyon, derleyici hatasına yol açar. 
    // "Static" değişkenler, derleme zamanında hesaplanamadığı için, 
    // "non-automatic storage" hatası oluşur. Çünkü statik değişkenlerin değeri runtime'da belirlenir.
    static int y = x;
    return y * y - 3;
}
```

**NOT-1:** Her fonksiyon ```constexpr``` fonksiyonu olamaz.
- ```constexpr``` fonksiyonlar, statik ömürlü yerel değişkenler içeremez.

**NOT-2:** Eğer bir fonksiyon, parametrelerine sabit ifadelerle çağrılırsa, fonksiyonun **geri dönüş değeri derleme zamanında** hesaplanır.

```cpp
constexpr int ndigit(int x)
{
    if (x == 0) { return 1; }

    int digit_count{};

    while (x)
    {
        ++digit_count;
        x /= 10;
    }

    return digit_count;
}

constexpr bool isprime(int val)
{
    if (val < 2) { return false; }

    if (val % 2 == 0) { return val == 2; }
    if (val % 3 == 0) { return val == 3; }
    if (val % 5 == 0) { return val == 5; }

    for (int i{ 7 }; i * i <= val; i += 2)
    {
        if (val % i == 0) { return false; }        
    }

    return true;
}

int main()
{
    int a[ndigit(234234)]{};    // Sol taraftaki ifade ile "int a[6]{};" yazmamız arasında bir fark yoktur.  
    
    const int x = 1234;
    const int y = 1234;

    ndigit(x * y - 19); // Fonksiyon parametreleri sabit değerdir. Bundan dolayı bir hata ile karşılaşmayız.

    const int z = 82345;
    const int r = 12191;

    constexpr auto b = isprime(z + r - 19);
}
```

---

## One Definition Rule - ODR (Tek Tanımlama Kuralı)

Bir öğenin (değişken, fonksiyon, sınıf, vb.) yalnızca **bir kez** tanımlanabileceğini belirten temel bir kuraldır. Bu kural, bir öğenin birden fazla tanımının **çakışmaması gerektiğini** garanti eder. Eğer bir öğe birden fazla kez tanımlanırsa, derleyici bir "**linker error**" verir.

- C++'ta bir fonksiyonun birden fazla tanımı olursa, bu ODR kuralını ihlal edir ve derleme zamanında hataya yol açar.

```cpp
// func.cpp dosyası
#include <iostream>

void foo() // Tanım 1
{   
    std::cout << "Hello from foo!" << std::endl;
}

int main() {
    foo();
    return 0;
}

// func_duplicate.cpp dosyası
#include <iostream>

void foo()  // Tanım 2, aynı fonksiyonu tekrar tanımlıyor
{   
    std::cout << "Hello from foo again!" << std::endl;
}

int main() 
{
    foo();
    return 0;
}
```

**NOT-1:** Yukarıdaki örnekte, ```foo()``` fonksiyonu **aynı adı taşıyan iki farklı dosyada** iki kez tanımlanmış. Bu durum **ODR kuralını** ihlal eder ve **linker error**'a neden olur. Derleyici **bu iki farklı tanımın çakıştığını** fark eder ve hata verir.

**Doğru Kullanımı:**

```cpp
// foo.h (Başlık dosyası)
#ifndef FOO_H
#define FOO_H

void foo();   // Fonksiyon bildirimi (declaration)

#endif

/* -------------------------------------- */

// foo.cpp (Kaynak dosyası)
#include "foo.h"
#include <iostream>

void foo()  // Fonksiyon tanımı (definition)
{   
    std::cout << "Hello from foo!" << std::endl;
}

/* -------------------------------------- */

// main.cpp
#include "foo.h"

int main() 
{
    foo();
    return 0;
}
```

- ```foo.h``` başlık dosyasındaki **declaration** (bildirim) birden fazla dosyada paylaşılabilir.
- ```foo.cpp``` dosyasındaki **definition** (tanım) sadece **bir dosyada** yapılmalıdır.
- Bu kullanım **ODR'yi ihlal etmez** çünkü fonksiyon sadece **bir kez tanımlanır**.

```cpp
// api.c dosyası
inline int foo(int x, int y)
{
    return x * y + 5;
}

// app.c dosyası
inline int foo(int x, int y)
{
    return x * y + 5;
}
```

**NOT-3:** **inline anahtar** kelimesi ile tanımlanmış bir fonksiyon birden fazla dosya içerisinde tekrar edilebilir (token by token aynı olmalıdır). **ODR(One Definition Rule) ihlal edilmemiş olur**!

### Hangi tanımlar ODR'yi ihlal etmez?
1) **Inline fonksiyon** tanımları  
2) **Inline değişken** tanımları (C++17)  
3) **User-defined types** tanımları  
4) **```constexpr```** fonksiyonlar  

---

## Inline Expansion (Satır İçi Genişletme)

```inline``` anahtar kelimesiyle işaretlenmiş fonksiyonların **derleyici tarafından fonksiyon çağrısı** yerine **fonksiyonun içeriği**'nin **yerine yerleştirilmesi** işlemidir. Bu sayede **fonksiyon çağrısı yapılmaz**, bunun yerine fonksiyonun kodu **doğrudan** çağrıldığı yere eklenir.

- ```inline``` fonksiyonlar, **küçük ve sıkça çağrılan** fonksiyonlar için genellikle kullanılır. **Büyük fonksiyonlar için** ```inline``` genişletmesi yapıldığında, **kod boyutunun** artmasına ve performansın **tersine etkilenmesine** yol açabilir.

### Inline Expansion Nasıl Çalışır?
- **Fonksiyon çağrısı yerine** fonksiyonun **gövdesi derleyici tarafından çağrıldığı yere eklenir**.
- Fonksiyonun **gövdeleri** sadece **derleme zamanında** eklenir. Yani, runtime'da işleme yapılmaz.
- **Inline fonksiyonlar**, genellikle **başlık dosyalarında tanımlanır**, çünkü şablonlar ve inline fonksiyonlar çoğu zaman birden fazla dosyada kullanılır.

⚠️**NOT-1:** Fonksiyonun ```inline``` anahtar kelimesi ile tanımlanması, **inline expand** edileceği garantisi kesinlikle vermez!

```cpp
inline int add(int a, int b) // Inline fonksiyon tanımı
{
    return a + b;
}

int main() 
{
    int result = add(5, 3);  // Fonksiyon çağrısı yerine fonksiyon gövdesi yerleştirilir
    std::cout << "Sum: " << result << std::endl;
    return 0;
}
```

**NOT-2:** ```add``` fonksiyonu **inline** olarak işaretlenmiştir. Bu, derleyicinin fonksiyon çağrısını doğrudan fonksiyonun içeriğiyle **değiştirmesini** sağlar. Yani, ```add(5, 3)``` çağrısı yerine derleyici ```5 + 3``` işlemini doğrudan ekler.

```cpp
// api.h dosyası 

inline int g = 10; // api.h dosyasını birden çok kere include edilse dahi run-time'da bir adet 'g' değişkeni oluşacak.
```

**NOT-3:** Yukarıda yapılmış olan tanım C++'ın 2017 standartı ile hayatımıza girmiştir. Global değişkenler için ```inline``` anahtar sözcüğü kullanılabiliyor.

---

## Enumeration Types (Numaralandırma Türleri)

Sabit sayısal değerlerin isimlendirilmiş bir koleksiyonunu tanımlamak için kullanılan bir veri türüdür. **```enum```** türleri, bir grup sabit değeri isimlendirerek daha okunabilir ve yönetilebilir hale getirir.

```cpp
enum Color {Blue, Black, White, Purple, Red}; // C'de kullanımı

int main()
{
    Color my_color;
    my_color = 3;   // C'de bu atama geçerlidir, fakat C++'da böyle bir atama yapılamaz! 
                    // C++'da sadece enum türünde tanımlanmış bir değişkene, enum tipinde tanımlanmış bir değer atanabilir!
}
```

### C++ dilinde (modern C++ öncesi), enum türlerinin istenmeyen özellikleri:
1) Underlying-type'ın (baz tür) derleyiciye bağlı olması nedeniyle, enum türleri başlık dosyalarında incomplete type olarak kullanılamıyor.

```cpp
// api.h dosyası

enum Color;

struct Data
{
  //...
  Color mc;
};

// Bu kod C'de geçerlidir fakat C++'da, enum türlerinin alt türlerinin derleyiciye bağlı olması nedeniyle, enum türlerinin incomplete-type olarak kullanılmasına engel olur.
```

2) Aritmetik türlerden, enum türlerine örtülü dönüşüm olmamasına rağmen, farklı enum türlerinden aritmetik türlere örtülü dönüşüm var.

```cpp
enum Color {Blue, Black, White, Purple, Red};
enum Pos {On, Off, Hold};

int main()
{
  Color c;
  // ...
  c = Off; // C++'da bu geçerli değil!

  Color my_color = Black;
  int ival;
  // ...
  ival = my_color; // C++'da bu geçerli!
}
```

3) Enumerator'ların ayrı bir scope'ları yoktur.

```cpp 
// api.h dosyası
enum traffic_light {Red, Yellow, Green};

// app.h dosyası
enum screen_color {Magenta, White, Black, Red};

// Her iki başlık dosyası include edildiğinde, "Red" in ayrı bir scope'u olmadığı için bu kod derlendiğinde "Syntax hatası" olur.
```
