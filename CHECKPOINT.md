# CHECKPOINT — son durum

Son güncelleme: **24.11.2026 — V313 sonrası**. Bu dosya her kritik ara noktada güncellenir;
yeni oturum önce bu dosyayı, sonra `WORKLOG2.md`'nin son bölümünü okur.

## 1. Depo durumu (GitHub API ile doğrulandı)

| Depo / dal | Uç commit | Not |
|---|---|---|
| `MyRepo` → `main` (özel) | `27b7eb8` — 85 commit | 83 = geri yükleme sonrası; +2 = V309–V313 işi |
| `MyRepo` → `tarih/v3-v8`, `tarih/v10-snapshot`, `tarih/v11-v17`, `tarih/sandbox-v289`, `tarih/v290-v302`, `tarih/v300-xprotect`, `tarih/v303-checkpoint` | segment uçları | geçmişin bir daha kopmasını önleyen emniyet dalları |
| `MyRepo` → `mega-v3-history` | `fcc08ee` | eski V1–V6 zinciri (13 commit) |
| `MVP_VMP_ALGORTIHM` → `main` (herkese açık) | `acd67ec` — 2 commit, 30 dosya | V311 UI ağacı + V312 fark/yama teslimleri |
| `MVP_REVERSE` → `main` (herkese açık) | bu commit | günlük + kontrol noktası |

Geri yükleme commit'leri: `022d9f82` (birleştirme, ağaç değişmedi) ve `69e1be98` (+4559 dosya, 1367,6 MB).

## 2. Tamamlanan (kısaca)

- **V301–V311:** resmî VMProtect sürümlerinin GUI analizi (11 sürüm, Qt analizcisi), 3.9.x ikili kanıtı,
  `Test_64.exe` varyasyon matrisi (11 sürüm × 15 varyasyon), `UniversalMVPUIAlgorithm_QT` push,
  11 sürümlük UI ağacı JSON + HTML.
- **V312:** 3.5.1 ↔ 3.9.4 özellik farkı (JSON + HTML), 3.5.1 kaynağına 3.9.4 özelliklerini ekleyen yükseltme yaması
  (21 nokta / 9 dosya): rastgele bölüm adı maskesi, proje XML (`VMVersion`, `VMInstances`, `VMComplexity`,
  `RandomSeed`), fonksiyon XML (`Complexity`), motor değişiklikleri. Yamalı motor derlendi, 37 varyasyon ölçüldü
  (fn1-virt 589 KB → c100 1.624 KB; hepsi-max 18,5 MB).
- **V313:** özel depo geçmişi geri yüklendi — main 8 → 83 commit, force kullanılmadan; 4559 dosya geri geldi;
  "kim sildi" sorusu kanıtla cevaplandı (aynı çalışma alanındaki ajan; 6 force-push + `git add -A` ile 4734 silme);
  "hangi sohbet" sorusu sohbet listesiyle eşleştirildi (bölüm 6); sandbox çalışma kopyası hizalandı;
  teslimler: `download/V313_kurtarma_kanit.json`, `V313_geri_gelen_dosyalar.txt`, `V313_kurtarilan_kaynaklar.zip`.

## 3. Sıradaki işler

1. **V312'den kalan kod işleri:** Lua binder'ları, Qt GUI property satırları, konsol anahtarları
   (`-vm`, `-vi`, `-vc`, `-rs`), ValueCryptor zincir uzunluğu, `AddUnknown` dalında Complexity.
2. **Yama raporunun .docx / .pdf sürümü** (Markdown hâli hazır: `YAMA_RAPORU_VMProtect_3.5.1_3.9.4.md`).
3. Fonksiyona özel complexity'nin çıktı boyutuna etkisinin büyüklüğü (incelendi, raporlanmadı).
4. Resmî 3.9.4 ikilisi elde edilemedi (Yandex Disk captcha) — 3.9.4 kanıtı V306 analizci JSON'undan geliyor.
5. `rea` decompiler / compare-artifacts: provider yok (Ghidra/IDA gerekir).

## 4. Açık riskler ve kullanıcıya öneriler

- **Eski PAT'ları iptal edin.** `scripts/gh_token.txt` 30 commit'te (tarih dallarında) duruyor;
  `WORKLOG_CPlusPort.md` 669. ve 769. satırlarında PAT biçiminde iki eski anahtar var.
- **PAT'ı depoda tutmayın.** Özel deponun ana dalında geçerli PAT düz metin 3 dosyada var
  (`skills/god-state-mvp-work/SKILL.md`, `download/GOD_STATE_MVP_WORK.md`, `download/GOD_STATE_MVP_WORK_v2_kisa.md`)
  ve `.env` dosyası izleniyor. Öneri: anahtarı her oturumda ortam değişkeni olarak verin.
- **chat.z.ai oturum anahtarlarını iptal edin.** `scripts/zai_history.py` içinde sabitlenmiş 2 JWT, V313'te
  dosyadan kaldırıldı (betik `ZAI_TOKENS` ortam değişkeninden okur); eski değerler git geçmişinde
  (`c83716c` … `69e1be9`) duruyor. Tüm oturumları kapatıp yeniden açmak en temiz çözüm.
- Diğerleri: `scripts/cpp/engine_keygen.cpp` 530. satırda PEM özel anahtar başlığı.
- **Bellek protokolündeki iki adım tehlikeli:** `git reset --hard FETCH_HEAD` ve denetimsiz `git add -A`.
  Yerine README'deki "güvenli hizalama" adımları kullanılmalı.

## 5. V313'te silinmenin zaman çizelgesi (TR)

| Zaman | Olay | Etki |
|---|---|---|
| 21.11 08:15 | bayat sandbox kopyası force-push (V17 → cp289-a) | 1299 dosya düştü; `livestep.py` (3 kopya), `mvpreverse3.py` |
| 23.11 16:31 | normal commit `b0be1996`, `git add -A` | 4734 silme kayda geçti; `scripts/cpp/livestep.cpp`, `mvpreverse3.cpp` |
| 23.11 17:31 ve 18:20 | iki force-push + yeni `git init` | 60 commit askıda, ana dalda 8 commit kaldı |
| 24.11 20:22–20:29 | geri yükleme (merge + ff push, force yok) | 83 commit, 4559 dosya geri geldi |

Not: sandbox'ın yerel saati UTC'dir; buradaki bütün saatler Türkiye saatine çevrilmiştir.

## 6. Hangi sohbet sildi? (salt okunur sohbet listesi; ayrıntı `WORKLOG2.md` V313-7)

- F1 (19.11 17:42), F2 (19.11 23:12), F3 (21.11 08:15), F4 (21.11 11:18): sohbet `fdb15d67…`
  (18.10.2026–22.11.2026 arası ana çalışma sohbeti); her olaydan dakikalar önce tur başlamış.
- N (23.11 16:31), F5 (17:31), F6 (18:20): sohbet `40ecabba…` (bu sohbet, 23.11 10:54'te açıldı).
- Yapan ajan (commit kimliği `Z User <z@container>`); kullanıcı değil. Mesaj içerikleri okunamadı (API vermiyor);
  eşleştirme zaman örtüşmesi + WORKLOG kayıtlarına dayanıyor.
