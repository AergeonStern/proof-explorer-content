# Proof Explorer — İçerik Repo'su

Bu repo, **Proof Explorer** Android uygulamasının dinamik içerik kaynağıdır.  
Uygulamayı güncellemeden yeni teorem ve ispatlar ekleyebilirsin.

---

## 📁 Repo yapısı

```
proof-explorer-content/
├── catalog.json          ← Tüm teoremler ve ispat listesi (TEK kaynak)
└── proofs/
    ├── pythagoras_euclid.html
    ├── pythagoras_trig.html
    ├── sqrt2_geometric.html
    └── ...               ← Senin hazırladığın HTML ispat dosyaları
```

---

## 🚀 GitHub'da nasıl kurulur?

1. GitHub'da **proof-explorer-content** adında yeni bir public repo oluştur.
2. Bu `catalog.json` ve `proofs/` klasörünü o repo'ya yükle.
3. Android projesinde şu satırı bul ve güncelle:

```kotlin
// ContentRepository.kt
const val CATALOG_URL =
    "https://cdn.jsdelivr.net/gh/YOUR_GITHUB_USER/proof-explorer-content@main/catalog.json"
```

`YOUR_GITHUB_USER` yerine kendi GitHub kullanıcı adını yaz.  
Örnek: `https://cdn.jsdelivr.net/gh/egeturann/proof-explorer-content@main/catalog.json`

---

## ➕ Yeni ispat nasıl eklenir?

### Adım 1 — HTML dosyasını hazırla

`proofs/` klasörüne yeni bir HTML dosyası ekle.  
Dosya adı: `<teorem_id>_<kısa_açıklama>.html`  
Örnek: `pythagoras_trig.html`

**Her HTML ispat dosyasının başına bu bloğu ekle:**

```html
<script>
// Android köprüsü — tema ve aksan rengi okur
(function() {
  try {
    if (!Android.isDarkMode()) document.body.classList.add('light');
    const hex = Android.getAccentHex();
    document.documentElement.style.setProperty('--accent', hex);
  } catch(e) {
    // Tarayıcıda test edilirken hata vermemesi için
  }
})();
</script>
```

### Adım 2 — catalog.json'a ekle

İlgili teoremin `proofs` listesine yeni bir giriş ekle:

```json
{
  "name": "Trigonometrik İspat",
  "description": "sin²θ + cos²θ = 1 özdeşliğinden türetme",
  "url": "https://cdn.jsdelivr.net/gh/KULLANICI/proof-explorer-content@main/proofs/pythagoras_trig.html"
}
```

### Adım 3 — GitHub'a push et

```bash
git add catalog.json proofs/pythagoras_trig.html
git commit -m "Pisagor: trigonometrik ispat eklendi"
git push
```

**jsDelivr CDN** birkaç dakika içinde güncellenir.  
Uygulama bir sonraki açılışta yeni içeriği çeker.

---

## 🆕 Tamamen yeni teorem nasıl eklenir?

`catalog.json`'a yeni bir teorem objesi ekle:

```json
{
  "id": "cantor_diagonal",
  "name": "Cantor'un Köşegen Argümanı",
  "category": "num",
  "description": "Gerçel sayılar sayılamaz",
  "statement": "|ℝ| > |ℕ|",
  "difficulty": 2,
  "tags": ["sonsuz", "kardinallik", "sayılamaz"],
  "proofs": [
    {
      "name": "Köşegen Yöntemi",
      "description": "Her listelemenin eksik olduğunun gösterimi",
      "url": "https://cdn.jsdelivr.net/gh/KULLANICI/proof-explorer-content@main/proofs/cantor_diagonal.html"
    }
  ]
}
```

Kategori değerleri: `"geo"`, `"num"`, `"alg"`, `"cal"`, `"kom"`, `"lin"`  
Zorluk değerleri: `1` (Temel), `2` (Orta), `3` (İleri)

---

## 🔄 jsDelivr CDN cache temizleme

Acil güncelleme gerekirse (CDN bazen 24 saat cache'ler):

```
https://purge.jsdelivr.net/gh/KULLANICI/proof-explorer-content@main/catalog.json
```

Veya commit hash ile doğrudan referans ver:

```
https://cdn.jsdelivr.net/gh/KULLANICI/proof-explorer-content@abc1234/catalog.json
```

---

## 📱 Uygulama davranışı

| Durum | Ne olur? |
|-------|----------|
| İnternet var | catalog.json indirilir, cache güncellenir |
| İnternet yok | Cache'deki son catalog kullanılır |
| Cache yok + internet yok | Sadece uygulamaya gömülü ispatlar görünür |
| Teoremin 1 ispatı var | Direkt açılır |
| Teoremin 2+ ispatı var | Seçim ekranı açılır |
| ispat URL'i `asset://` | Offline HTML dosyasından yüklenir |
| ispat URL'i `https://` | CDN'den indirilir, cache'lenir |

---

## 💡 İpuçları

- **Yerleşik ispatlar** (`asset://proofs/xxx.html`) her zaman çalışır, internet gerektirmez.
- **CDN ispatları** ilk açılışta internete ihtiyaç duyar, sonraki açılışlarda cache'den gelir.
- Bir ispat dosyasını değiştirirsen CDN cache'i temizle veya dosyaya versiyon ekle.
- HTML dosyaların içinde dışarıdan kaynak (CDN, Google Fonts vb.) **çekme** — tamamen self-contained olsun, offline çalışsın.
