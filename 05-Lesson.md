# 5. Ders

## Function Overloading (Fonksiyon Aşırı Yükleme)

Farklı parametrelerle aynı isme sahip fonksiyonlar oluşturmanıza olanak tanır. Bu, fonksiyonlar arasında parametre türü veya sayısı gibi farklar olabilir.

  * **Derleme zamanında (compile-time)** çözülür. Yani, hangi fonksiyonun çağrılacağı, programın derleme aşamasında belirlenir.
  * **Fonksiyonların adı aynıdır**, ancak parametre sayısı veya türleri farklıdır.
  * Derleyici, fonksiyonun çağrıldığı yere göre uygun fonksiyonu seçer.

<!-- end list -->

```cpp
void print(int i)
{
    std::cout << "Integer: " << i << '\n';
}

void print(double d)
{
    std::cout << "Double: " << d << '\n';
}

void print(std::string s)
{
    std::cout << "String: " << s << '\n';
}

int main()
{
    print(10);      // Integer: 10
    print(3.14);    // Double: 3.14
    print("Hello"); // String: Hello
}
```

  * `print(int)`, `print(double)` ve `print(string)` fonksiyonları aynı isme sahip olmasına rağmen, parametre türlerine göre yüklenmiştir.
  * Derleyici, **çağrı sırasında** hangi fonksiyonun çağrılacağını parametre türüne göre **otomatik olarak** belirler.

### Deciding on Function Overloading (Fonksiyon Aşırı Yüklemesine Karar Verme)

1)  **Fonksiyon isimlerinin aynı olması:** Eğer **birden fazla fonksiyon aynı isimle tanımlanmışsa**, bunlar function overloading olabilir.

2)  **Farklı parametre sayısı veya türü:** Fonksiyon aşırı yükleme, genellikle parametre sayısının veya parametre türlerinin farklı olduğu durumlarda görülür. Eğer aynı isimle birden fazla fonksiyon varsa ve her bir fonksiyonun **parametre sayısı** veya **parametre türleri** farklıysa, bu **function overloading** anlamına gelir.

<!-- end list -->

```cpp
int add(int a, int b);          // Geçerli bir aşırı yükleme adayı
int add(double a, double b);    // Parametre türleri farklı, geçerli aşırı yükleme
int add(int a, int b, int c);   // Parametre sayısı farklı, geçerli aşırı yükleme
```

3)  **Geri dönüş türü(Return type) farklılığı**: Function overloading **geri dönüş türüyle yapılmaz**. Yani, sadece geri dönüş türü farklı olan **fonksiyonlar overloading sayılmaz**. Fonksiyonların **parametre sayısı** ya da **türlerinin farklı** olması gerekir.

<!-- end list -->

```cpp
int add(int a, int b);       // int türünde dönen bir fonksiyon
double add(int a, int b);    // int, int parametreleriyle tanımlanmış başka bir fonksiyon, ancak geri dönüş türü farklı. Bu durumda derleyici, hangi 'add(int, int)' fonksiyonunu çağıracağını belirleyemez ve belirsizlik (ambiguity) hatası verir. Bu bir aşırı yükleme değildir.
```

### Understanding and Distinguishing the Differences Between Function Overloading and Function Redeclaration (Fonksiyon Aşırı Yükleme ve Yeniden Bildirim Arasındaki Farkları Anlama ve Ayırt Etme)

  * **Function overloading parametrelerin** farklı olduğu, aynı isimle tanımlanmış birden fazla fonksiyondur.

  * **Function redeclaration** aynı isimdeki bir fonksiyonun parametreleriyle birlikte tekrardan bildirilmesidir.

**NOT-1:** **Fonksiyon Redeclaration (Fonksiyon yeniden bildirimi)**, bir fonksiyonun zaten bildirildiği bir yerin üzerine tekrar aynı fonksiyonun bildirilmesidir. Ancak, bu **aynı fonksiyon değişmeden** tekrarlanır.

```cpp
int add(int a, int b)       // İlk tanım
{
    return a + b;
}

int add(int a, int b);    // Fonksiyon yeniden bildirilmiş, ancak tanım yapılmamış. Bu geçerli bir yeniden bildirimdir.

int main()
{
    std::cout << add(2, 3) << '\n'; // Çıktı: 5
}
```

Burada, `add` fonksiyonu ilk önce tanımlanmış, sonra aynı parametrelerle yeniden bildirilmiştir. Bu **function redeclaration** örneğidir.

| **Özellik** | **Function Overloading** | **Function Redeclaration** |
| -------------------- | -------------------------------------------------------------------- | --------------------------------------------------------------------- |
| **İsim** | Aynı fonksiyon ismi kullanılır                             | Aynı fonksiyon ismi kullanılır                              |
| **Parametreler** | Farklı parametre sayısı veya türleri olmalıdır             | Aynı parametreler (sayısı ve türleri değişmez)              |
| **Dönüş Türü** | Dönüş türü değişebilir, ancak yalnızca bu ile aşırı yükleme yapılmaz | Aynı dönüş türü olmalıdır (başka bir yerden tekrar bildirilmiştir)    |
| **Amaç** | Aynı işlevi farklı parametrelerle yapmak                   | Fonksiyonu başka bir yerde bildirmek ve başlık dosyasına yerleştirmek |
| **Derleyici Kararı** | **Derleme zamanında** parametre türüne göre uygun fonksiyon seçilir  | **Tekrar tanımlama**, derleyici zaten tanımlı fonksiyonu kabul eder   |

### Function Overloading Resolution Process (Fonksiyon Aşırı Yükleme Çözümleme Süreci)

C++ derleyicisi, fonksiyonun doğru versiyonunu seçerken şu adımları takip eder:

1)  **Parametre Türlerini Karşılaştırma**:

      * Derleyici, çağrılan fonksiyon ile mevcut aşırı yüklenmiş fonksiyonları karşılaştırır.
      * Eğer parametre sayısı aynıysa, fonksiyonların parametre türleriyle karşılaştırma yapılır.

2)  **Tam Eşleşme (Exact Match)**:

      * Derleyici, parametrelerin türlerini tam olarak eşleşecek şekilde arar. En güçlü eşleşme bu türdür. Örneğin, `int` türündeki bir parametreye `int` türündeki bir argüman verildiğinde, bu tam eşleşme olur.

    **Örnek:** Fonksiyon aşırı yükleme çözümleme:

    ```cpp
    void print(int x) { std::cout << "Integer: " << x << '\n'; }
    void print(double x) { std::cout << "Double: " << x << '\n'; }
    void print(std::string x) { std::cout << "String: " << x << '\n'; }

    int main()
    {
        print(10);         // Tam eşleşme: print(10) çağrısı, print(int) fonksiyonuna tam eşleşir çünkü 10 bir int literalidir.
        print(3.14);       // Tam eşleşme: print(3.14) çağrısı, print(double) fonksiyonuna tam eşleşir çünkü 3.14 bir double literalidir.
        print("Hello");    // Tam eşleşme: print("Hello") çağrısı, print(std::string) fonksiyonuna tam eşleşir çünkü "Hello" bir C-stil string literalidir ve std::string'e örtülü olarak dönüştürülebilir.
    }
    ```

      * Derleyici **tam eşleşme** için aşağıdaki işlemleri yapabilir.

        1)  **L Value to R Value Conversion (L Değeri'nden R Değeri'ne dönüşüm)**

            ```cpp
            void print(int x) { std::cout << "Integer: " << x << '\n'; }

            int main()
            {
                int a = 5;
                print(a);  // 'a' bir l-value'dur, 'print' fonksiyonu için tam eşleşme yapılır ve 'a'nın değeri parametreye kopyalanır.
                print(10); // '10' bir r-value'dur, tam eşleşme yapılır.
            }
            ```

        2)  **`const` Conversion (`const` dönüşümü):** `const` ile işaretlenmiş bir nesnenin, `const` olmayan bir parametre ile eşleşmesini sağlar.

            ```cpp
            void print(const int x) { std::cout << "Const Integer: " << x << '\n'; }
            void print_non_const(int x) { std::cout << "Non-Const Integer: " << x << '\n'; } // Sadece göstermek amaçlı

            int main()
            {
                const int a = 10;
                print(a);  // Tam eşleşme: 'const int' türü, 'const int' parametresiyle.
                // print_non_const(a); // Hata: 'const int' değerini 'non-const int' parametresine dönüştürmeye çalışıyor.
            }
            ```

        3)  **Array Decay (Array to Pointer Conversion):** Dizi Çürütme (Diziye Göstergelerle Dönüşüm): Bu işlem **dizi çürütme** olarak adlandırılır, yani dizinin ilk elemanına işaret eden bir pointer'a dönüşüm yapılır.

            ```cpp
            void print(int* arr) { std::cout << "Array address: " << arr << '\n'; }

            int main()
            {
                int arr[5] = {1, 2, 3, 4, 5};
                print(arr);  // 'arr' bir dizi olmasına rağmen, 'int*' bekleyen print fonksiyonuna geçirildiğinde bir 'int*'e dönüşür (çürür).
            }
            ```

        4)  **Function to Pointer Conversion (Fonksiyonun Göstergesine Dönüşüm):**

            ```cpp
            void print(int x) { std::cout << "Integer: " << x << '\n'; }

            int main()
            {
                void (*funcPtr)(int) = print;  // 'print' fonksiyonu, 'void(*)(int)' türünde bir fonksiyon işaretçisine otomatik olarak dönüştürülür.
                funcPtr(10);  // Fonksiyon işaretçisi ile çağrı. Çıktı: Integer: 10
            }
            ```

3)  **İleri Dönüşüm (Promotion) ve Dönüşüm (Conversion)**:

      * Eğer tam eşleşme yoksa, derleyici **dönüşüm (conversion)** ve **ileri dönüşüm (promotion)** yollarını kullanarak en yakın eşleşmeyi bulmaya çalışır.
      * Örneğin, `int` türündeki bir argüman, `float` türündeki bir parametreye dönüştürülebilir.

    <!-- end list -->

    1)  **İleri dönüşüm(Promotion):** Daha küçük türlerin, daha büyük türlere otomatik olarak dönüştürülmesidir. Veri kaybı olmaz. İleri Dönüşüm (Promotion) Örnekleri:

          * `char` → `int` dönüşümü
          * `int` → `float` dönüşümü
          * `float` → `double` dönüşümü

        <!-- end list -->

        ```cpp
        void print(double x) { std::cout << "Double: " << x << '\n'; }

        int main()
        {
            print(5);       // '5' bir 'int' türüdür. print(double) fonksiyonu çağrıldığında, 'int' türü 'double' türüne ileri dönüşüm ile dönüştürülür. Çıktı: Double: 5
            print(3.14f);   // '3.14f' bir 'float' türüdür. print(double) fonksiyonu çağrıldığında 'float' türü 'double'a ileri dönüşüm ile dönüştürülür. Çıktı: Double: 3.14
        }
        ```

    2)  **Dönüşüm (Conversion):** Farklı türler arasındaki uyumu sağlamak için yapılır ve bazen veri kaybına yol açabilir.

          * `int` → `double`
          * `float` → `int` (bu dönüşümda veri kaybı olabilir)
          * `char` → `int` (ASCII değerine dönüşüm)

        <!-- end list -->

        ```cpp
        void print(double x) { std::cout << "Double: " << x << '\n'; }

        int main()
        {
            print(10.5);    // '10.5' zaten bir 'double' türündedir, bu yüzden doğrudan print(double) fonksiyonu çağrılır (tam eşleşme). Çıktı: Double: 10.5
            print(4);       // '4' bir 'int' türüdür, ancak print(double) fonksiyonu çağrıldığında 'int' türü 'double' türüne dönüştürülür (conversion). Çıktı: Double: 4
        }
        ```

    | **Özellik** | **İleri Dönüşüm (Promotion)** | **Dönüşüm (Conversion)** |
    | ------------------- | --------------------------------------------------------------- | ----------------------------------------------------- |
    | **Amaç** | Küçük türleri daha büyük türlere dönüştürmek          | İki tür arasındaki uyumu sağlamak için dönüşüm yapmak |
    | **Veri Kaybı** | Veri kaybı olmaz (daha küçük tür, daha büyük türe dönüştürülür) | Veri kaybı olabilir (örneğin `float` → `int`) |
    | **Örnek** | `char` → `int`, `int` → `float`, `float` → `double`   | `int` → `double`, `float` → `int`         |
    | **Kullanım Durumu** | Genellikle temel türlerde (char, int, float) olur     | Farklı türler arasında uyum sağlamak için kullanılır |

4)  **En İyi Uyum (Best Match / Viable Function)**:

      * Eğer birkaç fonksiyon, parametrelerin türlerine uygun dönüşümle eşleşiyorsa, derleyici **en iyi eşleşmeyi** seçer. Bu, en az dönüşüm yapılması gereken fonksiyon olur.

5)  **Ambiguity (Çakışma)**:

      * Eğer derleyici, hangi fonksiyonun çağrılacağı konusunda emin olamazsa, **çakışma** durumu meydana gelir ve derleyici hata verir.

    **Örnek:** Çakışma durumu:

    ```cpp
    void print(int x) { std::cout << "Integer: " << x << '\n'; }
    void print(double x) { std::cout << "Double: " << x << '\n'; }

    int main()
    {
        print(10);      // Tam eşleşme: print(int)
        print(3.14);    // Tam eşleşme: print(double)
        print('a');     // Belirsizlik! 'a' bir char literalidir. C++'da 'char', 'int'e ileri dönüşümle (promotion) veya 'double'a dönüşümle (conversion) dönüştürülebilir. Her iki dönüşüm de geçerli olduğu için derleyici hangi fonksiyonu seçeceğine karar veremez ve "ambiguous call to overloaded function" hatası verir.
    }
    ```

-----

## Examples (Örnekler)

**Örnek-1:** Mutable ve Immutable için yapılan implamantasyon compile-time'da geçrekleşiyor.

```cpp
class my_class {};

void foo(my_class& obj) { std::cout << "my_class &\n"; }           // non-const l-value referans alan fonksiyon
void foo(const my_class& obj) { std::cout << "const my_class &\n"; } // const l-value referans alan fonksiyon

int main()
{
    my_class m;
    foo(m);     // Çıktı: my_class &. 'm' non-const bir l-value olduğu için foo(my_class&) fonksiyonu çağrılır.

    const my_class m_c;
    foo(m_c);   // Çıktı: const my_class &. 'm_c' const bir l-value olduğu için foo(const my_class&) fonksiyonu çağrılır.
}
```

**Örnek-2:** Varsayılan Argümanlar ve Function Overloading Arasındaki Çakışma

```cpp
void func(int, int = 10); // Fonksiyon bildirimi: Bir int ve opsiyonel ikinci bir int alır.
void func(int);           // Fonksiyon bildirimi: Bir int alır.

// Tanımlar
void func(int x, int y) { std::cout << "func(int, int): " << x << ", " << y << '\n'; }
void func(int x) { std::cout << "func(int): " << x << '\n'; }

int main()
{
    func(3);    // Hata: "Ambiguity hatası olacaktır." Çünkü 'func(3)' çağrısı hem 'func(int)' ile tam eşleşir hem de 'func(int, int = 10)' fonksiyonunun ilk parametresiyle tam eşleşir ve varsayılan argüman kullanılarak ikinci parametre sağlanabilir. Derleyici hangi fonksiyonu seçeceğine karar veremez.
    // Bu hatayı düzeltmek için:
    // func(3, 10); // Eğer func(int, int) çağrılmak isteniyorsa
    // Veya sadece func(int) fonksiyonunu kullanmak istiyorsanız, diğer aşırı yüklemeyi kaldırın veya varsayılan argümanlı fonksiyonu çağırırken ikinci argümanı belirtin.
}
```

**Örnek-3:** Referans Türleri ve Function Overloading Çözünürlüğü

```cpp
void func(int& ref)    { std::cout << "L value referans\n"; } // L-value referansı alan fonksiyon (değiştirilebilir)
void func(int val)     { std::cout << "Call-by-value\n"; }    // Değerle çağırma

void foo(const int& ref) { std::cout << "const L value referans\n"; } // const L-value referansı alan fonksiyon (hem L-value hem de R-value'ya bağlanabilir)
void foo(int val)        { std::cout << "Call-by-value (foo)\n"; }   // Değerle çağırma (foo)

int main()
{
    int x = 67;
    // func(x);        // Hata: "Ambiguity hatası olacaktır." 'x' bir L-value'dur. Hem 'func(int&)' hem de 'func(int)' için geçerli adaydır. 'func(int&)' tam eşleşme iken, 'func(int)' için l-value'dan r-value'ya dönüşüm gerekir. Genellikle l-value referans tam eşleşme olarak kabul edilir ancak bazen derleyiciye göre bu tür durumlarda belirsizlik oluşabilir. Özellikle `int&` ve `int` aşırı yüklemelerinde, l-value argümanlar için `int&` tercih edilirken, r-value argümanlar için `int` tercih edilir. Bu örnekte `int&` daha spesifik olduğu için genellikle o tercih edilir. Eğer hata veriyorsa, derleyici bunu belirsiz buluyordur.

    func(23);       // Çıktı: Call-by-value. '23' bir r-value'dur (geçici değer). 'func(int&)' bir l-value referansı beklediği için '23' ona bağlanamaz. Bu nedenle 'func(int)' fonksiyonu çağrılır.

    // --------------------------------------------------

    foo(12);        // Hata: "Ambiguity hatası olacaktır." '12' bir r-value literalidir. 'foo(int)' fonksiyonu değerle çağrılarak '12'yi kabul edebilir. 'foo(const int&)' fonksiyonu da 'const' referans olduğu için bir r-value'ya bağlanabilir. Her iki fonksiyon da r-value için geçerli aday olduğundan, derleyici hangisini seçeceğine karar veremez ve belirsizlik hatası verir.
}
```

**Örnek-4:** Null Pointer Literali (`0`) ve `nullptr` ile Aşırı Yükleme

```cpp
void func(int *ptr) { std::cout << "func(int *)\n"; }
void func(int val)  { std::cout << "func(int)\n"; }

int main()
{
    func(0);        // Çıktı: func(int). C++11 öncesinde 0 genellikle bir null pointer sabiti olarak kabul edilirdi. Ancak C++'da '0' aynı zamanda bir integer literalidir. Bu iki aşırı yüklenmiş fonksiyon arasında, 'int' parametresi alan 'func(int)' ile tam eşleşme sağlanırken, 'func(int*)' için 'int'ten 'int*'e bir dönüşüm gerekmektedir. Tam eşleşme daha iyi bir seçenek olduğu için 'func(int)' çağrılır.
    func(nullptr);  // Çıktı: func(int *). C++11 ile tanıtılan 'nullptr', özel olarak bir null pointer sabiti türü olan 'std::nullptr_t'dir. Bu nedenle 'func(int*)' fonksiyonu ile tam eşleşme sağlar.
}
```

**Örnek-5:** Pointer Türleri Arasında `nullptr` Belirsizliği

```cpp
void func(int *ptr)    { std::cout << "func(int *)\n"; }
void func(double *ptr) { std::cout << "func(double *)\n"; }

int main()
{
    func(nullptr);  // Hata: "Ambiguity hatası olacaktır." 'nullptr' hem 'int*' hem de 'double*' türüne dönüştürülebilir (örtülü dönüşüm). Bu durumda, derleyici hangi pointer türünün daha uygun olduğuna karar veremez ve belirsizlik hatası verir.
}
```

**Örnek-6:** `std::nullptr_t` ile Belirsizliğin Giderilmesi

```cpp
#include <cstddef> // std::nullptr_t için gerekli

void func(int *ptr)         { std::cout << "func(int *)\n"; }
void func(double *ptr)      { std::cout << "func(double *)\n"; }
void func(std::nullptr_t p) { std::cout << "func(std::nullptr_t)\n"; } // nullptr_t için özel aşırı yükleme

int main()
{
    func(nullptr);  // Çıktı: func(std::nullptr_t). Artık 'nullptr' için özel bir tam eşleşme (func(std::nullptr_t)) olduğu için, diğer genel pointer dönüşümleri yerine bu fonksiyon çağrılır. Belirsizlik ortadan kalkar.
}
```

**Örnek-7:** L-value ve R-value Referansları ile Function Overloading

```cpp
void func(int& ref)         { std::cout << "L value referans\n"; }         // Sadece değiştirilebilir (non-const) L-value'lara bağlanır.
void func(int&& rref)       { std::cout << "R value referans\n"; }         // Sadece R-value'lara bağlanır.
void func(const int& cref)  { std::cout << "const L value referans\n"; }   // Hem const L-value'lara, hem non-const L-value'lara, hem de R-value'lara bağlanabilir (ancak daha spesifik bir eşleşme yoksa).
// void func(const int&& crref) { std::cout << "const R value referans\n"; } // Bu fonksiyon pratikte neredeyse hiç çağrılmaz çünkü const int& aşırı yüklemesi daha geneldir ve aynı durumda tercih edilir. Nadiren kullanılır.

int main()
{
    int x = 10;
    func(x);         // Çıktı: L value referans. 'x' bir non-const l-value olduğu için func(int&) fonksiyonu en iyi ve tam eşleşme sağlar. Eğer func(int&) fonksiyonu mevcut olmasaydı, func(const int&) çağrılırdı.

    func(56);        // Çıktı: R value referans. '56' bir r-value olduğu için func(int&&) fonksiyonu tam eşleşme sağlar. Eğer func(int&&) fonksiyonu mevcut olmasaydı, func(const int&) çağrılırdı (çünkü const referanslar r-value'lara bağlanabilir).

    int y{ 657 };
    func(y);         // Çıktı: L value referans. 'y' bir non-const l-value olduğu için func(int&) fonksiyonu en iyi ve tam eşleşme sağlar. Eğer func(int&) fonksiyonu mevcut olmasaydı, func(const int&) çağrılırdı.

    const int z{25};
    func(z);         // Çıktı: const L value referans. 'z' bir const l-value olduğu için sadece func(const int&) fonksiyonu ona bağlanabilir. func(int&) veya func(int&&) bağlanamaz.
}
```

**Örnek-8:** İstisnai Bir Durum - Pointer ve Bool Aşırı Yüklemesi

```cpp
void func(bool b)    { std::cout << "bool\n"; }
void func(void *ptr) { std::cout << "void *\n"; }

int main()
{
    int x{ 677 };
    func(&x); // Çıktı: void *. '&x' bir 'int*' türündedir. Bu tür hem 'bool'a (nullptr dışındaki tüm pointerlar true'ya dönüşür) hem de 'void*'a dönüştürülebilir. Ancak C++ standartlarına göre, pointer türünden 'void*'a dönüşüm, pointer türünden 'bool'a dönüşüme göre daha iyi bir eşleşme olarak kabul edilir. Bu nedenle 'func(void*)' çağrılır.

    // İstisna bir durum olmasaydı "Ambiguity" hata olacaktı. Ancak burada void* aşırı yüklemesi tercih edilir.
}
```

**NOT:** `func(bool)` ve `func(void *)` fonksiyonları **Viable** durumundadır. `func(&x)` ile fonkisyon çağırıldığında argümanın türü `int *` olur. `int *` türünden `bool` dönüşümüde vardır, `void *` dönüşümüde vardır. Bu şekilde tanımlanmış olan **fonksiyon overload** işlemlerinde `void *` türünün `bool` türüne üstünlüğü vardır.

**Örnek-9:** Birden Fazla Argümanlı Aşırı Yükleme Çözünürlüğü

```cpp
void func(int a, double b, long c)   { std::cout << "int, double, long\n"; }
void func(bool a, float b, int c)    { std::cout << "bool, float, int\n"; }
void func(long a, double b, float c) { std::cout << "long, double, float\n"; }

int main()
{
    func(12, 12, 45u); // Çıktı: int, double, long
                       // Argümanlar: 12 (int), 12 (int), 45u (unsigned int)
                       // func(int, double, long) için: 12 (int -> int - Exact), 12 (int -> double - Promotion), 45u (unsigned int -> long - Conversion)
                       // func(bool, float, int) için: 12 (int -> bool - Conversion), 12 (int -> float - Promotion), 45u (unsigned int -> int - Conversion)
                       // func(long, double, float) için: 12 (int -> long - Promotion), 12 (int -> double - Promotion), 45u (unsigned int -> float - Conversion)
                       // Bu durumda, 'func(int, double, long)' birinci argüman için tam eşleşme, ikinci argüman için promotion ve üçüncü argüman için conversion sunar. Diğerlerine kıyasla daha iyi veya eşit eşleşmeler sağladığı için bu seçilir.

    func(57u, 4.5L, true); // Çıktı: bool, float, int
                           // Argümanlar: 57u (unsigned int), 4.5L (long double), true (bool)
                           // func(int, double, long) için: 57u (unsigned int -> int - Conversion), 4.5L (long double -> double - Conversion), true (bool -> long - Conversion)
                           // func(bool, float, int) için: 57u (unsigned int -> bool - Conversion), 4.5L (long double -> float - Conversion), true (bool -> int - Promotion)
                           // func(long, double, float) için: 57u (unsigned int -> long - Conversion), 4.5L (long double -> double - Conversion), true (bool -> float - Conversion)
                           // Burada 'func(bool, float, int)' daha iyi bir genel eşleşme sağlar. Özellikle 'true' argümanının 'int'e dönüşümü bir promotion'dır.
}
```

**NOT:** En az bir argüman için o parametrede diğerlerine üstünlük kuracak, diğer arümanlarda da daha kötü olmayacak.

-----

## Additional Information (Ek Bilgi)

1)  **Pointer** türünden, `bool` türüne örtülü dönüşüm vardır. `nullptr` (C++11 ve sonrası) veya `NULL` (C-stil) `false` olarak kabul edilirken, diğer tüm geçerli işaretçi değerleri `true` olarak kabul edilir. Bu, bir pointer'ın geçerli olup olmadığını kontrol etmek için sıklıkla kullanılır.

2)  Bir **fonksiyonun imzası (signature)**, o fonksiyonun **ismi** ve **parametre listesinden** (parametrelerin türleri ve sırası) oluşur. Geri dönüş türü ve parametre isimleri fonksiyon imzasının bir parçası değildir ve aşırı yükleme kararını etkilemez.