
<h2>CREATIONAL DESIGN PATTERN</h2>

Bir nesneyi üretmek yerine o **nesneyi talep etme yaklaşımı** söz konusudur.
Nesnelerin sürekli üretilmesi bir maliyettir. Nesnelerin üretimini optimize etmek/düzenlemek gerekir.
Creational Design Pattern'lar arasında birbirini tamamlayan yaklaşımlar vardır.
<br>

<h4>Singleton Design Pattern</h4>

* Bir sınıfın sadece tek bir nesnesinin kullanılması davranışıdır.
Bazı verilerin sadece tek bir nesne'de tutulması/saklanması gerekebilmektedir.
* Bu verilerin tutulacağı model Singleton Pattern'a göre tasarlanabilir.
* İlgili sınıfın **new operatörü** ile nesne üretilmesi engellenir.
* Entity sınıflarına Singleton davranışını uygun değildir.
* Genellikle service sınıflarına, belirli iş operasyonları yürüten yapılara daha uygundur.
* 2 türlü uygulanabilmektedir;

2 yöntem içinde ortak olarak new oparatörü ile nesne üretimi engellenir. Üretilecek tekil instance static bir referans noktası alır.

**İlk yöntem**, instance'ın talep edilmesini sağlayacak bir property veya metot tanımlar. Bu property veya metot ile önce static referans'a ait bir instance'ın varlığı kontrol edilir. Var ise o instance elde edilir, yok ise bir instance oluşturulur ve static referans ile işaretlenir.
<br>

**İkinci yöntem**, instance'ı talep edecek metot veya property tanımlanır. Static referans'a bir kontrol uygulanmaz ve nesne üretimi ilgili sınıfın **Static Constructor**'ında gerçekleştirilir.

Static Constructor nesne üretilirken sadece ve sadece bir kere tetiklenir.

<br>

**Avantajları**;

* Bir sınıfın yalnızca tek bir instance'ı olduğunun garantisini sağlar.
* İlgili instance'a uygulama çağında merkezi bir erişim noktası sağlanır.
* İlk talep edildiğinde üretilir ve aynı nesne farklı noktalarda talep edilir.
<br>

**Dezavantajları**;

* Single Responsibility davranışını ihlal eder. Çünkü ilgili sınıf hem tekil nesne davranışı hem de yapacağı işlemi/operasyonu içerisinde tanımlayacaktır.
* Asenkron süreçlerde birden fazla nesne oluşturabilme ihtimali vardır.
* Ctor private tanımlananacağı için Unit-Testlerde sorun olabilmektedir.
* İlgili sınıf kalıtım operasyonlarında kullanılamaz.

<br>
<h4>Kullanım</h4>
Öncelikle sınıfımızın nesne üretilebilirliği kaldırılır. bunun için constructor'u private tanımlarız.

```csharp
class SingletonClass
{
    private SingletonClass(){}
}
```
<br>

Daha sonra global olarak tanımlanacak ve geri dönüş tipi SingletonClass olacak field tanımlarız.

```csharp
class SingletonClass
{
    private SingletonClass(){}

    static SingletonClass _instance;
}
```
<br>



**1.Yöntem Kontrol Ederek**
Static bir member(property veya method) tanımlayarak instance'ın null kontrolü yapılarak yoksa instance üretip field'a atanır varsa aynı instance return edilir.

```csharp
class SingletonClass
{
    private SingletonClass(){}

    static SingletonClass _instance;

    public static SingletonClass GetInstance()
    {
        if(_instance == null)
            _instance = new SingletonClass();
        return _instance;
    }
}
```

**2.Yöntem Kontrol Etmeden Static Ctor İle Oluşturarak**
Static bir member(property veya method) tanımlayarak instance'ın null kontrolü yapılmadan static constructor'da üretilen nesne garantisi sağlanır ve static member'da return edilir.
Static Constructor herhangi bir static bir member'ın tetiklenmesiyle de tetiklenedebilir.

```csharp
class SingletonClass
{
    private SingletonClass(){}

    static SingletonClass _instance;

    static SingletonClass()
    {
        _instance = new SingletonClass();
    }

    public static SingletonClass GetInstance()
    {
        return _instance;
    }
}
```