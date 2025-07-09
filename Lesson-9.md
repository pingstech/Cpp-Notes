# 9.Ders

## CLASS

Bir tür veri yapısıdır ve iki ana bileşenden oluşur:
- **Veri üyeleri (data members)**: Bir sınıfın içindeki değişkenlerdir.
- **Fonksiyon üyeleri (member functions)**: Bu değişkenler üzerinde işlem yapan fonksiyonlardır.

### Class Definition

Bir sınıf, ```class``` anahtar kelimesi ile tanımlanır.

```cpp
class my_class
{
    // Bu alana  "Class scope" denir. Bu bildirilen varlıklara bu "Class"ın öğeleri(elemanları) denir.

    class class_obj     // Type member
    {

    };

    void class_func();  // member function

    int class_var;      // Data member
}
```

**Bir sınıf aşağıdaki özelliklere sahiptir:**

1) **Veri Üyeleri (Data Members)**:
    - Bir sınıf, değişkenlerin bulunduğu bir yapı olabilir. Bu değişkenler veri üyeleri olarak bilinir.
    - Veri üyeleri private (özel) veya public (genel) olabilir.

2) **Fonksiyon Üyeleri (Member Functions)**:
    - Sınıf içindeki fonksiyonlar, sınıfın veri üyeleri üzerinde işlem yapmak için kullanılır.
    - Bu fonksiyonlar da ```private``` veya ```public``` olabilir.

3) **Nesne (Object)**:
    - Sınıf, bir şablon sağlar; ancak bir sınıfın **nesneleri**, sınıfın örnekleridir.
    - **Nesneler**, sınıfın veri ve fonksiyon üyelerini kullanabilir.

4) **Access Control (Erişim Kontrolü)**:
    - ```public```: Sınıf dışından erişilebilir.
    - ```private```: Yalnızca sınıf içinden erişilebilir.
    - ```protected```: Bu erişim düzeyi, türetilmiş sınıfların erişmesine izin verir.

```cpp
struct data_t 
{ 
    int a, b, c;
};

int main()
{
    data_t _my_data;
    _my_data.a = 10;        // Bu şekilde erişim sağlayacaktık.

    data_t _my_data_p;    
    data* p = &_my_data_p;  // Bu şekilde tanımlama yaparsak 2 şekilde erişim sağlayabiliriz.

    (*p).a = 10;            // 1. erişim yoludur. Bir ifade de bir ismi '.' operatörünün sağında(qualified name) kullanmışsak, derleyici önce '.' operatörünün sol operandı olan ifadenin hangi sınıf türünden olduğunu anlayacak(sınıf türünden değilse "Syntax" hatası oluşacak). Sağ operandı olan ismi o sınıfın scope'u içerisinde arayacak(Scope içerisinde bulamazsa yine "Syntax" hatası oluşacak).
    p->b = 12;              // 2. erişim yoludur. '.' operatörü ile aynı yolu izler fakat '->' operatörünün sol operandı muhakkak "sınıf türünden pointer" olmalıdır!
}
```

### Access Control(Erişim Kontrolü)

1) #### **```public``` (Genel):**

    - ```public``` üyeler sınıf dışından erişilebilir.
    - Bu üyeler, sınıf dışındaki herhangi bir fonksiyon veya nesne tarafından kullanılabilir.

    **Örnek:**

    ```cpp
    #include <iostream>
    using namespace std;

    class MyClass {
    public:
        int x;  // public veri üyesi

        void setX(int value) {  // public fonksiyon
            x = value;
        }

        int getX() {  // public fonksiyon
            return x;
        }
    };

    int main() {
        MyClass obj;  
        obj.x = 10;  // public veri üyesine dışarıdan erişim
        cout << "X değeri: " << obj.getX() << endl;  // public fonksiyon kullanımı
        return 0;
    }
    ```
    - Burada, ```x``` ```public``` olarak tanımlandığı için ```main``` fonksiyonunda dışarıdan erişilebilir.
    - ```setX``` ve ```getX``` fonksiyonları da ```public``` olduğu için dışarıdan çağrılabilir.

    ---

2) #### **```private``` (Özel):**

    - ```private``` üyeler sadece sınıfın içinde bulunan fonksiyonlar tarafından erişilebilir.
    - ```private``` veriler, sınıf dışından erişilemez ve yalnızca sınıf içindeki fonksiyonlarla işlenebilir.

    **Örnek:**

    ```cpp
    #include <iostream>
    using namespace std;

    class MyClass {
    private:
        int x;  // private veri üyesi

    public:
        void setX(int value) {  // public fonksiyon
            x = value;
        }

        int getX() {  // public fonksiyon
            return x;
        }
    };

    int main() {
        MyClass obj;
        // obj.x = 10;  // HATA! private veri üyelerine dışarıdan erişilemez
        obj.setX(10);  // public fonksiyon ile erişim sağlanır
        cout << "X değeri: " << obj.getX() << endl;  // private veri üyesine erişim, public fonksiyon ile yapılır
        return 0;
    }
    ```
    - ```x``` değişkeni ```private``` olarak tanımlanmıştır, bu nedenle ```main``` fonksiyonunda doğrudan erişilemez.

    - Ancak ```setX``` ve ```getX``` gibi ```public``` fonksiyonlar kullanılarak erişim sağlanabilir.

    ---

3) #### **```protected``` (Korunmuş):**
    - ```protected``` üyeler, sınıf dışından erişilemez, ancak bu üyeler türeyen sınıflar tarafından erişilebilir. Bu, kalıtım (inheritance) ile türetilen sınıflara erişim izni verir.

    ```cpp
    #include <iostream>
    using namespace std;

    class Base {
    protected:
        int x;  // protected veri üyesi

    public:
        void setX(int value) {  // public fonksiyon
            x = value;
        }

        int getX() {  // public fonksiyon
            return x;
        }
    };

    class Derived : public Base {
    public:
        void display() {
            cout << "X değeri (türetilmiş sınıf): " << x << endl;  // protected üyeye erişim
        }
    };

    int main() {
        Derived obj;
        obj.setX(10);  // Base sınıfının public fonksiyonu ile erişim
        obj.display();  // Derived sınıfının fonksiyonu ile protected üyeye erişim
        return 0;
    }
    ```
    - Burada, ```x``` ```protected``` olarak tanımlandığı için ```Base``` sınıfının dışında doğrudan erişilemez, ancak ```Derived``` sınıfından erişilebilir.

    - ```setX``` fonksiyonu ```Base``` sınıfından çağrılabilir, bu da ```protected``` veriye dolaylı erişim sağlar.

    ---

| **Erişim Belirleyicisi** | **Erişim Durumu**                                     |
| ------------------------ | ----------------------------------------------------- |
| **`public`**             | Sınıf içinden ve dışından erişilebilir.               |
| **`private`**            | Yalnızca sınıf içinden erişilebilir.                  |
| **`protected`**          | Sınıf içinden ve türetilmiş sınıflardan erişilebilir. |


### Class Kullanımına Ait Ekstra Notlar:

1) **Erişim belirleyici kullanmadan sınıf tanımlama:**

    ```cpp
    class _my_class
    {
        // Bu scope'ta "private" veya"public" anahtar sözcüğü kullanılmamışsa ve "class" anahtar sözcüğü kullanılarak sınıf oluşturulmışsa, Class'ın sınıfı "private" olur
    }

    struct _my_struct
    {
        // Struct anahtar sözcüğü ile sınıf tanımlanmışsa sınıf "public" olur
    }
    ```

2) **Mülakat sorusu-1:** 

    C++' bir sınıfın ```public``` interface'i: sadece sınıfın ```public``` bölümü değil, sınıfın ```public``` bölümündeki öğeler + başlık dosyasındaki(headre file) ```global``` bildirimlerdir.

3) **Sınıf scope içinde aynı isimli değişken tanımlama:**
    Bir **syntax hatasıdır**. Bir sınıfın scope'u içerisinde aynı isimli bir değişken tanımlanamaz.

    ```cpp
    class _my_class
    {
        private:
            int var;
        
        public:
            double var; // Syntax hatası oluşur.
    }
    ```


**1.15'de kaldım**