# File Server - Share Permission Checklist

Bu doküman, Windows File Server ortamında paylaşım izinleri ve NTFS izinlerinin kontrol edilmesi için temel bir checklist sunar.

## Amaç

Dosya sunucularında kullanıcıların doğru klasörlere doğru yetkilerle erişmesini sağlamak, gereksiz geniş yetkileri azaltmak ve yetki problemlerini sistematik şekilde incelemek.

## Temel Kavramlar

| Başlık | Açıklama |
|---|---|
| Share Permission | Ağ paylaşımı üzerinden erişim yetkisini belirler. |
| NTFS Permission | Dosya sistemi üzerindeki gerçek klasör/dosya yetkilerini belirler. |
| Effective Access | Kullanıcının grup üyelikleriyle birlikte gerçekte sahip olduğu erişimi gösterir. |
| Least Privilege | Kullanıcıya sadece ihtiyacı olan minimum yetkinin verilmesi prensibidir. |

## Önerilen Yaklaşım

Genel kurumsal kullanımda paylaşım izni tarafında kontrollü bir yapı, NTFS tarafında ise grup bazlı detaylı yetkilendirme tercih edilmelidir.

Örnek yaklaşım:

```text
Share Permission: Authenticated Users / Change veya Read
NTFS Permission: AD güvenlik gruplarına göre Read, Modify veya Full Control
```

## Kontrol Listesi

- Paylaşım adı ve fiziksel klasör yolu doğru mu?
- Share Permission tarafında gereksiz Everyone / Full Control yetkisi var mı?
- NTFS Permission tarafında kullanıcı yerine AD grupları kullanılıyor mu?
- Kullanıcının hangi gruplara üye olduğu kontrol edildi mi?
- Inheritance açık mı, kapalı mı?
- Alt klasörlerde farklı yetki kırılımları var mı?
- Kullanıcı için Effective Access sonucu kontrol edildi mi?
- Eski, pasif veya ayrılan personel grupları temizlendi mi?
- Kritik klasörlerde gereksiz Modify veya Full Control yetkisi var mı?

## PowerShell Kontrol Komutları

Paylaşımları listelemek için:

```powershell
Get-SmbShare
```

Belirli bir paylaşımın izinlerini görmek için:

```powershell
Get-SmbShareAccess -Name "ShareName"
```

NTFS izinlerini kontrol etmek için:

```powershell
Get-Acl "D:\Shares\FolderName" | Format-List
```

Kullanıcının grup üyeliklerini kontrol etmek için:

```powershell
Get-ADUser username -Properties MemberOf | Select-Object -ExpandProperty MemberOf
```

## Sorun Giderme Tablosu

| Sorun | Olası Neden | Kontrol / Çözüm |
|---|---|---|
| Kullanıcı klasöre erişemiyor | Grup üyeliği eksik olabilir | AD grup üyeliği ve NTFS izinleri kontrol edilir. |
| Kullanıcı dosya açıyor ama değiştiremiyor | Read yetkisi vardır, Modify yoktur | NTFS Modify yetkisi kontrol edilir. |
| Kullanıcı fazla klasör görüyor | Üst klasörde geniş yetki olabilir | Inheritance ve üst klasör izinleri kontrol edilir. |
| Bazı kullanıcılar erişiyor, bazıları erişemiyor | Grup bazlı yetki farkı olabilir | Kullanıcıların grup üyelikleri karşılaştırılır. |
| Ağ yolundan erişim yok | Share Permission veya network erişimi sorunlu olabilir | Share Permission ve bağlantı testi yapılır. |

## İyi Uygulamalar

- Yetkilendirme doğrudan kullanıcıya değil, AD gruplarına verilmelidir.
- Kritik klasörlerde Full Control yetkisi sınırlı tutulmalıdır.
- Yetki değişiklikleri kayıt altına alınmalıdır.
- Ayrılan personel veya görev değişikliği olan kullanıcıların erişimleri düzenli kontrol edilmelidir.
- Dosya sunucusu izinleri belirli aralıklarla gözden geçirilmelidir.

## Güvenlik Notu

Bu dokümanda gerçek kurum adı, kullanıcı adı, sunucu adı, paylaşım yolu veya IP adresi kullanılmamıştır. Örnekler genel sistem yönetimi yaklaşımıyla hazırlanmıştır.
