# Group Policy - Password Lockout Policy

Bu doküman, Windows Server Active Directory ortamında hesap kilitleme ve şifre geçmişi politikasının temel olarak nasıl yapılandırılabileceğini açıklamak amacıyla hazırlanmıştır.

## Amaç

Kullanıcı hesaplarının yetkisiz erişim denemelerine karşı korunması, hatalı parola denemelerinin sınırlandırılması ve kullanıcıların eski parolalarını tekrar kullanmasının engellenmesi hedeflenir.

## Önerilen Politika Değerleri

| Politika | Önerilen Değer | Açıklama |
|---|---:|---|
| Account lockout threshold | 5 invalid logon attempts | 5 hatalı parola girişinden sonra hesap kilitlenir. |
| Account lockout duration | 15 minutes | Kilitlenen hesap 15 dakika sonra otomatik açılır. |
| Reset account lockout counter after | 15 minutes | Hatalı giriş sayacı 15 dakika sonra sıfırlanır. |
| Enforce password history | 5 passwords remembered | Kullanıcı son 5 parolasını tekrar kullanamaz. |

## GPO Yolu

```text
Computer Configuration
└── Policies
    └── Windows Settings
        └── Security Settings
            └── Account Policies
                ├── Account Lockout Policy
                └── Password Policy
```

## Yapılandırma Adımları

1. Group Policy Management Console açılır.
2. Domain seviyesinde veya ilgili OU üzerinde yeni bir GPO oluşturulur.
3. GPO adı örnek olarak `Password and Account Lockout Policy` verilebilir.
4. Account Lockout Policy altında hatalı giriş ve kilit süresi ayarları yapılır.
5. Password Policy altında şifre geçmişi ayarı yapılandırılır.
6. Politika ilgili OU veya domain seviyesine linklenir.
7. Test kullanıcısı üzerinde politika uygulanması kontrol edilir.

## Kontrol Komutları

İstemci bilgisayarda politika uygulamasını kontrol etmek için:

```powershell
gpupdate /force
gpresult /r
```

Domain parola ve kilitleme politikasını görüntülemek için:

```powershell
net accounts /domain
```

## Dikkat Edilmesi Gerekenler

- Hesap kilitleme politikası çok katı uygulanırsa kullanıcı destek talepleri artabilir.
- Wi-Fi, mobil cihaz, eski Outlook profili veya kayıtlı parola kullanan cihazlar tekrar eden hatalı girişlere neden olabilir.
- Politikayı tüm domain geneline yaymadan önce test OU üzerinde denemek önerilir.
- Servis hesapları için ayrı değerlendirme yapılmalıdır.

## Güvenlik Notu

Bu dokümanda gerçek domain, kullanıcı adı, IP adresi veya kurum içi özel bilgi kullanılmamıştır. Örnekler genel sistem yönetimi yaklaşımıyla hazırlanmıştır.
