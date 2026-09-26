# Grant Atlas — deploy qo'llanmasi

QS 2027 top-500 universitetlari bo'yicha magistratura/PhD grantlari sayti. Sayt to'liq **statik**: server kodi, ma'lumotlar bazasi yoki build jarayoni kerak emas. `public/` papkasini istalgan statik hostingga qo'yish kifoya.

## Fayllar

| Fayl | Nima |
|---|---|
| `public/index.html` | Sayt (barcha grant, universitet, muddat ma'lumotlari ichida) — 7,5 MB |
| `public/people.json` | Professorlar va ularning maqolalari — sahifa ochilgandan keyin yuklanadi |
| `public/tr_uz.json`, `tr_ru.json`, `tr_en.json` | Ma'lumotlar tarjimasi (til tanlanganda yuklanadi) |
| `public/QS2027_Top500_Grantlar.xlsx` | Barcha grantlar Excel jadvalda — `sizning-domen.uz/QS2027_Top500_Grantlar.xlsx` manzilidan yuklab olinadi |
| `nginx.conf` | O'z serveringiz uchun nginx sozlamasi |
| `Dockerfile`, `docker-compose.yml` | Docker orqali ishga tushirish |
| `netlify.toml`, `vercel.json` | Netlify / Vercel uchun sozlama |

**Muhim:** saytni `index.html` ni ikki marta bosib (`file://`) ochmang — JSON fayllar yuklanmaydi. Har doim HTTP server orqali oching.

## 1-variant: Netlify / Vercel / Cloudflare Pages (eng oson)

**Netlify:** app.netlify.com → *Add new site → Deploy manually* → `public/` papkasini sudrab tashlang. Yoki `deploy/` papkasini GitHub'ga yuklab, repozitoriyni ulang — `netlify.toml` avtomatik o'qiladi.

**Vercel:** `deploy/` papkasini GitHub'ga yuklang → vercel.com → *Add New Project* → repozitoriyni tanlang. Framework: **Other**, Build command: bo'sh. `vercel.json` `public/` ni avtomatik oladi.

**Cloudflare Pages:** *Create project → Direct Upload* → `public/` papkasini yuklang.

**GitHub Pages:** `public/` ichidagi fayllarni repozitoriyning ildiziga (yoki `docs/` papkasiga) qo'ying → *Settings → Pages* → branch'ni tanlang. Eslatma: GitHub fayl hajmi chegarasi 100 MB, bizning eng katta fayl 7,5 MB — muammo yo'q.

## 2-variant: O'z serveringiz (nginx)

```bash
# serverda
sudo mkdir -p /var/www/grant-atlas
sudo cp -r public/* /var/www/grant-atlas/
sudo cp nginx.conf /etc/nginx/conf.d/grant-atlas.conf
sudo nano /etc/nginx/conf.d/grant-atlas.conf   # server_name ni o'z domeningizga almashtiring
sudo nginx -t && sudo systemctl reload nginx
```

HTTPS uchun: `sudo certbot --nginx -d sizning-domen.uz`

`nginx.conf` da gzip yoqilgan — 7 MB lik fayllar tarmoq orqali taxminan 1–1,5 MB bo'lib uzatiladi.

## 3-variant: Docker

```bash
cd deploy
docker compose up -d --build
# sayt: http://localhost:8080
```

Portni o'zgartirish uchun `docker-compose.yml` da `"8080:80"` ni tahrirlang.

## Tekshirish (deploydan oldin, lokal)

```bash
cd deploy/public
python3 -m http.server 8000
# brauzerda: http://localhost:8000
```

## Bilish kerak bo'lgan narsalar

- **"Mening rejam"** (★ bilan saqlangan universitet/grantlar) har bir foydalanuvchining **o'z brauzerida** (localStorage) saqlanadi — serverga yuborilmaydi, boshqa qurilmada ko'rinmaydi.
- Sayt faqat Google Fonts'dan shrift yuklaydi; boshqa tashqi skript yo'q.
- Ma'lumotlarni yangilash: yangi build'dan keyin `public/` dagi fayllarni almashtirish kifoya. `index.html` keshlanmaydi (`no-cache`), JSON fayllar 1 soat keshlanadi.
