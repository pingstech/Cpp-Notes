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
        Urun(const std::string& ad, int kod) : urunAdi(ad), urunKodu(kod) 
        {
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
    my_class a[100];
}
```

---

## RAII(Resource Acquisition Is Initialization)

RAII, modern C++'ın temelini oluşturan ve **kaynak yönetimini** güvenli ve otomatik hale getiren güçlü bir programlama paradigmasıdır. RAII, programınızın bellek sızıntıları, dosya kapatma unutkanlıkları, kilitlenmeyen mutex'ler gibi yaygın kaynak yönetimi hatalarını önlemesine yardımcı olur.

#### Temel Prensip

- **Kaynakların Edinimi (Acquisition):** Bir kaynağı (bellek, dosya tanıtıcısı, ağ bağlantısı, kilit vb.) edindiğiniz an, bu edinimi bir **nesnenin ömrüne bağlarsınız**. Bu genellikle nesnenin **yapıcı (constructor)** fonksiyonunda gerçekleşir. Yapıcı, kaynağı başarıyla edinmekten sorumludur.

- **Kaynakların Serbest Bırakılması (Release):** Nesne, ömrünün sonuna geldiğinde (kapsam dışına çıktığında, ```delete``` ile silindiğinde vb.), otomatik olarak **yıkıcı (destructor)** fonksiyonu çağrılır. Yıkıcı, edinilen kaynağı güvenli ve temiz bir şekilde serbest bırakmaktan sorumludur.


Böylece, kaynağın edinimi "başlatma" (constructor) ile, kaynağın serbest bırakılması ise "yıkım" (destructor) ile eşleştirilir.

#### RAII'nin Faydaları:

1) **Otomatik Kaynak Yönetimi:** Manuel olarak kaynakları serbest bırakmayı unutma riskini ortadan kaldırır. Fonksiyonlardan normal çıkış yapıldığında, istisna fırlatıldığında veya erken dönüş yapıldığında bile kaynaklar otomatik olarak temizlenir.

2) **Bellek Sızıntılarını Önleme:** Dinamik olarak ayrılmış belleğin  ```delete``` ile serbest bırakılmamasından kaynaklanan yaygın hataları önler.

3) **Hata Yönetimini Basitleştirme:** İstisnalar (exceptions) fırlatıldığında bile kaynakların temizlenmesini garanti eder.  ```try-catch ``` bloklarında karmaşık ```finally``` benzeri temizlik kodlarına ihtiyaç duyulmaz.

4) **Güvenlik:** Kilitler gibi senkronizasyon mekanizmalarının her zaman doğru bir şekilde açılıp kapanmasını sağlar, böylece dead-lock (kilitlenme) gibi sorunların önüne geçer.

5) **Kod Okunabilirliği ve Bakım Kolaylığı:** Kaynak yönetimi mantığı tek bir yerde (sınıfın yapıcı ve yıkıcısında) kapsüllendiği için kod daha temiz ve anlaşılır olur.

#### RAII'nin Temel Örnekleri:

C++ Standart Kütüphanesi (STL), RAII prensibinin birçok mükemmel uygulamasını içerir:

1) **Akıllı İşaretçiler (Smart Pointers):**

    - **```std::unique_ptr```:** Yalnızca bir sahibin olabileceği dinamik bellek alanlarını yönetir. İşaretçinin ömrü sona erdiğinde bellek otomatik olarak serbest bırakılır.

    - **```std::shared_ptr```:** Belleği birden fazla sahibin paylaşmasına izin verir. Tüm shared_ptr kopyaları kapsam dışına çıktığında veya sıfırlandığında bellek serbest bırakılır.

    - **```std::weak_ptr```:** shared_ptr döngüsel referanslarını kırmak için kullanılır, kaynağa sahiplik sağlamaz.

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

    ---

2) **```std::fstream``` (Dosya Akışları):** Dosya nesneleri (input/output streams) oluşturulduğunda dosyayı açar ve nesne yok edildiğinde dosyayı otomatik olarak kapatır.

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

    ---

3) **```std::lock_guard``` ve ```std::unique_lock``` (Mutex Kilitleri):** Çoklu iş parçacığı programlamasında mutex'leri güvenli bir şekilde kilitlemek ve kilidi açmak için kullanılır. Nesne oluşturulduğunda mutex'i kilitler ve kapsam dışına çıktığında kilidi otomatik olarak serbest bırakır.

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

    ---

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

---

* **Örnek:**

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

---

* **Örnek:**

```cpp
class my_class
{
    private:
        int mx, my;

    public:
        my_class(int x)
        {
            std::cout << "my_class(int x) -> x = " << x <<'\n';
        }
};

int main()
{
    my_class m1(10);    // direct init
    my_class m2 = 20;   // copy init
    my_class m3{ 57 };  // direct list init
}
```

Yukarıdaki örnek ile direct, copy ve direct list init gösterilmiştir.

---

* **Örnek:**

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

Constructor fonksiyonunun "function overload" edilmesi.

---

* **Örnek:**

```cpp
class my_class
{
    my_class(int x) { std::cout << "my_class(int x) -> x = " << x <<'\n'; }

    public:
        my_class(double x) { std::cout << "my_class(double x) -> x = " << x <<'\n'; }
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

Derleyici, ```my_class m(12);``` çağrısını yaparken en iyi eşleşme olan ```my_class(int)``` yapıcısını seçer; oysa bu yapıcı sınıfın başında (public’den önce) tanımlandığı için private’dır ve erişilemez. Public olan ```my_class(double)``` yapıcısı ise dönüşüm gerektirdiği için daha düşük öncelikli olduğundan kullanılmaz ve sonuçta erişim hatası verilir.

**Hatırlatma:** **```class```** içinde <u>**erişim belirtmeden**</u> tanımlanan tüm üye değişken ve fonksiyonlar **private** kabul edilir. Struct’ta ise varsayılan erişim **public**’tır!</u>

---

* **Örnek:**

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

---

* **Başlatıcı Liste (Initializer List) Alternatif örnekleri**

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
    ---

    - **Örnek:**

        ```cpp
        class my_class
        {
            public:
                //my_class() : my(10), mx(my/3) {}    // Undefined behavior oluşur

                my_class() : mx(10), my(mx/3) {}

                void print() const { std::cout << "mx = " << mx << '\n'; }
            
            private:
                int mx, my;
        };

        int main()
        {
            my_class _my_class;

            _my_class.print();
        }
        ```

        **Açıklama:** Burada bir kaç özel durum vardır bunlar:
            
        - Sınıf nesnesini elemanlarının hayata gelme sırası ctor fonksiyonundan bağımsızdır. Bildirimdeki sıralamaya göre hayata gelecekleri için önce ```mx``` sonra ```my``` değeri oluşturulacaktır.
        
        - Yukarda yapılmış olan ```my_class() : my(10), mx(my/3) {}``` tanımlaması bir **undefined behavior** oluşmasına yol açar! ```mx``` değeri intialize edilirken ```my``` değerinin çöp değeri(garbage value) ile intialize edilmeye çalışılmaktadır.

---