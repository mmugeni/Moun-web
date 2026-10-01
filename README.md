# MOUN — Interior Design & Home Décor

A full-stack e-commerce and client management platform for MOUN, a luxury interior design and home décor brand based in Kigali, Rwanda.

---

## Features

- **Product Catalogue** — Browse curated home décor collections with filtering by category
- **Product Management** — Add, edit, price, hide and delete products from the admin dashboard, including photos, sizes and colours/scents
- **Quotation Requests** — Clients submit project details (service, budget, timeline) from the website and the team receives them by email
- **Order System** — Customers submit orders with payment receipts; admin tracks every order
- **Admin Dashboard** — Password + email code protected dashboard for orders, quotation requests and products
- **WhatsApp Integration** — Quick contact and order sharing via WhatsApp

---

## Tech Stack

**Frontend** — HTML, CSS, vanilla JavaScript. Hosted on Netlify (static, no build step).

**Backend** — Python, Flask, hosted on Google Cloud Run.
- PostgreSQL (Neon) for orders, quotations, products, product photos and admin sessions
- Firebase Storage for payment receipts (optional)
- Gmail SMTP for admin login codes and quotation emails

---

## Project Structure

```
MounInteriors/
├── index.html            # Website
├── script.js             # Website logic (loads products from the backend)
├── styles.css            # Website styles
├── images/               # Website photos
├── netlify.toml          # Netlify settings
└── backend/
    ├── app.py            # Flask API + admin dashboard
    ├── db.py             # Database tables and first-time product seed
    ├── seed_products.json# The original 8 products, copied into the database once
    ├── storage.py        # Firebase receipt upload
    ├── Dockerfile        # Cloud Run container
    └── .env.example      # Settings the backend needs
```

---

## How products work

- On first start, the backend creates the `products` table and copies in the 8 products from `seed_products.json`. After that, the admin dashboard is the source of truth.
- The website asks the backend for products (`GET /api/products`). If the backend can't be reached, it shows the built-in list in `script.js`, so the shop never goes empty.
- Photos uploaded in the dashboard are resized and stored in the database, and served from `/api/images/<id>`.
- Prices are optional. Leave a price empty and the product page shows no price.

---

## Deploying

### Backend (Google Cloud Run)

```bash
cd backend
gcloud run deploy moun-backend --source . --region europe-west1 --allow-unauthenticated
```

Set these environment variables on the Cloud Run service (see `backend/.env.example`):
`DATABASE_URL`, `ADMIN_PASSWORD`, `SECRET_KEY`, `SMTP_EMAIL`, `SMTP_PASSWORD`, `ADMIN_EMAIL`, `SITE_URL`,
and optionally `FIREBASE_CREDENTIALS`, `FIREBASE_STORAGE_BUCKET`.

If the backend URL changes, update `API_BASE` in `script.js`.

### Website (Netlify)

Drag the project folder onto Netlify Drop, or connect the Git repository. No build command; publish directory is the project root (already set in `netlify.toml`). Then set `SITE_URL` on the backend to the Netlify address.

---

## Local Development

**Backend**
```bash
cd backend
pip install -r requirements.txt
cp .env.example .env   # then fill it in
python app.py          # runs on http://localhost:8080, admin at /admin
```

**Website** — open `index.html` with VS Code Live Server. To use a local backend, set `API_BASE` in `script.js` to `http://localhost:8080`.

---

© 2026 MOUN — Kigali, Rwanda
