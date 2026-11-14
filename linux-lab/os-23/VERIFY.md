# OS-23 doğrulama

## Gerçekte çalıştırılan ortam

14 Kasım 2026; Debian GNU/Linux 13 (trixie), x86_64, Python 3.12.14.
Çalışma ortamının ana dpkg veritabanı boştur. Ubuntu VM erişimi yoktur.
Gerçek dpkg entegrasyonu geçici, sentetik `oslab-demo` veritabanını okur;
bu test kurulu bir Ubuntu paket kümesi üzerinde çalıştırılmış gibi sunulmaz.

## Tekrarlanabilir görev testleri

Depo kökünden:

```bash
python3 android-lab/lab/tests/test_dpkg_inventory.py
python3 -m pytest android-lab/lab/tests/test_dpkg_inventory.py -q
```

Sonuç: **12 passed**. 11 çevrimdışı birim testi ve dpkg mevcutsa gerçek
subprocess kullanan bir entegrasyon testi. Geçici veritabanında listeleme,
dosya listesi, boşluklu/jokerli tam yol sahipliği ve olmayan paket kontrol edilir.
Birim testleri multiarch, çoklu sahip, residual-config/unpacked durumları,
bozuk çıktı, komut eksikliği, erişim hatası, zaman aşımı ve geçersiz girdileri kapsar.
`dpkg` bulunmazsa yalnızca entegrasyon testi açıkça skip edilir.

```bash
python3 linux-lab/scripts/dpkg_inventory.py -l
python3 linux-lab/scripts/dpkg_inventory.py -L bash
python3 linux-lab/scripts/dpkg_inventory.py -S /usr/bin/dpkg
```

Boş host veritabanında `-l` başarılı boş envanter verir; diğer ikisi JSON hata ve
çıkış 1 verir. Paketlerin kurulmuş olduğu Debian/Ubuntu'da aynı komutlar canlı
veritabanını okur. Dosya yolu `dpkg -L PACKAGE` çıktısındaki tam yol olmalıdır.

## Deponun gerçek ortak test komutu

```bash
./scripts/run-tests.sh --quick
```

Komut çalıştırıldı; sonuç **başarısız**. Ortak altyapıdaki hatalar temiz temel
`d257386f642f776b7b665f231858e256b168efde` checkout'ında da tekrarlandı.

| Süit | Temel checkout | OS-23 değişikliği |
| --- | --- | --- |
| Android pytest | 215 passed, 5 failed, 4 skipped | 227 passed, 5 failed, 4 skipped |
| Source engine pytest | 176 passed | 176 passed |
| Cleanup reference | 35 tests OK | 35 tests OK |
| Cleanup engine | 228 tests; 3 failures, 6 errors | Aynı hatalar |
| OS-30 | 116 tests OK | 116 tests OK |
| Rust | cargo yok, skipped | cargo yok, skipped |

Android hataları `test_cleanup_console` içindeki relocation/volumes/privileged
capabilities, `test_source_console::test_merge_background_plan_and_build` ve
`test_web_ui::test_lab_start_requires_a_previewed_plan` testleridir. Cleanup engine
hataları relocation ve app-running safety testlerindedir. Görev kapsamı dışındaki
bu testler değiştirilmedi veya devre dışı bırakılmadı. Özet: 3 suite passed,
2 failed, 1 skipped. Quick modu diğer aşamaları tasarımı gereği çalıştırmaz.

## Teslim için açık kalanlar

- Öğrencinin Ubuntu Server'ında yukarıdaki ortak test ve canlı paket sorguları.
- 2–4 dakikalık öğrencinin kendi anlatımını içeren ekran videosu ve paylaşılabilir linki.
- Ortak test hatalarının maintainer değerlendirmesi.

Bu belgede video linki veya Ubuntu başarı kanıtı uydurulmamıştır.

## Bilgi grafiği kontrolü

`lat check` çalıştırıldı. OS-23 bağlantıları ve kısa giriş paragrafı kontrolü
geçer; tüm grafik, önceden var olan `lat.md/cleanup-lab.md:237` paragraf
uzunluğu hatası nedeniyle başarısızdır. Aynı tek hata temel checkout'ta da vardır.
