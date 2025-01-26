
<h2>CREATIONAL DESIGN PATTERN</h2>

Bir nesneyi üretmek yerine o **nesneyi talep etme yaklaşımı** söz konusudur.
Nesnelerin sürekli üretilmesi bir maliyettir. Nesnelerin üretimini optimize etmek/düzenlemek gerekir.
Creational Design Pattern'lar arasında birbirini tamamlayan yaklaşımlar vardır.
<br>

<h3>Singleton Design Pattern</h3>

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

<br>

<p>Asenkron süreçte birden fazla instance oluşturulabiliyor. Asenkron yapılar aynı T zamanında nesne üretimi bloğunda olabilir. 
Çözüm olarak Static Constructor da nesne üretimini sağlayarız. Static Consturctor sadece bir kez tetikleneceği için bu sorunu ortadan kaldırır.</p>

<br><br>

<h3>Multiton Design Pattern</h3>
Uygulama bazında bir sınıfın sadece belirli sayıda instance'ının olacağını sağlar.
Bir collection(Dictionary) olarak key value formatında tutar.
Her key için tek bir instance üretilecektir. Yani Singleton davranışıda devam etmektedir.
<br>
Mail Service Örneği ile Kullanım;

```csharp
class MailService
{
    private MailService(){}
    static Dictionary<string, MailService> _ms = new();
    public static MailService GetInstance(string key){
        if(!_ms.ContainsKey(key))
            _ms[key] = new MailService();
        return _ms[key];
    }
}
```
<br><br>

<h3>Factory Method Desing Pattern</h3>

* Nesne üretim süreçlerinde nesne'nin türüne odaklanmadan nesnelerin üretilmesini sağlar.
* Nesne üretimi sorumluluğu yardımcı bir sınıfta sağlanır.
* Nesne üretimi standart hale getirilebilir.
* Belirli nesne gruplarının üretimi ayrı ayrı gerçekleştirmek yerine merkezi bir sınıf üzerinden sağlanır.
* Kod inşa sürecinde nesneyi üretip kullanırken consturctor parametreleri, tetiklenmesi gereken metot veya propertyler üretim maliyetine odaklanmaya sebep olur. 
* Factory Method Design Pattern nesnelerin standart bir şekilde oluşturulmasını sağlar.
* Nesneleri Singleton olarakta tasarlayabiliriz.
* Sıkı bağımlılıktan kaçınmayı sağlar.
* Mevcut nesneleri kullanmak yerine, yeni nesneleri standart bir şekilde oluşturulmasını sağlar.
* Ortak bir arayüzden üretilecekleri için ortak işlevsellik barındırırlar.

<br>

**1-Factory Yöntemi**
* Ortak arayüz uygulanan sınıflarda, new operatörüyle üretim gerçekleştirilir.
* Alt factory sınıfı barındırmaz.

**Örnek Kullanım;**

Grup nesnelerini soyutlayacak sınıfımız.
```csharp
interface IA 
{
    /*
    ...
    */
    void Run();
}
```

<br>

Implement edilmiş grup nesneleri
```csharp
class A1 : IA
{
    public void Run()
    {
        Console.WriteLine($"{nameof(A1)} Running...");
    }
}
class A2 : IA
{
    public void Run()
    {
        Console.WriteLine($"{nameof(A2)} Running...");
    }
}
```

<br>

Creator Sınıfı(Nesneyi üretecek yardımcı sınıf)
Class türleri enum olarak parametreden alınmakta.
```csharp
class ACreator
{
    public static IA GetInstance(AType aType )
    {
        IA _ia = null;
        switch (aType)
        {
            case AType.A1:
               _ia = new A1();
                break;
            case AType.A2:
                _ia = new A2();
                break;
        }
        return _ia;
    }
}
```

<br>



**2-Factory Method yöntemi**
* Ortak arayüz uygulanan sınıflarda, alt factory oluşturulmuş sınıflar nesne üretimini üstlenir.
* Yardımcı sınıf nesnenin factory kısmıyla ilgilenirken, factory sınıflar üretim sorumluluğunu üstlenir.
* Belirli nesne grubunun üretilmesinin sorumluluğunu üstlenir.

<br>

**Factory Method ile kullanım;**
Grup nesnelerin kendine özel factory sınıfları oluşturulur ve nesne üretimini factory sınıflar üstlenir.

```csharp
interface IAFactory
{
    // create func imza
    IA Create();
}

//Factory Classes
class A1Factory : IAFactory
{
    public IA Create()
    {
        A1 a1 = new A1();
        return a1;

    }
}
class A2Factory : IAFactory
{
    public IA Create()
    {
        A2 a2 = new A2();
        return a2;
    }
}
```

<br>
Creator yardımcı sınıfı artık nesne üretimini üstlenmez. Parametreden alınacak türe göre Factory sınıflarından nesne üreterek o nesne üzerinden talep edilen nesne üretilir.

Ek olarak **single responsibility prensibi** de sağlanmış olmaktadır.

```csharp
class ACreator
{
    public static IA GetInstance(AType aType)
    {
        IAFactory _aFactory = aType switch
        {
            AType.A1 => new A1Factory(),
            AType.A2 => new A2Factory(),
            AType.A3 => new A3Factory()
        };
        return _aFactory.Create();
    }
}
```

<br><br>

<h3>Abstract Factory Method Desing Pattern</h3>

Abstract Factory Method, bir nesnenin kendi aralarında birden fazla  ilişkisel alt nesne grubu ile bir araya gelmesi durumlarında, bu alt nesnelerin soyutlanarak kullanılması yaklaşımıdır.
Bu alt nesne gruplarının nesne üretimleri yardımcı sınıflara(factory) verilir.
Bütünsel nesneyi üretebilmek için bu nesnelerin teker teker üretilmesine gerek kalmadan tek seferde üretebilmeyi sağlar.
Daha sonradan alt sınıf nesnesi kolayca eklenebilir.(Open Close Principle)
Nesnelerin üretim sorumluluğu ve üretim maliyeti Factory sınıflara verilmiş olur.
Her bir factory sınıfının kendi arayüzü oluşturulur.
Sıkı bağımlılıktan kaçınmayı sağlar.
Factory Method Design Pattern'a göre daha karmaşık bir yapıya sahiptir.
**Örnek olarak**; Bilgisayar Kasası
Aralarında ilişkisel alt sınıflar: Anakart - İşlemci - Ram - Ekran Kartı gibi.
<br>
EkranKartı-İşlemci-Ram products'ları ile örnek.
Abstract Products;

```csharp
interface ICPU
{
    string CPUName { get; set; }
    string CPUCore { get; set; }
    string CPUThread { get; set; }
}

interface IVideoCard
{
    string VideoCardName { get; set; }
    string VideoCardMemory { get; set; }
    string VideoCardBit { get; set; }
}

interface IRAM
{
    string RAMName { get; set; }
    string RAMMemory { get; set; }
    string RAMMhz { get; set; }
}
```
<br>

Concrete Products Abstract Products'ları ile implement edildi;
```csharp
class CPU : ICPU
{
    public string CPUName { get; set; }
    public string CPUCore { get; set; }
    public string CPUThread { get; set; }
}

class VideoCard : IVideoCard
{
    public string VideoCardName { get; set; }
    public string VideoCardMemory { get; set; }
    public string VideoCardBit { get; set; }
}

class RAM : IRAM
{
    public string RAMName { get; set; }
    public string RAMMemory { get; set; }
    public string RAMMhz { get; set; }
}
```
<br>

Abstract Factory, alt sınıf nesnelerin üretimini gerçekleştirecek arayüz.

```csharp
interface IComputerFactory
{
    ICPU CreateCPU();
    IVideoCard CreateVideoCard();
    IRAM CreateRAM();
}
```

<br>
Talep edilecek nesne class'ı;

```csharp
class Computer
{
    public Computer(ICPU _cpu, IVideoCard _videoCard, IRAM _ram)
    {
        CPU = _cpu;
        VideoCard = _videoCard;
        RAM = _ram;
    }
    public ICPU CPU { get; set; }
    public IVideoCard VideoCard { get; set; }
    public IRAM RAM { get; set; }
}
```
<br>
Computer nesnesini üretecek Creator class'ı;

```csharp
class ComputerCreator
{
    ICPU _cpu;
    IRAM _ram;
    IVideoCard _videoCard;

    public Computer CreateComputer(IComputerFactory computerFactory)
    {
        _cpu = computerFactory.CreateCPU();
        _ram = computerFactory.CreateRAM();
        _videoCard = computerFactory.CreateVideoCard();

        return new(_cpu, _ram, _videoCard);
    }
}
```
<br>


Abstract Factory i implement edecek olan Concrete Factory nesne/class'ı;
```csharp
class Computer1 : IComputerFactory
{
    public Computer1()
    {
        Console.WriteLine($"{nameof(Computer1)} created.");
    }
    public ICPU CreateCPU()
    {
        CPU cPU = new CPU();
        cPU.CPUName = "i5-12400F";
        cPU.CPUCore = "6";
        cPU.CPUThread = "12";
        return cPU;
    }
    public IRAM CreateRAM()
    {
        RAM rAM = new RAM();
        rAM.RAMName = "GSKILL Ripjavs V Siyah";
        rAM.RAMMemory = "16GB";
        rAM.RAMMhz = "3200MHz";
        return rAM;
    }

    public IVideoCard CreateVideoCard()
    {
        VideoCard videoCard = new VideoCard();
        videoCard.VideoCardName = "MSI GeForce RTX 4060 VENTUS 2X BLACK";
        videoCard.VideoCardMemory = "8GB";
        videoCard.VideoCardBit = "128 Bit";
        return videoCard;
    }
}
```
