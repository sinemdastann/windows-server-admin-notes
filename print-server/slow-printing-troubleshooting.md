# Print Server - Slow Printing Troubleshooting

Bu doküman, Windows Print Server ortamında çıktı alma işlemlerinin yavaşlaması durumunda yapılabilecek temel kontrolleri ve sorun giderme adımlarını açıklamak amacıyla hazırlanmıştır.

## Amaç

Kullanıcıların yazıcıya gönderdiği işlerin geç başlaması, kuyrukta beklemesi veya yazıcıya yavaş iletilmesi gibi durumlarda sistematik bir kontrol listesi oluşturmak.

## Olası Belirtiler

- Kullanıcı çıktı gönderdiğinde yazdırma geç başlar.
- Print queue üzerinde işler uzun süre bekler.
- Bazı kullanıcılar hızlı çıktı alırken bazı kullanıcılar yavaşlık yaşar.
- Yazıcı çevrim içi görünmesine rağmen işlem gecikir.
- Sunucu üzerinde Print Spooler servisi yoğun çalışır.

## Temel Kontrol Adımları

### 1. Print Spooler Servisini Kontrol Etme

```powershell
Get-Service Spooler
```

Gerekirse servis yeniden başlatılabilir:

```powershell
Restart-Service Spooler
```

> Not: Servis yeniden başlatılırsa kuyruktaki işler etkilenebilir. İşlem öncesi aktif çıktı olup olmadığı kontrol edilmelidir.

### 2. Yazdırma Kuyruğunu Kontrol Etme

```powershell
Get-Printer
Get-PrintJob -PrinterName "Printer-Name"
```

Uzun süredir bekleyen işler varsa kullanıcı ve belge bazında kontrol edilmelidir.

### 3. Yazıcı Portunu Kontrol Etme

```powershell
Get-PrinterPort
```

Kontrol edilecek başlıklar:

- IP adresi doğru mu?
- Standard TCP/IP Port kullanılıyor mu?
- WSD port yerine TCP/IP port tercih edilmiş mi?
- Aynı yazıcı için birden fazla port karışıklığı var mı?

### 4. Printer Pooling Ayarını Kontrol Etme

Printer Pooling yanlış yapılandırılırsa işler farklı portlara yönlenebilir veya yavaşlık oluşabilir.

Kontrol yolu:

```text
Printer Properties
└── Ports
    └── Enable printer pooling
```

Genel kullanımda tek yazıcı tek port kullanıyorsa Printer Pooling kapalı olmalıdır.

### 5. Sürücü Kontrolü

Yavaş yazdırma sorunlarında sürücü uyumsuzluğu sık görülür.

Kontrol edilecekler:

- Üreticinin güncel sürücüsü kullanılıyor mu?
- Universal driver ile model-specific driver karşılaştırıldı mı?
- 32-bit / 64-bit uyumluluğu doğru mu?
- Aynı yazıcının eski sürücüleri sistemde kalmış mı?

### 6. Sunucu Kaynak Kullanımı

Print Server üzerinde CPU, RAM ve disk kullanımı kontrol edilmelidir.

```powershell
Get-Process spoolsv
```

Event Viewer üzerinden PrintService logları incelenebilir:

```text
Event Viewer
└── Applications and Services Logs
    └── Microsoft
        └── Windows
            └── PrintService
```

## Sorun Giderme Tablosu

| Sorun | Olası Neden | Kontrol / Çözüm |
|---|---|---|
| Çıktı geç başlıyor | Print queue yoğun olabilir | Kuyruk kontrol edilir, bekleyen işler temizlenir. |
| Bazı kullanıcılarda yavaş | Kullanıcı profili veya eski bağlantı olabilir | Yazıcı kaldırılıp yeniden eklenir. |
| Herkeste yavaş | Print Server veya sürücü kaynaklı olabilir | Spooler, driver ve sunucu kaynakları kontrol edilir. |
| Yazıcı çevrim içi ama çıktı yok | Port veya IP hatalı olabilir | TCP/IP port ve yazıcı IP adresi kontrol edilir. |
| Kuyrukta belge takılıyor | Bozuk print job olabilir | İlgili iş iptal edilir, gerekirse spooler yeniden başlatılır. |

## Önerilen Yaklaşım

1. Aktif çıktı işlemi olup olmadığı kontrol edilir.
2. Print queue incelenir.
3. Yazıcı portu ve IP adresi doğrulanır.
4. Printer Pooling ayarı kontrol edilir.
5. Sürücü güncelliği kontrol edilir.
6. Event Viewer logları incelenir.
7. Sorun devam ederse test yazıcısı veya alternatif sürücü ile deneme yapılır.

## Güvenlik Notu

Bu dokümanda gerçek kurum adı, yazıcı adı, IP adresi veya kullanıcı bilgisi kullanılmamıştır. Örnekler genel sistem yönetimi yaklaşımıyla hazırlanmıştır.
