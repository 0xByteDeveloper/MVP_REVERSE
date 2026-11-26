# V328 KONTROL NOKTASI — 2026-11-26
TERSINECEVIRICI b437c0e (push doğrulandı): `etiket` komutu 4/4 varyant Python V327 ile BİREBİR
(51/8/859/23, dağılımlar dahil) + `avtuzak` (3 aday, kanıtlı tuzak 0x142998CC2 dahil) + regression
(mutasyon 42/42, oracle 12 olay) korunuyor. LZMA FORMATI 3.5.1 kaynağından çözüldü: PACKER_INFO
{Src,Dst} tablosu + props ayrı 5 bayt + end-marker'lı akış (boyut alanı yok) — ALL'da .#:V bu tablo,
Dst'ler koşu-anı çözülür, Src'ler .cqM-içi ofset. S1/S2'nin boş çıkma nedeni anlaşıldı (ALONE başlığı
yok — format başlıksız). AV-TUZAK KABA KUVVET: bit-3 set 58 K değeri kızaksız; K=0xE0/E8 7.5M insn
(normalin 21 katı, en uzun koşu) — textY=0, tek bayt yetmiyor, VEH çok-baytlı değer üretiyor.
reu: 3.8.4 tek-vaka Triton replay (VIP=r8, key=rbp, jmp rsi); füzyon gözlemimiz benzersiz.
Kaynak yükseltme fark listesi: 6 kanıtlı madde hazır (rapor tablosunda).
SIRADA: (a) Windows'ta x64dbg VEH-değer dökümü (K=0xE0/E8 bit-3 ailesi ipucuyla), (b) 3.5.1 kaynağına
6 maddenin aktarımı, (c) K=0xE0 yolunun iz-analizi (7.5M insn nereye gidiyor — dekompres başlangıcı
olabilir mi).
