# BGInfo Deployment Notes

Bu doküman, Windows istemci ve sunucu sistemlerinde BGInfo aracının Group Policy veya Scheduled Task yöntemiyle dağıtılması için temel teknik notları içermektedir.

## Amaç

BGInfo ile bilgisayar adı, kullanıcı adı, IP adresi, işletim sistemi bilgisi ve domain bilgisi gibi temel sistem bilgileri masaüstü arka planında gösterilebilir. Bu yöntem, destek ekiplerinin cihazları hızlı tanımasına yardımcı olur.

## Kullanım Senaryosu

- Laboratuvar bilgisayarlarında cihaz bilgilerini göstermek
- Sunucu ekranlarında hostname ve IP bilgisini görünür yapmak
- Helpdesk ve sistem destek süreçlerinde cihaz tespitini kolaylaştırmak
- Standart kurumsal arka plan üzerine teknik bilgileri yazdırmak

## Örnek Klasör Yapısı

```text
NETLOGON/
└── BGInfo/
    ├── Bginfo64.exe
    ├── corporate-bginfo.bgi
    └── background.jpg
```

## Group Policy ile Dağıtım Mantığı

BGInfo dağıtımı kullanıcı oturum açtığında veya bilgisayar açıldığında çalışacak şekilde planlanabilir. Kurumsal ortamlarda genellikle Scheduled Task yöntemi daha kontrollü sonuç verir.

## Scheduled Task Örneği

```text
Task Name: BGInfo
Trigger: At logon
Action: Start a program
Program: \\example.local\NETLOGON\BGInfo\Bginfo64.exe
Arguments: \\example.local\NETLOGON\BGInfo\corporate-bginfo.bgi /timer:0 /silent /nolicprompt
Start in: \\example.local\NETLOGON\BGInfo
```

## Dikkat Edilecek Noktalar

- BGInfo dosyaları tüm kullanıcıların okuyabileceği bir paylaşımda tutulmalıdır.
- Gerçek kurum domaini ve sunucu adı dokümana yazılmamalıdır.
- Siyah ekran problemi yaşanırsa arka plan görsel yolu, izinler ve `.bgi` dosyası kontrol edilmelidir.
- Kullanıcı profili yerine merkezi NETLOGON yolu tercih edilebilir.
- Test işlemi önce tek bir bilgisayar veya test OU üzerinde yapılmalıdır.

## Kontrol Komutları

İstemci üzerinde policy durumunu kontrol etmek için:

```powershell
gpupdate /force
gpresult /r
```

Scheduled Task kontrolü için:

```powershell
schtasks /query /tn "BGInfo" /v /fo list
```

## Sorun Giderme

| Sorun | Olası Neden | Kontrol |
|---|---|---|
| Arka plan siyah görünüyor | Görsel yolu hatalı veya erişim yok | Paylaşım izinleri ve dosya yolu kontrol edilir. |
| BGInfo hiç çalışmıyor | Scheduled Task oluşmamış olabilir | `schtasks` ile görev kontrol edilir. |
| IP bilgisi yanlış görünüyor | Birden fazla network adaptörü olabilir | BGInfo field veya script filtresi gözden geçirilir. |
| Kullanıcıda çalışıyor, bilgisayarda çalışmıyor | GPO scope veya security filtering hatalı olabilir | OU, Security Filtering ve Delegation kontrol edilir. |

## Güvenlik Notu

Bu dokümanda gerçek kurum adı, domain, IP adresi veya kullanıcı bilgisi kullanılmamıştır. Tüm yollar örnek ortam için anonimleştirilmiştir.
