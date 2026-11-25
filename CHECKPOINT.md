

<!-- V316_BAS -->
# V316 KONTROL NOKTASI — 2026-11-25

Görev: VMProtect 3.5.1 kaynağını 3.9.4 düzeyine yükseltme (scripts/v312_apply_yukseltme.py, 96 nokta).
Durum: MOVD/MOVQ GERÇEK sanallaştırma (changelog 3.7.3'ün son komut kalemi) + V315 BMI2 — VM tarafı
tamam. XMM'li diğer komutlar kapıda (128-bit işleyici zincirleri; V316 otXMMRegistr altyapısı hazır).
Kanıt ve rapor: MyRepo main → download/V316_rapor.md, download/v316_kanit/ ; WORKLOG2.md §38.
Yeniden kurma: v314_yeniden_kur.sh → v312_apply_yukseltme.py → v312_build_linux.py → v314_disasm_karsilastir.py → v314_pe_test.py → v315_of_of394.py → v315_zip_yenile.py → v316_kaydet.py → v315_commit.py.
Sıradaki iş: (1) XMM'li diğer komutlar (movdqa/128-bit zincirler), (2) resmî 3.9.4 derleme tetikleyicisi (menü tıklaması eklendi), (3) VEX kodlayıcı, (4) anti-decompiler (AddObfuscation), .NET [DllExport], Mach-O chained fixups, (5) BZHI/ANDN/BLSR/SHA-NI/RDGSBASE çözümleme.
<!-- V316_SON -->

<!-- V317_BAS -->
# V317 KONTROL NOKTASI — 2026-11-25

Görev: VMProtect 3.5.1 → 3.9.4 yükseltme (scripts/v312_apply_yukseltme.py, 101 nokta).
V317: Packer Finder ölçümü (Test_64.exe, 11 yapılandırma; çıktılar 3.6–3.8.7 bandında, 3.5.1 olarak ALGILANMIYOR) + resmî .vmp0/.vmp1 bölüm adlandırması (taban teli .vmp00 kalktı) + bölüm bayrakları resmî K1 deseniyle (0x68000020) birebir.
Kanıt/rapor: MyRepo main → download/V317_packer_finder_rapor.html, download/V317_rapor.md, download/v317_kanit/ ; WORKLOG2.md §39.
Yeniden kurma: v314_yeniden_kur.sh → (101 nokta) → v312_build_linux.py → scripts/v317_test64_coklu.py → scripts/v317_html_rapor.py → v317_kaydet.py.
Sıradaki iş: (1) çalışma-zamanı sarmalayıcısını 3.9.4 boyut profiline büyütmek (216 → 2048 B, dist hedefi), (2) XMM'li diğer komutlar (128-bit zincirler), (3) VEX kodlayıcı, (4) anti-decompiler/.NET/Mach-O, (5) BZHI/ANDN/BLSR/SHA-NI/RDGSBASE.
<!-- V317_SON -->

<!-- V318_BAS -->
# V318 KONTROL NOKTASI — 2026-11-25

Görev: VMProtect 3.5.1 → 3.9.4 yükseltme (scripts/v312_apply_yukseltme.py, 102 nokta).
V318: tam-ayarlı derlemeler (Options bitleri ilk kez açık; tam-ayar 6,87 MB rc=0, 3 .vmp bölümü; pack 4,88 MB rc=0) + x1/x2/x3 cevapları + virt-saflık düzeltmesi (ultra≡virt ve pack×vi≥2 çöküşü ikisi de çözüldü).
Kanıt/rapor: MyRepo main → download/V318_packer_finder_rapor.html, download/V318_rapor.md, download/v318_kanit/ ; WORKLOG2.md §40.
Yeniden kurma: v314_yeniden_kur.sh → (102 nokta) → v312_build_linux.py → scripts/v318_olcum.py → scripts/v318_html_rapor.py → v318_kaydet.py.
Sıradaki iş: (1) sarmalayıcı donanımını 3.9.4 boyut profiline büyütmek (216 → 2048 B), (2) XMM'li diğer komutlar, (3) VEX kodlayıcı, (4) anti-decompiler/.NET/Mach-O, (5) BZHI/ANDN/BLSR/SHA-NI/RDGSBASE.
<!-- V318_SON -->
