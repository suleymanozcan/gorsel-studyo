# Sürüm Yayınlama (Releasing)

Bu depo yalnızca dağıtım içindir. **İkili dosyalar (`.dmg`) depoya commit edilmez;**
GitHub **Releases** varlıkları (assets) olarak yüklenir. `.gitignore` `*.dmg` ve
`*.app` dosyalarını kasıtlı olarak yok sayar.

## Kanonik varlık adı

DMG varlığı **her zaman** şu adla yüklenmelidir:

```
GorselStudyo.dmg
```

İsteğe bağlı sağlama dosyası:

```
GorselStudyo.dmg.sha256
```

Bu ad; npm installer (`gorsel-studyo` paketi), bu Release'ler ve CI arasında
birebir aynı olmalıdır. `npm` paketi şu sabit URL'den indirir:

```
https://github.com/suleymanozcan/gorsel-studyo/releases/latest/download/GorselStudyo.dmg
```

## Akış (CI / fastlane)

Sürümler tercihen CI üzerinden (ör. GitHub Actions + fastlane) üretilir:

1. Xcode ile Release `.app` derlenir (arm64).
2. `.app`, **Developer ID Application** sertifikasıyla imzalanır, hardened runtime
   ile, ardından `notarytool` ile noter onayı alınır ve `stapler` ile mühürlenir.
3. `.app`'ten `GorselStudyo.dmg` oluşturulur (drag-to-install + `/Applications`
   sembolik bağı).
4. `shasum -a 256 GorselStudyo.dmg > GorselStudyo.dmg.sha256` üretilir.
5. Bir git etiketi (ör. `v1.0.0`) ile GitHub Release oluşturulur ve her iki varlık
   yüklenir.

> İmzalama / notarizasyon adımlarının ayrıntıları ve placeholder'lar için kaynak
> deposundaki `native/build/RELEASE.md` belgesine bakın.

## Elle yayınlama (CI yokken)

`gh` CLI ile (oturum açıkken):

```bash
# DMG hazır (imzalı + notarized) olduğunda, kaynak ağacında:
shasum -a 256 GorselStudyo.dmg > GorselStudyo.dmg.sha256

gh release create v1.0.0 \
  --repo suleymanozcan/gorsel-studyo \
  --title "Görsel Stüdyo 1.0.0" \
  --notes "İlk genel sürüm." \
  GorselStudyo.dmg GorselStudyo.dmg.sha256
```

Mevcut bir sürüme varlık eklemek için:

```bash
gh release upload v1.0.0 GorselStudyo.dmg --repo suleymanozcan/gorsel-studyo --clobber
```

## Yapılacaklar (TODO)

- [ ] Universal binary (arm64 + x86_64) — şu an yalnızca Apple Silicon (arm64).
- [x] CI iş akışını (GitHub Actions) ekle: derle → imzala → notarize → DMG → Release.
      Bkz. `.github/workflows/release.yml` ve README'deki **CI_SETUP** bölümü.
