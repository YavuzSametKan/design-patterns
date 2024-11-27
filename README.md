# Yapısal Tasarım Desenleri

## Flyweight

### Amaç?

Çok sayıda nesneyi oluşturmanın bellek maliyetini azaltmak ve bellek kullanımını optimize etmektir. Bu kalıp, aynı veya benzer durumlara sahip nesneleri paylaşarak bellekteki gereksiz yükleri en aza indirir.

### Ne Zaman Kullanılır?

Bir grafik uygulamasında, ekrana yüzlerce ağaç çizmek gerektiğini düşünelim. Her ağaç için ayrı nesne oluşturmak bellek tüketimini artırır. Ancak Flyweight kalıbıyla ağaç türü, rengi gibi ortak özellikler bir kez oluşturulup paylaşılabilir. Yalnızca konum gibi farklılık gösteren özellikler dışarıdan sağlanır.

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
