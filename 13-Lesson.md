# 13. Ders

## Class

### Özel Üye Fonksiyonları(Special Member Function)

#### Kopyalama Atama (`Copy Assignment`)

Zaten var olan bir nesnenin değerini, yine zaten var olan başka bir nesneye atamak için kullanılır. `operator=` şeklinde tanımlanır ve sol operandı (`this`) temsil eden bir referans döndürür. **Copy Assignment bir constructor değildir!**

```cpp
sinif_adi& operator=(const sinif_adi& diger_nesne);
```

- **`sinif_adi&`:** Atama sonrası sol operandın referansını döndürür. Bu, zincirleme atamalara (`a = b = c;`) olanak tanır.
- **`const sinif_adi& diger_nesne`:** Kopyalanacak nesnenin sabit (const) bir referansı. Bu, kopyalama sırasında kaynak nesnenin değişmemesini ve gereksiz kopyalamayı engeller.

##### Copy Assignment'ın çağırıldığı durumlar:

- **Mevcut Nesneler Arasında Atama Yapıldığında:** En temel kullanım durumudur. Bir nesne, `=` operatörü kullanılarak başka bir nesnenin değerlerini alır.

```cpp
my_class nesne_bir;    // Varsayılan kurucu çağrılır
my_class nesne_iki;    // Varsayılan kurucu çağrılır

nesne_iki = nesne_bir; // <<< Burada Kopyalama Atama Operatörü çağrılır.
                       // nesne_iki'nin içeriği, nesne_bir'in içeriğiyle güncellenir.
```

- **Zincirleme Atamalar Yapıldığında:** `=` operatörü kendi referansını döndürdüğü için zincirleme atamalar mümkündür ve her adımda kopyalama ataması çağrılır.

```cpp
my_class x, y, z;
x = y = z; // Önce y = z (Kopyalama Ataması), sonra x = (y'nin yeni hali) (Kopyalama Ataması)
```

##### Temel Görevi:

- Kaynak nesnedeki veriyi, hedef nesneye kopyalamaktır.
- Dinamik bellek veya diğer kaynaklar kullanılıyorsa, **derin kopya (deep copy)** yapılmalıdır. Bu, sallanan göstergeler veya çift serbest bırakma gibi sorunları önler.
- "Kendine atama" (self-assignment) durumunu (`a = a;` gibi) güvenli bir şekilde ele almalıdır.

```cpp
#include <iostream> // Çıkış için

class ornek_sinif
{
public:
    int* veri_isaretci; // Dinamik bellek gösteren pointer

    // Kurucu: Nesne oluştuğunda belleği tahsis et
    ornek_sinif(int baslangic_degeri = 0) : veri_isaretci(new int(baslangic_degeri))
    {
        std::cout << "Kurucu çağrıldı, adres: " << this << " | Değer: " << *veri_isaretci << '\n';
    }

    // Yıkıcı: Nesne yok edildiğinde belleği serbest bırak
    ~ornek_sinif()
    {
        delete veri_isaretci; // Tahsis edilen belleği serbest bırak
        veri_isaretci = nullptr; // Dangling pointer'ı engelle
        std::cout << "Yıkıcı çağrıldı, adres: " << this << '\n';
    }

    // Kopyalama Kurucusu: Nesne kopyalanarak oluşturulurken
    ornek_sinif(const ornek_sinif& diger) : veri_isaretci(new int(*diger.veri_isaretci))
    {
        std::cout << "Kopyalama Kurucusu çağrıldı, adres: " << this << " | Kopyalanan adres: " << &diger << '\n';
    }

    // Kopyalama Ataması Operatörü: Mevcut nesneye atama yapılırken
    ornek_sinif& operator=(const ornek_sinif& diger)
    {
        std::cout << "Kopyalama Ataması çağrıldı, mevcut adres: " << this << " | Atanan adres: " << &diger << '\n';

        // Kendi kendine atama kontrolü: nesne_a = nesne_a; durumunu engeller
        if (this == &diger)
        {
            std::cout << "Kendi kendine atama! İşlem yapılmadı." << '\n';
            return *this;
        }

        // Mevcut belleği serbest bırak
        delete veri_isaretci;

        // Yeni bellek tahsis et ve veriyi derinlemesine kopyala
        veri_isaretci = new int(*diger.veri_isaretci);

        return *this; // Zincirleme atamalar için kendi referansını döndür
    }
};

int main()
{
    std::cout << "==========>| Main Başlangıç |<==========\n\n";

    ornek_sinif nesne_bir(10); // Kurucu çağrıldı: nesne_bir (değer 10)
    ornek_sinif nesne_iki(20); // Kurucu çağrıldı: nesne_iki (değer 20)

    std::cout << "\n--- Atama Öncesi ---" << std::endl;
    std::cout << "Nesne Bir (adres: " << &nesne_bir << ") Veri: " << *nesne_bir.veri_isaretci << std::endl;
    std::cout << "Nesne İki (adres: " << &nesne_iki << ") Veri: " << *nesne_iki.veri_isaretci << std::endl;

    std::cout << "\n--- nesne_iki = nesne_bir Ataması ---" << std::endl;
    nesne_iki = nesne_bir; // Kopyalama Ataması çağrıldı

    std::cout << "\n--- Atama Sonrası ---" << std::endl;
    std::cout << "Nesne Bir (adres: " << &nesne_bir << ") Veri: " << *nesne_bir.veri_isaretci << std::endl;
    std::cout << "Nesne İki (adres: " << &nesne_iki << ") Veri: " << *nesne_iki.veri_isaretci << std::endl;

    // Kendi kendine atama örneği
    std::cout << "\n--- Kendi kendine atama (nesne_bir = nesne_bir) ---" << std::endl;
    nesne_bir = nesne_bir; // Kendi kendine atama kontrolü devrede

    std::cout << "\n==========>| Main Bitiş |<==========\n\n";
    // main'den çıkışta nesnelerin yıkıcıları çağrılır
}
```

---

#### Taşıma Kurucusu (`Move Constructor`)

Bir sınıfın **aynı türden, geçici (`rvalue`) bir nesnesinin kaynaklarını "çalarak" (taşınarak) yeni bir nesne oluşturan** özel bir kurucu fonksiyondur. Kopyalama yapmak yerine kaynak sahipliğini aktarır, bu da gereksiz veri kopyalamayı ve performans kaybını önler. **Taşıma kurucusu bir `constructor`'dır!**

```cpp
sinif_adi(sinif_adi&& diger_nesne) noexcept;
```

- **`sinif_adi&&`:** Taşınacak geçici nesnenin `rvalue` referansıdır. Bu, sadece geçici nesnelerden (veya `std::move` ile açıkça taşınan nesnelerden) çağrılmasını sağlar.
- **`noexcept`:** Bu kurucunun istisna atmayacağını belirtir. Taşıma kurucusunun `noexcept` olması, STL konteynerleri gibi yapıların taşıma optimizasyonlarını güvenle kullanabilmesi için önemlidir.

---

##### Taşıma Kurucusunun Çağrıldığı Durumlar:

- **Geçici Nesnelerden Başlatma:** Bir nesne, geçici bir değer (bir `rvalue`) kullanılarak ilk değer ataması ile oluşturulduğunda.

  ```cpp
  my_class gecici_uret(); // Geçici bir my_class nesnesi döndüren fonksiyon
  my_class yeni_nesne = gecici_uret(); // <<< Burada Taşıma Kurucusu çağrılabilir.
                                       // (RVO/NRVO optimizasyonu yoksa)
                                       // gecici_uret'ten dönen geçici nesnenin kaynakları yeni_nesne'ye taşınır.
  ```

- **`std::move` ile Açık Taşıma:** `std::move` fonksiyonu, bir `lvalue` (normal değişken) ifadeyi bir `rvalue`'ye dönüştürerek taşıma semantiğini açıkça tetikler.

  ```cpp
  my_class kaynak_ornek(123); // Normal bir nesne
  my_class hedef_ornek = std::move(kaynak_ornek); // <<< Burada Taşıma Kurucusu çağrılır.
                                                 // kaynak_ornek'in kaynakları hedef_ornek'e taşınır.
                                                 // kaynak_ornek artık "boş" bir durumdadır ve kullanılmamalıdır.
  ```

- **Fonksiyondan Değer Olarak Döndürüldüğünde (RVO/NRVO Yoksa):** Bir fonksiyon, yerel bir nesneyi değer olarak döndürdüğünde (modern derleyicilerdeki RVO/NRVO optimizasyonları bu çağrıyı çoğu zaman atlar).

  ```cpp
  my_class olustur_nesne() {
      my_class temp(5); // Geçici nesne
      return temp; // <<< RVO/NRVO yoksa Taşıma Kurucusu çağrılabilir.
                   // temp'in kaynakları döndürülen değere taşınır.
  }
  my_class ana_nesne = olustur_nesne();
  ```

---

##### Temel Görevi:

- Kaynak nesnenin sahip olduğu dinamik bellek veya diğer kaynakların **sahipliğini yeni nesneye aktarmaktır**.
- Kopyalama yapmaz, bu yüzden **çok daha hızlıdır** ve gereksiz bellek tahsisinden kaçınır.
- Taşınan (kaynak) nesnenin işaretçilerini `nullptr` veya benzeri bir "boş" duruma getirerek, kendi yıkıcısının yanlışlıkla aynı belleği serbest bırakmasını engeller.

```cpp
#include <iostream> // Çıkış için
#include <utility>  // std::move için

class kaynak_yonetici
{
public:
    int* veri_isaretci; // Dinamik bellek gösteren pointer

    // Kurucu
    kaynak_yonetici(int deger = 0) : veri_isaretci(new int(deger))
    {
        std::cout << "Kurucu çağrıldı, adres: " << this << " | Değer: " << *veri_isaretci << '\n';
    }

    // Yıkıcı
    ~kaynak_yonetici()
    {
        if (veri_isaretci != nullptr) // Eğer pointer null değilse serbest bırak
        {
            delete veri_isaretci;
            std::cout << "Yıkıcı çağrıldı, adres: " << this << " | Bellek serbest bırakıldı." << '\n';
        }
        else
        {
            std::cout << "Yıkıcı çağrıldı, adres: " << this << " | Boş nesne (taşınmış/nullptr)." << '\n';
        }
    }

    // Kopyalama Kurucusu
    kaynak_yonetici(const kaynak_yonetici& diger) : veri_isaretci(new int(*diger.veri_isaretci))
    {
        std::cout << "Kopyalama Kurucusu çağrıldı, adres: " << this << " | Kopyalanan adres: " << &diger << '\n';
    }

    // Taşıma Kurucusu
    kaynak_yonetici(kaynak_yonetici&& diger) noexcept
    {
        // Kaynakları çal: Diger nesnenin pointer'ını kendine ata
        veri_isaretci = diger.veri_isaretci;
        // Diger nesnenin pointer'ını null yap ki yıkıcısı belleği serbest bırakmasın
        diger.veri_isaretci = nullptr;

        std::cout << "Taşıma Kurucusu çağrıldı, adres: " << this << " | Taşınan kaynak adresi: " << &diger << '\n';
    }
};

// Fonksiyon: Geçici bir nesne döndürür
kaynak_yonetici nesne_dondur(int deger)
{
    kaynak_yonetici temp(deger); // Kurucu çağrılır
    std::cout << "Fonksiyon içi temp nesnesi: " << *temp.veri_isaretci << '\n';
    return temp; // <<< Burada Taşıma Kurucusu çağrılabilir (RVO/NRVO yoksa)
}

int main()
{
    std::cout << "==========>| Main Başlangıç |<==========\n\n";

    std::cout << "--- Nesne Oluşturma (Fonksiyondan Dönüş) ---\n";
    kaynak_yonetici ana_nesne = nesne_dondur(42); // Taşıma Kurucusu potansiyel olarak burada çağrılır
    std::cout << "Ana nesne değeri: " << *ana_nesne.veri_isaretci << "\n\n";

    std::cout << "--- std::move ile Açık Taşıma ---\n";
    kaynak_yonetici kaynak_ornek(99); // Kurucu çağrılır
    std::cout << "Kaynak örnek değeri: " << *kaynak_ornek.veri_isaretci << '\n';

    kaynak_yonetici hedef_ornek = std::move(kaynak_ornek); // <<< Taşıma Kurucusu çağrılır
    std::cout << "Hedef örnek değeri: " << *hedef_ornek.veri_isaretci << '\n';
    // kaynak_ornek artık taşındığı için kullanılmamalıdır!

    std::cout << "\n==========>| Main Bitiş |<==========\n\n";
    // main'den çıkışta nesnelerin yıkıcıları çağrılır
}
```

---

#### Taşıma Ataması (`Move Assignment`)

Zaten var olan bir nesnenin değerini, **geçici (`rvalue`) bir nesneden** kaynakları "çalarak" güncelleyen bir atama operatörüdür. `operator=` şeklinde tanımlanır, ancak `rvalue` referansı (`&&`) alır. **Taşıma Ataması bir `constructor` değildir!**

```cpp
sinif_adi& operator=(sinif_adi&& diger_nesne) noexcept;
```

- **`sinif_adi&`:** Atama sonrası sol operandın referansını döndürür. Zincirleme atamalara olanak tanır.
- **`sinif_adi&& diger_nesne`:** Taşınacak geçici (veya `std::move` ile dönüştürülmüş) nesnenin `rvalue` referansı. `const` değildir çünkü kaynakları taşımak için değiştirilecektir.
- **`noexcept`:** Bu operatörün istisna atmayacağını belirtir. Güvenli taşıma için kritik öneme sahiptir.

---

##### Taşıma Atamasının Çağrıldığı Durumlar:

- **`std::move` ile Mevcut Nesneler Arasında Atama:** Bir nesneye, `std::move` ile bir `rvalue`'ye dönüştürülmüş başka bir mevcut nesne atandığında.

  ```cpp
  my_class kaynak_nesne(10); // Mevcut bir nesne
  my_class hedef_nesne(20);  // Mevcut başka bir nesne

  hedef_nesne = std::move(kaynak_nesne); // <<< Burada Taşıma Ataması Operatörü çağrılır.
                                        // kaynak_nesne'nin kaynakları hedef_nesne'ye taşınır.
                                        // kaynak_nesne artık "boş" durumdadır ve kullanılmamalıdır.
  ```

- **Geçici Nesnelerden Atama:** Bir ifadenin sonucu olan geçici bir nesne, mevcut bir nesneye atandığında.

  ```cpp
  my_class bir_fonksiyon_uret(int deger) {
      return my_class(deger); // Geçici nesne döner
  }

  my_class mevcut_nesne;
  mevcut_nesne = bir_fonksiyon_uret(50); // <<< Burada Taşıma Ataması çağrılabilir.
                                       // bir_fonksiyon_uret'ten dönen geçici nesne,
                                       // mevcut_nesne'ye atanırken kaynakları taşınır.
  ```

---

##### Temel Görevi:

- Hedef nesnenin sahip olduğu mevcut kaynakları (belleği) serbest bırakmak.
- Taşınacak kaynak nesnedeki dinamik bellek veya diğer kaynakların **sahipliğini hedef nesneye devretmektir**.
- Taşınan (kaynak) nesnenin işaretçilerini `nullptr` veya benzeri bir "boş" duruma getirerek, kendi yıkıcısının yanlışlıkla aynı belleği serbest bırakmasını engellemektir.
- Kopyalama yapmadığı için **çok daha hızlıdır** ve bellek tahsis/serbest bırakma maliyetlerini azaltır.
- "Kendine atama" (self-assignment) durumunu (`a = std::move(a);` gibi) güvenli bir şekilde ele almalıdır.

```cpp
#include <iostream> // Çıkış için
#include <utility>  // std::move ve std::swap için

class kaynak_yonetici
{
public:
    int* veri_isaretci; // Dinamik bellek gösteren pointer

    // Kurucu
    kaynak_yonetici(int deger = 0) : veri_isaretci(new int(deger))
    {
        std::cout << "Kurucu çağrıldı, adres: " << this << " | Değer: " << *veri_isaretci << '\n';
    }

    // Yıkıcı
    ~kaynak_yonetici()
    {
        if (veri_isaretci != nullptr) // Eğer pointer null değilse serbest bırak
        {
            delete veri_isaretci;
            std::cout << "Yıkıcı çağrıldı, adres: " << this << " | Bellek serbest bırakıldı." << '\n';
        }
        else
        {
            std::cout << "Yıkıcı çağrıldı, adres: " << this << " | Boş nesne (taşınmış/nullptr)." << '\n';
        }
    }

    // Kopyalama Kurucusu
    kaynak_yonetici(const kaynak_yonetici& diger) : veri_isaretci(new int(*diger.veri_isaretci))
    {
        std::cout << "Kopyalama Kurucusu çağrıldı, adres: " << this << " | Kopyalanan adres: " << &diger << '\n';
    }

    // Kopyalama Ataması
    kaynak_yonetici& operator=(const kaynak_yonetici& diger)
    {
        std::cout << "Kopyalama Ataması çağrıldı, mevcut adres: " << this << " | Atanan adres: " << &diger << '\n';
        if (this == &diger) return *this;
        delete veri_isaretci;
        veri_isaretci = new int(*diger.veri_isaretci);
        return *this;
    }

    // Taşıma Kurucusu
    kaynak_yonetici(kaynak_yonetici&& diger) noexcept
    {
        veri_isaretci = diger.veri_isaretci;
        diger.veri_isaretci = nullptr;
        std::cout << "Taşıma Kurucusu çağrıldı, adres: " << this << " | Taşınan kaynak adresi: " << &diger << '\n';
    }

    // Taşıma Ataması Operatörü
    kaynak_yonetici& operator=(kaynak_yonetici&& diger) noexcept
    {
        std::cout << "Taşıma Ataması çağrıldı, mevcut adres: " << this << " | Taşınan kaynak adresi: " << &diger << '\n';

        // Kendi kendine atama kontrolü
        if (this == &diger)
        {
            std::cout << "Kendi kendine taşıma ataması! İşlem yapılmadı." << '\n';
            return *this;
        }

        // Mevcut belleği serbest bırak
        delete veri_isaretci;

        // Kaynakları çal: Diger nesnenin pointer'ını kendine ata
        veri_isaretci = diger.veri_isaretci;
        // Diger nesnenin pointer'ını null yap
        diger.veri_isaretci = nullptr;

        return *this; // Zincirleme atamalar için kendi referansını döndür
    }
};

int main()
{
    std::cout << "==========>| Main Başlangıç |<==========\n\n";

    kaynak_yonetici nesne_a(10); // Kurucu çağrıldı
    kaynak_yonetici nesne_b(20); // Kurucu çağrıldı

    std::cout << "\n--- Taşıma Ataması Öncesi ---" << std::endl;
    std::cout << "Nesne A (adres: " << &nesne_a << ") Veri: " << *nesne_a.veri_isaretci << std::endl;
    std::cout << "Nesne B (adres: " << &nesne_b << ") Veri: " << *nesne_b.veri_isaretci << std::endl;

    std::cout << "\n--- nesne_a = std::move(nesne_b) ---" << std::endl;
    nesne_a = std::move(nesne_b); // <<< Taşıma Ataması Operatörü çağrılır

    std::cout << "\n--- Taşıma Ataması Sonrası ---" << std::endl;
    std::cout << "Nesne A (adres: " << &nesne_a << ") Veri: " << *nesne_a.veri_isaretci << std::endl;
    std::cout << "Nesne B (taşınmış): Artık kullanılmamalıdır! Pointer: " << nesne_b.veri_isaretci << '\n';

    // Kendi kendine taşıma ataması örneği
    std::cout << "\n--- Kendi kendine taşıma ataması (nesne_a = std::move(nesne_a)) ---" << std::endl;
    nesne_a = std::move(nesne_a); // Kendi kendine atama kontrolü devrede

    std::cout << "\n==========>| Main Bitiş |<==========\n\n";
    // main'den çıkışta nesnelerin yıkıcıları çağrılır
}
```

-----

### Derleyici Hangi Durumlarda Sınıfın Special Member Function'ları Yazar?

Derleyicinin otomatik olarak yazabileceği özel üye fonksiyonları şunlardır:

  * **Varsayılan Kurucu (`Default Constructor`)**: Parametre almayan kurucu.
  * **Yıkıcı (`Destructor`)**: Nesne yok edildiğinde çağrılan fonksiyon.
  * **Kopyalama Kurucusu (`Copy Constructor`)**: Bir nesnenin başka bir nesneden kopyalanarak oluşturulması.
  * **Kopyalama Atama Operatörü (`Copy Assignment Operator`)**: Mevcut bir nesneye başka bir mevcut nesnenin değerlerinin atanması.
  * **Taşıma Kurucusu (`Move Constructor`)**: Bir nesnenin, geçici bir nesnenin kaynakları "çalınarak" oluşturulması (C++11 ve sonrası).
  * **Taşıma Atama Operatörü (`Move Assignment Operator`)**: Mevcut bir nesneye, geçici bir nesnenin kaynaklarının atanması (C++11 ve sonrası).

### Derleyicinin Özel Üye Fonksiyonlarını Otomatik Yazma Kuralları

-----

| Özel Üye Fonksiyonu       | Derleyici Ne Zaman Otomatik Yazar?                                                                                              | Derleyici Ne Zaman **YAZMAZ**? (veya `delete` olarak işaretler)                                                                                                                                                                                                                                                |
| :------------------------ | :------------------------------------------------------------------------------------------------------------------------------ | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Varsayılan Kurucu** | - Hiçbir kurucu tanımlamazsanız.                                                                                                | - **Herhangi bir kurucu** (parametreli, kopyalama, taşıma) tanımlarsanız.  - Bazı üye değişkenleri varsayılan olarak başlatılamıyorsa (örn: `const` veya referans üyeler).                                                                                                                                 |
| **Yıkıcı** | - Siz tanımlamazsanız.                                                                                                         | - Siz tanımlarsanız.                                                                                                                                                                                                                                                                                         |
| **Kopyalama Kurucusu** | - Siz tanımlamazsanız.  - **Taşıma kurucusu veya taşıma atama operatörü tanımlamazsanız.** | - Siz tanımlarsanız.  - **Taşıma kurucusu VEYA taşıma atama operatörü tanımlarsanız.** (C++11 sonrası) - Sınıfın `const` veya referans türünde üyeleri varsa.                                                                                                                                         |
| **Kopyalama Atama Op.** | - Siz tanımlamazsanız.  - **Taşıma kurucusu veya taşıma atama operatörü tanımlamazsanız.** | - Siz tanımlarsanız.  - **Taşıma kurucusu VEYA taşıma atama operatörü tanımlarsanız.** (C++11 sonrası) - Sınıfın `const` veya referans türünde üyeleri varsa.                                                                                                                                         |
| **Taşıma Kurucusu** | - Siz tanımlamazsanız.  - **Hiçbir Kopyalama Kurucusu, Kopyalama Atama Operatörü veya Yıkıcı tanımlamazsanız.** | - Siz tanımlarsanız.  - **Kopyalama kurucusu, Kopyalama atama operatörü VEYA Yıkıcı tanımlarsanız.**  - Sınıfın yıkıcısı `noexcept` değilse (potansiyel olarak `deleted` olur).                                                                                                                           |
| **Taşıma Atama Op.** | - Siz tanımlamazsanız.  - **Hiçbir Kopyalama Kurucusu, Kopyalama Atama Operatörü veya Yıkıcı tanımlamazsanız.** | - Siz tanımlarsanız.  - **Kopyalama kurucusu, Kopyalama atama operatörü VEYA Yıkıcı tanımlarsanız.**  - (Ek kısıtlamalar da olabilir, örn: `const` üyeler genellikle taşıma atamayı engeller.)                                                                                                          |

-----

```cpp
// my_class: Rule of Zero örneği
// Sınıf içerisinde hiçbir özel üye fonksiyonu tanımlamadığınızda,
// derleyici tüm varsayılan özel üye fonksiyonlarını (kurucu, yıkıcı,
// kopya kurucu, kopya atama, taşıma kurucu, taşıma atama) otomatik olarak tanımlar.
// = default; kullanarak da açıkça derleyiciye oluşturmasını söylemiş oluruz.
class my_class
{
    public:
        my_class()                           = default; // -> Constructor
        ~my_class()                          = default; // -> Desturctor
        my_class(const my_class&)            = default; // -> Copy Constructor
        my_class(my_class&&)                 = default; // -> Move Constructor
        my_class& operator=(const my_class&) = default; // -> Copy Assignment
        my_class& operator=(my_class&&)      = default; // -> Move Assignment
};

// my_class_2: Parametreli kurucu tanımlandığında
// Kullanıcı tarafından parametreli bir kurucu (my_class_2(int)) tanımlandığında,
// derleyici artık varsayılan kurucuyu otomatik olarak oluşturmaz.
// Diğer tüm özel üye fonksiyonları (yıkıcı, kopya/taşıma kurucusu/atama) otomatik olarak oluşturulur.
class my_class_2
{
    public:
        my_class(int); 
        /* ---| Derleyici Tarafından Oluşturulacak Fonksiyonlar |---
        ~my_class()                          = default; // -> Desturctor
        my_class(const my_class&)            = default; // -> Copy Constructor
        my_class(my_class&&)                 = default; // -> Move Constructor
        my_class& operator=(const my_class&) = default; // -> Copy Assignment
        my_class& operator=(my_class&&)      = default; // -> Move Assignment
        */
};

// my_class_3: Varsayılan kurucu tanımlandığında
// Kullanıcı tarafından varsayılan kurucu (my_class_3()) tanımlandığında,
// derleyici yine de diğer tüm özel üye fonksiyonları (yıkıcı, kopya/taşıma kurucusu/atama) otomatik olarak oluşturur.
class my_class_3
{
    public:
        my_class(); 
        /* ---| Derleyici Tarafından Oluşturulacak Fonksiyonlar |---
        ~my_class()                          = default; // -> Desturctor
        my_class(const my_class&)            = default; // -> Copy Constructor
        my_class(my_class&&)                 = default; // -> Move Constructor
        my_class& operator=(const my_class&) = default; // -> Copy Assignment
        my_class& operator=(my_class&&)      = default; // -> Move Assignment
        */
};

// my_class_4: Yıkıcı tanımlandığında (Rule of Three/Five Etkisi)
// Kullanıcı tarafından bir yıkıcı tanımlandığında,
// C++11 ve sonrası standartlarında, derleyici Taşıma Kurucusu ve Taşıma Atama Operatörünü otomatik olarak oluşturmaz.
// Ancak Varsayılan Kurucu, Kopyalama Kurucusu ve Kopyalama Atama Operatörünü hala otomatik olarak oluşturur (eğer başka bir kısıt yoksa).
class my_class_4
{
    public:
        ~my_class(); 
        /* ---| Derleyici Tarafından Oluşturulacak Fonksiyonlar |---
        my_class()                           = default; // -> Constructor
        my_class(const my_class&)            = default; // -> Copy Constructor
        my_class& operator=(const my_class&) = default; // -> Copy Assignment
        */
};

// my_class_5: Kopyalama Kurucusu tanımlandığında (Rule of Three/Five Etkisi)
// Kullanıcı tarafından Kopyalama Kurucusu tanımlandığında,
// derleyici Taşıma Kurucusu ve Taşıma Atama Operatörünü otomatik olarak oluşturmaz.
// Ayrıca, herhangi bir kurucu tanımlandığı için Varsayılan Kurucuyu da otomatik oluşturmaz.
// Yıkıcı ve Kopyalama Atama Operatörü hala otomatik olarak oluşturulur.
class my_class_5
{
    public:
        my_class(const my_class&); 
        /* ---| Derleyici Tarafından Oluşturulacak Fonksiyonlar |---
        ~my_class()                          = default; // -> Desturctor
        my_class(const my_class&)            = default; // -> Copy Constructor
        my_class& operator=(const my_class&) = default; // -> Copy Assignment
        */
};

// my_class_6: Kopyalama Atama Operatörü tanımlandığında (Rule of Three/Five Etkisi)
// Kullanıcı tarafından Kopyalama Atama Operatörü tanımlandığında,
// derleyici Taşıma Kurucusu ve Taşıma Atama Operatörünü otomatik olarak oluşturmaz.
// Varsayılan Kurucu, Yıkıcı ve Kopyalama Kurucusu otomatik olarak oluşturulur.
class my_class_6
{
    public:
        my_class& operator=(const my_class&); 
        /* ---| Derleyici Tarafından Oluşturulacak Fonksiyonlar |---
        my_class()                           = default; // -> Constructor
        ~my_class()                          = default; // -> Desturctor
        my_class(const my_class&)            = default; // -> Copy Constructor
        my_class& operator=(const my_class&) = default; // -> Copy Assignment
        */
};

// my_class_7: Taşıma Kurucusu tanımlandığında (Rule of Five Etkisi)
// Kullanıcı tarafından Taşıma Kurucusu tanımlandığında,
// derleyici Kopyalama Kurucusu ve Kopyalama Atama Operatörünü otomatik olarak oluşturmaz.
// Ayrıca, herhangi bir kurucu tanımlandığı için Varsayılan Kurucuyu da otomatik oluşturmaz.
// Sadece Yıkıcı otomatik olarak oluşturulur.
class my_class_7
{
    public:
        my_class (my_class&&); 
        /* ---| Derleyici Tarafından Oluşturulacak Fonksiyonlar |---
        ~my_class()                          = default; // -> Desturctor
        */
};

// my_class_8: Taşıma Atama Operatörü tanımlandığında (Rule of Five Etkisi)
// Kullanıcı tarafından Taşıma Atama Operatörü tanımlandığında,
// derleyici Kopyalama Kurucusu ve Kopyalama Atama Operatörünü otomatik olarak oluşturmaz.
// Varsayılan Kurucu ve Yıkıcı otomatik olarak oluşturulur.
class my_class_8
{
    public:
        my_class& operator=(my_class&&)
        /* ---| Derleyici Tarafından Oluşturulacak Fonksiyonlar |---
        my_class()                           = default; // -> Constructor
        ~my_class()                          = default; // -> Desturctor
        */
};
```

-----

## Ekstra Bilgiler:

### Özel Üye Fonksiyonlar

```cpp
class my_class
{
    public:
        my_class();                             // default ctor
        ~my_class();                            // destructor

        my_class(const my_class&);              // copy ctor
        my_class(my_class&& r)                  // move ctor

        my_class& operator=(const my_class &);  // copy assignment
        my_class& operator=(my_class &&);       // move assignment
}
```

### `std::move` Alternatif Gösterimi

Adına rağmen, `std::move` **hiçbir şeyi hareket ettirmez veya taşımaz** (yani bellek üzerinde bir işlem yapmaz). Asıl görevi, **kendisine verilen bir ifadeyi (genellikle bir `lvalue`), `rvalue` reference'a (`&&`) dönüştürmektir**.

```cpp
class my_class
{
    // ...
};

void func(const my_class&) { std::cout << "const my_class&\n"; }

void func(const my_class&&) { std::cout << "my_class&&\n"; }

int main()
{
    my_class _my_class;
    
    func(_my_class);                          // L-Value parametre ile fonksiyona gönderim
    
    func(static_cast<my_class&&>(_my_class)); // L-Value değeri R-Value'ye cast edip R-Value parametre isteyen fonksiyona gönderim yapıyoruz
    
    func(std::move(_my_class));               // L-Value değeri R-value'ye cast eden bir fonksiyondur std::move()
}
```

---

### Derleyici Ne Zaman Move Constructor (Taşıma Kurucusu) veya Ne Zaman Copy Constructor'ı (Kopyalama Kurucusunu) Seçer?

Derleyici, bir nesnenin ilk değer atamasıyla oluşturulduğu durumlarda (initialization), hangi kurucuyu (Taşıma veya Kopyalama) çağıracağına, **kaynak ifadenin türüne** bakarak karar verir:

- Kaynak İfade Bir `R-value` İse → Taşıma Kurucusu (`Move Constructor`) Çağrılır.
  - `R-value`'ler genellikle geçici nesnelerdir (fonksiyon dönüş değerleri gibi) veya `std::move` ile açıkça taşınabilir hale getirilmiş ifadelerdir. Bu durumda, derleyici kaynakların kopyalanması yerine taşınmasını tercih ederek performansı optimize eder.

- Kaynak İfade Bir `L-value` İse → Kopyalama Kurucusu (`Copy Constructor`) Çağrılır.
  - `L-value`'ler, bellekte belirli bir konumu olan ve genellikle ismi olan kalıcı nesnelerdir (değişkenler gibi). Bu durumda, orijinal nesnenin bozulmadan kalması gerektiği için derleyici **kopyalama** işlemini tercih eder.

---

### Dangling Pointer

Artık geçerli veya ayrılmış bir bellek konumunu işaret etmeyen, ancak yine de o belleğin adresini tutan bir işaretçidir. 

#### Dangling Pointer Gerçekleşme Durumları:

Sallanan göstergeler genellikle üç ana senaryoda ortaya çıkar:

1) **Bellek Serbest Bırakma (Deallocation of Memory):** Bir işaretçinin gösterdiği dinamik olarak ayrılmış bellek (new veya malloc ile), delete veya free() kullanılarak serbest bırakıldığında, ancak işaretçi nullptr olarak ayarlanmadığında oluşur. İşaretçi hala serbest bırakılmış belleğin adresini tutar.

```cpp
int* sayi_ptr = new int(10);
delete sayi_ptr; // Bellek serbest bırakıldı
// sayi_ptr hala 10'un önceki adresini tutuyor, bu bir sallanan göstergedir.
// sayi_ptr = nullptr; // Sallanan gösterge olmasını engellemek için bu satır eklenmeli
```

2) **Yerel Değişken Adresi Döndürme:** Bir fonksiyonun yerel bir değişkeninin adresi döndürüldüğünde meydana gelir. Fonksiyonun yürütülmesi bittiğinde, yerel değişken yığın bellekten silinir ve döndürülen işaretçi artık geçerli olmayan bir bellek konumunu gösterir.

```cpp
int* yerel_adres_dondur() 
{
    int yerel_sayi = 42;    // Yerel değişken
    return &yerel_sayi;     // Fonksiyon bittiğinde 'yerel_sayi' silinecek
}
// main fonksiyonunda:
// int* sarkan_ptr = yerel_adres_dondur(); // sarkan_ptr bir sallanan göstergedir
```

3) **Kapsam Dışına Çıkma (Going Out of Scope):** Bir işaretçi, kendisinden daha dar bir kapsamda tanımlanmış bir değişkene işaret ettiğinde ve bu değişken kapsam dışına çıktığında oluşur.

```cpp
int* dis_kapsam_ptr;
{
    int ic_kapsam_sayi = 100;           // İç kapsamda tanımlı değişken
    dis_kapsam_ptr = &ic_kapsam_sayi;   // İşaretçi ic_kapsam_sayi'nin adresini tutar
} // ic_kapsam_sayi kapsam dışına çıkar ve silinir
// dis_kapsam_ptr artık bir sallanan göstergedir.
```

-----

#### Engelleme Yöntemleri:

- **Bellek Serbest Bırakıldıktan Sonra İşaretçiyi ```nullptr``` Yapın:** Dinamik belleği ```delete``` ile serbest bıraktıktan hemen sonra, ilgili işaretçiyi ```nullptr``` olarak atamak en temel ve etkili yöntemdir. ```nullptr```'a atama, işaretçinin artık geçerli bir adresi göstermediğini açıkça belirtir ve ```nullptr```'ı referans almak derleme/çalışma zamanı hatası verir, böylece potansiyel hataları erken yakalamanıza yardımcı olur.

```cpp
int* veri = new int(5);
// ... kullan
delete veri;
veri = nullptr; // ÖNEMLİ: Sallanan göstergeyi engelle
```

- **Yerel Değişkenlerin Adreslerini Döndürmeyin:** Fonksiyonlardan yerel değişkenlerin adreslerini döndürmekten kaçının. Eğer dinamik belleğe ihtiyacınız varsa, bunu fonksiyon içinde new ile ayırın ve döndürün (tercihen akıllı işaretçi ile).

- **Kapsam Yönetimine Dikkat Edin:** Değişkenlerin ömrünü ve kapsamını iyi anlayın. Bir işaretçiyi, referans aldığı değişken kapsam dışına çıktıktan sonra kullanmaktan kaçının.

- **Referansları Kullanın:** Mümkün olduğunda, sadece bir değeri okumanız veya değiştirmeniz gerekiyorsa işaretçiler yerine referansları kullanın. Referanslar asla "dangling" olamaz (null olamazlar ve her zaman geçerli bir nesneye bağlı olmalıdırlar).


-----

## Mülakat Soruları ve Cevapları: Kopya ve Taşıma Semantiği

1) **Soru:** Aşağıdaki `func(std::move(_my_class));` satırı çalıştırıldığında Move'mu yoksa Copy Constructor mı çağırılır?

```cpp
class my_class
{
public:
    my_class(); // Constructor

    my_class(const my_class&){ std::cout << "Copy Ctor Called!\n"; } // Copy Constructor

    my_class(my_class&&){ std::cout << "Move Ctor Called!\n"; } // Move Constructor
};

void func(const my_class&) { std::cout << "const my_class&\n"; }

void func(const my_class&&) { std::cout << "my_class&&\n"; }

int main()
{
    my_class _my_class;

    func(std::move(_my_class));
}
```

  - **Cevap:** **Her ikisi de çağrılmaz\!**

    **Açıklama:** `func`'ın her iki aşırı yüklemesi de parametreyi `const` olarak almaktadır (`const my_class&` ve `const my_class&&`). `std::move(_my_class)` ifadesi bir `my_class&&` (rvalue referansı) üretir. Ancak, bu `rvalue` referansı `const` olduğu için (fonksiyon imzası `const my_class&&`), içindeki kaynakların taşınmasına (değiştirilmesine) izin verilmez. Taşıma kurucusu ise parametresini `const` almadığı (`my_class&&`) için eşleşmez.

    Bu durumda, derleyici `const my_class&&` parametresine sahip `func` aşırı yüklemesini seçecektir. Ancak bu fonksiyon sadece bir referansı kabul eder, yeni bir `my_class` nesnesi oluşturmaz. Bu nedenle, herhangi bir kurucu (`Copy Ctor` veya `Move Ctor`) çağrılmaz.

---

2) **Soru:** Aşağıdaki `func(std::move(_my_class));` satırı çalıştırıldığında Move'mu yoksa Copy Constructor mı çağırılır?

```cpp
class my_class
{
public:
    my_class() = default; // Default init edilmiş ctor

    my_class(const my_class&){ std::cout << "Copy Ctor Called!\n"; } // Copy Constructor

    my_class(my_class&&){ std::cout << "Move Ctor Called!\n"; } // Move Constructor
};

void func(const my_class&) { std::cout << "const my_class&\n"; }

void func(const my_class&&) { std::cout << "my_class&&\n"; }

int main()
{
    my_class _my_class;

    func(std::move(_my_class));
}
```

  - **Cevap:** **Her ikisi de çağrılmaz\!** `func(const my_class&&)` fonksiyonu çağrılır.

    **Açıklama:** Bu soru bir öncekiyle aynıdır ve aynı mantık geçerlidir. `std::move(_my_class)` bir `my_class&&` (rvalue referansı) üretir. `func` fonksiyonunun iki aşırı yüklemesi arasında, `func(const my_class&&)` daha iyi bir eşleşmedir çünkü `rvalue` referansı `const rvalue` referansına dönüştürülebilir.

    Yine de, `func` fonksiyonu sadece bir referans alır ve yeni bir `my_class` nesnesi yaratmaz. Dolayısıyla, **hiçbir kurucu çağrılmaz**. Sadece `func(const my_class&&)`'nin içine yazılan metin ekrana basılır.

---

3) **Soru:** Aşağıdaki `my_class &&r = std::move(m);` satırı çalıştırıldığında Move Constructor çağırılır mı?

```cpp
class my_class
{
public:
    my_class() = default; // Default init edilmiş ctor

    my_class(const my_class&){ std::cout << "Copy Ctor Called!\n"; } // Copy Constructor

    my_class(my_class&&){ std::cout << "Move Ctor Called!\n"; } // Move Constructor
};

int main()
{
    my_class _my_class;

    my_class &&r = std::move(_my_class);
}
```

  - **Cevap:** **Çağrılmaz\!**

    **Açıklama:** `my_class &&r = std::move(_my_class);` satırı bir nesne oluşturma işlemi değildir. Bu satırda, `std::move(_my_class)` ile `_my_class`'ın bir `rvalue` referansı elde edilir ve bu `rvalue` referansı, **`r` adlı bir başka `rvalue` referansına bağlanır**. Yani, `r` sadece `_my_class` nesnesine bir takma ad (alias) olarak işlev görür. Bellekte yeni bir `my_class` nesnesi yaratılmadığı için ne Kopyalama ne de Taşıma Kurucusu çağrılır.

---

4) **Soru:** Aşağıdaki `foo(std::move(_my_class));` satırı çalıştığında `func(const my_class&)` fonksiyonu mu çağrılır yoksa `func(const my_class&&)` fonksiyonu mu çağrılır?

```cpp
class my_class
{
public:
};

void func(const my_class&) { std::cout << "const my_class&\n"; }

void func(const my_class&&) { std::cout << "my_class&&\n"; }

void foo(my_class&& r)
{
    func(r); // <<< Buraya dikkat!
}

int main()
{
    my_class _my_class;

    foo(std::move(_my_class));
}
```

  - **Cevap:** `func(const my_class&)` fonksiyonu çağrılır.

    **Açıklama:** `foo(my_class&& r)` fonksiyonunun içinde `r` bir **isimlendirilmiş `rvalue` referanstır**. C++'ta, isimlendirilmiş `rvalue` referansları dahi **`lvalue` olarak kabul edilirler**. Yani, `func(r)` ifadesindeki `r`, bir `lvalue` ifadedir.

    Bu nedenle, `func`'ın aşırı yüklemeleri arasında, `lvalue` referansı alan `func(const my_class&)` fonksiyonu çağrılacaktır. Bu, C++'ın "isimler `lvalue`'dir" kuralının bir sonucudur. Bir `rvalue` referansını taşımaya devam etmek isterseniz, `func(std::move(r));` şeklinde açıkça `std::move` kullanmanız gerekirdi.

---

5) **Soru:** `foo(parametre)` fonksiyonunun içi öyle bir oluşturulmalıdır ki `foo()`'ya gönderilen ifade L-Value ise buradaki nesneyi Copy Constructor ile eğer gönderilen ifade R-Value ise Move Constructor ile oluşacak. Gerek kodu yazın.

```cpp
class my_class
{
public:
    my_class() = default; // Default init edilmiş ctor

    my_class(const my_class&){ std::cout << "Copy Ctor Called!\n"; } // Copy Constructor

    my_class(my_class&&){ std::cout << "Move Ctor Called!\n"; } // Move Constructor
};

void foo(parametre) // <<< Burası doldurulacak
{
    // ...
}

int main()
{
    my_class _my_class;

    foo(_my_class); // Copy Constructor çağrılmalı

    foo(std::move(_my_class)); // Move Constructor çağrılmalı
}
```

  - **Cevap:** Bu durumu elde etmek için **fonksiyon aşırı yüklemesi (`function overloading`)** kullanmamız gerekir.

    **Kod:**

    ```cpp
    #include <iostream>
    #include <utility> // std::move için

    class my_class
    {
    public:
        my_class() = default; // Default init edilmiş ctor

        my_class(const my_class&){ std::cout << "Copy Ctor Called!\n"; } // Copy Constructor

        my_class(my_class&&){ std::cout << "Move Ctor Called!\n"; }     // Move Constructor
    };

    // L-Value ifadeler için aşırı yükleme: Parametreyi const lvalue referans olarak alır
    void foo(const my_class &param)
    {
        std::cout << "foo(const my_class&) çağrıldı\n";
        // Param bir lvalue olduğu için, 'param'dan yeni bir nesne oluştururken Copy Constructor çağrılır.
        my_class yeni_nesne(param);
    }

    // R-Value ifadeler için aşırı yükleme: Parametreyi rvalue referans olarak alır
    void foo(my_class &&param)
    {
        std::cout << "foo(my_class&&) çağrıldı\n";
        // Param artık bir isimlendirilmiş rvalue referansı olduğu için lvalue'dir.
        // Onu yeniden bir rvalue'ye dönüştürmek için std::move kullanmalıyız,
        // böylece Move Constructor çağrılır.
        my_class yeni_nesne(std::move(param));
    }

    int main()
    {
        std::cout << "==========>| Main Başlangıç |<==========\n\n";

        my_class _my_class; // Default ctor çağrılır

        std::cout << "\n--- foo(_my_class) Çağrısı ---\n";
        foo(_my_class); // <<< Copy Constructor çağırılmalı (param: const my_class&)

        std::cout << "\n--- foo(std::move(_my_class)) Çağrısı ---\n";
        foo(std::move(_my_class)); // <<< Move Constructor çağırılmalı (param: my_class&&)

        std::cout << "\n==========>| Main Bitiş |<==========\n\n";
    }
    ```

    **Açıklama:**

      * `foo(const my_class &param)` aşırı yüklemesi, `_my_class` gibi bir **`lvalue`** argümanla çağrıldığında seçilir. Fonksiyonun içinde `my_class yeni_nesne(param);` ifadesi, `param` bir `lvalue` olduğu için **Kopyalama Kurucusu**'nu tetikler.
      * `foo(my_class &&param)` aşırı yüklemesi, `std::move(_my_class)` gibi bir **`rvalue`** argümanla çağrıldığında seçilir. Fonksiyonun içinde `my_class yeni_nesne(std::move(param));` ifadesi kullanılır. Burada `std::move(param)` kritik öneme sahiptir çünkü `param` her ne kadar bir `rvalue` referansı olsa da, **ismi olduğu için fonksiyon içinde bir `lvalue` gibi davranır**. `std::move` kullanarak onu tekrar bir `rvalue`'ye dönüştürür ve böylece **Taşıma Kurucusu**'nun çağrılmasını sağlarız.

---

6) **Soru:** Bir nesnenin **move constructor** ile, **copy constructor** ile hayata getirilmesindeki fark nedir?

  - **Cevap:** Kaynakların ele alınma biçimindeki farktır.

    **Açıklama:**

      * **Kopyalama Kurucusu:** Kaynak nesnedeki tüm verinin **yeni bir kopyasını** oluşturur. Maliyetlidir, bağımsız iki nesne oluşur.

      * **Taşıma Kurucusu:** Kaynak nesnedeki dinamik bellek gibi **kaynakların sahipliğini aktarır** (kopyalamaz). Çok daha hızlıdır. Kaynak nesne taşındıktan sonra genellikle "boş" veya geçersiz durumda bırakılır.

    Kısaca: **Kopyalama veriyi çoğaltır; Taşıma sahipliği aktarır.**

-----