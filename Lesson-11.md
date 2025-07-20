# 11.Ders

## Class

### Constructor Functions(Yapıcı Fonkisyonlar)

Bir sınıfın nesnesi oluşturulduğunda **otomatik olarak** çağrılan özel bir üye fonksiyondur. (**ctor** olarak kısaltması yapılabilir.)

#### Constructor Function(Yapıcı Fonkisyon) Nasıl Tanımlanır?
    
1) **Sınıf Adıyla Aynı İsim:** Yapıcı fonksiyonun adı, ait olduğu sınıfın adıyla tamamen aynı olmalıdır.

2) **Geri Dönüş Tipi Yok:** Yapıcı fonksiyonların açıkça belirtilmiş bir geri dönüş tipi yoktur ( ```void``` bile yazılmaz).

3) **Erişim Belirleyici:** Genellikle ```public``` erişim belirleyicisi altında tanımlanırlar, böylece sınıfın dışından nesneler oluşturulabilir.

```cpp
class Ogrenci 
{
    private:
        std::string ad;
        int yas;
        double notOrtalamasi;

    public:
        // Bu bir yapıcı fonksiyondur.
        // Dışarıdan 'ad', 'yas' ve 'notOrtalamasi' değerleri alarak nesneyi başlatır.
        Ogrenci(const std::string& ogrenciAdi, int ogrenciYasi, double ortalama) 
        {
            ad = ogrenciAdi;
            yas = ogrenciYasi;
            notOrtalamasi = ortalama;
            std::cout << ad << " adında yeni bir öğrenci nesnesi oluşturuldu." << std::endl;
        }

        // Parametresiz (Varsayılan) Yapıcı
        Ogrenci() 
        {
            ad = "Bilinmiyor";
            yas = 0;
            notOrtalamasi = 0.0;
            std::cout << "Varsayılan değerlerle bir öğrenci nesnesi oluşturuldu." << std::endl;
        }

        // Diğer üye fonksiyonlar (getter'lar vb.)
        void bilgileriGoster() const 
        {
            std::cout << "Ad: " << ad << ", Yaş: " << yas << ", Ortalama: " << notOrtalamasi << std::endl;
        }
};
```

#### Constructor Function(Yapıcı Fonkisyon) Türleri

1) **Parametreli Yapıcı (Parameterized Constructor)**
    
    Yukarıdaki ```Ogrenci(const std::string& ogrenciAdi, int ogrenciYasi, double ortalama)``` gibi, parametreler alarak nesneye ilk değerleri atayan yapıcılardır. Nesne oluşturulurken parantez içinde bu parametreler verilir:

    ```cpp
    Ogrenci ali("Ali Can", 18, 3.5); // Parametreli yapıcı çağrılır
    ```    

2) **Varsayılan Yapıcı (Default Constructor)**

    Hiç parametre almayan yapıcı fonksiyondur. Eğer bir sınıf için hiçbir yapıcı tanımlamazsanız, C++ derleyicisi otomatik olarak bir varsayılan yapıcı oluşturur (eğer başka bir yapıcı tanımlanmamışsa). Ancak, parametreli bir yapıcı tanımlarsanız, derleyici varsayılan yapıcıyı otomatik olarak oluşturmaz; bu durumda parametresiz bir yapıcıya ihtiyacınız varsa onu kendiniz tanımlamanız gerekir.

    ```cpp
    Ogrenci yeniOgrenci; // Varsayılan yapıcı çağrılır
    ```
---

#### Başlatıcı Listesi (Initializer List)

Yapıcı fonksiyonun gövdesinden önce, iki nokta üst üste (```:```) ile belirtilen bir liste içinde veri üyelerini başlatır.

 Başlatıcı listesi kullanmak, üyelerin doğrudan başlatılmasını (direct initialization) sağlar, atama (assignment) işlemi yapmaz. Bu, özellikle ```const``` veri üyeleri, referans veri üyeleri ve temel tipler için daha verimli ve bazen zorunlu bir yöntemdir.

```cpp
class Urun 
{
    private:
        std::string urunAdi;
        const int urunKodu; // const üye - sadece başlatıcı listesinde başlatılabilir

    public:
        // Başlatıcı listesi kullanılarak tanımlanmış yapıcı
        Urun(const std::string& ad, int kod) : urunAdi(ad), urunKodu(kod) {
            std::cout << urunAdi << " (" << urunKodu << ") ürünü oluşturuldu." << std::endl;
        }

        // Not: urunKodu const olduğu için bu şekilde tanımlanamaz:
        // Urun(const std::string& ad, int kod) {
        //     urunAdi = ad;
        //     urunKodu = kod; // Hata! const üye atanamaz, sadece başlatılabilir
        // }
};

// main içinde:
int main() 
{
    Ogrenci o1("Ayşe Yılmaz", 20, 3.8); // Parametreli yapıcı
    o1.bilgileriGoster();

    Ogrenci o2; // Varsayılan yapıcı
    o2.bilgileriGoster();

    Urun telefon("Akıllı Telefon", 12345); // Başlatıcı listeli yapıcı
    return 0;
}
```

**NOT:** Contructor member functionlar kesinlikle **```const```<u> member function olamaz!**</u>

---

### Destructors Functions (Yıkıcı Fonksiyonlar)

Bir sınıfın nesnesi **bellekten silinirken veya kapsam dışına çıkarken** (ömrü sona erdiğinde) otomatik olarak çağrılan özel bir üye fonksiyondur. Yapıcı fonksiyonların (constructors) tam tersi bir görevi vardır: nesnenin yaşam döngüsünün sonunu yönetmek. (**dtor** olarak kısaltması yapılabilir.)

- Kullanım amacı şunlardır.

    1) **Kaynakları Serbest Bırakma:** Nesnenin yaşam süresi boyunca tahsis ettiği dinamik belleği (```new``` ile ayrılan), açtığı dosyaları, kurduğu ağ bağlantılarını veya diğer sistem kaynaklarını serbest bırakır. Bu, bellek sızıntılarını (memory leaks) ve kaynak tükenmelerini önler.
    2) **Temizlik İşlemleri:** Nesneye ait son temizlik işlemlerini (örneğin, bir veritabanı bağlantısını kapatma, bir log dosyasını sonlandırma) yapar
    3) **Tutarlı Durumun Sona Ermesi:** Nesne bellekten kaldırılmadan önce gerekli son durum ayarlamalarını yapar.

#### Destructors Function(Yıkıcı Fonkisyon) Nasıl Tanımlanır?

1) **Tilde (```~```) İşareti ve Sınıf Adı:** Yıkıcı fonksiyonun adı, sınıf adının önüne bir tilde (```~```) işareti konularak oluşturulur. Örneğin, ```MyClass``` sınıfının yıkıcısı ```~MyClass``` olacaktır.

2) **Geri Dönüş Tipi Yok:** Yapıcı fonksiyonlar gibi, yıkıcı fonksiyonların da açıkça belirtilmiş bir geri dönüş tipi yoktur ( ```void``` bile yazılmaz).

3) **Parametre Almaz:** Yıkıcı fonksiyonlar hiçbir parametre almaz. Bu yüzden, bir sınıfın sadece bir tane yıkıcısı olabilir.

4) **Erişim Belirleyici:** Genellikle ```public``` erişim belirleyicisi altında tanımlanırlar.

```cpp
#include <iostream>
#include <string>

class DosyaYoneticisi 
{
private:
    std::string dosyaAdi;
    FILE* dosyaIsaretcisi; // Dosya işlemlerinde kullanılan bir işaretçi (örnek amaçlı)

public:
    // Yapıcı (Constructor): Dosyayı açar ve kaynağı tahsis eder
    DosyaYoneticisi(const std::string& ad) : dosyaAdi(ad), dosyaIsaretcisi(nullptr) 
    {
        dosyaIsaretcisi = fopen(dosyaAdi.c_str(), "w"); // Dosyayı yazma modunda aç
        if (dosyaIsaretcisi) {
            std::cout << dosyaAdi << " dosyası başarıyla açıldı." << std::endl;
        } else {
            std::cerr << "Hata: " << dosyaAdi << " dosyası açılamadı!" << std::endl;
        }
    }

    // Yıkıcı (Destructor): Nesne ömrünü tamamladığında dosyayı kapatır ve kaynağı serbest bırakır
    ~DosyaYoneticisi() 
    {
        if (dosyaIsaretcisi) {
            fclose(dosyaIsaretcisi); // Dosyayı kapat
            std::cout << dosyaAdi << " dosyası kapatıldı ve kaynak serbest bırakıldı." << std::endl;
        }
        std::cout << "DosyaYoneticisi nesnesi yok ediliyor." << std::endl;
    }

    void dosyaYaz(const std::string& icerik) 
    {
        if (dosyaIsaretcisi) {
            fputs(icerik.c_str(), dosyaIsaretcisi);
            std::cout << "Dosyaya yazıldı: \"" << icerik << "\"" << std::endl;
        }
    }
};

void fonsiyonKapsami() 
{
    std::cout << "--- fonsiyonKapsami başladı ---" << std::endl;
    DosyaYoneticisi logDosyasi("my_log.txt"); // Nesne burada oluşturulur, yapıcı çağrılır
    logDosyasi.dosyaYaz("Bu bir log mesajıdır.");
    std::cout << "--- fonsiyonKapsami sona eriyor ---" << std::endl;
    // 'logDosyasi' nesnesi kapsam dışına çıkar ve yıkıcısı otomatik olarak çağrılır.
} // Nesnenin ömrü burada biter

int main() 
{
    std::cout << "=== Main başladı ===" << std::endl;
    fonsiyonKapsami(); // fonsiyonKapsami çağrılıyor
    std::cout << "=== Main sona eriyor ===" << std::endl;

    // Dinamik olarak tahsis edilmiş bir nesne örneği
    DosyaYoneticisi* raporDosyasi = new DosyaYoneticisi("rapor.txt"); // 'new' ile oluşturuldu, yapıcı çağrıldı
    raporDosyasi->dosyaYaz("Bu bir rapor içeriğidir.");
    delete raporDosyasi; // 'delete' ile manuel olarak bellek serbest bırakıldı, yıkıcı çağrıldı

    return 0;
}
```

#### Destructors Function(Yıkıcı Fonkisyon) Çağırılma Zamanları:

- **Yerel Nesneler:** Bir fonksiyon içinde tanımlanan yerel nesneler, fonksiyonun yürütülmesi sona erdiğinde (kapsam dışına çıktıklarında) otomatik olarak yok edilir.

- **Global/Statik Nesneler:** Global veya statik ömre sahip nesneler, programın sonu geldiğinde yok edilir.

- **Dinamik Olarak Tahsis Edilmiş Nesneler:** ```new``` operatörü ile tahsis edilmiş nesneler, ```delete``` operatörü ile açıkça serbest bırakıldıklarında yıkıcıları çağrılır. Eğer ```delete``` çağrılmazsa, bellek sızıntısı oluşur.


**NOT:** Eğer bir sınıf için hiçbir yıkıcı **tanımlamazsanız**, C++ derleyicisi sizin için **otomatik olarak bir varsayılan yıkıcı oluşturur.** Bu varsayılan yıkıcı, sınıfın veri üyeleri için (varsa) tanımlanmış yıkıcıları otomatik olarak çağırır. Ancak, eğer sınıfınız ```new``` ile dinamik bellek tahsis ediyorsa veya başka sistem kaynaklarını kullanıyorsa, **mutlaka kendi yıkıcınızı tanımlamanız ve bu kaynakları serbest bırakmanız gerekir.** Aksi takdirde ciddi bellek sızıntıları ve kararsız programlar ortaya çıkabilir.

---

**NOT:** Sınıfın **defalt ctor** ve **destructor** fonksiyonları aynı zamanda sınıfın **"special member function"** kategorisindedir.

---

### C++'de Storage Duration

Değişkenin ne kadar süreyle bellekte var olacağını ve yaşam döngüsünü (ne zaman oluşturulup ne zaman yok edileceğini) belirleyen önemli bir özelliktir.

1) **Otomatik Depolama Süresi (Automatic Storage Duration)**
    * **Tanım:** Bir kod bloğuna (fonksiyon gövdesi, ```if``` bloğu, döngü bloğu vb.) girildiğinde oluşturulan ve o bloktan çıkıldığında otomatik olarak yok edilen değişkenlerdir. Bunlar genellikle **yığın (stack)** üzerinde depolanır.
    
    * **Anahtar Kelime:** Genellikle açıkça belirtilmez; varsayılan depolama süresidir. İsteğe bağlı olarak ```auto``` anahtar kelimesiyle belirtilebilir, ancak C++11'den itibaren ```auto```'nun farklı bir anlamı (tip çıkarımı) olduğundan bu kullanım artık yaygın değildir.

    * **Yaşam Döngüsü:**
        - **Oluşum:** Blok başladığında.
        - **Yok Oluş:** Blok sona erdiğinde.

    * **Örnek:**

        ```cpp
        void myFunction() 
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

    ---

2) **Statik Depolama Süresi (Static Storage Duration)**
    * **Tanım:** Programın yaşam süresi boyunca var olan değişkenlerdir. Yalnızca bir kez başlatılırlar ve program sonlandığında yok edilirler. Bunlar genellikle programın **veri bölümünde** (data segment) depolanır.

    * **Anahtar Kelime:** ```static``` veya global değişkenler için hiç anahtar kelime kullanılmaz.

    * **Yaşam Döngüsü:**
        - **Oluşum:** Program başlamadan önce (veya ilk kullanıldıklarında, C++11 sonrası yerel statik değişkenler için).
        - **Yok Oluş:**  Program sonlandığında.
    
    * **Alt Kategoriler:**
        - **Global Değişkenler:** Herhangi bir fonksiyonun veya sınıfın dışında tanımlanan değişkenler. Varsayılan olarak statik depolama süresine sahiptir.

            ```cpp
            int globalSayac = 0; // Statik depolama süresi
            ```

        - **Statik Yerel Değişkenler:** Bir fonksiyon veya blok içinde ```static``` anahtar kelimesiyle tanımlanan değişkenler. Sadece bir kez başlatılır ve fonksiyon tekrar çağrılsa bile değerlerini korurlar.

            ```cpp
            void sayacFonk() 
            {
                static int cagriSayisi = 0; // Statik depolama süresi, sadece bir kez 0'a atanır
                cagriSayisi++;
                std::cout << "Çağrı Sayısı: " << cagriSayisi << std::endl;
            }
            // sayacFonk() ilk çağrıldığında cagriSayisi = 1, ikinci çağrıda = 2 vb. olur.
            ```

        - **Statik Sınıf Üyeleri:** Bir sınıfın ```static``` olarak tanımlanmış veri üyeleri. Sınıfın tüm nesneleri arasında paylaşılan tek bir kopyaları vardır.

            ```cpp
            class MyClass 
            {
                public:
                    static int instanceCount; // Statik depolama süresi
                    MyClass() { instanceCount++; }
            };

            int MyClass::instanceCount = 0; // Sınıf dışında başlatılmalı
            ```

    ---

3) **Dinamik Depolama Süresi (Dynamic Storage Duration)**
    * **Tanım:** Programcı tarafından açıkça tahsis edilen ve serbest bırakılan bellek bloklarıdır. Bunlar genellikle **free store** veya **yığın(heap)** üzerinde depolanır.

    * **Anahtar Kelime:** Belleği tahsis etmek için ```new``` ve serbest bırakmak için ```delete``` operatörleri kullanılır.

    * **Yaşam Döngüsü:**
        - **Oluşum:** ```new``` operatörü çağrıldığında.
        - **Yok Oluş:**  ```delete``` operatörü çağrıldığında. Eğer ```delete``` çağrılmazsa, bellek sızıntısı (memory leak) meydana gelir.

    * **Örnek:**        

        ```cpp
        void dynamicExample() {
            int* ptr = new int; // Dinamik depolama süresine sahip bir int tahsis edildi
            *ptr = 100;
            // ptr burada kullanılabilir
            delete ptr; // Dinamik bellek serbest bırakıldı, 'ptr'nin işaret ettiği bellek yok edildi
            ptr = nullptr; // Dangling pointer'ı önlemek için iyi pratik
        } // 'ptr' işaretçisi (kendisinin depolama süresi otomatik) burada kapsam dışına çıkar
        // ama işaret ettiği bellek 'delete' edilmediyse hala var olabilirdi.
        ```

Akıllı işaretçiler (```std::unique_ptr```, ```std::shared_ptr```) bu tür belleği daha güvenli bir şekilde yönetmek için kullanılır.

    ---

4)  **İş Parçacığı Depolama Süresi (Thread Storage Duration)**
    * **Tanım:** C++11 ile tanıtılan bu depolama süresi, her bir iş parçacığı (thread) için ayrı bir kopyası olan değişkenleri tanımlar. Değişken, iş parçacığı başladığında oluşturulur ve iş parçacığı sonlandığında yok edilir.

    * **Anahtar Kelime:** ```thread_local```.

    * **Yaşam Döngüsü:**
        - **Oluşum:** İlgili iş parçacığı başladığında veya değişkenin ilk kullanıldığı anda (ilk kullanım temelli başlatma için).
        - **Yok Oluş:**  İlgili iş parçacığı sonlandığında.

    * **Örnek:**    

        ```cpp
        #include <thread>
        #include <iostream>

        thread_local int threadSpecificData = 1; // Her thread için ayrı kopya

        void threadFunction() 
        {
            threadSpecificData++; // Bu thread'e ait kopya artırılır
            std::cout << "Thread ID: " << std::this_thread::get_id()
                    << ", Data: " << threadSpecificData << std::endl;
        }

        int main() 
        {
            std::thread t1(threadFunction);
            std::thread t2(threadFunction);

            t1.join();
            t2.join();

            // main thread'in kendi kopyası
            std::cout << "Main Thread Data: " << threadSpecificData << std::endl;
        }
        ```
        
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

* **Açıklama** 
    - Bir sınıf nesnesi global ise, o sınıf nesnesinin ömrü ```main``` foksiyonuı çağırılmadan başlatılıyor. 
    - Dilin vermiş olduğu garanti doğrultusunda bildirim sırasına göre contruct ve desconstruct fonksiyonları örnekte verildiği gibi ilk çağırlanın **"constructor" fonksiyonu ilk** ve **"deconstruct" fonkisyonu en son** çağılır. 
    - Global sınıf nesneleri **Static Storage Duration** kategorisindelerdir.
    - ```func()``` fonksiyonu içerisinde ```static``` olarak nesne tanımlandığı için fonksiyon **ilk çağırıldığında "ctor"** ve fonksiyonun **son çağırıldığı çağrıda "dtor"** fonkisyonu çağırılacakır. 

 * **NOT:** Aynı proramın farklı kaynak dosyalarında tanımlanan global sınıf nesnelerinin **ctor**'larının **çağırılması sırası dil tarafından belirlenmiş değildir!**

---

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

---

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

---

* **Örnek:** Aşağıda örnek ile herhangi bir döngü ve el ile yazmadan 1'den  100'e kadar nasıl yazdırılabileceği gösterilmiştir. Bu gösterimin amacı "ctor" un çalıştırıldığına dair örnek vermektir.

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
    my_class int a[10];
}
```

---

1.15'de kaldım