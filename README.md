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

# Deploy ke Render (gratis, tidak perlu kartu kredit)

> **Jangan deploy ke Vercel.** Sudah diverifikasi dua kali (Node serverless & Edge Runtime): otakudesu.blog mengembalikan HTTP 403 untuk request yang datang dari IP Vercel apapun jenisnya — diblokir di level network/WAF, bukan soal kode. Render belum diblokir.

1. Buka https://render.com, login/daftar pakai akun GitHub.
2. **New +** → **Web Service** → **Build and deploy from a Git repository** → pilih repo ini (`wajik-anime-api`). Izinkan akses Render ke repo kalau diminta.
3. Render biasanya auto-detect lewat `render.yaml` di repo ini (Build Command `npm run build`, Start Command `npm start`, plan **Free**). Kalau diminta isi manual, samakan dengan itu.
4. Klik **Create Web Service**, tunggu build & deploy selesai (~2-3 menit). Status jadi "Live".
5. URL publik langsung tersedia di bagian atas dashboard, formatnya `https://wajik-anime-api.onrender.com`.
6. Tes URL itu di browser/curl: `<url>/otakudesu/ongoing?page=1` harus mengembalikan JSON daftar anime ongoing.
7. Set URL itu sebagai env var `SCRAPER_API_URL` pada project Vercel yang memakai API ini, lalu redeploy.

> Catatan: di plan Free, service akan "tidur" setelah ~15 menit tanpa request dan butuh ~30-50 detik untuk bangun lagi saat ada request pertama setelah idle (cold start). Ini normal untuk free tier, bukan error.

Railway juga bisa dipakai dengan flow serupa (New Project → Deploy from GitHub repo → Generate Domain) kalau sudah punya plan berbayar di sana.

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
