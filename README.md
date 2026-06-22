# wajik-anime-api

REST API streaming dan download Anime subtitle Indonesia dari berbagai sumber.

Fork ini dipakai sebagai backend scraper untuk [ran-netflix](https://github.com/qcxint-crypto/ran-netflix) (lewat env var `SCRAPER_API_URL`). Perbaikan dari upstream:
- Domain otakudesu yang basi (`otakudesu.best` → `otakudesu.blog`)
- `getHTML` sekarang ikut redirect otomatis kalau domain pindah lagi
- Port server baca dari env var `PORT` (dibutuhkan Railway/Render)

# Sumber:

API ini unofficial jadi ga ada kaitan dengan sumber yang tersedia...

1. otakudesu: https://otakudesu.blog
2. kuramanime: https://v8.kuramanime.tel

- domain sering berubah jangan lupa pantau terus untuk edit url ada di di "src/configs/{source}.config.ts"

# Installasi App

- install NodeJS 20 || >=22
- Jalankan perintah di terminal

```sh
# clone repo
git clone https://github.com/qcxint-crypto/wajik-anime-api.git

# masuk repo
cd wajik-anime-api

# install dependensi
npm install

# menjalankan server mode development
npm run dev
```

# Build App

```sh
# build
npm run build

# menjalankan server
npm start
```

- Server akan berjalan di http://localhost:3001 (atau `$PORT` kalau env var itu di-set)

# Deploy ke Railway

> **Jangan deploy ke Vercel.** Sudah diverifikasi: otakudesu.blog mengembalikan HTTP 403 untuk request yang datang dari IP datacenter Vercel (kemungkinan diblokir Cloudflare/WAF situsnya). Railway/Render belum diblokir.

1. Buka https://railway.app, login pakai akun GitHub.
2. **New Project** → **Deploy from GitHub repo** → pilih repo ini (`wajik-anime-api`). Izinkan akses Railway ke repo kalau diminta.
3. Railway otomatis mendeteksi Node.js dan menjalankan `npm install` → `npm run build` → `npm start` (sesuai script di `package.json`). Tunggu sampai status deployment "Active".
4. Buka tab **Settings** pada service tersebut → bagian **Networking** → klik **Generate Domain** untuk mendapat URL publik, misalnya `https://wajik-anime-api-production.up.railway.app`.
5. Tes URL itu di browser/curl: `<url>/otakudesu/ongoing?page=1` harus mengembalikan JSON daftar anime ongoing.
6. Set URL itu sebagai env var `SCRAPER_API_URL` pada project Vercel yang memakai API ini, lalu redeploy.

Render.com punya flow yang serupa (New → Web Service → connect repo → build command `npm run build`, start command `npm start`) kalau Railway minta kartu kredit untuk plan-nya.

# Routes

| Endpoint  | Description                                                                                      |
| --------- | ------------------------------------------------------------------------------------------------ |
| /{sumber} | Deskripsi ada di response sesuai dengan sumber, gunakan ext JSON Parser jika menggunakan browser |

### Contoh request

```js
(async () => {
  const response = await fetch("http://localhost:3001/otakudesu/ongoing?page=1");
  const result = await response.json();

  console.log(result);
})();
```

### Contoh response

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "message": "",
  "data": {
    "animeList": [
      {
        "title": "Dr. Stone Season 3 Part 2",
        "poster": "https://otakudesu.cloud/wp-content/uploads/2024/01/Dr.-Stone-Season-3-Part-2-Sub-Indo.jpg",
        "episodes": "11",
        "animeId": "drstn-s3-p2-sub-indo",
        "latestReleaseDate": "05 Jan",
        "releaseDay": "Jum'at",
        "otakudesuUrl": "https://otakudesu.cloud/anime/drstn-s3-p2-sub-indo/"
      },
      {"..."}
    ]
  },
  "pagination": {
    "currentPage": 1,
    "prevPage": null,
    "hasPrevPage": false,
    "nextPage": 2,
    "hasNextPage": true,
    "totalPages": 4
  },
}
```
