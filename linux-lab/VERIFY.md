# OS-21 Dogrulama (Verification) — Boot-Time Python Service Starter (systemd)

## 1. Calistirma Komutlari

Servis birim dosyasini (.service) uretmek ve onizlemek icin:
    ./linux-lab/scripts/os-21-service-starter.sh

Servisi systemd altina kurmak, acilista otomatik baslayacak sekilde etkinlestirmek (enable) ve status / journalctl ciktilariyla dogrulamak icin:
    sudo ./linux-lab/scripts/os-21-service-starter.sh --install

## 2. Beklenen Cikti

    === [1/4] loop.py dosyasi kontrol ediliyor ===
    === [2/4] Systemd .service birim dosyasi olusturuluyor ===
    === [3/4] Servis sisteme kuruluyor ve etkinlestiriliyor (enable & start) ===
    === [4/4] Dogrulama: enable / status / journalctl ===
    -> Enabled durumu:
    enabled
    -> Servis durumu (status):
    Active: active (running)
    -> Servis loglari (journalctl):
    [OS-21] Python systemd servisi aktif! Sayac: 1

## OS-23 — dpkg inventory (Beratkursun)

Göreve özel gerçek test sonuçları ve açık sınırlamalar:
[OS-23 VERIFY](os-23/VERIFY.md). 12 görev testi başarılıdır; ortak testlerde
aynı temel hatalar vardır. Ubuntu ve öğrenci video kanıtı henüz sağlanmamıştır.
Önceki sys_audit.py sonucu OS-23'ün doğrulandığı anlamına gelmez.
