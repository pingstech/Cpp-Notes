# Contents of Cpp Notes

```
├── 01-Lesson.md
│   ├── Genel C++ Kavramları, Pointer'lar ve Referanslar
│   │   ├── Bilinmesi Gereken C Konuları
│   │   ├── Referanslar (Reference)
│   │   ├── Dizi Pointer'ları
│   │   └── `a` ve `&a` Arasındaki Fark
│   ├── Semantik ve Sık Geçen Semantik Türleri
│   │   ├── `int a[10];` Dizisinin Fonksiyon Dönüş Tipi
│   │   ├── Semantik: Bir dil öğesinin (değişken, ifade vb.) gerçek davranışını tanımlar.
│   │   ├── C++'ta Sık Geçen Semantik Türleri
│   │   ├── `std::move`: Geçici nesnelerden kaynakları çalmak veya verimli nesne transferi yapmak.
│   │   └── Pointer ve Referans Karşılaştırması
│   ├── `const` Niteleyicisi ve Referansların Davranışı
│   │   ├── Top Level `const` (Üst Seviye `const`)
│   │   ├── Low Level `const` (Alt Seviye `const`)
│   │   ├── Fonksiyonlarda Referans Dönüşü
│   │   ├── Referans Türleri ve Bağlanma
│   │   └── Pointer ve Referans Semantiği
│   ├── Scope Kaçağı, Tip Çıkarımı ve `using`
│   │   ├── Scope Kaçağı (Scope Leakage)
│   │   ├── Değer Kategorileri
│   │   ├── Type Deduction (Tip Çıkarımı)
│   │   ├── `auto` ve `const` İlişkisi
│   │   └── `Using`: `typedef` Alternatifi
│   └── Referans Katlama (Reference Collapsing) ve Universal Referanslar
│       └── Reference Collapsing (Referans Katlama)
│
├── 02-Lesson.md
│   ├── Decltype Operatörü
│   │   ├── İsim Operandı Kuralları
│   │   └── İfade Operandı Kuralları (Value Category)
│   ├── Unevaluated Context
│   │   ├── Yan Etkisiz Tür Çıkarımı
│   │   └── Fonksiyon Çağrı Örnekleri
│   ├── Default Argument
│   │   ├── Variyadik Fonksiyonlar
│   │   ├── Sağdan Sola Varsayılan Değer Atama
│   │   └── Fonksiyon Redeclaration
│   └── constexpr Anahtar Sözcüğü
│       ├── Derleme Zamanı Sabitleri
│       └── Runtime İşlem Kısıtlamaları
│
├── 03-Lesson.md
│   ├── constexpr Fonksiyonlar
│   │   ├── Derleme Zamanı Hesaplama
│   │   └── Statik Değişken Kısıtlaması
│   ├── ODR (One Definition Rule)
│   │   ├── Tanım Çakışması Örnekleri
│   │   └── ODR İstisnaları (Inline, constexpr)
│   ├── Inline Expansion
│   │   ├── Kod Yerleştirme Mekanizması
│   │   └── Başlık Dosyalarında Kullanım
│   └── Enumaration Types
│       ├── C-Style Enum Kısıtlamaları
│       └── Tür Güvenliği Sorunları
│
├── 04-Lesson.md
│   ├── Scope Enum
│   │   ├── enum class Sözdizimi
│   │   ├── Underlying Type Belirleme
│   │   └── C++20 using enum Özelliği
│   ├── Çözünürlük Operatörü (::)
│   │   ├── Global Scope Erişimi
│   │   └── Nitelikli İsim Kullanımı
│   ├── If with Initialization
│   │   ├── Değişken Yaşam Döngüsü
│   │   └── Çoklu Değişkenli Koşullar
│   ├── Type-Cast Operators
│   │   ├── static_cast (Güvenli Dönüşüm)
│   │   ├── const_cast (const Nitelik Değişimi)
│   │   ├── reinterpret_cast (Bit Düzeyi Dönüşüm)
│   │   └── dynamic_cast (Polimorfik Dönüşüm)
│   └── [[nodiscard]] Attribute
│       └── Geri Dönüş Değeri Zorunluluğu
│
├── 05-Lesson.md
│   ├── Function Overloading Temelleri
│   │   ├── Parametre Türü/Sayısı Farkları
│   │   └── Geri Dönüş Türü Kısıtlaması
│   ├── Overload Resolution
│   │   ├── Tam Eşleşme (Exact Match)
│   │   ├── İleri Dönüşüm (Promotion)
│   │   └── Standart Dönüşüm (Conversion)
│   └── Özel Durumlar
│       ├── nullptr Belirsizliği
│       ├── const/Referans Aşırı Yüklemesi
│       └── Bool/Pointer Çözümlemesi
│
├── 06-Lesson.md
│   ├── Class Anatomisi
│   │   ├── Veri Üyeleri (Data Members)
│   │   └── Üye Fonksiyonlar (Member Functions)
│   ├── Erişim Kontrolü
│   │   ├── public/private/protected
│   │   └── struct vs class Farkları
│   ├── Üye Fonksiyonlar
│   │   ├── Sınıf İçi/Dışı Tanım
│   │   ├── const Üye Fonksiyonlar
│   │   └── static Üye Fonksiyonlar
│   └── İsim Arama (Name Lookup)
│       ├── Sınıf Kapsamı (Class Scope)
│       ├── Bağımlı İsim Arama (ADL)
│       └── Global/Local Çakışma Çözümü
│
├── 07-Lesson.md
│   ├── this Pointer
│   │   ├── Nesne Erişim Mekanizması
│   │   ├── Zincirleme Çağrılar (Chaining)
│   │   └── const/static Farkları
│   └── const Üye Fonksiyonlar
│       ├── Salt-Okur Fonksiyonlar
│       ├── const Nesne Kısıtlamaları
│       └── mutable Anahtar Sözcüğü
│
├── 08-Lesson.md
│   ├── Constructor Functions (Yapıcı Fonkisyonlar)
│   │   ├── Definition Rules
│   │   ├── Parameterized Constructor
│   │   ├── Default Constructor
│   │   └── Initializer List (Başlatıcı Listesi)
│   ├── Destructor Functions (Yıkıcı Fonksiyonlar)
│   │   ├── Definition Rules
│   │   └── Resource Management Example
│   ├── Storage Duration (Depolama Süresi)
│   │   ├── Automatic Storage Duration
│   │   ├── Static Storage Duration
│   │   ├── Dynamic Storage Duration
│   │   ├── Thread Storage Duration
│   │   └── Global Objects Initialization Order
│   └── RAII (Resource Acquisition Is Initialization)
│       ├── Basic Principle
│       ├── Benefits
│       ├── Examples (Smart Pointers, fstream, lock_guard)
│       └── Custom RAII Class
│
├── 09-Lesson.md
│   ├── Memory Management: `new` and `delete`
│   │   ├── Single Object
│   │   ├── Array
│   │   └── Important Notes
│   ├── Declaration and Special Member Functions
│   │   ├── `not declared` error
│   │   ├── `user declared` vs `user defined`
│   │   ├── `= default`
│   │   ├── `= delete`
│   │   └── `implicitly declared` (default and deleted)
│   ├── Special Member Functions Table
│   │   ├── Constructor
│   │   ├── Destructor
│   │   ├── Copy Constructor
│   │   ├── Copy Assignment
│   │   ├── Move Constructor
│   │   └── Move Assignment
│   ├── Value Type vs Value Semantics
│   ├── Key Terms
│   │   ├── Invariant
│   │   └── Aggregate
│   └── Rule of Zero
│
└── 10-Lesson.md
    ├── Copy Assignment
    │   ├── Syntax
    │   ├── When Called
    │   └── Example
    ├── Move Constructor
    │   ├── Syntax
    │   ├── When Called
    │   └── Example
    ├── Move Assignment
    │   ├── Syntax
    │   ├── When Called
    │   └── Example
    ├── Rules for Compiler-Generated Special Member Functions
    └── Extra Information
        ├── `std::move` Alternative
        ├── Compiler Choice: Move vs Copy Constructor
        ├── Dangling Pointer
        └── Interview Questions
```