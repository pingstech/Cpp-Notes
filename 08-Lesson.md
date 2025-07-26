# 8.Ders

**Eğitimin 11. Dersidir.**

## Class

### Constructor Functions(Yapıcı Fonkisyonlar)

Bir sınıfın nesnesi oluşturulduğunda **otomatik olarak** çağrılan özel bir üye fonksiyondur. (**ctor** olarak kısaltması yapılabilir.)

#### Constructor Function(Yapıcı Fonkisyon) Nasıl Tanımlanır?

1) **Sınıf Adıyla Aynı İsim:** Yapıcı fonksiyonun adı, ait olduğu sınıfın adıyla tamamen aynı olmalıdır.

2) **Geri Dönüş Tipi Yok:** Yapıcı fonksiyonların açıkça belirtilmiş bir <u>**geri dönüş tipi yoktur!**</u> ( ```void``` bile yazılmaz).

3) **Erişim Belirleyici:** Genellikle ```public``` erişim belirleyicisi altında tanımlanırlar, böylece sınıfın dışından nesneler oluşturulabilir.

```cpp
class ogrenci
{
private:
    std::string ad;
    int yas;
    double not_ortalamasi;

public:
    // Parametreli Yapıcı Fonksiyon:
    // Dışarıdan aldığı değerlerle nesnenin veri üyelerini başlatır.
    ogrenci(const std::string& ogrenci_adi, int ogrenci_yasi, double ortalama)
    {
        ad = ogrenci_adi;
        yas = ogrenci_yasi;
        not_ortalamasi = ortalama;
        std::cout << ad << " adında yeni bir öğrenci nesnesi oluşturuldu." << std::endl;
    }

    // Parametresiz (Varsayılan) Yapıcı Fonksiyon (Default Constructor):
    // Hiç parametre almaz ve üyeleri varsayılan değerlerle başlatır.
    ogrenci()
    {
        ad = "Bilinmiyor";
        yas = 0;
        not_ortalamasi = 0.0;
        std::cout << "Varsayılan değerlerle bir öğrenci nesnesi oluşturuldu." << std::endl;
    }

    // Diğer üye fonksiyonlar
    void bilgileri_goster() const
    {
        std::cout << "Ad: " << ad << ", Yaş: " << yas << ", Ortalama: " << not_ortalamasi << std::endl;
    }
};
````

#### Constructor Function(Yapıcı Fonkisyon) Türleri

1)  **Parametreli Yapıcı (Parameterized Constructor)**

    Yukarıdaki `ogrenci(const std::string& ogrenci_adi, int ogrenci_yasi, double ortalama)` gibi, parametreler alarak nesneye ilk değerleri atayan yapıcılardır. Nesne oluşturulurken parantez içinde bu parametreler verilir:

    ```cpp
    ogrenci ali("Ali Can", 18, 3.5); // Parametreli yapıcı çağrılır
    ```

2)  **Varsayılan Yapıcı (Default Constructor)**

      - Hiç parametre almayan yapıcı fonksiyondur.
      - Eğer bir sınıf için hiçbir yapıcı tanımlamazsanız, C++ derleyicisi otomatik olarak bir varsayılan yapıcı oluşturur (eğer başka bir yapıcı tanımlanmamışsa).
      - **Önemli:** Ancak, siz herhangi bir parametreli yapıcı (örneğin `ogrenci(std::string, int, double)`) tanımlarsanız, derleyici artık otomatik olarak varsayılan yapıcıyı oluşturmaz. Bu durumda, `ogrenci yeni_ogrenci;` gibi parametresiz bir nesne oluşturmak isterseniz, varsayılan yapıcıyı kendiniz `public` olarak tanımlamak zorundasınız.

    <!-- end list -->

    ```cpp
    Ogrenci yeniOgrenci; // Varsayılan yapıcı çağrılır
    ```

    **NOT:** Yapıcı fonksiyonlar, bir nesnenin oluşturulma anını yönettikleri için `const` üye fonksiyon olamazlar.

-----

#### Başlatıcı Listesi (Initializer List)

Başlatıcı listesi, yapıcı fonksiyonun gövdesinden önce (`:` ile başlayarak) \<u\>veri üyelerini doğrudan başlatan (direct initialization) bir yöntemdir\</u\>. Bu, özellikle aşağıdaki durumlar için hem daha verimli hem de zorunludur:

  - **`const` Veri Üyeleri:** `const` olarak tanımlanmış üyeler sadece bir kez, oluşturulma anında başlatılabilir. Onlara daha sonra değer atanamaz. Bu yüzden `const` üyeler yapıcı fonksiyonun gövdesinde değil, yalnızca başlatıcı listesinde başlatılabilir.

  - **Referans Veri Üyeleri:** Referanslar da `const` üyeler gibi tanımlandıkları anda bir nesneye veya değişkene bağlanmalıdır. Bu nedenle yalnızca başlatıcı listesinde başlatılabilirler.

  - **Verimlilik:** Temel tipler ve sınıf nesneleri için başlatıcı listesi kullanmak, atama (assignment) yerine doğrudan başlatma (initialization) yaptığı için daha verimlidir. Atama işleminde önce nesne varsayılan yapıcı ile oluşturulur, sonra atama operatörü ile yeni değer verilir. Başlatıcı listesi ise nesneyi doğrudan istenen değerle oluşturur.

<!-- end list -->

```cpp
class urun
{
private:
    std::string urun_adi;
    const int urun_kodu; // const üye - sadece başlatıcı listesinde başlatılabilir.

public:
    // Başlatıcı listesi kullanılarak tanımlanmış yapıcı
    urun(const std::string& ad, int kod) : urun_adi(ad), urun_kodu(kod)
    {
        // Yapıcı gövdesi burada başka işlemler için kullanılabilir.
        std::cout << urun_adi << " (" << urun_kodu << ") ürünü oluşturuldu." << std::endl;
    }

    /*
    // YANLIŞ KULLANIM: urun_kodu 'const' olduğu için bu kod derlenmez.
    urun(const std::string& ad, int kod) {
        urun_adi = ad;
        urun_kodu = kod; // HATA: const üyeye atama yapılamaz.
    }
    */
};
```

**NOT:** Contructor member functionlar kesinlikle **`const`**\<u\> member function olamaz\!\*\*\</u\>

-----

### Destructors Functions (Yıkıcı Fonksiyonlar)

Bir sınıfın nesnesi **bellekten silinirken veya kapsam dışına çıkarken** (ömrü sona erdiğinde) otomatik olarak çağrılan özel bir üye fonksiyondur. Yapıcı fonksiyonların (constructors) tam tersi bir görevi vardır: nesnenin yaşam döngüsünün sonunu yönetmek. (**dtor** olarak kısaltması yapılabilir.)

  - Kullanım amacı şunlardır.

    1)  **Kaynakları Serbest Bırakma:** Nesnenin `new` ile ayırdığı dinamik belleği iade etmek, açtığı dosyaları kapatmak, ağ bağlantılarını sonlandırmak gibi işlemleri gerçekleştirir. Bu, bellek sızıntılarını (memory leaks) ve kaynak israfını önler.

    2)  **Temizlik İşlemleri:** Nesneye ait son temizlik işlemlerini (örneğin, bir veritabanı bağlantısını kapatma, bir log dosyasını sonlandırma) yapar

    3)  **Tutarlı Durumun Sona Ermesi:** Nesne bellekten kaldırılmadan önce gerekli son durum ayarlamalarını yapar.

#### Destructors Function(Yıkıcı Fonkisyon) Nasıl Tanımlanır?

1)  **Tilde (`~`) İşareti ve Sınıf Adı:** Yıkıcı fonksiyonun adı, sınıf adının önüne bir tilde (`~`) işareti konularak oluşturulur. Örneğin, `MyClass` sınıfının yıkıcısı `~MyClass` olacaktır.

2)  **Geri Dönüş Tipi Yok:** Yapıcı fonksiyonlar gibi, yıkıcı fonksiyonların da açıkça belirtilmiş bir geri dönüş tipi yoktur ( `void` bile yazılmaz).

3)  **Parametre Almaz:** Yıkıcı fonksiyonlar hiçbir parametre almaz. Bu yüzden, bir sınıfın sadece bir tane yıkıcısı olabilir.

4)  **Erişim Belirleyici:** Genellikle `public` erişim belirleyicisi altında tanımlanırlar.

<!-- end list -->

```cpp
#include <iostream>
#include <string>
#include <cstdio> // C-stili dosya işlemleri için

class dosya_yoneticisi
{
private:
    std::string dosya_adi;
    FILE* dosya_isaretcisi;

public:
    // Yapıcı (Constructor): Kaynağı (dosyayı) edinir.
    dosya_yoneticisi(const std::string& ad) : dosya_adi(ad), dosya_isaretcisi(nullptr)
    {
        dosya_isaretcisi = fopen(dosya_adi.c_str(), "w");
        if (dosya_isaretcisi) {
            std::cout << dosya_adi << " dosyası başarıyla açıldı (KAYNAK EDİNİLDİ)." << std::endl;
        } else {
            std::cerr << "Hata: " << dosya_adi << " dosyası açılamadı!" << std::endl;
        }
    }

    // Yıkıcı (Destructor): Nesnenin ömrü bitince kaynağı serbest bırakır.
    ~dosya_yoneticisi()
    {
        std::cout << "dosya_yoneticisi nesnesi yok ediliyor..." << std::endl;
        if (dosya_isaretcisi) {
            fclose(dosya_isaretcisi);
            std::cout << dosya_adi << " dosyası kapatıldı (KAYNAK SERBEST BIRAKILDI)." << std::endl;
        }
    }

    void dosyaya_yaz(const std::string& icerik)
    {
        if (dosya_isaretcisi) {
            fputs(icerik.c_str(), dosya_isaretcisi);
            std::cout << "Dosyaya yazıldı: \"" << icerik << "\"" << std::endl;
        }
    }
};

void fonksiyon_kapsami()
{
    std::cout << "--- fonksiyon_kapsami başladı ---" << std::endl;
    dosya_yoneticisi log_dosyasi("my_log.txt"); // Nesne burada oluşur, yapıcı çağrılır.
    log_dosyasi.dosyaya_yaz("Bu bir log mesajıdır.");
    std::cout << "--- fonksiyon_kapsami sona eriyor ---" << std::endl;
    // 'log_dosyasi' nesnesi bu satırda kapsam dışına çıkar ve yıkıcısı otomatik olarak çağrılır.
}

int main()
{
    std::cout << "=== Main başladı ===" << std::endl;
    fonksiyon_kapsami();
    std::cout << "---" << std::endl;

    // Dinamik olarak tahsis edilmiş nesne örneği
    dosya_yoneticisi* rapor_dosyasi = new dosya_yoneticisi("rapor.txt"); // 'new' ile oluşturuldu.
    rapor_dosyasi->dosyaya_yaz("Bu bir rapor içeriğidir.");
    delete rapor_dosyasi; // 'delete' ile bellek serbest bırakılırken yıkıcı manuel olarak tetiklenir.

    std::cout << "=== Main sona eriyor ===" << std::endl;
    return 0;
}
```

**ÖNEMLİ NOT:** Eğer bir sınıf için özel bir yıkıcı tanımlamazsanız, C++ derleyicisi sizin için otomatik olarak bir yıkıcı oluşturur. Bu varsayılan yıkıcı, sınıfınız dinamik bellek veya harici kaynaklar (dosya, soket vb.) yönetmiyorsa yeterlidir. Ancak, yapıcı fonksiyonda `new` ile bellek ayırdıysanız veya bir kaynağı manuel olarak edindiyseniz, **kaynak sızıntısını önlemek için bu kaynakları serbest bırakan bir yıkıcı yazmanız zorunludur.**

**Special Member Functions:** Bir sınıfın varsayılan yapıcısı ve yıkıcısı, aynı zamanda o sınıfın "özel üye fonksiyonları" (special member functions) kategorisine girer.

-----

**NOT:** Sınıfın **defalt ctor** ve **destructor** fonksiyonları aynı zamanda sınıfın **"special member function"** kategorisindedir.

-----

### C++'de Storage Duration (Depolama Süresi)

Değişkenin ne kadar süreyle bellekte var olacağını ve yaşam döngüsünü (ne zaman oluşturulup ne zaman yok edileceğini) belirleyen önemli bir özelliktir.

1)  **Otomatik Depolama Süresi (Automatic Storage Duration)**

      * **Tanım:** Bir kod bloğuna (fonksiyon gövdesi, `if` bloğu, döngü bloğu vb.) girildiğinde oluşturulan ve o bloktan çıkıldığında otomatik olarak yok edilen değişkenlerdir. Bunlar genellikle **yığın (stack)** üzerinde depolanır.

      * **Anahtar Kelime:** Genellikle açıkça belirtilmez; varsayılan depolama süresidir. İsteğe bağlı olarak `auto` anahtar kelimesiyle belirtilebilir, ancak C++11'den itibaren `auto`'nun farklı bir anlamı (tip çıkarımı) olduğundan bu kullanım artık yaygın değildir.

      * **Yaşam Döngüsü:**

          - **Oluşum:** Blok başladığında.
          - **Yok Oluş:** Blok sona erdiğinde.

      * **Örnek:**

        ```cpp
        void func()
        {
            int x = 10; // Otomatik depolama süresine sahip 'x'
            if (true)
            {
                double y = 20.5; // Otomatik depolama süresine sahip 'y'
                // 'y' burada kullanılabilir
            } // 'y' burada yok edilir
            // 'x' burada hala kullanılabilir
        } // 'x' burada yok edilir
        ```

    -----

2)  **Statik Depolama Süresi (Static Storage Duration)**

      * **Tanım:** Programın yaşam süresi boyunca var olan değişkenlerdir. Yalnızca bir kez başlatılırlar ve program sonlandığında yok edilirler. Bunlar genellikle programın **veri bölümünde** (data segment) depolanır.

      * **Anahtar Kelime:** `static` veya global değişkenler için hiç anahtar kelime kullanılmaz.

      * **Yaşam Döngüsü:**

          - **Oluşum:** Program başlamadan önce (veya ilk kullanıldıklarında, C++11 sonrası yerel statik değişkenler için).
          - **Yok Oluş:** Program sonlandığında.

      * **Alt Kategoriler:**

          - **Global Değişkenler:** Herhangi bir fonksiyonun veya sınıfın dışında tanımlanan değişkenler. Varsayılan olarak statik depolama süresine sahiptir.

            ```cpp
            int globalSayac = 0; // Statik depolama süresi
            ```

          - **Statik Yerel Değişkenler:** Bir fonksiyon veya blok içinde `static` anahtar kelimesiyle tanımlanan değişkenler. Sadece bir kez başlatılır ve fonksiyon tekrar çağrılsa bile değerlerini korurlar.

            ```cpp
            void sayac_func()
            {
                static int cagri_sayisi = 0; // Statik depolama süresi, sadece bir kez 0'a atanır
                cagri_sayisi++;
                std::cout << "Çağrı Sayısı: " << cagri_sayisi << std::endl;
            }
            // sayac_func() ilk çağrıldığında cagri_sayisi = 1, ikinci çağrıda = 2 vb. olur.
            ```

          - **Statik Sınıf Üyeleri:** Bir sınıfın `static` olarak tanımlanmış veri üyeleri. Sınıfın tüm nesneleri arasında paylaşılan tek bir kopyaları vardır.

            ```cpp
            class my_class
            {
                public:
                    static int instance_count; // Statik depolama süresi
                    my_class() { instance_count++; }
            };

            int my_class::instance_count = 0; // Sınıf dışında başlatılmalı
            ```

    -----

3)  **Dinamik Depolama Süresi (Dynamic Storage Duration)**

      * **Tanım:** Programcı tarafından açıkça tahsis edilen ve serbest bırakılan bellek bloklarıdır. Bunlar genellikle **free store** veya **yığın(heap)** üzerinde depolanır.

      * **Anahtar Kelime:** Belleği tahsis etmek için `new` ve serbest bırakmak için `delete` operatörleri kullanılır.

      * **Yaşam Döngüsü:**

          - **Oluşum:** `new` operatörü çağrıldığında.
          - **Yok Oluş:** `delete` operatörü çağrıldığında. Eğer `delete` çağrılmazsa, bellek sızıntısı (memory leak) meydana gelir.

    -----

4)  **İş Parçacığı Depolama Süresi (Thread Storage Duration)**

      * **Tanım:** `thread_local` anahtar kelimesiyle tanımlanır. Her bir iş parçacığı (thread) için bu değişkenden ayrı bir kopya oluşturulur.

      * **Yaşam Döngüsü:**

          - **Oluşum:** İlgili iş parçacığı başladığında veya değişkenin ilk kullanıldığı anda (ilk kullanım temelli başlatma için).
          - **Yok Oluş:** İlgili iş parçacığı sonlandığında.

#### Global Nesnelerin Başlatılma Sırası

  - **Aynı Dosya İçinde:** Tek bir kaynak dosyası içindeki global **nesnelerin yapıcıları (constructor fonksiyonları)**, bildirim sıralarına göre çağrılır. **Yıkıcıları (deconstruct fonkisyonları)** ise bu sıranın tam tersi yönde çağrılır.

  - **Farklı Dosyalarda:** Farklı kaynak dosyalarında (`.cpp`) tanımlanan **global nesnelerin yapıcılarının hangi sırayla çağrılacağı C++ standardı tarafından belirlenmemiştir\!** Bu durum, bir global nesnenin yapıcısının, henüz oluşturulmamış başka bir global nesneye bağımlı olması halinde "**Static Initialization Order Fiasco**" olarak bilinen ve tanıması zor bir hataya yol açabilir.

**Örnek:**

```cpp
class my_class
{
    public:
        my_class() { std::cout << "my_class default ctor this = " << this << '\n'; }

        ~my_class() { std::cout << "my_class destructor this = " << this << '\n'; }

        void foo() { std::cout << "my_class::foo() this = " << this << '\n'; }
};

class my_class2
{
    public:
        my_class2() { std::cout << "my_class2 default ctor this = " << this << '\n'; }

        ~my_class2() { std::cout << "my_class2 destructor this = " << this << '\n'; }
};

class my_class3
{
    public:
        my_class3() { std::cout << "my_class3 default ctor this = " << this << '\n'; }

        ~my_class3() { std::cout << "my_class3 destructor this = " << this << '\n'; }
};

my_class    _my_class;
my_class2   _my_class2;

void func()
{
    static int call_count{};
    std::cout << "func fonkisyonuna yapılan "<< ++call_count << ". cagri\n";
    static my_class3 _my_class3;
}

int main()
{
    std::cout << "Main started\n";

    std::cout << "Address of the \"_my_class\" object = " << &_my_class << '\n';
    std::cout << "Address of the \"_my_class2\" object = " << &_my_class2 << '\n';
    _my_class.foo();

    func(); // "ctor" ve "dtor" sadece bu fonksiyon çağırıldığında çağırılacaktır. Bu fonksiyon çağırılmasaydı "my_class3" isimli obje çalıştırılmayacaktı.

    func();
    func();
    func();

    std::cout << "Main end\n";
}
```

-----

```cpp
/* api.cpp kaynak dosyası içeriği */
    a my_class;

// ----------------------------

/* utils.cpp kaynak dosyası içeriği */
    b my_class;

// ----------------------------

/* app.cpp kaynak dosyası içeriği */

    c my_class;

// Yukarıdaki dosyalardaki "ctor" veya "dtor" fonksiyonlarının çağırılma sırası dil tarafından belirlenmemiştir. Bir çakışma olması durumuna "static init. fiasco" denmektedir.
```

-----

```cpp
class my_class
{
    public:
        my_class() { std::cout << "my_class default ctor this = " << this << '\n'; }

        ~my_class() { std::cout << "my_class destructor this = " << this << '\n'; }

        void foo() { std::cout << "my_class::foo() this = " << this << '\n'; }
};

void func()
{
  my_class _my_class_f; // Her "func()" çağırısında "ctor" çağırılacak ve fonksiyonun bitmesiyle birlikte "dtor" çağırılacaktır.
  // ...
}

int main()
{
    std::cout << "Main started-1\n";
    {
        std::cout << "Main on going-1\n";
        my_class _my_class; // ctor

        std::cout << "Main on going-2\n";
    } // "_my_class" nesnesinin dtor fonksiyonu burada çağırılacak

    func();
    func();
    func();
    func();

    std::cout << "Main end\n";
}
```

  * **Açıklama:** Yukarıdaki örnek **otomatik ömürlü nesnelerin** ömrünün gösterimine örnektir.

-----

  * **Örnek:** Aşağıda örnek ile herhangi bir döngü ve el ile yazmadan 1'den 100'e kadar nasıl yazdırılabileceği gösterilmiştir. Bu gösterimin amacı "ctor" un çalıştırıldığına dair örnek vermektir.

<!-- end list -->

```cpp
class my_class
{
    public:
        my_class()
        {
            static int counter{0} ;
            std::cout << ++counter <<" ";
        }
};

int main()
{
    my_class a[100];
}
```

-----

## RAII(Resource Acquisition Is Initialization)

RAII, modern C++'ın temelini oluşturan ve **kaynak yönetimini** güvenli ve otomatik hale getiren güçlü bir programlama paradigmasıdır. RAII, programınızın bellek sızıntıları, dosya kapatma unutkanlıkları, kilitlenmeyen mutex'ler gibi yaygın kaynak yönetimi hatalarını önlemesine yardımcı olur.

#### Temel Prensip

  - **Kaynakların Edinimi (Acquisition):** Bir kaynağı (bellek, dosya tanıtıcısı, ağ bağlantısı, kilit vb.) edindiğiniz an, bu edinimi bir **nesnenin ömrüne bağlarsınız**. Bu genellikle nesnenin **yapıcı (constructor)** fonksiyonunda gerçekleşir. Yapıcı, kaynağı başarıyla edinmekten sorumludur.

  - **Kaynakların Serbest Bırakılması (Release):** Nesne, ömrünün sonuna geldiğinde (kapsam dışına çıktığında, `delete` ile silindiğinde vb.), otomatik olarak **yıkıcı (destructor)** fonksiyonu çağrılır. Yıkıcı, edinilen kaynağı güvenli ve temiz bir şekilde serbest bırakmaktan sorumludur.

Böylece, kaynağın edinimi "başlatma" (constructor) ile, kaynağın serbest bırakılması ise "yıkım" (destructor) ile eşleştirilir.

#### RAII'nin Faydaları:

1)  **Otomatik Kaynak Yönetimi:** Manuel olarak kaynakları serbest bırakmayı unutma riskini ortadan kaldırır. Fonksiyonlardan normal çıkış yapıldığında, istisna fırlatıldığında veya erken dönüş yapıldığında bile kaynaklar otomatik olarak temizlenir.

2)  **Bellek Sızıntılarını Önleme:** Dinamik olarak ayrılmış belleğin `delete` ile serbest bırakılmamasından kaynaklanan yaygın hataları önler.

3)  **Hata Yönetimini Basitleştirme:** İstisnalar (exceptions) fırlatıldığında bile kaynakların temizlenmesini garanti eder. ` try-catch  ` bloklarında karmaşık `finally` benzeri temizlik kodlarına ihtiyaç duyulmaz.

4)  **Güvenlik:** Kilitler gibi senkronizasyon mekanizmalarının her zaman doğru bir şekilde açılıp kapanmasını sağlar, böylece **dead-lock** (kilitlenme) gibi sorunların önüne geçer.

5)  **Kod Okunabilirliği ve Bakım Kolaylığı:** Kaynak yönetimi mantığı tek bir yerde (sınıfın yapıcı ve yıkıcısında) kapsüllendiği için kod daha temiz ve anlaşılır olur.

#### RAII'nin Temel Örnekleri:

C++ Standart Kütüphanesi (STL), RAII prensibinin birçok mükemmel uygulamasını içerir:

1)  **Akıllı İşaretçiler (Smart Pointers):**

      - **`std::unique_ptr`:** Yalnızca bir sahibin olabileceği dinamik bellek alanlarını yönetir. İşaretçinin ömrü sona erdiğinde bellek otomatik olarak serbest bırakılır.

      - **`std::shared_ptr`:** Belleği birden fazla sahibin paylaşmasına izin verir. Tüm shared\_ptr kopyaları kapsam dışına çıktığında veya sıfırlandığında bellek serbest bırakılır.

      - **`std::weak_ptr`:** shared\_ptr döngüsel referanslarını kırmak için kullanılır, kaynağa sahiplik sağlamaz.

    <!-- end list -->

    ```cpp
    #include <memory> // Akıllı işaretçiler için

    void akilliIsaretciOrnek()
    {
        std::cout << "Fonksiyon başladı." << std::endl;
        // int türünde dinamik bellek tahsis eder.
        // unique_ptr kapsam dışına çıktığında bellek otomatik silinir.
        std::unique_ptr<int> sayi(new int(10));

        std::cout << "Sayı değeri: " << *sayi << std::endl;

        if (true)
        {
            std::unique_ptr<double> pi(new double(3.14));
            std::cout << "Pi değeri: " << *pi << std::endl;
        } // 'pi' burada kapsam dışına çıkar ve belleği otomatik silinir.

        // Fonksiyonun ortasında bir istisna atılsa bile 'sayi' tarafından tutulan bellek serbest kalır.
        // throw std::runtime_error("Hata oluştu!");

        std::cout << "Fonksiyon sona eriyor." << std::endl;
    } // 'sayi' burada kapsam dışına çıkar ve belleği otomatik silinir.

    /* Çıktı örneği (bellek silme mesajları yoksa bile, C++ garantisiyle silinir):
    Fonksiyon başladı.
    Sayı değeri: 10
    Pi değeri: 3.14
    Fonksiyon sona eriyor.
    */
    ```

    -----

2)  **`std::fstream` (Dosya Akışları):** Dosya nesneleri (input/output streams) oluşturulduğunda dosyayı açar ve nesne yok edildiğinde dosyayı otomatik olarak kapatır.

    ```cpp
    #include <fstream> // Dosya işlemleri için

    void dosyaOrnek() {
        std::ofstream dosya("ornek.txt"); // Constructor dosyayı açar
        if (dosya.is_open()) {
            dosya << "Merhaba, RAII!" << std::endl;
            std::cout << "Dosyaya yazıldı." << std::endl;
        } else {
            std::cerr << "Dosya açılamadı!" << std::endl;
        }
    } // 'dosya' nesnesi burada kapsam dışına çıkar ve destructor dosyayı otomatik kapatır.
    ```

    -----

3)  **`std::lock_guard` ve `std::unique_lock` (Mutex Kilitleri):** Çoklu iş parçacığı programlamasında mutex'leri güvenli bir şekilde kilitlemek ve kilidi açmak için kullanılır. Nesne oluşturulduğunda mutex'i kilitler ve kapsam dışına çıktığında kilidi otomatik olarak serbest bırakır.

    ```cpp
    #include <mutex> // Mutex işlemleri için

    std::mutex mtx; // Bir mutex nesnesi

    void kilitOrnek() {
        std::cout << "Kilit denemesi..." << std::endl;
        std::lock_guard<std::mutex> kilit(mtx); // Constructor mutex'i kilitler
        // Kritik bölüm: Sadece bir thread bu kısma girebilir
        std::cout << "Kritik bölümdeyiz." << std::endl;
        // Herhangi bir istisna atılsa bile veya fonksiyon normal tamamlansa bile,
        // 'kilit' nesnesi kapsam dışına çıktığında mutex otomatik olarak serbest bırakılır.
        std::cout << "Kritik bölümden çıkılıyor." << std::endl;
    } // 'kilit' burada kapsam dışına çıkar ve destructor mutex'i açar.
    ```

    -----

#### Kendi RAII Sınıfınızı Oluşturmak:

Kendi özel kaynaklarınız (örneğin, C API'lerinden elde edilen tanıtıcılar) için de RAII sınıfları yazabilirsiniz. Anahtar, yapıcıda kaynağı edinmek ve yıkıcıda onu serbest bırakmaktır.

```cpp
#include <iostream>
// varsayalım ki bu dışarıdan gelen bir C API'si
void* acquire_resource()
{
    std::cout << "Kaynak edinildi!" << std::endl;
    return new int(5);
}

void release_resource(void* res) { std::cout << "Kaynak serbest bırakıldı!" << std::endl; delete static_cast<int*>(res); }

class MyResource
{
    private:
        void* resource_handle;

    public:
        // Yapıcı: Kaynağı edin
        MyResource() : resource_handle(acquire_resource()) { if (resource_handle == nullptr) { throw std::runtime_error("Kaynak edinilemedi!"); } }

        // Yıkıcı: Kaynağı serbest bırak
        ~MyResource() { if (resource_handle != nullptr) { release_resource(resource_handle); } }

        // Kopya yapıcı ve atama operatörlerini engelle (veya özel olarak yönet)
        // Kaynak sahipliği tekilse kopyalamayı genellikle yasaklarız.
        MyResource(const MyResource&) = delete;
        MyResource& operator=(const MyResource&) = delete;

        // Taşıma yapıcı ve atama operatörlerini ekle (C++11+)
        // Sahipliğin güvenli bir şekilde taşınmasını sağlar.
        MyResource(MyResource&& other) noexcept : resource_handle(other.resource_handle)
        {
            other.resource_handle = nullptr; // Kaynağı taşıdığımızı belirt
        }
        MyResource& operator=(MyResource&& other) noexcept
        {
            if (this != &other)
            {
                release_resource(resource_handle); // Kendi kaynağımızı serbest bırak
                resource_handle = other.resource_handle;
                other.resource_handle = nullptr;
            }
            return *this;
        }
};

void myCustomResourceExample() {
    std::cout << "RAII'li özel kaynak örneği başladı." << std::endl;
    MyResource res; // Kaynak edinildi
    // Kaynak kullanılıyor...
    std::cout << "Kaynak kullanılıyor..." << std::endl;
    std::cout << "RAII'li özel kaynak örneği bitti." << std::endl;
} // 'res' nesnesi kapsam dışına çıkar ve kaynak serbest bırakılır

// main() içinde çağrı:
// myCustomResourceExample();
/* Çıktı:
   RAII'li özel kaynak örneği başladı.
   Kaynak edinildi!
   Kaynak kullanılıyor...
   RAII'li özel kaynak örneği bitti.
   Kaynak serbest bırakıldı!
*/
```

-----

  * **Örnek:**

<!-- end list -->

```cpp
class my_class
{
    private:
        int mx, my;

    public:
        void print() const
        {
            std::cout << "mx = " << mx << "my =" << my << '\n';
        }
};

int main()
{
    my_class m1;        // Default init
    m1.print();

    my_class m2{};      // Value init, yapıldığında herhangi bir constructor çağırılmadan önce "zero init" denilen özel durum gerçekleştiriliyor.
    m2.print();
}
```

Yukarıdaki örnek ile default ve value init gösterilmiştir.

-----

  * **Örnek: Initialization Türleri**

<!-- end list -->

```cpp
class my_class
{
private:
    int m_x, m_y;

public:
    // Kullanıcı tarafından tanımlanmış bir yapıcı
    my_class(int x) { std::cout << "my_class(int x) ctor\n"; }
    // ...
};

int main()
{
    // my_class m1; HATA: Parametreli yapıcı tanımlandığı için varsayılan yapıcı derleyici tarafından oluşturulmaz.

    my_class m1(10);      // Direct Initialization
    my_class m2 = 20;     // Copy Initialization (Önce geçici bir nesne oluşturulur, sonra kopyalanır - derleyici optimize edebilir)
    my_class m3{ 57 };    // Direct List Initialization (C++11) - Genellikle en güvenli yöntemdir.
}
```

Yukarıdaki örnek ile direct, copy ve direct list init gösterilmiştir.

-----

  * **Örnek: Yapıcı Fonksiyonun Aşırı Yüklenmesi (Overloading)**

<!-- end list -->

```cpp
class my_class
{
    public:
        my_class() { std::cout << "my_class default ctor \n"; }
        my_class(int x, int y) { std::cout << "my_class(int x, int y) -> x = " << x << " | -> y = " << y <<'\n'; }
        my_class(double x, double y) { std::cout << "my_class(double x, double y) -> x = " << x << " | -> y = " << y <<'\n'; }
        my_class(int x) { std::cout << "my_class(int x) -> x = " << x <<'\n'; }
        my_class(double x) { std::cout << "my_class(double x) -> x = " << x <<'\n'; }
};

int main()
{
    my_class m1;            // default "my_class()" ctor çalışır.
    my_class m2(3.456f);    // Double parametreli "my_class(double x)" ctor çalışır.
    //my_class m3(4u);        // Syntax error
    my_class m4(2, 4);      // Int parametreli "my_class(double x, double y)" ctor çalışır.
    my_class m5(2., 4.);    // Double parametreli "my_class(double x)" ctor çalışır.
}
```

-----

  * **Örnek: Erişim Belirleyici Hatası ve Overload Resolution**

<!-- end list -->

```cpp
class my_class
{
    // Erişim belirleyici belirtilmediği için burası varsayılan olarak 'private'dır.
    my_class(int x) { std::cout << "private ctor(int)\n"; }

public:
    my_class(double x) { std::cout << "public ctor(double)\n"; }
};

int main()
{
    my_class m(12);
/*
--------------------------------------------------------------------------------
Main.cpp:17:18: error: ‘my_class::my_class(int)’ is private within this context
   17 |     my_class m(12);
      |                  ^
Main.cpp:6:5: note: declared private here
    6 |     my_class(int x) { std::cout << "my_class(int x) -> x = " << x <<'\n'; }
      |     ^~~~~~~~
--------------------------------------------------------------------------------
*/
}
```

**Açıklama:** Bu kod derlenmez ve bir erişim hatası verir. Sebebi şudur:

1)  **Aday Belirleme (Overload Resolution):** Derleyici, `m(12)` çağrısı için en uygun fonksiyonu arar. `12` bir `int` olduğu için en iyi eşleşme `my_class(int x)` yapıcısıdır.

2)  **Erişim Kontrolü:** Derleyici en iyi eşleşmeyi bulduktan sonra bu fonksiyona erişim hakkı olup olmadığını kontrol eder. `my_class(int x)` `private` olduğu için `main` fonksiyonundan erişilemez ve derleyici hata verir. Derleyici, "en iyi eşleşme `private` olduğu için bir sonraki en iyi eşleşmeyi (örn: `double`'a çevirip `public` olanı) deneyeyim" demez. Süreç, en iyi eşleşmeyi bul ve sonra erişimi kontrol et şeklindedir.

-----

  * **Örnek:**

<!-- end list -->

```cpp
class my_class
{
    public:
        my_class()
        {
            mx = 0;
            my = 0;
            // Zero init burada gerçekleşmez çünkü bu ifade bir atamadır.
        }

    private:
        int mx, my;
};
```

**Açıklama:** yukarıdaki gibi ctor içerisinde yapılan işlem **Zero-init olmaz** bu işlem bir **atama işlemidir**.

-----

  * **Başlatıcı Liste (Initializer List) Alternatif Örnekleri**

      - **Örnek:**

        ```cpp
        //api.h dosaysı içeriği

        class my_class
        {
            public:
                my_class() {}

            private:
                int mx, my;
        };

        //-----------------------

        //api.cpp dosaysı içeriği

        my_class::my_class() : mx(10), my(20) { }   // Geleneksel (pre‑C++11 / hâlâ geçerli)

        my_class::my_class() : mx{10}, my{20} { }   // Liste‑başlatma (C++11+)
        ```

    -----

      - **Örnek: Başlatıcı Listesi ve Tanımsız Davranış (Undefined Behavior)**

        ```cpp
        class my_class
        {
        public:
            // DOĞRU KULLANIM: Üyeler, bildirim sırasına göre başlatılır (önce m_x, sonra m_y).
            my_class() : m_x(10), m_y(m_x / 3) {} // m_y, m_x'in başlatılmış değerini kullanır.

            /*
            // YANLIŞ KULLANIM - TANIMSIZ DAVRANIŞ (UNDEFINED BEHAVIOR)
            // Başlatıcı listesindeki sıra değil, sınıftaki bildirim sırası önemlidir.
            // my_class() : m_y(10), m_x(m_y / 3) {}
            */

            void print() const { std::cout << "m_x = " << m_x << ", m_y = " << m_y << '\n'; }

        private:
            // Bildirim sırası: 1. m_x, 2. m_y
            int m_x;
            int m_y;
        };
        ```

        **Açıklama:** Sınıf üyeleri, başlatıcı listesinde hangi sırada yazıldıklarına göre değil, sınıf tanımı içinde hangi sırada bildirildiklerine göre başlatılırlar.

          - Sınıf nesnesini elemanlarının hayata gelme sırası ctor fonksiyonundan bağımsızdır. Bildirimdeki sıralamaya göre hayata gelecekleri için önce `mx` sonra `my` değeri oluşturulacaktır.

          - Yukarda yapılmış olan `my_class() : my(10), mx(my/3) {}` tanımlaması bir **undefined behavior** oluşmasına yol açar\! `mx` değeri intialize edilirken `my` değerinin çöp değeri(garbage value) ile intialize edilmeye çalışılmaktadır.

<!-- end list -->
