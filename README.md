# Mensch Hub - Key System

Mensch Hub için **her 5 dakikada bir yenilenen** key sistemi. Tamamen ücretsiz, 7/24 çalışır, **internet gerektirmez**.

## Nasıl çalışıyor
- Key **istemcide hesaplanır**: `slot = floor(now / 300)` ve `key = hex(djb2(SEED|slot)) + hex(djb2(slot|SEED))` → büyük harf, ilk 10 karakter.
- Aynı `SEED` site JavaScript'ine ve script'e **obfusce** edilmiş hâliyle gömülüdür; ham değer yalnızca GitHub secret (`KEY_SEED`) olarak durur.
- **Script** key'i internet olmadan doğrular (slot - 1 toleranslı): "Sunucuya ulaşılamadı" hataları yok.
- **GitHub Actions** (`rotate-key`) aynı djb2 algoritmasıyla `current-key.json`'u üretir — bu yalnızca **referans/kayıt** amaçlıdır; site ve script key'i kendisi hesaplar.
- **GitHub Pages**: `https://<kullanici>.github.io/mensch-key/` → key + geri sayım + copy butonu. Site key'i asla "--" göstermez.

## Kurulum (bir kez)
1. Bu klasörün içeriğini GitHub'da **public** repoya yükleyin (örn. adı `mensch-key`).
2. Repo → **Settings → Secrets and variables → Actions → New repository secret**:
   - Name: `KEY_SEED`
   - Value: `index.html` ve `MenschV3.3.lua` içindeki obfusce seed'in **ham hâli** (aşağıya bak).
3. Repo → **Actions** → `rotate-key` → **Run workflow** (referans `current-key.json` üretilir).
4. Repo → **Settings → Pages** → Source: `Deploy from a branch`, Branch `main`, folder `/` (root) → Save.
5. Adresi aç: `https://<kullanici>.github.io/mensch-key/` → key görünür.

> **Seed senkronu:** `MenschV3.3.lua` içindeki `KEY_SEED_ENC` + `keySeed()` (ters çevir, her bayta 37 ekle) ve `index.html` içindeki `SEED_ENC` aynı seed'i üretir. Bu seed'i deşifre edip ham hâlini `KEY_SEED` secret'ına yaz. Secret olmasa bile sistem çalışır; sadece referans JSON sitedeki key ile örtüşmez.

## Doğrulama
1. Scripti çalıştır → key ekranı gelir.
2. Sitede **Copy key** (veya kod satırına tıkla) → key kopyalanır.
3. Scripte yapıştır → **Verify** → menü açılır.
4. 5 dk geçtikten sonra eski key reddedilir.

## Script tarafı
- `MenschV3.3.lua`: `KEY_SITE = "https://Menschtr.github.io/mensch-key"` — **Copy key website link** butonu bu site linkini kopyalar.
- `GITHUB_KULLANICI` kendi kullanıcı adınla değişmeden kalırsa key sistemi devre dışı kalır (menü direkt açılır).

## Notlar
- Key her 5 dk'da bir HERKES için aynıdır; amaç gönüllü key akışıdır, korsan koruması kurulu bir sistemde tam sağlanamaz.
- Seed obfusced koddan teorik olarak çıkarılabilir çünkü scripti herkes indirir. Bu, "key = ücretsiz ama ziyaret edilmeli"ni sağlayan bilinçli bir tercihtir.
- Ham seed'i repoya (README, kod, commit) YAZMAYIN; yalnızca secret'ta ve obfusce hâli kodlarda bulunur.