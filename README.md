# Tasarım Desenleri (Design Patterns)

Tasarım desenleri, yazılım geliştirme süreçlerinde karşılaşılan yaygın problemleri çözmek için önerilen, tekrar kullanılabilir ve sistematik çözümlerdir. Bu desenler, yazılımın yapısını ve tasarımını belirlerken en iyi uygulamaları ve standartları takip etmeyi sağlar.

Tasarım desenleri genellikle 3 ana kategoriye ayrılır. Haydi bu başlıkları beraber inceleyelim.

# Yapısal (Structural) Tasarım Desenleri

Farklı sınıflar ve nesneler arasındaki ilişkileri düzenler ve bunları daha esnek ve verimli hale getirir. Yapısal desenler, sistemin bileşenleri arasında daha iyi bir yapı kurar.

- [Flyweight](#Flyweight)
- [Adapter](#Adapter)
- [Composite](#Composite)
- [Facade](#Facade)
- [Proxy](#Proxy)
- [Decorator](#Decorator)
- [Bridge](#Bridge)

# Yaratımsal (Creational) Tasarım Desenleri

Nesne oluşturma sürecini yönetir ve nesnelerin yaratılmasında esneklik sağlar. Bu desenler, nesne oluşturma süreçlerini soyutlayarak yazılımın bağımlılıklarını azaltır.

- [Singleton](#Singleton)
- [Factory](#Factory)
- [Abstract Factory](#Abstract-Factory)
- [Builder](#Builder)
- [Prototype](#Prototype)

# Davranışsal (Behavioral) Tasarım Desenleri

Nesneler arasındaki iletişimi ve davranışları düzenler. Bu desenler, nesnelerin etkileşim biçimlerini optimize eder ve daha esnek hale getirir.

- [Strategy](#Strategy)
- [Command](#Command)
- [Iterator](#Iterator)
- [Template](#Template)
- [Observer](#Observer)
- [Memento](#Memento)
- [Mediator](#Mediator)
- [Chain of Responsibility](#Chain-of-Responsibility)
- [Visitor](#Visitor)
- [State](#State)

## Flyweight

### Amaç?

Çok sayıda nesneyi oluşturmanın bellek maliyetini azaltmak ve bellek kullanımını optimize etmektir. Bu kalıp, aynı veya benzer durumlara sahip nesneleri paylaşarak bellekteki gereksiz yükleri en aza indirir.

### Ne Zaman Kullanılır?

Bir grafik uygulaması düşünün, burada birçok şekil (daire, kare, üçgen vb.) çizmek gerekiyor. Bu şekillerin çoğu, örneğin renk veya çizgi kalınlığı gibi bazı ortak özelliklere sahip olabilir. Flyweight deseni kullanarak, her şekil için yalnızca farklı olan özellikleri (örneğin, konum) saklayabiliriz ve ortak özellikleri merkezi bir havuzda tutarak bellek kullanımını optimize edebiliriz.

#### Flyweight Arayüzü:

Flyweight nesnelerinin ortak bir arayüzü olmalıdır. Bu arayüz, nesnelerin kullanılabilir bir şekilde dışarıya sunulmasını sağlar.

```java 
// Flyweight Arayüzü
interface Shape {
    void draw(int x, int y);  // Çizim fonksiyonu, konum verisini alır
}
```

#### ConcreteFlyweight (Somut Flyweight):

Somut Flyweight sınıfı, nesnelerin ortak özelliklerini (örn. renk, çizgi kalınlığı gibi) saklar. Bu özellikler, tüm nesneler için ortak olan verilerdir.

```java 
// ConcreteFlyweight (Somut Flyweight)
class Circle implements Shape {
    private String color;  // Ortak özellik, örneğin renk

    public Circle(String color) {
        this.color = color;
    }

    @Override
    public void draw(int x, int y) {
        System.out.println("Çizilen daire: Konum(" + x + ", " + y + "), Renk: " + color);
    }
}
```

#### FlyweightFactory (Flyweight Fabrikası):

Flyweight nesnelerini yöneten ve gerektiğinde mevcut nesneleri geri döndüren bir fabrikadır. Bu sınıf, nesneleri merkezi bir havuzda tutar ve aynı nesneden birden fazla istendiğinde, yeni bir nesne oluşturmak yerine mevcut nesneyi döndürür.

```java 
// FlyweightFactory
import java.util.HashMap;
import java.util.Map;

class ShapeFactory {
    private static final Map<String, Shape> shapeMap = new HashMap<>();

    public static Shape getShape(String color) {
        Shape shape = shapeMap.get(color);
        
        if (shape == null) {
            shape = new Circle(color);  // Yeni nesne oluşturuluyor
            shapeMap.put(color, shape);  // Kaydediliyor
            System.out.println(color + " renkli daire oluşturuldu");
        }

        return shape;  // Var olan nesne döndürülüyor
    }
}
```

#### Client (Kullanım):

İstemci sınıfı, Flyweight nesnelerini kullanır. Bu sınıf, nesneleri kullanırken, sadece değişken olan verileri (örn. konum) gönderir, ortak verileri ise Flyweight nesnesi üzerinden alır.

```java 
// Client
public class Main {
    public static void main(String[] args) {
        // Renkleri paylaşarak şekillerin çizilmesi
        Shape redCircle = ShapeFactory.getShape("Kırmızı");
        redCircle.draw(10, 20);

        Shape blueCircle = ShapeFactory.getShape("Mavi");
        blueCircle.draw(30, 40);

        // Aynı kırmızı daire tekrar kullanılıyor
        Shape anotherRedCircle = ShapeFactory.getShape("Kırmızı");
        anotherRedCircle.draw(50, 60);
    }
}
```

#### Çıktı

``` 
Kırmızı renkli daire oluşturuldu
Çizilen daire: Konum(10, 20), Renk: Kırmızı
Mavi renkli daire oluşturuldu
Çizilen daire: Konum(30, 40), Renk: Mavi
Kırmızı renkli daire oluşturuldu
Çizilen daire: Konum(50, 60), Renk: Kırmızı
```

## Adapter

### Amaç?

Bir sınıfın arayüzünü başka bir arayüze dönüştürerek uyumluluk sağlamaktır. Yani, birbiriyle uyumsuz olan iki sınıfın birlikte çalışmasına olanak tanır. Adapter, mevcut kodu değiştirmeden yeni bir yapı ile uyumlu hale getirir.

### Ne Zaman Kullanılır?

Bir sunum yaparken laptopunda yalnızca USB çıkışı var, fakat projeksiyon cihazı HDMI girişini kabul ediyor. Sunumu yapabilmek için USB’den HDMI’ya bir adaptör kullanman gerekiyor.

#### Hedef Arayüz

```java
// HDMI bağlantısı (Target Interface)
interface HDMI {
void connectWithHDMI();
}
```

#### Adaptee

```java
// USB bağlantısı (Adaptee)
class USB {
    public void connectWithUSB() {
        System.out.println("USB üzerinden bağlantı sağlandı.");
    }
}
```

#### Adaptör

```java
// USB'den HDMI'ya dönüştürücü Adapter
class USBToHDMIAdapter implements HDMI {
    private USB usbDevice;

    public USBToHDMIAdapter(USB usbDevice) {
        this.usbDevice = usbDevice;
    }

    @Override
    public void connectWithHDMI() {
        // USB bağlantısını HDMI olarak kullanıyoruz
        usbDevice.connectWithUSB();
        System.out.println("HDMI bağlantısı için adaptör kullanıldı.");
    }
}
```

#### Kullanım

```java
public class Main {
    public static void main(String[] args) {
        // USB cihazını oluştur
        USB usbDevice = new USB();

        // Adapter kullanarak HDMI'ya uyumlu hale getir
        HDMI hdmiAdapter = new USBToHDMIAdapter(usbDevice);

        // HDMI bağlantısı sağla
        hdmiAdapter.connectWithHDMI();
    }
}
```

#### Çıktı

```
USB üzerinden bağlantı sağlandı.
HDMI bağlantısı için adaptör kullanıldı.
```

## Composite

### Amaç?

Nesneleri ağaç yapısı şeklinde organize etmek için kullanılır. Tekil nesnelerle (yapraklar) bileşik nesneleri (düğümler) aynı şekilde işlemek için kullanılır. Bu sayede, bir nesne grubunu ve tek bir nesneyi aynı arayüzle ele alabiliriz.

### Ne Zaman Kullanılır?

Bir dosya sistemi düşün:
- Dosyalar ve klasörler vardır.
- Klasörler başka dosyalar veya klasörler içerebilir.
- Dosyalar ve klasörler üzerinde aynı işlemler yapılabilir (örneğin, boyut hesaplama).

Bu senaryoyu Composite tasarım kalıbıyla modelleyelim.

#### Genel Arayüz

Bu arayüz, dosyalar ve klasörlerin ortak davranışlarını tanımlar.

```java
// Ortak arayüz (Component)
interface FileSystemComponent {
void showDetails();
}
```

#### Yaprak (Leaf)

Tekil nesne olan dosyaları temsil eder.

```java
// Dosya (Leaf)
class File implements FileSystemComponent {
    private String name;

    public File(String name) {
        this.name = name;
    }

    @Override
    public void showDetails() {
        System.out.println("Dosya: " + name);
    }
}
```

#### Bileşik (Composite)

Klasörleri temsil eder. Klasörler, başka dosyaları veya klasörleri içerir.

```java
// Klasör (Composite)
import java.util.ArrayList;
import java.util.List;

class Folder implements FileSystemComponent {
    private String name;
    private List<FileSystemComponent> components = new ArrayList<>();

    public Folder(String name) {
        this.name = name;
    }

    public void add(FileSystemComponent component) {
        components.add(component);
    }

    public void remove(FileSystemComponent component) {
        components.remove(component);
    }

    @Override
    public void showDetails() {
        System.out.println("Klasör: " + name);
        for (FileSystemComponent component : components) {
            component.showDetails();
        }
    }
}
```

#### Kullanım

Dosya ve klasörleri ağaç yapısı olarak organize edip detaylarını gösterelim.

```java
public class Main {
    public static void main(String[] args) {
        // Dosyalar oluştur
        File file1 = new File("Dosya1.txt");
        File file2 = new File("Dosya2.txt");

        // Alt klasör oluştur
        Folder subFolder = new Folder("Alt Klasör");
        subFolder.add(file1);

        // Ana klasör oluştur
        Folder mainFolder = new Folder("Ana Klasör");
        mainFolder.add(file2);
        mainFolder.add(subFolder);

        // Tüm yapıyı göster
        mainFolder.showDetails();
    }
}
```

#### Çıktı

```
Klasör: Ana Klasör
Dosya: Dosya2.txt
Klasör: Alt Klasör
Dosya: Dosya1.txt
```

## Facade

### Amaç?

Facade tasarım deseni, karmaşık bir sistemi basitleştirerek dış dünyaya daha kolay bir arayüz sunmayı amaçlar. Birden fazla sınıf ve metottan oluşan karmaşık yapılar, Facade ile tek bir arayüz üzerinden erişilebilir hale gelir.

### Ne Zaman Kullanılır?

Bir ev sinema sistemi düşünelim. Bu sistemde:
- TV 
- Ses sistemi 
- Blu-ray oynatıcı gibi bileşenler var.

Bu cihazları ayrı ayrı açmak, ayarlamak yerine tek bir arayüz (Facade) ile tüm sistemi çalıştırabiliriz.

#### Alt Sistem Sınıfları:

Her bir cihaz kendi işlevine sahiptir.

```java
// TV
class TV {
    public void turnOn() {
        System.out.println("TV açıldı.");
    }

    public void turnOff() {
        System.out.println("TV kapatıldı.");
    }
}

// Ses Sistemi
class SoundSystem {
    public void setVolume(int level) {
        System.out.println("Ses seviyesi " + level + " olarak ayarlandı.");
    }
}

// Blu-ray Oynatıcı
class BluRayPlayer {
    public void play() {
        System.out.println("Blu-ray oynatılıyor.");
    }
}
```

#### Facade Sınıfı:

Karmaşık alt sistemleri basit bir arayüzle birleştirir.

```java
// Ev Sinema Sistemi Facade
class HomeTheaterFacade {
    private TV tv;
    private SoundSystem soundSystem;
    private BluRayPlayer bluRayPlayer;

    public HomeTheaterFacade(TV tv, SoundSystem soundSystem, BluRayPlayer bluRayPlayer) {
        this.tv = tv;
        this.soundSystem = soundSystem;
        this.bluRayPlayer = bluRayPlayer;
    }

    public void watchMovie() {
        System.out.println("Film izlemeye hazırlanılıyor...");
        tv.turnOn();
        soundSystem.setVolume(5);
        bluRayPlayer.play();
        System.out.println("Film keyfi başladı!");
    }

    public void endMovie() {
        System.out.println("Film izlemeyi bitir...");
        tv.turnOff();
        System.out.println("Sistem kapatıldı.");
    }
}
```
#### Kullanım:

Facade sınıfını kullanarak tüm sistemi basitçe yönetelim.

```java
public class Main {
    public static void main(String[] args) {
        TV tv = new TV();
        SoundSystem soundSystem = new SoundSystem();
        BluRayPlayer bluRayPlayer = new BluRayPlayer();

        // Facade oluştur
        HomeTheaterFacade homeTheater = new HomeTheaterFacade(tv, soundSystem, bluRayPlayer);

        // Film başlat
        homeTheater.watchMovie();

        // Film bitir
        homeTheater.endMovie();
    }
}
```

#### Çıktı:

```
Film izlemeye hazırlanılıyor...
TV açıldı.
Ses seviyesi 5 olarak ayarlandı.
Blu-ray oynatılıyor.
Film keyfi başladı!
Film izlemeyi bitir...
TV kapatıldı.
Sistem kapatıldı.
```

## Proxy

### Amaç?

Proxy tasarım deseni, başka bir nesnenin temsilcisi olan bir nesne sağlar. Bu, gerçek nesneye erişimi kontrol etmek veya sınırlamak amacıyla kullanılır. Proxy, genellikle performans, güvenlik veya nesnenin karmaşıklığını soyutlamak için kullanılır.

### Ne Zaman Kullanılır?

Bir video platformu düşünün. Kullanıcı videoları izlemek istediğinde, video dosyası henüz yüklenmiş olmayabilir. Video dosyasını sadece gerektiğinde (izleme zamanı) yüklemek için Proxy deseni kullanabiliriz. Burada Proxy, video yüklemesini geciktirir (Lazy Loading).

#### Gerçek Nesne (RealSubject):

Gerçek video dosyasını temsil eder.

```java
// Gerçek Video (RealSubject)
class Video {
    private String videoFile;

    public Video(String videoFile) {
        this.videoFile = videoFile;
    }

    public void loadVideo() {
        System.out.println(videoFile + " videosu yüklendi.");
    }

    public void play() {
        System.out.println(videoFile + " oynatılıyor...");
    }
}
```

#### Proxy (Vekil) Sınıfı:

Gerçek video dosyasını yüklemeden önce, proxy sınıfı video dosyasını yalnızca gerektiğinde yükler.

```java
// Video Proxy (Proxy)
class VideoProxy {
    private Video realVideo;
    private String videoFile;

    public VideoProxy(String videoFile) {
        this.videoFile = videoFile;
    }

    public void play() {
        // Video yüklendiyse doğrudan oynat, yoksa yükle
        if (realVideo == null) {
            realVideo = new Video(videoFile);
            realVideo.loadVideo(); // Lazy loading
        }
        realVideo.play();
    }
}
```

#### Kullanım:

Proxy kullanarak video dosyasını sadece gerektiğinde yükleyelim.

```java
public class Main {
    public static void main(String[] args) {
        // Proxy'yi oluştur
        VideoProxy videoProxy = new VideoProxy("Java Proxy Deseni");

        // Video başlat: video dosyasını ancak izleme zamanı yüklüyoruz
        videoProxy.play();  // İlk defa oynatırken video yüklenecek

        // Aynı video tekrar oynatılabilir, ancak önceden yüklenmiş olacak
        videoProxy.play();  // Video hemen oynatılacak, tekrar yüklemeyecek
    }
}
```

#### Çıktı

```
Java Proxy Deseni videosu yüklendi.
Java Proxy Deseni oynatılıyor...
Java Proxy Deseni oynatılıyor...
```

## Decorator

### Amaç?

bir nesnenin işlevselliğini dinamik olarak genişletmek için kullanılır. Bu desen, var olan sınıflara yeni özellikler eklemek için kullanılır, ancak bu sınıfların değiştirilmesini gerektirmez. Temelde, nesnelerin davranışını zarf içinde (wrap) ekleme işlemi yapar.

### Ne Zaman Kullanılır?

Bir kahve siparişi verirken, kahvenin üzerine ekstra eklemeler (süt, şeker, çikolata vb.) yapabilirsiniz. Bu eklemeleri dekoratör deseniyle dinamik olarak gerçekleştirebiliriz.

#### Temel Kahve Sınıfı (Component):

Öncelikle, bir temel Kahve sınıfı tanımlarız.

```java
// Temel Kahve Sınıfı (Component)
interface Coffee {
    double cost();  // Kahvenin maliyeti
}
```

#### Basit Kahve (ConcreteComponent):

Basit bir kahve türü oluşturuyoruz.

```java
// Basit Kahve (ConcreteComponent)
class SimpleCoffee implements Coffee {
    @Override
    public double cost() {
        return 5.00; // Temel kahve fiyatı
    }
}
```

#### Decorator Sınıfı:

Decorator sınıfı, Coffee nesnesinin referansını alır ve temel işlevselliğini genişletir.

```java
// Dekoratör (Decorator)
class CoffeeDecorator implements Coffee {
    protected Coffee decoratedCoffee;

    public CoffeeDecorator(Coffee coffee) {
        this.decoratedCoffee = coffee;
    }

    @Override
    public double cost() {
        return decoratedCoffee.cost();  // Temel kahvenin fiyatını döner
    }
}
```

#### Ekstra Malzemeler (Concrete Decorators):

Dekoratör sınıfı sayesinde, farklı ekstra malzemeler (süt, şeker vb.) ekleyebiliriz.

```java
// Süt Eklenmiş Kahve (Concrete Decorator)
class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public double cost() {
        return decoratedCoffee.cost() + 1.00; // Süt eklemek için fiyat ekler
    }
}

// Şeker Eklenmiş Kahve (Concrete Decorator)
class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public double cost() {
        return decoratedCoffee.cost() + 0.50; // Şeker eklemek için fiyat ekler
    }
}
```

#### Kullanım:

Şimdi bu dekoratörleri kullanarak bir kahve oluşturabiliriz.

```java
public class Main {
    public static void main(String[] args) {
        // Basit bir kahve oluştur
        Coffee myCoffee = new SimpleCoffee();
        System.out.println("Fiyat: " + myCoffee.cost() + " TL");

        // Sütlü kahve oluştur
        myCoffee = new MilkDecorator(myCoffee);
        System.out.println("Fiyat: " + myCoffee.cost() + " TL");

        // Şekerli ve sütlü kahve oluştur
        myCoffee = new SugarDecorator(myCoffee);
        System.out.println("Fiyat: " + myCoffee.cost() + " TL");
    }
}
```

#### Çıktı

```
Fiyat: 5.0 TL
Fiyat: 6.0 TL
Fiyat: 6.5 TL
```

## Bridge

### Amaç?

bir nesnenin soyutlamasını (abstraction) ve onun gerçekleştirilmesini (implementation) birbirinden bağımsız olarak geliştirmeyi amaçlar. Bu desen, nesnenin soyutlaması ile gerçekleştirilmesi arasındaki bağı ortadan kaldırır ve her ikisinin de bağımsız bir şekilde değişmesine olanak tanır. Böylece her iki taraf da birbirinden etkilenmeden geliştirilebilir ve değiştirebilir.

### Ne Zaman Kullanılır?

Bir grafik programı düşünün. Programın farklı aygıtlar üzerinde şekiller çizmesi gerekmektedir (örneğin, bir ekran, yazıcı veya başka bir çıktı aygıtı). Aynı soyutlama (örneğin, bir şekil çizme) işlemi, her aygıt için farklı bir şekilde gerçekleştirilmelidir. Bu durumda Bridge deseni kullanarak soyutlama ve implementasyonu birbirinden ayırabiliriz.

#### Implementor Arayüzü (Aygıtlar):

Çizim yapılacak aygıtların temel fonksiyonları burada tanımlanır.

```java 
// Implementor Arayüzü (Aygıtlar)
interface DrawingAPI {
    void drawCircle(double x, double y, double radius);  // Çizim işlemi
}
```

#### Concrete Implementors (Gerçekleştiriciler):

Gerçekleştiriciler, farklı aygıt türlerini (Ekran, Yazıcı vb.) temsil eder ve çizim işlemini gerçekleştirir. 

```java 
// Ekran Çizim Aygıtı (Concrete Implementor)
class DrawingAPI1 implements DrawingAPI {
    @Override
    public void drawCircle(double x, double y, double radius) {
        System.out.println("Ekran üzerinde daire çizildi: (" + x + ", " + y + ") yarıçap: " + radius);
    }
}

// Yazıcı Çizim Aygıtı (Concrete Implementor)
class DrawingAPI2 implements DrawingAPI {
    @Override
    public void drawCircle(double x, double y, double radius) {
        System.out.println("Yazıcıda daire çizildi: (" + x + ", " + y + ") yarıçap: " + radius);
    }
}
```

#### Abstraction (Soyutlama):

Soyutlama sınıfı, DrawingAPI ile etkileşimde bulunarak farklı çizim işlemlerini yönetir.

```java 
// Soyutlama (Abstraction)
abstract class Shape {
    protected DrawingAPI drawingAPI;

    protected Shape(DrawingAPI drawingAPI) {
        this.drawingAPI = drawingAPI;
    }

    public abstract void draw();  // Şekli çizme
    public abstract void resizeByPercentage(double pct);  // Şekli büyütme/küçültme
}
```

#### Refined Abstraction (İleri Düzey Soyutlama):

Soyutlama sınıfının alt sınıfı, belirli şekil türlerini temsil eder (örneğin, daire).

```java 
// Daire (Refined Abstraction)
class Circle extends Shape {
    private double x, y, radius;

    public Circle(double x, double y, double radius, DrawingAPI drawingAPI) {
        super(drawingAPI);
        this.x = x;
        this.y = y;
        this.radius = radius;
    }

    @Override
    public void draw() {
        drawingAPI.drawCircle(x, y, radius);  // Çizim işlemi için ilgili implementor'u çağırır
    }

    @Override
    public void resizeByPercentage(double pct) {
        radius *= (1 + pct / 100.0);  // Yüzdeye göre büyütme/küçültme
    }
}
```

#### Kullanım:

Şimdi bu köprü desenini kullanarak şekilleri çizelim.

```java 
public class Main {
    public static void main(String[] args) {
        // Ekranda çizim yapmak için API1, Yazıcıda çizim için API2 kullanılıyor
        Shape shape1 = new Circle(1.0, 2.0, 3.0, new DrawingAPI1());
        Shape shape2 = new Circle(5.0, 7.0, 8.0, new DrawingAPI2());

        // Çizim işlemi
        shape1.draw();
        shape2.draw();

        // Şekli büyütme
        shape1.resizeByPercentage(10);
        shape1.draw();
    }
}
```

#### Çıktı

```
Ekran üzerinde daire çizildi: (1.0, 2.0) yarıçap: 3.0
Yazıcıda daire çizildi: (5.0, 7.0) yarıçap: 8.0
Ekran üzerinde daire çizildi: (1.0, 2.0) yarıçap: 3.3
```

## Singleton

### Amaç?

Bir sınıfın yalnızca bir örneğinin oluşturulmasını ve bu örneğe global bir erişim noktası sağlanmasını garanti eder. Bu desen, nesnenin global erişilebilirliğini sağlar, yani sadece bir tane nesne olduğundan, tüm uygulama bu nesneye başvurur.

### Ne Zaman Kullanılır?

Bir uygulama düşünün, burada tüm hata ve bilgi mesajlarını kaydetmek için bir loglama sistemi kullanmak istiyorsunuz. Bu loglama sistemi, uygulama boyunca yalnızca bir kez oluşturulmalı, çünkü her yerde aynı loglama nesnesi kullanılacak.

#### Singleton Sınıfı

Bu sınıf sadece bir örneğini oluşturacak ve uygulama genelinde her yerden bu nesneye erişim sağlayacaktır.

```java 
// Singleton sınıfı
public class Logger {
    private static Logger instance;  // Sınıfın tek örneği
    private Logger() {  // private constructor, dışarıdan nesne oluşturulmasını engeller
        // Loglama işlemleri için başlatma kodları
    }

    // Nesne örneğini döndüren statik metod
    public static Logger getInstance() {
        if (instance == null) {
            instance = new Logger();  // Nesne yalnızca ilk kez çağrıldığında oluşturulur
        }
        return instance;  // Her durumda aynı nesneyi döndürür
    }

    // Loglama işlevi
    public void log(String message) {
        System.out.println("Log: " + message);
    }
}
```

#### Client Kullanımı

Uygulamanın her yerinde Logger sınıfını kullanırken, getInstance() metoduyla her zaman aynı nesneyi alırsınız.

```java 
public class Main {
    public static void main(String[] args) {
        // Logger sınıfının tek örneği kullanılıyor
        Logger logger1 = Logger.getInstance();
        logger1.log("Uygulama başlatıldı!");

        Logger logger2 = Logger.getInstance();
        logger2.log("Veritabanı bağlantısı kuruldu.");

        // Her iki logger aynı nesneyi işaret eder
        if (logger1 == logger2) {
            System.out.println("logger1 ve logger2 aynı nesne.");
        }
    }
}
```

#### Çıktı

``` 
Log: Uygulama başlatıldı!
Log: Veritabanı bağlantısı kuruldu.
logger1 ve logger2 aynı nesne.
```

## Factory

### Amaç?

Nesne oluşturma işlemini bir fabrika metodu aracılığıyla yapmak amacıyla kullanılır. Bu desen, nesne oluşturma sürecini soyutlar ve istemciden (client) nesne oluşturma detaylarını gizler. Yani, bir sınıfın nesnesi oluşturulurken kullanılan türlerin belirlenmesi ve yaratılması süreci, doğrudan istemciden bağımsız bir hale gelir. Factory deseni, genellikle nesne türlerini değiştirmek ya da nesne oluşturma mantığını değiştirmek gerektiğinde kullanılır.

### Ne Zaman Kullanılır?

Bir araba üretim fabrikası düşünün. Bu fabrikada farklı araba türleri (örneğin Sedan, SUV) üretiliyor, ancak istemci yalnızca bir arabanın üretildiğini bilir. Üretim türü (Sedan mı SUV mi?) tamamen fabrika tarafından belirlenir. İstemci, hangi türde araba istediğini bilemez; sadece bir araba ister.

#### Araba Sınıfı (Product)

Öncelikle, üretilecek olan arabaların temel sınıfını tanımlayalım.

```java 
// Ürün sınıfı
public abstract class Car {
    public abstract void drive();
}
```

#### Somut Araba Sınıfları (Concrete Products)

Farklı araba türlerini temsil eden sınıflar.

```java 
// Sedan sınıfı
public class Sedan extends Car {
    @Override
    public void drive() {
        System.out.println("Sedan araba sürülüyor.");
    }
}

// SUV sınıfı
public class SUV extends Car {
    @Override
    public void drive() {
        System.out.println("SUV araba sürülüyor.");
    }
}
```

#### Factory Sınıfı (Factory)

Nesne oluşturma işini yönetecek fabrika sınıfı. İstemciden bağımsız olarak nesneleri yaratır.

```java 
// Factory sınıfı
public class CarFactory {
    public Car createCar(String type) {
        if (type.equalsIgnoreCase("Sedan")) {
            return new Sedan();
        } else if (type.equalsIgnoreCase("SUV")) {
            return new SUV();
        }
        return null;
    }
}
```

#### Client Kullanımı:

İstemci, doğrudan araba nesnelerini kendisi yaratmaz. Bunun yerine, fabrika sınıfını kullanarak araba nesnelerini oluşturur.

```java 
public class Main {
    public static void main(String[] args) {
        CarFactory factory = new CarFactory();
        
        // İstemci, hangi türde araba istediğini belirtir, ancak üretim süreci gizlidir
        Car sedan = factory.createCar("Sedan");
        sedan.drive();  // Sedan araba sürülüyor.

        Car suv = factory.createCar("SUV");
        suv.drive();  // SUV araba sürülüyor.
    }
}
```

#### Çıktı

``` 
Sedan araba sürülüyor.
SUV araba sürülüyor.
```

## Abstract Factory

### Amaç?

Benzer türdeki nesneleri üretebilen bir arayüz (interface) sağlar, ancak istemcinin hangi sınıfın nesnesini oluşturduğuna dair bilgisi yoktur. Bu desen, çeşitli nesne ailesi türlerinin oluşturulmasını sağlar ve her nesne ailesi farklı tiplerde nesneler içerir. İstemci yalnızca bir tür nesne ailesi ile etkileşime girer, böylece her bir nesne ailesinin üyeleri uyumlu ve tutarlı olur.

### Ne Zaman Kullanılır?

Bir kullanıcı arayüzü (UI) için farklı platformlarda (örneğin, Windows, Mac) benzer bileşenler (buton, menü, pencere) üretmek istiyoruz. Her platformun kendi bileşenleri farklı olabilir, ancak her platforma ait bileşenler uyumlu olmalıdır. İstemci yalnızca platforma özgü bir bileşen ailesi ister, fabrika sınıfı ise doğru platforma uygun bileşenleri yaratacaktır.

#### Bileşen Sınıfları (Product Interfaces)

Öncelikle farklı platformlar için ortak arayüzleri tanımlayalım.

```java
// Buton arayüzü (Button)
public interface Button {
    void render();
}

// Pencere arayüzü (Window)
public interface Window {
    void render();
}
```

#### Somut Bileşen Sınıfları (Concrete Products)

Farklı platformlara ait somut bileşen sınıfları.

```java
// Windows için Buton
public class WindowsButton implements Button {
    @Override
    public void render() {
        System.out.println("Windows butonu render ediliyor.");
    }
}

// Windows için Pencere
public class WindowsWindow implements Window {
    @Override
    public void render() {
        System.out.println("Windows penceresi render ediliyor.");
    }
}

// Mac için Buton
public class MacButton implements Button {
    @Override
    public void render() {
        System.out.println("Mac butonu render ediliyor.");
    }
}

// Mac için Pencere
public class MacWindow implements Window {
    @Override
    public void render() {
        System.out.println("Mac penceresi render ediliyor.");
    }
}
```

#### Abstract Factory Arayüzü (Abstract Factory Interface)

Bu arayüz, her platform için ürünleri üreten metodları tanımlar.

```java
// Abstract Factory arayüzü
public interface GUIFactory {
    Button createButton();
    Window createWindow();
}
```

#### Somut Factory Sınıfları (Concrete Factories)

Her platform için somut fabrika sınıfları, doğru bileşenleri üretir.

```java
// Windows için Fabrika
public class WindowsFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new WindowsButton();
    }

    @Override
    public Window createWindow() {
        return new WindowsWindow();
    }
}

// Mac için Fabrika
public class MacFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new MacButton();
    }

    @Override
    public Window createWindow() {
        return new MacWindow();
    }
}
```

#### Client Kullanımı

İstemci kodu, yalnızca ilgili platformu seçer ve doğru bileşen ailesi ile etkileşime girer.

```java
public class Application {
    private Button button;
    private Window window;

    // Factory nesnesini kabul eder
    public Application(GUIFactory factory) {
        button = factory.createButton();
        window = factory.createWindow();
    }

    public void renderUI() {
        button.render();
        window.render();
    }

    public static void main(String[] args) {
        // İstemci, platforma bağlı olarak doğru fabrika sınıfını seçer
        GUIFactory factory = new WindowsFactory(); // ya da new MacFactory()
        Application app = new Application(factory);

        // UI render edilir
        app.renderUI();
    }
}
```

#### Çıktı

Eğer WindowsFactory kullanılırsa:

```
Windows butonu render ediliyor.
Windows penceresi render ediliyor.
```

Eğer MacFactory kullanılırsa:

```
Mac butonu render ediliyor.
Mac penceresi render ediliyor.
```

## Builder

### Amaç?

Bir nesnenin oluşturulma sürecini soyutlamak ve bu süreci adım adım kontrol edebilmek için kullanılır. Builder deseni, bir nesnenin kompleks yapılarının oluşturulmasını adım adım düzenleyerek nesne oluşturmayı daha kontrollü hale getirir. Bu desen, özellikle çok sayıda parametreye sahip, karmaşık nesneler oluşturulurken faydalıdır. Builder deseni, istemciye nesneyi nasıl oluşturduğuna dair detayları gizler ve sadece nesnenin oluşturulmuş halini sunar.

### Ne Zaman Kullanılır?

Bir evin inşası sürecini düşünelim. Bir evin duvarları, pencereleri, kapıları, çatısı gibi birçok farklı bileşeni olabilir. Bu bileşenlerin kombinasyonları ve inşa sıralamaları farklılık gösterebilir. Builder deseni, her bir bileşeni adım adım inşa etmek için kullanılabilir.

#### Ev Bileşenleri (Product)

Ev, bir dizi bileşenden oluşur. Bu bileşenlerin her birini tanımlayalım:

```java 
// Ev sınıfı (Product)
public class House {
    private String foundation;
    private String walls;
    private String roof;
    private String doors;
    private String windows;

    // Getter ve Setter metodları
    public void setFoundation(String foundation) {
        this.foundation = foundation;
    }
    public void setWalls(String walls) {
        this.walls = walls;
    }
    public void setRoof(String roof) {
        this.roof = roof;
    }
    public void setDoors(String doors) {
        this.doors = doors;
    }
    public void setWindows(String windows) {
        this.windows = windows;
    }

    @Override
    public String toString() {
        return "House{" +
                "foundation='" + foundation + '\'' +
                ", walls='" + walls + '\'' +
                ", roof='" + roof + '\'' +
                ", doors='" + doors + '\'' +
                ", windows='" + windows + '\'' +
                '}';
    }
}
```

#### Builder Arayüzü (Builder Interface)

Builder arayüzü, evin her bileşenini oluşturacak metotları tanımlar.

```java 
// Builder arayüzü
public interface HouseBuilder {
    void buildFoundation();
    void buildWalls();
    void buildRoof();
    void buildDoors();
    void buildWindows();
    House getResult();
}
```

#### Somut Builder Sınıfları (Concrete Builder)

Ev inşa sürecini adım adım kontrol etmek için farklı builder sınıfları tanımlayalım. Örneğin, modern bir ev ve geleneksel bir ev inşa edelim.

```java 
// ModernEvBuilder
public class ModernHouseBuilder implements HouseBuilder {
    private House house;

    public ModernHouseBuilder() {
        this.house = new House();
    }

    @Override
    public void buildFoundation() {
        house.setFoundation("Modern beton temel");
    }

    @Override
    public void buildWalls() {
        house.setWalls("Modern alçıpan duvarlar");
    }

    @Override
    public void buildRoof() {
        house.setRoof("Düz çatı");
    }

    @Override
    public void buildDoors() {
        house.setDoors("Cam kapı");
    }

    @Override
    public void buildWindows() {
        house.setWindows("Büyük cam pencereler");
    }

    @Override
    public House getResult() {
        return house;
    }
}

// GelenekselEvBuilder
public class TraditionalHouseBuilder implements HouseBuilder {
    private House house;

    public TraditionalHouseBuilder() {
        this.house = new House();
    }

    @Override
    public void buildFoundation() {
        house.setFoundation("Taş temel");
    }

    @Override
    public void buildWalls() {
        house.setWalls("Tuğla duvarlar");
    }

    @Override
    public void buildRoof() {
        house.setRoof("Kare çatı");
    }

    @Override
    public void buildDoors() {
        house.setDoors("Ahşap kapılar");
    }

    @Override
    public void buildWindows() {
        house.setWindows("Küçük pencereler");
    }

    @Override
    public House getResult() {
        return house;
    }
}
```

#### Director (Yönetici)

Yönetici sınıfı, nesnenin nasıl oluşturulacağına dair adımları sıralar. İstemci, yönetici sınıfını kullanarak nesnenin adım adım inşa edilmesini sağlar.

```java 
// Director sınıfı
public class Director {
    private HouseBuilder builder;

    public Director(HouseBuilder builder) {
        this.builder = builder;
    }

    public void constructHouse() {
        builder.buildFoundation();
        builder.buildWalls();
        builder.buildRoof();
        builder.buildDoors();
        builder.buildWindows();
    }
}
```

#### İstemci Kullanımı

İstemci, hangi ev türünü inşa etmek istediğini belirler ve süreci başlatır.

```java 
// İstemci Kullanımı
public class Client {
    public static void main(String[] args) {
        // ModernEvBuilder ile bir ev inşa et
        HouseBuilder modernHouseBuilder = new ModernHouseBuilder();
        Director director = new Director(modernHouseBuilder);
        director.constructHouse();
        House modernHouse = modernHouseBuilder.getResult();
        System.out.println(modernHouse);

        // GelenekselEvBuilder ile bir ev inşa et
        HouseBuilder traditionalHouseBuilder = new TraditionalHouseBuilder();
        director = new Director(traditionalHouseBuilder);
        director.constructHouse();
        House traditionalHouse = traditionalHouseBuilder.getResult();
        System.out.println(traditionalHouse);
    }
}
```

#### Çıktı

```
House{
    foundation='Modern beton temel',
    walls='Modern alçıpan duvarlar',
    roof='Düz çatı', 
    doors='Cam kapı',
    windows='Büyük cam pencereler'
}
House{
    foundation='Taş temel',
    walls='Tuğla duvarlar',
    roof='Kare çatı',
    doors='Ahşap kapılar',
    windows='Küçük pencereler'
}
```

## Prototype

### Amaç?

bir nesnenin mevcut bir örneğini (prototype) kopyalayarak yeni nesneler oluşturulmasını sağlar. Yani, nesnelerin doğrudan bir sınıfı tarafından oluşturulmak yerine, daha önce var olan bir nesnenin bir kopyası alınarak yeni bir nesne oluşturulur. Bu desen, nesne oluşturma maliyetlerinin yüksek olduğu veya nesneler benzer yapıda ve özelliklerde olduğu durumlarda kullanışlıdır.

### Ne Zaman Kullanılır?

Bir grafik düzenleyici uygulaması üzerinde çalıştığınızı düşünün. Uygulama içerisinde şekiller çizilir. Bu şekillerin bazıları karmaşık yapılar olabilir. Eğer her seferinde sıfırdan bir şekil çizmek yerine, mevcut bir şeklin kopyasını almak daha hızlı ve verimli olacaktır.

#### Prototype Arayüzü (Prototype Interface)

Öncelikle kopyalanabilir nesneler için bir arayüz tanımlarız. Bu arayüzde, nesnenin kopyalanabilmesi için bir clone() metodunun tanımlanması gerekir.

```java
// Prototype arayüzü
public interface Shape {
    Shape clone();
    void draw();
}
```

#### Somut Nesneler (Concrete Prototype)

Sonrasında, kopyalanabilecek somut nesneleri tanımlarız. Bu nesneler, şekil nesneleri olabilir (örneğin daire, kare).

```java
// ConcretePrototype: Daire
public class Circle implements Shape {
    private int radius;

    public Circle(int radius) {
        this.radius = radius;
    }

    @Override
    public Shape clone() {
        return new Circle(this.radius); // Yeni bir daire kopyası oluşturulur
    }

    @Override
    public void draw() {
        System.out.println("Çevresi çiziliyor, yarıçap: " + radius);
    }

    public void setRadius(int radius) {
        this.radius = radius;
    }
}

// ConcretePrototype: Kare
public class Square implements Shape {
    private int sideLength;

    public Square(int sideLength) {
        this.sideLength = sideLength;
    }

    @Override
    public Shape clone() {
        return new Square(this.sideLength); // Yeni bir kare kopyası oluşturulur
    }

    @Override
    public void draw() {
        System.out.println("Kenar uzunluğu: " + sideLength);
    }

    public void setSideLength(int sideLength) {
        this.sideLength = sideLength;
    }
}
```

#### Prototype Manager (Client)

İstemci, kopyalama işlemi için bir Prototype Manager veya Factory sınıfı kullanabilir. Bu sınıf, kopyalama işlemi yapacak olan örnekleri tutar ve istemciye gerektiği zaman kopya sağlar.

```java
// Prototype Manager sınıfı
public class ShapeManager {
    private Shape circlePrototype;
    private Shape squarePrototype;

    public ShapeManager() {
        this.circlePrototype = new Circle(5); // Orijinal daire
        this.squarePrototype = new Square(4); // Orijinal kare
    }

    public Shape getCirclePrototype() {
        return circlePrototype.clone(); // Daire kopyası al
    }

    public Shape getSquarePrototype() {
        return squarePrototype.clone(); // Kare kopyası al
    }
}
```

#### İstemci Kullanımı

İstemci, ShapeManager üzerinden prototip nesnelerini alarak, bu nesneler üzerinde işlem yapabilir.

```java
// İstemci Kullanımı
public class Client {
    public static void main(String[] args) {
        ShapeManager shapeManager = new ShapeManager();
        
        // Daire kopyası al
        Shape circle1 = shapeManager.getCirclePrototype();
        circle1.draw(); // Çevresi çiziliyor, yarıçap: 5
        
        // Kare kopyası al
        Shape square1 = shapeManager.getSquarePrototype();
        square1.draw(); // Kenar uzunluğu: 4
        
        // Kopyalanan nesneleri değiştirebiliriz
        Shape circle2 = shapeManager.getCirclePrototype();
        ((Circle)circle2).setRadius(10);
        circle2.draw(); // Çevresi çiziliyor, yarıçap: 10
        
        Shape square2 = shapeManager.getSquarePrototype();
        ((Square)square2).setSideLength(6);
        square2.draw(); // Kenar uzunluğu: 6
    }
}
```

#### Çıktı

```
Çevresi çiziliyor, yarıçap: 5
Kenar uzunluğu: 4
Çevresi çiziliyor, yarıçap: 10
Kenar uzunluğu: 6
```

## Strategy

### Amaç? 

Strategy deseni, bir ailenin benzer algoritmalarını tanımlayıp, bu algoritmaları birbirlerinin yerine kullanılabilir hale getirir. Algoritmayı kullanacak sınıftan bağımsız olarak algoritmaları değiştirebilme esnekliği sağlar.

### Ne Zaman Kullanılır?

Bir e-ticaret uygulamasında farklı ödeme yöntemleriyle (kredi kartı, PayPal, kripto para) ödeme yapılmasını sağlayan bir yapı düşünelim.

1. Kullanıcı, alışverişi tamamladığında ödeme yöntemi seçer.
2. Seçilen ödeme yöntemi doğrultusunda, farklı ödeme işlemleri uygulanır.

#### Strategy Arayüzü

Ödeme işlemini tanımlayan PaymentStrategy adında bir arayüz oluşturulur.

```java
public interface PaymentStrategy {
    void pay(double amount);
}
```

#### Concrete Strategy (Somut Stratejiler)

Her ödeme yöntemi için farklı strateji sınıfları oluşturulur.

```java 
public class CreditCardPayment implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("Kredi kartıyla " + amount + " TL ödendi.");
    }
}

public class PayPalPayment implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("PayPal ile " + amount + " TL ödendi.");
    }
}

public class CryptoPayment implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("Kripto para ile " + amount + " TL ödendi.");
    }
}
```

#### Context (Bağlam) Sınıfı

Kullanıcıdan hangi stratejiyi kullanacağını belirleyen sınıf.

```java 
public class ShoppingCart {
    private PaymentStrategy paymentStrategy;
    
    public ShoppingCart(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }
    
    public void checkout(double amount) {
        paymentStrategy.pay(amount);
    }
}
```

#### Kullanım

```java 
public class Main {
    public static void main(String[] args) {
        ShoppingCart cart1 = new ShoppingCart(new CreditCardPayment());
        cart1.checkout(250.0); // Çıktı: Kredi kartıyla 250.0 TL ödendi.
        
        ShoppingCart cart2 = new ShoppingCart(new PayPalPayment());
        cart2.checkout(150.0); // Çıktı: PayPal ile 150.0 TL ödendi.
    }
}
```

#### Çıktı

```
Kredi kartıyla 250.0 TL ödendi.
PayPal ile 150.0 TL ödendi.
```

## Command

### Amaç?

Command (Komut) tasarım deseni, bir isteği veya işlemi bir nesne olarak encapsulate (kapsülleme) ederek, bu isteği parametreleştirmeyi, sıraya almayı, geri almayı (undo) veya loglamayı kolaylaştırmayı amaçlar. Bu desen, istekleri gönderen nesneler ile istekleri işleyen nesneler arasındaki bağımlılığı azaltır ve esnek bir yapı sunar.

### Ne Zaman Kullanılır?

- Bir işlemi veya isteği nesne olarak temsil etmek istediğinizde.
- İşlemleri sıraya almak, geri almak (undo) veya tekrar etmek (redo) istediğinizde.
- İstekleri gönderen nesneler ile işlemleri gerçekleştiren nesneler arasında gevşek bir bağlantı (loose coupling) sağlamak istediğinizde.
- İşlemleri loglamak veya işlem geçmişini tutmak istediğinizde.

**Senaryo:** Bir akıllı ev sisteminiz olduğunu düşünün. Bu sistemde, uzaktan kumanda ile birden fazla cihazı (lamba, televizyon, klima, ses sistemi vb.) kontrol edebiliyorsunuz. Ayrıca, yanlışlıkla bir cihazı açtığınızda veya kapattığınızda bu işlemi geri alabilmeniz gerekiyor (undo özelliği). İşte bu gibi durumlarda Command Design Pattern kullanılır.

#### Command Arayüzü

```java
interface Command {
    void execute();
    void undo();
}
```

#### ConcreteCommand (Somut Komut)

```java
// Lambayı açma komutu
class LightOnCommand implements Command {
    private Light light;  // Receiver (Alıcı)

    public LightOnCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.on();  // Lambayı aç
    }

    @Override
    public void undo() {
        light.off();  // Lambayı kapat (geri alma)
    }
}

// Lambayı kapatma komutu
class LightOffCommand implements Command {
    private Light light;  // Receiver (Alıcı)

    public LightOffCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.off();  // Lambayı kapat
    }

    @Override
    public void undo() {
        light.on();  // Lambayı aç (geri alma)
    }
}
```

#### Receiver (Alıcı)

```java
// Receiver (Alıcı): Lamba sınıfı
class Light {
    public void on() {
        System.out.println("Lamba açıldı.");
    }

    public void off() {
        System.out.println("Lamba kapatıldı.");
    }
}
```

#### Invoker (Çağıran)

```java
// Invoker (Çağıran): Uzaktan kumanda
class RemoteControl {
    private Command command;

    public void setCommand(Command command) {
        this.command = command;
    }

    public void pressButton() {
        command.execute();  // Komutu çalıştır
    }

    public void pressUndo() {
        command.undo();  // Komutu geri al
    }
}
```

#### Client (İstemci)

```java
public class Main {
    public static void main(String[] args) {
        // Alıcı (Receiver) oluştur
        Light light = new Light();

        // Komutlar oluştur
        Command lightOn = new LightOnCommand(light);
        Command lightOff = new LightOffCommand(light);

        // Invoker (Çağıran) oluştur
        RemoteControl remote = new RemoteControl();

        // Lambayı aç
        remote.setCommand(lightOn);
        remote.pressButton();  // Çıktı: "Lamba açıldı."

        // Lambayı kapat
        remote.setCommand(lightOff);
        remote.pressButton();  // Çıktı: "Lamba kapatıldı."

        // Geri al (undo)
        remote.pressUndo();  // Çıktı: "Lamba açıldı."
    }
}
```

#### Çıktı

```
Lamba açıldı.
Lamba kapatıldı.
Lamba açıldı.
```

## Iterator

### Amaç?

Iterator (Yineleyici) tasarım deseni, bir koleksiyonun (örneğin, liste, dizi, ağaç) elemanlarına sırayla erişmek için kullanılır. Bu desen, koleksiyonun iç yapısını gizler ve elemanlara erişim için standart bir yöntem sunar. Böylece, koleksiyonun nasıl uygulandığından bağımsız olarak elemanlara erişebilirsiniz.

### Ne Zaman Kullanılır?

**Senaryo:** Bir müzik çalma listesi (playlist) uygulaması düşünün. Bu listede birden fazla şarkı bulunuyor ve bu şarkıları sırayla çalmak, önceki şarkıya geri dönmek veya rastgele bir şarkıya atlamak istiyorsunuz. Iterator Design Pattern, bu tür senaryolarda kullanılır.

Örneğin:
- Sırayla Şarkı Çalma: Playlist'teki şarkıları birer birer çalmak istiyorsunuz.
- Rastgele Şarkıya Atlama: Playlist'teki herhangi bir şarkıya atlamak istiyorsunuz.
- Şarkıları Geriye Doğru Çalma: Playlist'teki şarkıları tersten çalmak istiyorsunuz.

Iterator Pattern, bu işlemleri koleksiyonun iç yapısını değiştirmeden gerçekleştirmenizi sağlar. Örneğin, playlist bir dizi, bağlı liste veya başka bir veri yapısı olabilir, ancak Iterator sayesinde bu farkı hissetmezsiniz.

#### Iterator Arayüzü

```java
// Iterator Arayüzü
interface Iterator<T> {
    boolean hasNext();  // Sonraki eleman var mı?
    T next();           // Sonraki elemanı döndür
}
```

#### ConcreteIterator (Somut Iterator)

```java
// Somut Iterator: Playlist'teki şarkıları sırayla dolaşır
class PlaylistIterator implements Iterator<String> {
    private String[] songs;
    private int position = 0;

    public PlaylistIterator(String[] songs) {
        this.songs = songs;
    }

    @Override
    public boolean hasNext() {
        return position < songs.length;
    }

    @Override
    public String next() {
        if (this.hasNext()) {
            return songs[position++];
        }
        return null;
    }
}
```

#### Aggregate (Koleksiyon)

```java
// Aggregate (Koleksiyon) Arayüzü
interface Playlist {
    Iterator<String> createIterator();  // Iterator oluştur
}
```

#### ConcreteAggregate (Somut Koleksiyon)

```java
// Somut Koleksiyon: Playlist
class MusicPlaylist implements Playlist {
    private String[] songs;

    public MusicPlaylist(String[] songs) {
        this.songs = songs;
    }

    @Override
    public Iterator<String> createIterator() {
        return new PlaylistIterator(songs);  // Iterator oluştur
    }
}
```

#### Client (İstemci)

```java
public class Main {
    public static void main(String[] args) {
        // Playlist oluştur
        String[] songs = {"Şarkı 1", "Şarkı 2", "Şarkı 3", "Şarkı 4"};
        Playlist playlist = new MusicPlaylist(songs);

        // Iterator oluştur
        Iterator<String> iterator = playlist.createIterator();

        // Şarkıları sırayla çal
        System.out.println("Playlist'teki şarkılar:");
        while (iterator.hasNext()) {
            String song = iterator.next();
            System.out.println("Çalınıyor: " + song);
        }
    }
}
```

#### Çıktı

```
Playlist'teki şarkılar:
Çalınıyor: Şarkı 1
Çalınıyor: Şarkı 2
Çalınıyor: Şarkı 3
Çalınıyor: Şarkı 4
```

## Template

### Amaç?

Template (Şablon) tasarım deseni, bir algoritmanın iskeletini tanımlar ve bazı adımların alt sınıflar tarafından uygulanmasını sağlar. Bu desen, algoritmanın genel yapısını korurken, belirli adımların değiştirilmesine izin verir. Böylece, kod tekrarı önlenir ve esneklik sağlanır.

### Ne Zaman Kullanılır?

**Senaryo:** Bir kahve dükkanı uygulaması düşünün. Bu dükkanda hem kahve hem de çay hazırlanıyor. Her ikisinin hazırlanma süreci benzer adımlardan oluşur:

- Su kaynatma.
- Demleme (kahve veya çay). 
- Bardakta servis etme. 
- İsteğe bağlı olarak süt veya şeker ekleme.

Ancak, kahve ve çayın demleme adımları farklıdır. Template Design Pattern, bu tür senaryolarda kullanılır. Algoritmanın genel yapısını (su kaynatma, servis etme) korurken, demleme gibi belirli adımları alt sınıflara bırakır.

#### AbstractClass (Soyut Sınıf)

```java
// Soyut Sınıf: İçecek hazırlama şablonu
abstract class Beverage {

    // Template Metodu: Algoritmanın iskeleti
    public final void prepareBeverage() {
        boilWater();       // Su kaynat
        brew();            // Demle (soyut adım)
        pourInCup();       // Bardakta servis et
        addCondiments();   // İsteğe bağlı ek malzemeler (soyut adım)
    }

    // Ortak adım: Su kaynatma
    private void boilWater() {
        System.out.println("Su kaynatılıyor...");
    }

    // Ortak adım: Bardakta servis etme
    private void pourInCup() {
        System.out.println("Bardakta servis ediliyor...");
    }

    // Soyut adım: Demleme (alt sınıflar tarafından uygulanacak)
    protected abstract void brew();

    // Soyut adım: Ek malzemeler (alt sınıflar tarafından uygulanacak)
    protected abstract void addCondiments();
}
```

#### ConcreteClass (Somut Sınıf)

```java
// Somut Sınıf: Kahve hazırlama
class Coffee extends Beverage {

    @Override
    protected void brew() {
        System.out.println("Kahve demleniyor...");
    }

    @Override
    protected void addCondiments() {
        System.out.println("Süt ve şeker ekleniyor...");
    }
}

// Somut Sınıf: Çay hazırlama
class Tea extends Beverage {

    @Override
    protected void brew() {
        System.out.println("Çay demleniyor...");
    }

    @Override
    protected void addCondiments() {
        System.out.println("Limon ekleniyor...");
    }
}
```

#### Client (İstemci)

```java
public class Main {
    public static void main(String[] args) {
        // Kahve hazırla
        System.out.println("Kahve hazırlanıyor:");
        Beverage coffee = new Coffee();
        coffee.prepareBeverage();

        System.out.println();

        // Çay hazırla
        System.out.println("Çay hazırlanıyor:");
        Beverage tea = new Tea();
        tea.prepareBeverage();
    }
}
```

#### Çıktı

```
Kahve hazırlanıyor:
Su kaynatılıyor...
Kahve demleniyor...
Bardakta servis ediliyor...
Süt ve şeker ekleniyor...

Çay hazırlanıyor:
Su kaynatılıyor...
Çay demleniyor...
Bardakta servis ediliyor...
Limon ekleniyor...
```

## Observer

### Amaç?

Observer (Gözlemci) tasarım deseni, bir nesnenin durumu değiştiğinde, bu nesneye bağlı olan diğer nesnelere otomatik olarak bildirim göndermeyi sağlar. Bu desen, nesneler arasında gevşek bir bağlantı (loose coupling) oluşturur ve durum değişikliklerinin takip edilmesini kolaylaştırır.

### Ne Zaman Kullanılır?

**Senaryo:** Bir haber portalı düşünün. Bu portalda yeni haberler yayınlandığında, abonelere otomatik olarak bildirim gönderilsin. 

Örneğin:
- Yeni Haber Yayınlandığında: Aboneler (kullanıcılar) anında haberdar olur. 
- Abonelik İptali: Bir kullanıcı artık bildirim almak istemiyorsa, abonelikten çıkabilir.

Observer Design Pattern, bu tür senaryolarda kullanılır. Haber portalı (Subject) ve aboneler (Observers) arasında gevşek bir bağlantı kurar. Haber portalı, abonelerin kim olduğunu bilmez, sadece bildirim gönderir.

#### Observer Arayüzü

```java
// Observer (Gözlemci) Arayüzü
interface Observer {
    void update(String news);  // Bildirim al
}
```

#### ConcreteObserver (Somut Gözlemci)

```java
// Somut Gözlemci: Abone
class Subscriber implements Observer {
    private String name;

    public Subscriber(String name) {
        this.name = name;
    }

    @Override
    public void update(String news) {
        System.out.println(name + " için yeni haber: " + news);
    }
}
```

#### Subject (Gözlemlenen)

```java
// Subject (Gözlemlenen) Arayüzü
interface NewsAgency {
    void registerObserver(Observer observer);  // Gözlemci ekle
    void removeObserver(Observer observer);    // Gözlemci çıkar
    void notifyObservers(String news);         // Gözlemcilere bildirim gönder
}
```

#### ConcreteSubject (Somut Gözlemlenen)

```java
// Somut Gözlemlenen: Haber Ajansı
class ConcreteNewsAgency implements NewsAgency {
    private List<Observer> observers = new ArrayList<>();

    @Override
    public void registerObserver(Observer observer) {
        observers.add(observer);  // Gözlemci ekle
    }

    @Override
    public void removeObserver(Observer observer) {
        observers.remove(observer);  // Gözlemci çıkar
    }

    @Override
    public void notifyObservers(String news) {
        for (Observer observer : observers) {
            observer.update(news);  // Tüm gözlemcilere bildirim gönder
        }
    }

    // Yeni haber yayınla
    public void publishNews(String news) {
        System.out.println("Yeni haber yayınlandı: " + news);
        notifyObservers(news);  // Gözlemcilere bildirim gönder
    }
}
```

#### Client (İstemci)

```java
public class Main {
    public static void main(String[] args) {
        // Haber ajansı oluştur
        ConcreteNewsAgency newsAgency = new ConcreteNewsAgency();

        // Aboneler oluştur
        Observer subscriber1 = new Subscriber("Ahmet");
        Observer subscriber2 = new Subscriber("Mehmet");
        Observer subscriber3 = new Subscriber("Ayşe");

        // Aboneleri haber ajansına kaydet
        newsAgency.registerObserver(subscriber1);
        newsAgency.registerObserver(subscriber2);
        newsAgency.registerObserver(subscriber3);

        // Yeni haber yayınla
        newsAgency.publishNews("Bugün hava çok güzel!");

        System.out.println();

        // Bir abonenin aboneliğini iptal et
        newsAgency.removeObserver(subscriber2);

        // Yeni haber yayınla
        newsAgency.publishNews("Yeni bir film vizyona girdi!");
    }
}
```

#### Çıktı

```
Yeni haber yayınlandı: Bugün hava çok güzel!
Ahmet için yeni haber: Bugün hava çok güzel!
Mehmet için yeni haber: Bugün hava çok güzel!
Ayşe için yeni haber: Bugün hava çok güzel!

Yeni haber yayınlandı: Yeni bir film vizyona girdi!
Ahmet için yeni haber: Yeni bir film vizyona girdi!
Ayşe için yeni haber: Yeni bir film vizyona girdi!
```

## Memento

### Amaç?

Memento (Hatıra) tasarım deseni, bir nesnenin iç durumunu kaydetmeyi ve daha sonra bu durumu geri yüklemeyi sağlar. Bu desen, nesnenin iç yapısını gizler ve durum yönetimini kolaylaştırır. Genellikle "geri alma" (undo) veya "durum kaydetme" gibi işlemlerde kullanılır.

### Ne Zaman Kullanılır?

**Senaryo:** Bir metin düzenleyici (text editor) uygulaması düşünün. Bu uygulamada kullanıcılar metin yazabilir ve daha sonra yaptıkları değişiklikleri geri alabilir (undo). Memento Design Pattern, bu tür senaryolarda kullanılır.

Örneğin:
- Metin Kaydetme: Kullanıcı metni düzenler ve bu durum kaydedilir.
- Geri Alma (Undo): Kullanıcı bir önceki duruma geri döner.
- Yineleme (Redo): Kullanıcı geri aldığı işlemi tekrar uygular.

Memento Pattern, nesnenin durumunu kaydeder ve bu durumu daha sonra geri yükler. Bu sayede, nesnenin iç yapısı dışarıya açılmaz ve durum yönetimi kolaylaştırılır.

#### Memento (Hatıra)

```java
// Memento: Metin durumunu saklar
class TextMemento {
    private final String text;  // Metin durumu

    public TextMemento(String text) {
        this.text = text;
    }

    public String getText() {
        return text;  // Kaydedilen metni döndür
    }
}
```

#### Originator (Başlatıcı)

```java
// Originator: Metin düzenleyici
class TextEditor {
    private String text;  // Metin durumu

    public void setText(String text) {
        this.text = text;
    }

    public String getText() {
        return text;
    }

    // Metin durumunu kaydet
    public TextMemento save() {
        return new TextMemento(text);
    }

    // Metin durumunu geri yükle
    public void restore(TextMemento memento) {
        this.text = memento.getText();
    }
}
```

#### Caretaker (Bakıcı)

```java
// Caretaker: Memento nesnelerini yönetir
class History {
    private final Stack<TextMemento> mementos = new Stack<>();  // Memento yığını

    // Memento kaydet
    public void save(TextMemento memento) {
        mementos.push(memento);
    }

    // Son Memento'yu geri yükle
    public TextMemento undo() {
        if (!mementos.isEmpty()) {
            return mementos.pop();  // Son durumu geri yükle
        }
        return null;
    }
}
```

#### Client (İstemci)

```java
public class Main {
    public static void main(String[] args) {
        // Metin düzenleyici ve geçmiş yöneticisi oluştur
        TextEditor editor = new TextEditor();
        History history = new History();

        // Metin yaz ve durumu kaydet
        editor.setText("Merhaba Dünya!");
        history.save(editor.save());
        System.out.println("Güncel Metin: " + editor.getText());

        // Metni değiştir ve durumu kaydet
        editor.setText("Bu bir test mesajıdır.");
        history.save(editor.save());
        System.out.println("Güncel Metin: " + editor.getText());

        // Geri alma (undo) işlemi
        TextMemento previousState = history.undo();
        if (previousState != null) {
            editor.restore(previousState);
            System.out.println("Geri alındı. Güncel Metin: " + editor.getText());
        }
    }
}
```

#### Çıktı

```
Güncel Metin: Merhaba Dünya!
Güncel Metin: Bu bir test mesajıdır.
Geri alındı. Güncel Metin: Merhaba Dünya!
```

## Mediator

### Amaç?

Mediator (Arabulucu) tasarım deseni, birbirleriyle iletişim halinde olan nesneler arasındaki doğrudan bağlantıyı ortadan kaldırarak, bu iletişimi merkezi bir nesne (mediator) üzerinden yönetmeyi sağlar. Bu desen, nesneler arasındaki bağımlılığı azaltır ve sistemin daha esnek ve bakımı kolay hale gelmesini sağlar.

### Ne Zaman Kullanılır?

**Senaryo:** Bir hava trafik kontrol sistemi düşünün. Bu sistemde birden fazla uçak (aircraft) ve bir hava trafik kontrol kulesi (air traffic control tower) bulunur. Uçaklar birbirleriyle doğrudan iletişim kurmak yerine, tüm iletişimi kontrol kulesi üzerinden gerçekleştirir.

Örneğin:
- Uçakların İniş İzni İstemesi: Bir uçak, iniş izni için kontrol kulesine istek gönderir.
- Kontrol Kulesinin Yönlendirmesi: Kontrol kulesi, uçaklar arasında çakışma olmaması için gerekli yönlendirmeleri yapar.
- Uçakların Durum Bilgisi: Uçaklar, konum ve hız bilgilerini kontrol kulesine bildirir.

Mediator Design Pattern, bu tür senaryolarda kullanılır. Uçaklar birbirleriyle doğrudan iletişim kurmaz, tüm iletişim kontrol kulesi üzerinden gerçekleşir. Bu sayede sistem daha düzenli ve yönetilebilir hale gelir.

#### Mediator Arayüzü

```java
// Mediator (Arabulucu) Arayüzü
interface AirTrafficControl {
    void registerAircraft(Aircraft aircraft);  // Uçağı kaydet
    void sendMessage(Aircraft sender, String message);  // Mesaj gönder
}
```

#### ConcreteMediator (Somut Arabulucu)

```java
// Somut Arabulucu: Hava Trafik Kontrol Kulesi
class ControlTower implements AirTrafficControl {
    private List<Aircraft> aircrafts = new ArrayList<>();

    @Override
    public void registerAircraft(Aircraft aircraft) {
        aircrafts.add(aircraft);  // Uçağı kaydet
    }

    @Override
    public void sendMessage(Aircraft sender, String message) {
        for (Aircraft aircraft : aircrafts) {
            // Mesajı gönderen uçağa gönderme
            if (aircraft != sender) {
                aircraft.receive(message);  // Mesajı diğer uçaklara ilet
            }
        }
    }
}
```

#### Colleague (İş Arkadaşı)

```java
// Colleague (İş Arkadaşı) Arayüzü
abstract class Aircraft {
    protected AirTrafficControl mediator;  // Arabulucu
    protected String name;

    public Aircraft(AirTrafficControl mediator, String name) {
        this.mediator = mediator;
        this.name = name;
    }

    public abstract void send(String message);  // Mesaj gönder
    public abstract void receive(String message);  // Mesaj al
}
```

#### ConcreteColleague (Somut İş Arkadaşı)

```java
// Somut İş Arkadaşı: Uçak
class CommercialAircraft extends Aircraft {
    public CommercialAircraft(AirTrafficControl mediator, String name) {
        super(mediator, name);
    }

    @Override
    public void send(String message) {
        System.out.println(name + " gönderiyor: " + message);
        mediator.sendMessage(this, message);  // Mesajı arabulucu üzerinden gönder
    }

    @Override
    public void receive(String message) {
        System.out.println(name + " aldı: " + message);
    }
}
```

#### Client (İstemci)

```java
public class Main {
    public static void main(String[] args) {
        // Arabulucu oluştur (Kontrol Kulesi)
        AirTrafficControl controlTower = new ControlTower();

        // Uçaklar oluştur
        Aircraft aircraft1 = new CommercialAircraft(controlTower, "Uçak 1");
        Aircraft aircraft2 = new CommercialAircraft(controlTower, "Uçak 2");
        Aircraft aircraft3 = new CommercialAircraft(controlTower, "Uçak 3");

        // Uçakları kontrol kulesine kaydet
        controlTower.registerAircraft(aircraft1);
        controlTower.registerAircraft(aircraft2);
        controlTower.registerAircraft(aircraft3);

        // Uçaklar arasında mesajlaşma
        aircraft1.send("İniş izni istiyorum.");
        aircraft2.send("Pistte bir sorun var mı?");
        aircraft3.send("Pist temiz, iniş yapabilirsiniz.");
    }
}
```

#### Çıktı

```
Uçak 1 gönderiyor: İniş izni istiyorum.
Uçak 2 aldı: İniş izni istiyorum.
Uçak 3 aldı: İniş izni istiyorum.

Uçak 2 gönderiyor: Pistte bir sorun var mı?
Uçak 1 aldı: Pistte bir sorun var mı?
Uçak 3 aldı: Pistte bir sorun var mı?

Uçak 3 gönderiyor: Pist temiz, iniş yapabilirsiniz.
Uçak 1 aldı: Pist temiz, iniş yapabilirsiniz.
Uçak 2 aldı: Pist temiz, iniş yapabilirsiniz.
```

## Chain of Responsibility

### Amaç?

Chain of Responsibility (Sorumluluk Zinciri) tasarım deseni, bir isteği işlemek için birden fazla nesnenin bir zincir halinde bağlandığı bir yapı oluşturur. İstek, bu zincir boyunca ilerler ve her nesne, isteği işleyip işlemeyeceğine kendisi karar verir. Bu desen, isteği gönderen ile isteği işleyen nesneler arasındaki bağımlılığı azaltır ve esnek bir yapı sunar.

### Ne Zaman Kullanılır?

**Senaryo:** Bir destek hattı (support ticket) sistemi düşünün. Bu sistemde, müşterilerden gelen destek talepleri farklı seviyelerdeki destek ekipleri tarafından işlenir.

Örneğin:
- Seviye 1 Destek: Basit sorunları çözer (örneğin, şifre sıfırlama).
- Seviye 2 Destek: Daha karmaşık sorunları çözer (örneğin, yazılım hataları).
- Seviye 3 Destek: Kritik sorunları çözer (örneğin, donanım arızaları).

Chain of Responsibility Pattern, bu tür senaryolarda kullanılır. Her destek seviyesi, bir sonraki seviyeye bağlanır ve talep, zincir boyunca ilerler. Eğer bir seviye talebi işleyemezse, talebi bir sonraki seviyeye iletir.

#### Handler Arayüzü

```java
// Handler (İşleyici) Arayüzü
interface SupportHandler {
    void setNextHandler(SupportHandler nextHandler);  // Sonraki işleyiciyi ayarla
    void handleRequest(String request);  // İsteği işle
}
```

#### ConcreteHandler (Somut İşleyici)

```java
// Somut İşleyici: Seviye 1 Destek
class Level1Support implements SupportHandler {
    private SupportHandler nextHandler;

    @Override
    public void setNextHandler(SupportHandler nextHandler) {
        this.nextHandler = nextHandler;
    }

    @Override
    public void handleRequest(String request) {
        if (request.contains("şifre")) {
            System.out.println("Seviye 1 Destek: Şifre sıfırlama işlemi yapıldı.");
        } else if (nextHandler != null) {
            nextHandler.handleRequest(request);  // İsteği bir sonraki işleyiciye ilet
        }
    }
}

// Somut İşleyici: Seviye 2 Destek
class Level2Support implements SupportHandler {
    private SupportHandler nextHandler;

    @Override
    public void setNextHandler(SupportHandler nextHandler) {
        this.nextHandler = nextHandler;
    }

    @Override
    public void handleRequest(String request) {
        if (request.contains("yazılım")) {
            System.out.println("Seviye 2 Destek: Yazılım hatası çözüldü.");
        } else if (nextHandler != null) {
            nextHandler.handleRequest(request);  // İsteği bir sonraki işleyiciye ilet
        }
    }
}

// Somut İşleyici: Seviye 3 Destek
class Level3Support implements SupportHandler {
    private SupportHandler nextHandler;

    @Override
    public void setNextHandler(SupportHandler nextHandler) {
        this.nextHandler = nextHandler;
    }

    @Override
    public void handleRequest(String request) {
        if (request.contains("donanım")) {
            System.out.println("Seviye 3 Destek: Donanım arızası giderildi.");
        } else if (nextHandler != null) {
            nextHandler.handleRequest(request);  // İsteği bir sonraki işleyiciye ilet
        } else {
            System.out.println("Destek ekibi bu talebi işleyemiyor.");
        }
    }
}
```

#### Client (İstemci)

```java
public class Main {
    public static void main(String[] args) {
        // Destek işleyicileri oluştur
        SupportHandler level1 = new Level1Support();
        SupportHandler level2 = new Level2Support();
        SupportHandler level3 = new Level3Support();

        // Zinciri oluştur
        level1.setNextHandler(level2);
        level2.setNextHandler(level3);

        // Destek taleplerini işle
        level1.handleRequest("Şifremi unuttum, sıfırlayabilir misiniz?");
        level1.handleRequest("Yazılımda bir hata var, çözebilir misiniz?");
        level1.handleRequest("Donanım arızası var, yardım edebilir misiniz?");
        level1.handleRequest("İnternet bağlantım yok, ne yapmalıyım?");
    }
}
```

#### Çıktı

```
Seviye 1 Destek: Şifre sıfırlama işlemi yapıldı.
Seviye 2 Destek: Yazılım hatası çözüldü.
Seviye 3 Destek: Donanım arızası giderildi.
Destek ekibi bu talebi işleyemiyor.
```

## Visitor

### Amaç?

Visitor (Ziyaretçi) tasarım deseni, bir nesne yapısındaki elemanlara yeni işlemler eklemeyi kolaylaştırır. Bu desen, nesnelerin yapısını değiştirmeden, bu nesneler üzerinde farklı işlemler gerçekleştirmeyi sağlar. Bu sayede, yeni işlemler eklemek için mevcut kodu değiştirmek gerekmez.

### Ne Zaman Kullanılır?

**Senaryo:** Bir belge işleme sistemi düşünün. Bu sistemde farklı türde belge elemanları (örneğin, metin, resim, tablo) bulunur. Bu elemanlar üzerinde farklı işlemler (örneğin, dışa aktarma, yazdırma, analiz etme) gerçekleştirilir. Visitor Design Pattern, bu tür senaryolarda kullanılır.

Örneğin:
- Dışa Aktarma İşlemi: Belge elemanlarını PDF veya PNG formatında dışa aktarmak.
- Yazdırma İşlemi: Belge elemanlarını yazdırmak.
- Analiz İşlemi: Belge elemanlarını analiz ederek istatistikler çıkarmak.

Visitor Pattern, bu işlemleri belge elemanlarının yapısını değiştirmeden eklemeyi sağlar. Her işlem, bir "ziyaretçi" tarafından gerçekleştirilir.

#### Visitor Arayüzü

```java
// Visitor (Ziyaretçi) Arayüzü
interface DocumentVisitor {
    void visit(TextElement text);  // Metin elemanını ziyaret et
    void visit(ImageElement image);  // Resim elemanını ziyaret et
}
```

#### ConcreteVisitor (Somut Ziyaretçi)

```java
// Somut Ziyaretçi: Dışa Aktarma İşlemi
class ExportVisitor implements DocumentVisitor {
    @Override
    public void visit(TextElement text) {
        System.out.println("Metin elemanı PDF olarak dışa aktarıldı: " + text.getContent());
    }

    @Override
    public void visit(ImageElement image) {
        System.out.println("Resim elemanı PNG olarak dışa aktarıldı: " + image.getFileName());
    }
}

// Somut Ziyaretçi: Yazdırma İşlemi
class PrintVisitor implements DocumentVisitor {
    @Override
    public void visit(TextElement text) {
        System.out.println("Metin elemanı yazdırıldı: " + text.getContent());
    }

    @Override
    public void visit(ImageElement image) {
        System.out.println("Resim elemanı yazdırıldı: " + image.getFileName());
    }
}
```

#### Element (Eleman)

```java
// Element (Eleman) Arayüzü
interface DocumentElement {
    void accept(DocumentVisitor visitor);  // Ziyaretçiyi kabul et
}
```

#### ConcreteElement (Somut Eleman)

```java
// Somut Eleman: Metin
class TextElement implements DocumentElement {
    private String content;

    public TextElement(String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }

    @Override
    public void accept(DocumentVisitor visitor) {
        visitor.visit(this);  // Ziyaretçiyi kabul et
    }
}

// Somut Eleman: Resim
class ImageElement implements DocumentElement {
    private String fileName;

    public ImageElement(String fileName) {
        this.fileName = fileName;
    }

    public String getFileName() {
        return fileName;
    }

    @Override
    public void accept(DocumentVisitor visitor) {
        visitor.visit(this);  // Ziyaretçiyi kabul et
    }
}
```

#### ObjectStructure (Nesne Yapısı)

```java
// Nesne Yapısı: Belge
class Document {
    private List<DocumentElement> elements = new ArrayList<>();

    public void addElement(DocumentElement element) {
        elements.add(element);  // Eleman ekle
    }

    public void accept(DocumentVisitor visitor) {
        for (DocumentElement element : elements) {
            element.accept(visitor);  // Tüm elemanları ziyaretçiye göster
        }
    }
}
```

#### Client (İstemci)

```java
public class Main {
    public static void main(String[] args) {
        // Belge oluştur
        Document document = new Document();
        document.addElement(new TextElement("Merhaba Dünya!"));
        document.addElement(new ImageElement("foto.png"));

        // Ziyaretçiler oluştur
        DocumentVisitor exportVisitor = new ExportVisitor();
        DocumentVisitor printVisitor = new PrintVisitor();

        // Belgeyi dışa aktar
        System.out.println("Belge dışa aktarılıyor:");
        document.accept(exportVisitor);

        System.out.println();

        // Belgeyi yazdır
        System.out.println("Belge yazdırılıyor:");
        document.accept(printVisitor);
    }
}
```

#### Çıktı

```
Belge dışa aktarılıyor:
Metin elemanı PDF olarak dışa aktarıldı: Merhaba Dünya!
Resim elemanı PNG olarak dışa aktarıldı: foto.png

Belge yazdırılıyor:
Metin elemanı yazdırıldı: Merhaba Dünya!
Resim elemanı yazdırıldı: foto.png
```

## State

### Amaç?

State (Durum) tasarım deseni, bir nesnenin iç durumu değiştiğinde davranışını değiştirmesini sağlar. Bu desen, nesnenin durumunu temsil eden sınıflar oluşturur ve bu sınıflar arasında geçiş yaparak davranışı dinamik olarak değiştirir. Bu sayede, duruma bağlı olan karmaşık koşul ifadeleri ortadan kalkar ve kod daha temiz hale gelir.

### Ne Zaman Kullanılır?

**Senaryo:** Bir müzik çalar (music player) uygulaması düşünün. Bu uygulamada, çalma durumuna göre farklı davranışlar sergilenir.

Örneğin:
- Çalma Durumu (Playing State): Müzik çalıyorken, "play" butonu müziği durdurur.
- Durdurma Durumu (Stopped State): Müzik durdurulmuşken, "play" butonu müziği başlatır.

State Design Pattern, bu tür senaryolarda kullanılır. Her durum, ayrı bir sınıf tarafından temsil edilir ve durum değişikliği sırasında davranış otomatik olarak güncellenir.

#### State Arayüzü

```java
// State (Durum) Arayüzü
interface MusicPlayerState {
    void play();  // Çalma veya durdurma işlemi
}
```

#### ConcreteState (Somut Durum)

```java
// Somut Durum: Çalma Durumu
class PlayingState implements MusicPlayerState {
    private MusicPlayer player;

    public PlayingState(MusicPlayer player) {
        this.player = player;
    }

    @Override
    public void play() {
        System.out.println("Müzik durduruldu.");
        player.setState(player.getStoppedState());  // Durdurma durumuna geç
    }
}

// Somut Durum: Durdurma Durumu
class StoppedState implements MusicPlayerState {
    private MusicPlayer player;

    public StoppedState(MusicPlayer player) {
        this.player = player;
    }

    @Override
    public void play() {
        System.out.println("Müzik başlatıldı.");
        player.setState(player.getPlayingState());  // Çalma durumuna geç
    }
}
```

#### Context (Bağlam)

```java
// Context (Bağlam): Müzik Çalar
class MusicPlayer {
    private MusicPlayerState playingState;
    private MusicPlayerState stoppedState;
    private MusicPlayerState currentState;

    public MusicPlayer() {
        this.playingState = new PlayingState(this);
        this.stoppedState = new StoppedState(this);
        this.currentState = stoppedState;  // Başlangıç durumu: Durdurulmuş
    }

    public void setState(MusicPlayerState state) {
        this.currentState = state;  // Durumu değiştir
    }

    public MusicPlayerState getPlayingState() {
        return playingState;
    }

    public MusicPlayerState getStoppedState() {
        return stoppedState;
    }

    public void play() {
        currentState.play();  // Mevcut duruma göre çalma/durdurma işlemi
    }
}
```

#### Client (İstemci)

```java
public class Main {
    public static void main(String[] args) {
        // Müzik çalar oluştur
        MusicPlayer player = new MusicPlayer();

        // Müzik çalma işlemleri
        player.play();  // Müzik başlatıldı.
        player.play();  // Müzik durduruldu.
        player.play();  // Müzik başlatıldı.
        player.play();  // Müzik durduruldu.
    }
}
```

#### Çıktı

```
Müzik başlatıldı.
Müzik durduruldu.
Müzik başlatıldı.
Müzik durduruldu.
```