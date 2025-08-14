# 12. Ders

**Eğitimin 15. Dersidir.**

### Copy Elision

**Giriş:**
**Copy Elision (Kopya İptali/Elemesi)**, C++ derleyicisinin gereksiz nesne kopyalama veya taşıma işlemlerini tamamen ortadan kaldırarak performansı artıran bir optimizasyondur. Nesne doğrudan nihai varış noktasında ("in-place") inşa edilir. C++17 standardından itibaren belirli Copy Elision senaryoları **zorunlu hale gelmiştir**. Eğitmeninizin bahsettiği durumları aşağıda inceleyelim:

-----

#### Copy Elision'ın Uygulandığı Başlıca Durumlar:

**1) Geçici Nesneleri Fonksiyonlara Parametre Olarak Geçirme (Zorunlu - C++17)**

  * **Açıklama:** Bir fonksiyona parametre olarak doğrudan bir geçici nesne (bir `prvalue`) verildiğinde, derleyici bu geçici nesneyi doğrudan fonksiyonun parametresinin belleğinde inşa edebilir. C++17'den itibaren, bu durum **zorunlu Copy Elision** kapsamındadır. Yani derleyici bunu yapmak zorundadır.

  * **Nasıl Çalışır:** Normalde, bir geçici nesne oluşturulur, sonra fonksiyon parametresine kopyalanır/taşınır. Ancak Copy Elision ile, geçici nesne doğrudan parametrenin alanında oluşturulur ve böylece ara kopya/taşıma işlemi atlanır.

  * **Örnek:**

    ```cpp
    #include <iostream>
    #include <string>
    #include <utility> // std::move için

    class my_class {
    private:
        std::string name_; 

    public:
        my_class(const std::string& name = "Default") : name_(name) {
            std::cout << "my_class Constructor: " << name_ << std::endl;
        }
        my_class(const my_class& other) : name_("Copy of " + other.name_) {
            std::cout << "my_class Copy Constructor: " << name_ << " from " << other.name_ << std::endl;
        }
        my_class(my_class&& other) noexcept : name_("Moved from " + other.name_) {
            std::cout << "my_class Move Constructor: " << name_ << " from " << other.name_ << std::endl;
            other.name_ = "Moved-from source";
        }
        ~my_class() {
            std::cout << "my_class Destructor: " << name_ << std::endl;
        }
        void print_name() const {
            std::cout << "  Current Object Name: " << name_ << std::endl;
        }
    };

    // my_class nesnesi bekleyen bir fonksiyon
    void process_item_by_value(my_class item) {
        std::cout << "  - process_item_by_value fonksiyonu çağrıldı." << std::endl;
        item.print_name();
    }

    int main() {
        std::cout << "--- Main Başlangıcı ---" << std::endl;

        std::cout << "\n--- Durum 1: Geçici Nesneleri Fonksiyona Parametre Olarak Geçirme ---" << std::endl;
        // 'my_class("Param_Temp")' bir geçici nesnedir (prvalue).
        // C++17'den itibaren, bu geçici nesne doğrudan 'process_item_by_value' fonksiyonunun
        // 'item' parametresi yerine inşa edilmek ZORUNDADIR.
        process_item_by_value(my_class("Param_Temp"));
        std::cout << "  process_item_by_value çağrısı tamamlandı." << std::endl;

        std::cout << "\n--- Main Sonu ---" << std::endl;
        return 0;
    }
    ```

  * **Beklenen Çıktı (Copy Elision gerçekleştiğinde):**

    ```
    --- Main Başlangıcı ---

    --- Durum 1: Geçici Nesneleri Fonksiyona Parametre Olarak Geçirme ---
    my_class Constructor: Param_Temp
      - process_item_by_value fonksiyonu çağrıldı.
      Current Object Name: Param_Temp
    my_class Destructor: Param_Temp
      process_item_by_value çağrısı tamamlandı.

    --- Main Sonu ---
    ```

    (Gördüğünüz gibi, `Copy Constructor` veya `Move Constructor` çağrısı yoktur, çünkü nesne doğrudan fonksiyonda inşa edilmiştir.)

**2) Fonksiyonlardan Geçici Nesne Döndürme (Zorunlu - C++17)**

  * **Açıklama:** Bir fonksiyon, doğrudan bir geçici nesne (bir `prvalue`) oluşturup bunu döndürdüğünde, bu `prvalue` doğrudan fonksiyonun dönüş değerini alacak değişkene veya ifadeye inşa edilebilir. Bu durum, **Unnamed Return Value Optimization (URVO)** olarak da bilinir ve C++17'den itibaren **zorunlu Copy Elision** kapsamındadır.

  * **Nasıl Çalışır:** Fonksiyon içinde geçici nesne oluşturulmaz, doğrudan dönüş değerinin veya atama yapılacak değişkenin belleğinde oluşturulur.

  * **Örnek:**

    ```cpp
    #include <iostream>
    #include <string>
    #include <utility> 

    // my_class tanımı yukarıdaki ile aynı.

    class my_class { /* ... (Yukarıdaki my_class sınıfının tam tanımı) ... */
    private:
        std::string name_; 

    public:
        my_class(const std::string& name = "Default") : name_(name) {
            std::cout << "my_class Constructor: " << name_ << std::endl;
        }
        my_class(const my_class& other) : name_("Copy of " + other.name_) {
            std::cout << "my_class Copy Constructor: " << name_ << " from " << other.name_ << std::endl;
        }
        my_class(my_class&& other) noexcept : name_("Moved from " + other.name_) {
            std::cout << "my_class Move Constructor: " << name_ << " from " << other.name_ << std::endl;
            other.name_ = "Moved-from source";
        }
        ~my_class() {
            std::cout << "my_class Destructor: " << name_ << std::endl;
        }
        void print_name() const {
            std::cout << "  Current Object Name: " << name_ << std::endl;
        }
    };

    // Fonksiyon: İsimsiz geçici nesne döndürüyor (URVO / Mandatory Copy Elision)
    my_class create_return_temporary_object(int id) {
        std::cout << "  - create_return_temporary_object fonksiyonu çağrıldı." << std::endl;
        // Bu isimsiz geçici nesne doğrudan dönüş değerinin yerine inşa edilir.
        return my_class("Func_Temp_" + std::to_string(id)); 
    }

    int main() {
        std::cout << "--- Main Başlangıcı ---" << std::endl;

        std::cout << "\n--- Durum 2: Fonksiyonlardan Geçici Nesne Döndürme ---" << std::endl;
        // 'result_obj' doğrudan 'create_return_temporary_object' içindeki isimsiz geçici yerine inşa edilir.
        // C++17'den itibaren bu zorunludur.
        my_class result_obj = create_return_temporary_object(20);
        std::cout << "  result_obj oluştu." << std::endl;
        result_obj.print_name();

        std::cout << "\n--- Main Sonu ---" << std::endl;
        return 0;
    }
    ```

  * **Beklenen Çıktı (Copy Elision gerçekleştiğinde):**

    ```
    --- Main Başlangıcı ---

    --- Durum 2: Fonksiyonlardan Geçici Nesne Döndürme ---
      - create_return_temporary_object fonksiyonu çağrıldı.
    my_class Constructor: Func_Temp_20
      result_obj oluştu.
      Current Object Name: Func_Temp_20
    my_class Destructor: Func_Temp_20

    --- Main Sonu ---
    ```

    (Yine, gereksiz `Copy Constructor` veya `Move Constructor` çağrısı yoktur.)

**3) Otomatik Depolama Sınıfına Sahip Bir Nesneyi Döndürme (Optimizasyon - NRVO)**

  * **Açıklama:** Bir fonksiyon içinde adlandırılmış (named) bir yerel nesne oluşturulup bu nesne değer olarak döndürüldüğünde, derleyici bu adlandırılmış nesneyi doğrudan fonksiyonun dönüş değerini alacak değişkenin belleğinde inşa etmeyi seçebilir. Bu durum **Named Return Value Optimization (NRVO)** olarak bilinir ve Copy Elision'ın bir türüdür, ancak C++17'den sonra bile **zorunlu değildir**, sadece bir **optimizasyon tercihidir**.

  * **Nasıl Çalışır:** Derleyici, adlandırılmış yerel nesnenin fiziksel olarak fonksiyonun dönüş değerinin yerine inşa edildiği bir kod üretir.

  * **Örnek:**

    ```cpp
    #include <iostream>
    #include <string>
    #include <utility> 

    // my_class tanımı yukarıdaki ile aynı.

    class my_class { /* ... (Yukarıdaki my_class sınıfının tam tanımı) ... */
    private:
        std::string name_; 

    public:
        my_class(const std::string& name = "Default") : name_(name) {
            std::cout << "my_class Constructor: " << name_ << std::endl;
        }
        my_class(const my_class& other) : name_("Copy of " + other.name_) {
            std::cout << "my_class Copy Constructor: " << name_ << " from " << other.name_ << std::endl;
        }
        my_class(my_class&& other) noexcept : name_("Moved from " + other.name_) {
            std::cout << "my_class Move Constructor: " << name_ << " from " << other.name_ << std::endl;
            other.name_ = "Moved-from source";
        }
        ~my_class() {
            std::cout << "my_class Destructor: " << name_ << std::endl;
        }
        void print_name() const {
            std::cout << "  Current Object Name: " << name_ << std::endl;
        }
    };

    // Fonksiyon: Adlandırılmış yerel nesne döndürüyor (NRVO için senaryo)
    my_class create_named_local_object(const std::string& context) {
        std::cout << "  - create_named_local_object fonksiyonu çağrıldı." << std::endl;
        my_class named_local_obj("Named_Local_" + context); // Adlandırılmış yerel nesne
        // Derleyici burada NRVO'yu uygulamayı seçebilir.
        return named_local_obj;
    }

    int main() {
        std::cout << "--- Main Başlangıcı ---" << std::endl;

        std::cout << "\n--- Durum 3: Otomatik Depolama Sınıfına Sahip Nesneyi Döndürme (NRVO) ---" << std::endl;
        // 'result_nrvo' doğrudan 'named_local_obj' yerine inşa edilebilir (derleyici tercihi).
        my_class result_nrvo = create_named_local_object("Main_Call");
        std::cout << "  result_nrvo oluştu." << std::endl;
        result_nrvo.print_name();

        std::cout << "\n--- Main Sonu ---" << std::endl;
        return 0;
    }
    ```

  * **Beklenen Çıktı (NRVO gerçekleştiğinde):**

    ```
    --- Main Başlangıcı ---

    --- Durum 3: Otomatik Depolama Sınıfına Sahip Nesneyi Döndürme (NRVO) ---
      - create_named_local_object fonksiyonu çağrıldı.
    my_class Constructor: Named_Local_Main_Call
      result_nrvo oluştu.
      Current Object Name: Named_Local_Main_Call
    my_class Destructor: Named_Local_Main_Call

    --- Main Sonu ---
    ```

    (Yine, NRVO gerçekleştiği için ek `Copy Constructor` veya `Move Constructor` çağrısı yoktur.)

-----

**Özet:**
Copy Elision, performans için kritik bir optimizasyondur. İlk iki durum (geçici nesnelerin parametre olarak geçirilmesi ve fonksiyonlardan geçici nesne döndürülmesi) C++17'den itibaren **zorunlu hale gelmiştir**. Bu, kodunuzun bu optimizasyonlardan her zaman faydalanacağını garanti eder. Üçüncü durum (adlandırılmış yerel nesnelerin döndürülmesi olan NRVO) ise hala bir **optimizasyon tercihidir**, ancak modern derleyiciler genellikle bunu da yapmaya meyillidir.


## Temporary Materialization

**Giriş:**
C++'da rvalue'ler (geçici değerler) normalde bellekte kalıcı bir konuma sahip değildir ve doğrudan adresleri alınamaz. Ancak, belirli durumlarda, özellikle C++17 ile birlikte, bir rvalue'nin bir `prvalue`'nin (pure rvalue) geçici bir nesneye dönüştürülerek bellekte bir konuma sahip olması sağlanır. Bu sürece **"Geçici Malzemeleştirme" (Temporary Materialization)** denir.

-----

#### Temporary Materialization Nedir?

**Geçici Malzemeleştirme**, C++'daki bir `prvalue`'nin (örneğin, bir fonksiyon dönüş değeri veya bir aritmetik işlem sonucu gibi geçici, isimsiz bir değer) bir `xvalue`'ye veya bir `glvalue`'ye (bellek konumu olan bir değere) dönüştürülmesi işlemidir. Bu dönüşüm sırasında, derleyici bu `prvalue` için otomatik olarak bir geçici nesne oluşturur ve bu nesneye bir bellek adresi verir.

**Neden Gerekli?**
Normalde, `prvalue`'lerin adresi alınamaz. Ancak bazı durumlarda, örneğin bir `const` lvalue referansına bağlanırken veya bir `rvalue`'nin üye fonksiyonuna erişirken, derleyiciye bu geçici değer için bir bellek konumu oluşturması gerekir. Geçici malzemeleştirme bu ihtiyacı karşılar.

-----

**Örnek Kod:**

```cpp
class my_class
{
    public:
        int id_; // Nesnenin kimliğini tutan değer

        // Yapıcı
        my_class(int id = 0) : id_(id) { std::cout << "my_class Constructor (" << id_ << ")" << std::endl; }

        // Yıkıcı
        ~my_class() { std::cout << "my_class Destructor (" << id_ << ")" << std::endl; }

        // Bir değeri döndüren fonksiyon (rvalue/prvalue döndürür)
        my_class create_my_class_prvalue(int val)
        {
            std::cout << "  - create_my_class_prvalue cagrildi." << std::endl;
            return my_class(val); // Bir prvalue döner
        }

        // const üye fonksiyon: Bu fonksiyonu çağırmak için nesnenin bir adresi olmalı.
        // prvalue üzerinden çağrıldığında Temporary Materialization tetiklenir.
        void print_id() const { std::cout << "  my_class ID: " << id_ << std::endl; }
};

// const lvalue referansı alan fonksiyon
void take_const_lvalue_ref(const my_class& obj) { std::cout << "  take_const_lvalue_ref cagrildi. Nesne ID: " << obj.id_ << std::endl; }

int main()
{
    std::cout << "--- Main Baslangici ---" << std::endl;

    // Durum 1: const Lvalue Referansa Bağlama (Geçici Malzemeleştirme C++98'den beri var)
    std::cout << "\n--- Durum 1: const Lvalue Referansa Baglama ---" << std::endl;
    // 'my_class(10)' bir prvalue'dir. 'const my_class&'e atanırken malzemeleştirilir.
    const my_class& ref_to_temp = my_class(10);
    ref_to_temp.print_id(); // Malzemeleştirilmiş geçici nesneyi kullanırız
    // ref_to_temp'in ömrü main fonksiyonu sonuna kadar uzar (C++ kuralı).

    // Durum 2: prvalue üzerinden üye fonksiyon çağırma (C++17 ile genişledi)
    std::cout << "\n--- Durum 2: prvalue uzerinden Uye Fonksiyon Cagirma ---" << std::endl;
    // 'my_class().create_my_class_prvalue(20)' bir prvalue döndürür.
    // '.print_id()' çağrısı için bu prvalue malzemeleştirilir.
    my_class().create_my_class_prvalue(20).print_id();

    // Durum 3: Fonksiyona prvalue geçirme (C++98'den beri var)
    std::cout << "\n--- Durum 3: Fonksiyona prvalue gecirme ---" << std::endl;
    // 'my_class(30)' bir prvalue'dir. 'take_const_lvalue_ref'in const referans parametresine
    // geçerken malzemeleştirilir.
    take_const_lvalue_ref(my_class(30));


    std::cout << "\n--- Main Sonu ---" << std::endl;
    // Durum 1'deki 'ref_to_temp' nesnesi burada yok edilir.
    return 0;
}
```

#### Temporary Materialization'ın Kullanım Amaçları ve Özellikleri:

  * **Ne Yapar?** Bir `prvalue`'yi (geçici, isimsiz bir değeri) bellekte fiziksel bir konuma sahip olan geçici bir nesneye dönüştürür.
  * **Neden Kullanılır?**
      * **Referans Bağlama:** `prvalue`'lerin `const` lvalue referanslarına ( `const T&` ) veya rvalue referanslarına (`T&&`) bağlanabilmesi için gereklidir. Referanslar, bir nesnenin bellekteki adresine ihtiyaç duyar.
      * **Üye Fonksiyon Erişimi (C++17):** C++17'den önce, `prvalue`'ler üzerinden doğrudan üye fonksiyonlar (`.member_func()`) çağrılamazdı (ancak `xvalue`'ler çağırabilirdi). C++17 ile birlikte, bir `prvalue` üzerinden üye fonksiyon çağrıldığında, `prvalue` otomatik olarak malzemeleştirilir ve geçici nesne üzerinden fonksiyon çağrılır. Bu, zincirleme fonksiyon çağrılarını daha esnek hale getirir.
      * **Puanlayıcıların Geçici Nesnelere İşaret Etmesi:** Bir `prvalue`'den bir işaretçi oluşturulması veya bir `void*`'a dönüşüm gerektiğinde de malzemeleştirme gerçekleşir.
  * **Nasıl Çalışır?** Derleyici, ihtiyaç duyulduğunda `prvalue`'yi depolamak için bellekte bir alan ayırır ve bu alanı temsil eden bir geçici nesne oluşturur. Bu geçici nesne, genellikle onu oluşturan ifadenin sonuna veya bağlı olduğu referansın ömrü boyunca yaşar.
  * **Otomatiktir:** Bu süreç tamamen derleyici tarafından otomatik olarak yönetilir ve programcı tarafından açıkça talep edilmez.

**⚠️ DİKKAT:**
Geçici malzemeleştirme, performans maliyeti olmayan bir işlem değildir. Her ne kadar derleyici optimizasyonları (örneğin Copy Elision) bu maliyeti azaltabilse de, gereksiz geçici nesne oluşturmaktan kaçınmak genellikle iyi bir programlama pratiğidir. Özellikle büyük veya karmaşık nesneler için.

-----

### Return Value Optimization (RVO) ve Named Return Value Optimization (NRVO)

**Giriş:**
C++'da fonksiyonlardan nesne döndürülürken oluşabilecek gereksiz kopya veya taşıma işlemlerini önlemek için derleyicilerin uyguladığı kritik optimizasyonlara **Return Value Optimization (RVO)** denir. RVO'nun bir alt kümesi veya özel bir hali olan **Named Return Value Optimization (NRVO)** ise, özellikle adlandırılmış yerel nesnelerin döndürüldüğü senaryolarda devreye girer.

-----

#### Return Value Optimization (RVO)

**Tanım:**
**Return Value Optimization (RVO)**, C++ derleyicisinin bir fonksiyondan değer olarak döndürülen bir nesnenin **gereksiz kopya veya taşıma yapıcısı çağrılarını atlayarak**, doğrudan hedef bellek konumunda inşa edilmesini sağlayan genel bir optimizasyon tekniğidir. Bu, nesnenin önce fonksiyon içinde oluşturulup sonra kopyalanması veya taşınması yerine, tek bir kez ve doğrudan nihai adresinde oluşturulması anlamına gelir.

**Amacı ve Faydaları:**

  * **Performans Artışı:** Büyük ve karmaşık nesnelerin kopyalanmasının veya taşınmasının getirdiği zaman ve bellek maliyetini sıfırlar.
  * **Kaynak Yönetimi:** Kaynak yöneten sınıflar (örneğin akıllı işaretçiler, dosya tanıtıcıları) için hata potansiyelini (çift serbest bırakma gibi) azaltır.
  * **Genel Bir Kural:** Hem adlandırılmış (`NRVO`) hem de isimsiz (`URVO`/`Copy Elision`) dönüş senaryolarını kapsayan daha geniş bir terimdir.

**Nasıl Çalışır (Genel Prensip):**
Derleyici, bir fonksiyonun bir nesneyi değer olarak döndürdüğünü fark ettiğinde, bu nesnenin önce fonksiyon içinde oluşturulup sonra dışarıdaki değişkene kopyalanması/taşınması yerine, doğrudan o dışarıdaki değişkenin belleğinde oluşturulmasına karar verebilir. Böylece, ara kopyalar ortadan kalkar.

**Örnek Senaryo (Unnamed Return Value Optimization / Copy Elision):**
Bu, RVO'nun en yaygın türüdür ve C++17'den itibaren belirli durumlarda **zorunlu hale gelmiştir**.

```cpp
#include <iostream>
#include <string>
#include <utility> // std::move için

class my_class 
{
private:
    std::string name_; // Nesne adı

public:
    // Yapıcı (Constructor)
    my_class(const std::string& name = "Default") : name_(name) {
        std::cout << "my_class Constructor: " << name_ << std::endl;
    }

    // Kopya Yapıcı (Copy Constructor)
    my_class(const my_class& other) : name_("Copy of " + other.name_) {
        std::cout << "my_class Copy Constructor: " << name_ << " from " << other.name_ << std::endl;
    }

    // Taşıma Yapıcı (Move Constructor) (C++11)
    my_class(my_class&& other) noexcept : name_("Moved from " + other.name_) {
        std::cout << "my_class Move Constructor: " << name_ << " from " << other.name_ << std::endl;
        other.name_ = "Moved-from source"; // Kaynağı güvenli duruma getir
    }

    // Yıkıcı (Destructor)
    ~my_class() {
        std::cout << "my_class Destructor: " << name_ << std::endl;
    }

    // Fonksiyon: Değer döndüren (RVO burada etki edebilir)
    // Named Return Value Optimization (NRVO) için örnek.
    my_class create_my_class_by_value(const std::string& func_name) {
        std::cout << "  - create_my_class_by_value fonksiyonu çağrıldı." << std::endl;
        my_class temp_obj("Temp_" + func_name); // Yerel adlandırılmış nesne
        // Derleyici burada RVO yapabilir: temp_obj doğrudan dışarıdaki hedefe inşa edilir.
        return temp_obj;
    }

    // Fonksiyon: Değer döndüren (RVO/Copy Elision burada etki edebilir)
    // Unnamed Return Value Optimization (URVO) / Copy Elision için örnek.
    my_class create_unnamed_my_class() {
        std::cout << "  - create_unnamed_my_class fonksiyonu çağrıldı." << std::endl;
        // Derleyici burada RVO yapabilir: geçici nesne doğrudan dışarıdaki hedefe inşa edilir.
        return my_class("Unnamed_Temp"); // İsimsiz geçici nesne
    }
};

my_class create_unnamed_object() {
    std::cout << "  - create_unnamed_object fonksiyonu çağrıldı." << std::endl;
    return my_class("Unnamed_Temp_Global"); // İsimsiz (geçici) bir nesne döndürülüyor.
}

int main() {
    std::cout << "--- Main Başlangıcı ---" << std::endl;

    std::cout << "\n--- URVO/Copy Elision Potansiyeli ---" << std::endl;
    // 'obj_urvo' doğrudan 'Unnamed_Temp_Global' yerine inşa edilir.
    // C++17'den itibaren bu durumda copy elision zorunludur, bu nedenle tek bir Constructor çağrısı görmelisiniz.
    my_class obj_urvo = create_unnamed_object();
    std::cout << "  obj_urvo oluştu." << std::endl;
    
    // Sınıf içindeki metoda çağrı ile URVO/Copy Elision
    std::cout << "\n--- Sınıf İçi Metod ile URVO/Copy Elision Potansiyeli ---" << std::endl;
    my_class temp_my_class_obj; // Geçici bir my_class nesnesi oluşturulur (Default Constructor)
    my_class obj_urvo_from_method = temp_my_class_obj.create_unnamed_my_class();
    std::cout << "  obj_urvo_from_method oluştu." << std::endl;

    std::cout << "\n--- Main Sonu ---" << std::endl;
    return 0;
}
```

-----

#### Named Return Value Optimization (NRVO)

**Tanım:**
**Named Return Value Optimization (NRVO)**, RVO'nun özel bir durumudur ve yalnızca bir fonksiyondan **adlandırılmış bir yerel nesnenin** değer olarak döndürüldüğü senaryolarda uygulanır. Derleyici, fonksiyon içindeki bu adlandırılmış yerel nesnenin, aslında fonksiyonun dönüş değerinin veya atamanın yapılacağı nihai bellek konumunda doğrudan inşa edilmesine karar verir.

**Amacı ve Faydaları:**

  * **Adlandırılmış Nesneler İçin Optimizasyon:** NRVO, `temp_obj` gibi açıkça tanımlanmış yerel değişkenlerin kopyalanmasını/taşınmasını engeller.
  * **Performans ve Kaynak Yönetimi:** Tıpkı genel RVO gibi, gereksiz yapıcı/yıkıcı çağrılarını ortadan kaldırarak performansı artırır ve kaynak yönetimini basitleştirir.

**Nasıl Çalışır (NRVO Özel Durumu):**
Bir fonksiyonda `MyClass temp_obj("Adim"); return temp_obj;` gibi bir yapı gördüğünüzde, derleyici `temp_obj`'nin belleğini, onu çağıran fonksiyonun dönüş değeri veya atanacağı değişken için ayrılmış bellekte tahsis edebilir. Böylece, `temp_obj`'nin bir kopyası (veya taşıması) oluşturulmadan doğrudan dışarıdaki nihai nesne olarak var olur.

**Örnek Senaryo:**

```cpp
#include <iostream>
#include <string>
#include <utility> // std::move için

class my_class 
{
private:
    std::string name_; // Nesne adı

public:
    // Yapıcı (Constructor)
    my_class(const std::string& name = "Default") : name_(name) {
        std::cout << "my_class Constructor: " << name_ << std::endl;
    }

    // Kopya Yapıcı (Copy Constructor)
    my_class(const my_class& other) : name_("Copy of " + other.name_) {
        std::cout << "my_class Copy Constructor: " << name_ << " from " << other.name_ << std::endl;
    }

    // Taşıma Yapıcı (Move Constructor) (C++11)
    my_class(my_class&& other) noexcept : name_("Moved from " + other.name_) {
        std::cout << "my_class Move Constructor: " << name_ << " from " << other.name_ << std::endl;
        other.name_ = "Moved-from source"; // Kaynağı güvenli duruma getir
    }

    // Yıkıcı (Destructor)
    ~my_class() {
        std::cout << "my_class Destructor: " << name_ << std::endl;
    }

    // Fonksiyon: Değer döndüren (RVO burada etki edebilir)
    // Named Return Value Optimization (NRVO) için örnek.
    my_class create_my_class_by_value(const std::string& func_name) {
        std::cout << "  - create_my_class_by_value fonksiyonu çağrıldı." << std::endl;
        my_class temp_obj("Temp_" + func_name); // Yerel adlandırılmış nesne
        // Derleyici burada RVO yapabilir: temp_obj doğrudan dışarıdaki hedefe inşa edilir.
        return temp_obj;
    }

    // Fonksiyon: Değer döndüren (RVO/Copy Elision burada etki edebilir)
    // Unnamed Return Value Optimization (URVO) / Copy Elision için örnek.
    my_class create_unnamed_my_class() {
        std::cout << "  - create_unnamed_my_class fonksiyonu çağrıldı." << std::endl;
        // Derleyici burada RVO yapabilir: geçici nesne doğrudan dışarıdaki hedefe inşa edilir.
        return my_class("Unnamed_Temp"); // İsimsiz geçici nesne
    }
};

// Fonksiyon: Named Return Value Optimization (NRVO) için örnek.
// 'result_obj' adlandırılmış bir yerel nesnedir.
my_class create_named_object(const std::string& context) {
    std::cout << "  - create_named_object fonksiyonu çağrıldı." << std::endl;
    my_class result_obj("Named_Temp_" + context); // Adlandırılmış yerel nesne
    return result_obj; // Adlandırılmış nesne döndürülüyor.
}

int main() {
    std::cout << "--- Main Başlangıcı ---" << std::endl;

    std::cout << "\n--- NRVO Potansiyeli ---" << std::endl;
    // 'obj_nrvo' doğrudan 'Named_Temp_Main_Call' yerine inşa edilebilir.
    // Eğer NRVO gerçekleşirse, 'Named_Temp_Main_Call' için yalnızca bir Constructor çağrısı görürsünüz.
    // Kopya veya Taşıma Yapıcısı çağrılmaz.
    my_class obj_nrvo = create_named_object("Main_Call");
    std::cout << "  obj_nrvo oluştu." << std::endl;

    std::cout << "\n--- Main Sonu ---" << std::endl;
    return 0;
}
```

-----

**RVO ve NRVO Arasındaki Temel Farklar:**

| Özellik        | Return Value Optimization (RVO) (Genel)        | Named Return Value Optimization (NRVO) (Özel)   |
| :------------- | :----------------------------------------------- | :------------------------------------------------ |
| **Kapsam** | Fonksiyon dönüş değeri optimizasyonlarının genel terimi. Hem isimsiz (prvalue) hem de adlandırılmış (lvalue) nesneleri kapsar. | RVO'nun, **adlandırılmış bir yerel nesnenin** döndürüldüğü özel durumudur. |
| **Kullanım** | `return MyClass(arg);` (isimsiz geçici)         | `MyClass temp_obj; return temp_obj;` (adlandırılmış yerel) |
| **Garantisi (C++17)** | İsimsiz geçiciler için Copy Elision **zorunludur**. | Adlandırılmış yerel nesneler için hala bir **optimizasyon tercihidir** (zorunlu değildir, ancak genellikle yapılır). |
| **Gözlem** | `Copy Constructor` ve `Move Constructor` çağrılarını gözlemleyerek anlaşılır. Görülmezlerse optimizasyon gerçekleşmiştir. | Aynı şekilde `Copy Constructor` ve `Move Constructor` çağrılarının olmamasıyla anlaşılır. |

**Özetle:** RVO, derleyicinin kopya/taşıma işlemlerini ortadan kaldırmak için kullandığı daha geniş bir optimizasyon kategorisidir. NRVO ise, bu genel kategorinin, fonksiyon içindeki belirli bir adlandırılmış yerel değişkenin döndürüldüğü durumlar için geçerli olan özel bir türüdür. Her ikisi de performans iyileştirmeyi ve gereksiz nesne oluşturma/yok etme maliyetini azaltmayı hedefler.