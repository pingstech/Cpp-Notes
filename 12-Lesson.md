# 12. Ders:

## Class

### Bellek Yönetimi: `new` ve `delete`

C++'ta program çalışırken bellek ayırıp serbest bırakmaya **dinamik bellek yönetimi** deriz. Bunu `new` ve `delete` anahtar kelimeleriyle yaparız.

  * **`new`:** Bellekte yer açar.
  * **`delete`:** `new` ile açılan yeri geri verir (belleği serbest bırakır).
  * **Önemli:** `new` ve `delete`'i doğru kullanmak, **bellek sızıntılarını** (kullanılmayan belleğin boşaltılmaması) engeller.

#### Tek Nesne İçin

```cpp
int* tek_sayi_ptr = new int(42);    // Bir int için yer aç ve 42 ata
// ... kullan
delete tek_sayi_ptr;                // Belleği serbest bırak
tek_sayi_ptr = nullptr;             // Güvenlik için işaretçiyi sıfırla
```

#### Dizi İçin

```cpp
int* sayi_dizisi_ptr = new int[5];  // 5 elemanlı int dizisi oluştur
// ... kullan
delete[] sayi_dizisi_ptr;           // Dizi belleğini serbest bırak (köşeli parantezlere dikkat!)
sayi_dizisi_ptr = nullptr;          // Güvenlik için işaretçiyi sıfırla
```

#### Dikkat Edilmesi Gerekenler

  * `delete` sonrası işaretçiyi hemen `nullptr` yapın. Bu, aynı belleği tekrar silmeyi (çift serbest bırakma) engeller.
  * `delete nullptr;` güvenlidir, hata vermez.
  * `new` ile alınan belleği **her zaman** `delete` ile silmelisiniz. Silmezseniz bellek sızıntısı olur.
  * **Asla karıştırmayın:** `new` ile ayrılanı `delete[]` ile, `new[]` ile ayrılanı `delete` ile silemezsiniz. Bu, çalışma zamanı hatasına yol açar.

-----

### C++'ta Bildirim ve Özel Üye Fonksiyonları

#### `not declared` (Bildirilmemiş) Hatası

Bir değişkeni, fonksiyonu veya başka bir şeyi kullanmaya kalktığınızda derleyici onu tanımıyorsa bu hatayı alırsınız. Temel kural: Bir şeyi kullanmadan önce **tanıtmalısınız**.

  * **Çözüm:** Kullanacağınız her şeyi (değişkenin türü ve adı, fonksiyonun dönüş türü, adı ve parametreleri gibi) **bildirin**. Ayrıca, kullandığınız yerde erişilebilir (kapsam içinde) olmalı.

<!-- end list -->

```cpp
#include <iostream>

int main() {
    // int benim_degiskenim; // Bu yorum satırı olmasaydı hata olmazdı
    benim_degiskenim = 10; // Hata: 'benim_degiskenim' bildirilmemiş
    // benim_fonksiyonum(); // Hata: 'benim_fonksiyonum' bildirilmemiş
    return 0;
}
```

-----

#### `user declared` (Kullanıcı Tarafından Bildirilmiş) & `user defined` (Kullanıcı Tanımlı)

Bu terimler benzerdir:

  * **`user declared`:**  Sizin kodda tanıttığınız yapılar (değişken, fonksiyon, sınıf vb.).
  * **`user defined`:**  İçeriği yine sizin yazdığınız fonksiyon/sınıflar (özellikle fonksiyon ve sınıfların).

**Özel Durumlar: Kurucular ve Yıkıcılar**

Sınıflar için özel öneme sahip fonksiyonlardır. Nesne oluşturulduğunda veya yok edildiğinde otomatik çalışırlar.

  * **Kullanıcı Tanımlı Kurucu:** Nesnenin başlangıç ayarlarını yapar. Siz yazmazsanız, derleyici otomatik bir tane oluşturur.

  * **Kullanıcı Tanımlı Yıkıcı:** Nesne silindiğinde (ömrü bittiğinde) kaynakları temizler (örneğin `new` ile ayrılmış belleği `delete` eder). Siz yazmazsanız, derleyici otomatik bir tane oluşturur.

<!-- end list -->

```cpp
#include <iostream>
#include <string>

class Ogrenci {
public:
    std::string isim;
    int numara;

    // Kullanıcı tanımlı kurucu: Nesne oluşturulurken çalışır.
    Ogrenci(std::string ogrenci_adi, int ogrenci_numarasi) {
        isim = ogrenci_adi;
        numara = ogrenci_numarasi;
        std::cout << "Ogrenci nesnesi olusturuldu: " << isim << std::endl;
    }

    // Kullanıcı tanımlı yıkıcı: Nesne yok edilirken çalışır.
    ~Ogrenci() {
        std::cout << "Ogrenci nesnesi yok edildi: " << isim << std::endl;
    }
};

int main() {
    Ogrenci ali("Ali", 101); // Constructor çağrılır
    // Program bitince 'ali' nesnesi yok edilir ve yıkıcı çağrılır.
    return 0;
}
```

-----

#### `= default` (Varsayılan Olarak Ayarla)

C++11 ile geldi. Derleyiciye, özel üye fonksiyonlar (kurucular, yıkıcılar, kopyalama/atama operatörleri) için **kendi varsayılan uygulamasını oluşturmasını** açıkça söylemek için kullanılır. Siz özel bir fonksiyon tanımladığınızda derleyici bazen diğer varsayılanları oluşturmayı bırakabilir; `= default` bunu engeller.

  * **Ne Zaman Kullanılır?** Kendi kodunuzu yazmak yerine, derleyicinin sunduğu standart davranışı istediğinizde.

<!-- end list -->

```cpp
#include <iostream>

class VarsayilanSinif {
public:
    int veri;
    
    // Varsayılan (parametresiz) Constructor: Derleyiciye oluşturmasını söyle
    VarsayilanSinif() = default; 

    // Parametreli Constructor
    VarsayilanSinif(int v) : veri(v) { std::cout << "Parametreli Constructor cagridi: " << veri << std::endl; }

    // Kopyalama Constructor ve atama operatörünü varsayılan yap
    VarsayilanSinif(const VarsayilanSinif& diger) = default;
    VarsayilanSinif& operator=(const VarsayilanSinif& diger) = default;
};

int main() {
    VarsayilanSinif nesne_a; // = default Constructor çağrılır
    nesne_a.veri = 5;
    VarsayilanSinif nesne_b = nesne_a; // = default kopyalama Constructor'ı çağrılır
    nesne_b = VarsayilanSinif(10); // = default atama operatörü çağrılır
    return 0;
}
```

-----

#### `= delete` (Silinmiş Olarak Ayarla)

C++11 ile geldi. Bir fonksiyonun (özellikle özel üye fonksiyonların) **kullanımını tamamen yasaklar**. Bu fonksiyonu çağırmaya çalışan kod, derleme hatası alır.

  * **Ne Zaman Kullanılır?** Nesnenizin kopyalanmasını veya atanmasını istemediğinizde (örneğin, tekil (Singleton) desenlerde veya kaynak yönetimi yapan sınıflarda).

<!-- end list -->

```cpp
#include <iostream>

class TekilNesne {
private:
    TekilNesne() { std::cout << "TekilNesne olustu." << std::endl; } // Constructor'ı gizle

    // Kopyalamayı ve atamayı yasakla
    TekilNesne(const TekilNesne&) = delete; 
    TekilNesne& operator=(const TekilNesne&) = delete; 

public:
    static TekilNesne& ornek_getir() {
        static TekilNesne tek_ornek; // Sadece bir kez oluşur
        return tek_ornek;
    }
};

int main() {
    TekilNesne& benim_nesnem = TekilNesne::ornek_getir(); // Tekil nesneye eriş
    // TekilNesne baska_nesne = benim_nesnem; // HATA: Kopyalama yasak!
    // TekilNesne yeni_bir_nesne;             // HATA: Constructor gizli!
    return 0;
}
```

-----

#### `implicitly declared` (Dolaylı Olarak Bildirilmiş)

Bir sınıf tanımladığınızda, C++ derleyicisi sizin için bazı özel üye fonksiyonları (kurucu, yıkıcı, kopyalama/taşıma operatörleri) **otomatik olarak** oluşturabilir. Siz bunları yazmasanız bile derleyici sizin yerinize "dolaylı olarak" var kabul eder.

  * **`implicitly declared default` (Dolaylı Varsayılan):** Siz herhangi bir özel üye fonksiyonu (kurucu, yıkıcı vb.) tanımlamazsanız, derleyici size otomatik olarak varsayılan bir kurucu, kopyalama kurucusu, atama operatörü ve yıkıcı sağlar.

    ```cpp
    #include <iostream>
    #include <string>

    class BasitOgrenci {
    public:
        int id;
        std::string ad;
        // Derleyici otomatik olarak varsayılan Constructor, kopyalama Constructor'ı,
        // atama operatörü ve yıkıcıyı sağlayacak.
    };

    int main() {
        BasitOgrenci ogrenci_1; // Dolaylı varsayılan Constructor
        ogrenci_1.id = 1;
        ogrenci_1.ad = "Ayse";

        BasitOgrenci ogrenci_2 = ogrenci_1; // Dolaylı varsayılan kopyalama Constructor'ı
        // ogrenci_1'deki veriler ogrenci_2'ye kopyalanır.
        return 0;
    }
    ```

  * **`implicitly declared delete` (Dolaylı Silinmiş):** Bazı durumlarda, siz bir özel üye fonksiyonu tanımladığınızda veya sınıfınızda belirli koşullar varsa, derleyici diğer bazı özel üye fonksiyonlarını **otomatik olarak yasaklar (`= delete` gibi)**. Bu, sınıfın mantıksal tutarlılığını korumak içindir.

      * **Örnek:** Eğer sınıfınızda `const` bir veri üyesi varsa, derleyici varsayılan atama operatörünü otomatik olarak yasaklar. Çünkü `const` (sabit) bir üyeye atama yapılamaz.

    <!-- end list -->

    ```cpp
    #include <iostream>

    class SabitVeriSinif {
    public:
        const int sabit_deger; // Sabit bir üye

        SabitVeriSinif(int v) : sabit_deger(v) {} // Constructor

        // Bu sınıfta 'sabit_deger' const olduğu için,
        // derleyici otomatik atama operatörünü 'silinmiş' yapar.
        // Yani `SabitVeriSinif& operator=(const SabitVeriSinif&)` çağrılamaz.
    };

    int main() {
        SabitVeriSinif nesne_a(5);
        SabitVeriSinif nesne_b(10);
        // nesne_a = nesne_b; // HATA: Atama operatörü dolaylı olarak silinmiş!
        return 0;
    }
    ```

-----

### Özel Üye Fonksiyonları

C++’ta derleyici veya geliştirici tarafından sağlanan altı özel üye fonksiyon vardır. Bunlar nesne yaşam döngüsünü ve kaynak yönetimini kontrol eder.

| Fonksiyon            | Söz Dizimi                                 | Ne Zaman Çağrılır?                                       |
| -------------------- | ------------------------------------------ | -------------------------------------------------------- |
| **Constructor**      | `class_name(args);`                        | Nesne yaratılırken                                       |
| **Destructor**       | `~class_name();`                           | Nesne ömrü bittiğinde                                    |
| **Copy Constructor** | `class_name(const class_name& other);`     | Başlatma, değer geçişi, döndürülen nesne                 |
| **Copy Assignment**  | `class_name& operator=(other);`            | Var olan iki nesne arasında atama                        |
| **Move Constructor** | `class_name(class_name&& other) noexcept;` | Geçici nesne oluşturulurken, `std::move` kullanıldığında |
| **Move Assignment**  | `class_name& operator=(other&&) noexcept;` | Var olan nesneye geçici nesne atandığında                |

#### Constructor

```cpp
class student 
{
    public:
        // Varsayılan yapıcı: Üye değişkenlerine boş string ve 0 değeri atar.
        student() : name_str(""), age_int(0) {}

        // Yaş parametresi ile yapıcı: Sadece yaş bilgisiyle öğrenci nesnesi oluşturur, adı boş bırakır.
        explicit student(int age_int) : name_str(""), age_int(age_int) {}

        // Ad ve yaş parametreleri ile yapıcı: Öğrenci nesnesini verilen ad ve yaş ile oluşturur.
        student(const std::string& name_str, int age_int) : name_str(name_str), age_int(age_int) {}

    private:
        std::string name_str;
        int age_int;
};
```

#### Destructor

```cpp
class buffer_class 
{
    int* data_ptr;  // Dinamik olarak ayrılacak tam sayı dizisinin işaretçisi

    public:
        // Constructor: Belirtilen boyutta dinamik bir dizi oluşturur.
        buffer_class(int size_int) : data_ptr(new int[size_int]) {}

        // Destructor: Dinamik belleği serbest bırakır ve işaretçiyi sıfırlar.
        ~buffer_class() 
        {
            delete[] data_ptr;      // Dinamik belleği serbest bırak
            data_ptr = nullptr;     // İşaretçiyi sıfırla
        }
};
```
#### Copy Constructor (Kopyalama Kurucusu)

Bir sınıfın **aynı türden başka bir nesnesini parametre olarak alan** bir kurucu fonksiyondur.

```cpp
class my_class
{
    public: 
        my_class()  // Default Constructor
        {
            std::cout << "Default ctor called here, this : " << this << '\n';
        }

        my_class(const my_class& ref)   // Copy Constructor
        {
            std::cout << "Copy Constructor called here, this : " << this << '\n';
            std::cout << "&ref in Copy Constructor : " << &ref << '\n';
        }

        ~my_class()  // Destructor
        {
            std::cout << "Destructor called here, this : " << this << '\n';
        }
};

void foo(my_class)  
{

}

int main()
{
    std::cout << " ============>| Main Start |<============\n\n";
    
    my_class _my_class;
    std::cout << "&_my_class : " << &_my_class << '\n';

    foo(_my_class); // Başka bir sınıf nesnesin parametre olarak oluşturulduğu için burada "Copy Consturctor" gerçekleşir. Bu yüzden çıktımızı aldığımızda sadece 1 tane "default ctor" görürüz.

    std::cout << "\n ============>| Main End |<============\n\n";
}

/*
Output:

 ============>| Main Start |<============

Default ctor called here, this : 0x7ffc126b8a76
&_my_class : 0x7ffc126b8a76
Copy Constructor called here, this : 0x7ffc126b8a77
&ref in Copy Constructor : 0x7ffc126b8a76
Destructor called here, this : 0x7ffc126b8a77

 ============>| Main End |<============

Destructor called here, this : 0x7ffc126b8a76
*/
```

##### Copy Constructor'ın çağırıldığı durumlar

- **Nesne Başlangıç Değeriyle Oluşturulduğunda:** Bir nesne, başka bir mevcut nesne kullanılarak ilk değer ataması ile oluşturulduğunda.

```cpp
MyClass obj_1;
MyClass obj_2 = obj_1; // Kopyalama Constructor'ı çağrılır.
MyClass obj_3(obj_1);  // Kopyalama Constructor'ı çağrılır.
```

- **Fonksiyona Nesne Değer Olarak Geçirildiğinde (Pass by Value):** Bir fonksiyona nesne, referans yerine değer olarak geçirildiğinde.

```cpp
void process_object(MyClass passed_object) {
    // passed_object oluşturulurken kopyalama Constructor'ı çağrılır.
}

MyClass original_object;
process_object(original_object);
```

- **Fonksiyondan Nesne Değer Olarak Döndürüldüğünde (Return by Value):** Bir fonksiyon, yerel bir nesneyi değer olarak döndürdüğünde (modern C++ derleyicilerinde RVO/NRVO optimizasyonları ile bu durum bazen atlanabilir, ancak kavramsal olarak kopyalama burada gerçekleşir).

```cpp
MyClass create_object() {
    MyClass temp_object;
    // ...
    return temp_object; // temp_object döndürülürken kopyalama Constructor'ı çağrılabilir.
}

MyClass new_object = create_object();
```

**Örnek**

```cpp
class my_class
{
    //...
}

int main()
{
    my_class x;         // Default ctor çağırılır.
    my_class c1 = x;    // Copy Constructor çağırılır.
    my_class c2(x);     // Copy Constructor çağırılır.
    my_class c3{x};     // Copy Constructor çağırılır.

    auto c4 = x;        // Copy Constructor çağırılır.
    auto c5(x);         // Copy Constructor çağırılır.
    auto c6{x};         // Copy Constructor çağırılır.
}
```

**NOT:** Derleyicinin örtülü olarak (implicity) bildirdiği, ve tanımladığı herhangi bir **special member function** şu özelliklere sahiptir; sınıfın non-static, inline, public member functionlarıdır!

-----

## Değer Tipi(Value Type)

Bir değer tipi, veriyi doğrudan içeren bir değişkendir. Kopyalandığında veya atandığında, verinin kendisi kopyalanır. Orijinal ve kopya bağımsızdır; birindeki değişiklik diğerini etkilemez.

- **Örnek:** ```int```, ```double```, basit ```struct```'lar.

```cpp
int a = 10;
int b = a; // 'a'nın değeri 'b'ye kopyalanır.
b = 20;    // 'b' değişir, 'a' (10) aynı kalır.
```

-----

## Değer Semantiği (Value Semantics)

Bir sınıfın veya türün, tıpkı değer tipleri gibi davranacak şekilde tasarlanmasıdır. Yani, bir nesne kopyalandığında veya atandığında, tüm kaynakları (bellek, dosyalar vb.) dahil tam ve bağımsız bir kopyası oluşturulur. Özellikle dinamik bellek kullanan sınıflar için bu, derin kopyalama (deep copy) yapılmasını gerektirir

- **Gereklilik:** Kopyalama kurucusu, atama operatörü ve yıkıcının (destructor) doğru implementasyonu

```cpp
class MyString {
public:
    char* data;

    // Kurucu, Kopyalama Kurucusu (derin kopya), Atama Operatörü (derin kopya), Yıkıcı
    // Bunlar MyString'e değer semantiği kazandırır.

    // Örnek Kopyalama Kurucusu:
    MyString(const MyString& other) {
        data = new char[strlen(other.data) + 1];
        strcpy(data, other.data); // Yeni belleğe kopyala
    }
};
```

**Özetle:** **Değer tipi** veriyi doğrudan içeren bir kategoriyken, **değer semantiği** ise bir sınıfın kopyalandığında veya atandığında tamamen bağımsız, eksiksiz bir kopya oluşturmasını sağlayan **tasarım prensibidir.**

-----

## Özel Kelimeler

### **Invariant (Sınıf İnvariantı)**

**İnvariant**, bir sınıfın içindeki verilerin her zaman uyması gereken bir **kuraldır**. Nesne oluşturulduğunda ve kullanıldığında bu kural asla bozulmamalıdır. Sınıfın tüm metotları bu kuralı korumalıdır.

  * **Örnek:** Bir dikdörtgen sınıfında genişlik ve yüksekliğin asla negatif olmaması bir invarianttır.

<!-- end list -->

```cpp
#include <iostream>

class Dikdortgen 
{
    private:
        double genislik_degeri;
        double yukseklik_degeri;

    public:
        // Constructor: Negatif değerleri engeller.
        Dikdortgen(double genislik, double yukseklik) 
        {
            if (genislik < 0 || yukseklik < 0) 
            {
                std::cerr << "Hata: Genislik/yukseklik negatif olamaz!" << std::endl;
                genislik_degeri = 0; // Geçersiz durumda sıfır atar
                yukseklik_degeri = 0;
            }
            else 
            {
                genislik_degeri = genislik;
                yukseklik_degeri = yukseklik;
            }
        }

        // Genişlik ayarlama metodu: Negatif değeri engeller.
        void genislik_ayarla(double yeni_genislik) 
        {
            if (yeni_genislik >= 0) // Sadece pozitif/sıfır değerlere izin ver
            { 
                genislik_degeri = yeni_genislik;
            } else {
                std::cerr << "Hata: Genislik negatif olamaz!" << std::endl;
            }
        }

    // Diğer metotlar (get_genislik, alan_hesapla vb.) da invariant'ı korur.
};

int main() 
{
    Dikdortgen kutu_bir(10.0, 5.0); // Geçerli
    Dikdortgen kutu_iki(-2.0, 8.0); // Hatalı, 0'a ayarlanır

    kutu_bir.genislik_ayarla(-3.0); // Hata verir, genişlik değişmez
    return 0;
}
```

-----

### **Aggregate (Agrega)**

**Agrega**, C++'ta çok basit, sadece veri tutan bir sınıf veya yapıdır. Karmaşık kurucusu, özel/korumalı üyesi, sanal fonksiyonu veya temel sınıfı olmaz. Üyelerini `{}` (küme parantezleri) ile sırayla kolayca başlatabilirsiniz.

  * **Örnek:** Basit bir renk bilgisi yapısı.

<!-- end list -->

```cpp
#include <string>

// Bu bir "agrega" struct'tır.
struct RenkBilgisi 
{
    std::string ad;
    int kirmizi;
    int yesil;
    int mavi;
};

int main() 
{
    // Agrega Başlatma: Üyeleri sırayla süslü parantezlerle atama
    RenkBilgisi kirmizi_renk = {"Kırmızı", 255, 0, 0};
    std::cout << "Renk: " << kirmizi_renk.ad 
              << " RGB: (" << kirmizi_renk.kirmizi << "," 
              << kirmizi_renk.yesil << "," << kirmizi_renk.mavi << ")" << std::endl;

    // Kendi kurucusu olsaydı bu şekilde başlatılamazdı.
}
```

-----

## **Rule of Zero:** “özel üye fonksiyonları yazma” felsefesidir. 

Eğer sınıfınız **ham kaynak (ham işaretçi, dosya tanıtıcısı vb.) yönetmiyorsa**, **destructor, copy/move constructor ve assignment operator yazmayın**, derleyicinin oluşturmasına izin verin.

-----

## Mülakat Sorusu:

* **Soru:** Hangi durumda derleyici bir sınıfın "special member function"unu default eder ve nasıl default eder?

* **Cevap:** Eğer kullanıcı tarafından özel olarak tanımlanmadıysa, derleyici otomatik olarak (dolaylı şekilde) varsayılan constructor, copy constructor, copy assignment operator, destructor ve move fonksiyonlarını default olarak sağlar. Eğer sınıfta özel bir üye fonksiyon tanımlanırsa, ilgili default fonksiyonlar otomatik olarak oluşturulmayabilir. İstenirse ```= default``` ile açıkça default edilmesi sağlanır.
