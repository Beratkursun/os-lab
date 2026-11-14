fatal: not a git repository (or any parent up to mount point /)
Stopping at filesystem boundary (GIT_DISCOVERY_ACROSS_FILESYSTEM not set).

## 14 Kasım 2026 — öğrencinin Ubuntu terminalinden ekran kanıtı

Bu bölüm, yukarıdaki canlı Ubuntu kanıtının henüz bulunmadığına ilişkin
önceki durum notunu günceller. Komutları öğrenci kendi terminalinde çalıştırmış,
ekran görüntülerini paylaşmıştır; aşağıdakiler bu görüntülerden okunmuştur.

### Görev testleri — 18:57 ekranı

```text
Ran 12 tests in 0.331s
OK
ubuntu@ubuntu:~/os23-teslim$
```

OS-23 testleri öğrencinin Ubuntu terminalinde başarılıdır. Bu ekran tek başına
Ubuntu sürümünü, mimarisini, Git commit kimliğini veya üç canlı sorgunun
sonuçlarını göstermez; bu bilgiler ayrıca doğrulanmış kabul edilmez.

### Ortak test — 19:06 ve 19:08 ekranları

```text
Ran 228 tests in 1.295s
FAILED (failures=4, errors=5)
[fail] cleanup-lab/engine
[skip] cargo not installed; qrust-scan tests not run
Ran 116 tests in 40.870s
OK
[ok] linux-lab/tests (OS-30 hardening)
--quick: skipping remaining stages
== Summary ==
2 passed, 4 failed, 1 skipped
```

Ortak komut tamamlanmıştır ve başarılı değildir. Cleanup testlerinde platform
seçimi/relocation ve app-running denetimleriyle ilgili hatalar görünmektedir.
Ubuntu'daki 4 başarısız süitin tamamını belirlemek için tam log henüz
alınmamıştır. Bu sonuç, Debian ortamındaki 3/2/1 özetiyle aynı değildir;
Ubuntu başarısızlıklarının tamamının upstream kaynaklı olduğu iddia edilmez.

### Son teslim durumu

- OS-23 için öğrencinin terminalinden 12 başarılı test ekranı alındı.
- Ortak testin başarısız sonucu dürüstçe kaydedildi; tam log ve canlı paket
  sorgularının çıktıları henüz alınmadı.
- 2–4 dakikalık öğrencinin anlatımlı ekran videosu ve erişilebilir bağlantı
  henüz yoktur. Bu nedenle tam teslim/kabul tamamlandı denmez.
