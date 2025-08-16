# 11. Ders

## Classes (Sınıflar)

### Move-Only Types (Sadece Taşınabilir Tipler)

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

**⚠️ DİKKAT:** Asla ama asla move memberlar **`= delete;` edilmemelidir\!** Çünkü Copy işlemleri bloklanmış olur.

-----

### Temporary Objects (Geçici Nesneler)

Bazen program çalışırken kısa bir süre için var olan, adı olmayan (anonim) ve belirli bir ifadenin sonucunda oluşan nesneler bulunur. Bu nesnelere **"Geçici Nesneler"** denir. Genellikle bir ifade tamamlandıktan hemen sonra yok olurlar.

#### Geçici Nesneler Nasıl Oluşturulur?

  * **Fonksiyonların Geri Dönüş Değerleri:** Bir fonksiyon bir nesne döndürdüğünde, bu nesne genellikle geçici bir nesne olarak oluşturulur.
  * **Tür Dönüşümleri (Casting):** Bir türü başka bir türe dönüştürdüğünüzde, yeni türde geçici bir nesne oluşabilir.
  * **İfadelerdeki Ara Sonuçlar**: Karmaşık ifadelerde, ara işlemlerin sonuçları geçici nesneler olarak depolanabilir.

<!-- end list -->

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

C++'da, bir nesnenin sahip olduğu kaynaklar (örneğin, dinamik bellek, dosya tanıtıcısı gibi) başka bir nesneye "taşındığında", orijinal nesne arkasında özel bir durumda kalır. Bu duruma **"Taşınmış Nesne Durumu" (Moved-From State)** denir. Bu durum, taşıma semantiği (move semantics) ile doğrudan ilgilidir ve kaynak yönetiminde önemlidir.

#### Taşınmış Nesne Durumu Nasıl Oluşturulur?

Taşınmış nesne durumu, bir **taşıma işlemi** (move operation) sonucunda ortaya çıkar. Bu işlemler genellikle şunları içerir:

1.  **Taşıma Yapıcı (Move Constructor):** Bir nesne, başka bir nesnenin kaynaklarını "çalarak" (taşıyarak) oluşturulduğunda.
2.  **Taşıma Atama Operatörü (Move Assignment Operator):** Bir nesne, başka bir nesnenin kaynaklarını "çalarak" (taşıyarak) değer atadığında.
3.  **`std::move` Kullanımı:** `std::move` fonksiyonu, bir nesnenin taşınabilir olduğunu derleyiciye bildirir ve taşıma yapıcı/atama operatörünün çağrılmasına olanak tanır. `std::move` aslında taşıma yapmaz, sadece bir nesneyi "rvalue reference" olarak işaretler.

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

#### Taşınmış Nesne Durumu (Moved-From State) Nedir?

  * **Ne Yapar?** Bir nesnenin "taşınmış durumu", o nesnenin sahip olduğu kaynakların (bellek, dosya işaretçisi gibi) başka bir nesneye devredildiği andaki halidir. Taşınma işlemi bittiğinde, orijinal nesne artık bu kaynaklara sahip değildir.

  * **Neden Kullanılır?** Bu durum, özellikle kaynak yöneten sınıflarda (örneğin akıllı işaretçiler) "tekil sahiplik" modelini sürdürmek için kritik öneme sahiptir. Kaynakların kopyalanması yerine taşınmasını sağlayarak bellek israfını ve "çift serbest bırakma" (double free) gibi hataları önler.

  * **Nasıl Çalışır?** Taşıma yapıcı veya taşıma atama operatörü içinde, kaynaklarını devreden nesnenin (yani `other` parametresinin) iç işaretçileri veya kaynak göstergeleri `nullptr` gibi güvenli ve boş bir duruma getirilir. Bu sayede, orijinal nesne (artık taşınmış durumda olan nesne) kendi yıkıcısı çağrıldığında geçersiz veya serbest bırakılmış kaynakları tekrar serbest bırakmaya çalışmaz.

  * **Önemli Özellikleri:**

      * **Geçerli Ama Belirsiz (Valid But Unspecified):** C++ standardı, taşınmış bir nesnenin geçerli bir durumda kalacağını garanti eder. Yani onu silebilir veya üzerine yeni bir değer atayabilirsiniz. Ancak, taşındıktan sonraki içeriğinin (örneğin, sayısal değerlerin veya işaretçilerin ne olacağının) garantisi yoktur; genellikle `nullptr` veya "boş" bir duruma ayarlanır.
      * **Güvenli Operasyonlar:** Taşınmış bir nesne üzerinde sadece yıkıcı (`~`), atama operatörleri (`=`) veya ona yeni bir değer/kaynak atayacak operasyonlar (örneğin, `swap` veya yeniden atama) güvenle çağrılabilir.

**⚠️ DİKKAT:** Taşınmış duruma geçen bir nesnenin önceki kaynaklarını (örneğin, `data_` işaretçisiyle gösterilen belleği) okumaya çalışmak veya üzerinde önceki içeriğiyle ilgili başka işlemler yapmak **tanımsız davranışa (Undefined Behavior)** yol açabilir. Çünkü nesnenin içeriği artık öngörülemez bir durumdadır. Kendi taşıma yapıcılarınızı yazarken, kaynaklarını devrettiğiniz nesneyi mutlaka geçerli ve güvenli bir "boş" duruma bırakmalısınız (genellikle işaretçileri `nullptr` yapmak gibi).

-----

### Conversion Constructors (Dönüşüm Yapıcıları)

C++'da, bir sınıfın nesnesini, farklı bir veri tipinden (örneğin, bir `int` veya `std::string`) otomatik olarak oluşturmaya olanak tanıyan özel yapıcılara **"Dönüşüm Yapıcısı" (Conversion Constructor)** denir. Bu yapıcılar, belirli durumlarda türler arası otomatik dönüşümlere izin verir.

#### Dönüşüm Yapıcısı Nedir?

Bir dönüşüm yapıcısı, **tek bir parametre alan** (veya ilk parametresi tek bir zorunlu argüman olan ve kalanları varsayılan değerli olan) bir yapıcıdır. Bu tek parametre, yapıcının ait olduğu sınıftan farklı bir türdedir. Derleyici, bu türde bir değerden sınıfın bir nesnesini otomatik olarak oluşturmak için bu yapıcıyı kullanabilir.

**Örnek:**
Aşağıdaki `my_number` sınıfı, bir `int` değerinden otomatik olarak nasıl oluşturulabileceğini gösteriyor.

```cpp
#include <iostream>

class my_number 
{
private:
    int value_; // Sınıfın tuttuğu sayı değeri

public:
    // Dönüşüm Yapıcısı: int türünden my_number nesnesi oluşturur.
    // Tek bir parametre (int) aldığı için dönüşüm yapıcısı olarak görev yapar.
    my_number(int val) : value_(val) 
    {
        std::cout << "Conversion Constructor: my_number(" << value_ << ") created." << std::endl;
    }

    // Normal Yapıcı (opsiyonel, sadece karışıklığı önlemek için ek bilgi)
    my_number() : value_(0) 
    {
        std::cout << "Default Constructor: my_number() created." << std::endl;
    }

    // Değeri yazdıran yardımcı fonksiyon
    void print_value() const { std::cout << "my_number Value: " << value_ << std::endl; }

    // Toplama işlemi (örnek kullanım için)
    my_number add(const my_number& other) const 
    {
        return my_number(this->value_ + other.value_); // Ara sonuç olarak yeni bir my_number döner
    }
};

int main() 
{
    std::cout << "--- Main Başlangıcı ---" << std::endl;

    // Durum 1: Doğrudan Dönüşüm Yapıcısını Çağırma
    // Açıkça int değerinden my_number nesnesi oluşturulur.
    my_number num1(10);
    num1.print_value();

    std::cout << "\n--- Durum 2: Otomatik Dönüşüm (Implicit Conversion) ---" << std::endl;
    // '20' bir int değeridir. Derleyici, my_number'a bir int parametresi alan yapıcı
    // olduğunu fark eder ve otomatik olarak my_number nesnesi oluşturur.
    my_number num2 = 20; // my_number(20) çağrısı otomatik olarak yapılır.
    num2.print_value();

    std::cout << "\n--- Durum 3: Fonksiyon Çağrılarında Otomatik Dönüşüm ---" << std::endl;
    // add fonksiyonu my_number nesnesi bekler.
    // '5' int değeri otomatik olarak my_number(5) şeklinde dönüştürülür.
    num1.add(5).print_value(); // my_number(10).add(my_number(5)) -> my_number(15)

    std::cout << "\n--- Main Sonu ---" << std::endl;
    return 0;
}
```

#### Dönüşüm Yapıcısının Kullanım Amaçları ve Özellikleri:

  * **Otomatik Tip Dönüşümü (Implicit Conversion):** En temel amacı, belirli bir türdeki değerden sınıfın bir nesnesini otomatik olarak oluşturulmasına izin vermektir. Bu, kodu daha kısa ve okunaklı hale getirebilir.
  * **Esneklik:** Fonksiyonlara sınıfın kendi türünden nesne beklerken, farklı ama dönüştürülebilir bir türde argüman geçirilmesine olanak tanır.
  * **Tek Parametre Kuralı:** Bir yapıcı, ancak **tek bir parametre** aldığında (veya ek parametrelerin varsayılan değerleri olduğunda) dönüşüm yapıcısı olarak kabul edilir.
  * **`explicit` Anahtar Kelimesi:** Otomatik dönüşümleri istemediğinizde (`implicit conversion`), dönüşüm yapıcısının önüne `explicit` anahtar kelimesi koyarak bu dönüşümleri engelleyebilirsiniz. Bu durumda sadece doğrudan (`explicit`) oluşturmaya izin verilir.
      * `explicit my_number(int val);` olarak tanımlansaydı, `my_number num2 = 20;` veya `num1.add(5);` satırları derleme hatası verirdi. Yalnızca `my_number num2(20);` veya `num1.add(my_number(5));` gibi açıkça dönüşüm belirtilen kullanımlara izin verilirdi.

**⚠️ DİKKAT:** Otomatik dönüşüm yapıcıları bazen beklenmedik veya istenmeyen dönüşümlere yol açarak kodda mantık hatalarına neden olabilir. Bu yüzden, otomatik dönüşümlerin gerçekten faydalı ve güvenli olduğundan emin değilseniz, `explicit` anahtar kelimesini kullanmak iyi bir programlama pratiğidir.

-----

### Implicit Conversion Sequences (Örtülü Dönüşüm Sekansları)

C++ derleyicisi, belirli durumlarda bir veri tipini başka bir veri tipine sizin açıkça belirtmenize gerek kalmadan otomatik olarak dönüştürebilir. Bu otomatik dönüşümlere **örtülü dönüşüm (implicit conversion)** denir. Ancak, bu dönüşümler rastgele yapılmaz; belirli kurallara ve sekanslara (sıralamalara) uyarlar.

#### Temel Kavramlar:

1.  **Standart Dönüşüm (Standard Conversion):** C++ dilinin kendiliğinden yapabildiği temel tip dönüşümleridir. Bunlar şunları içerir:

      * **Kimlik Dönüşümü (Identity Conversion):** Tipi değiştirmeden yapılan "dönüşüm" (yani aynı tip).
      * **Lvalue-to-Rvalue Dönüşümü:** Bir değişkenin değerini elde etme (örneğin, `int x = 5; int y = x;` burada `x`'in değeri `y`'ye atanmadan önce rvalue'ye dönüşür).
      * **Nitelik Ekleme/Çıkarma (Qualification Conversion):** `const` veya `volatile` gibi niteleyicilerin eklenmesi (örneğin, `int`'ten `const int`'e).
      * **Sayısal Yükseltmeler (Numeric Promotions):** Daha küçük tam sayı tiplerinin (`char`, `short`) `int`'e yükseltilmesi.
      * **Sayısal Dönüşümler:** Farklı sayısal tipler arası dönüşümler (örneğin, `int`'ten `double`'a).

2.  **Kullanıcı Tanımlı Dönüşüm (User-Defined Conversion):** Sizin bir sınıf içinde tanımladığınız özel dönüşümlerdir. İki ana yolu vardır:

      * **Dönüşüm Yapıcısı (Conversion Constructor):** Tek bir parametre alan (veya ilk parametresi zorunlu olup kalanları varsayılan değerli olan) bir yapıcıdır. Bu yapıcı, kendi sınıfını başka bir tipten oluşturur.
      * **Dönüşüm Operatörü (Conversion Operator):** Bir sınıfın nesnesini başka bir tipe dönüştürmek için kullanılan özel bir üye fonksiyondur (örneğin, `operator int() const;` gibi).

Derleyicinin **otomatik olarak (örtülü)** yapabileceği dönüşüm sekanslarının en fazla bir kullanıcı tanımlı dönüşüm içerebileceğini ifade eder. Bu, aşağıdaki iki ana yapıya izin verir:

1.  **`user-defined conversion + standart conversion`**

      * Önce bir kullanıcı tanımlı dönüşüm gerçekleşir (örneğin, bir dönüşüm yapıcısı çağrılır).
      * Ardından, bu dönüşümün sonucunda oluşan tip, hedef tipe bir standart dönüşümle ulaşır.

2.  **`standart conversion + user-defined conversion`**

      * Önce başlangıçtaki tip, bir standart dönüşümle (örneğin, `const` eklenerek veya bir yükseltme yapılarak) başka bir tipe dönüşür.
      * Ardından, bu standart dönüşümün sonucunda oluşan tip, bir kullanıcı tanımlı dönüşüm (örneğin, bir dönüşüm yapıcısı) ile hedef tipe ulaşır.

Bu iki senaryoda da **yalnızca bir adet kullanıcı tanımlı dönüşüme** izin verilir. İki veya daha fazla kullanıcı tanımlı dönüşüm içeren bir zincir, derleyici tarafından otomatik olarak yapılmaz; açıkça belirtilmesi gerekir.

**Örnek Üzerinden İnceleme:**

Şimdi örneğinizdeki kodu bu kurallar ışığında inceleyelim:

```cpp
class A{}; // A sınıfı
class B{
public:
    B();    // Varsayılan yapıcı
    B(A);   // A sınıfından B sınıfına dönüşüm yapıcı (User-Defined Conversion)
};

int main() {
    B bx = A{}; // <-- Burada dönüşüm gerçekleşiyor.
}
```

**`B bx = A{};` Satırında Neler Oluyor?**

1.  **`A{}`:** Bu ifade, bir `A` sınıfı nesnesi (geçici bir nesne) oluşturur. Bu, kaynak tipimizdir.

2.  **`B bx = ...`:** `bx` adlı bir `B` sınıfı nesnesini başlatmaya çalışıyoruz. Bu, hedef tipimizdir.

3.  Derleyici, `A` tipinden `B` tipine bir yol bulmaya çalışır:

      * **Uygulanan Sekans:** **`user-defined conversion + standart conversion`** sekansı burada devreye girer.
        1.  **Kullanıcı Tanımlı Dönüşüm (User-Defined Conversion):** `A` nesnesi, `B` sınıfının `B(A)` dönüşüm yapıcısı aracılığıyla bir `B` nesnesine dönüştürülür. (Yani, `B(A{})` çağrısı otomatik olarak yapılır.) Bu, sekansımızdaki tek kullanıcı tanımlı dönüşümdür.
        2.  **Standart Dönüşüm (Standard Conversion):** `B(A{})` çağrısından dönen geçici `B` nesnesi (bir "prvalue") daha sonra `bx` adlı `B` nesnesini başlatmak için kullanılır. Bu, bir tür "kimlik dönüşümü" veya "prvalue'dan başlatma" gibi bir standart dönüşümdür.

Derleyici, bu tek kullanıcı tanımlı dönüşümü içeren zinciri otomatik olarak yapabildiği için, `B bx = A{};` ifadesi hatasız bir şekilde derlenir ve çalışır.

-----

### Explicit Specifier (Explicit Belirleyicisi)

C++'da bir **dönüşüm yapıcısı** (yani tek parametre alan bir yapıcı), otomatik (örtülü) tür dönüşümlerine izin verir. Ancak bu durum, bazen beklenmedik veya istenmeyen dönüşümlere yol açabilir. İşte bu istenmeyen örtülü dönüşümleri engellemek için **`explicit`** anahtar kelimesi kullanılır. Bir yapıcı `explicit` olarak işaretlendiğinde, yalnızca **açıkça (explicitly)** çağrıldığında kullanılabilir; örtülü dönüşümler için kullanılamaz.

#### `explicit` Nedir?

`explicit` anahtar kelimesi, tek parametre alan bir yapıcıya uygulandığında, o yapıcının **örtülü dönüşümler (implicit conversions)** yapmasını engeller. Bu, derleyicinin belirli bir tipten sınıfın bir nesnesini otomatik olarak oluşturmasına izin vermez; nesneyi oluşturmak için her zaman açık bir çağrı veya açık bir tür dönüşümü (casting) gereklidir.

**Örnek:**
Aşağıdaki `MyItem` sınıfı, `explicit` anahtar kelimesinin bir dönüşüm yapıcısını nasıl etkilediğini gösteriyor:

```cpp
#include <iostream>
#include <string>

class my_item 
{
    private:
        int item_value_; // Öğe değeri
        std::string item_name_; // Öğe adı

    public:
        // Varsayılan yapıcı
        my_item() : item_value_(0), item_name_("Default") { std::cout << "Default Constructor: " << item_name_ << " (" << item_value_ << ")" << std::endl; }

        // Explicit Dönüşüm Yapıcısı: int türünden my_item nesnesi oluşturur.
        // 'explicit' olduğu için, sadece açıkça çağrılabilir, örtülü dönüşümlere izin vermez.
        explicit my_item(int val) : item_value_(val), item_name_("Item from int") { std::cout << "Explicit Constructor: " << item_name_ << " (" << item_value_ << ")" << std::endl; }

        // String'den öğe oluşturan (explicit olmayan) bir yapıcı
        my_item(const std::string& name) : item_value_(0), item_name_(name) { std::cout << "String Constructor: " << item_name_ << std::endl; }

        // Değeri yazdıran yardımcı fonksiyon
        void print_info() const {
            std::cout << "MyItem Info: " << item_name_ << " (Value: " << item_value_ << ")" << std::endl; }

        // Toplama işlemi için fonksiyon - my_item nesnesi bekler
        // Not: my_item objesi beklediği için, int tipindeki argümanları örtülü dönüştürebiliriz.
        // Ancak my_item(int) explicit olduğu için, bu tür örtülü dönüşümler engellenir.
        my_item add_value(const my_item& other) const 
        {
            // Bu satırda, 'explicit' yapıcı kullanıldığı için, 'other.item_value_' doğrudan
            // my_item'a örtülü olarak dönüştürülemez. Bu nedenle toplama işlemi için farklı bir yaklaşım gerekir.
            // Basitlik adına, burada direkt int değerlerini topluyoruz.
            return my_item(this->item_value_ + other.item_value_); // Dönen nesne için yine explicit çağrı gerekir
        }
};

// my_item nesnesi bekleyen bir fonksiyon
void process_item(my_item item) 
{
    std::cout << "Processing item: ";
    item.print_info();
}

int main() 
{
    std::cout << "--- Main Başlangıcı ---" << std::endl;

    // Durum 1: Explicit Yapıcının Doğrudan Çağrılması (Doğru Kullanım)
    // Açıkça int değerinden my_item nesnesi oluşturulur.
    my_item item1(10); // OK: Doğrudan başlatma
    item1.print_info();

    // Durum 2: Örtülü Dönüşümün Engellenmesi (Derleme Hatası)
    // my_item(int) explicit olduğu için bu satır derleme hatası verir.
    // my_item item2 = 20; // HATA: copy-initialization is not allowed for explicit constructors

    // Durum 3: Açık Dönüşüm (Explicit Cast) Kullanarak Oluşturma (Doğru Kullanım)
    // int değerini açıkça my_item tipine dönüştürüyoruz.
    my_item item3 = static_cast<my_item>(30); // OK: Explicit cast
    item3.print_info();

    std::cout << "\n--- Durum 4: Fonksiyon Argümanı Olarak Örtülü Dönüşüm (Derleme Hatası) ---" << std::endl;
    // process_item fonksiyonu my_item beklerken int gönderme.
    // 'explicit' yapıcı olduğu için bu da derleme hatası verir.
    // process_item(40); // HATA: implicit conversion not allowed

    // Durum 5: Fonksiyon Argümanı Olarak Açık Dönüşüm (Doğru Kullanım)
    process_item(my_item(50)); // OK: Doğrudan başlatma (geçici nesne oluşturarak)
    process_item(static_cast<my_item>(60)); // OK: Explicit cast
    
    std::cout << "\n--- Durum 6: String Yapıcının Örtülü Kullanımı (Explicit Değil) ---" << std::endl;
    my_item item_from_string = "Hello"; // OK: String constructor explicit değil
    item_from_string.print_info();

    std::cout << "\n--- Main Sonu ---" << std::endl;
    return 0;
}
```

#### Explicit Constructor'ın Kullanım Amaçları ve Özellikleri:

  * **Ne Yapar?** Tek parametre alan bir yapıcıyı, sadece açıkça çağrılmaya zorlar. Örtülü (implicit) dönüşümleri tamamen engeller.
  * **Neden Kullanılır?**
      * **Belirsizlikleri Önler:** Derleyicinin farklı dönüşüm yolları arasında seçim yapmakta zorlandığı durumlarda belirsizliği ortadan kaldırır.
      * **İstenmeyen Dönüşümleri Engeller:** Bazen otomatik dönüşümler mantıksal hatalara yol açabilir veya kodun okunabilirliğini düşürebilir. `explicit` kullanarak bu tür "kazara" dönüşümlerin önüne geçilir.
      * **Kod Niyetini Açıkça Belirtir:** Bir dönüşümün sadece programcının bilinçli isteğiyle gerçekleşmesini sağlar.
  * **Nasıl Çalışır?** Derleyici, bir `explicit` yapıcıyı aşağıdaki durumlarda kullanamaz:
      * Kopyalama Başlatma (Copy Initialization): `MyItem item2 = 20;`
      * Fonksiyon Argümanı Geçirme: `process_item(40);`
      * Fonksiyon Dönüş Değeri: Bir fonksiyonun `MyItem` döndürmesi beklenirken `int` döndürülmesi.
      * Aynı zamanda, `explicit` anahtar kelimesi sadece tek parametre alan yapıcılar için geçerlidir. Birden fazla parametre alan veya varsayılan yapıcılarda kullanılması anlamsızdır veya hataya yol açabilir.

**⚠️ DİKKAT:** `explicit` sadece **örtülü** dönüşümleri engeller, **açık** dönüşümleri (`static_cast<my_item>(30)` veya `my_item(50)` gibi) engellemez. Bu sayede, gerekli durumlarda tipi dönüştürme esnekliği korunur ancak bu dönüşümün her zaman bilinçli bir eylem olduğu vurgulanır.