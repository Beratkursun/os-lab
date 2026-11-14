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

## PR #208 — upstream birleştirmesinden sonra son kontrol

PR: https://github.com/keyvanarasteh/os-lab/pull/208 (taslak).
Upstream `33d703fc946d6f67472dd64fc8a89e5348a0c8a5` uygulama dalına
`08c92550da18c4d1b3fc2ad83ee1a7070216bbca` ile alındı. İki ortak raporun
çakışması, güncel OS-21 raporları korunup OS-23 bağlantıları eklenerek çözüldü.
Kaynak kod ve 12 görev testi değişmedi; görev testleri tekrar **12 passed**.

Güncel upstream Android suite'i temiz checkout'ta **368 passed, 8 failed,
4 skipped**; OS-23 eklenmiş sürüm **380 passed, 8 failed, 4 skipped** verdi.
Aynı sekiz başarısız test karşılaştırıldı. Önceki beşine eklenenler
`test_oslab_console` içindeki üç native macOS capability testidir. Bunlar
upstream'de de başarısızdır; OS-23 ile ilgili değillerdir.

Birleştirme sonrası ortak komutun gerçek son çıktı özeti:

```text
Ran 116 tests in 30.797s
OK
[ok]   linux-lab/tests (OS-30 hardening)
--quick: skipping remaining stages
== Summary ==
3 passed, 2 failed, 1 skipped
```

Source engine 176 passed, cleanup reference 35 OK, cleanup engine yine
3 failures/6 errors, Rust cargo yok skipped. `lat check` aynı tek mevcut
cleanup-lab paragraf uzunluğu hatasını verdi. Yukarıdaki ilk tablo önceki
checkout'a aittir; son durum bu bölümdedir.

## Ubuntu'da son teslim kontrolü ve video akışı

Ubuntu terminalinde aşağıdaki komutlar çalıştırılmalıdır. Bunlar henüz
öğrencinin Ubuntu makinesinde çalıştırılmış sayılmaz:

```bash
git clone --branch feat/dpkg-inventory https://github.com/Beratkursun/os-lab.git os23-teslim
cd os23-teslim
python3 --version
python3 linux-lab/scripts/dpkg_inventory.py --help
python3 linux-lab/scripts/dpkg_inventory.py -l
python3 linux-lab/scripts/dpkg_inventory.py -L dpkg
python3 linux-lab/scripts/dpkg_inventory.py -S /usr/bin/dpkg
python3 android-lab/lab/tests/test_dpkg_inventory.py
./scripts/run-tests.sh --quick 2>&1 | tee /tmp/os23-test-sonucu.txt
```

Paketin dosyası farklı yoldaysa `-L dpkg` çıktısındaki tam yolu `-S` ile sorgulayın.
Python 3.11+ gerekir. Ortak testin başarısız sonuçları da videoda açıkça gösterilir.
Daha önce klonlanmış klasörün üzerine yazmayın; örnekteki `os23-teslim` yeni klasördür.

### 2–4 dakikalık anlatım planı

- 0:00–0:25: Ad, ders, OS-23 görevi, fork ve rezervasyon PR'ını göster.
- 0:25–1:20: Ubuntu terminalinde listeleme, paket dosyaları ve dosyadan sahip bulma modlarını göster.
- 1:20–1:50: Kodda komutun shell kullanmadan çağrılmasını, girdi doğrulamayı ve süre sınırını anlat.
- 1:50–2:30: 12 görev testini ve ortak testin gerçek sonucunu göster; mevcut hataları gizleme.
- 2:30–3:00: REPORT/VERIFY dosyalarını, uygulama PR'ını ve AI kullanım beyanını göster.

Videoyu erişilebilir bağlantıyla paylaş; gizli bilgi, parola veya token gösterme.
Bağlantı ve gerçek Ubuntu çıktıları PR açıklamasına eklenince son kontrol yapılır.
Hazır olmayan video için örnek veya sahte bağlantı eklenmez.

Türkçeleştirme sonrası görev testleri yeniden çalıştırıldı: **12 test başarılı**.
JSON alan adları ve komut seçenekleri mevcut arayüzle uyum için korunmuştur.
