# 9.Ders

## Class

Bir tür veri yapısıdır ve iki ana bileşenden oluşur:
- **Veri üyeleri (data members)**: Bir sınıfın içindeki değişkenlerdir.
- **Fonksiyon üyeleri (member functions)**: Bu değişkenler üzerinde işlem yapan fonksiyonlardır.

### Class Definition

Bir sınıf, ```class``` anahtar kelimesi ile tanımlanır.

```cpp
class my_class
{
    // Bu alana  "Class scope" denir. Bu bildirilen varlıklara bu "Class"ın öğeleri(elemanları) denir.

    class class_obj     // Type member
    {

    };

    void class_func();  // member function

    int class_var;      // Data member
}
```

**Bir sınıf aşağıdaki özelliklere sahiptir:**

1) **Veri Üyeleri (Data Members)**:
    - Bir sınıf, değişkenlerin bulunduğu bir yapı olabilir. Bu değişkenler veri üyeleri olarak bilinir.
    - Veri üyeleri private (özel) veya public (genel) olabilir.

2) **Fonksiyon Üyeleri (Member Functions)**:
    - Sınıf içindeki fonksiyonlar, sınıfın veri üyeleri üzerinde işlem yapmak için kullanılır.
    - Bu fonksiyonlar da ```private``` veya ```public``` olabilir.

3) **Nesne (Object)**:
    - Sınıf, bir şablon sağlar; ancak bir sınıfın **nesneleri**, sınıfın örnekleridir.
    - **Nesneler**, sınıfın veri ve fonksiyon üyelerini kullanabilir.

4) **Access Control (Erişim Kontrolü)**:
    - ```public```: Sınıf dışından erişilebilir.
    - ```private```: Yalnızca sınıf içinden erişilebilir.
    - ```protected```: Bu erişim düzeyi, türetilmiş sınıfların erişmesine izin verir.

```cpp
struct data_t 
{ 
    int a, b, c;
};

int main()
{
    data_t _my_data;
    _my_data.a = 10;        // Bu şekilde erişim sağlayacaktık.

    data_t _my_data_p;    
    data* p = &_my_data_p;  // Bu şekilde tanımlama yaparsak 2 şekilde erişim sağlayabiliriz.

    (*p).a = 10;            // 1. erişim yoludur. Bir ifade de bir ismi '.' operatörünün sağında(qualified name) kullanmışsak, derleyici önce '.' operatörünün sol operandı olan ifadenin hangi sınıf türünden olduğunu anlayacak(sınıf türünden değilse "Syntax" hatası oluşacak). Sağ operandı olan ismi o sınıfın scope'u içerisinde arayacak(Scope içerisinde bulamazsa yine "Syntax" hatası oluşacak).
    p->b = 12;              // 2. erişim yoludur. '.' operatörü ile aynı yolu izler fakat '->' operatörünün sol operandı muhakkak "sınıf türünden pointer" olmalıdır!
}
```

```cpp
struct _my_str_t 
{
    int a, b, x;
}

void func(_my_str_t * p) { }    // C'de bu şekilde erişim sağlıyorduk.

class _my_class_t
{
    public:    
        void func( );
}   //            ^~~~~ "_my_class_t *" sınıfını burada her zaman gizli bir parametre olarak tutar.

int main()
{
    struct _my_str_t real_my_str_t;
    func(&real_my_str_t);

    _my_class_t real_my_class_t;
    real_my_class_t.func();
}
```

### Access Control(Erişim Kontrolü)

1) #### **```public``` (Genel):**

    - ```public``` üyeler sınıf dışından erişilebilir.
    - Bu üyeler, sınıf dışındaki herhangi bir fonksiyon veya nesne tarafından kullanılabilir.

    **Örnek:**

    ```cpp
    class MyClass 
    {
        public:
            int x;  // public veri üyesi

            void setX(int value)    // public fonksiyon
            {  
                x = value;
            }

            int getX() // public fonksiyon
            {  
                return x;
            }
    };

    int main() 
    {
        MyClass obj;  
        obj.x = 10;  // public veri üyesine dışarıdan erişim
        std::cout << "X değeri: " << obj.getX() << '\n';  // public fonksiyon kullanımı
    }
    ```
    - Burada, ```x``` ```public``` olarak tanımlandığı için ```main``` fonksiyonunda dışarıdan erişilebilir.
    - ```setX``` ve ```getX``` fonksiyonları da ```public``` olduğu için dışarıdan çağrılabilir.

    ---

2) #### **```private``` (Özel):**

    - ```private``` üyeler sadece sınıfın içinde bulunan fonksiyonlar tarafından erişilebilir.
    - ```private``` veriler, sınıf dışından erişilemez ve yalnızca sınıf içindeki fonksiyonlarla işlenebilir.

    **Örnek:**

    ```cpp
    class MyClass 
    {
        private:
            int x;  // private veri üyesi

        public:
            void setX(int value) // public fonksiyon
            {  
                x = value;
            }

            int getX() // public fonksiyon
            {  
                return x;
            }
    };

    int main() 
    {
        MyClass obj;
        // obj.x = 10;  // HATA! private veri üyelerine dışarıdan erişilemez
        obj.setX(10);  // public fonksiyon ile erişim sağlanır
        std::cout << "X değeri: " << obj.getX() << '\n';  // private veri üyesine erişim, public fonksiyon ile yapılır
    }
    ```
    - ```x``` değişkeni ```private``` olarak tanımlanmıştır, bu nedenle ```main``` fonksiyonunda doğrudan erişilemez.

    - Ancak ```setX``` ve ```getX``` gibi ```public``` fonksiyonlar kullanılarak erişim sağlanabilir.

    ---

3) #### **```protected``` (Korunmuş):**
    - ```protected``` üyeler, sınıf dışından erişilemez, ancak bu üyeler türeyen sınıflar tarafından erişilebilir. Bu, kalıtım (inheritance) ile türetilen sınıflara erişim izni verir.

    ```cpp
    #include <iostream>
    using namespace std;

    class Base 
    {
        protected:
            int x;  // protected veri üyesi

        public:
            void setX(int value) // public fonksiyon
            {  
                x = value;
            }

            int getX() // public fonksiyon
            {  
                return x;
            }
    };

    class Derived : public Base 
    {
        public:
            void display() 
            {
                std::cout << "X değeri (türetilmiş sınıf): " << x << '\n';  // protected üyeye erişim
            }
    };

    int main() 
    {
        Derived obj;
        obj.setX(10);  // Base sınıfının public fonksiyonu ile erişim
        obj.display();  // Derived sınıfının fonksiyonu ile protected üyeye erişim
    }
    ```
    - Burada, ```x``` ```protected``` olarak tanımlandığı için ```Base``` sınıfının dışında doğrudan erişilemez, ancak ```Derived``` sınıfından erişilebilir.

    - ```setX``` fonksiyonu ```Base``` sınıfından çağrılabilir, bu da ```protected``` veriye dolaylı erişim sağlar.

    ---

| **Erişim Belirleyicisi** | **Erişim Durumu**                                     |
| ------------------------ | ----------------------------------------------------- |
| **`public`**             | Sınıf içinden ve dışından erişilebilir.               |
| **`private`**            | Yalnızca sınıf içinden erişilebilir.                  |
| **`protected`**          | Sınıf içinden ve türetilmiş sınıflardan erişilebilir. |

### Member Function

Bir sınıfın içine dahil edilmiş fonksiyonlardır ve bu fonksiyonlar sınıfın nesneleri üzerinde işlem yapar.

```cpp
// 
// api.h dosyası
class _my_class()
{
    public:
        void func();    // Sınıfın içinde tanımladığımız için "inline" anahtar kelimesi ile fonksiyonu göstermemize gerek yoktur. "Implicite inline" olarak orada bulunur.
}

inline void _my_class::func() // Sınıfın dışında tanımlama yapmadığımız için "inline" kullanmak zorundayız.
```

1) **Temel Bildirim ve Tanım:**

    ```cpp
    class Araba 
    {
        public: // Erişim belirleyici
            // Üye fonksiyon bildirimi (declaration)
            void hizlan(int artis);

            // Inline tanım (doğrudan sınıf içinde)
            void frenYap() 
            {
                hiz -= 10;
                if (hiz < 0) hiz = 0; // Hız negatif olamaz
            }

        private:
            int hiz = 0; // Üye değişken
    };

    // Sınıf dışında tanım (definition) → "Araba::" scope'u zorunlu!
    void Araba::hizlan(int artis) {
        hiz += artis; // Üye değişkene doğrudan erişim
    }
    ```
    ---

2) **Erişim Kuralları(```public``` vs ```private```):**  

    Üye fonksiyonların dışarıdan çağrılabilmesi için public olmalıdır.

    ```cpp
    int main() 
    {
        Araba tesla;
        tesla.hizlan(50); // public → Geçerli
        tesla.frenYap();   // public → Geçerli

        // tesla.hiz = 100; // HATA! private üyeye dışarıdan erişilemez
        // Üye değişkenlere sadece sınıfın KENDİ fonksiyonları erişebilir.
    }
    ```
    ---

3) **```const``` Üye Fonksiyonlar:**

    Nesnenin durumunu değiştirmeyen fonksiyonlar ```const``` ile işaretlenir.

    ```cpp
    class Araba 
    {
        public:
            // 3.1 const üye fonksiyon → içeride üye değişken değiştirilemez
            int mevcutHiz() const {
                // hiz = 0; // HATA! const fonksiyon içinde üye değiştirilemez
                return hiz;
            }

        private:
            int hiz;
    };

    int main() 
    {
        const Araba sabitAraba; // const nesne
        // sabitAraba.hizlan(10); // HATA! const nesne sadece const fonksiyon çağırabilir
        sabitAraba.mevcutHiz(); // Geçerli (const fonksiyon)
    }
    ```
    ---

4) **```static``` Üye Fonksiyonlar:**

    Nesneye değil, sınıfa ait fonksiyonlar. İçeride this yoktur.

    ```cpp
    class Araba 
    {
        public:
            static int toplamArabaSayisi() 
            {
                return sayac; // Sadece static üyelere erişebilir
            }

            Araba() { sayac++; } // Kurucu (constructor)

        private:
            static int sayac; // Sınıf genelinde paylaşılan değişken
    };

    int Araba::sayac = 0; // Static değişkenin tanımı (zorunlu)

    int main() 
    {
        Araba a1, a2;
        // Nesne olmadan çağırma
        std::cout << Araba::toplamArabaSayisi(); // Çıktı: 2
    }
    ```
    ---

5) **Tanım Yerleri: Inline vs Dışarıda**

    - **Inline**: Sınıf içinde tanım → Derleyici optimizasyonu.
    - **Dışarıda**: Büyük fonksiyonlar için → ```SınıfAdı::fonksiyon``` syntax'ı zorunlu.

    ```cpp
    class Araba 
    {
        public:
            void korna_cal(); // Sadece bildirim
    };

    // Dışarıda tanım → "Araba::" unutulursa LINK HATASI!
    void Araba::korna_cal() {
        std::cout << "Beep!";
    }
    ```
---

**Özet Tablo: Kritik Kurallar**
| Özellik             | Syntax Kuralı                         | Sık Hata                     |
|---------------------|---------------------------------------|------------------------------|
| **Sınıf Dışı Tanım**| `void Sınıf::fonksiyon() { ... }`     | `Sınıf::` unutma → Tanımsızlık hatası! |
| **`const` Fonksiyon**| `int get() const { ... }`            | `const` nesneler için zorunlu |
| **`static` Fonksiyon**| `static void foo();` → `this` yok!  | Static üye değişken gerektirir |
| **Erişim**          | `private` fonksiyon → Sadece sınıf içi | Dışarıdan çağırma hatası    |

> ✅ **En İyi Uygulama:**  
> - Nesne durumu değişmiyorsa **`const`** ekle.  
> - Sınıf geneli işlemler için **`static`** kullan.  
> - Büyük fonksiyonları **sınıf dışında tanımla** (kod okunabilirliği).

#### Member Function ile Function Overloading:**

```cpp
class Calculator 
{
    public:
        // İki tam sayıyı toplayan fonksiyon
        int add(int a, int b) { return a + b; }

        // İki ondalıklı sayıyı toplayan fonksiyon
        double add(double a, double b) { return a + b; }

        // Üç tam sayıyı toplayan fonksiyon
        int add(int a, int b, int c) { return a + b + c; }
        
        // Varsayılan argüman ile tanımlanmış beş sayıyı toplayan fonksiyon
        int add(int a, int b, int c, int d, int e = 10) { return a + b + c + d + e; }
};

int add(double a, int b) { return a + b; }      // Bu fonkisyon "function overloading" değildir. Farklı scope'taki fonkisyonlar birbirlerini overload etmezler!

int main() 
{
    Calculator calc;  // Calculator sınıfından bir nesne oluşturuluyor

    std::cout << "2 + 3 = " << calc.add(2, 3) << '\n';                          // int türündeki add fonksiyonu çağrılır
    std::cout << "2.5 + 3.5 = " << calc.add(2.5, 3.5) << '\n';                  // double türündeki add fonksiyonu çağrılır
    std::cout << "1 + 2 + 3 = " << calc.add(1, 2, 3) << '\n';                   // Üç parametreli add fonksiyonu çağrılır
    
    std::cout << "1 + 2 + 3 + 4 = " << calc.add(1, 2, 3, 4) << '\n';            // Beş parametreli add fonksiyonu çağrılır
    
    std::cout << "1 + 2 + 3 + 4 + 5 = " << calc.add(1, 2, 3, 4, 5) << '\n';     // Beş parametreli add fonksiyonu çağrılır
}
```

**NOT-1:** **Member functions(üye fonksiyonlar)** bulundukları scope içerisinde **redeclaration(yeniden tanımlama) yapılamazlar!** **Syntax hatasına** yol açar.


```cpp
class my_class
{
    private:
        int foo(double);    // Function overloading gerçekleşir
        int foo(int);       // Function overloading gerçekleşir fakat "public" kısmında redeclaration yapıldığı için "Syntax" hatası verir
    public:
        int foo(int);       // Function overloading gerçekleşir fakat "private" kısmında redeclaration yapıldığı için "Syntax" hatası verir 
}
```

```cpp
class my_class
{
    private:
        int foo(int);
    public:
        int foo(double);
}
// Önce look-up, sonra context kontrolü en sonra access kontrolü yapılır.
int main()
{
    my_class _class;

    _class.foo(12); // Derleyici bir fonksiyonu çağırırken önce look-up table, sonra "context" kontrolü daha sonra ise "access" kontorlü gerçekleştirdiği için. Bu Fonksiyon "syntax" hatası oluşturacak.

    //  Derleyici "private" kısmında tanımlanan fonksiyonu "exact match" olarak görecek ve bu fonksiyon seçilecektir fakat bu fonksiyon "access control" kısmından geçemeyeceği için "syntax" hatası bildirecektir.
}
```

---

### C++'da İsim Arama(Name Lookup) Mantığı

1) #### Kapsam(Scope):

    İsim aramanın en temel prensibi kapsamdır (scope). Bir ismin tanımlı olduğu bölgeye "kapsam" denir. C++'da çeşitli kapsam türleri bulunur:

        - **Yerel Kapsam (Local Scope)**: Bir fonksiyon veya kod bloğu (```{}```) içinde tanımlanan isimler sadece o blok içinde görünürdür.

        ```cpp
        void myFunction() 
        {
            int x = 10; // 'x' yerel kapsamda
            // ...
        } // 'x' burada kapsam dışına çıkar
        ```

        - **Sınıf Kapsamı (Class Scope)**: Bir sınıfın içinde tanımlanan üyeler (veri üyeleri, üye fonksiyonlar, iç içe sınıflar) sadece o sınıfın üyeleri veya o sınıfın nesneleri aracılığıyla erişilebilir.

        ```cpp
        class MyClass 
        {
            public:
                int value; // 'value' sınıf kapsamda
                void printValue() 
                {
                    // ...
                }
        };
        ```

        - **Namespace Kapsamı (Namespace Scope)**: Bir namespace içinde tanımlanan isimler sadece o ```namespace``` içinde veya ```using``` bildirimi ile erişilebilir.

        ```cpp
        namespace MyNamespace 
        {
            int globalVar; // 'globalVar' MyNamespace kapsamda
        }
        ```

        - **Global Kapsam (Global Scope)**: Herhangi bir fonksiyon, sınıf veya namespace dışında tanımlanan isimler programın her yerinden erişilebilir.

        ```cpp
        int programId; // 'programId' global kapsamda
        ```

    Derleyici, bir ismi ararken öncelikle o ismin kullanıldığı mevcut **iç kapsamlardan dış kapsamlara doğru** arama yapar.

2) #### Bağımlı Olmayan İsim Arama (Unqualified Name Lookup):

    Bir ismin önüne ```::``` (kapsam çözümleme operatörü) veya bir sınıf/namespace adı gibi bir niteleyici koymadan arandığı durumdur. Derleyici, bu durumda şu sırayı takip eder:

    - İsmin kullanıldığı **mevcut kapsam** (fonksiyon bloğu, sınıf üye fonksiyonu vb.).
    - Mevcut kapsamı çevreleyen **dış kapsamlar** (iç içe fonksiyon blokları, ana fonksiyon, sınıfın kendisi).
    - Dosya kapsamı (global veya namespace).

    İlk bulduğu geçerli tanımı kullanır. Eğer aynı isim birden fazla kapsamda tanımlıysa, **en içteki kapsamdaki tanım öncelik alır**.

    ```cpp
    int global_data = 10; // Global kapsam

    class MyClass {
    public:
        int class_data = 20; // Sınıf kapsamı

        void printData() {
            int local_data = 30; // Yerel kapsam

            std::cout << local_data << std::endl; // Çıktı: 30 (Yerel olan öncelikli)
            std::cout << class_data << std::endl; // Çıktı: 20 (Sınıf üyesi)
            std::cout << global_data << std::endl; // Çıktı: 10 (Global olan)
        }
    };

    int main() {
        MyClass obj;
        obj.printData();
        // std::cout << local_data << std::endl; // Hata! 'local_data' burada görünür değil.
    }
    ```

3) #### Bağımlı İsim Arama (Qualified Name Lookup)

    Bir ismin önüne ```::``` operatörü ile bir sınıf veya namespace adı (```NamespaceAdi::isim``` veya ```SinifAdi::isim```) gibi bir **niteleyici (qualifier)** koyularak arandığı durumdur. Bu durumda derleyici, aramayı sadece belirtilen sınıfın veya namespace'in içinde yapar.

    ```cpp
    namespace MyLibrary { int value = 100; }

    class MyOtherClass 
    {
        public:
            int value = 200;
            void printValues() 
            {
                std::cout << value << std::endl; // Kendi sınıfının 'value'su: 200
                std::cout << MyLibrary::value << std::endl; // MyLibrary'deki 'value': 100
            }
    };

    int main() 
    {
        MyOtherClass obj;
        obj.printValues();
    }
    ```

4) #### Bağımlı Argüman Arama (Argument-Dependent Lookup - ADL / Koenig Lookup)

    Bu özel bir isim arama türüdür ve özellikle operatör aşırı yüklemeleri ve fonksiyon şablonları için önemlidir. Bir fonksiyon çağrıldığında, eğer fonksiyon ismi niteleyici olmadan kullanılırsa (```func(arg)```), derleyici sadece mevcut kapsamda arama yapmakla kalmaz, aynı zamanda fonksiyonun argümanlarının tiplerinin tanımlandığı namespace'leri de arar.

    ```cpp
    namespace N 
    {
        struct S {};
        void f(S) { std::cout << "N::f(S) çağrıldı" << std::endl; }
    }

    void f(int) { std::cout << "global f(int) çağrıldı" << std::endl; }

    int main() 
    {
        N::S s_obj;
        f(s_obj); // Çıktı: N::f(S) çağrıldı. ADL sayesinde 'N::f' bulundu.
        f(5);     // Çıktı: global f(int) çağrıldı.
    }
    ```

    Burada ```f(s_obj)``` çağrısında, ```f``` fonksiyonu ```N::f``` olarak doğrudan nitelenmediği halde, argüman olan ```s_obj```'nin tipi ```N::S``` olduğu için derleyici ```N``` namespace'ini de arar ve ```N::f(S)```'i bulur.


#### Detaylı Örnek:

    ```cpp
    class my_class
    {
        private:
            int x;              // Sınıfın private veri üyesi 'x'
        public:
            void foo();         // my_class'ın üye fonksiyonu
    };

    int x = 45;                 // Global kapsamdaki 'x' değişkeni

    void my_class::foo()        // my_class'ın foo() fonksiyonunun tanımı
    {
        int x = 67;             // foo() fonksiyonunun yerel kapsamındaki 'x' değişkeni
        
        my_class::x = x + ::x;  // İşlemin yapıldığı kritik satır
    }
    ```

**```my_class::x = x + ::x;``` ifadesi bu durumda şu anlama gelir:**

```my_class``` sınıfının üyesi olan ```x``` = (```foo()``` fonksiyonunun yerel ```x```'i) + (```global x```'i) şeklinde nitelendirilir. Şimdi buradaki değerleriyerine koyarsak

- ```my_class::x``` = ```67``` + ```45```
- ```my_class::x``` = ```112```

---

### Class Kullanımına Ait Ekstra Notlar:

1) **Erişim belirleyici kullanmadan sınıf tanımlama:**

    ```cpp
    class _my_class
    {
        // Bu scope'ta "private" veya"public" anahtar sözcüğü kullanılmamışsa ve "class" anahtar sözcüğü kullanılarak sınıf oluşturulmışsa, Class'ın sınıfı "private" olur
    }

    struct _my_struct
    {
        // Struct anahtar sözcüğü ile sınıf tanımlanmışsa sınıf "public" olur
    }
    ```

2) **Mülakat sorusu-1:** 

    C++' bir sınıfın **```public``` interface**'i: sadece sınıfın **```public``` bölümü değil**, sınıfın **```public``` bölümündeki öğeler + başlık dosyasındaki(headre file) ```global``` bildirimlerdir**.

3) **Sınıf scope içinde aynı isimli değişken tanımlama:**
    Bir **syntax hatasıdır**. Bir sınıfın scope'u içerisinde **aynı isimli** bir **değişken** tanımlanamaz. 
    
    Sınıfın ```public```, ```private``` ve ```protected``` bölümleri yani erişim ayrıcalıklı kod alanları **bir scope değildir**.

    ```cpp
    class _my_class
    {
        private:
            int var;
        
        public:
            double var; // Syntax hatası oluşur.
    }
    ```
