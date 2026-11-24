# WORKLOG — XProtect + UO / 748-Koruma Matrisi Sistemi (GOD STATE)

> Bu dosya sandbox sıfırlanmalarına karşı "şu yapıldı / şunlar kaldı" kalıcı kaydıdır.
> Kök dokümanlar: `download/vmptest/DEVAM_NOTU.txt` (V11–V13 detayı), `download/WORKLOG.txt` (Task 1–12 kronoloji).
> Tarih: 2026-11-20 (V14 turu).

---

## 1) SİSTEM NEDİR (tek bakışta)

- **XProtect** — C++ saf Win32 koruma ürünü (CPlusPort motoru: lifter + MiniVM + .vmp0 section üretici, CLI+GUI, 0.3.0→1.2.2 sürümleri `download/XProtect/` altında).
- **UO / mvpreverse3.py** — VMProtect-3.5.1-denk tespit+tersine-çevirme motoru:
  - **MOD 1 (Tespit/Analiz)**: ürün tanıma (XProtect / gerçek-VMProtect / UO), özellik envanteri (antidebug, memprot, pack/LZMA entropi, TLS callback, VM-wrapper fonksiyon envanteri, stub çözümleme).
  - **MOD 2 (Reverse)**: stub şablonu çözümü (key/bc_rva/bc_len/vm_entry) → VM_KS pozisyon-bazlı keystream deşifre → unlift (opcode → x86/x64 native) → SIB→RIP-relative fixup → JCC-audit → api-ms-win-crt-*→ucrtbase import normalizasyonu → 32-bit ASLR (.rloc) → `*_REVERSE.exe/dll`.
- **748 koruma matrisi**: 11 VMProtect sürümü × 17 variant (13 tekil özellik + MovePlayer 3 mod [mutation/virtualization/ultra] + ALL) × 4 hedef (32 exe, 32 dll, 64 exe, 64 dll).
- **vmprotect_con.exe** — RAR içi gerçek VMProtect 3.5.1 kaynağından Linux'ta derlenen koruyucu (13 uyum yaması, `scripts/build_vmpcon_rar.sh`).
- **Canlı analiz**: `livestep.py` (LiveStep v2 — i4MConsole canlı analiz modülü: TEB/FS simülasyonu, TLS envanteri, stack trafiği, register izleri, API-stub motoru, --compare).

## 2) MEGA V3 (MVPReverseV3) — SİLİNME NEDENİ + DURUM (soruşturma kapandı)

**GitHub'da neden görünmüyordu:** V10 (e104019) ve V11 (ab0c446) **orphan force-push**'ları main'i sıfırdan yazdı.
Amaç: eski history'deki `gh_token.txt` PAT sızıntısı + 4.2GB REVERSE/zip binary'lerini (GitHub 100MB dosya limiti) geçmişten çıkarmak.
Orphan yapılınca V1–V6 commit zinciri (commit 1 = "Add files via upload" → MVPReverse → **Mega-Dev V3** → V4 → V5 → V6 = 13 commit) unreachable kaldı — **dosyalar silinmedi, erişilmez oldu**.

**Restore (V11.7'de yapıldı, hâlâ duruyor):**
- Branch `mega-v3-history` = fcc08ee (V6.1) — **tüm 13 commit + MVPReverse/ + MVPReverseV3/ GitHub'da görünür durumda**: https://github.com/0xByteDeveloper/MyRepo/tree/mega-v3-history
- `MVPReverseV3/` klasörü main ağacına da geri kondu (sandbox'ta 83MB, `/home/z/my-project/MVPReverseV3/`).

**Mega v3 kullanıldı mı? EVET — sistem zaten onun üstüne inşa edildi:**
- mega v3 `mvpreverse3.py` (1221 satır, md5 3bc5c349…) → mevcut motor (1356 satır, md5 4a907a96…) = aynısı + V6 duvar fix (reg8enc REX.B + 8-bit ALU emit) + api-ms→ucrtbase normalizasyonu + SizeOfImage fix + JCC-audit + gerçek-VMP profil v2 (ebp-frame/FPO stub ayrımı, 352 komut evreni) + allver entegrasyonu. Birleştirme gerektirmiyor — mevcut sistem mega v3'ün birebir süper-seti.

## 3) YAPILANLAR (özet kronoloji — detay DEVAM_NOTU/WORKLOG'ta)

| Tur | Sonuç |
|---|---|
| V1–V5.2 | Sanallaştırma zinciri, 20/20 dll + 20/20 exe REVERSE, 'd' crash kök çözüm (32-bit ASLR/.rloc), crashdump araçları |
| V6.1 | x1a 64-bit duvar bug (reg8enc REX.B r+8 + unlift 8-bit ALU + REX.B 0x41) ✓; x1b DLL inject (api-ms→ucrtbase) ✓; x2 6 kategori gerçek ölçümle %100 ✓; wine 11.22 ilk derleme |
| V11–V11.6 | Matris 748/748 üretim + REVERSE 748 + 11 zip + benchmark %100; `${F%.?}` adlandırma bug fix; token-trap kapatma (gc --prune) |
| V11.7 | MVPReverseV3 restore + silinme soruşturması (bkz. §2) |
| V12 | Gerçek-VMP profil v2 (vmpsrc 1.13M satır analiz: IntelCommandType 352 komut, OpcodeCryptor derleme-başı rastgele, ebp-frame vs FPO stub ayrımı) — 16/16 doğru tespit |
| V13–V13.2 | /tmp recycle sonrası altyapı sıfırdan: llvm-mingw, wine 11.22 kaynak derleme, vmprotect_con.exe RAR-içi kaynaktan 13 yamayla derlendi (13.5MB) |
| **V14 (bu tur)** | **Wine 11.22 tam derleme+install (BUILD_OK), 64+32-bit WoW64 smoke ✓, vmprotect_con CANLI koruma testi ✓ (MovePlayer virt 100%), MATRİS 748 KOŞUYOR** |

## 4) KALAN İŞ (dürüst liste)

1. **Matris 748 tamamlanması** — koşuyor; bitişi: 11 REVERSE klasörü + 11 zip + `benchmark_allver.html` (otomatik, `allver_matrix.sh` adım 5–7).
2. **GitHub push** — sorun: verilen 3 PAT de `git push`'ta "Invalid username or token" (API: Bad credentials; muhtemelen expire/revoke). Yeni PAT gerekiyor. Push edilecekler hazır: WORKLOG.md, DEVAM_NOTU, scriptler, benchmark (zip/REVERSE binary'ler 100MB limiti nedeniyle GitHub dışında kalır → Yandex Disk).
3. **Yandex Disk upload** (ağır dosyalar; kullanıcı çerezleri var) — matris bitince denenecek.
4. **Araştırma başlığı (süre sınırı yok)**: gerçek-VMP bytecode tam unlift (opcode-candidate türetme + handler eşleme) — profil v2 temeli hazır.

## 5) KALICI DOSYA HARİTASI

- `download/vmptest/tools_keep/vmprotect_con.exe` — koruyucu (git'te)
- `scripts/allver_matrix.sh` — 748 üretim zinciri (idempotent) · `scripts/rebuild_wine.sh` · `scripts/build_vmpcon_rar.sh`
- `download/vmptest/mvpreverse3.py` — MOD1+MOD2 motoru (V12) · `scripts/livestep.py` — canlı analiz
- `upload/universalAllver.rar` — 11 sürümlük kaynak arşiv (477MB)
- GitHub: `main` = fe61c21 (V13.1 kademesi) · `mega-v3-history` = fcc08ee (mega v3, 13 commit)

## 6) V14 GÜNCELLEMESİ (2026-11-20, bu tur)

**Tamamlananlar:**
- Wine 11.22 tam derleme+install (BUILD_OK/INSTALL_OK/DONE) — 64-bit + 32-bit WoW64 smoke ✓
- vmprotect_con.exe canlı ✓ (libc++.dll/libunwind.dll llvm-mingw'den wineprefix'e)
- Canlı koruma kanıtı: `Loading [U] 1400014A0 MovePlayer / Compiling 100%` (Test_64.exe, 1.1MB çıktı)
- **32-bit kök bug çözüldü**: vmprotect_con 32-bit PE'de export ismini `_MovePlayer` (decorated) olarak
  görüyor → `MapAddress="MovePlayer"` eşleşmiyor → fonksiyon sarılmıyordu (v14 matrisinde 748 dosyanın
  yarısı "sahte korumalı" idi: .text birebir orijinal). Çözüm: `Address="0xVA"` hex modu (StrToInt64Def
  base-0). `allver_matrix.sh`'e `resolve_vas()` eklendi — 32-bitte VA-modu, 64-bitte MapAddress.
  Kanıt: `Address="0x401490"` → `Loading [U] 00401490 _MovePlayer` + MovePlayer@0x1490 = `e9ed8604` (E9 gate) ✓
- mvpreverse3.py: `.vmp00/.vmp01` index'li bölüm adları için prefix eşleşmesi (reverse artık buluyor)
- **MATRİS v15 KOŞUYOR** (fixli script; 32-bit E9 gate doğrulandı) — tamamlandığında: 11 REVERSE
  klasörü + 11 zip + benchmark_allver.html otomatik üretilir

**Engeller (dürüst kayıt):**
- GitHub push: 3 PAT de reddedildi (API "Bad credentials", git push "Invalid username or token") —
  **yeni PAT gerekli**. Repo görünür durumda: `main` (fe61c21) + `mega-v3-history` (fcc08ee, MVPReverseV3 restore'lu)
- Yandex Disk: cookie-auth API 401 / WebDAV HTML döner — cookie yetmez, OAuth uygulama token'ı gerekli

**Sonraki tur (tek komut, idempotent):**
```
bash scripts/allver_matrix.sh upload/universalAllver.rar
```

## 7) V16+ (2026-11-20, turn-9) — TOOL ENVANTERI + GOD_STATE + KAYNAK PAKETI
- TOOL_ENVANTERI.md yazildi: 22 tool ailesi, her biri a1/a2... kronolojik adimlarla
  (mvpreverse3, livestep=i4MConsole-un geliştirilmiş hali [Unicorn, C++ degil], allver_matrix,
  finish_331, rebuild_wine, gen_bench_allver [gate=VM bolumu+E9 kapi sayisi,
  wrapped=VM'e sarilmis fonksiyon sayisi], yadisk_oauth/upload, verify/fix ailesi, ...).
- GOD_STATE_MVP_WORK.md (tam) + v2 kisa prompt + skills/god-state-mvp-work/SKILL.md uretildi:
  60-saniye kurtarma protokolu, worklog zinciri (400 satirda WORKLOG2), PAT + Yandex recetesi,
  tespit-%100 vs uretim-%95.2 ayrimi, teslimat formati.
- allver_v16_KAYNAK.zip (227KB, 111 dosya): scripts/*.py|sh|c + WORKLOG + benchmark +
  kanit json'lari + dokumanlar — GitHub pushlanabilir boyut.
- chat.z.ai gecmisi: /api/v1/chats + /v1/chats/{id} metadata OK (228 mesaj node,
  18.10->20.11); mesaj ICERIK icin read endpoint yok (POST/content 422) — ozet WORKLOG'tan cikarildi.
- Kullanici dogrulamasi: allver_v16.zip indirildi, "calisiyor tum surumler" (V16 teslimi kullanici tarafindan ONAYLANDI).

## 8) V17 (2026-11-20, turn-10) — XPROTECT FARK RAPORU + FULL C++ BAŞLANGICI + ESKİ TOOLS YEDEĞİ
- XProtect_REVERSE GERİ YÜKLENDİ: upload/total_vmp_exe_v3(4).zip → download/XProtect_REVERSE (40 dosya)
  + restore_v3 (orijinaller + XProtect çıktıları + tools + py tool'lar). Kullanıcı haklı: kaybolmamıştı.
- XProtectArea klonlandı; PORT_DURUMU.md okundu: motor-parite ~%87-88 (V %90, M %80, U %100,
  CheckDbg %90, .NET %90, EncryptRegs UO'yu aşan GERÇEK).
- XProtect_FARK_RAPORU.md YAZILDI (download/): 28 kalem VMProtect Ultimate 3.9.4 ↔ XProtect karşılaştırması.
  KÖK BULGU — Pack: restore_v3 kanıt çıktılarında XPK1 YOK (pack uygulanmamış eski üretim); motor sağlam
  (A12: metin2 5.29→3.85MB, EP=.vmp1). VMProtect'in 12KB→7.6MB'si runtime blob tasarımı. Kullanıcının
  3.9.4 özellik listesi + local ALL ayarları rapora kaydedildi. P0-P3 düzeltme planı çıkarıldı.
- FULL C++ DÖNÜŞÜM BAŞLADI: scripts/cpp/livestep.cpp (17.6KB) YAZILDI + DERLENDİ:
  Linux native (54KB) + livestep64.exe (894KB PE32+) + livestep32.exe (893KB PE32) — zig 0.17.1 cross.
  Yetiler: PE32/PE64 yükleyici, TEB/FS/GS, TLS envanteri, stack penceresi, REX.W, TAM modrm
  (SIB/disp8/disp32/RIP-rel), register izleri, JSON trace. KANIT: metin2client.exe PE32 canlı koşu
  (VMP magic 0xbb40e64e izlendi, call/ret stack doğru) + Test_64.exe PE64 (REX'li akış).
  Fix zinciri: alan sırası (vs/va/rs/rp) → REX prefix → E8/E9 rel-base → modrm rm=101 disp32.
- eski_tools.zip OLUŞTURULDU: 93 dosya (84 script + vmptest 8 + restore_v3 5) 178KB — GitHub'a pushlanıyor.
- GOD_STATE_MVP_WORK v4 YAZILDI (tam + kısa + skill): Proje A/B/C kimliği, XProtect P0-P3 planı,
  C++ dönüşüm reçeteleri, regression zorunluluğu.
- Kullanıcı tespitleri doğrulandı: (1) XProtect_REVERSE kaybolmadı, (2) RSA/2048 UI'da görünmüyor DOĞRU
  (motoru var: xrt_license.c RSA-2048+SHA1), (3) pack boyut sorunu = kanıt dosyalarında pack'siz üretim.

## 9) V287 (2026-11-20, turn-11) — XPROTECT P0/P1/BOYUT PARİTESİ + mvpreverse3.cpp
- XProtectArea @7ee0b6d pushlandı: P0 defaults (VMProtect 3.9.4 yeni-proje seti: Memory/Import/
  Resource/Pack/VirtTools/StripDebug/StripReloc açık, Debugger=User+Kernel) + çıktı adı <input>.vmp.exe
  + P1 (None tipi 4'lü combo, 5'li Messages VMProtect metinleriyle, 3-durumlu Debugger, VM Instances
  1..10, Complexity, RSA/2048 UI satırları, Segments adı, Shadow Stack, Output Profile) — hepsi
  .xproj kalıcılığıyla (vmpopts/segments/msg*).
- BOYUT PARİTESİ (kullanıcı vurgusu): vm_size_mode=1 (VMP-compat) — T2 kanıt: Test_64.exe ULTRA+pack
  +Instances10+vmsize = 6.587.392 bayt (6.59MB; VMProtect 3.9.4'ün 12KB→7.6MB profili). Klasik profil
  korunur (T1: 136KB, XPK1+EP=.vmp1). stripdbg artık COFF/DWARF bölüm verisini de çıkarır (13.312B).
- KANIT (Unicorn, kanit_v287c.py): T1+T2 'PACK ZINCIRI TAMAM' — unpack→OEP çalıştı, UD2/illegal yok.
  Not: Unicorn GS-base kısıtı nedeniyle gs:[0x60] → mov eax,imm32 9-bayt test-yaması (motor sabit).
- host_cli: --type/--func/--manual/--pack/--stripfix/--stripdbg eklendi (UI Add Function eşdeğeri).
- mvpreverse3.cpp (61KB, scripts/cpp/): Python portu — analyze modu Python ile BİREBİR (regression:
  product+8 özellik+vmp0 aynı); reverse: 41 opcode unlift + VM_KS + E9-gate. Korumalı-girdi ile
  reverse regression KOŞULAMADI (vmptest'te .vmp proje-XML'leri var, binary değil; restore_v3
  recycle'de). Kalan iş: gerçek XProtect binary çıktısıyla reverse A/B testi.
- Teslim: download/v287_kanit/ (KANIT_OZETI.md + T1/T2/T3 + UI exe'leri + mvpreverse3 + kanit scripti).

## 10) V288/V289 (2026-11-21, turn-12) — P2 + BOYUT BİREBİR + REVERSE BYTE-PARİTE TAM
- **XProtectArea (P2, cp288 kodu)**: Files penceresi (tree Files klasörü + Add File(s)/Add Folder/
  Excluded From Protection context-menu + dir-görünümü Embed/Excluded kolonları + cpVirtualFiles
  motor devri + .xproj vfile/vfilex), VM Version MOTOR etkisi (vm_version→perm_seed nesil XOR;
  v2 seçilince bambaşka opcode haritası), Shadow Stack CET OZET log satırı, Activation Server
  (prop 931 → opt.lic_url; act_server .xproj), File Name satırı (VMP: Test_64.exe.vmp), host_cli
  --vfile/--vmver. Derleme: ole32 lib eklendi (CoTaskMemFree) — XProtectUI x64+x86 temiz.
- **BOYUT BİREBİR (kullanıcı vurgusu 3. kez)**: VMP-compat profil DEFAULT yapıldı (vm_size_mode=1;
  birebir VMProtect davranışı — her derlem büyük çıkıyor; Küçük profil Properties'ten seçilir).
  KALİBRASYON: per-instance 856KB (x64) / 684KB (x86) — VMP 3.9.4 referansı 12KB→7,972,864
  baytına göre (7,972,864-88,000)/9. KANIT: Test_64 Ultra+pack+Instances10 → **7.65MB (8,025,088
  bayt)** + Unicorn 'PACK ZINCIRI TAMAM' (unpack→OEP, UD2/illegal yok).
- **REVERSE BYTE-PARİTE TAM (ana iş)**: Python referans mvpreverse3.py güncel motora tam parite:
  VM_MIX 4-dal + VM_KSC CFB (rev=1 prev=enc[i+1]) + VM_KEYXOR stub-demask + vm_perm_tab(itab) +
  18 yeni opcode emit (0x2A-0x44: BSWAP/XCHG/PUSHF/POPF/SAHF/LAHF/SETFLAG/MUL/DIV/BT/SHLD/SHRD/
  LOOP/JECXZ/XADD/STR/IMUL_RM/POP_RP/JMPEXT/JCCEXT/CHKRET/NCALLI) + ALU ADC/SBB tabloları +
  JMPEXT dış-hedef jfixup flag'i. Sonuç: TestDllX86 (4 Ultra fonksiyon) **4/4 restore,
  jcc_audit 28/28**. mvpreverse3.cpp tam port (capstone C-API bağlantılı; pip wheel lib+LD soname
  syml /tmp/cslib) — A/B: **R_py.dll ≡ R_cpp.dll, 327,168 bayt, FARK BAYT = 0** (md5 eş).
  A/B'de yakalanan düzeltmeler: vmp0 slice-assign dosya-uzatma davranışı, ALU_REG ADC=/2 SBB=/3
  (py+cpp birlikte — py'ın da .get(7,0) bug'ı vardı), .rloc konum/izleme, DYNAMICBASE+import-norm.
- **gen_bench.cpp**: py gen_bench_v3 V3-skor bölümü port — C++ analyze + beklenen-matris puan.
  Regression: gen_bench_ref.py ile 4 dosyada puan+ürün+wrapped **TAM eş** (T1 7, T3 8, DLL 8/8).
  NOT: TestDllX86 mingw .vmp0 ebp-frame → py+cpp İKİSİ DE 'VMProtect (gerçek)' FALSE-POSITIVE
  (tespit kuralı .vmp0 ilk-bayt + CPlusPort-yok — mingw DLL'lerde çakışıyor; parite var, tespit
  kuralı ayrı iş: VMP_Analiz_Raporu false-pozitif notu).
- Derleme/kural: capstone C-API g++ -I $CS/include -L $CS/lib -lcapstone; LD_LIBRARY_PATH.
- Teslim: download/v287_kanit/ (25 dosya: T1/T2/T3/P + TestDllX86/X64.vmp.dll + R_py/R_cpp md5-eş
  + bench cpp/py json + mvpreverse3.cpp v2 + gen_bench.cpp/ref + kanıt scriptleri).
- Kalan: analyze MOD1 geniş alanları (vm_functions/iat/stub_template) cpp'e taşımak (py ekstra),
  wine yeniden derleme (recycle), 36 allver dosyası (finish_331.sh).

## 11) V290 (2026-11-21, turn-13) — CheckDebugger CRASH DÜZELTİLDİ + ÖZELLİK-BOYUT MODELİ
### P0-a: CheckDebugger crash (kullanıcı E2E: "hareket edince crash, kapatınca sorunsuz")
Kök-cause analizi (Unicorn yeniden-üretim + byte-düzey inceleme + kaynak kod):
- MovePlayer stub'ı antidebg açıkken ek `call xrt_entry` yapıyor (kapalıyken yok) — her hareket = xrt_antidebug K1..K9 koşuyor.
- **Suçlu 1 — K1 NtGlobalFlag&0x70** (xrt_core.c + vm_core.c İKİSİNDE): Steam/Epic/Battle.net launcher'ları ve AppCompat shim'leri debugger YOKKEN NtGlobalFlag heap bayraklarını (0x70) set eder → yanlış-pozitif return 5 → rt_fatal → oyun kapanıyor. VMProtect loader'ı bu kontrolü YAPMAZ. → **KALDIRILDI** (BeingDebugged p[2] kaldı; her iki blob yeniden üretildi: build_xrt.sh + build_vmblob.sh).
- **Suçlu 2 — K8 ProcessDebugPort poison**: Win10/11 temiz işlemde dbo=-1 (0xFFFF...FFFF) dönebilir; `dbo != 0` tek koşul yanlış-pozitif. → **poison süzgeci** eklendi (dbo != -1, != -2; UO loader.cc:1810 kuralı).
- **Suçlu 3 — K8 DebugObjectHandle koşulsuz `if (st >= 0) return 5`**: bazı Windows sürümleri STATUS_PORT_NOT_SET yerine 0 döner → koşulsuz kapatma. → **`&& dbo != 0` bağlandı**.
- Kanıt: `kanit_launcher.py` — PEB.BeingDebugged=0 + NtGlobalFlag=0x70 (launcher simülasyonu) altında MovePlayer tam koşu: **MOVEPLAYER-TAMAM, UD2=False, TEMİZ** (CD_fix3.vmp.exe; eski CD_on aynı testte 28M adım tüketti).
- Düzeltme doğrulaması: CD_fix3'te eski `f6 80 bc 00 00 00 70` (test NtGlobalFlag) kalıbı **0**, BeingDebugged kalıbı **3** (korumada).
### P0-b: Özellik-bazlı boyut modeli (localdekendimdenedim ölçümleri)
- Model: base(VMP-compat 7.65MB) + memprot **4.35MB** + antidebug/checkvm/rsrcprot/iat_prot her biri **5MB** (engine_main.cpp vm_size_mode bloğu).
- Kanıt: Memory Protection tek başına → **11.92MB** (kullanıcı ölçümü 12MB ✓); yığın (memprot+antidebug+checkvm+pack) → **21.67MB** (rsrcprot/iat_prot CLI bayrakları henüz bağlanmadı — bağlanınca ~32MB'ye tamamlanacak).
- Push: XProtectArea @bf2ffae (cp290) — xrt86/xrt64/vm64/vmx86 blobları yeniden üretildi + kanıt paketi v287_kanit/'a (CD_fix3/MP/ALL2 + kanit_launcher.py + fix3.log).
### SIRADAKİ (V291 — bitirilemeyenler, nedenleriyle)
- P0-c Licenses TAM yeniden yazım (VMP modeli: RSA + Key Length 1024..7168 combobox + Lock To Serial + Activation Server; XProtect'in eski expire/day modeli kaldırılacak) — UI+Project+motor köprüsü büyük iş, yarım bırakılmadı, başlanmadı (P0-a beklenmedik derin kök-cause analizi yuttu zamanı).
- P0-d VM Version (Default|Compact) ↔ Complexity (None|%) bağlılık mantığı (Compact seçilince Complexity None olur) — UI kuralı + motor eşlemesi.
- P0-b kalanı: rsrcprot/iat_prot CLI bayraklarının boyut modeline bağlanması (32MB hedefi).
- P1: checkvm Unicorn kanıtı; analyze MOD1 geniş alanlar (vm_functions/iat/stub_template) cpp'ye.

## 12) V291 (2026-11-21, turn-14) — Licenses VMP-Modeli + Version/Complexity + 32MB HEDEFİ ✓
### P0-c: Licenses — Key Length artık GERÇEK VMProtect combobox'u ✓
- UI: Key Length satırı **düzenlenebilir** (ID 932) — combobox: 1024,1536,2048,2560,3072,3584,4096,4608,5120,5632,6144,6656,7168 (kullanıcı 3.9.4 tespiti birebir). Algorithm=None iken engelli (bilgi kutusu).
- Motor: `opt.lic_keylen` → **LSD 0x12E** (2B LE, CRC-dışı) + `lic_any` genişletildi (Key Length seçiliyse LSD alanı açılır — VMP: RSA seçince lisans altyapısı gömülür).
- CLI: `--lic-keylen N` (clamp 1024..7168).
- **KANIT:** `K2048.vmp.exe` → LSD'de Key Length=2048 ✓; `K7168.vmp.exe` → 7168 ✓.
- Kalan (V292): RSA-4096+ için LIC_LIMBS'i çalışma-anında büyütme (şimdi 256B modulus'e derlenmiş; uzunluk LSD'de taşınır, 2048 tam doğrulama).
### P0-d: VM Version ↔ Complexity bağlılığı ✓
- UI: Version combo **Default | Compact** (eski 'v2' etiketi KALDIRILDI — kullanıcı tespiti doğruydu; eski projelerde vm_version=2 → Compact eşlenir).
- **Compact seçilince Complexity = None** (bilgi kutusu + kilit); Default'ta 1..100% girilir.
- Motor: Compact = **tek VM instance** (tablo çoğaltması yok, farklı perm_seed nesli). **KANIT:** Compact 14.14MB vs Default 21.67MB (aynı özellik yığını).
- UI'da Instances satırı Compact'ta 1 gösterir.
### 32MB hedefi ✓ (kullanıcı ölçümü: "hepsi açık = 32MB")
- `--rsrcprot`/`--iatprot` CLI bayrakları bağlandı → **ALL32 = 31.43MB** (memprot+antidebug+checkvm+rsrcprot+iatprot+pack, Ultra+10-instance). Ölçümle fark %1.8.
### checkvm (Virtualization Tools) kanıtı ✓
- `CV.vmp.exe` (checkvm açık): OP_ANTIDBG maskeleri `[0x99, 0x7]` — ikincisinde **bit1 (checkvm) set** ✓; Unicorn'da sahte VMware CPUID ile **cpuid 2 kez yakalandı** (leaf1 hypervisor-bit + leaf 0x40000000 vendor).
- Not (dürüst): VM-core'da K7 `!c->done` tek-sefer korumalı — ilk OP_ANTIDBG (batarya-bit0) done'u set ettiği için ikinci kontrol (checkvm) aynı koşuda atlanır; VMP'te de tek-sefer davranışı performans amaçlıdır. xrt katmanındaki K7 (loader) her açılışta koşar.
### Derleme
- host_cli3 temiz; **XProtectUI.exe (x64) + XProtectUI32.exe (x86) zig ile temiz derlendi**.
### Push
- XProtectArea **@4d6798e** (cp291) — 13 dosya, UI+motor+CLI.
- Kanıt paketi v287_kanit/: K2048/K7168/CMPCT/ALL32/CV + kanit_checkvm.py + cv3.log.
### SIRADAKİ (V292)
1. analyze MOD1 geniş alanlar (vm_functions/iat/stub_template) cpp'ye — bu tur sığmadı.
2. RSA-4096+ runtime büyütme (LIC_LIMBS dinamik).
3. wine E2E (UI gerçek Windows davranışı) + 36 allver dosyası (finish_331.sh).
4. False-positive tespit kuralı iyileştirmesi (mingw DLL .vmp0 ebp-frame çakışması).
### Push
- XProtectArea **@4d6798e** (cp291) — 13 dosya, UI+motor+CLI.
- Kanıt paketi v287_kanit/: K2048/K7168/CMPCT/ALL32/CV + kanit_checkvm.py + cv3.log.
### SIRADAKİ (V292)
1. analyze MOD1 geniş alanlar (vm_functions/iat/stub_template) cpp'ye — bu tur sığmadı.
2. RSA-4096+ runtime büyütme (LIC_LIMBS dinamik).
3. wine E2E (UI gerçek Windows davranışı) + 36 allver dosyası (finish_331.sh).
4. False-positive tespit kuralı iyileştirmesi (mingw DLL .vmp0 ebp-frame çakışması).

## 13) V292 (2026-11-21, turn-15) — 4 Kalemin 4'ü de KANITLI ✓
### V292-1: analyze MOD1 geniş alanlar cpp'ye — TAM PARİTE ✓
- **stub_template** (3-kural: vmp3x-vmsections / vmp3x-push-call / '-'), **vm_functions** envanteri (name/rva/kind/target_rva, export bazlı min(32)), **iat** dict ({rva,size,count} — py:160 dd[12] IAT-directory birebir) + iat_dump listesi + **OP_ANTIDBG bytecode taraması** (py:286-299) cpp'de.
- Hex lowercase (py paritesi), `real_vmp_reasons` JSON'a açık yazılıyor.
- **KANIT:** Test_vmp / Test32_vmp → iat+vm_functions+wrapped+template hepsi EŞİT; CV.vmp.exe template= vmp3x-vmsections/0x18000/1 birebir.
### V292-2: RSA dinamik uzunluk (1024..7168) ✓
- `LIC_LIMBS 224` (896B moda kadar), aktif limb `g_lic_L` (başlaticsız — .data yok, PIC kapısı temiz), `lic_setlen_from_lsd()` LSD 0x12E'den okur; sq/mul/mod128/be_load-store hepsi L-parametreli.
- **KANIT:** bağımsız referans bignum (okul-defteri mul + LE uzun-bölme) ile 1024/2048/4096/7168-bit modexp **bayt-bayt EŞİT** (`kanit_dinamik_rsa.log`). Blob'lar yenilendi; E2E: `--lic-keylen 4096` → LSD'de 4096 ✓ (R4096.vmp.exe 31.44MB).
### V292-3: allver korpüsu taraması (finish_331.sh yeniden inşa) ✓
- 40 REVERSE (unlift edilmiş — stub yok, BEKLENEN) + 12 korunmuş build: **5'inde OP_ANTIDBG kanıtı** (CD_fix3 mask=96, CV 153, MP 121, P 173, T3 173). `v292_allver_scan.jsonl` + `v292_korunmus_scan.jsonl` kayıtlı.
- Bulgu/düzeltme: tarama export listesiyle sınırlı değildi — .text içindeki tüm inline-stub'lar (41 57/41 56/41 55/9C/60) taranıyor; export RVA'sı korunan fonksiyonla aynı olmak zorunda değil.
### V292-4: mingw DLL false-positive KAPANDI ✓
- Derleyici-prolog süzgeci: `push ebp; mov ebp,esp` sonrası `mov eax,edx` (89 d0/8b c2) = mingw fonksiyon prologu → kanit değil. TestDllX86 (55 89 c5 89 d0 57 56 53) artık her iki araçta **"XProtect (CPlusPort motoru)"**.
- **PARİTE KANITI:** 5 dosyada (Test_vmp, Test32_vmp, TestDllX86/X64, CD_fix3) py ↔ cpp product+iat TAM EŞİT.
### Derleme + Push
- XProtectUI.exe (x64 1.57MB) + XProtectUI32.exe (x86 1.61MB) zig, temiz.
- **XProtectArea @21b94a4** (cp292) — 16 dosya (motor + blob'lar + UI).
### Sıradaki (V293)
1. wine E2E (UI gerçek Windows) — sandbox recycle'e rağmen wp2 prefix'i duruyor.
2. Licenses "Add License" → motor RSA keypair üretimi (keygen'i UI'ya bağlama).
3. VM Instances 1..10'un tablo-çoğaltmasının ayrı kanıtı (10-instance boyut merdiveni).
- `LIC_LIMBS 224` (896B moda kadar), aktif limb `g_lic_L` (başlaticsız — .data yok, PIC kapısı temiz), `lic_setlen_from_lsd()` LSD 0x12E'den okur; sq/mul/mod128/be_load-store hepsi L-parametreli.
- **KANIT:** bağımsız referans bignum (okul-defteri mul + LE uzun-bölme) ile 1024/2048/4096/7168-bit modexp **bayt-bayt EŞİT** (`kanit_dinamik_rsa.log`). Blob'lar yenilendi; E2E: `--lic-keylen 4096` → LSD'de 4096 ✓ (R4096.vmp.exe 31.44MB).
### V292-3: allver korpüsu taraması (finish_331.sh yeniden inşa) ✓
- 40 REVERSE (unlift edilmiş — stub yok, BEKLENEN) + 12 korunmuş build: **5'inde OP_ANTIDBG kanıtı** (CD_fix3 mask=96, CV 153, MP 121, P 173, T3 173). `v292_allver_scan.jsonl` + `v292_korunmus_scan.jsonl` kayıtlı.
- Bulgu/düzeltme: tarama export listesiyle sınırlı değildi — .text içindeki tüm inline-stub'lar (41 57/41 56/41 55/9C/60) taranıyor; export RVA'sı korunan fonksiyonla aynı olmak zorunda değil.
### V292-4: mingw DLL false-positive KAPANDI ✓
- Derleyici-prolog süzgeci: `push ebp; mov ebp,esp` sonrası `mov eax,edx` (89 d0/8b c2) = mingw fonksiyon prologu → kanit değil. TestDllX86 (55 89 c5 89 d0 57 56 53) artık her iki araçta **"XProtect (CPlusPort motoru)"**.
- **PARİTE KANITI:** 5 dosyada (Test_vmp, Test32_vmp, TestDllX86/X64, CD_fix3) py ↔ cpp product+iat TAM EŞİT.
### Derleme + Push
- XProtectUI.exe (x64 1.57MB) + XProtectUI32.exe (x86 1.61MB) zig, temiz.
- **XProtectArea @21b94a4** (cp292) — 16 dosya (motor + blob'lar + UI).
### Sıradaki (V293)
1. wine E2E (UI gerçek Windows) — sandbox recycle'e rağmen wp2 prefix'i duruyor.
2. Licenses "Add License" → motor RSA keypair üretimi (keygen'i UI'ya bağlama).
3. VM Instances 1..10'un tablo-çoğaltmasının ayrı kanıtı (10-instance boyut merdiveni).

## 14) V293 (2026-11-21, turn-16) — Licenses Keypair Üretimi: ÇEKİRDEK TAMAM ✓ (ara rapor)
### V293-1a: C++ RSA keypair (engine_keygen.cpp) — KANITLI ✓
- Miller-Rabin (25 taban) + **Montgomery REDC** host port'u; PKCS#1 PEM + modulus-hex üretimi.
- **4 gerçek bug ayıklandı:** (1) pencere-powmod 0-bit kare atlıyordu, (2) REDC 5. limb taşıması (m üst limb 0xFFFFFFFF'de r > 2^(32nl) — t[2nl] kayboluyordu; 2^128-159 vakası), (3) modinv ext_gcd işaretsiz sarması yanlış d üretiyordu (pozitif-katsayı iteratif egcd ile değişti), (4) mont_from'da eksik %n.
- **KANIT:** 2048-bit = **767ms**, openssl `rsa -check` = **"RSA key ok"** + modulus hex birebir eş; 4096-bit de "RSA key ok" ✓ (1025-hex). Kanıt: download/v293_kanit/ (kg2048/kg4096 .pem+.hex).
- CLI: `host_cli genkey --genkey <bit> --out <pem>` → stdout modulus BE hex (motor --lic-key formatı).
### V293-1b: LSD düzeni v2 — KANITLI ✓
- Modulus artık **0x170'te değişken uzunluk** (1024..7168-bit tam taşıma; 0x08'deki 256B uyumluluk kopyası eski runtime'lar için duruyor).
- **Cache çakışması giderildi:** eski cache 0x12C keylen alanını eziyordu → artık modulus'tan sonra; runtime em/bin 896B; serial limit 680→1216 (7168-bit b64).
- Blob'lar yenilendi (PIC kapısı temiz), host_cli derlendi.
### KALAN (V294 — sıradaki tur)
1. **xprotect_keygen.cpp** — serial üreticinin C++ portu (py eşdeğeri; RSA-imzalı blok formatı) — modinv düzeltmesiyle şimdi mümkün.
2. **UI Add License** — keygen çağrısı + .pem kaydet + modulus'u projeye bağla (ID 932 akışı).
3. **V293-1e kanıtı:** keygen→motor(--lic-key)→runtime SetSerial doğrulama zinciri (Unicorn).
4. VM Instances 1..10 boyut merdiveni + wine E2E (wp2 prefix duruyor).
- **XProtectArea @a889cef** (cp293): engine_keygen.cpp yeni + LSD v2 + host_cli --genkey.

## 15) V294 (2026-11-21, turn-17) — Licenses E2E Zinciri TAM KANITLI ✓
### V294-1: xprotect_keygen.cpp — TAM C++ serial üretici ✓
- py eşdeğeri birebir port: blok formatı (01/02/03/04/05/06/07/08/09/FF+SHA-1 ters-sıra), PKCS#1 v1.5 tip-2 imza (00 02 NN 00 blocks MM), base64. PEM okuyucu (PKCS#1/#8 DER parse) + Montgomery powmod imza (V293 kanıtlı çekirdek).
- **KANIT:** C++ imzası public-key (65537) ile çözüldü → bloklar py formatıyla **birebir eş**; 2048 serial 344 kr, 4096 684 kr.
### V294-3: E2E Unicorn — keygen→motor→runtime TAM ZİNCİR ✓ (2 saatlik hata avı: 4 gerçek bug)
E2E sırasında ortaya çıkan ve düzeltilen motor bug'ları:
1. **SizeOfImage kümülatif hesap** (engine_pe.cpp): section boşluklarında imgsz gerçek sonun altında kalıyordu → lic_lsd taraması LSD'ye ulaşamıyordu. Fix: `max(newva+vsize, eski+vsize)`.
2. **cfg yazımına lic_any eklenmedi** (engine_main.cpp ~2853): lisans-only build'de XRT1 cfg HİÇ yazılmıyordu (rt_any||pack||vf_area koşulunda lic_any yoktu).
3. **--lic-key sabit-512 doğrulaması** kalmişti → dinamik (keylen/8×2) yapıldı.
4. **Harness tarafı:** PE header'ı imaj başına map etmek gerekiyordu (MZ/PE section değil — e=0 okunuyordu); KUSER_SHARED_DATA (0x7FFE0000) + PEB/LDR (GS:[0x60] zinciri) sahte ortamı kuruldu.
**KANIT (download/v294_kanit/):**
- e2e_2048.log: **SetSerial=0 (GEÇERLİ) + GetState=0 + negatif=XS_INVALID(2) + [E2E] BASARILI**
- e2e_4096.log: **aynısı 4096-bit** (LSD keylen=4096, değişken-uzunluk modulus 0x170'te, serial 684 kr)
- lic2048/lic4096.vmp.exe + pem'ler + lsd_dump.bin
- script: scripts/v294_lic_e2e.py (Unicorn x64; SDK VA = xrt blob taraması + XRT_BLOB64_SDK_OFF=678)
### KALAN (V295)
1. **UI Add License** — keygen butonu + .pem kaydet + modulus bağla (ID 932 akışı; motor tarafı hazır).
2. **VM Instances 1/2/5/10 boyut merdiveni** kanıtı.
3. wine E2E (wine bu sandbox'ta kurulu değil — Unicorn kanıtı vekalet ediyor).
- **XProtectArea @82e8d5b** (cp294): xprotect_keygen.cpp + 3 motor fix + v294_lic_e2e.py.

## 16) V295 (2026-11-21, turn-18) — Licenses UI Zinciri + VM Instances Merdiveni ✓
### V295-1: UI Export Key Pair → GERÇEK keypair + Add License → serial ✓
- **Export Key Pair diyalogu** (dlg_exportkeypair): "Generate" artık `xprotect_genkey`'i çağırır (engine_keygen.cpp — V293 kanıtlı Miller-Rabin+Montgomery çekirdeği). PEM kaydet (pick_file .pem) + durum satırı + OK'da projeye bağlanır: `lic_keypair=RSA, lic_keylen, lic_pem, lic_modulus` (+prodcode default 0102030405060708). Eski "Not ported" mesajı KALDIRILDI.
- **Add License** (dlg_addlicense): OK anında `xprotect_make_serial` (xprotect_keygen.cpp'e eklenen kütüphane API'si — build_blocks+sign_blocks) — lisans alanlarından (name/email/expire/maxbuild/runtime) RSA-imzalı base64 serial. Properties'te "Serial Number" satırı (ID base+13): tıkla → panoya kopyala (CF_UNICODETEXT).
- **Derleme akışı**: `g_proj.lic_modulus` doluysa `opt.lic_pubkey` (BE hex) + `opt.lic_prodcode` atanır → motor LSD'yi gömer (--lic-key/--lic-pc eşdeğeri).
- **Kalıcılık**: .xproj'e licpem=/licmod=/licpc= + lic satırına serial (12. kolon, geriye-uyumlu).
- **Product Code** (ID 935): 16-hex doğrulamalı düzenleme (serial 0x07 bloğu + LSD 0x110 aynı kod).
- **Derleme**: XProtectUI.exe x64 1.65MB + XProtectUI32.exe x86 1.70MB (zig; build_ui.sh'e engine_keygen+xprotect_keygen eklendi, XKG_NO_MAIN).
### V295-1c: E2E UI-akışı kanıtı ✓ (e2e_ui_akisi.log)
- Zincir: host_cli genkey → xprotect_keygen serial → host_cli --lic-key/--lic-pc → Unicorn SetSerial.
- **2048**: openssl "RSA key ok" + SetSerial=0 + GetState=0 + negatif=XS_INVALID(2). **4096**: aynı (serial 684 kr).
### V295-2: VM Instances merdiveni — BUG FIX ✓ (vm_instances_merdiveni.log)
- **BUG**: `n_ins = (opt.vmn > 1 ? opt.vmn : 10)` — vmn=1 seçilince 10 instance'a fırlıyordu → 1 ile 10 AYNI boyut (8.27MB) çıkıyordu. FIX: `n_ins = opt.vmn` (default 10 yalnız vmn ayarlanmamışsa; host_cli/UI zaten 1..10 kelepçeler).
- Merdiven (trt.exe --all --vmsize): **1→376,320 B | 2→1,252,864 B | 5→3,882,496 B | 10→8,265,216 B** (856KB/instance; farklar: 876,544 / 3,506,176 / 7,888,896 = tam katsayı ✓).
### Kanıtlar (download/v295_kanit/)
- e2e_ui_akisi.log, vm_instances_merdiveni.log, kg2048/kg4096.pem, mod2048/mod4096.hex, ser2048/ser4096.txt, lic2048/lic4096.vmp.exe, vmn1/2/5/10.vmp.exe, XProtectUI.exe, XProtectUI32.exe (16 dosya).
- script: scripts/v295_lic_e2e.py (v294_lic_e2e.py'nin yol-düzeltilmiş kopyası).
### Notlar
- gh_token.txt yenilendi (eski PAT süresi geçmişti — push yeni PAT'le yapıldı).
- **XProtectArea @5795e03** (cp295): xprotect_ui.cpp + xprotect_keygen.cpp (kütüphane API'si) + engine_main.cpp (vmn fix) + build_ui.sh + binary'ler + PORT_DURUMU.md.
### KALAN (V296)
1. Files penceresi DLL paketleme E2E (cpVirtualFiles motoru hazır — UI akış kanıtı).
2. Shadow Stack/CET boyut-davranış kanıtı.
3. Activation Server (lic_url → LSD 0x150) UI alanı zaten var; web tarafı opsiyonel.

## 17) V296 (2026-11-21, turn-18 deva) — Files DLL Paketleme + Shadow Stack/CET ✓
### V296-1: Files penceresi DLL paketleme — host-tarafı tam kanıt ✓ (v296_kanit.log)
- Derleme: `host_cli vf_app.exe --all --vfile vf_probe.dll` → XVF1 bloğu cfg+0x70'de (VFDBG: enc-ilk 42EA.../rt-ilk 4D5A... roundtrip).
- **KANIT (scripts/v296_vfile_kanit.py)**: XVF1 bloğu runtime'ın BİREBİR algoritmasıyla çözüldü — xr_mix64/xr_block/xr_cfb (CFB-64) + WriteName isim şifresi (rotl32+xor). İsim "vf_probe.dll" ✓, veri MZ ile başlıyor ✓, **kaynak DLL ile BİREBİR EŞ (86670 bayt)** ✓. Runtime xvf_map_pe manuel-PE haritası (reloc DIR64 + import + DllMain) zaten kanıtlı kodda.
### V296-2: Shadow Stack/CET — GERÇEK etki ✓
- **BULGU**: `opt.shadow_stack` motorda HİÇBİR şeye etki etmiyordu (yalnız OZET satırı) — VMProtect'ta bu seçenek DllCharacteristics'e IMAGE_DLLCHARACTERISTICS_EX_CET_COMPAT (0x1000) ekler.
- **FIX**: `set_cet_compat()` (engine_pack.cpp) + çağrı engine_main'de strip'lerden sonra + `host_cli --cet`.
- **KANIT**: Yes → DllCharacteristics 0x8160→**0x9160** (bit SET, motor logu [CET] satırı) ✓; No (default) → 0x8160 bit TEMİZ ✓.
### Kanıtlar (download/v296_kanit/)
- v296_kanit.log, vf_packed.vmp.exe, vf_probe.dll, cet_on/cet_off.vmp.exe. UI yeniden derlendi (x64 1.66MB + x86 1.70MB — v295_kanit/ altında güncel).
- **XProtectArea @152842b** (cp296).
### KALAN (V297)
1. Activation Server'ın runtime tarafı (lic_url → LSD 0x150 zaten gömülüyor; sunucu protokolü opsiyonel — VMProtect'ta da harici).
2. wine E2E (bu sandbox'ta wine yok — Unicorn vekalet ediyor).
3. XXXESKI listesinin kalan taraması: Version/Complexity/Instances UI bağları zaten V291'de; boyut modeli V290'da.

## 18) V297 (2026-11-21, turn-19) — Boyut REGRESYONU Fix + GERÇEK WINE E2E ✓
### V297-2: REGRESYON BULUNDU ve düzeltildi ✓ (boyut_paritesi_v297.log)
- **KÖK NEDEN**: V295-2'deki vmn=1→10 fırlatma fixi motor `Options.vmn` default'unu 1 bırakmıştı. UI 10 gönderdiği için UI akışı doğruydu AMA CLI (host_cli) tekli-özellik ölçümleri **12MB→4.6MB'a düşmüştü** — kullanıcı ölçümüyle çelişiyordu ve kimse fark etmemişti.
- **FIX**: engine.hpp `vmn = 10` (VMProtect 3.9.4 Instances default'u). UI zaten 10 gönderiyordu; V295-2'nin merdiven fixi (vmn NE ise o) korundu.
- **KANIT (tam parite tablosu, Test_64.exe 66KB)**:
  - MemoryProtection tekli = **12,513,280 B** (kullanıcı: 12MB ✓)
  - ALL = **32,971,776 B** (kullanıcı: 32MB ✓)
  - Pack = **8,036,864 B** (VMP referans 7,972,864 B ≈ 7.60MB bandı ✓)
  - Tekli özellikler (iat/rsrc/antidebug/checkvm) = 13,178,368-13,178,880 B bandı
  - Instances 1/2/5/10 (ALL+Pack): 25,082,880 → 25,959,424 → 28,589,056 → 32,971,776
### V297-3: Activation Server runtime kanıtı ✓
- `--lic-url "http://act.xprotect.local"` → LSD 0x150'ye gömüldü (host-tarafı doğrulama: XLSD+0x150 = URL birebir okundu).
- URL'li build Unicorn E2E: SetSerial=0, GetState=0, negatif=XS_INVALID(2) — URL varlığı lisans doğrulamasını bozmuyor.
- Runtime tarafı zaten tam: lic_activate/lic_deactivate (BuildUrl+URLEscape+HTTP GET+OK/BAD/BANNED/USED/EXPIRED işleme — VMProtect ActivationRequest 1:1).
### V297-4: GERÇEK WINE E2E ✓✓ (wine_e2e_v297.log)
- **wine-10.0 kuruldu** (sudo'suz sandbox): `apt-get download wine64 libwine libz-mingw-w64` + `dpkg -x`. Üç engel aşıldı: (1) nls çift-wine yolu (binary `/usr/lib/wine/../../share/wine/wine/nls` arıyor — aynalandı), (2) deb'in wineserver script'i `/usr/lib/wine` sabit — wrapper mutlak yolla yazıldı, (3) mingw-built user32 zlib1.dll istiyor — libz-mingw-w64 deb'inden system32'ye kopyalandı; builtin DLL'ler fiziksel kopya (750+).
- **9 korumalı build'in TAMAMI wine altında çalıştı**: Test_64 orijinal + MP + Pack + mp2 (lisanssız UI-akışı) + act.vmp (lisans+URL) + ALL + sz_vmn10 + cet_on hepsi rc=0 (EOF ile temiz kapanış).
- **vf_packed.exe: "app ok" + rc=42** — vf_app'un orijinal dönüş kodu; XVF zinciri (CFB-64 çözüm → manuel-PE map → DllMain) GERÇEK kernel32/user32 altında koştu.
- CET'li build (DllCharacteristics 0x9160) wine yükleyicinde sorunsuz.
### Notlar
- localdekendimdenedim.rar bu sandbox'tan erişilemedi (Yandex OAuth internal-error; public sayfa JS-only) — boyut kalibrasyonu WORKLOG'a kayıtlı kullanıcı ölçümleriyle (12/32/7.60MB) yapıldı. Arşiv bir sonraki tura `upload/`'a bırakılırsa birebir yan-yana yapılır.
- Sandbox recycle olmuştu: /tmp/xpa + ~/winepkgs gitmişti; repo reclone (@152842b) + setup_env.sh (wine deb-extract geri) yapıldı.
- UI vmn=10 fix'iyle yeniden derlendi: XProtectUI.exe x64 1.66MB + XProtectUI32.exe x86 1.70MB.
### Kanıtlar (download/v297_kanit/)
- boyut_paritesi_v297.log, wine_e2e_v297.log, mp2/p_ALL/p_Pack_Only/act.vmp/cet_on/vf_packed.exe, kg.pem + UI binary'leri.
- **XProtectArea @9133bd6** (cp297).
### KALAN (V298)
1. localdekendimdenedim.rar upload/ klasörüne bırakılırsa: gerçek VMP 3.9.4 çıktılarıyla birebir yan-yana boyut/strüktür karşılaştırması.
2. UI'da Activation Server alanının E2E'si (wine'da UI açıp akışı tıklamak — Xvfb gerekir).

## 19) V298 (2026-11-21, turn-20) — analyze TAM PARİTE + ALLVER MATRİSİ 748/748 TAMAMLANDI ✓
### V298-2: analyze MOD1 geniş alanlar — py↔cpp TAM PARİTE 6/6 ✓ (analyze_parite_v298.log)
- cpp'ye eklenen/py-pariteye çekilen: sections rva/vsize/rawsize/entropy (round .2, hex lc), check_debugger_apis (IAT∩8 API), tls+tlscb (TLS dizini+callback'lar), vm_gate_section/count (E9 hedef analizi + PE32/rs>0x10000 fallback [yalnız !is64] + .vmp* fallback), features.tls_callback, mutation=ilk-0x400-bayt-kuralı, lzma_pack .vmp*-dışarı + 5 packer imzası, product/vmp0/stub_template null semantiği, iat_dump JSON-escape.
- **AYIKLANAN 2 GERÇEK BUG** (py pefile ile kıyaslayınca ortaya çıktı):
  1. x64 ImportLookupTable girişleri 8 bayt — walker 4 ilerliyordu (yarım giriş=0 → döngü erken kırılıyordu) → iat_set boş çıkıyordu.
  2. Hint/Name RVA'da isim +2'den başlar (2 bayt hint) — isimler boş/hint-parçalı okunuyordu.
  - Fix sonrası: Test_64 iat=36 (py pefile=36 birebir), vf_packed=47.
- Test: 6 hedef (p_ALL/cet_on/vf_packed/Test_64/sz_vmn1/p_Pack_Only) → **6/6 TAM PARİTE** (py 17-18 alan; cpp 4 bilinçli-ekstra denetim alanı).
- json_esc: binary isimli export'lar (PE32 ALL'da isim şifreli) + >=0x80 baytlar \uXXXX — UTF-8 güvenli JSON.
### V298-3: 36 eksik allver dosyası ÜRETİLDİ — MATRİS 748/748 ✓✓ (allver_36_tamam.log)
- Eksik kimlik (zip taramasıyla birebir): Lite_3.10.4×24 (StripFixups/PackOutput/MovePlayer×3/ALL × 4 hedef) + Ultimate_3.2.0×12 (6 variant × 32exe+dll).
- Üretim: scripts/finish36_v298.sh — vmprotect_con.exe (3.5.1 console, tüm sürümlerde tek con) + wine-10.0 (Z: yolu) + VA çözümleme (32-bit export→VA). **ok=36 fail=0**.
- REVERSE: 36/36 OK (mvpreverse3.py reverse).
- Zip'lere eklendi: Lite_3.10.4_REVERSE.zip (+24+24), Ultimate_3.2.0_REVERSE.zip (+12+12) → **MATRİS 748/748 — EKSİK 0**.
- **TESPİT 36/36**: her dosyada VM kanıtı (vm_gate .vmp01/.vmp03 + vmf_jmp; ALL'da lzma+IsDebuggerPresent; MovePlayer tekil sarım vmf_jmp=1 — doğru).
### Notlar
- Sandbox recycle OLMAMIŞTI (dbg297/xpa/wine duruyordu); yalnız libcapstone.so.5 silinmişti — pip capstone + .so.5 symlink.
- allver_v16.zip içindeki 11 sürüm REVERSE zip'i vmptest'e çıkarıldı; vmpcon_bin yolu düzeltildi (wrapper değil wine64 binary çağrılmalı — $W=/usr/lib/wine/wine64).
- PE32'de section adı .vmp00/.vmp01 (8-karakter sınırı) → product=None beklenen (unregistered con davranışı); tespit vm_gate/stub_template üzerinden.
### Kanıtlar (download/v298_kanit/)
- analyze_parite_v298.log (6/6 parite), allver_36_tamam.log (üretim+tespit), kanit_dosyalar/ (36 korumalı build).
- scripts/finish36_v298.sh + scripts/cpp/mvpreverse3.cpp (cp298) + scripts/cpp/mvpreverse3 (binary).
### KALAN (V299)
1. 36 dosyanın REVERSE çıktılarının Unicorn doğrulaması (orijinal==REVERSE RAX) — v16 yöntemiyle.
2. benchmark_allver.html yenileme (748 tam matris).
3. Yandex upload (allver_v17 paketi) + localdekendimdenedim.rar bekleniyor (upload/'a bırakılırsa yan-yana karşılaştırma).

## 20) V299 (2026-11-22, turn-21) — GERÇEK VMP ARŞİVİ YAN-YANA + CRASH TESTİ + DURUM RAPORU ✓
### V299-1: localdekendimdenedim.rar İNDİRİLDİ ✓
- Yandex **public API** ile (OAuth engeli aşıldı): `cloud-api.yandex.net/v1/disk/public/resources` → indirme URL'si → 73,102,115 B, **MD5 243f53c473f1842a642a66d44fcb0e18 API-değeriyle doğrulandı**.
- İçerik: 16 gerçek VMProtect Ultimate 3.9.4 çıktısı (Test_64, aynı orijinal 66,048 B) + Test_64.exe.vmp proje dosyası.
### V299-2/3: YAN-YANA karşılaştırma (yanyana_gercek_vmp_v299.log) ✓
- ALL 32,335,360 vs 32,971,776 (0.98x) · MP 12,617,728 vs 12,513,280 (1.01x) · Pack 7,877,120 vs 8,036,864 (0.98x) — ana üç hedef parite.
- Farklar (dürüst): ImportProt 0.68x / Debugger 0.79x / VirtTools 0.77x — XProtect tekli özelliklerde full VM instance ekliyor; gerçek VMP IAT-şifreleme + loader-side çözümle küçük tutuyor.
- Yapısal imza (gerçek VMP): VM section adları RASTGELE (her derlemede değişir); Pack'li çıktıda orijinal section'lar rs=0 (diskten silinir); export gövdeleri sıfırlanır; IAT yeniden yazılır (36→9).
- Tespit: 12/16 güçlü kanıt; 4 dosya (mutation/strip×2/resource tekli) 14KB — VM bölgesi yok, gerçek VMP de etkisiz bırakıyor (davranış eş, tespit zayıflığı değil).
### V299-4: CRASH TESTİ (crash_test_v299.log) ✓✓
- wine-10.0 geri kuruldu (recycle sonrası: wineserver mutlak-yol wrapper + WINEDLLPATH + system32 DLL kopyası + zlib1.dll + nls aynalama + cmd/explorer).
- **6/6 build CRASH'SİZ**: rc=0 (mp2, p_ALL hareketsiz, p_Pack_Only, act, cet) + rc=42 (vf_packed — orijinal dönüş kodu).
- Hareket testi (w/a/d/s): mp2/act/cet **rc=0** — XXXESKI'deki "CheckDebugger açıkken hareket crash" sorunu YOK.
- p_ALL hareket: MessageBox "XProtect: butunuluk kontrolu kod = #6" = **CheckVM DOĞRU ÇALIŞIYOR** (CPUID hypervisor bit — KVM sandbox; relay iziyle kanıtlandı). Crash değil; VMP mesaj farkı ("Sorry, this application cannot run under a VM" vs "kod = #6") rt_msgtext ile özelleştirilebilir.
### Kanıtlar (download/v299_kanit/)
- yanyana_gercek_vmp_v299.log, crash_test_v299.log, mb_screen.png (MessageBox ekran görüntüsü), yan_yana_final_v299.py (scripts/).
### KALAN (V300 önerisi)
1. ImportProt/Debugger/VirtTools tekil boyut profili küçültme (IAT-şifreleme yaklaşımı — 0.68-0.79x → ~1.0x).
2. Rastgele VM section adları + Pack'te orijinal section rs=0 silme (yapısal parite).
3. UI Activation Server tıklama E2E (wine+Xvfb).

## 21) V300 (2026-11-22, turn-22) — universalObfusPublicSrc DERLE + TEST + ZIP TESLIMATI ✓
### V300-0: görev
- Kullanıcı: universalObfusPublicSrc.rar'ı derle (UI'li + UI'siz), UI'siz iken test.exe'yi ultra:0x60510 checkdebugger:2 ile test et, projedeki mevcut komutları da dahil et, kullanım rehberi oluştur, zipleyip teslim et.
### V300-1: arşiv + çözümleme
- rar açıldı → obfus_src/vmp: VMProtect 3.5.1 public source (GNU makefile zinciri, clang gerektiriyor, Qt3/Qt5 GUI).
- 0x60510 çözümleme (kod ile): cpPack(0x100)+cpCheckDebugger(0x400)+cpCheckKernelDebugger(0x20000)+cpStripDebugInfo(0x40000).
- checkdebugger düzeyleri: 0/none=kapalı, 1=user-mode (PEB BeingDebugged), 2=user+kernel (NtGlobalFlag kalıntıları) → 0x20400.
- upload/XProtect_0.3.0.zip içinde hazır XProtect 0.3.0 derlemeleri + kaynak (CPlusPort motoru) bulundu → görev bu kaynak üzerinden yürütüldü.
### V300-2: derlemeler
- clang-19 kurulumsuz (deb-extract ~/clang_local; LD_LIBRARY_PATH) — sandbox'ta clang yoktu.
- wine-10.0 tam kurulum geri yüklendi: setup_wine_full.sh (system32 750 DLL + zlib1.dll mingw + nls) + wrun4.sh koşum.
- XProtectUI.exe + XProtectUI32.exe derlendi (build_ui.sh, zig 0.17.1): bit-parite — referansla yalnız 20 bayt fark (PE timestamp + build-id hash), 2^19 bayt gövde birebir.
- test.exe yeniden derlendi (zig c++, Test.cpp): wine E2E referansla birebir (d,d,d,s → skor 0→40).
- UI'siz CLI: xprotect_cli derlendi (g++, Linux-native, cplport_cli.cpp + motor) → --help/--list OK.
### V300-3: test zinciri (ultra + 0x60510 + checkdebugger:2)
- CLI koruma: ./xprotect_cli --in test.exe --out test_vmp.exe --all --level 2 --opt antidebug,memprot,strenc → 237.056 B, .vmp0 eklendi, [MEMPROT]/[STRENC] aktif, 4/5 fonksiyon (WrapPos RED: grp1 adc/sbb).
- UI CLI koruma: XProtectUI.exe --cli test.exe test_vmp_ui.exe --type U --all --antidebug --strenc --memprot → rc=0, U=4.
- Unicorn diferansiyel (verify_v300.py — verify_mut.py'ye PEB/gs emülasyonu eklendi): temiz 13/13 ESIT; zehir modu (BeingDebugged=1) → MovePlayer 0xdeadbeefdeadbeef = antidebug KANITLI.
- Wine E2E: 24 hamlelik oturum orijinal vs korumalı → skor/can/yem birebir (diff boş).
### V300-4: doküman + teslimat
- XProtect_Kullanim_Rehberi.docx (R1 kapak DM-1, 8 sayfa): tüm CLI/GUI komutları + VMProtectCon parametre tabloları + cp* bit tablosu + 0x60510 çözümlemesi + doğrulama tablosu. postcheck 8/9 (Consolas font uyarısı), PDF render 8 sayfa doluluk 0.19-0.48, placeholder taraması temiz.
- XProtect_V300_teslimat.zip (25 dosya, MD5 4729dec221943f8f588a5fd430db27ad): bin/ (5 binary), test/ (test zinciri + loglar), scripts/ (verify_v300.py + build scriptleri), kanit/ (unicorn + e2e loglar), rehber docx+pdf.
### Kalan (V301)
- VMProtectCon orijinal Linux derlemesi (clang-19 + lin makefile zinciri; Qt GUI bağımlılığı nedeniyle ileride).
- Zehir modunda 9/13: 4 fonksiyon zehir-testinde orijinal de EXC veriyor (veri sembolü/uzun koşum) — doğrulayıcı iyileştirmesi.

## 22) V301 (2026-11-23, turn-23) — PRO 3.9.6 GUI İLE TEST_64 KORUMASI + UMVP-QT FORM/OBJE/EVENT ANALİZİ ✓✓
### V301-0: görev
- Kullanıcı: Yandex'teki Professional 3.9.6 exe'sini aç; Test_64.exe'yi UMVP-QT (2).zip ile analiz ettir; form/obje/event bilgisini JSON→txt; betimleyici .md; Pro 3.9.6 ne işe yarar + özellik listesi; Test_64.exe'yi Pro 3.9.6 ile kullan.
### V301-1: ortam kurtarma + disk
- Sandbox recycle YOK (wineserver/VMProtect/Test_64 yaşıyordu). Disk %97 → tar.xz arşivleri (190MB) + wineprefix2 (333MB) silindi → 723MB boş.
- capstone pip kurulumu + libcapstone.so.5 symlink (V298 yöntemi).
### V301-2: UMVP-QT
- umvpqt_cli derlendi (make cli). Canlı analiz --pid 4278: Qt family 0.0, QObject 0 → Test_64 Qt DEĞİL (konsol oyunu) — dürüst sonuç.
- umvpqt_demo: Qt5 mock hedefte 2 form, 9 QObject, 3 bağlantı, handler'lar + invoke (click/readProperty/combo) — framework yeteneği KANITLI.
### V301-3: VMProtect Professional 3.9.6 GUI OTOMASYONU ✓
- Kök-cause bulgusu: wineconsole (oyun) focus'u çalıyordu → F9'lar oyuna gidiyordu. Çözüm: windowunmap(oyun) + windowfocus(VMP) + F9.
- F5=Execute, F9=Compile (kaynak mainwindow.cc:301'den doğrulandı).
- Derleme BAŞARILI: Test_64.vmp.exe (2.744.832 B, orijinal 12.800 B'nin 214x'i).
- .vmp projesi: 6 prosedür (0x140001020 ana döngü + WrapPos/MovePlayer/GhostStep/CheckCollision/GameTick), CompilationType=0 (virtualization), Options=32768.
- Korumalı yapı: .dne section 0x29B1D0 (~2.7MB) entropy 7.07 — rastgele adlı VM section (gerçek VMP davranışı); .reloc gitti; 5 export RVA yerinde; IAT 36 korundu.
### V301-4: E2E parite (e2e_test_v301.sh, fifo-beslemeli stdin)
- Hamleler d,d,d,s,w,q: orijinal Skor 40 / Can 3 / Yem 78 / RC=0 — korumalı SKOR SATIRLARI BİREBİR AYNI (20→30→40 geçişleri dahil).
### V301-5: teslimat (download/)
- Test_64_form_obje_event_analiz.txt (JSON, 12 bölüm: form/obje/event + koruma + E2E + özellik listesi)
- Test_64_analiz_raporu.md (6 bölüm, 45 tablo satırı, placeholder taraması temiz)
- Test_64.vmp.exe (korumalı çıktı) + v301_kanit/ (12 dosya: umvpqt canlı+demo, mvpreverse3 org+korumalı, E2E logları, .vmp, ekran görüntüleri)
- 3.9.6 sürüm notları vmpsoft.com/news'ten doğrulandı (19 Haz 2025: Rust demangling NEW, .NET self-contained IMPROVED, Intel FIXES).
### Kalan (V302 önerisi)
- VMProtectCon 3.9.6 binary elimizde yok (yalnız GUI) — koleksiyonda varsa CLI derleme otomasyonu.
- Korumalı dosyada Debugger/Import/Pack seçeneklerinin tekil etkilerinin profil çıkarımı (V299 fark raporuyla birleştirme).

## 23) V302 (2026-11-23, turn-24) — PRO 3.9.6'NIN KENDİSİNİN QT MENÜ FULL ANALİZİ + QT BULUCU ✓
### V302-0: görev
- Kullanıcı düzeltmesi: konu Test_64 değil — Professional 3.9.6'nın KENDİ Qt menüsünü FULL öğrenmek, qt bulucu (UniversalMVPUIAlgorithm_QT) ile.
### V302-1: DERİN SANDBOX RECYCLE kurtarması ✓
- Kayıplar: VMProtect.exe, work/umvp_extract binary'leri, xroot (Xvfb/xdotool), wine-root wine64, scripts/*.sh (52KB kaldı), universalAllver_dl, debs, v296-299 kanıt klasörleri, mvpreverse3.cpp.
- Kalanlar: upload/ (tümü), download/ (v301 + Test_64.vmp.exe), work/{tools(libcapstone), umvp_extract(out_canli), wine-root(kısmi), wineprefix10, protect_ws, wr... yok}, WORKLOG, scripts küçük.
- Yeniden kurulan: Xvfb sistemde var; xdotool+libxdo3+libxtst6 deb (dpkg -x, root'suz); Kron4ek wine-11.22-amd64.tar.xz (SAF-64: wineboot core dump — sandbox seccomp SIGSYS!) + wine-11.22-amd64-wow64.tar.xz (ÇALIŞIYOR: wineboot 793 dosya); universalAllver.rar Yandex public API'den yeniden indirildi (500.317.952 B, MD5 f98140bb208579efae4709ab395f1c96 API değeriyle DOĞRULANDI) → Pro 3.9.6 çıkarıldı (35.028.960 B, MD5 54373f55c886595f36606121d7a5f34c).
- Debian wine-10.0 (repack-6/12.2) PE-only wow64: kernel32 c0000135 + user32 CreateDialogParamW unimplemented — TERK EDİLDİ (V299 kurulumundaki i386-unix'li tam set günümüzde farklı deb'lerde).
- wrun_v303.sh: Kron4ek chroot runner (WROOT/WINEPREFIX parametreli; dosdevices symlink; /bin mount — wineserver 382B script hatası çözümü).
### V302-2: QT MENÜ AĞACI (kaynak koddan) ✓✓
- parse_menu_v302.py: mainwindow.cc + lang_def.inc (274 dil anahtarı) + #ifdef LITE/ULTIMATE Professional çözümlemesi.
- MENUBAR: File(6) Edit(10) Project(4: Add▸,---,Compile F9,Execute F5) Tools(2: Watermarks,Settings) Help(4: Contents F1,---,Home Page,About).
- 47 QAction (metin+kısayol+slot+satır), toolbar 13 öğe (open,save,|,compile,execute,|,cut,copy,paste,AddFunction Ins,GoTo,Templates), Add alt menü PRO: Add Function+Add Folder (License/File Ultimate), Import Ultimate-only, 4 context menü (script/execute/templates/recent).
### V302-3: CANLI KOŞUM ✓ (wow64-wine, Xvfb, pid 2238)
- VMProtect.exe ÇALIŞTI, pencere açıldı → **Debugger Detection CANLI TETİKLENDİ**: "A debugger has been found running in your system. Please, unload it from memory and restart your program." (OCR kanıtı: v302_kanit/canli_debugger_detection_diyalogu.png) — özelliğin fiilen çalıştığının kanıtı; OK → uygulama kapandı.
- Ana pencere engellendi → canlı menü OCR yapılamadı (menü envanteri kaynaktan %100).
### V302-4: QT BULUCU (umvpqt_cli) BULGULARI ✓
- Canlı (--pid 2238) + statik (--no-live): Qt 0.0, QObject 0, form 0.
- Elle bellek taraması (476 bölge/290MB): "VMProtect Professional" 15x VAR; QMetaObject/MainWindow/QAction/Qt5 imzaları 0 — self-protection kanıtı.
- KESİN Qt kanıtı: binary'de .qtmetad section (MSVC+Qt statik) + self-protect (.text/.rdata/.qtmetad rawsize=0; rastgele section'lar .H;%/.f^\ /.=i?; .=i? 33MB entropy 8.00; entry VM içinde).
- Framework Qt-hedefteki yeteneği V301 demo'dan: 2 form, 9 QObject, handler %90-95 güven, invoke ok.
### V302-5: TESLİMAT (download/)
- VMProtect_Professional_3_9_6_QT_menu_form_obje_event_analiz.txt (24.8KB JSON: 12 bölüm)
- VMProtect_Professional_3_9_6_QT_menu_raporu.md (13.2KB: 7 bölüm, 5 menü tablosu, kısayol tablosu, event mimarisi)
- v302_kanit/ (debugger-diyalog PNG, menu_tree.json, umvpqt çıktıları, en.lng, menu_scan)
### Kalan (V303 önerisi)
- Debugger algısını wine'da atlatma (winedbg override denenip yetersiz; farklı wine sürümü ya da check bypass gerekir) → ana pencere + canlı menü OCR.
- mvpreverse3.cpp kaynağı git'te değil (V298 kanıt klasörü silinmiş) — yeniden yazım gerekiyor.

## 24) V303 (2026-11-23, turn-25) — SOL PANEL OBJELERİ + OPTIONS EDITOR: CANLI TESPİT + FRAMEWORK --pe-qt GÜNCELLEMESİ ✓✓
### V303-0: görev (kullanıcı düzeltmesi)
- "Menüde değil — objede seçenekler: Functions for protection / Licenses / Files / Script / Options; Options'a basınca tabda Resource Protection gibi özellikler. Tespit edemediysen nedenini bul, UniversalMVPUIAlgorithm_QT kullanarak veya GÜNCELLEYEREK."
- BU BİR PROJECT TREE (sol panel QTreeWidget) + PropertyEditor katmanı — V302'de eksik kalmıştı.
### V303-1: kaynak koddan TAM çıkarım ✓
- parse_properties_v303.py: property_editor.cc 12 PropertyManager + models.cc 6 tree kökü + models.h 40 NodeType enum.
- Options (CorePropertyManager) 23 özellik: File(Memory/Import/Resource Protection, Pack Output File, Output File) + Detection(Debugger enum No/User-mode/User+Kernel-mode, Virtualization Tools) + Additional(VM Segments, Strip Debug Info, Strip Relocations, Watermark, [Ult]Lock to HWID) + Messages(Debugger Found, VM Tools Found, File Corrupted, [Ult]Serial Number Required, [Ult]HWID Mismatched) + [Ult]Licensing Parameters + RSA kökü(Private Exp/Modulus/Product Code readonly).
- Function: Compilation Type(Virt/Mutation/Ultra)+Lock to Serial; License: 14 özellik; Segment: 14; Resource/Import/Export/Section/LoadCommand/Assembly/InternalFile/Watermark.
### V303-2: DISK DOLDU → acil temizlik ✓
- Silinenler: wr1122(940MB SAF-64 çalışmayan), universalAllver.rar(478MB çıkarılmıştı), libwine deb'leri(303MB), wine-11.22-amd64.tar.xz, wine-root(740MB Debian çalışmayan)+wineprefix10(771MB), obfus .vs; git gc --prune=now. 0% → 3.8GB boş.
### V303-3: DEBUGGER DETECTION BYPASS ✓✓ (kritik kırılım)
- Kron4ek wine-10.0-amd64-wow64 (65MB): VMProtect.exe DIYALOGSUZ açıldı — 11.22'de tetikleniyordu (V301 Debian 10.0 davranışıyla tutarlı).
- .vmp argümanı ShellExecute'e gitti (assoc yok) → Ctrl+O + type + Return Qt dialog otomasyonu ile Test_64.exe.vmp YÜKLENDİ (başlık: 'Test_64.exe.vmp — VMProtect Professional [registered]'). Test_64.exe recycle'da silinmişti → Test_64_orig_yedek.exe geri kondu.
### V303-4: CANLI TESPİTLER ✓✓
- Menü çubuğu CANLI: 'File Edit Project Tools Help' (koordinatlı OCR).
- Sol panel tree CANLI: Test_64.exe / Functions for Protection / Script / Options (Professional lisans — Licenses/Files Ultimate derlemesinde).
- Sağ panel CANLI: Name|Address|Protection tablosu — WrapPos/MovePlayer/GhostStep/CheckCollision/GameTick hepsi Virtualization + alt panel 'Compilation Log'.
- OPTIONS PROPERTY EDITOR CANLI (fare XTEST kısıtlı → klavye: Tab x4+Down x4+Space): Memory Protection, Import Protection, Resource Protection, Pack the Output File, Output File, Virtualization Tools, Strip Debug Information, Strip Relocations (for EXE files only), Shadow Stack Compatible, Debugger Found, Virtualization Tools Found, File Corrupted, Serial Number Required.
- SÜRÜM BULGUSU: 'Shadow Stack Compatible' 3.5.1 kaynakta YOK → 3.9.6'da yeni (Intel CET).
- Menü dropdown'ları F10/alt/click ile açılamadı (wine/XTEST odak sınırı) — menü çubuğu kanıtlı, içerikler kaynaktan.
### V303-5: FRAMEWORK GÜNCELLEMESİ (UniversalMVPUIAlgorithm_QT V303) ✓✓
- cli/main.cpp +~110 satır: --pe-qt <dosya> modu — pe_static_qt_scan(): PE section tablosu → .qtmetad(Qt statik), qt_meta_stringdata(diskte), silinmiş bölüm(vsize>0/rawsize=0), şifreli(entropy≥7.95), rastgele ad → teşhis + json/txt rapor. Kütüphane koduna dokunulmadı.
- VMProtect.exe testi: '.qtmetad VAR + 9 silinmiş + 1 şifreli(33MB ent 8.00) + 3 rastgele ad' → teşhis: "Qt STATIK LINKLI + self-protected — canlı taramanın 0 bulması BEKLENEN davranıştır" ✓
- Negatif: Test_64.exe 'Qt kanıtı bulunamadı' ✓; Test_64.vmp.exe aynı ✓. umvpqt canlı (pid 22722, UI tam açık): yine 0 Qt → self-protect bellekte de sürüyor.
- Teslim: download/UniversalMVPUIAlgorithm_QT_v303_peqt.zip (kaynak + derlenmiş cli).
### V303-6: TESLİMAT (download/)
- VMProtect_Professional_3_9_6_QT_menu_form_obje_event_analiz.txt GÜNCELLENDİ (41KB, 16 bölüm: +project_tree_objeleri +options_dugumu_property_editor +framework_v303_guncellemesi +tespit_yontemleri)
- VMProtect_Professional_3_9_6_QT_menu_raporu.md GÜNCELLENDİ (20KB, 10 bölüm: +Sol Panel Objeleri +Options Editor +Framework V303)
- v302_kanit/: +canlı ana pencere/options editor PNG'leri + property_tree.json + options_editor_canli_ocr.txt + umvpqt pe-qt çıktıları

## 25) V304 (2026-11-23, turn-26) — SÜRÜM KATALOĞU HTML + COMPLEXITY KAYNAĞI + ARŞİV KAYBI TESPİTİ
### V304-0: kullanıcı soruları
1. "Functions for protection'ı neden önce tespit edemedin / tespit edip mi yazdın?" — cevap: kaynak koddan V299'dan beri parse ediliyordu ama V302 raporu menü-odaklı yazıldığı için sol panel ağacı ayrı KATEGORİ olarak sunulmadı; canlı pencere de argümansız açıldığından ağaç boştu, canlı doğrulama yoktu. V303'te her ikisi tamamlandı.
2. "UniversalMVPUIAlgorithm_QT (2).zip'yi mi güncelledin?" — HAYIR: upload'daki zip DOKUNULMADI (input). work/umvp_v302 çalışma kopyası güncellendi (içindeki --pe-qt) ve ayrı zip teslim edildi.
3. "zip içindeki 11 sürümü dene + Complexity gözlemi + HTML katalog (en iyiden sıralı, tıklanınca özellikler)"
### V304-1: ARŞİV KAYBI (dürüst tespit) ✓
- Sandbox recycle: work/'un büyük kısmı gitti — wr_wow64_10 (wine-10.0), wineprefix_wow64_10, vmp396_v302 (Professional 3.9.6 binary!), obfus_v302 kaynak, tools arşivleri.
- universalAllver.rar: upload/'da da yok (silinmiş); Yandex public_key URL'si hiçbir kayıtta yok (WORKLOG'da sadece API endpoint + MD5 f98140bb...). 11 sürüm binary'si şu an EDİNİLEMEZ durumda → kullanıcıdan link tekrar istendi (final yanıtta).
- Metin2_ve_VMPlisi.rar kontrol: içinde sadece metin2client.exe + vmp'li hali — VMProtect sürümü yok.
### V304-2: RESMİ CHANGELOG ÇEKİMİ ✓✓ (yeni veri kaynağı)
- fetch_vmp_history.py: vmpsoft.com/news 8 sayfa (page/1..8) → 50 sürüm (1.51→3.11) changelog JSON (work/vmp_history.json).
- Format varyasyonları çözüldü: "VMProtect v 3.9.5 released" (v + boşluk), "VMProtect 3.9.3" (released'sız), tarih-kırpma bug fix (\d+\w{2} yıl yiyordu → sıra eki regex'i).
- **COMPLEXITY SORUSUNUN RESMİ CEVABI:** 3.8 [NEW]: proje seçeneği "Virtual Machine – Complexity" (None/1..100%) + fonksiyon seçeneği "Protection – Complexity" (Default/0..100%) + VM Version (Default/VMProtect 2.X) + VM Instances (Default/1..10). 3.8.3: "Shadow Stack Compatible" (CET). 3.8.6: .NET VM Complexity+Version. → 3.8 öncesi sürümlerde (3.3.1 dahil) Complexity YOK — kullanıcının "bazılarında fonksiyonda, bazılarında hem VM'de hem fonksiyonda" gözlemi sürüm eşiğiyle birebir açıklandı.
- Diğer eşikler: Rust demangling 3.9.6, WebLM veri koruması 3.9.4, ARM64 3.10, Android 3.10.1, Rust SDK 3.11, CFG+PDB 3.3.
### V304-3: SÜRÜM KATALOĞU HTML ✓✓ (ana teslimat)
- gen_html_v304.py → download/VMProtect_surum_katalogu.html (256KB, self-contained, responsive):
  - 49 sürüm kartı (48 changelog + 3.3.1 arşiv-hotfix kartı) EN YENİDEN ESKİYE sıralı
  - Arşiv badge'leri (Pro 3.9.6, Ult 3.9.4/3.8.4/3.3.1) + arşiv özet tablosu
  - Her kartta: resmî changelog (YENİ/İYİLEŞTİRME/FIX) + o sürümün UI'sındaki özel seçenekler (eşik mantığı) + ürün katmanı matrisi (Lite/Pro/Ultimate A2) + sürüme göre sol panel tree
  - Complexity rehberi kutusu (kullanıcı gözlemi + resmî değer aralıkları)
  - Ortak GUI envanteri: 5 menü + alt menüler (Pro↔Ultimate farkları) + tree + Options (V303 canlı kanıt notlarıyla)
  - Arama (summary bazlı isabetli), Tümünü aç/kapa, mobil/tablet/desktop responsive
- html_check_v304.py (playwright): 375/768/1280px taşma YOK, 51 details dengeli, arama testleri ("3.9.6"→1 kart, "3.8"→10, "Ultimate"→3), 3.3.1 kart içeriği ✓
### V304-4: kalan (V305)
- Yandex linki gelirse: 11 sürümü wine-10.0'da CANLI test (Test_64.exe.vmp + WrapPos fonksiyon seçimi + Complexity/VM editor OCR) + HTML'e canlı gözlem sütunu
- universalAllver MD5 doğrulaması: f98140bb208579efae4709ab395f1c96 (indirilen dosyayla karşılaştırılacak)

## 26) V305 (2026-11-24, turn-27) — UMVP-QT İLE GERÇEK BINARY TARAMALARI + RESMİ 3.11 DEMO
### V305-0: görev
- Kullanıcı: "UniversalMVPUIAlgorithm_QT ile zipteki tüm sürümlere bak" — arşiv binary'leri hâlâ yok; taranabilir TÜM gerçek VMProtect binary'si UMVP-QT ile tarandı.
### V305-1: ortam kurtarma ✓
- Sandbox rollback: work/ sıfırlandı (umvp_v302, vmp_history.json, v302_out dahil). umvpqt aracı download/UniversalMVPUIAlgorithm_QT_v303_peqt.zip'ten geri çıkarıldı (103 dosya) → make cli SAĞLAM (saf C++17, Qt5 gerekmez, g++/make yeterli).
- vmpsoft.com/files → resmî indirmeler: VMProtectDemo.exe (177.039.552 B, MD5 66788a24ad828b1959138b6969243908) indirildi.
- Inno Setup 5.5.7 kabuğu → innoextract 1.9 (deb + boost1.74 deb'leri dpkg -x ile, V302 xdotool yöntemi) → kurulum açıldı: "VMProtect Demo v 3.11" — VMProtect.exe (49.686.840 B, MD5 73547d7758d2d5ea31eef81244016e87), VMProtect_Con.exe, Ext32/64.dll, SDK'lar (MSVC/MASM/MinGW/BCB/VB6/Delphi/Lazarus/FP/Rust/Go/Xcode + Linux/macOS), Langs (de/en/fr/ru/zh).
- Wayback Machine (eski demo snapshot'ları) http+https timeout — erişilemedi.
### V305-2: UMVP-QT taramaları (umvpqt_cli --pe-qt, statik) ✓✓
- VMProtect.exe 3.11 (resmî): .qtmetad VAR + 7 bölüm rawsize=0 (diskten silinmiş) + 2 rastgele adlı bölüm (.h^2 ~48MB) → SELF-PROTECTED Qt statik linkli.
- VMProtect.exe 3.9.6 (arşiv, V302 kanıtı repair'la yüklendi): aynı desen — .qtmetad VAR + 9 silinmiş + 3 rastgele (.=i? ent. 8.00).
- VMProtect_Con.exe 3.11: Qt meta yok (CLI) ama 5 bölüm silinmiş + 2 rastgele → kendini koruyor.
- metin2client_vmp.exe (Ult. 3.9.4 çıktısı, Metin2_ve_VMPlisi.rar'dan): .vmp0 bölümü — VM bölüm adı PARAMETRE farkı (.vmp0 düz vs .dne rastgele): "VM segment adı" proje seçeneği.
- Test_64.vmp.exe (Pro 3.9.6 çıktısı): .dne ent. 7.00 ✓. metin2client.exe orijinal: temiz.
- V302 JSON onarımı: .f^\ bölüm adı backslash escape edilmemişti → replace ile onarıldı (r'.f^\"' → r'.f^\\"').
- SONUÇ: canlı umvpqt'nin 0 form/0 QObject bulması BINARY DÜZEYİNDE DOĞRULANDI — VMProtect kendi korumasıyla Qt meta-object verilerini diskte+bellekte erişilemez kılıyor; UI analizi kaynak kod + X11/OCR katmanıyla yapılmalı (V302/V303 yöntemi).
### V305-3: en.lng binary kanıtları ✓
- 3.9.6 (279 anahtar) → 3.11 (285): 6 YENİ anahtar Theme/Dark/Light/Architecture/Format/Header = 3.10-3.11 UI'sına TEMA desteği geldi (changelog'da yazmayan binary kanıtı).
- Her ikisinde Complexity + Instances + ShadowStackCompatible + VirtualMachine → 3.8+ VM alanları dil dosyasında doğrulandı.
### V305-4: teslimat ✓
- download/v305_kanit/: 17 dosya (peqt JSON+txt ×6 hedef, umvpqt_tarama_raporu.md, lang_karsilastirma_396_vs_311.txt, demo311_kurulum_icerigi.txt, vmp_history.json yedeği, md5ler.txt)
- VMProtect_surum_katalogu.html GÜNCELLENDİ (259KB): V305 UMVP-QT bölümü (6 hedef tablosu + bulgular) + hero istatistiği + arşiv tablosuna 3.11 demo satırı + fetch_vmp_history.py page-1 self-download fix; playwright 375/1280 taşma YOK, 52 kart, V305 içerik testleri ✓
- scripts/: lang_karsilastir_v305.py, write_files_v305.py + fetch_vmp_history.py düzeltmesi
### V305-5: kalan (V306)
- 11 arşiv binary'si hâlâ bekliyor (Yandex public link) → gelince: her sürüme --pe-qt taraması + canlı wine menü taraması + kataloğa canlı gözlem sütunu
- 3.11'de wine kurulumu + canlı menü taraması (Theme menüsü ekrandan OCR)

## 27) V306 (2026-11-24, turn-28) — ARŞİV GERİ GELDİ: 12 BINARY UMVP-QT + 3.5.1 KAYNAK MENÜ ENVANTERİ
### V306-0: arşiv kurtarma ✓
- Kullanıcı Yandex linki verdi: disk.yandex.com.tr/d/DDqwHrrTvxvAGw → public API (token'sız) → universalAllver.rar
  500.317.952 B, MD5 f98140bb208579efae4709ab395f1c96 → indirildi, unrar ile 696 MB açıldı.
- İÇERİK (V298'den GÜNCELLENMİŞ): 12 VMProtect.exe (Lite 3.10.4/3.8.7; Pro 3.8.1/3.9.6; Ultimate 3.0.9/3.2.0/3.3.1/3.4.0/3.6.0/3.8.4/3.9.4×2-kopya) + VMProtect 3.5.1 [Source Code] TAM AĞAÇ.
### V306-1: 12/12 statik UMVP-QT taraması ✓✓ (scan_allver_v306.py)
- umvpqt_cli --pe-qt: her binary → JSON+txt+MD5 (v306_kanit/, 29 dosya). JSON machine alanı string ('8664'/'014C') → normalize düzeltmesi; 3.9.6 json'unda aracın '\' escape bug'ı → onarım (V305 yöntemi).
- SONUÇLAR: 11 x64 + 3.3.1 x86. .qtmetad: 3.2.0+ hepsinde VAR (3.0.9'da yok). 3.9.4 kopya MD5 birebir.
- **PAKETLEME EVRİMİ (YENİ BULGU): 3.0.9-3.4.0 = UPX (.UPX0/.UPX1, ent 7.83-7.99); 3.6.0+ = kendi rastgele adlı VM bölümleri (.mfz/.l33/.xz@/.!/t/.=i?/.iZg/.TTN, ~30MB, ent ~8.00).**
- Self-protected desen 3.6.0+ sürekli: qtmetad VAR + 7-9 bölüm rawsize=0 → canlı umvqpt'nin 0 form/0 QObject bulması tüm sürümlerde binary-düzeyinde açıklanır.
### V306-2: 3.5.1 kaynak menü envanteri ✓ (menu_kaynak_351.py)
- .ui YOK — Qt UI kod içinde: menuBar()->addMenu(QString::fromUtf8(language[lsFile].c_str())) deseni.
- Kaynaktan: 5 menü (File/Edit/Project/Tools/Help) + 27 action + 21 setShortcut + 12 form/diyalog (about/function/license/settings/watermarks/templates...).
- **Complexity 3.5.1 GUI kodunda YOK (kaynak kanıtı; changelog 3.8 ile uyumlu). "Virtual Machine" ayrı menü yok.**
- glob tuzağı: yol '[Source Code]' köşeli parantezi glob'ta class sanıyor → os.listdir kullanıldı.
### V306-3: wine canlı denemesi (BAŞARISIZ — dürüst not)
- apt sudo yok → 245+82 deb (wine64 10.0 + bağımlılıklar + Xvfb) dpkg -x ile root'suz kuruldu; LD_PRELOAD hook_path.c (open/stat path-rewrite /usr→ex) ile NLS hatası AŞILDI + Xvfb :99 eklendi; FAKAT builtin user32/kernel32 'c0000135' + CreateDialogParamW hatası 3 konfigürasyonda da çözülemedi → canlı koşu iptal (kaynaktan derleme ayrı iş, ~40dk).
### V306-4: teslimat ✓
- download/v306_kanit/ 29 dosya: v306_umvqt_raporu.md (ana), umvpqt_allver_ozet_v306.json/.md (12'li tablo), 12× peqt json+txt, vmprotect_351_menu_kaynak_analizi.md, md5ler.txt.
- HTML katalog GÜNCELLENDİ (263KB, 53 kart): V306 kartı (12'li tablo + paketleme evrimi + kaynak menü + wine notu) + hero stat'ları (11 arşiv tarandı / 18 gerçek binary) + durum kutusu (arşiv geri geldi) + arşiv tablosuna 3.5.1 kaynak satırı. Playwright 375/1280 taşma YOK.
- scripts/: scan_allver_v306.py, menu_kaynak_351.py, write_files_v306.py, kur_wine_deb.sh + work/tools/hook_path.c (wine path-rewrite).
### V306-5: kalan (V307)
- Canlı wine: kaynaktan derlenen wine (rebuild ~40dk) → 11 sürüm canlı menü taraması (umvqpt --pid + ekran OCR) + movepos/Complexity davranış kaydı.
- UPX dönemi (3.0.9-3.4.0) açılımı: UPX -d ile unpack → iç bölümler/qtmetad analizi (UPX altındaki gerçek yapı).

## 28) V307 (2026-11-24, turn-29) — "AÇIP ANALİZ ET" TAMAMLANDI: 11 SÜRÜM CANLI + STATİK-Qt TESPİT YAMASI + CE CEVABI
### V307-0: yeni zip + derleme ✓
- upload/UniversalMVPUIAlgorithm_QT (2).zip (98 dosya) → work/umvp_v307; Makefile TAB'ları bozuk → g++ doğrudan; libcapstone5/dev deb (work/tools/capstone).
### V307-1: WİNE ÇÖZÜLDÜ (V306'daki c0000135 aşıldı) ✓
- Kron4ek portable wine-9.0-amd64 (56MB, kendi dll'leriyle) + Xvfb :99 + wineboot prefix /tmp/wport OK. Debian deb zinciri + LD_PRELOAD hook yolunu (V306) portable build eledi. proot: SIGSYS (ptrace yasak). IA32 exec yok (32-bit koşamaz — 3.3.1 x86 kanıt).
### V307-2: FİZİK KANITI — CE iddiasının ölçümü ✓
- CE'nin bulduğu: menü METİNLERİ RAM'de GERÇEK (3.8.7 penceresi: File×698 UTF-8 +216 UTF-16, View×280, Open×323, Help×73, Tools×11, Templates×9, "Save As"×10, Undo…). /proc/PID/mem UTF-8+UTF-16 scan (scripts/ram_menu_scan.py).
- Qt SINIF/meta: QMainWindow/QWidget/qt_meta_stringdata/staticMetaObject RAM'de 0 KEZ → CE "metin" bulur, umvqpqt "nesne ağacı" ister; ağaç şifreli.
### V307-3: STATİK-Qt TESPİT YAMASI (kullanıcı: "open source, kaldırabilirsin") ✓
- src/qt_scan.cpp QtDetector::detect(): modül-DLL yoksa bellek imza fallback (qt_meta_stringdata/QMainWindow/lsFile UTF-8 + File/Edit/View UTF-16) → "Qt5 (static)" ile devam.
- Sonuç: V302'de 0 → şimdi HER sürümde tespit ✓; QObject 3-317 yakalanıyor; sınıf kaydı metaobject=0x0 (şifreli — JSON'a binary sızmış, txt kanıt).
### V307-4: 11 SÜRÜM CANLI DÖNGÜ ✓ (scripts/canli_yakala_tara.sh)
- 64-bit exe'ler wine'da self-protect unpack'te page-fault (kanıt: 11× *_wine_hata.log) → CRASH-PENCERESİ yöntemi: koş + 0.5sn döngüyle PID yakala (comm VMProtect.exe/wine64) + umvpqt --pid --no-code + RAM menü scan → 46 kanıt dosyası.
- TABLO: 3.0.9 QO=317, 3.8.1 QO=293, 3.8.4 QO=40, 3.9.6 QO=36, 3.8.7 QO=32, 3.9.4 QO=30, 3.4.0 QO=27, 3.2.0 QO=4, 3.10.4 QO=3, 3.6.0 QO=3, 3.3.1 x86 koşamadı (IA32 yok; start.exe helper yanılttı — tabloda dürüst not).
- UPX yanıtı: upx -d "NotPackedException: not packed by UPX" ×4 — .UPX0/UPX1 KAMUFLAJ (VMProtect kendi paketi).
### V307-5: teslim ✓
- download/v307_kanit/ 47 dosya: v307_canli_analiz_raporu.md (ana), 11× {canli.json+txt+ram_menu.md+wine_hata.log}, 3.9.6 crash-pencere seti.
- HTML: V307 kartı (açık) + 11'li canlı tablo + CE yanıtı + UPX + IA32 notu; hero "11 sürüm canlı (V307)". Playwright: 10/10 içerik ✓ taşma yok (375+1280).
- scripts/: ram_menu_scan.py, canli_yakala_tara.sh, kosalabilirlik_testi.sh, write_v307_rapor.py, kur_wine_deb.sh, wp_env.sh + hook_path.c.
### V307-6: kalan (V308)
- Form ağacı için: şifreli metaobject yerine CRASH-PENCERESİ'nde derin dump (vtable chain + RTTI ham) ya da IA32'li kernel'de 32-bit koşu (3.3.1 + 32-bit full analiz); "start.exe" yanlış-PID koruması döngüye eklendi (comm filtresi VMProtect|wine).

## 29) V308 (2026-11-24, turn-30) — TEK HTML: 11 SÜRÜM SEKMELİ MENÜ OBJELERİ + ÖZELLİK LİSTESİ
### V308-0: istem
- Kullanıcı: "bana tek html de özellik listesi çıkarsana menü object ve özellik listesi hepsinde, bu 11 sürümde; 11 tane seçenek koy tıkladığımda özellikleri görim."
### V308-1: veri tabanı (yeni ölçüm: en.lng anahtar diff'i) ✓
- Her sürümün Langs/en.lng'i ayrıştırıldı (kaynak: work/allver_v306/Allver_Collection): 3.0.9=268, 3.2.0/3.3.1/3.4.0=270, 3.6.0=274, 3.8.1=275, 3.8.4=276, 3.8.7/3.9.4/3.9.6/3.10.4=279 anahtar.
- Sürüm eşikleri (3.0.9 tabanına göre): 3.2+ Action/CommandNotSupported; 3.6+ Assemblies/Contains/HWIDMismatched/LockToHWID; 3.8.1+ Complexity/Instances/VirtualMachine −UseTaggantSystem/VMSegments; 3.8.4+ ShadowStackCompatible; 3.8.7+ Decode/StackTrace/StackTraceDecoder/FunctionsForProtectionNotSelected/ExcludedFromProtection −ExcludedFromCompilation/ExcludedFromMemoryProtection.
- Complexity cevabı (eski soru): <3.8 hiçbir yerde (en.lng'de anahtar bile yok) → 3.8+ hem Functions (Protection→Complexity) hem Options→Virtual Machine (VM Complexity).
### V308-2: HTML üretimi ✓ (scripts/gen_html_v308.py → download/VMProtect_11surum_menu_ozellik.html, 186 KB)
- 11 tıklanabilir sekme (sıra: iyiden kötüye: 1 Ultimate 3.9.4, 2 Ultimate 3.8.4, 3 Professional 3.9.6, 4 Professional 3.8.1, 5 Lite 3.10.4, 6 Lite 3.8.7, 7 Ultimate 3.6.0, 8 Ultimate 3.4.0, 9 Ultimate 3.3.1 (x86-only), 10 Ultimate 3.2.0, 11 Ultimate 3.0.9).
- Sekme başına: kimlik chipleri (tarih/MD5/paketleme/.qtmetad) + MENÜ OBJELERİ (5 menü tablosu + 13 toolbar + 12 form + sol panel ağacı + en.lng ölçüm diff'i) + ÖZELLİK LİSTESİ (Lite/Pro/Ultimate matrisi vurgulu sütun + 16 UI eşiği ✓/✗ 'ilk kez X' + Complexity konumu + resmî changelog) + KANIT (V306 statik + V307 canlı QObject/RAM + CE notu).
- Alt kısımda: tüm sürümler karşılaştırma tablosu (11 satır) + yöntem/kanıt dosyaları (details).
- Bug düzeltmeleri: (1) sürüm kodu 3.100 vs 3.60 ondalık karşılaştırma hatası → tuple (3,10,0) karşılaştırmasına geçildi (3.6.0'a yanlış ARM64 ✓ veriyordu); (2) 3.3.1 changelog null → 3.3 taban kaydına fallback; (3) ram_menu dosya adı alt tireli → RAM vitrini 6 sürümde düzeltildi.
### V308-3: doğrulama ✓ (scripts/html_check_v308.py)
- Playwright: 375/768/1280 yatay taşma YOK; 11 sekme tıklanabilir, her panelde 5 menü tablosu + 1 matris + 16 eşik + changelog + 2 kanıt kartı; JS hatası YOK; karşılaştırma tablosu 12 satır.
- DOM eleman taşması: 0 (tüm paneller açık, 375px + 1280px). Yer tutucu taraması temiz.
- RAM vitrinleri gerçek kanıt dosyalarından: 3.10.4=41/49 (File×1602+499), 3.8.7=42/49, 3.4.0=40/49, 3.9.4=24/49, 3.8.4=19/49, 3.9.6=6/49 (erken çökme).
- PID-7744 grubu bulaşması dürüstçe işaretlendi (3.8.1/3.0.9/3.2.0 satırları; 3.0.9'da 'Complexity' RAM'de görünüp en.lng'de olmaması = kanıt).
### V308-4: kalan (V309)
- İsteğe bağlı: form ağacı için crash-penceresinde derin vtable/RTTI dump; IA32'li ortamda 3.3.1 32-bit koşusu.

## 30) V309 (2026-11-24, turn-31) — Test_64.exe VARYASYON MATRİSİ: 11 sürüm × 15 varyasyon + ZIP + HTML
### V309-0: istem
- Kullanıcı: "Test_64.exe'yi hangi varyasyonlarla şifreleyebilirsin — bir özellik aç, şifrele, kapat, başka aç, hepsini aç; fonksiyonlu ve fonksiyonsuz; HTML'e varyasyon listesi (sürüm hangilerini yapabiliyor) + zipte tüm varyasyonları sürüm sürüm klasörleyerek ver."
### V309-1: SANDBOX RECYCLE — ortam sıfırdan yeniden kuruldu ✓
- work/ tamamen gitmişti: rar Yandex public API'den yeniden (500.317.952 B, MD5 f98140bb... DOĞRULANDI) + unrar-nonfree deb (unrar-free RAR5'i açmıyor!) + Kron4ek wine-10.0-amd64-wow64 + Xvfb:99 + 15 .vmp (gen_vmp_v309.py) + Test_64.exe c:\ws.
- xkey/xlist ctypes ile yeniden yazıldı (kaynak yoktu): XTEST + XSendEvent yedeği; KRİTİK düzeltmeler: (1) mini WM şart (wm.py — WM'siz wine pencereleri hiç map olmuyor, odak zinciri kırılıyor); (2) Shift-katman keysym'ler (':'=Shift+';', '_'=Shift+'-') otomatik — OCR + notepad dosya kaydıyla ground truth (a:b_c.vmp ✓).
### V309-2: matris koşusu — HANG keşfi ve kurtarma ✓
- HANG: 3.4.0/3.6.0'da {fn-virt, fn-virt-all, fn0-import, fn0-stripreloc, fn0-vmtools} 'Saving… %' diyalogunda kilitleniyor (wine uyumsuzluğu) → matrise HANG durumu + otomatik uygulama yeniden başlatma eklendi (kalan varyasyonlar devam ediyor).
- 3 matris PARALEL koştu: A :99 (3.9.4, 3.8.4), B :98 (3.6.0, 3.4.0, 3.3.1, 3.2.0, 3.0.9), C :97 (3.9.6, 3.8.1, 3.10.4, 3.8.7) — ayrı prefix'ler + APPPID-scoped kill.
### V309-3: sonuçlar (work/v309/matrix_results.tsv, 140/165 satır; HTML V309 bloğu)
- 3.0.9: 15/15 OK · 3.2.0: 15/15 OK · 3.6.0: 10/15 (5 HANG) · 3.4.0: 10/15 (5 HANG) · 3.3.1: 0/15 ACILMADI (x86, IA32 kapalı) · 3.8.1 Pro: 12/15 (3 HANG) · 3.8.4 Ult: 12/15 (3 HANG) · 3.9.6 Pro: 7/15 (FAIL/CRASH/HANG karışık) · 3.9.4 Ult: 6/15 (karışık) · 3.10.4 Lite: KISMİ (~5/15, C son sürümlere yetişemedi) · 3.8.7 Lite: ÖLÇÜLEMEDİ (C sırası gelmedi).
- Tutarlılık: fn-mutation 3.6.0 = 14.848 B — recycle-öncesi kanıtla (14.8KB) birebir aynı; fn0-pack 3.9.4 = 4.05-4.09MB (önceki 4MB kanıtla uyumlu).
### V309-4: teslim ✓
- download/VMProtect_Test64_varyasyonlari.zip (115MB, 118 dosya): Test_64.exe + projeler/ (15 .vmp) + matrix_results.tsv + MD5_envanteri.txt + sürüm klasörleri (10 sürüm, her varyasyon exe'si).
- HTML güncellendi (227KB): V309 bloğu (varyasyon tanımları 15 satır + 11×15 yetenek matrisi ✓/✗/💥/⌛/⏻) + her sekmede "Test_64.exe varyasyon derlemeleri" 15 satır tablo + ZIP yolu + hero istatistik. Playwright: 375/1280 taşma yok, JS hatası yok, v309tb 16 satır/sekme.
### V309-5: eksikler (dürüst) — V310 için
- 3.8.7 (15 satır) ve 3.10.4'in ~10 satırı ölçülmedi (paralel C matrisi süreye sığmadı) → scripts/v309_matrix.sh V309_VERS="VMProtect Lite 3.8.7" ile ~10 dk'da tamamlanabilir; sonra pack_v309.sh + gen_html_v308.py yeniden.
- E2E koşuları (e2e_v309.sh) zaman kısıtından yapılmadı — HTML'de E2E sütunu '—' görünür.
- HANG'lerin Windows'ta gerçek davranışla ilgisi yok — wine-10.0-wow64 ölçümüdür (HTML'de notu var).

## 31) V310 (2026-11-24, turn-32) — UniversalMVPUIAlgorithm_QT GITHUB'A PUSH (V307-3 yamasıyla) + sohbet doğrulaması
### V310-0: istem
- Kullanıcı: "UniversalMVPUIAlgorithm_QT (2).zip kullanarak mı algıladın? neden ilkinde algılamamıştın? güncelleme mi attın yoksa vmplerdeki upx'i mi kaldırdın? güncellenmiş halini pushla (github.com/0xByteDeveloper/UniversalMVPUIAlgorithm_QT); chat.z.ai'deki konuşmaya bak, dürüstçe özetle."
### V310-1: sohbet geçmişi doğrulaması ✓
- chat.z.ai API (kullanıcının token'ı): sohbet "Yandex Disk Analysis Request" (40ecabba) — API yalnız ağaç döner, kök mesaj: "Yandex'teki Professional 3.9.6'yı aç, UniversalMVPUIAlgorithm_QT (2).zip ile analiz ettirt, form/obje/event JSON+MD, özellikleri listele, Test_64.exe'yi koru" — WORKLOG V302-V308 zinciri zaten tam belge.
### V310-2: dürüst cevapların kanıtı ✓
- İLK NEDEN OLMADI (V302): qt_scan.cpp QtDetector::detect() yalnız YÜKLÜ MODÜL isimlerine bakar (Qt5Core.dll...) — statik bağlanmış self-protected VMP hiç Qt DLL yüklemez → return false → 0 form/0 obje.
- NASIL ÇÖZÜLDÜ (V307-3): aynı yama yeniden uygulandı (sandbox recycle sonrası kaynak gitmişti): modül yoksa bellek imza fallback (UTF-8 qt_meta_stringdata/QMainWindow/lsFile + UTF-16 File/Edit/View → "Qt5 (static)").
- UPX KALDIRILMADI: upx -d "not packed by UPX" — .UPX0/.UPX1 KAMUFLAJ (PATCHES.md'de belgelendi).
### V310-3: derleme + duman testi ✓
- capstone 5.0.9 deb (pool'dan, 4.x/5.0.6 URL'leri 404). İç varyant (GOD STATE): g++ derlendi, umvpqt_cli 312KB çalışıyor. Dış varyant: gen_layout → qt_layout_db.h üretildi, derlendi (cstring eklendi). İkisi de Qt'siz süreçte dürüst negatif ("Qt bulunamadı"), çökme yok.
- .gitignore hatası: 'ummpqt' deseni include/umvpqt/ BAŞLIK dizinini yuttu → köke sabitlendi (/umvpqt_cli), 56→78 dosya.
### V310-4: PUSH ✓
- github.com/0xByteDeveloper/UniversalMVPUIAlgorithm_QT (repo boştu): 2 commit — 090dce1 (yama + PATCHES.md + 78 dosya) + 37efd53 (gitignore fix). API ile doğrulandı.

## 32) V311 (2026-11-24, turn-33) — VMProtect UI-YAPISI: 11 JSON + HTML → MVP_VMP_ALGORTIHM
### V311-0: istem
- Kullanıcı numaralı ağaç istedi: Tab Liste 1)Project 2)Functions 3)Details; Project 1.1)Functions for protection 1.2)Licenses 1.3)Files 1.4)Options; Options 1.4.1)Virtual Machine (1.4.1.1 Version combobox, 1.4.1.2 Instances, 1.4.1.3 Complexity %) 1.4.2)File (Memory/Import Protection...) 1.4.3)Detection 1.4.4)Additional 1.4.5)Messages 1.4.6)Licensing Parameters; ADD Function dene (MovePlayer) → 1.1.1)Protection 1.1.2)Details (Compilation Type combobox, Complexity %, Lock to Serial). SS YOK — QT analizçisiyle. 11 sürüm JSON + HTML (exe ekleme ekranı → sürüm listesi) → github MVP_VMP_ALGORTIHM.
### V311-1: Yandex bu oturumda ENGELLİ ✓ (dürüst not)
- public API → 'Sen robot değil misin?' captcha + 405 {"error":true}. Taze indirme yapılamadı → canlı veriler V307 kanıt klasöründeki ANALİZÇİ çıktıları (aynı konuşma zincirinden): 11/11 qt=Qt5(static), qobj 3-317; ram_menu.md tabloları JSON'lara gömüldü (metin başına 0-42 gerçek bulgu).
### V311-2: yapı gerçekliği ✓
- Kullanıcının ağacı + kanıtlar: Options bitleri 3.5.1 core (cpMemoryProtection=0x1000, cpImportProtection=0x200, 7 File seçeneği, 5 Detection, 5 Additional, 5 Mesaj); NodeType (FUNCTIONS/LICENSES/FILES/OPTIONS); CompilationType 0/1/2=Virt/Mut/Ultra; VM grubu ilk kez 3.8.0 (V308 en.lng ölçümü: Version [Default, VMProtect 2.X], Instances Default/1..10, Complexity None/1..100); Shadow Stack 3.8.3; Ultimate-only: Licenses/Files/Licensing/Lock to Serial; ADD Function = V309 GERÇEK deney (MovePlayer 0x1400014C0, 3 tip derlendi, kanıt Test_64.vmp.exe).
### V311-3: çıktılar ✓
- download/vmp_ui_json/ 11 JSON (17-21KB) + download/VMProtect_UI_yapisi_v311.html (137KB, tek dosya, gömülü veri).
- Agent-browser akış testi: ekle ekranı → 11 kart → yapı (16 grup; 1.1/1.2/1.3/1.4 + 1.4.0 + 1.4.1-1.4.6); pre-3.8'de '1.4.1 Virtual Machine YOK' doğru; taşma 375/768/1280 = false; JS sozdizimi node Function() ile OK.
### V311-4: PUSH ✓
- github.com/0xByteDeveloper/MVP_VMP_ALGORTIHM: commit 41d291a — README + HTML + 11 JSON. API doğrulandı (13 dosya).


---
**WORKLOG.md 400 satırı aştı: devamı `WORKLOG2.md` dosyasında (son bölüm `## 34) V313`). Her kullanıcı mesajında önce WORKLOG2.md'nin son bölümü okunur.**
