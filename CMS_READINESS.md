---
site: ternakwalet.com
deploy: { host: unknown, cf_account: null, branch: main, deploy_hook: unknown }
url_patterns: { artikel: "/{slug}/", kota: "/kota/jasa-konsultasi-budidaya-walet-{kabupaten}-{provinsi}/", page: "/{slug}/", desain: "/desain/desain-gedung-walet-ukuran-{NxN}/" }
chrome: { header_lines: "120-206 (index.html, id=masthead, 87 lines)", footer_lines: "1037-1077 (index.html, id=colophon, 41 lines)", byte_identical: true }
analytics: { gtm: "GTM-5H2XHSX", ga4_property_id: "", tiktok: "CJA4IDBC77U5K7SPBH0G", fb_verify: "", adsense: "" }
brand: { primary: "#FFB100", font: "Poppins, Montserrat, Lato", logo: "/wp-content/uploads/2023/03/cropped-Hitam_dan_Oranye_Sederhana_Internet_Logo-removebg-preview.png", favicon: "/wp-content/uploads/2023/03/TERNAK WALET FAVICON-min.png" }
ecosystem_menu: absent
contact: { wa: "6287725260196", email: "sales@markaswalet.com" }
media: { local_mb: 327, hotlink: "blogspot:1114|yelpcdn:557|weebly:140" }
content: { articles: 53, kota_unique: 489, kota_admin_level: kabupaten }
cleanup:
  - "dev-domain leak: http://ternak-walet.test/ tertanam di 1065 file (schema JSON-LD, og:image, sebagian <img src>) -> rewrite ke domain produksi / R2"
  - "sitemap usang & bolong: post-sitemap hanya 3 dari 53 artikel; kota-sitemap1-3 & desain-sitemap tidak tertaut dari sitemap_index -> regen otomatis"
  - "hotlink eksternal: blogspot 1114, yelpcdn 557, weebly 140 -> rehome ke R2"
  - "media 327MB belum dioptimasi (ada original WhatsApp-Image-*), belum ada WebP/AVIF"
  - "wpforms di /kontak-kami & kotak search = mati (statis), kontak nyata via WhatsApp"
  - "taksonomi tipis: hanya kategori 'uncategorized', author tunggal 'Anggelina'"
blockers:
  - "BLOCKER#1 deploy: mekanisme repo->live tidak terekam di repo (tidak ada CNAME/.nojekyll/CI/config) -> butuh konfirmasi pemilik"
  - "BLOCKER#2 write-access CMS: menunggu #1; rekomendasi GitHub App org atau deploy key per-repo"
---

# Catatan integrasi CMS — ternakwalet.com

## Identitas
Static export WordPress 6.1.1 + tema Astra + Elementor 3.11.5, SEO oleh Rank Math.
Bukan WordPress hidup: tanpa PHP/DB/admin. 1.065 file HTML statis, repo 727 MB.
`wp-json/...` = snapshot statis, bukan API hidup.

## Chrome (header/footer)
Byte-identik di seluruh halaman. Batas di `index.html`:
- Header `#masthead`: baris **120–206** (87 baris).
- Footer `#colophon`: baris **1037–1077** (41 baris).
- Slot konten body ada di antara keduanya. `<head>` (s/d baris ~108) memuat field SEO **per-halaman**
  (title, meta description, canonical, og:*, schema JSON-LD) → template butuh slot di `<head>` juga,
  bukan hanya `<!-- CONTENT -->` di body.
Menu utama: BERANDA · PROFIL · Jenis Burung Walet · Proses Budidaya · Blog Artikel · LAYANAN · GALERI · KONTAK.

## Konten
- Artikel: ±53, slug flat di root (`/{slug}/`), bukan di bawah `/blog/`.
- Kota: 489 landing programmatic, level administrasi = kabupaten/kota, 34 provinsi.
  Dataset master `kabupaten<TAB>provinsi` sudah terparse dari `<title>` tiap halaman
  (format: "…Profesional di {Kabupaten}, {Provinsi} - Ternak Walet").
  Model yang diinginkan: 1 template + dataset (bukan 489 halaman manual).
- Desain: 5 (ukuran gedung 4×4 … 14×14), custom post type.

## URL / slug
Semua konten = folder + `/index.html` (pretty URL, trailing slash). Tidak ada `.html` telanjang.
Link menu internal ditulis root-relative tanpa trailing slash (`/profil-kami`).
Jangan ubah slug. Rewrite dev-domain (`http://ternak-walet.test/wp-content` → `/wp-content` atau R2)
BUKAN perubahan slug, jadi tidak perlu 301. Bila `/kota/...` dobel-kata atau taksonomi dirapikan → wajib peta 301.

## Deploy (BLOCKER)
Repo tidak mendeklarasikan cara tayang. Perlu konfirmasi pemilik: GitHub Pages / Cloudflare Pages /
manual-cPanel / server pull, plus branch & deploy hook. Sampai terjawab, "Publish" CMS belum punya target.
