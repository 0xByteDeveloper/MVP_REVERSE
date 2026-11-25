

<!-- V314_BAS -->
# V314 KONTROL NOKTASI — 2026-11-25

Görev: VMProtect 3.5.1 kaynağını 3.9.4 düzeyine yükseltme (yama: scripts/v312_apply_yukseltme.py).
Durum: yeni komut desteği ÖLÇÜLDÜ, EKSİK. Önceki '185/185 TAMAM' ölçümü düzenek hatasıydı (geçersiz).
Kanıt ve rapor: MyRepo main → download/V314_rapor.md, download/v314_kanit/ ; WORKLOG2.md §36.
Yeniden kurma sırası: v312_prep_trees.py → v312_apply_yukseltme.py → v312_build_linux.py (iki motor) → v312_wine_kur.sh → v314_disasm_karsilastir.py → v314_pe_test.py → v314_kaydet.py.
Sıradaki iş: (1) çözümleme tablosu düzeltmesi (doğru harita WORKLOG2 §36'da), (2) yeni tipler için CompileToNative kodlayıcı + GetCommandInfo + CompileToVM, (3) MOVD/MOVQ sanallaştırma, (4) resmî 3.9.4 GUI karşılaştırması (Xvfb + xdotool), (5) Mach-O/.NET, anti-decompiler, Qt GUI, rapor.

Kayıt durumu: V314 dosyaları (scripts/v314_*, download/V314_rapor.md, download/v314_kanit/, WORKLOG2.md) MyRepo main'e hızlı-ileri ile gönderildi (ilk commit 24ed08a12ab1). YEREL DEPO GERİDE: yerel HEAD 104246c (V308), uzak main 86+ commit ileride; yerel WORKLOG.md ve scripts/v312_*/v313_* sürümlerinin uzaktakiyle AYNI mı FARKLI mı olduğu DOĞRULANAMADI (betiğin karşılaştırması bu yollar için başarısız/okunamadı; aynı içerikli dosyaları da işaretliyor) → güvenlik için üzerine yazılmadı (55 dosya atlandı). V314 ölçümü YEREL scripts/v312_apply_yukseltme.py sürümüyle yapıldı; uzaktaki sürümün (commit 895cf75) aynı olduğu DOĞRULANMADI. Devam etmeden önce: uzak main fetch edilip yerel farklar karşılaştırılmalı ve ölçüm uzak yama sürümüyle yinelenmeli. Commit aracı: scripts/v314_commit_sinirli.py (geçici indeks; v313_commit.py kullanma).
<!-- V314_SON -->
