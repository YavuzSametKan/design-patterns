# Tasarım Desenleri

# Yapısal Tasarım Desenleri

- [Flyweight](#Flyweight)
- [Adapter](#Adapter)
- [Composite](#Composite)
- [Facade](#Facade)
- [Proxy](#Proxy)
- [Decorator](#Decorator)
- [Bridge](#Bridge)

# Yaratımsal Tasarım Desenleri

- [Singleton](#Singleton)
- [Factory](#Factory)

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