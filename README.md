# Kiss Fighter — Ali × Aslı

Phaser 3.90.0 ile hazırlanmış iki kişilik arcade öpücük oyunu. Ali ve Aslı'nın kullanıcı tarafından sağlanan fotoğrafından üretilen şeffaf kafa görselleri kullanılır. Street Fighter varlıkları veya markası kullanılmaz.

## Çalıştırma

`npm run dev` → http://localhost:5173. Paket kurulumu gerekmez. `npm test` dövüş kurallarını doğrular. Üretimde `dist/` klasörünü HTTPS sağlayan herhangi bir statik sunucuya yükleyin. Kitaplıklar proje içinde tutulur; arayüz yazı tipi için Google Fonts isteği isteğe bağlıdır ve sistem yazı tipine geri düşer.

## İki oyuncu

1. İlk oyuncu Ali veya Aslı'yı seçer, **Arkadaşını davet et → Yeni oda oluştur** der.
2. İkinci oyuncu aynı siteyi açıp 6 karakterli kodla katılır veya davet bağlantısını kullanır. İlk oyuncunun seçmediği karakter ikinci oyuncuya atanır. İki taraf aynı karakter olamaz.
3. Oda sahibi sayfayı açık tutar. Eş bağlanınca 90 saniyelik maç başlar. Oklar / A-D ile yürü, yukarı / W ile zıpla, Space ile öpücük gönder. Mobil düğmeler basılı tutulabilir. Her isabet 10 can götürür. Saldırı aralığı 550 ms. Canı biten kaybeder; süre dolarsa canı yüksek olan kazanır.
4. Maçtan sonra oda sahibi rövanşı başlatabilir; katılan oyuncu rövanş isteği gönderebilir.

## Ücretsiz bağlantının sınırları

PeerJS 1.5.5 varsayılan ücretsiz PeerServer Cloud sinyalleşmesini kullanır. API anahtarı, kullanıcı tokenı, üyelik veya bizim oyun sunucumuz gerekmez. Oyun verisi WebRTC DataChannel üzerinden eşler arasında gider. Kamera ve mikrofon izni istenmez. PeerJS'nin protokol içi rastgele oturum belirteci SDK tarafından üretilir; satın alınacak veya kullanıcı tarafından sağlanacak bir erişim tokenı yoktur.

Ücretsiz Cloud ve STUN hizmetlerinin kullanılabilirliği dış servislere bağlıdır. Bu sürüm ücretli/kimlikli TURN sağlamaz; simetrik NAT, kurumsal güvenlik duvarları veya bazı mobil ağlarda bağlantı başarısız olabilir. Farklı ağla deneyin veya kendi PeerServer/TURN altyapınızı kurun. Oda kodunu bilen ilk kişi katılabilir; oda kodu güçlü kimlik doğrulama değildir.

## Mimari ve protokol

Oda sahibi tek yetkili simülasyonu çalıştırır. Rakip yalnızca `{left,right,jump,kiss}` boole değerlerini 20 Hz gönderir; hasar ve kazananı oda sahibi hesaplar. Konum ve can 20 Hz yayınlanır. Gelen paketler tür ve sınır kontrolünden geçer. Bir odada tek rakip kabul edilir. 500 ms taze girdi alınmazsa rakip hareketi durur; 12 saniye paket gelmezse bağlantı kapanır. Bağlanma için 25 saniye zaman aşımı ve anlaşılır hata mesajları vardır.

`welcome {protocol:1,slot:0|1}`: karakter ataması. `start`: maçı sıfırla. `input {keys}`: rakibin kontrolü. `state {state}`: yetkili konum, can, mermiler, kalan süre ve sonuç. `rematch`: misafirin rövanş isteği. `game.js` içindeki Combat sınıfı arayüzden bağımsızdır ve Node testlerinde de çalışır.

## Doğrulama

`node --check dist/game.js`, `node --check dist/app.js`, `npm test`. Mantık testleri isabet, bekleme süresi, zıplayarak kaçma, can bitişi, süre bitişi ve sıfırlamayı kapsar. Canlı WebRTC sonucu ayrıca tarayıcıyla kontrol edilmelidir; yerel mantık testleri internet üzerinden bağlantıyı kanıtlamaz.

## Kaynaklar

- Phaser: https://phaser.io (MIT)
- PeerJS: https://peerjs.com (MIT); ücretsiz sinyalleşme: https://peerjs.com/client/faq
- Yüz görseli: kullanıcı referans fotoğrafı üzerinden ImageGen ile şeffaflaştırılmıştır.
