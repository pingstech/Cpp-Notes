# 10.Ders

## Class

### ```this``` Keyword(Anahtar Kelimesi):

```this``` anahtar kelimesi, bir **üye fonksiyon (member function)** içinde çağrıldığı **nesnenin (object) kendisini** gösteren bir **işaretçidir (pointer)**. Aşağıdaki durumlarda kullanılır:

```this``` keyword'ü oluşturulan bir değer **PR-Value** ifadedir.

```cpp
class my_class
{
    public:
        foo();
    private:
        int mx{}, my{}
}

void fun1(my_class *p)
{

}

void fun1(my_class &p)
{

}

void my_class::foo()
{
    mx = 56
    this->mx = 56;
    (*this).mx = 56;
    my_class::mx = 56;    

    // Yukarıdaki ifadelerin hepsinin derleyici kısmında aynı kodu üretecektir.

    func1(this);    // fonksiyon parametresi pointer ise
    fun2(*this);    // fonksiyon parametresi referans ise
}
```

1) **Sınıfın Veri Üyeleri ile Yerel Değişkenler Arasındaki İsim Çakışmalarını Çözmek:**
    
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

2) **Zincirleme Çağrılar (Chaining) Yapmak:**

    Bir üye fonksiyonun, çağrıldığı nesnenin kendisini döndürmesi gerektiğinde (```*this``` kullanarak), aynı nesne üzerinde birden fazla üye fonksiyon çağrısı arka arkaya yapılabilir.

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
        return *this    // Çağrıldığı nesnenin kendisini döndür
    }

    // main içinde zincirleme çağrı örneği:
    // HesapMakinesi h(10.0);
    // h.topla(5.0).cikar(2.0); // Zincirleme çağrı
    // std::cout << h.sonuc << std::endl; // Çıktı: 13
    ```

3) **Nesnenin Kendi Adresini Başka Bir Fonksiyona Geçirmek:**

    Bir nesnenin kendi adresini (```this```) başka bir fonksiyona veya nesneye parametre olarak geçirmek gerektiğinde kullanılır.

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

* **Önemli Notlar:**

    - **```const``` Üye Fonksiyonlarda ```this```:** Bir ```const``` üye fonksiyon içinde ```this``` işaretçisi, ```const``` olmayan bir ```this``` işaretçisi yerine const bir işaretçi (```const Ornek*```) olarak kabul edilir. Bu, ```const``` fonksiyonların sınıfın veri üyelerini değiştirememesini sağlar.

    - **Static Üye Fonksiyonlarda ```this``` Yoktur:** ```static``` üye fonksiyonlar bir sınıfın nesnesine bağlı değildir; doğrudan sınıf adıyla çağrılırlar. Bu nedenle, ```static``` üye fonksiyonların içinde ```this``` işaretçisi bulunmaz ve kullanılamaz.

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
        }
        ```

    - Aşağıdaki gibi bir **Zincirleme Çağrılar (Chaining)** yapmak mümkündür:

        ```cpp
        class my_class
        {
            public:
                my_class* foo() { return this; }
                my_class* bar() { return this; }
                my_class* baz() { return this; }
        }

        int main()
        {
            my_class * x = new my_class;
            x->bar()->foo()->baz();     // Zincirleme Çağrılar (Chaining) alternatif kullanım 
        }
        ``` 

---

1.25'de kaldım