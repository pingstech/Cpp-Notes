# 12.Ders

## Class

### ```delete``` Bildirimi:

C++'ta ```delete``` bildirimi, dinamik olarak tahsis edilmiş belleği serbest bırakmak için kullanılan kritik bir operatördür. ```new``` operatörü ile tahsis edilmiş belleği işletim sistemine geri verir ve bu bellek alanının başka programlar veya sizin programınızın diğer kısımları tarafından tekrar kullanılabilmesini sağlar. Bu, **bellek sızıntılarını (memory leak)** önlemek için hayati öneme sahiptir.

#### ```delete``` Nasıl Kullanılır:

```delete``` operatörü, ```new``` ile tahsis edilmiş belleğin adresini alan bir işaretçi (pointer) ile kullanılır.

- **Tek Bir Nesne İçin Kullanım:**

    ```new``` ile tek bir nesne için bellek tahsis ettiyseniz, ```delete``` ile serbest bırakırsınız.

    ```cpp
    int main() 
    {
        int* sayi_ptr = new int; // Dinamik olarak bir tamsayı için bellek tahsis et

        *sayi_ptr = 42; // Tahsis edilen belleğe değer ata

        std::cout << "Bellekteki değer: " << *sayi_ptr << std::endl;

        delete sayi_ptr; // Tahsis edilen belleği serbest bırak

        // sayi_ptr artık geçersiz bir adresi gösterir.
        // Erişmeye çalışmak tanımsız davranışa (undefined behavior) yol açar.
        // sayi_ptr = nullptr; // İyi bir pratik: serbest bırakılan işaretçiyi nullptr'a eşitlemek.
    }
    ```

- **Dizi İçin Kullanım (```delete[]```):**

    ```new[]``` ile bir dizi için bellek tahsis ettiyseniz, ```delete[]``` kullanarak serbest bırakmanız zorunludur. Sadece ```delete``` kullanırsanız, bu **tanımsız davranışa** yol açar ve genellikle sadece dizinin ilk elemanı için yıkıcı çağrılır, bu da **bellek sızıntılarına** neden olur.

    ```cpp
    int main() 
    {
        int* sayilar_dizisi = new int[5]; // 5 adet tamsayı için bellek tahsis et

        for (int i = 0; i < 5; ++i) {
            sayilar_dizisi[i] = (i + 1) * 10;
        }

        std::cout << "Dizi elemanları: ";
        for (int i = 0; i < 5; ++i) {
            std::cout << sayilar_dizisi[i] << " ";
        }
        std::cout << std::endl;

        delete[] sayilar_dizisi; // Dizi için tahsis edilen belleği serbest bırak

        // sayilar_dizisi = nullptr; // İyi bir pratik
    }
    ```

#### ```delete``` Bildiriminin Ardından Ne Olur?

1) **Yıkıcı Fonksiyon Çağrısı:** Eğer serbest bırakılan bellek bir sınıf nesnesi içeriyorsa, ```delete``` operatörü önce nesnenin yıkıcı fonksiyonunu (```~SınıfAdı()```) çağırır. Bu, nesnenin sahip olduğu tüm kaynakların (dinamik bellek, açık dosyalar vb.) düzgün bir şekilde serbest bırakılmasını sağlar. Diziler için ```delete[]``` kullanıldığında, dizideki her nesne için yıkıcı çağrılır.

2) **Bellek Serbest Bırakma:** Yıkıcı çağrıldıktan sonra, ```delete``` operatörü belleği işletim sistemine geri verir. Bu bellek artık programınız tarafından kullanılmamalıdır.

---

#### Dikkat Edilmesi Gerekenler ve Yaygın Hatalar:

- **Çift Serbest Bırakma (Double Deletion):** Aynı bellek bloğunu birden fazla kez ```delete``` etmek tanımsız davranıştır. Bu genellikle programın çökmesine yol açar. Bunu önlemek için, ```delete``` ettikten sonra işaretçiyi ```nullptr```'a (```NULL``` C++98'de) eşitlemek iyi bir pratiktir.

    ```cpp
    int* ptr = new int;
    delete ptr;
    // delete ptr; // HATA: Çift serbest bırakma!
    ptr = nullptr; // Bu satır, double deletion hatasını engellemeye yardımcı olur.
    ```

- **Boş İşaretçiyi ```delete``` Etmek:** Bir ```nullptr``` işaretçisini ```delete``` etmek güvenlidir ve herhangi bir etkiye sahip değildir.

- **Tahsis Edilmemiş Belleği ```delete``` Etmek:** ```new``` ile tahsis edilmemiş bir bellek adresini veya statik/otomatik belleği ```delete``` etmek tanımsız davranıştır.

    ```cpp
    int a = 10;
    // delete &a; // HATA: 'a' statik/otomatik bellekte.
    ```

- **```new``` ve ```delete[]``` Karışıklığı:** Tek bir nesne için ```new``` kullanıp ```delete[]``` kullanmak veya bir dizi için ```new[]``` kullanıp delete kullanmak tanımsız davranıştır ve genellikle bellek sızıntılarına veya çökmelere yol açar.

---

### Special Member Functions (Sınıfın Özel Üye Fonksiyonları)

**Özel üye fonksiyonları** sınıf tabanlı programlamanın anlaşılması gereken en kritik konularından biridir. Bu fonksiyonlar, nesnelerin **yaşam döngüsünü** yönetmek ve beklenmedik davranışları önlemek için derleyici tarafından otomatik olarak oluşturulabilir veya sizin tarafınızdan açıkça tanımlanabilir.

1) #### Constructor (Yapıcı Fonksiyonlar)

    Bir sınıfın nesnesi oluşturulduğunda otomatik olarak çağrılan özel üye fonksiyonudur. Nesnenin ilk durumunu (başlangıç değerlerini) ayarlamak için kullanılır.

    **Erişim**
    - Kurucu fonksiyonlar, sınıfın adıyla aynı isme sahiptir ve **geri dönüş tipi belirtilmez.**
    - Genellikle ```public``` erişim belirteci altında tanımlanırlar, böylece sınıf dışından nesneler oluşturulabilir.

    ```cpp
    class Oyuncu 
    {
        public:
            int can_sayisi; // Can sayısını tutan değişken

            // Varsayılan kurucu: Eğer bu kurucuyu yazmazsak ve başka kurucu da olmazsa derleyici otomatik oluşturur.
            // Eğer parametreli bir kurucu tanımlasaydık ve bu kurucuyu tanımlamasaydık
            // 'Oyuncu oyuncu_bir;' şeklinde nesne oluşturamazdık.
            Oyuncu() 
            {
                can_sayisi = 100; // Nesne oluşturulduğunda can_sayisi'nı 100 olarak başlatırız.
                // std::cout << "Varsayılan kurucu çağrıldı!" << std::endl; // Debug amaçlı
            }

            // Explicitly defaulted default constructor (açıkça varsayılan kurucu)
            // Oyuncu() = default; // Bu şekilde de varsayılan kurucuyu belirtebiliriz.
    };

    int main() 
    {
        Oyuncu oyuncu_bir; // Varsayılan kurucu çağrılır.
        // oyuncu_bir.can_sayisi değeri 100 olacaktır.
        return 0;
    }
    ```

---

2) #### Destructor (Yıkıcı Fonksiyonlar)

    Sınıf nesnesinin ömrü bittiğinde otomatik çağrılır. Syntax Hatası: Parametre alamaz ve overload edilemez. Nesnenin kullanmadığı kaynakları (bellek, dosya tanıtıcıları vb.) serbest bırakmak için kullanılır.

    **Erişim:**

    - Yıkıcı fonksiyonun adı, sınıf adının önüne ```~``` (tilde) işareti eklenerek oluşturulur.
    - **Hiçbir parametre almaz ve geri dönüş tipi belirtmez.**
    - Genellikle ```public``` erişim belirteci altında tanımlanır.
    - Bir sınıf için **yalnızca bir yıkıcı fonksiyon** olabilir.

    ```cpp
    #include <iostream>

    class kaynak_yoneticisi 
    {
        private:
            int* veri_ptr; // Dinamik bellek alanını işaret eden pointer

        public:
            // Kurucu
            kaynak_yoneticisi(int boyut) 
            {
                veri_ptr = new int[boyut]; // Belirtilen boyutta bellek tahsis et.
                // std::cout << "Kaynak tahsis edildi." << std::endl;
            }

            // Yıkıcı
            // Nesne kapsam dışına çıktığında veya silindiğinde otomatik çağrılır.
            ~kaynak_yoneticisi() 
            {
                delete[] veri_ptr; // Tahsis edilen belleği serbest bırak.
                veri_ptr = nullptr; // Dangling pointer olmasını engellemek için nullptr ata.
                // std::cout << "Kaynak serbest bırakıldı." << std::endl;
            }

            // Diğer üye fonksiyonları...
    };

    int main() 
    {
        {
            kaynak_yoneticisi yonetici_bir(10); // kaynak_yoneticisi nesnesi oluşturulur, bellek tahsis edilir.
        } // yonetici_bir kapsam dışına çıkar, yıkıcı çağrılır ve bellek serbest bırakılır.

        kaynak_yoneticisi* dinamik_yonetici = new kaynak_yoneticisi(5); // Dinamik bellekten nesne oluşturulur.
        delete dinamik_yonetici; // Dinamik nesne silinir, yıkıcı çağrılır.

        return 0;
    }
    ```

---

3) #### Copy Constructor (Kopyalan Kurucu)

    Mevcut bir nesneyi kullanarak aynı türden yeni bir nesne oluşturmak için kullanılır. Eğer kendi kopyalama kurucunuzu tanımlamazsanız, derleyici varsayılan olarak **shallow copy (sığ kopyalama)** yapan bir kopyalama kurucusu oluşturur. Eğer sınıfınız pointerlar gibi dinamik kaynaklar içeriyorsa sorunlara yol açabilir.

    **Erişim:**

    - ```ClassAdı(const ClassAdı& other);``` şeklinde tanımlanır.
    - ```const``` referans olmalıdır, aksi takdirde temporory (geçici) nesneler kopyalanamaz veya sonsuz döngü oluşabilir.

    ```cpp
    #include <cstring> // strcpy için

    class MetinDizi 
    {
        private:
            char* metin_verisi; // Dinamik karakter dizisi
            int uzunluk;        // Metnin uzunluğu

        public:
            // Kurucu
            MetinDizi(const char* m) 
            {
                uzunluk = strlen(m);
                metin_verisi = new char[uzunluk + 1]; // Null sonlandırıcı için +1
                strcpy(metin_verisi, m);
                // std::cout << "MetinDizi kurucusu: " << metin_verisi << std::endl;
            }

            // Yıkıcı
            ~MetinDizi() 
            {
                delete[] metin_verisi;
                metin_verisi = nullptr;
                // std::cout << "MetinDizi yıkıcısı: Bellek serbest bırakıldı." << std::endl;
            }

            // Kopyalama Kurucusu (Derin Kopyalama)
            // Mevcut bir nesneden yeni bir nesne oluştururken çağrılır.
            MetinDizi(const MetinDizi& diger) 
            {
                uzunluk = diger.uzunluk;
                metin_verisi = new char[uzunluk + 1]; // Yeni bir bellek alanı tahsis et
                strcpy(metin_verisi, diger.metin_verisi); // Veriyi bu yeni alana kopyala
                // std::cout << "Kopyalama kurucusu çağrıldı: " << metin_verisi << std::endl;
            }

            // Metin içeriğini döndüren fonksiyon
            const char* get_metin() const { return metin_verisi; }
    };

    // Değer olarak MetinDizi alan fonksiyon
    void goster(MetinDizi md)   // Burada kopyalama kurucusu çağrılır.
    { 
        std::cout << "Fonksiyon içinde: " << md.get_metin() << std::endl;
    }

    int main() 
    {
        MetinDizi orijinal_metin("Merhaba Dunya");
        // Kopyalama kurucusu çağrılır: orijinal_metin'den yeni_metin oluşturulur.
        MetinDizi yeni_metin = orijinal_metin;

        std::cout << "Orijinal: " << orijinal_metin.get_metin() << std::endl;
        std::cout << "Yeni: " << yeni_metin.get_metin() << std::endl;

        goster(orijinal_metin); // Fonksiyona değer olarak geçişte kopyalama kurucusu çağrılır.
    }
    ```
    
---

4) #### Copy Assignment (Kopyalama Atama)

    Zaten var olan bir nesnenin değerlerini başka bir var olan nesneye atamak için kullanılır. Kopyalama kurucusu yeni bir nesne oluştururken, kopyalama atama operatörü mevcut iki nesne arasında atama yapar.

    **Erişim:**

    - ```ClassAdı& operator = (const ClassAdı& other);``` şeklinde tanımlanır.

    - ```mevcut_nesne_bir = mevcut_nesne_iki;``` gibi durumlarda çağırılır

    ```cpp
    #include <cstring> // strcpy için

    class VeriPaketi 
    {
        private:
            int* veri_dizisi;
            int boyut;

        public:
            // Kurucu
            VeriPaketi(int b, int deger = 0) : boyut(b) 
            {
                veri_dizisi = new int[boyut];
                for (int i = 0; i < boyut; ++i) 
                {
                    veri_dizisi[i] = deger;
                }
                // std::cout << "VeriPaketi kurucusu çağrıldı." << std::endl;
            }

            // Yıkıcı
            ~VeriPaketi() 
            {
                delete[] veri_dizisi;
                veri_dizisi = nullptr;
                // std::cout << "VeriPaketi yıkıcısı çağrıldı." << std::endl;
            }

            // Kopyalama Kurucusu
            VeriPaketi(const VeriPaketi& diger) : boyut(diger.boyut) 
            {
                veri_dizisi = new int[boyut];
                for (int i = 0; i < boyut; ++i) 
                {
                    veri_dizisi[i] = diger.veri_dizisi[i];
                }
                // std::cout << "VeriPaketi kopyalama kurucusu çağrıldı." << std::endl;
            }

            // Kopyalama Atama Operatörü (Deep Copy)
            // Kaynak nesne (sağdaki) değerlerini mevcut nesneye (soldaki) kopyalar.
            VeriPaketi& operator=(const VeriPaketi& diger) 
            {
                // Kendi kendine atamayı kontrol et (self-assignment check)
                // Bu, `a = a;` gibi durumları ele alır ve bellek sızıntısını önler.
                if (this == &diger) 
                {
                    return *this;
                }

                // Mevcut belleği serbest bırak (önceki verileri sil)
                delete[] veri_dizisi;

                // Yeni bellek tahsis et ve veriyi kopyala
                boyut = diger.boyut;
                veri_dizisi = new int[boyut];
                for (int i = 0; i < boyut; ++i) 
                {
                    veri_dizisi[i] = diger.veri_dizisi[i];
                }
                // std::cout << "VeriPaketi kopyalama atama operatörü çağrıldı." << std::endl;
                return *this; // Zincirleme atama için *this döndür (a = b = c;)
            }

            void yazdir_veri() const 
            {
                for (int i = 0; i < boyut; ++i) 
                {
                    std::cout << veri_dizisi[i] << " ";
                }
                std::cout << std::endl;
            }
    };

    int main() 
    {
        VeriPaketi paket_bir(5, 10); // Boyut 5, tüm değerler 10
        VeriPaketi paket_iki(3, 20); // Boyut 3, tüm değerler 20

        std::cout << "Paket Bir (önce): ";
        paket_bir.yazdir_veri();
        std::cout << "Paket İki (önce): ";
        paket_iki.yazdir_veri();

        // Kopyalama atama operatörü çağrılır
        paket_bir = paket_iki; // paket_bir'in içeriği paket_iki'nin içeriğine kopyalanır.

        std::cout << "Paket Bir (sonra): ";
        paket_bir.yazdir_veri();
        std::cout << "Paket İki (sonra): ";
        paket_iki.yazdir_veri();
    }
    ```
    
---

5) #### Move Constructor (Taşıma Kurucusu)

    C++11 ile birlikte tanıtılmış olup, kaynakları pahalı kopyalama işlemleri yerine "taşıyarak" verimli bir şekilde nesne oluşturmayı sağlar. Geçici (temporary) nesnelerden veya kaynakları artık kullanılmayacak olan nesnelerden verimli bir şekilde veri transferi yapmak için kullanılır.

    **Erişim:** 

    - ```ClassAdı(ClassAdı&& other);``` şeklinde tanımlanır. ```&&``` (rvalue referans) geçici veya taşınabilir nesneleri gösterir.

    **Ne Zaman Çağırılır:**

    - Geçici bir nesneden yeni bir nesne başlatıldığında.
    
    - Bir fonksiyon bir nesneyi rvalue olarak geri döndürdüğünde.
    
    - ```std::move``` kullanılarak bir nesne taşınabilir hale getirildiğinde.

    Kopyalama maliyeti yüksek olan durumlarda performansı artırır. Kopyalama yerine, kaynak nesnenin pointerları (veya kaynak yönetimini yapan diğer üyeleri) yeni nesneye taşınır ve kaynak nesne "boş" bir duruma getirilir.

    ```cpp
    #include <iostream>
    #include <utility> // std::move için

    class BuyukVeri 
    {
        private:
            int* veri_ptr;
            int boyut;

        public:
            // Kurucu
            BuyukVeri(int b) : boyut(b) 
            {
                veri_ptr = new int[boyut];
                for (int i = 0; i < boyut; ++i) 
                {
                    veri_ptr[i] = i * 2;
                }
                // std::cout << "BuyukVeri kurucusu çağrıldı. Adres: " << veri_ptr << std::endl;
            }

            // Yıkıcı
            ~BuyukVeri() 
            {
                if (veri_ptr != nullptr) 
                {
                    delete[] veri_ptr;
                    // std::cout << "BuyukVeri yıkıcısı çağrıldı. Bellek serbest bırakıldı. Adres: " << veri_ptr << std::endl;
                } 
                else 
                {
                    // std::cout << "BuyukVeri yıkıcısı çağrıldı. Zaten boştu." << std::endl;
                }
                veri_ptr = nullptr;
            }

            // Kopyalama Kurucusu (Gerekirse)
            BuyukVeri(const BuyukVeri& diger) : boyut(diger.boyut) 
            {
                veri_ptr = new int[boyut];
                for (int i = 0; i < boyut; ++i) 
                {
                    veri_ptr[i] = diger.veri_ptr[i];
                }
                // std::cout << "BuyukVeri kopyalama kurucusu çağrıldı. Adres: " << veri_ptr << std::endl;
            }

            // Taşıma Kurucusu
            // Kaynak (diger) nesnenin kaynaklarını (veri_ptr) yeni nesneye "çalar" ve kaynak nesneyi boşaltır.
            BuyukVeri(BuyukVeri&& diger) noexcept : veri_ptr(diger.veri_ptr), boyut(diger.boyut) 
            {
                diger.veri_ptr = nullptr; // Kaynak nesneyi boşalt
                diger.boyut = 0;         // Kaynak nesnenin boyutunu sıfırla
                // std::cout << "BuyukVeri taşıma kurucusu çağrıldı. Yeni adres: " << veri_ptr << ", Eski adres boşaltıldı." << std::endl;
            }

            void yazdir_veri() const 
            {
                if (veri_ptr != nullptr) 
                {
                    for (int i = 0; i < boyut; ++i) 
                    {
                        std::cout << veri_ptr[i] << " ";
                    }
                    std::cout << std::endl;
                } else {
                    std::cout << "Veri boş veya taşınmış." << std::endl;
                }
            }
    };

    BuyukVeri fonksiyon_uret_veri() 
    {
        return BuyukVeri(1000); // RVO (Return Value Optimization) veya taşıma kurucusu çağrılır.
    }

    int main() 
    {
        // Taşıma kurucusu çağrılır: Geçici nesneden veri taşıması.
        BuyukVeri veri_bir = BuyukVeri(10); // Geçici nesne oluşturulur ve hemen taşınır.

        std::cout << "Veri Bir: ";
        veri_bir.yazdir_veri();

        BuyukVeri veri_iki(5);
        std::cout << "Veri İki (önce): ";
        veri_iki.yazdir_veri();

        // std::move ile açıkça taşıma kurucusunu çağırabiliriz.
        // veri_iki'nin kaynakları veri_uc'e taşınır.
        BuyukVeri veri_uc = std::move(veri_iki);
        std::cout << "Veri İki (sonra, taşındı): ";
        veri_iki.yazdir_veri(); // Artık boş olmalı
        std::cout << "Veri Üç: ";
        veri_uc.yazdir_veri();

        BuyukVeri veri_dort = fonksiyon_uret_veri(); // Fonksiyon dönüş değeri optimizasyonu veya taşıma.
        std::cout << "Veri Dört: ";
        veri_dort.yazdir_veri();
    }
    ```
    
---

6) #### Move Assignment (Taşıma Atama)

    Zaten var olan bir nesnenin kaynaklarını, başka bir var olan nesneye verimli bir şekilde taşımak için kullanılır. Kopyalama atama operatörünün taşıma karşılığıdır.

    **Erişim:** 

    - ```ClassAdı& operator = (ClassAdı&& other);``` şeklinde tanımlanır.

    **Ne Zaman Çağırılır:**

    - ```mevcut_nesne_bir = std::move(mevcut_nesne_iki);``` gibi taşıma atama işlemlerinde çağrılır.

    Kopyalama atama operatörüne benzer şekilde, pahalı kopyalama yerine kaynakların hızlıca yer değiştirmesini sağlar. Mevcut nesnenin kaynakları önce serbest bırakılır, ardından kaynak nesnenin kaynakları yeni nesneye atanır ve kaynak nesne boşaltılır.

    ```cpp
    #include <utility> // std::move için

    class GorselVeri 
    {
        private:
            int* piksel_verisi;
            int genislik;
            int yukseklik;

        public:
            // Kurucu
            GorselVeri(int g, int y) : genislik(g), yukseklik(y) {
                piksel_verisi = new int[genislik * yukseklik];
                for (int i = 0; i < genislik * yukseklik; ++i) {
                    piksel_verisi[i] = i; // Örnek veri
                }
                // std::cout << "GorselVeri kurucusu çağrıldı. Adres: " << piksel_verisi << std::endl;
            }

            // Yıkıcı
            ~GorselVeri() {
                if (piksel_verisi != nullptr) {
                    delete[] piksel_verisi;
                    // std::cout << "GorselVeri yıkıcısı çağrıldı. Bellek serbest bırakıldı. Adres: " << piksel_verisi << std::endl;
                } else {
                    // std::cout << "GorselVeri yıkıcısı çağrıldı. Zaten boştu." << std::endl;
                }
                piksel_verisi = nullptr;
            }

            // Kopyalama Kurucusu (Derin Kopyalama)
            GorselVeri(const GorselVeri& diger) : genislik(diger.genislik), yukseklik(diger.yukseklik) {
                piksel_verisi = new int[genislik * yukseklik];
                for (int i = 0; i < genislik * yukseklik; ++i) 
                {
                    piksel_verisi[i] = diger.piksel_verisi[i];
                }
                // std::cout << "GorselVeri kopyalama kurucusu çağrıldı. Adres: " << piksel_verisi << std::endl;
            }

            // Taşıma Kurucusu
            GorselVeri(GorselVeri&& diger) noexcept :
                piksel_verisi(diger.piksel_verisi), genislik(diger.genislik), yukseklik(diger.yukseklik) {
                diger.piksel_verisi = nullptr;
                diger.genislik = 0;
                diger.yukseklik = 0;
                // std::cout << "GorselVeri taşıma kurucusu çağrıldı. Yeni adres: " << piksel_verisi << std::endl;
            }

            // Kopyalama Atama Operatörü
            GorselVeri& operator=(const GorselVeri& diger) 
            {
                if (this == &diger) 
                {
                    return *this;
                }
                delete[] piksel_verisi;
                genislik = diger.genislik;
                yukseklik = diger.yukseklik;
                piksel_verisi = new int[genislik * yukseklik];
                for (int i = 0; i < genislik * yukseklik; ++i) {
                    piksel_verisi[i] = diger.piksel_verisi[i];
                }
                // std::cout << "GorselVeri kopyalama atama operatörü çağrıldı." << std::endl;
                return *this;
            }

            // Taşıma Atama Operatörü
            // Sağdaki (diger) nesnenin kaynaklarını soldaki nesneye "taşır".
            GorselVeri& operator=(GorselVeri&& diger) noexcept 
            {
                if (this == &diger) // Kendi kendine atama kontrolü
                { 
                    return *this;
                }

                delete[] piksel_verisi; // Mevcut belleği serbest bırak

                piksel_verisi = diger.piksel_verisi; // Kaynakları taşı
                genislik = diger.genislik;
                yukseklik = diger.yukseklik;

                diger.piksel_verisi = nullptr; // Kaynak nesneyi boşalt
                diger.genislik = 0;
                diger.yukseklik = 0;

                // std::cout << "GorselVeri taşıma atama operatörü çağrıldı. Yeni adres: " << piksel_verisi << std::endl;
                return *this; // Zincirleme atama için *this döndür
            }

            void yazdir_boyut() const 
            {
                if (piksel_verisi != nullptr) 
                {
                    std::cout << "Boyut: " << genislik << "x" << yukseklik << std::endl;
                } else {
                    std::cout << "Veri boş veya taşınmış." << std::endl;
                }
            }
    };

    int main() 
    {
        GorselVeri resim_bir(100, 50);
        GorselVeri resim_iki(20, 10);

        std::cout << "Resim Bir (önce): ";
        resim_bir.yazdir_boyut();
        std::cout << "Resim İki (önce): ";
        resim_iki.yazdir_boyut();

        // Taşıma atama operatörü çağrılır
        // resim_iki'nin kaynakları resim_bir'e taşınır.
        resim_bir = std::move(resim_iki);

        std::cout << "Resim Bir (sonra): ";
        resim_bir.yazdir_boyut();
        std::cout << "Resim İki (sonra, taşındı): ";
        resim_iki.yazdir_boyut(); // Artık boş olmalı
    }
    ```

---

Bu özel üye fonksiyonları, C++'ta "Rule of Three/Five/Zero" olarak bilinen kavramların temelini oluşturur.