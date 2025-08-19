# 13. Ders


## Sınıfların Statik Veri Elemanları

Bir sınıfın statik veri elemanı (`static data member`), o sınıfa ait tüm nesneler tarafından paylaşılan tek bir değişkendir. Bir statik veri elemanının tek bir kopyası bulunur ve bu kopya, sınıfın hiçbir nesnesi oluşturulmasa bile bellekte mevcuttur.

-----

### Statik Veri Elemanlarının Özellikleri

  * **Ortak Kullanım:** Bir statik değişken, sınıfa ait tüm nesnelerin ortak bir kaynağıdır. Bir nesne bu değişkenin değerini değiştirdiğinde, diğer tüm nesneler de bu değişikliği görür.
  * **Nesneden Bağımsızlık:** Statik elemanlar, bir sınıf nesnesi (object) oluşturulmasına gerek kalmadan kullanılabilir. Sınıf adıyla (`ClassName::static_member_name`) veya herhangi bir nesne üzerinden (`object_name.static_member_name`) erişilebilirler.
  * **Bellekte Yaşam Süresi:** Statik veri elemanları, programın başlangıcından sonuna kadar bellekte varlıklarını sürdürürler. Global değişkenler gibi, program sonlandığında bellekten silinirler.
  * **Tanımlama ve Başlatma:** Statik veri elemanları sınıfın içinde sadece **bildirilir** (`declaration`), ancak bellekte yer ayrılması ve ilk değerin atanması (`definition` ve `initialization`) sınıfın dışında, genellikle bir `.cpp` dosyasında yapılır.

<!-- end list -->

```cpp
// -------------------------------------------------------------
// static_member.cpp
// -------------------------------------------------------------

#include <iostream>

class my_class {
public:
    // Statik veri elemanının bildirimi (declaration)
    // Bu, her nesne için değil, sınıfın tamamı için tek bir 'counter' olduğunu söyler.
    static int object_counter_;

    my_class() {
        // Her nesne oluşturulduğunda sayacı artırırız.
        object_counter_++;
        std::cout << "Nesne oluşturuldu. Toplam nesne sayısı: " << object_counter_ << std::endl;
    }

    ~my_class() {
        // Her nesne yok edildiğinde sayacı azaltırız.
        object_counter_--;
        std::cout << "Nesne yok edildi. Kalan nesne sayısı: " << object_counter_ << std::endl;
    }
};

// Statik veri elemanının sınıf dışında tanımlanması ve başlatılması (definition and initialization)
// Tek bir 'object_counter_' kopyası oluşturulur ve 0 olarak başlatılır.
int my_class::object_counter_ = 0;

int main() {
    // Statik elemana sınıf adıyla erişim
    std::cout << "Başlangıçta nesne sayısı: " << my_class::object_counter_ << std::endl;

    my_class nesne_bir;    // Kurucu çağrılır, object_counter_ 1 olur.
    my_class nesne_iki;    // Kurucu çağrılır, object_counter_ 2 olur.
    {
        my_class nesne_uc; // Kurucu çağrılır, object_counter_ 3 olur.
    } // Nesne_uc yok edilir, object_counter_ 2 olur.

    std::cout << "Şu anki nesne sayısı: " << my_class::object_counter_ << std::endl;

    return 0;
}
// -------------------------------------------------------------
```

### Statik Veri Elemanları Neden Kullanılır?

  * **Nesne Sayısını Takip Etme:** Yukarıdaki örnekte görüldüğü gibi, bir sınıftan kaç nesne oluşturulduğunu takip etmek için idealdir.
  * **Sabit Değerleri Paylaşma:** Tüm nesnelerin kullanacağı sabit bir değeri (örn. bir fizik sabiti) saklamak için kullanılabilir.
  * **Kaynaktan Bağımsız Veri:** Bir nesneye ait olmayan, ancak sınıfın bir parçası olan verileri saklamak için kullanılır. Örneğin, tüm nesnelerin ortak bir ayar dosyasına veya veritabanı bağlantısına erişmesi gerektiğinde.
  * **Singleton Tasarım Kalıbı:** Yalnızca bir nesnenin oluşturulmasını garanti eden Singleton tasarım kalıbının temelinde statik veri elemanları bulunur.

### Sınıfların Statik Veri Elemanlarının Tanımı ve İstisnai Durumlar

Bir sınıfın statik veri elemanının tanımı, o elemana bellek tahsis etme ve ilk değerini atama işlemidir. Bu işlem, genellikle sınıfın bildirildiği başlık dosyasının (`.h`) dışında, bir kaynak kod dosyasında (`.cpp`) yapılır.

-----

#### Tanım (Definition) Nasıl Yapılır?

Statik veri elemanının tanımı aşağıdaki genel formata uyar:

`veri_tipi sınıf_adı::statik_veri_elemanı_adı = başlangıç_değeri;`

  * **`veri_tipi`:** Statik elemanın veri tipidir.
  * **`sınıf_adı::`:** Bu kısım, tanımlanan statik elemanın hangi sınıfa ait olduğunu belirtir. Bu, elemanın sınıfın kapsamına (`scope`) ait olduğunu gösterir.
  * **`statik_veri_elemanı_adı`:** Sınıf içinde `static` anahtar kelimesiyle bildirilmiş olan veri elemanının adıdır.
  * **`= başlangıç_değeri`:** İsteğe bağlıdır. Eğer bir başlangıç değeri atanmazsa, statik eleman varsayılan olarak sıfır ile başlatılır (`zero-initialization`).

<!-- end list -->

```cpp
// -------------------------------------------------------------
// static_definition.cpp
// -------------------------------------------------------------

// my_class.h Dosyası
class my_class {
public:
    static int object_counter_; // Bildirim
    static double total_value_; // Bildirim
};

// my_class.cpp Dosyası
#include "my_class.h"

// object_counter_ için tanım ve başlangıç değeri ataması
int my_class::object_counter_ = 0;

// total_value_ için tanım ve başlangıç değeri ataması
double my_class::total_value_ = 0.0;

int main() {
    // Statik elemanlara erişim ve kullanım
    return 0;
}
// -------------------------------------------------------------
```

-----

#### İstisnai Durumlar (Modern C++ ile Gelenler)

Bazı istisnai durumlarda, statik veri elemanının tanımı doğrudan sınıf içinde yapılabilir. Bu durumlar modern C++ standartlarıyla birlikte (C++11, C++17) geçerlilik kazanmıştır.

##### İstisna 1: `const static int` ve Entegral Tipler

  * **Kural:** Eğer bir statik veri elemanı `const` ise ve tam sayı (`integral`) tiplerden biriyse (örn. `int`, `char`, `bool`), tanımı ve başlatılması doğrudan sınıf içinde yapılabilir.
  * **Neden:** Derleyici, bu tür değerlerin bir sabitten ibaret olduğunu bilir ve bellekte ayrı bir yer ayırmak yerine değeri doğrudan kullanır. Bu sayede linker aşamasında bir tanıma gerek kalmaz.

<!-- end list -->

```cpp
class my_class {
public:
    // Sınıf içinde hem bildirim hem tanım
    const static int max_count = 10;
};
```

##### İstisna 2: C++17 ile Gelen `inline static`

  * **Kural:** C++17 standardı ile birlikte, herhangi bir statik veri elemanı `inline` anahtar kelimesi ile bildirildiğinde, tanımı ve başlatılması doğrudan sınıf içinde yapılabilir.
  * **Neden:** `inline` anahtar kelimesi, birden fazla dosya bu başlık dosyasını dahil ettiğinde bile, linker'ın aynı isimdeki değişkenin birden fazla kopyasını değil, sadece tek bir kopyasını oluşturmasını sağlar. Bu, statik üyelerin başlık dosyasında başlatılmasına olanak tanır ve harici tanım ihtiyacını ortadan kaldırır.

<!-- end list -->

```cpp
// C++17 ve sonrası
class my_class {
public:
    // Sınıf içinde hem bildirim hem tanım
    inline static int object_counter = 0;
};
```

Bu istisnai durumlar, özellikle başlık dosyası tabanlı kütüphaneler yazarken veya basit statik elemanlar için harici tanım karmaşıklığını azaltmak istediğinizde oldukça faydalıdır.

-----

## Tamamlanmış (Complete) ve Tamamlanmamış (Incomplete) Tipler

Bir tipin **tamamlanmış (complete)** olması, derleyicinin o tipin bellekte ne kadar yer kapladığını tam olarak bilmesi demektir. Buna karşılık, bir tipin **tamamlanmamış (incomplete)** olması ise, bellekteki boyutunun henüz belli olmaması anlamına gelir.

-----

### Tamamlanmamış Tiplerin Özellikleri

  * **Boyutları Bilinmez:** Bir tip tamamlanmamışsa, boyutu bilinmediği için bu tipten bir değişken oluşturamaz veya `sizeof` operatörünü kullanamazsınız.
  * **İleri Bildirim (Forward Declaration):** Bu durum genellikle bir sınıf veya yapı için **ileri bildirim** yaptığınızda ortaya çıkar. İleri bildirim, derleyiciye böyle bir tipin var olduğunu söyler ancak detaylarını (üyelerini) henüz açıklamaz.
  * **İşaretçiler ve Referanslar:** Tamamlanmamış tiplere ait **işaretçi** ve **referanslar** oluşturulabilir çünkü bir işaretçi veya referansın boyutu sabittir (genellikle 4 veya 8 byte) ve işaret ettiği tipin boyutuyla doğrudan ilgili değildir.

<!-- end list -->

```cpp
// -------------------------------------------------------------
// incomplete_types.cpp
// -------------------------------------------------------------

#include <iostream>

// 'my_class' için ileri bildirim (incomplete type)
class my_class;

void process_incomplete_type(my_class* my_pointer) 
{
    // Sadece pointer'ı kullanabiliriz.
    // Nesnenin üyelerine erişemeyiz veya boyutunu öğrenemeyiz.
    // sizeof(my_class) burada derleme hatası verir.
    std::cout << "Pointer adresi: " << my_pointer << std::endl;
}

// -------------------------------------------------------------
```

**NOT:** `void` bir incomplete type'dır.
-----

### Tamamlanmış Tiplerin Özellikleri

  * **Boyutları Bilinir:** Bir tipin tüm detayları (üyeleri, metotları vb.) derleyiciye açıklandığında, o tip **tamamlanmış** hale gelir.
  * **Değişken Oluşturma:** Tamamlanmış tiplerden değişkenler (`my_class my_object;`) oluşturulabilir, fonksiyon argümanı olarak değerle geçirilebilir ve `sizeof` operatörü kullanılabilir.

<!-- end list -->

```cpp
// -------------------------------------------------------------
// complete_types.cpp
// -------------------------------------------------------------

#include <iostream>

class my_class {
private:
    int id_;
    double value_;

public:
    my_class(int id, double value) : id_(id), value_(value) {
        std::cout << "Nesne oluşturuldu." << std::endl;
    }
};

void process_complete_type(my_class my_object) 
{
    // my_object artık tamamlanmış bir tip, çünkü my_class'ın
    // boyutu biliniyor.
    std::cout << "sizeof(my_class): " << sizeof(my_class) << std::endl;
}

int main() {
    my_class my_object(1, 10.5);
    process_complete_type(my_object);

    return 0;
}
// -------------------------------------------------------------
```

| Kriter | Tamamlanmamış (Incomplete) Tip | Tamamlanmış (Complete) Tip |
|---|---|---|
| **Boyut** | Bilinmez | Bilinir |
| **Kullanım Alanı**| İşaretçi/referans tanımlama ve fonksiyon prototipleri | Değişken oluşturma, `sizeof` kullanımı ve tam nesne işlemleri |
| **Durum** | Genellikle ileri bildirimden sonra | Tipin tam tanımının derleyiciye sunulmasından sonra |

-----

Dakika 50'de kaldım