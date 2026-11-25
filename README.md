# MVP_REVERSE — VMProtect çalışma günlüğü ve kontrol noktaları

Bu depo, VMProtect sürümleri üzerinde yürütülen tersine mühendislik / kaynak yükseltme çalışmasının
**günlüğünü ve kontrol noktasını** saklar. Amaç: çalışma ortamı sıfırlansa bile işin nerede kaldığı
kaybolmasın. Kod ve kanıt dosyaları burada DEĞİL; ikili dosyalar ve büyük kanıt dizinleri MyRepo
deposundadır (adres WORKLOG / CHECKPOINT içinde).

## İçerik

| Dosya | Ne var |
|---|---|
| `CHECKPOINT.md` | En güncel durum: ne bitti, ne açık, kaldığı yerden nasıl devam edilir |
| `WORKLOG.md` | Ana çalışma günlüğü (V300'den itibaren, sürüm sürüm) |
| `WORKLOG2.md` | V312–V314: 3.5.1 → 3.9.4 özellik farkı, yükseltme yaması, ölçüm düzeltmeleri |

## Güncel durum (2026-11-25, V314)

3.5.1 → 3.9.4 yükseltme yamasının yeni komut desteği (SSE4.1 / AVX2 / BMI2 / MOVD-MOVQ) motorun kendi
çözümleyicisiyle ölçüldü: **eksik**. Daha önce "tamamlandı" sanılan sonuç bir test düzeneği hatasından
geliyordu ve geri çekildi. Ayrıntı ve doğrulanmış opcode haritası: `WORKLOG2.md` §36.

## Güvenlik

Bu depo herkese açıktır; kimlik bilgisi, belirteç veya kişisel veri içermez. Gönderim öncesi her dosya
desen taramasından geçer; eşleşme bulunursa gönderim durdurulur.
