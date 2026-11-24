# MVP_REVERSE KONTROL NOKTASI — V312 (VMProtect 3.5.1 → 3.9.4)

Güncelleme: 2026-11-24 20:18. Amaç: eldeki 3.5.1 kaynak kodunu geliştirerek 3.9.4'ün özelliklerini birebir
kazandırmak. Önceki durum WORKLOG.md / WORKLOG2.md bölümler 33–35'te.

## Durum

- 70 noktalı yama uygulandı (diff ve kayıtlar MyRepo download/vmp_yukseltme_3.9.4/patch/);
  yamalı kaynak ağacının tam kopyası src/vmp_3.5.1_yamali_3.9.4_kaynak.zip içinde.
- Eklenenler: proje + fonksiyon Complexity, Instances, VM sürümü, rastgele bölüm adı, tohum; kanallar:
  XML, konsol (-vm/-vi/-vc/-rs), Lua (vmprotect.core()); Qt satırları kaynakta hazır, DERLENMEDİ.
- Doğrulama: 37 varyasyon çift motorlu matris (rc=0: taban 37, yamalı 37; wine 35/37),
  konsol anahtarı testleri, Lua proje ayarı testleri.

## Bilinen açıklar (öncelik sırasıyla)

1. Complexity'in resmî mekanizması bileşik işleyici olasılığıdır; uygulamada ön mutasyon + kripto zinciri
   var, birleştirme kapısı yok (intel.cc Merge/can_merge/CompileCommand).
2. Lua betik yolu konsol yolundan farklı, koşular arası tekrarlanamaz çıktı üretiyor.
3. Qt GUI derlenmedi; .NET complexity ve Shadow Stack yok.

## Sandbox sıfırlanınca yeniden kurulum

1. Kaynak zip repo kökünde: universalObfusPublicSrc_kaynak_kod.zip. Ağaçlar: `python3 scripts/v312_prep_trees.py`
   (Kullanım: v312_prep_trees.py [--yeni]     (--yeni: BASE ve PATCHED'i silip yeniden kurar) ).
2. Yama: `python3 scripts/v312_apply_yükseltme.py` (önce denetim: `V312_KONTROL=1` → tüm çapalar eşleşmeli).
3. Derleme: `python3 scripts/v312_build_linux.py --src work/v312/src_patched --out work/v312/build_patched -j 2`.
4. Matris: `bash scripts/v312_matris_kos.sh` (taban + yamalı, log: work/v312/matris_kosu.log).
5. Wine (köksüz): `bash scripts/v312_wine_kur.sh`.
6. Araştırma: `GH_TOKEN=… python3 -I scripts/v312_arastirma_cek.py` (anahtar SKILL dosyasından, yazılmaz).
7. Kapanış/paket/rapor: `python3 scripts/v312_kapanis.py`.

## Gönderim

- MyRepo (kaynak+teslimler): `GH_TOKEN=… python3 scripts/v313_commit.py "V312: …"`
- MVP_REVERSE (WORKLOG/CHECKPOINT): `GH_TOKEN=… python3 scripts/v313_mvp_reverse_push.py`
