# Contents of Cpp Notes

<details>
<summary><strong>🔍 Ders 1: Genel C++ Kavramları, Pointer'lar ve Referanslar</strong></summary>

<details>
<summary><code>Essential C Topics (Bilinmesi Gereken C Konuları)</code></summary>
  
- [Essential C Topics (Bilinmesi Gereken C Konuları)](01-Lesson.md#essential-c-topics-bilinmesi-gereken-c-konuları)
</details>

<details>
<summary><code>References (Referanslar)</code></summary>
  
- [References (Referanslar)](01-Lesson.md#references-referanslar)
</details>

<details>
<summary><code>Array Pointers (Dizi Pointer'ları)</code></summary>
  
- [Array Pointers (Dizi Pointer'ları)](01-Lesson.md#array-pointers-dizi-pointerları)
- [Definition and Explanation Table (Tanım ve Açıklama Tablosu)](01-Lesson.md#definition-and-explanation-table-tanım-ve-açıklama-tablosu)
- [`a` ve `&a` Arasındaki Fark](01-Lesson.md#a-ve-a-arasındaki-fark)
</details>

<details>
<summary><code>Semantics and Common Semantic Types (Semantik ve Sık Geçen Semantik Türleri)</code></summary>
  
- [Semantics and Common Semantic Types (Semantik ve Sık Geçen Semantik Türleri)](01-Lesson.md#semantics-and-common-semantic-types-semantik-ve-sık-geçen-semantik-türleri)
- [Function Return Type of an Array (Bir Dizinin Fonksiyon Dönüş Tipi)](01-Lesson.md#function-return-type-of-an-array-bir-dizinin-fonksiyon-dönüş-tipi)
- [Semantics (Semantik)](01-Lesson.md#semantics-semantik)
- [Common Semantic Types in C++ (C++'ta Sık Geçen Semantik Türleri)](01-Lesson.md#common-semantic-types-in-c-c-ta-sık-geçen-semantik-türleri)
- [`std::move`](01-Lesson.md#stdmove)
- [Pointer vs. Reference Comparison (Pointer ve Referans Karşılaştırması)](01-Lesson.md#pointer-vs-reference-comparison-pointer-ve-referans-karşılaştırması)
</details>

<details>
<summary><code>The `const` Qualifier and Reference Behavior (`const` Niteleyicisi ve Referansların Davranışı)</code></summary>
  
- [The `const` Qualifier and Reference Behavior (`const` Niteleyicisi ve Referansların Davranışı)](01-Lesson.md#the-const-qualifier-and-reference-behavior-const-niteleyicisi-ve-referansların-davranışı)
- [Top Level `const` (Üst Seviye `const`)](01-Lesson.md#top-level-const-üst-seviye-const)
- [Low Level `const` (Alt Seviye `const`)](01-Lesson.md#low-level-const-alt-seviye-const)
- [Returning References from Functions (Fonksiyonlardan Referans Dönüşü)](01-Lesson.md#returning-references-from-functions-fonksiyonlardan-referans-dönüşü)
- [Reference Types and Binding (Referans Türleri ve Bağlanma)](01-Lesson.md#reference-types-and-binding-referans-türleri-ve-bağlanma)
- [Pointer and Reference Semantics (Pointer ve Referans Semantiği)](01-Lesson.md#pointer-and-reference-semantics-pointer-ve-referans-semantiği)
</details>

<details>
<summary><code>Scope, Type Deduction, and `using` (Scope, Tip Çıkarımı ve `using`)</code></summary>
  
- [Scope, Type Deduction, and `using` (Scope, Tip Çıkarımı ve `using`)](01-Lesson.md#scope-type-deduction-and-using-scope-tip-çıkarımı-ve-using)
- [Scope Leakage (Scope Kaçağı)](01-Lesson.md#scope-leakage-scope-kaçağı)
- [Type Deduction (Tip Çıkarımı)](01-Lesson.md#type-deduction-tip-çıkarımı)
- [`auto` and `const` Relationship (`auto` ve `const` İlişkisi)](01-Lesson.md#auto-and-const-relationship-auto-ve-const-ilişkisi)
- [`using`: `typedef` Alternative (`using`: `typedef` Alternatifi)](01-Lesson.md#using-typedef-alternative-using-typedef-alternatifi)
</details>

<details>
<summary><code>Value Categories (Değer Kategorileri)</code></summary>
  
- [Value Categories (Değer Kategorileri)](01-Lesson.md#value-categories-değer-kategorileri)
- [Category Hierarchy (Kategori Hiyerarşisi)](01-Lesson.md#category-hierarchy-kategori-hiyerarşisi)
- [1. lvalue (Left-hand Value)](01-Lesson.md#1-lvalue-left-hand-value)
- [2. prvalue (Pure rvalue)](01-Lesson.md#2-prvalue-pure-rvalue)
- [3. xvalue (eXpiring Value)](01-Lesson.md#3-xvalue-expiring-value)
- [4. Hybrid Categories (Karma Kategoriler)](01-Lesson.md#4-hybrid-categories-karma-kategoriler)
- [glvalue (Generalized lvalue)](01-Lesson.md#glvalue-generalized-lvalue)
- [rvalue (Right-hand value)](01-Lesson.md#rvalue-right-hand-value)
- [Practical Determination Table (Pratik Belirleme Tablosu)](01-Lesson.md#practical-determination-table-pratik-belirleme-tablosu)
- [Code Examples (Kod Örnekleri)](01-Lesson.md#code-examples-kod-örnekleri)
- [Basic Examples (Temel Örnekler)](01-Lesson.md#basic-examples-temel-örnekler)
- [Reference Binding Rules (Reference Bağlama Kuralları)](01-Lesson.md#reference-binding-rules-reference-bağlama-kuralları)
- [Summary Table (Özet Tablo)](01-Lesson.md#summary-table-özet-tablo)
- [Quick Reminder (Hızlı Hatırlatma)](01-Lesson.md#quick-reminder-hızlı-hatırlatma)
- [Simple Test Questions (Basit Test Soruları)](01-Lesson.md#simple-test-questions-basit-test-soruları)
- [Practical Tips (Pratik İpuçları)](01-Lesson.md#practical-tips-pratik-ipuçları)
</details>

<details>
<summary><code>Reference Collapsing and Universal References (Referans Katlama ve Universal Referanslar)</code></summary>
  
- [Reference Collapsing and Universal References (Referans Katlama ve Universal Referanslar)](01-Lesson.md#reference-collapsing-and-universal-references-referans-katlama-ve-universal-referanslar)
- [Reference Collapsing (Referans Katlama)](01-Lesson.md#reference-collapsing-referans-katlama)
- [Conversion Rules (Dönüşüm Kuralları)](01-Lesson.md#conversion-rules-dönüşüm-kuralları)
</details>

</details>

---

<details>
<summary><strong>🔍 Ders 2: `decltype` Belirleyici, Varsayılan Argümanlar ve `constexpr`</strong></summary>

<details>
<summary><code>The `decltype` Specifier (`decltype` Belirleyici)</code></summary>
  
- [The `decltype` Specifier (`decltype` Belirleyici)](02-Lesson.md#the-decltype-specifier-decltype-belirleyici)
- [Rule Set 1 (Kural Seti 1)](02-Lesson.md#rule-set-1-kural-seti-1)
- [Rule Set 2 (Kural Seti 2)](02-Lesson.md#rule-set-2-kural-seti-2)
- [Karışık Örnek](02-Lesson.md#karışık-örnek)
</details>

<details>
<summary><code>Unevaluated Context (İşlem Kodu Üretilmemiş Bağlam)</code></summary>
  
- [Unevaluated Context (İşlem Kodu Üretilmemiş Bağlam)](02-Lesson.md#unevaluated-context-i̇şlem-kodu-üretilmemiş-bağlam)
</details>

<details>
<summary><code>Default Arguments (Varsayılan Argümanlar)</code></summary>
  
- [Default Arguments (Varsayılan Argümanlar)](02-Lesson.md#default-arguments-varsayılan-argümanlar)
- [Declaring Default Arguments (Varsayılan Argüman Bildirimi)](02-Lesson.md#declaring-default-arguments-varsayılan-argüman-bildirimi)
- [Kullanım örneği-1](02-Lesson.md#kullanım-örneği-1)
- [Kullanım örneği-2](02-Lesson.md#kullanım-örneği-2)
</details>

<details>
<summary><code>The `constexpr` Keyword (`constexpr` Anahtar Sözcüğü)</code></summary>
  
- [The `constexpr` Keyword (`constexpr` Anahtar Sözcüğü)](02-Lesson.md#the-constexpr-keyword-constexpr-anahtar-sözcüğü)
</details>

</details>

---

<details>
<summary><strong>🔍 Ders 3: `constexpr` Fonksiyonlar, ODR ve Enum Türleri</strong></summary>

<details>
<summary><code>`constexpr` Functions (`constexpr` Fonksiyonlar)</code></summary>
  
- [`constexpr` Functions (`constexpr` Fonksiyonlar)](03-Lesson.md#constexpr-functions-constexpr-fonksiyonlar)
</details>

<details>
<summary><code>One Definition Rule - ODR (Tek Tanımlama Kuralı)</code></summary>
  
- [One Definition Rule - ODR (Tek Tanımlama Kuralı)](03-Lesson.md#one-definition-rule---odr-tek-tanımlama-kuralı)
</details>

<details>
<summary><code>Inline Expansion (Satır İçi Genişletme)</code></summary>
  
- [Inline Expansion (Satır İçi Genişletme)](03-Lesson.md#inline-expansion-satır-i̇çi-genişletme)
</details>

<details>
<summary><code>Enumeration Types (Numaralandırma Türleri)</code></summary>
  
- [Enumeration Types (Numaralandırma Türleri)](03-Lesson.md#enumeration-types-numaralandırma-türleri)
- [C++'da enum türlerinin istenmeyen özellikleri](03-Lesson.md#c-dilinde-modern-c-öncesi-enum-türlerinin-istenmeyen-özellikleri)
</details>

</details>

---

<details>
<summary><strong>🔍 Ders 4: Scoped Enumerations, İsim Arama ve Tür Dönüşümleri</strong></summary>

<details>
<summary><code>Scoped Enumerations (Kapsamlı Numaralandırmalar)</code></summary>
  
- [Scoped Enumerations (Kapsamlı Numaralandırmalar)](04-Lesson.md#scoped-enumerations-kapsamlı-numaralandırmalar)
</details>

<details>
<summary><code>Scoped Enumerations and Underlying Type Specification (Kapsamlı Numaralandırmalar ve Temel Tür Belirtme)</code></summary>
  
- [Scoped Enumerations and Underlying Type Specification (Kapsamlı Numaralandırmalar ve Temel Tür Belirtme)](04-Lesson.md#scoped-enumerations-and-underlying-type-specification-kapsamlı-numaralandırmalar-ve-temel-tür-belirtme)
</details>

<details>
<summary><code>The Connection Between Name Lookup, Context Control, and Access Control (İsim Arama, Bağlam Kontrolü ve Erişim Kontrolü Arasındaki Bağlantı)</code></summary>
  
- [The Connection Between Name Lookup, Context Control, and Access Control (İsim Arama, Bağlam Kontrolü ve Erişim Kontrolü Arasındaki Bağlantı)](04-Lesson.md#the-connection-between-name-lookup-context-control-and-access-control-i̇sim-arama-bağlam-kontrolü-ve-erişim-kontrolü-arasındaki-bağlantı)
</details>

<details>
<summary><code>`::` - Scope Resolution Operator (Kapsam Çözünürlük Operatörü)</code></summary>
  
- [`::` - Scope Resolution Operator (Kapsam Çözünürlük Operatörü)](04-Lesson.md#---scope-resolution-operator-kapsam-çözünürlük-operatörü)
</details>

<details>
<summary><code>Qualified and Unqualified Names (Nitelikli ve Niteliksiz İsimler)</code></summary>
  
- [Qualified and Unqualified Names (Nitelikli ve Niteliksiz İsimler)](04-Lesson.md#qualified-and-unqualified-names-nitelikli-ve-niteliksiz-i̇simler)
- [Qualified Name (Nitelikli İsim)](04-Lesson.md#qualified-name-nitelikli-i̇sim)
- [Unqualified Name (Niteliksiz İsim)](04-Lesson.md#unqualified-name-niteliksiz-i̇sim)
- [Qualified and Unqualified Name Example (Nitelikli ve Niteliksiz İsim Örneği)](04-Lesson.md#qualified-and-unqualified-name-example-nitelikli-ve-niteliksiz-i̇sim-örneği)
</details>

<details>
<summary><code>`if` with Initialization (`if` Başlatmalı Yapısı)</code></summary>
  
- [`if` with Initialization (`if` Başlatmalı Yapısı)](04-Lesson.md#if-with-initialization-i̇f-başlatmalı-yapısı)
</details>

<details>
<summary><code>Type-Cast Operators (Tür Dönüşüm Operatörleri)</code></summary>
  
- [Type-Cast Operators (Tür Dönüşüm Operatörleri)](04-Lesson.md#type-cast-operators-tür-dönüşüm-operatörleri)
- [C-Style Cast (C Tarzı Dönüşüm)](04-Lesson.md#c-style-cast-c-tarzı-dönüşüm)
- [`static_cast` (Statik Dönüşüm)](04-Lesson.md#static_cast-statik-dönüşüm)
- [`const_cast` (Const Dönüşümü)](04-Lesson.md#const_cast-const-dönüşümü)
- [`reinterpret_cast` (Yeniden Yorumlama Dönüşümü)](04-Lesson.md#reinterpret_cast-yeniden-yorumlama-dönüşümü)
- [Type-Cast Operators Summary (Tür Dönüşüm Operatörleri Özeti)](04-Lesson.md#type-cast-operators-summary-tür-dönüşüm-operatörleri-özeti)
</details>

<details>
<summary><code>`[[nodiscard]]` Attribute (`[[nodiscard]]` Özniteliği)</code></summary>
  
- [`[[nodiscard]]` Attribute (`[[nodiscard]]` Özniteliği)](04-Lesson.md#nodiscard-attribute-nodiscard-özniteliği)
</details>

</details>

---

<details>
<summary><strong>🔍 Ders 5: Fonksiyon Aşırı Yükleme ve Çözümleme</strong></summary>

<details>
<summary><code>Function Overloading (Fonksiyon Aşırı Yükleme)</code></summary>
  
- [Function Overloading (Fonksiyon Aşırı Yükleme)](05-Lesson.md#function-overloading-fonksiyon-aşırı-yükleme)
- [Deciding on Function Overloading (Fonksiyon Aşırı Yüklemesine Karar Verme)](05-Lesson.md#deciding-on-function-overloading-fonksiyon-aşırı-yüklemesine-karar-verme)
- [Understanding and Distinguishing the Differences Between Function Overloading and Function Redeclaration (Fonksiyon Aşırı Yükleme ve Yeniden Bildirim Arasındaki Farkları Anlama ve Ayırt Etme)](05-Lesson.md#understanding-and-distinguishing-the-differences-between-function-overloading-and-function-redeclaration-fonksiyon-aşırı-yükleme-ve-yeniden-bildirim-arasındaki-farkları-anlama-ve-ayırt-etme)
- [Function Overloading Resolution Process (Fonksiyon Aşırı Yükleme Çözümleme Süreci)](05-Lesson.md#function-overloading-resolution-process-fonksiyon-aşırı-yükleme-çözümleme-süreci)
- [Parametre Türlerini Karşılaştırma](05-Lesson.md#parametre-türlerini-karşılaştırma)
- [Tam Eşleşme (Exact Match)](05-Lesson.md#tam-eşleşme-exact-match)
- [L Value to R Value Conversion (L Değeri'nden R Değeri'ne dönüşüm)](05-Lesson.md#l-value-to-r-value-conversion-l-değerinden-r-değerine-dönüşüm)
- [`const` Conversion (`const` dönüşümü)](05-Lesson.md#const-conversion-const-dönüşümü)
- [Array Decay (Array to Pointer Conversion)](05-Lesson.md#array-decay-array-to-pointer-conversion)
- [Function to Pointer Conversion (Fonksiyonun Göstergesine Dönüşüm)](05-Lesson.md#function-to-pointer-conversion-fonksiyonun-göstergesine-dönüşüm)
- [İleri Dönüşüm (Promotion) ve Dönüşüm (Conversion)](05-Lesson.md#i̇leri-dönüşüm-promotion-ve-dönüşüm-conversion)
- [İleri dönüşüm(Promotion)](05-Lesson.md#i̇leri-dönüşümpromotion)
- [Dönüşüm (Conversion)](05-Lesson.md#dönüşüm-conversion)
- [En İyi Uyum (Best Match / Viable Function)](05-Lesson.md#en-i̇yi-uyum-best-match--viable-function)
- [Ambiguity (Çakışma)](05-Lesson.md#ambiguity-çakışma)
</details>

<details>
<summary><code>Examples (Örnekler)</code></summary>
  
- [Examples (Örnekler)](05-Lesson.md#examples-örnekler)
- [Örnek-1: Mutable ve Immutable için yapılan implamantasyon](05-Lesson.md#örnek-1-mutable-ve-immutable-için-yapılan-implamantasyon)
- [Örnek-2: Varsayılan Argümanlar ve Function Overloading Arasındaki Çakışma](05-Lesson.md#örnek-2-varsayılan-argümanlar-ve-function-overloading-arasındaki-çakışma)
- [Örnek-3: Referans Türleri ve Function Overloading Çözünürlüğü](05-Lesson.md#örnek-3-referans-türleri-ve-function-overloading-çözünürlüğü)
- [Örnek-4: Null Pointer Literali (`0`) ve `nullptr` ile Aşırı Yükleme](05-Lesson.md#örnek-4-null-pointer-literali-0-ve-nullptr-ile-aşırı-yükleme)
- [Örnek-5: Pointer Türleri Arasında `nullptr` Belirsizliği](05-Lesson.md#örnek-5-pointer-türleri-arasında-nullptr-belirsizliği)
- [Örnek-6: `std::nullptr_t` ile Belirsizliğin Giderilmesi](05-Lesson.md#örnek-6-stdnullptr_t-ile-belirsizliğin-giderilmesi)
- [Örnek-7: L-value ve R-value Referansları ile Function Overloading](05-Lesson.md#örnek-7-l-value-ve-r-value-referansları-ile-function-overloading)
- [Örnek-8: İstisnai Bir Durum - Pointer ve Bool Aşırı Yüklemesi](05-Lesson.md#örnek-8-i̇stisnai-bir-durum---pointer-ve-bool-aşırı-yüklemesi)
- [Örnek-9: Birden Fazla Argümanlı Aşırı Yükleme Çözünürlüğü](05-Lesson.md#örnek-9-birden-fazla-argümanlı-aşırı-yükleme-çözünürlüğü)
</details>

<details>
<summary><code>Additional Information (Ek Bilgi)</code></summary>
  
- [Additional Information (Ek Bilgi)](05-Lesson.md#additional-information-ek-bilgi)
</details>

</details>

---

<details>
<summary><strong>🔍 Ders 6: Sınıflar ve İsim Arama</strong></summary>

<details>
<summary><code>Classes</code></summary>
  
- [Classes](06-Lesson.md#classes)
- [Class Definition](06-Lesson.md#class-definition)
- [Access Specifiers](06-Lesson.md#access-specifiers)
- [`public` Access Specifier](06-Lesson.md#public-access-specifier-genel-erişim-belirleyicisi)
- [`private` Access Specifier](06-Lesson.md#private-access-specifier-özel-erişim-belirleyicisi)
- [`protected` Access Specifier](06-Lesson.md#protected-access-specifier-korunmuş-erişim-belirleyicisi)
- [Member Functions](06-Lesson.md#member-functions)
- [Temel Bildirim ve Tanım](06-Lesson.md#temel-bildirim-ve-tanım)
- [Access Rules (`public` vs `private`)](06-Lesson.md#access-rules-public-vs-private)
- [`const` Member Functions](06-Lesson.md#const-member-functions)
- [`static` Member Functions](06-Lesson.md#static-member-functions)
- [Definition Locations: Inline vs Outside](06-Lesson.md#definition-locations-inline-vs-outside)
- [Member Function Overloading](06-Lesson.md#member-function-overloading)
</details>

<details>
<summary><code>Name Lookup</code></summary>
  
- [Name Lookup](06-Lesson.md#name-lookup)
- [Scope](06-Lesson.md#scope)
- [Unqualified Name Lookup](06-Lesson.md#unqualified-name-lookup)
- [Qualified Name Lookup](06-Lesson.md#qualified-name-lookup)
- [Argument-Dependent Lookup (ADL)](06-Lesson.md#argument-dependent-lookup-adl)
- [Detailed Example](06-Lesson.md#detailed-example)
</details>

<details>
<summary><code>Extra Notes on Class Usage</code></summary>
  
- [Extra Notes on Class Usage](06-Lesson.md#extra-notes-on-class-usage)
- [Defining a Class Without an Access Specifier](06-Lesson.md#defining-a-class-without-an-access-specifier)
- [Interview Question-1](06-Lesson.md#interview-question-1)
- [Defining a Variable with the Same Name within Class Scope](06-Lesson.md#defining-a-variable-with-the-same-name-within-class-scope)
</details>

</details>

---

<details>
<summary><strong>🔍 Ders 7: Classes (Sınıflar)</strong></summary>

<details>
<summary><code>The `this` Pointer (this İşaretçisi)</code></summary>
  
- [The `this` Pointer (this İşaretçisi)](07-Lesson.md#the-this-pointer-this-i̇şaretçisi)
- [Sınıfın Veri Üyeleri ile Yerel Değişkenler Arasındaki İsim Çakışmalarını Çözmek](07-Lesson.md#sınıfın-veri-üyeleri-ile-yerel-değişkenler-arasındaki-i̇sim-çakışmalarını-çözmek)
- [Chaining (Zincirleme Çağrılar) Yapmak](07-Lesson.md#chaining-zincirleme-çağrılar-yapmak)
- [Nesnenin Kendi Adresini Başka Bir Fonksiyona Geçirmek](07-Lesson.md#nesnenin-kendi-adresini-başka-bir-fonksiyona-geçirmek)
- [Önemli Notlar](07-Lesson.md#önemli-notlar)
</details>

<details>
<summary><code>const Member Functions (const Üye Fonksiyonlar)</code></summary>
  
- [const Member Functions (const Üye Fonksiyonlar)](07-Lesson.md#const-member-functions-const-üye-fonksiyonlar)
</details>

<details>
<summary><code>const Objects and const Member Functions (const Nesneler ve const Üye Fonksiyonlar)</code></summary>
  
- [const Objects and const Member Functions (const Nesneler ve const Üye Fonksiyonlar)](07-Lesson.md#const-objects-and-const-member-functions-const-nesneler-ve-const-üye-fonksiyonlar)
- [The `mutable` Keyword (mutable Anahtar Sözcüğü)](07-Lesson.md#the-mutable-keyword-mutable-anahtar-sözcüğü)
</details>

<details>
<summary><code>Mülakat Notları-1</code></summary>
  
- [Mülakat Notları-1](07-Lesson.md#mülakat-notları-1)
</details>

</details>

---

<details>
<summary><strong>🔍 Ders 8: Sınıflar</strong></summary>

<details>
<summary><code>Constructor Functions (Yapıcı Fonksiyonlar)</code></summary>
  
- [Constructor Functions (Yapıcı Fonksiyonlar)](08-Lesson.md#constructor-functions-yapıcı-fonksiyonlar)
- [How to Define a Constructor Function (Yapıcı Fonksiyon Nasıl Tanımlanır?)](08-Lesson.md#how-to-define-a-constructor-function-yapıcı-fonksiyon-nasıl-tanımlanır)
- [Types of Constructor Functions (Yapıcı Fonksiyon Türleri)](08-Lesson.md#types-of-constructor-functions-yapıcı-fonksiyon-türleri)
</details>

<details>
<summary><code>Initializer List (Başlatıcı Listesi)</code></summary>
  
- [Initializer List (Başlatıcı Listesi)](08-Lesson.md#initializer-list-başlatıcı-listesi)
</details>

<details>
<summary><code>Destructor Functions (Yıkıcı Fonksiyonlar)</code></summary>
  
- [Destructor Functions (Yıkıcı Fonksiyonlar)](08-Lesson.md#destructor-functions-yıkıcı-fonksiyonlar)
- [How to Define a Destructor Function (Yıkıcı Fonksiyon Nasıl Tanımlanır?)](08-Lesson.md#how-to-define-a-destructor-function-yıkıcı-fonksiyon-nasıl-tanımlanır)
</details>

<details>
<summary><code>Storage Duration in C++ (C++'da Depolama Süresi)</code></summary>
  
- [Storage Duration in C++ (C++'da Depolama Süresi)](08-Lesson.md#storage-duration-in-c-cda-depolama-süresi)
- [Automatic Storage Duration (Otomatik Depolama Süresi)](08-Lesson.md#automatic-storage-duration-otomatik-depolama-süresi)
- [Static Storage Duration (Statik Depolama Süresi)](08-Lesson.md#static-storage-duration-statik-depolama-süresi)
- [Dynamic Storage Duration (Dinamik Depolama Süresi)](08-Lesson.md#dynamic-storage-duration-dinamik-depolama-süresi)
- [Thread Storage Duration (İş Parçacığı Depolama Süresi)](08-Lesson.md#thread-storage-duration-i̇ş-parçacığı-depolama-süresi)
- [Initialization Order of Global Objects (Global Nesnelerin Başlatılma Sırası)](08-Lesson.md#initialization-order-of-global-objects-global-nesnelerin-başlatılma-sırası)
</details>

<details>
<summary><code>RAII (Resource Acquisition Is Initialization) (Kaynak Edinimi Başlatmadır)</code></summary>
  
- [RAII (Resource Acquisition Is Initialization) (Kaynak Edinimi Başlatmadır)](08-Lesson.md#raii-resource-acquisition-is-initialization-kaynak-edinimi-başlatmadır)
- [Core Principle (Temel Prensip)](08-Lesson.md#core-principle-temel-prensip)
- [Benefits of RAII (RAII'nin Faydaları)](08-Lesson.md#benefits-of-raii-raii-nin-faydaları)
- [Basic Examples of RAII (RAII'nin Temel Örnekleri)](08-Lesson.md#basic-examples-of-raii-raiinin-temel-örnekleri)
- [Creating Your Own RAII Class (Kendi RAII Sınıfınızı Oluşturmak)](08-Lesson.md#creating-your-own-raii-class-kendi-raii-sınıfınızı-oluşturmak)
</details>

</details>

---

<details>
<summary><strong>🔍 Ders 9: Class, Value Type, Value Semantics ve Özel Anahtar Sözcükler</strong></summary>

<details>
<summary><code>Class</code></summary>
  
- [Class](09-Lesson.md#class)
- [Memory Management: `new` and `delete` (Bellek Yönetimi: `new` ve `delete`)](09-Lesson.md#memory-management-new-and-delete-bellek-yönetimi-new-ve-delete)
- [Single Object (Tek Nesne İçin)](09-Lesson.md#single-object-tek-nesne-için)
- [Array (Dizi İçin)](09-Lesson.md#array-dizi-için)
- [Points to Consider (Dikkat Edilmesi Gerekenler)](09-Lesson.md#points-to-consider-dikkat-edilmesi-gerekenler)
- [Declarations and Special Member Functions in C++](09-Lesson.md#declarations-and-special-member-functions-in-c)
- [`not declared` Error (`not declared` Hatası)](09-Lesson.md#not-declared-error-not-declared-hatası)
- [`user-declared` & `user-defined`](09-Lesson.md#user-declared--user-defined-kullanıcı-tarafından-bildirilmiş--kullanıcı-tanımlı)
- [`= default`](09-Lesson.md=-default-varsayılan-olarak-ayarla)
- [`= delete`](09-Lesson.md=-delete-silinmiş-olarak-ayarla)
- [`implicitly declared`](09-Lesson.md#implicitly-declared-dolaylı-olarak-bildirilmiş)
- [Special Member Functions](09-Lesson.md#special-member-functions-özel-üye-fonksiyonları)
- [Constructor (Kurucu)](09-Lesson.md#constructor-kurucu)
- [Destructor (Yıkıcı)](09-Lesson.md#destructor-yıkıcı)
- [Copy Constructor (Kopyalama Kurucusu)](09-Lesson.md#copy-constructor-kopyalama-kurucusu)
- [Situations Where Copy Constructor is Called](09-Lesson.md#situations-where-copy-constructor-is-called-kopyalama-kurucusunun-çağrıldığı-durumlar)
</details>

<details>
<summary><code>Value Type (Değer Tipi)</code></summary>
  
- [Value Type (Değer Tipi)](09-Lesson.md#value-type-değer-tipi)
</details>

<details>
<summary><code>Value Semantics (Değer Semantiği)</code></summary>
  
- [Value Semantics (Değer Semantiği)](09-Lesson.md#value-semantics-değer-semantigi)
</details>

<details>
<summary><code>Special Keywords (Özel Anahtar Kelimeler)</code></summary>
  
- [Special Keywords (Özel Anahtar Kelimeler)](09-Lesson.md#special-keywords-özel-anahtar-kelimeler)
- [Invariant (Sınıf Sabiti/Değişmezlik Kuralı)](09-Lesson.md#invariant-sınıf-sabitideğişmezlik-kuralı)
- [Aggregate (Agrega)](09-Lesson.md#aggregate-agrega)
- [Rule of Zero (`Rule of Zero` Kuralı)](09-Lesson.md#rule-of-zero-rule-of-zero-kuralı)
- [Interview Question (Mülakat Sorusu)](09-Lesson.md#interview-question-mülakat-sorusu)
</details>

</details>

---

<details>
<summary><strong>🔍 Ders 10: Copy & Move Semantics, Derleyici Kuralları ve Ek Bilgiler</strong></summary>

<details>
<summary><code>Class (Sınıf)</code></summary>
  
- [Class (Sınıf)](10-Lesson.md#class-sınıf)
- [Special Member Functions (Özel Üye Fonksiyonları)](10-Lesson.md#special-member-functions-özel-üye-fonksiyonları)
- [Copy Assignment (Kopyalama Ataması)](10-Lesson.md#copy-assignment-kopyalama-ataması)
- [Copy Assignment'ın Çağrıldığı Durumlar](10-Lesson.md#copy-assignmentın-çağrıldığı-durumlar)
- [Temel Görevi](10-Lesson.md#temel-görevi)
- [Move Constructor (Taşıma Kurucusu)](10-Lesson.md#move-constructor-taşıma-kurucusu)
- [Taşıma Kurucusunun Çağrıldığı Durumlar](10-Lesson.md#taşıma-kurucusunun-çağrıldığı-durumlar)
- [Temel Görevi](10-Lesson.md#temel-görevi-1)
- [Move Assignment (Taşıma Ataması)](10-Lesson.md#move-assignment-taşıma-ataması)
- [Taşıma Atamasının Çağrıldığı Durumlar](10-Lesson.md#taşıma-atamasının-çağrıldığı-durumlar)
- [Temel Görevi](10-Lesson.md#temel-görevi-2)
</details>

<details>
<summary><code>When Does the Compiler Write Special Member Functions?</code></summary>
  
- [When Does the Compiler Write Special Member Functions?](10-Lesson.md#when-does-the-compiler-write-special-member-functions-derleyici-hangi-durumlarda-özel-üye-fonksiyonları-yazar)
</details>

<details>
<summary><code>Automatic Generation Rules for Special Member Functions</code></summary>
  
- [Automatic Generation Rules for Special Member Functions](10-Lesson.md#automatic-generation-rules-for-special-member-functions-özel-üye-fonksiyonlarının-otomatik-oluşturulma-kuralları)
</details>

<details>
<summary><code>Additional Information (Ek Bilgiler)</code></summary>
  
- [Additional Information (Ek Bilgiler)](10-Lesson.md#additional-information-ek-bilgiler)
- [C++ Özel Üye Fonksiyonları: Derleyicinin Otomatik Yazma Davranışı](10-Lesson.md#c-özel-üye-fonksiyonları-derleyicinin-otomatik-yazma-davranışı)
- [Special Member Functions (Özel Üye Fonksiyonları)](10-Lesson.md#special-member-functions-özel-üye-fonksiyonları-1)
- [Alternative Representation of `std::move`](10-Lesson.md#alternative-representation-of-stdmove-stdmove-alternatif-gösterimi)
- [When Does the Compiler Choose the Move Constructor or Copy Constructor?](10-Lesson.md#when-does-the-compiler-choose-the-move-constructor-or-copy-constructor-derleyici-ne-zaman-taşıma-kurucusunu-veya-kopyalama-kurucusunu-seçer)
- [Dangling Pointer (Sallanan Gösterge)](10-Lesson.md#dangling-pointer-sallanan-gösterge)
- [Dangling Pointer Gerçekleşme Durumları](10-Lesson.md#dangling-pointer-gerçekleşme-durumları)
- [Engelleme Yöntemleri](10-Lesson.md#engelleme-yöntemleri)
- [Interview Questions and Answers: Copy and Move Semantics](10-Lesson.md#interview-questions-and-answers-copy-and-move-semantics-mülakat-soruları-ve-cevapları-copy-ve-taşıma-semantigi)
</details>

</details>

---

<details>
<summary><strong>🔍 Ders 11: Classes (Sınıflar)</strong></summary>

<details>
<summary><code>Move-Only Types (Sadece Taşınabilir Tipler)</code></summary>
  
- [Move-Only Types (Sadece Taşınabilir Tipler)](11-Lesson.md#move-only-types-sadece-taşınabilir-tipler)
- [Neden Bazı Şeyler Sadece Taşınabilir Olmalıdır?](11-Lesson.md#neden-bazı-şeyler-sadece-taşınabilir-olmalıdır)
</details>

<details>
<summary><code>Temporary Objects (Geçici Nesneler)</code></summary>
  
- [Temporary Objects (Geçici Nesneler)](11-Lesson.md#temporary-objects-geçici-nesneler)
- [Geçici Nesneler Nasıl Oluşturulur?](11-Lesson.md#geçici-nesneler-nasıl-oluşturulur)
- [Durum 1: Fonksiyonun Geri Dönüş Değeri](11-Lesson.md#durum-1-fonksiyonun-geri-dönüş-değeri-bir-ifade-olarak-kullanılırsa)
- [Durum 2: Geçici Nesnenin Sabit Bir Referansa Bağlanması](11-Lesson.md#durum-2-geçici-nesnenin-sabit-bir-referansa-bağlanması-life-expansion-durumu)
- [Durum 3: Doğrudan Geçici Nesne Oluşturma](11-Lesson.md#durum-3-doğrudan-geçici-nesne-oluşturma)
</details>

<details>
<summary><code>Moved-From State (Taşınmış Nesne Durumu)</code></summary>
  
- [Moved-From State (Taşınmış Nesne Durumu)](11-Lesson.md#moved-from-state-taşınmış-nesne-durumu)
- [Taşınmış Nesne Durumu Nasıl Oluşturulur?](11-Lesson.md#taşınmış-nesne-durumu-nasıl-oluşturulur)
- [Taşınmış Nesne Durumu (Moved-From State) Nedir?](11-Lesson.md#taşınmış-nesne-durumu-moved-from-state-nedir)
</details>

<details>
<summary><code>Conversion Constructors (Dönüşüm Yapıcıları)</code></summary>
  
- [Conversion Constructors (Dönüşüm Yapıcıları)](11-Lesson.md#conversion-constructors-dönüşüm-yapıcıları)
- [Dönüşüm Yapıcısı Nedir?](11-Lesson.md#dönüşüm-yapıcısı-nedir)
- [Dönüşüm Yapıcısının Kullanım Amaçları ve Özellikleri](11-Lesson.md#dönüşüm-yapıcısının-kullanım-amaçları-ve-özellikleri)
</details>

<details>
<summary><code>Implicit Conversion Sequences (Örtülü Dönüşüm Sekansları)</code></summary>
  
- [Implicit Conversion Sequences (Örtülü Dönüşüm Sekansları)](11-Lesson.md#implicit-conversion-sequences-örtülü-dönüşüm-sekansları)
- [Temel Kavramlar](11-Lesson.md#temel-kavramlar)
- [`B bx = A{};` Satırında Neler Oluyor?](11-Lesson.md#b-bx--a-satırında-neler-oluyor)
</details>

<details>
<summary><code>Explicit Specifier (Explicit Belirleyicisi)</code></summary>
  
- [Explicit Specifier (Explicit Belirleyicisi)](11-Lesson.md#explicit-specifier-explicit-belirleyicisi)
- [`explicit` Nedir?](11-Lesson.md#explicit-nedir)
- [Explicit Constructor'ın Kullanım Amaçları ve Özellikleri](11-Lesson.md#explicit-constructorkullanım-amaçları-ve-özellikleri)
</details>

</details>

---

<details>
<summary><strong>🔍 Ders 12: Copy Elision (Kopya Elemesi), Temporary Materialization (Geçici Malzemeleştirme) ve Return Value Optimization (Dönüş Değeri Optimizasyonu)</strong></summary>

<details>
<summary><code>Copy Elision (Kopya Elemesi)</code></summary>
  
- [Copy Elision (Kopya Elemesi)](12-Lesson.md#copy-elision-kopya-elemesi)
- [Passing a Temporary Object to a Function (Geçici Nesneyi Fonksiyona Parametre Olarak Geçirme)](12-Lesson.md#passing-a-temporary-object-to-a-function-geçici-nesneyi-fonksiyona-parametre-olarak-geçirme)
- [Returning a Temporary Object from a Function (Fonksiyondan Geçici Nesne Döndürme)](12-Lesson.md#returning-a-temporary-object-from-a-function-fonksiyondan-geçici-nesne-döndürme)
- [Returning a Named Object (Named Return Value Optimization - NRVO) (Adlandırılmış Nesneyi Döndürme)](12-Lesson.md#returning-a-named-object-named-return-value-optimization---nrvo-adlandırılmış-nesneyi-döndürme)
</details>

<details>
<summary><code>Temporary Materialization (Geçici Malzemeleştirme)</code></summary>
  
- [Temporary Materialization (Geçici Malzemeleştirme)](12-Lesson.md#temporary-materialization-geçici-malzemeleştirme)
</details>

<details>
<summary><code>Return Value Optimization (Dönüş Değeri Optimizasyonu)</code></summary>
  
- [Return Value Optimization (Dönüş Değeri Optimizasyonu)](12-Lesson.md#return-value-optimization-dönüş-değeri-optimizasyonu)
- [Unnamed RVO (URVO)](12-Lesson.md#unnamed-rvo-urvo)
- [Named RVO (NRVO)](12-Lesson.md#named-rvo-nrvo)
</details>

</details>