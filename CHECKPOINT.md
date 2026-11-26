# V329 KONTROL NOKTASI — 2026-11-26
KAYIP KURTARMA: work/ recycle → universalAllver.rar'dan 3.5.1 tam kaynak (1197 dosya, rarfile ile;
unrar jokerleri boş açıyordu) → lang/version/lzma/tinyxml/demangle/lua/msvc_macros yeniden üretildi →
**vmprotect_con Linux-native CON_OK** ("VMProtect Professional v3.5.1", g++ 14.2, 19 kaynak).
VARYANT YENİDEN ÜRETİM: gerçek .vmp XML formatı çözüldü (MapAddress=EXPORT ADI + IncludedInCompilation;
hex adres eşleşmiyordu) → mutation/virt/ultra/ALL farklı MD5 + 0x14C0 E9 jmp (V324 deseni) +
V324-tarzı ALL (Options=0x513C8): .text/.rdata/.data/.pdata RSZ=0 SANAL.
ÇÖZÜM TURU: yeni ALL'da AV-tuzağı YOK (AV=0, kızaksız 34.5M insn/300s) + LZMA DEKOMPRES DÖNGÜSÜ
YAKALANDI: 0x200000-0x2FFFFF'ye 34.4M yazma (probs tablosu), 64K-blok başına 32768 uniform talimat.
.text yazma henüz 0 — dekompres tamamlanmadı; 900s koşu betiği kayıtlı (scripts/v329_all_cozum.py).
3.5.1-motor ALL'da DÜZ LZMA akışı → unpack emülatörde tamamen mümkün (sadece uzun koşu).
YONTEM.md: TersineCevirici 742b975 (push doğrulandı) — A.1-A.10 zincir + 6 KANITLI yükseltme maddesi
+ ALL playbook. MyRepo 7e65170 (push doğrulandı).
SIRADA: (a) 900s uzun koşunun sonucu (uzun_kosu.log) — dekompres .text'e ulaşınca PACKER_INFO
{Src,Dst} çözümü + OEP; (b) 900s yetmezse koşuyu parçalara böl (checkpoint/restore) veya
probs-döngüsünü statik tespit edip atla; (c) TersineCevirici'ye lzma-izleme komutu.
