

<!-- V315_BAS -->
# V315 KONTROL NOKTASI — 2026-11-25

Görev: VMProtect 3.5.1 kaynağını 3.9.4 düzeyine yükseltme (scripts/v312_apply_yukseltme.py).
Durum: BMI2 GPR (SHLX/SHRX/SARX/RORX) GERÇEK sanallaştırma — 10/10 yerel=0 + rc=42. XMM'li komutlar
hâlâ kapı (MOVD/MOVQ = 3.7.3 kalemi). Wine zlib düzeltmesi v312_wine.sh'ye kalıcı işlendi.
Kanıt ve rapor: MyRepo main → download/V315_rapor.md, download/v315_kanit/ ; WORKLOG2.md §37.
Yeniden kurma: v314_yeniden_kur.sh → v312_apply_yukseltme.py → v312_build_linux.py → v314_disasm_karsilastir.py → v314_pe_test.py → v315_of_of394.py → v315_kaydet.py → v315_commit.py.
Sıradaki iş: (1) MOVD/MOVQ + XMM işleyici zincirleri (VM), (2) VEX kodlayıcı (CompileToNative), (3) resmî 3.9.4 GUI ölçümünün tamamlanması, (4) anti-decompiler (AddObfuscation), .NET [DllExport], Mach-O chained fixups, (5) BZHI/ANDN/BLSR/SHA-NI/RDGSBASE çözümleme.
<!-- V315_SON -->
