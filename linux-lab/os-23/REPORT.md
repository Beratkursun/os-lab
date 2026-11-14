# MYO203 — OS-23 — dpkg envanteri ve dosya sahipliği

Öğrenci: Berat Kurşun (`Beratkursun`)  
Tarih: 14 Kasım 2026  
Rezervasyon: https://github.com/keyvanarasteh/os-lab/pull/205

## Problem ve çözüm

`dpkg -l`, `dpkg -L` ve `dpkg -S` çıktılarından paket durumlarını ve dosya
sahiplerini elle okumak yerine, Python standart kütüphanesiyle tek bir JSON
arayüzü hazırlandı. Görev kapsamı, `CONTRIBUTING.md` ve dashboard içindeki
OS-23 kartının `linux-lab/scripts/dpkg_inventory.py` teslimi esas alınarak belirlendi.

- `-l`: ad, sürüm, mimari, açıklama, üç karakterli durum ve kurulu paket sayısı.
- `-L PACKAGE`: paketin veritabanında kayıtlı dosyaları; boşluklu yollar korunur.
- `-S /PATH`: tam yol eşleştirmesi; çoklu sahip ve `:architecture` desteği.
- `--admindir`: ana sistem veritabanına dokunmadan bir kopya/örnek üzerinde sorgu.

## Tasarım kararları

Komutlar argüman listesiyle çağrılır; shell kullanılmaz. Paket adlarında seçenek
ve joker girişleri reddedilir. Dosya aramasında dpkg'nin kendi jokerleri de
kaçırılır, sonuç tam yolla karşılaştırılır. Dil `LC_ALL=C`, süre sınırı 20 saniyedir.
Eksik komut, zaman aşımı veya bulunamayan paket/yol boş başarılı rapor olarak
gösterilmez. İşlem hatası JSON ve çıkış 1, kullanım hatası argparse ve çıkış 2 verir.

Araç sadece yerel paket metadatasını okur; root, ağ, bağımlılık kurulumu veya
paket silme/güncelleme yoktur. Dosya sahipliği kaydı, dosyanın gerçekten mevcut
veya değiştirilmemiş olduğunun kanıtı değildir. Symlink veya usr-merge yolları
otomatik çevrilmez. Bakım betiklerinin oluşturduğu her dosya dpkg'de kayıtlı olmaz.

## Değişen alanlar ve doğrulama

Kaynak: [dpkg_inventory.py](../scripts/dpkg_inventory.py). Testler, deponun ortak
pytest keşfine dahil olan [test_dpkg_inventory.py](../../android-lab/lab/tests/test_dpkg_inventory.py)
içindedir. Kullanım `linux-lab/README.md`, davranış `lat.md/linux-lab.md` içinde açıklanır.
Kanıtlar ve sınırlamalar: [VERIFY.md](VERIFY.md).

12 görev testi başarılıdır. Gerçek dpkg ile geçici sentetik veritabanı testi,
canlı Ubuntu Server testiyle aynı şey değildir. Ortak testlerdeki mevcut hatalar
ayrı ve temiz temel checkout'ta tekrarlandı; genel testlerin hepsi geçti iddiası yoktur.

## AI kullanım beyanı ve kalan teslim adımı

Kod, test ve belgeler ChatGPT/Codex yardımıyla hazırlanmış ve test edilmiştir.
Bu beyan öğrencinin araçları şahsen çalıştırdığı veya video çektiği iddiasını içermez.
Ubuntu Server üzerinde doğrulama ve öğrencinin 2–4 dakikalık anlatımlı ekran
videosunun paylaşılabilir bağlantısı henüz sağlanmamıştır. Bu nedenle PR,
incelemeye açık uygulamadır; eksiksiz vize kabulü/merge edilmiş teslim değildir.
