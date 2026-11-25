

<!-- V316_BAS -->
# V316 KONTROL NOKTASI — 2026-11-25

Görev: VMProtect 3.5.1 kaynağını 3.9.4 düzeyine yükseltme (scripts/v312_apply_yukseltme.py, 96 nokta).
Durum: MOVD/MOVQ GERÇEK sanallaştırma (changelog 3.7.3'ün son komut kalemi) + V315 BMI2 — VM tarafı
tamam. XMM'li diğer komutlar kapıda (128-bit işleyici zincirleri; V316 otXMMRegistr altyapısı hazır).
Kanıt ve rapor: MyRepo main → download/V316_rapor.md, download/v316_kanit/ ; WORKLOG2.md §38.
Yeniden kurma: v314_yeniden_kur.sh → v312_apply_yukseltme.py → v312_build_linux.py → v314_disasm_karsilastir.py → v314_pe_test.py → v315_of_of394.py → v315_zip_yenile.py → v316_kaydet.py → v315_commit.py.
Sıradaki iş: (1) XMM'li diğer komutlar (movdqa/128-bit zincirler), (2) resmî 3.9.4 derleme tetikleyicisi (menü tıklaması eklendi), (3) VEX kodlayıcı, (4) anti-decompiler (AddObfuscation), .NET [DllExport], Mach-O chained fixups, (5) BZHI/ANDN/BLSR/SHA-NI/RDGSBASE çözümleme.
<!-- V316_SON -->
