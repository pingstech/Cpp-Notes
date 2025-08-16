# 12. Ders

## Copy Elision (Kopya Elemesi)

Copy Elision, C++ derleyicilerinin gereksiz nesne kopyalama veya taşıma işlemlerini atladığı bir optimizasyon tekniğidir. Bu sayede programın performansı artar. C++17 ve sonraki standartlarda bazı durumlarda bu optimizasyon zorunlu hale gelmiştir. Nesne, kopyalanmak yerine doğrudan hedeflenen bellek konumunda oluşturulur.

### Key Cases (Ana Durumlar)

#### 1\. Passing a Temporary Object to a Function (Geçici Nesneyi Fonksiyona Parametre Olarak Geçirme)

Bir geçici nesne (prvalue) bir fonksiyonun parametresine aktarılırken, kopya veya taşıma işlemi yapılmaz. Nesne, doğrudan fonksiyon parametresinin bellek alanında oluşturulur.

**Örnek:**

```cpp
#include <iostream>
#include <string>

class my_class {
public:
    std::string name;

    my_class(const std::string& n) : name(n) {
        std::cout << "Oluştu: " << name << "\n";
    }

    ~my_class() {
        std::cout << "Yok edildi: " << name << "\n";
    }
};

void process(my_class obj) {
    // Nesne parametreye doğrudan yerleştirilir (copy/move yok)
    std::cout << "Fonksiyon: " << obj.name << "\n";
}

int main() {
    // "Geçici" adında geçici nesne oluşturuluyor
    // C++17 ile bu nesne doğrudan 'obj' parametresine yerleştirilir
    process(my_class("Geçici"));
}
```

**Çıktı:**

```
Oluştu: Geçici
Fonksiyon: Geçici
Yok edildi: Geçici
```

Bu çıktı, `my_class("Geçici")` ile oluşturulan nesnenin doğrudan `process` fonksiyonunun `obj` parametresi olarak kullanıldığını ve ayrı bir kopyalama işlemi olmadığını gösterir.

#### 2\. Returning a Temporary Object from a Function (Fonksiyondan Geçici Nesne Döndürme)

Bir fonksiyon geçici bir nesne döndürdüğünde, bu nesne kopyalanmaz. Nesne, doğrudan çağrıldığı yerdeki değişkende oluşturulur.

**Örnek:**

```cpp
#include <iostream>
#include <string>

class my_class {
public:
    std::string name;

    my_class(const std::string& n) : name(n) {
        std::cout << "Oluştu: " << name << "\n";
    }

    ~my_class() {
        std::cout << "Yok edildi: " << name << "\n";
    }
};

my_class create() {
    // Döndürülen nesne doğrudan main içindeki hedef objeye inşa edilir
    return my_class("Dönen");
}

int main() {
    // 'create' fonksiyonundan dönen nesne doğrudan 'obj' içine konur
    my_class obj = create();
}
```

**Çıktı:**

```
Oluştu: Dönen
Yok edildi: Dönen
```

Bu çıktı, `create` fonksiyonu içinde oluşturulan geçici nesnenin, `main` fonksiyonundaki `obj` değişkenine kopyalanmadan veya taşınmadan doğrudan yerleştirildiğini gösterir.

#### 3\. Returning a Named Object (Named Return Value Optimization - NRVO) (Adlandırılmış Nesneyi Döndürme)

Fonksiyon içinde adlandırılmış bir yerel nesne döndürüldüğünde, derleyici kopyalamayı atlayabilir. Bu optimizasyon zorunlu değildir, ancak çoğu derleyici tarafından uygulanır.

**Örnek:**

```cpp
#include <iostream>
#include <string>

class my_class {
public:
    std::string name;

    my_class(const std::string& n) : name(n) {
        std::cout << "Oluştu: " << name << "\n";
    }

    ~my_class() {
        std::cout << "Yok edildi: " << name << "\n";
    }
};

my_class create() {
    my_class obj("Adlandırılmış"); // Adlandırılmış lokal nesne
    // Derleyici burada NRVO uygulayarak kopya/taşıma atlayabilir
    return obj;
}

int main() {
    // Eğer NRVO uygulanırsa ek kopya/taşıma olmaz
    my_class result = create();
}
```

**NRVO Uygulanmış Çıktı:**

```
Oluştu: Adlandırılmış
Yok edildi: Adlandırılmış
```

Eğer derleyici NRVO uygularsa, `create` fonksiyonundaki `obj` nesnesi, `main` fonksiyonundaki `result` nesnesinin bellek konumunda doğrudan oluşturulur, bu da ekstra bir kopyalama veya taşıma işlemini önler.

## Temporary Materialization (Geçici Malzemeleştirme)

Temporary Materialization, bellekte bir adresi olmayan geçici bir değeri (prvalue) geçici bir nesneye dönüştürme işlemidir. Bu, bir prvalue'ya referans bağlanması veya üye fonksiyonlarının çağrılması gerektiğinde gerçekleşir.

**Örnek:**

```cpp
#include <iostream>

class my_class {
public:
    int id;

    my_class(int i) : id(i) {
        std::cout << "Oluştu: " << id << "\n";
    }

    void print() const {
        std::cout << "ID: " << id << "\n";
    }

    ~my_class() {
        std::cout << "Yok edildi: " << id << "\n";
    }
};

void take_ref(const my_class& obj) {
    // Geçici nesne referansa bağlandığında materialize edilir
    obj.print();
}

int main() {
    // 42 değeriyle geçici nesne yaratılıyor
    // Materialize edilir ve print çağrısı yapılır
    my_class(42).print();

    // Geçici nesne fonksiyona referans parametresiyle aktarılır
    // Bu nedenle yine materialization gerekir
    take_ref(my_class(10));
}
```

**Çıktı:**

```
Oluştu: 42
ID: 42
Yok edildi: 42
Oluştu: 10
ID: 10
Yok edildi: 10
```

Burada `my_class(42)` ifadesi geçici bir değerdir. `print()` fonksiyonunu çağırmak için bu geçici değerin bir nesneye dönüştürülmesi (materialization) gerekir. Bu dönüşüm, nesnenin ömrünü `print()` çağrısı bitene kadar uzatır.

## Return Value Optimization (Dönüş Değeri Optimizasyonu)

Return Value Optimization (RVO), fonksiyonlardan nesne döndürülürken kopya veya taşıma işlemlerini atlar. Nesne doğrudan hedefte oluşturulur. RVO, `Copy Elision` teriminin bir alt kümesidir.

### RVO Types (RVO Türleri)

#### 1\. Unnamed RVO (URVO)

Bu optimizasyon, isimsiz (geçici) nesnelerin döndürülmesi durumunda uygulanır. C++17'den itibaren zorunludur.

#### 2\. Named RVO (NRVO)

Bu optimizasyon, fonksiyon içinde adlandırılmış bir nesnenin döndürülmesi durumunda uygulanır. Zorunlu değildir, ancak derleyiciler tarafından sıklıkla uygulanır.

**Not:** NRVO, `Copy Elision` başlığındaki "Adlandırılmış Nesneyi Döndürme" bölümüyle aynı konuyu ifade etmektedir.

### Summary Notes (Özet Notlar)

  * **Copy Elision**: Performansı artırmak için gereksiz nesne kopyalama işlemlerini ortadan kaldırır.
  * **Temporary Materialization**: Geçici değerlerin bellek adresine sahip nesnelere dönüştürülmesini sağlar.
  * **RVO/NRVO**: `Copy Elision` kapsamında, fonksiyon dönüşlerinde yapılan kopya işlemlerini optimize eder.