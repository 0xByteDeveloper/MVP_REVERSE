

<!-- V321_BAS -->
# V321 KONTROL NOKTASI — 2026-11-26

Görev: VMProtect 3.5.1 → 3.9.4 yükseltme (scripts/v312_apply_yukseltme.py, 111 nokta).
V321: kopya-başı rastgele VM yazmaçları (resmî Instances üç bileşen tamam: konum+yön+gövde) + rea v6.10.1 + Ghidra 12.2 + JDK25 RE altyapısı (Test_64 kanıtlı) + universalAllver.rar upload/ KALICI (rar içi 3.9.4 = analiz ikilisi, MD5 eş). İstikrar 16×3: 14 temiz + 2 anti-analiz.
Kanıt/rapor: download/V321_rapor.html + .md + download/v321_kanit/; WORKLOG2.md §43.
Sıfırlanınca: git remote set-url origin (PAT skill dosyasında) + reset origin/main → v314_yeniden_kur.sh → v312_apply_yukseltme.py (111) → build → v321_istikrar.py.
Sıradaki: (1) birleşik işleyici derinliği, (2) MBA×Pack kökü, (3) dönen-anahtar, (4) BMI, (5) saldırı direnç testi.
<!-- V321_SON -->
