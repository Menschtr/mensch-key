# Mensch Hub - Key System

Mensch Hub için **5 dakikada bir yenilenen** key sistemi. Tamamen ücretsiz, 7/24 çalışır (GitHub Actions + GitHub Pages).

## Nasıl çalışıyor
- **GitHub Actions** eklenen bir GitHub secret (`KEY_SEED`) ve 5 dakikalık zaman diliminden SHA256 ile key üretir, `current-key.json` dosyasına yazar.
- **GitHub Pages** bu JSON'u site olarak gösterir (`https://<kullaniciad>.github.io/mensch-key/`) — key + geri sayım.
- **Script** (`MenschV3.3.lua`) aynı JSON'u `HttpService:GetAsync` ile çeker, girilen key ile karşılaştırır, süresi dolmuşsa reddeder.

## Kurulum (bir kez)
1. Bu klasörün içeriğini GitHub'da yeni bir **public** repoya yükleyin (örn. adı `mensch-key`).
2. Repo → **Settings → Secrets and variables → Actions → New repository secret**:
   - Name: `KEY_SEED`
   - Value: uzun, rastgele bir metin (örn. 32 karakterlik). Bu sır repo'ya hiç yazılmamalı, sadece secret olarak kalmalı.
3. Repo → **Actions** → `rotate-key` → **Run workflow** (ilk key'i oluşturur). Bundan sonra her 5 dk'da otomatik döner.
4. Repo → **Settings → Pages** → Source: `Deploy from a branch`, Branch: `main`, folder: `/` (root) → Save. (Site kökü repo kökü olmalı, böylece `current-key.json` doğru adreste kalır.)
5. Adresi aç: `https://<kullaniciad>.github.io/mensch-key/` → key görünmelidir.

## Script tarafı
`MenschV3.3.lua` içinde en üstte:

```lua
local KEY_URL = "https://GITHUB_KULLANICI.github.io/mensch-key/current-key.json"
```

`GITHUB_KULLANICI` yerine kendi GitHub kullanıcı adını yaz. Repo adı farklıysa onu da değiştir.

## Doğrulama
1. Scripti çalıştır → key ekranı çıkar, menü açılmaz.
2. Siteden key'i kopyala → scripte yapıştır → **Doğrula** → menü açılır.
3. 5 dk sonra eski key ile tekrar dene → "Geçersiz" der.

## Notlar
- Key her 5 dk'da bir HERKES için aynıdır (tasarım gereği); amaç casual paylaşımı değil, gönüllü key akışını sağlamaktır. Korsan koruması kurulu bir sistemde de tam sağlanamaz.
- GitHub secret (`KEY_SEED`) sırrının scriptte veya repoda OLMAMASI önemli; sadece Actions secret'ında duracak.
- JJsploit/executor ortamında `HttpService:GetAsync` çalışması gerekir. Çalışmazsa mesaj yaz, alternatif yol bakarız.