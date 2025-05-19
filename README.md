**Projemiz:**
Bu proje, Python programlama dili kullanılarak geliştirilen çift oyunculu bir Pong oyunudur. Oyun, TCP/IP protokolü üzerinden haberleşen bir istemci-sunucu mimarisi ile çalışmaktadır. Amaç, iki oyuncunun ağ üzerinden bağlanarak gerçek zamanlı olarak Pong oyununu oynamalarını sağlamaktır. Sunucu tarafında, gelen istemci bağlantıları yönetilir, her oyuncuya bir oyun ve oyuncu kimliği atanır. Oyun içindeki topun hareketi, raketlerin konumu ve skorlar gibi bilgiler sunucu tarafından merkezi olarak güncellenir ve her istemciye senkronize bir şekilde iletilir. Bu veri aktarımı, PongDTO isimli özel bir veri sınıfı (Data Transfer Object) aracılığıyla gerçekleştirilir.İstemci tarafında, oyuncular Pygame kütüphanesi ile hazırlanmış görsel arayüz üzerinden oyunu oynarlar. Oyuncular klavye girdileriyle raketlerini kontrol eder ve sunucudan gelen güncel oyun durumu doğrultusunda topun hareketi ve skorlar gibi öğeleri gerçek zamanlı olarak takip ederler.
Projenin temel özellikleri şunlardır:
•	Gerçek zamanlı iki oyunculu çevrim içi Pong oyunu
•	TCP soket bağlantısı ile oyuncular arası iletişim
•	Pygame arayüzü ile kullanıcı dostu görseller
•	Raket ve top çarpışma hesapları
•	Neon tema ve etkili arka plan müzikleri

**Kullanılan Teknolojiler:**
•	Python
•	Socket Kütüphanesi
•	Thread Kütüphanesi
•	Pygame Kütüphanesi
•	Pickle

**Giriş**

**1.Arayüz**
Kullanıcı arayüzü için Kivy kütüphanesi kullanılmıştır. Kullanıcı, oyunu farklı modlarda başlatabilmekte, sunucu kurabilmekte ve önceki maç sonuçlarını görebilmektedir.

**2.Main.py**
Amaçları:
•	Uygulamanın farklı ekranlarını yönetmek
•	Oyun modlarını başlatmak
•	Sunucu/istemci yapısını başlatmak (subprocess ile)
•	Arka plan müziği kontrol etmek
•	Skorları JSON dosyasından okumak
MainMenu Sınıfı:
•	Ana menüyü temsil eder.
•	Arka plan müziği açılır (oyununarayüzsesi.mp3)
•	Dört ana işlev sunar:
•	Start Server → server.py çalıştırılır
•	Join Online Game → Oyun modu seçme ekranına geçilir
•	Play Offline → Hem offline oyun hem de online istemci başlatılır
•	History → Skorlar JSON dosyasından okunarak görüntülenir
ChooseModeScreen Sınıfı:
•	Oyun modlarını seçmek için kullanılır.
•	Üç farklı mod sunar:
1.	Normal Mode → client.py
2.	Galatasaray Mode → clientgs.py
3.	Fenerbahçe Mode → clientfb.py

•	Her mod seçildiğinde ilgili müzik çalınır.
HistoryScreen Sınıfı:
•	Skorları JSON dosyasından okuyarak kullanıcıya gösterir.
•	keep_score.json dosyasından "player1" ve "player2" galibiyet sayıları alınır.
PongApp Sınıfı:
•	Ekran yöneticisi (ScreenManager) ile uygulama başlatılır.
•	menu, history, modescreen ekranları tanımlanır ve yüklenir.
İnterface.kv:
Kivy dilinde yazılmıştır.
Arka plan resmi (projearkaplanresmi.jpg) kullanılmıştır.
Üç ana ekran tanımlanır:
•	<MainMenu>
•	<HistoryScreen>
•	<ChooseModeScreen>
Butonlar, konum ve işlev olarak tasarlanmıştır.
<MainMenu>:
•	Ana menü ekranında FloatLayout kullanılır.
•	Kullanıcı Join Online Game Butonu ile Online olarak bir oyuna bağlanır.
•	Kullanıcı internet bağlantısı olmadan yerel oyun başlatmak isterse bu butona tıklar.
•	 Start Server Butonu sunucuyu başlatmak için kullanılır server.py dosyasını arka planda çalıştırır.
•	History butonu kullanıcının geçmiş maçlardaki galibiyet sayılarını görüntülemesini sağlar.
<HistoryScreen>:
•	player1 ve player2 etiketleri, Python tarafından dinamik olarak güncellenir.
•	Go Back butonu ile ana menüye dönülür.
<ChooseModeScreen>:
•	Kullanıcı, klasik Pong oynanışı tercih ettiğinde Normal Mode butonuna tıklar.
•	Kullanıcı Galatasaray Temalı oynamak istediğinde  Galatasaray Mode butonuna tıklar.
•	Kullanıcı Fenerbahçe Temalı oynamak istediğinde Fenerbahçe Mode butonuna tıklar

**3. Server.py**
Amaçları:
•	TCP soketleri kullanarak iki oyuncunun birbirine bağlanmasını sağlamak
•	Pong oyununun durumunu eş zamanlı olarak her iki oyuncu arasında senkronize etmek
•	Oyuncuların giriş bilgilerini yönetmek
•	Oyun nesnelerinin (top, raketler) durumunu güncellemek
•	İstemciler arasında veri alışverişi sağlamak
pongDTO Sınıfı:
•	Ağ üzerinden (örneğin pickle ile) istemcilerle haberleşirken, oyunla ilgili bütün veriler tek bir nesne olarak gönderilir veya alınır.
•	Pong oyununda iki oyuncunun pozisyonlarını, topun konumunu, hareket bilgilerini ve skoru barındıran veri taşıyıcısıdır.
Game Sınıfı:  
•	Her biri iki oyuncudan oluşan tek bir oyunun durumunu yönetir.
•	Pong oyununun sunucudaki temsilidir.
•	Oyuncuları tanımlar, oyunun başlangıç değerlerini ayarlar ve PongDTO aracılığıyla oyunun durumunu takip eder.
•	Sunucu, birden fazla Game nesnesi oluşturarak aynı anda birçok oyunu kontrol edebilir.
get_game_dto(game_id) Fonksiyonu:  
•	Her oyun için oyuncu konumları, top pozisyonu, hız, yön, skor gibi bilgileri içeren PongDTO nesnesine erişim sağlar
•	İstemciler arası veri güncellenebilir ve paylaşılabilir.
get_game(game_id) Fonksiyonu:
•	game_ids listesindeki tüm Game nesnelerini dolaşır.
•	game.game_id değeri, parametre olarak verilen game_id ile eşleşirse o Game nesnesini döndürür.
update_game_dto(dto) Fonksiyonu: 
Oyuncunun y (dikey) konumunu güncellemek için kullanılır.
update_game_state(dto) Fonksiyonu:
•	Oyun sırasında her oyuncunun pozisyonu, puanı, topun konumu gibi dinamik verileri güncelleyerek oyunun devamlılığını sağlar.
•	Oyunculardan alınan verileri (DTO) işler.
•	Çarpışma kontrolü (top-raket veya top-duvar) yapar.
•	Oyun bitiş durumu veya skor kontrolü yapabilir.
•	Oyun içi objelerin (raket, top, skor) konumlarını günceller.
•	Güncellenmiş durumu istemcilere geri gönderir.
get_game_player_id() Fonksiyonu:
•	Oyunu başlatmak için yeni bir bağlantı kabul edildiğinde çağrılır ve oyun kimliği ve oyuncu kimliğine ihtiyaç duyar.
•	Yeni oyuncu için kullanılabilir oyun kimliğini ve oyuncu kimliğini sağlar.
•	Bir oyuncunun başka bir oyuncuyu beklediği bir oyun varsa, o oyun kimliği ve oyuncu kimliği verilir.
•	Bir kullanılabilir yuvası olan mevcut bir oyun yoksa, yeni bir oyun oluşturur ve kimlikleri döndürür.
threaded_client(conn, game_id, player_id) Fonksiyonu:
•	Her istemci bağlantısı için ayrı bir thread içerisinde çalışarak iletişimi yönetir.
•	Oyun kuyruğundan oyun için veri aktarım nesnesini alır.
•	Oyunda her iki oyuncu da varsa ve başlatma işareti etkinse, oyun durumunu günceller.
•	Herhangi bir nedenle istemciyle bağlantı kesilirse oyun durumunu sıfırlar.
•	Her iki oyuncu da ayrıldıysa, oyunu oyun kuyruğundan kaldır.

**4.Client.py**
Amaçları:
•	Sunucuya bağlanarak oyuna katılmak
•	Oyuncudan alınan giriş verilerini (hareket gibi) sunucuya göndermek
•	Sunucudan gelen PongDTO nesnesiyle oyunun güncel durumunu almak
•	Ekranda oyun grafiklerini (raket, top, skor) çizmek ve güncel tutmak
•	Kullanıcı ile etkileşim sağlayarak gerçek zamanlı bir oyun deneyimi sunmak
•	Sunucudan gelen verilere göre topun ve raketlerin konumunu güncellemek
•	Oyuncunun yukarı/aşağı hareketlerini klavye aracılığıyla kontrol etmek
pongDTO Sınıfı:
•	Ağ üzerinden (örneğin pickle ile) istemcilerle haberleşirken, oyunla ilgili bütün veriler tek bir nesne olarak gönderilir veya alınır.
•	Pong oyununda iki oyuncunun pozisyonlarını, topun konumunu, hareket bilgilerini ve skoru barındıran veri taşıyıcısıdır.
Bat sınıfı:
•	Her bir oyuncunun raketini temsil eder.
•	Oyuncunun yatay konumu sabit, dikey konumu ise move fonksiyonu ile klavye girişlerine göre değişir.
•	draw fonksiyonu ile raketin ekran üzerine çizilmesini sağlar
•	add_point fonksiyonu ile oyuncunun puanı bir artırılır.
•	Her oyuncunun skoru ayrı ayrı tutulur ve oyunun gidişatına göre güncellenir
Ball Sınıfı:
•	Oyunda kullanılan topu temsil eder.
•	Başlangıçta rastgele bir yatay ve dikey yön belirlenir (direction_x, direction_y).
•	Topun konumu (x, y) ve hızı (velocity_x, velocity_y) tanımlanır.
•	draw fonksiyonu ile top, ekranda daire şeklinde çizilir.
•	Topun hareketi ve çarpışma kontrolü sunucu tarafında yapılırken, istemci tarafı sadece gelen verilere göre topun pozisyonunu güncelleyip ekrana yansıtır.
update_bat_ball Fonksiyonu:
•	Sunucudan alınan PongDTO nesnesi ile raketlerin (bat) ve topun (ball) konumunu günceller.
•	Oyuncu kimliğine göre kendi raketinin ve rakibin raketinin renklerini ayarlar.
•	Her iki oyuncunun x ve y koordinatları güncellenir.
•	Topun konumu ve rengi, gelen DTO verilerine göre ayarlanır.
•	Bu sayede oyun ekranında doğru pozisyonlar gösterilerek istemcide gerçek zamanlı görsellik sağlanır.
Ana Oyun Döngüsü ve İstemci Bağlantısı:
 İstemci (client), TCP protokolü kullanarak belirtilen IP ve port ile sunucuya bağlanır.
•	Sunucudan gelen ilk PongDTO verisi alınır ve oyuncunun kimliği belirlenir.
•	Oyuncunun ve rakibin kimlikleri belirlendikten sonra iki raket (Bat) ve bir top (Ball) nesnesi oluşturulur.
•	update_bat_ball fonksiyonu çağrılarak oyun nesnelerinin ilk konumları ayarlanır.
•	Pygame ile pencere, yazı tipi ve arka plan görseli hazırlanır.
•	Sonsuz bir while döngüsü ile oyun sürekli çalıştırılır; bu döngü FPS’ye göre kontrol edilir.
•	Her döngüde ekran güncellenir, ortadaki çizgiler çizilir, raketler ve top yeniden çizilir, skorlar yazdırılır.
•	Klavye girişleri ile yukarı (W) ve aşağı (S) hareket algılanır, ESC ile oyun sonlandırılır.
•	Oyun sırasında DTO nesnesi sunucuya gönderilir ve güncel DTO tekrar alınır.
•	Alınan verilerle update_bat_ball tekrar çağrılarak top ve raket konumları güncellenir.
•	Oyun kapatılırken bağlantı hataları kontrol edilerek kullanıcıya hata mesajı gösterilir.
