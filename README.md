# MVP_REVERSE — Çalışma Günlüğü ve Kontrol Noktaları

VMProtect 3.5.1 → 3.9.4 tersine mühendislik ve yükseltme çalışmasının **kalıcı çalışma günlüğüdür (WORKLOG)**.
Amaç: çalışma ortamı (sandbox) sıfırlansa, bağlam kaybolsa ya da dosyalar kaybolsa bile işin **kaldığı yerden**
devam edebilmesidir. Bu depoya yalnız günlük ve kontrol noktası bilgisi konur; kaynak kod, ikili dosya ve
kanıtlar özel depoda durur.

## Yeni bir oturumda okuma sırası

1. `CHECKPOINT.md` — en son durum (commit numaraları, tamamlananlar, sıradaki işler, açık riskler).
2. `WORKLOG2.md` — son bölüm aşağıdadır (`## 34) V313`); her bölüm "istek → engeller → tamamlanan → devam notu" biçimindedir.
3. `WORKLOG.md` — V1–V311 arası ayrıntılı geçmiş (700 satır; başlıklar `## N) V<no> (tarih, turn)`).

## Depolar

| Depo | Görünürlük | İçerik |
|---|---|---|
| `0xByteDeveloper/MyRepo` | özel | Kaynak kod, betikler, teslim dosyaları, tam git geçmişi (V313 sonrası 85 commit) |
| `0xByteDeveloper/MVP_REVERSE` | herkese açık | Bu çalışma günlüğü ve kontrol noktaları |
| `0xByteDeveloper/MVP_VMP_ALGORTIHM` | herkese açık | 3.5.1 ↔ 3.9.4 özellik farkı (JSON + HTML) ve yükseltme yaması |
| `0xByteDeveloper/UniversalMVPUIAlgorithm_QT` | — | Qt arayüz analizi aracı (V310'da güncellendi) |

Özel depoya erişim, depo sahibinin kişisel erişim anahtarı (PAT) ister. **Anahtar bu depoda hiçbir zaman
saklanmaz, yazılmaz, alıntılanmaz.**

## Sandbox sıfırlandıktan sonra güvenli hizalama

```bash
cd /home/z/my-project
git fetch origin                 # origin = kimlik bilgisi içermeyen HTTPS adresi
git status --short | grep -c '^ D'   # 0 olmalı; değilse DUR (aşağıdaki kural 2)
git merge --ff-only origin/main  # hızlı ileri değilse durulur; birleşme çakışması çözülmeden commit atılmaz
```

Eski sürüm dosyalarına bakılacaksa (kısmi klon, `blob:none`): `git show origin/tarih/<dal>:<yol>` komutu
kimlik doğrulaması ister. Toplu çekme için `scripts/v313_yerel_hizala.py` (özel depoda) kullanılır.

## Zorunlu güvenlik kuralları (24.11.2026 kaybından sonra)

1. **Yasak:** `git push --force`, `git reset --hard`, mevcut deponun üstüne `git init`, `git clean`,
   kullanıcı dosyasının üstüne `git checkout -- <dosya>`.
2. `git add -A` (ya da `git commit -a`) **öncesinde** `git status --short | grep -c '^ D'` kontrol edilir.
   Sonuç 0 değilse silme commit'lenmez: silinmenin nedeni sandbox geri dönüşümüyse önce dosyalar geri getirilir.
3. Kontrol noktası = hızlı ileri (fast-forward) push. Push reddedilirse durulur; önce çekilir, sonra tekrar denir.
   Geçmiş değişecekse (ör. büyük dosya ya da sızan anahtar temizliği) eski uç önce `tarih/<tarih>` dalına sabitlenir.
4. Kimlik bilgisi (PAT, JWT, `.env`, `.pem`, çerez) hiçbir depoya konmaz. Anahtar yalnız ortam değişkeninden
   okunur; komut satırına, günlüğe, rapora, hata mesajına yazılmaz.
5. Her kritik ara noktada: `WORKLOG2.md`'ye bölüm eklenir + `CHECKPOINT.md` güncellenir + bu depoya push edilir.

## 24.11.2026 — Özet

Özel deponun ana dalında yalnız 8 commit kalmıştı; 81 commit'ten 60'ı hiçbir daldan erişilemez durumdaydı ve
`livestep.cpp`, `livestep.py` gibi kişisel proje dosyaları ana daldan düşmüştü. V313'te geçmiş geri bağlandı
(main 8 → 83 commit, force kullanılmadan), 4559 dosya (1367,6 MB) eski yerine kondu ve kök neden
(sandbox geri dönüşümü + `git add -A` + `reset --hard`/force push alışkanlığı) günlüğe yazıldı.
Ayrıntı: `WORKLOG2.md` bölüm 34 ve özel depodaki `download/V313_*` raporları.
