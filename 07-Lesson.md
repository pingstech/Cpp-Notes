# 7. Ders

## Classes (Sınıflar)

### The `this` Pointer (this İşaretçisi)

`this` anahtar kelimesi, bir **üye fonksiyon (member function)** içinde çağrıldığı **nesnenin (object) kendisini** gösteren bir **işaretçidir (pointer)**. `this` keyword'ü oluşturulan bir değer **PR-Value** ifadesidir. Aşağıdaki durumlarda kullanılır:

```cpp
class my_class
{
    public:
        foo();
    private:
        int mx{}, my{};
};

void fun1(my_class *p)
{

}

void fun1(my_class &p)
{

}

void my_class::foo()
{
    mx = 56;
    this->mx = 56;
    (*this).mx = 56;
    my_class::mx = 56;    

    // Yukarıdaki ifadelerin hepsinin derleyici kısmında aynı kodu üretecektir.

    fun1(this);    // fonksiyon parametresi pointer ise
    fun1(*this);    // fonksiyon parametresi referans ise
}
```

1)  **Sınıfın Veri Üyeleri ile Yerel Değişkenler Arasındaki İsim Çakışmalarını Çözmek:**

    Bir üye fonksiyonun parametresi veya yerel değişkeni, sınıfın bir veri üyesiyle aynı isme sahip olduğunda kullanılır.

    ```cpp
    class Ornek 
    {
        public:
            int veri; // Sınıfın veri üyesi

            void ayarla(int veri)   // 'veri' burada bir parametre (yerel bir değişken gibi)
            { 
                this->veri = veri;  // this->veri: Sınıfın veri üyesi
                                    // veri: Parametre olarak gelen 'veri'
            }
    };
    ```

2)  **Chaining (Zincirleme Çağrılar) Yapmak:**

    Bir üye fonksiyonun, çağrıldığı nesnenin kendisini döndürmesi gerektiğinde (`*this` kullanarak), aynı nesne üzerinde birden fazla üye fonksiyon çağrısı arka arkaya yapılabilir.

    ```cpp
    class HesapMakinesi 
    {
        public:
            double sonuc;

            HesapMakinesi(double baslangic) : sonuc(baslangic) {}

            HesapMakinesi& topla(double deger) // Referans olarak kendisini döndürüyor
            { 
                sonuc += deger;
                return *this; // Çağrıldığı nesnenin kendisini döndür
            }

            HesapMakinesi& cikar(double deger) 
            {
                sonuc -= deger;
                return *this;   // Çağrıldığı nesnenin kendisini döndür
            }
    };

    HesapMakinesi& HesapMakinesi::bol(double deger)
    {
        sonuc *= deger;
        return *this;    // Çağrıldığı nesnenin kendisini döndür
    }

    // main içinde zincirleme çağrı örneği:
    // HesapMakinesi h(10.0);
    // h.topla(5.0).cikar(2.0); // Zincirleme çağrı
    // std::cout << h.sonuc << std::endl; // Çıktı: 13
    ```

3)  **Nesnenin Kendi Adresini Başka Bir Fonksiyona Geçirmek:**

    Bir nesnenin kendi adresini (`this`) başka bir fonksiyona veya nesneye parametre olarak geçirmek gerektiğinde kullanılır.

    ```cpp
    class B; // İleri bildirim (Forward declaration)

    class A 
    {
        public:
            void b_yeGonder(B& b_obj); // B sınıfından bir nesneye kendi adresini gönderecek
    };

    class B 
    {
        public:
            void a_danAl(A* a_ptr) { // A sınıfından bir işaretçi alıyor
                // a_ptr şimdi 'A' sınıfının kendisini gösteriyor
                // ...
            }
    };

    void A::b_yeGonder(B& b_obj) 
    {
        b_obj.a_danAl(this); // 'this' A sınıfının nesnesinin adresidir
    }
    ```

<!-- end list -->

  * **Önemli Notlar:**

      * **`const` Üye Fonksiyonlarda `this`:** Bir `const` üye fonksiyon içinde `this` işaretçisi, `const` olmayan bir `this` işaretçisi yerine `const` bir işaretçi (`const Ornek*`) olarak kabul edilir. Bu, `const` fonksiyonların sınıfın veri üyelerini değiştirememesini sağlar.

      * **Static Üye Fonksiyonlarda `this` Yoktur:** `static` üye fonksiyonlar bir sınıfın nesnesine bağlı değildir; doğrudan sınıf adıyla çağrılırlar. Bu nedenle, `static` üye fonksiyonların içinde `this` işaretçisi bulunmaz ve kullanılamaz.

        ```cpp
        class my_class
        {
            void func()
            {
                this;   // Doğru kullanımdır.
            }
            
            static void foo()
            {
                this;   // Syntax hatası oluşturur.
            }
        };
        ```

      * Aşağıdaki gibi bir **Chaining (Zincirleme Çağrılar)** yapmak mümkündür:

        ```cpp
        class my_class
        {
            public:
                my_class* foo() { return this; }
                my_class* bar() { return this; }
                my_class* baz() { return this; }
        };

        int main()
        {
            my_class * x = new my_class;
            x->bar()->foo()->baz();     // Zincirleme Çağrılar (Chaining) alternatif kullanım 
        }
        ```

-----

### const Member Functions (const Üye Fonksiyonlar)

`const` üye fonksiyonların temel amacı, bir nesnenin durumunu (yani veri üyelerinin değerlerini) **okuyan** ancak **değiştirmeyen** fonksiyonları açıkça belirtmektir. Bu, aşağıdaki faydaları sağlar:

1)  **Veri Bütünlüğü ve Güvenlik:** Bir nesnenin, `const` bir bağlamda kullanıldığında (örneğin `const` bir referans veya işaretçi aracılığıyla erişildiğinde) veri üyelerinin yanlışlıkla değiştirilmesini engeller. Bu, özellikle büyük ve karmaşık sistemlerde veri bütünlüğünü korumak için kritik öneme sahiptir.

2)  **Okunabilirlik ve Niyet Açıklığı:** Fonksiyonun ismine bakmadan bile, `const` anahtar kelimesi sayesinde o fonksiyonun nesnenin durumunu değiştirmeyeceğini anlarsınız. Bu, kodun okunabilirliğini ve anlaşılırlığını artırır.

3)  **`const` Nesnelerle Çalışma Yeteneği:** Sadece `const` üye fonksiyonlar, `const` olarak tanımlanmış nesneler üzerinden çağrılabilir. Eğer bir fonksiyon `const` olmasaydı ve `const` bir nesne üzerinden çağrılmaya çalışılsaydı derleme hatası alınırdı. Bu, `const` nesnelerle güvenli bir şekilde etkileşim kurmayı sağlar.

<!-- end list -->

```cpp
class Kitap 
{
    private:
        std::string baslik;
        int sayfaSayisi;

    public:
        Kitap(const std::string& b, int ss) : baslik(b), sayfaSayisi(ss) {}

        // Bu bir const üye fonksiyondur. Sınıfın veri üyelerini değiştiremez.
        std::string getBaslik() const   // 'const' olabilmesi için sınıf içinde tanımlanırken fonksiyonun sonuna 'const' ifadesi eklenir
        { 
            return baslik;
        }

        // Bu da bir const üye fonksiyondur.
        int getSayfaSayisi() const // 'const' olabilmesi için sınıf içinde tanımlanırken fonksiyonun sonuna 'const' ifadesi eklenir
        { 
            return sayfaSayisi;
        }

        // Bu const bir üye fonksiyon değildir. Sınıfın veri üyelerini değiştirebilir.
        void setSayfaSayisi(int yeniSayfaSayisi) 
        {
            sayfaSayisi = yeniSayfaSayisi; // 'sayfaSayisi' değiştiriliyor
        }

        // Hatalı const fonksiyon örneği (derleme hatası verir)
        // int getSayfaSayisiHata() const {
        //     sayfaSayisi = 100; // Hata: 'const' fonksiyonda veri üyesi değiştirilemez
        //     return sayfaSayisi;
        // }
};
```

### const Objects and const Member Functions (const Nesneler ve const Üye Fonksiyonlar)

**Yalnızca `const` üye fonksiyonlar, `const` nesneler üzerinden çağrılabilir**.

```cpp
#include <string>

class Kitap 
{
    private:
        std::string baslik;
        int sayfaSayisi;

    public:
        Kitap(const std::string& b, int ss) : baslik(b), sayfaSayisi(ss) {} // Constructor bir fonksiyondur. İlerleyen zamanlarda anlatılacak.
        std::string getBaslik() const { return baslik; }
        int getSayfaSayisi() const { return sayfaSayisi; }
        void setSayfaSayisi(int yeniSayfaSayisi) { sayfaSayisi = yeniSayfaSayisi; }
};

int main() 
{
    // Normal (non-const) bir nesne
    Kitap roman("Yüz Yıllık Yalnızlık", 450);

    // Normal nesne üzerinden hem const hem de non-const fonksiyonlar çağrılabilir.
    std::cout << "Başlık (Normal Nesne): " << roman.getBaslik() << std::endl;
    roman.setSayfaSayisi(460); // Sayfa sayısını değiştirebiliriz

    // const bir nesne
    const Kitap dersKitabi("C++ Programlama", 800);

    // const nesne üzerinden SADECE const üye fonksiyonlar çağrılabilir.
    std::cout << "Başlık (Const Nesne): " << dersKitabi.getBaslik() << std::endl;
    std::cout << "Sayfa Sayısı (Const Nesne): " << dersKitabi.getSayfaSayisi() << std::endl;

    // dersKitabi.setSayfaSayisi(810); // Hata! 'dersKitabi' const olduğu için
                                    // non-const 'setSayfaSayisi' çağrılamaz.
}
```

Bu, fonksiyonlara `const` referans veya işaretçi ile nesneler geçirdiğinizde büyük önem taşır. Bu sayede fonksiyonun, aldığı nesnenin durumunu değiştirmeyeceğinden emin olursunuz.

#### The `mutable` Keyword (mutable Anahtar Sözcüğü)

Nadiren de olsa, bir `const` üye fonksiyon içinde dahi bazı veri üyelerini değiştirmeye ihtiyaç duyabilirsiniz (örneğin, bir önbellek sayacı veya bir kilit mekanizması gibi mantıksal olarak nesnenin durumunu değiştirmeyen ancak fiziksel olarak belleği güncelleyen üyeler). Bu tür durumlar için, veri üyesini `mutable` olarak işaretleyebilirsiniz.

```cpp
class Sayac 
{
    private:
        int deger;
        mutable int erisimSayisi; // Bu üye 'const' fonksiyonlarda dahi değiştirilebilir

    public:
        Sayac(int d) : deger(d), erisimSayisi(0) {}

        int getDeger() const {
            erisimSayisi++; // 'const' fonksiyonda bile 'erisimSayisi' değiştirilebilir
            return deger;
        }
};

// main içinde:
// const Sayac s(10);
// std::cout << s.getDeger() << std::endl; // erisimSayisi artırılır
// std::cout << s.getDeger() << std::endl; // erisimSayisi tekrar artırılır
```

-----

**Mülakat Notları-1**

```cpp
// Setter (Mutator) functions
void func(T *); // Nesnenin adresi gönderilir
void foo(T &);  // Nesnenin kendisi gönderilir

// ------------------------------

// Getters (Accessor) functions: Salt okuma amacıyla bir nesneye erişim sağlandığı anlamını verir.
void func(const T *);
void foo(const T &);
```