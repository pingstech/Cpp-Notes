# 11. Ders

**Eğitimin 14. Dersidir.**

## Class

### Move-Only Types(Sadece Taşınabilir Tipler)

C++'da nesneler genellikle kopyalanabilir veya taşınabilir özelliklere sahiptir. "Move-only type" (taşınabilir-sadece tip) ise, adı üzerinde, **yalnızca taşınabilen ancak kopyalanamayan** bir nesne türünü ifade eder.

Bir sınıfı "**move-only**" yapmak için, genellikle kopyalama yapıcıyı ve kopyalama atama operatörünü `= delete;` kullanarak sileriz. Böylece derleyici, bu **nesnelerin kopyalanmasına izin vermez**. Taşıma yapıcı ve taşıma atama operatörleri ise `&&` (**rvalue referans**) kullanarak, kaynağı bir nesneden diğerine güvenli bir şekilde aktarmamızı sağlar.

```cpp
#include <iostream>
#include <utility> // std::move için

class unique_resource 
{
    private:
        int* data_; // Kendi yönettiği dinamik belleğe işaretçi

    public:
        // 1. Yapıcı (Constructor): Nesne oluşturulduğunda belleği ayırır
        unique_resource(int val) : data_(new int(val)) 
        {
            std::cout << "Constructor: " << *data_ << std::endl;
        }

        // 2. Kopyalama Yapıcıyı Siliyoruz:
        // Bu satır, unique_resource nesnelerinin kopyalanarak yeni bir nesne oluşturmasını engeller.
        unique_resource(const unique_resource&) = delete;

        // 3. Kopyalama Atama Operatörünü Siliyoruz:
        // Bu satır, bir unique_resource nesnesinin içeriğinin başka bir nesneye kopyalanmasını engeller.
        unique_resource& operator=(const unique_resource&) = delete;

        // 4. Taşıma Yapıcı (Move Constructor): Kaynağı başka bir nesneden "taşır"
        // Parametresi 'unique_resource&& other' (rvalue referans) ile taşınacak nesneyi alır.
        unique_resource(unique_resource&& other) : data_(other.data_) 
        {
            other.data_ = nullptr; // Kaynağı taşıdık, diğer nesneyi boşalttık
            std::cout << "Move Constructor" << std::endl;
        }

        // 5. Taşıma Atama Operatörü (Move Assignment Operator): Kaynağı başka bir nesneden "taşır"
        // Mevcut nesnenin kendi kaynağını serbest bırakır ve diğer nesnenin kaynağını alır.
        unique_resource& operator=(unique_resource&& other) 
        {
            if (this != &other)  // Kendi kendine atamayı önle
            { 
                delete data_;       // Kendi kaynağımızı serbest bırak
                data_ = other.data_; // Kaynağı taşı
                other.data_ = nullptr; // Eski nesneyi boşalt
            }
            std::cout << "Move Assignment" << std::endl;
            return *this;
        }

        // 6. Yıkıcı (Destructor): Nesne silindiğinde ayrılan belleği serbest bırakır
        ~unique_resource() 
        {
            if (data_) // data_ nullptr değilse, yani hala bir kaynağa sahipse
            { 
                std::cout << "Destructor: " << *data_ << std::endl;
                delete data_; // Belleği serbest bırak
            } 
            else 
            {
                std::cout << "Destructor (nullptr)" << std::endl; // Zaten boşaltılmış
            }
        }

        // Yardımcı fonksiyon: İçindeki veriyi yazdırır
        void print_data() const 
        {
            if (data_) 
            {
                std::cout << "Data: " << *data_ << std::endl;
            } 
            else 
            {
                std::cout << "Data: (null)" << std::endl;
            }
        }
};

int main() 
{
    unique_resource resource_1(10); // Normal yapıcı
    resource_1.print_data();

    // unique_resource resource_2 = resource_1; // HATA! Kopyalamaya izin verilmez.

    unique_resource resource_4 = std::move(resource_1);  // resource_1'den resource_4'e taşıma
    resource_4.print_data();
    resource_1.print_data();            // resource_1 artık boşaldı

    unique_resource resource_5(30);
    resource_5.print_data();
    resource_5 = std::move(resource_4); // resource_4'ten resource_5'e taşıma
    resource_5.print_data();
    resource_4.print_data();            // resource_4 artık boşaldı
}
```

#### Neden Bazı Şeyler Sadece Taşınabilir Olmalıdır?

Elinizde çok özel bir anahtar var olduğunu düşünün. Bu anahtarın kopyasını yapıp başkasına verirseniz sorun çıkabilir, çünkü anahtarın kontrolü sizden gider. Ancak anahtarı bir başkasına devredebilir (taşıyabilir) ve artık anahtarın tek sahibinin o olmasını sağlayabilirsiniz.

**⚠️ DİKKAT:** Bir nesne "move-only" ise, onu kopyalamaya çalışmak **derleyici hatasına** neden olacaktır. Bu, programınızın daha güvenli olmasını sağlar çünkü yanlışlıkla kaynakların çift sahipliğini oluşturmanızı engeller.

**⚠️ DİKKAT:** Asla ama asla move memberlar **`= delete;` edilmemelidir!** Çünkü Copy işlemleri bloklanmış olur.

-----

### Temporaray Objects (Geçici Nesneler)

Bazen program çalışırken kısa bir süre için var olan, adı olmayan (anonim) ve belirli bir ifadenin sonucunda oluşan nesneler bulunur. Bu nesnelere **"Geçici Nesneler"** denir. Genellikle bir ifade tamamlandıktan hemen sonra yok olurlar.

####  Geçici Nesneler Nasıl Oluşturulur?

1) **Fonksiyonların Geri Dönüş Değerleri:** Bir fonksiyon bir nesne döndürdüğünde, bu nesne genellikle geçici bir nesne olarak oluşturulur.
2) **Tür Dönüşümleri (Casting):** Bir türü başka bir türe dönüştürdüğünüzde, yeni türde geçici bir nesne oluşabilir.
3) **İfadelerdeki Ara Sonuçlar**: Karmaşık ifadelerde, ara işlemlerin sonuçları geçici nesneler olarak depolanabilir.


```cpp
class my_class 
{
  private:
      std::string name_; // Sınıf üyesi için snake_case ve alt çizgi
  
  public:
      // Yapıcı (Constructor)
      my_class(const std::string& object_name) : name_(object_name) { std::cout << "my_class Constructor: " << name_ << std::endl; }
  
      // Yıkıcı (Destructor)
      ~my_class(){ std::cout << "my_class Destructor: " << name_ << std::endl; }
  
      // Bir değeri döndüren fonksiyon
      my_class create_my_class(const std::string& temp_name) 
      {
          // Bu fonksiyondan döndürülen nesne geçici bir nesne olacaktır.
          return my_class(temp_name);
      }
  
      // Sınıfın adını döndüren fonksiyon
      const std::string& get_name() const { return name_; }
};

// Fonksiyon: Geçici bir my_class nesnesi döndürür
my_class return_temporary_object() 
{
    std::cout << "  - return_temporary_object fonksiyonu çağrıldı." << std::endl;
    // Burada "Temporary From Func" adında bir my_class nesnesi oluşturulur
    // Bu nesne, fonksiyon bittiğinde geçici olarak var olacak.
    return my_class("Temporary From Func");
}

int main() 
{
    std::cout << "--- Main Başlangıcı ---" << std::endl;

    // Durum 1: Fonksiyonun geri dönüş değeri olarak geçici nesne
    std::cout << "\n--- Durum 1: Fonksiyon Dönüş Değeri ---" << std::endl;
    // return_temporary_object() çağrıldığında bir geçici nesne oluşur.
    // Bu geçici nesne, ifadenin sonunda (yani noktalı virgülden sonra) yok olur.
    return_temporary_object();
    std::cout << "Durum 1 ifadenin sonu." << std::endl;

    // Durum 2: Geçici nesnenin sabit bir referansa bağlanması
    // Geçici nesneler normalde ifadenin sonunda yok olur.
    // Ancak, eğer bir geçici nesne 'const' bir referansa bağlanırsa,
    // bu geçici nesnenin ömrü, referansın ömrü kadar uzar.
    std::cout << "\n--- Durum 2: Const Referansa Bağlama ---" << std::endl;
    const my_class& const_ref_to_temp = return_temporary_object();
    std::cout << "const_ref_to_temp adı: " << const_ref_to_temp.get_name() << std::endl;
    std::cout << "Durum 2 ifadenin sonu." << std::endl;
    // const_ref_to_temp nesnesi main fonksiyonu bitene kadar yaşar.

    // Durum 3: Doğrudan geçici nesne oluşturma
    std::cout << "\n--- Durum 3: Doğrudan Geçici Nesne ---" << std::endl;
    // my_class("Direct Temporary") ifadesi bir geçici nesne oluşturur.
    // Bu nesne de ifadenin sonunda yok olur.
    my_class("Direct Temporary");
    std::cout << "Durum 3 ifadenin sonu." << std::endl;

    std::cout << "\n--- Main Sonu ---" << std::endl;
    // Burada const_ref_to_temp nesnesi yok olur.
}
```

#### Durum 1: Fonksiyonun Geri Dönüş Değeri (Bir İfade Olarak Kullanılırsa)

  * **Ne Yapar?** Fonksiyon bir nesne döndürür, ancak bu nesneye herhangi bir değişken atanmadığı için geçici bir nesne olur ve hemen yok edilir.
  * **Neden Kullanılır?** Fonksiyonun asıl amacı yan etki (ekrana yazdırma, loglama) olduğunda veya dönen değere daha sonra ihtiyaç duyulmadığında.
  * **Nasıl Çalışır?** `return_temporary_object();` ifadesi bittiği an (noktalı virgülde) geçici nesne yok olur.

#### Durum 2: Geçici Nesnenin Sabit Bir Referansa Bağlanması (Life Expansion Durumu)

  * **Ne Yapar?** Fonksiyondan dönen geçici nesne, `const` bir referansa (`const my_class&`) bağlanır. Bu, geçici nesnenin ömrünü, bağlandığı referansın ömrü boyunca uzatır.
  * **Neden Kullanılır?** Büyük nesnelerin kopyalama maliyetinden kaçınmak ve geçici nesnelerin daha uzun süre kullanılabilmesini sağlamak için performans optimizasyonu amacıyla kullanılır.
  * **Nasıl Çalışır?** `const my_class& const_ref_to_temp = return_temporary_object();` satırındaki geçici nesne, `main` fonksiyonu sonlanana kadar (yani `const_ref_to_temp` referansı kapsamdan çıkana kadar) yaşamaya devam eder.
  * **⚠️ DİKKAT:** Yalnızca `const` L-Value referanslarına bağlanabilir; aksi takdirde derleme hatası oluşur.

#### Durum 3: Doğrudan Geçici Nesne Oluşturma

  * **Ne Yapar?** Herhangi bir değişkene atanmayan, doğrudan oluşturulan bir nesne (örn: `my_class("...")`). Bu da geçici bir nesne olarak kabul edilir.
  * **Neden Kullanılır?** Sadece tek seferlik, çok kısa süreli bir işlem için bir nesneye ihtiyaç duyulduğunda (örn: bir fonksiyona parametre olarak geçirmek için).
  * **Nasıl Çalışır?** `my_class("Direct Temporary");` ifadesi bittiği an (noktalı virgülde) geçici nesne yok olur.

-----

### Moved-From State (Taşınmış Nesne Durumu)

**Giriş:**
C++'da, bir nesnenin sahip olduğu kaynaklar (örneğin, dinamik bellek, dosya tanıtıcısı gibi) başka bir nesneye "taşındığında", orijinal nesne arkasında özel bir durumda kalır. Bu duruma **"Taşınmış Nesne Durumu" (Moved-From State)** denir. Bu durum, taşıma semantiği (move semantics) ile doğrudan ilgilidir ve kaynak yönetiminde önemlidir.

-----

#### Taşınmış Nesne Durumu Nasıl Oluşturulur?

Taşınmış nesne durumu, bir **taşıma işlemi** (move operation) sonucunda ortaya çıkar. Bu işlemler genellikle şunları içerir:

1.  **Taşıma Yapıcı (Move Constructor):** Bir nesne, başka bir nesnenin kaynaklarını "çalarak" (taşıyarak) oluşturulduğunda.
2.  **Taşıma Atama Operatörü (Move Assignment Operator):** Bir nesne, başka bir nesnenin kaynaklarını "çalarak" (taşıyarak) değer atadığında.
3.  **`std::move` Kullanımı:** `std::move` fonksiyonu, bir nesnenin taşınabilir olduğunu derleyiciye bildirir ve taşıma yapıcı/atama operatörünün çağrılmasına olanak tanır. `std::move` aslında taşıma yapmaz, sadece bir nesneyi "rvalue reference" olarak işaretler.

-----

**Örnek Kod:**

```cpp
class my_resource 
{
    private:
        int* data_;       // Yönettiği dinamik bellek
        std::string name_; // Nesne adı

    public:
        // Yapıcı (Constructor): Nesne oluşturulduğunda belleği ayırır.
        my_resource(const std::string& object_name, int val) : name_(object_name), data_(new int(val)) 
        {
            std::cout << "my_resource Constructor " << name_ << ": " << (data_ ? std::to_string(*data_) : "nullptr") << std::endl;
        }

        // Taşıma Yapıcı (Move Constructor): Kaynağı başka bir nesneden "taşır".
        // 'other' nesnesinin kaynaklarını (data_) alır ve 'other' nesnesini "taşınmış" duruma sokar.
        my_resource(my_resource&& other) : data_(other.data_), name_(other.name_ + " (Moved)") 
        {
            other.data_ = nullptr; // Orijinal nesne (other) kaynaklarını devretti, nullptr'a ayarlandı.
            other.name_ = other.name_ + " (Moved-from)"; // Taşan nesnenin adını değiştiriyoruz.
            std::cout << "Move Constructor " << name_ << " from " << other.name_ << std::endl;
        }

        // Yıkıcı (Destructor): Nesne silindiğinde ayrılan belleği serbest bırakır.
        ~my_resource() 
        {
            if (data_) 
            { // data_ nullptr değilse, yani hala bir kaynağa sahipse
                std::cout << "my_resource Destructor " << name_ << ": " << *data_ << std::endl;
                delete data_; // Belleği serbest bırak
            } 
            else 
            {
                // nullptr durumunda: ya hiç kaynak almadı ya da kaynakları taşındı.
                std::cout << "my_resource Destructor " << name_ << ": (nullptr)" << std::endl;
            }
        }

        // Mevcut değeri yazdırır
        void print_data() const 
        {
            if (data_) { std::cout << name_ << " - Data: " << *data_ << std::endl; } 
            else       { std::cout << name_ << " - Data: (nullptr / Moved-from)" << std::endl; }
        }
};

int main() 
{
    std::cout << "--- Main Başlangıcı ---" << std::endl;

    my_resource res_a("res_a", 100); // 'res_a' oluşturuldu, kaynağa sahip.
    res_a.print_data();

    std::cout << "\n--- res_a'yı res_b'ye taşıyoruz ---" << std::endl;
    // 'res_a'dan kaynakları 'res_b'ye taşıyoruz.
    // 'res_b'nin taşıma yapıcısı çağrılır.
    // Bu işlemden sonra 'res_a' "taşınmış nesne durumu"na girer.
    my_resource res_b = std::move(res_a);
    res_b.print_data(); // 'res_b' artık kaynağa sahip.
    res_a.print_data(); // 'res_a' artık "taşınmış" durumda.

    std::cout << "\n--- Main Sonu ---" << std::endl;
    return 0;
}
```

-----

#### Taşınmış Nesne Durumu (Moved-From State) Nedir?

  * **Ne Yapar?** Bir nesnenin "taşınmış durumu", o nesnenin sahip olduğu kaynakların (bellek, dosya işaretçisi gibi) başka bir nesneye devredildiği andaki halidir. Taşınma işlemi bittiğinde, orijinal nesne artık bu kaynaklara sahip değildir.
  
  * **Neden Kullanılır?** Bu durum, özellikle kaynak yöneten sınıflarda (örneğin akıllı işaretçiler) "tekil sahiplik" modelini sürdürmek için kritik öneme sahiptir. Kaynakların kopyalanması yerine taşınmasını sağlayarak bellek israfını ve "çift serbest bırakma" (double free) gibi hataları önler.
  
  * **Nasıl Çalışır?** Taşıma yapıcı veya taşıma atama operatörü içinde, kaynaklarını devreden nesnenin (yani `other` parametresinin) iç işaretçileri veya kaynak göstergeleri `nullptr` gibi güvenli ve boş bir duruma getirilir. Bu sayede, orijinal nesne (artık taşınmış durumda olan nesne) kendi yıkıcısı çağrıldığında geçersiz veya serbest bırakılmış kaynakları tekrar serbest bırakmaya çalışmaz.

  * **Önemli Özellikleri:**
      * **Geçerli Ama Belirsiz (Valid But Unspecified):** C++ standardı, taşınmış bir nesnenin geçerli bir durumda kalacağını garanti eder. Yani onu silebilir veya üzerine yeni bir değer atayabilirsiniz. Ancak, taşındıktan sonraki içeriğinin (örneğin, sayısal değerlerin veya işaretçilerin ne olacağının) garantisi yoktur; genellikle `nullptr` veya "boş" bir duruma ayarlanır.
      * **Güvenli Operasyonlar:** Taşınmış bir nesne üzerinde sadece yıkıcı (`~`), atama operatörleri (`=`) veya ona yeni bir değer/kaynak atayacak operasyonlar (örneğin, `swap` veya yeniden atama) güvenle çağrılabilir.

**⚠️ DİKKAT:**
Taşınmış duruma geçen bir nesnenin önceki kaynaklarını (örneğin, `data_` işaretçisiyle gösterilen belleği) okumaya çalışmak veya üzerinde önceki içeriğiyle ilgili başka işlemler yapmak **tanımsız davranışa (Undefined Behavior)** yol açabilir. Çünkü nesnenin içeriği artık öngörülemez bir durumdadır. Kendi taşıma yapıcılarınızı yazarken, kaynaklarını devrettiğiniz nesneyi mutlaka geçerli ve güvenli bir "boş" duruma bırakmalısınız (genellikle işaretçileri `nullptr` yapmak gibi).

-----

01.25'de kaldım