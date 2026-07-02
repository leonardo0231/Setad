# Atlas Deployment Guide

این راهنما برای دیپلوی تمیز Atlas روی Cloudflare Workers نوشته شده است.

## 1. پیش‌نیازها

- حساب Cloudflare
- Node.js و npm
- دسترسی ترمینال
- فایل‌های پروژه Atlas

## 2. نصب وابستگی‌ها

```bash
npm install
```

## 3. ورود به Cloudflare با Wrangler

```bash
npx wrangler login
```

بعد از باز شدن مرورگر، دسترسی Wrangler را تأیید کنید.

## 4. ساخت D1 Database

```bash
npx wrangler d1 create atlas-db
```

خروجی شامل `database_id` خواهد بود. همان مقدار را در فایل `wrangler.toml` جایگزین این مقدار کنید:

```toml
database_id = "REPLACE_WITH_YOUR_D1_DATABASE_ID"
```

binding باید همین مقدار بماند:

```toml
binding = "DB"
```

کد پروژه دیتابیس را با `env.DB` می‌خواند، پس تغییر نام binding باعث خطا می‌شود.

## 5. تنظیم URL سورس برای آپدیت خودکار

اگر می‌خواهید آپدیت خودکار داخل پنل فعال بماند، پروژه را در یک ریپوی GitHub با نام دلخواه، مثلاً `atlas`، قرار دهید و URL خام فایل‌ها را در `wrangler.toml` اصلاح کنید:

```toml
ATLAS_SOURCE_URL = "https://raw.githubusercontent.com/YOUR_GITHUB_USERNAME/atlas/refs/heads/main/atlas.js"
ATLAS_IPS_URL = "https://raw.githubusercontent.com/YOUR_GITHUB_USERNAME/atlas/refs/heads/main/ips.txt"
```

اگر فعلاً آپدیت خودکار نمی‌خواهید، می‌توانید این مقادیر را بعداً اصلاح کنید. پنل اصلی بدون این قابلیت هم دیپلوی و اجرا می‌شود.

## 6. دیپلوی پنل اصلی

```bash
npx wrangler deploy --config wrangler.toml
```

بعد از دیپلوی، آدرس Worker نمایش داده می‌شود. مسیر پنل:

```text
https://YOUR_WORKER.YOUR_SUBDOMAIN.workers.dev/panel
```

در اولین ورود، پنل صفحه تنظیم رمز اولیه را نشان می‌دهد.

## 7. دیپلوی نصب‌کننده Atlas، اختیاری

اگر نصب‌کننده جداگانه می‌خواهید:

```bash
npx wrangler deploy --config wrangler.deployer.toml
```

در فایل `wrangler.deployer.toml` مقدار `ATLAS_SOURCE_URL` باید به فایل خام `atlas.js` در ریپوی خودتان اشاره کند، چون نصب‌کننده هنگام ساخت پنل جدید، سورس پنل را از آن آدرس دریافت می‌کند.

## 8. تنظیم توکن Cloudflare داخل پنل

برای قابلیت‌هایی مثل آپدیت خودکار، ری‌استارت و مانیتورینگ Cloudflare، داخل پنل توکن Cloudflare را تنظیم کنید.

دسترسی‌های پیشنهادی توکن:

- Workers Scripts: Edit
- Workers Routes/Subdomain: Edit
- D1: Edit
- Account Settings: Read
- Account Analytics: Read

توکن را عمومی نکنید و داخل GitHub قرار ندهید.

## 9. تست بعد از دیپلوی

این مسیرها را بررسی کنید:

```text
/panel
/login
/sub/<username>
/status/<username>
```

چک‌لیست سریع:

- ورود به پنل بدون خطای D1 انجام شود.
- ساخت کاربر جدید کار کند.
- لینک VLESS با مسیر `/atlas-ws` ساخته شود.
- مسیر `/status/<username>` باز شود.
- در خروجی پروژه هیچ عبارت قدیمی یا متن ناخواسته باقی نمانده باشد.

## 10. آپدیت دستی بعدی

برای آپدیت دستی، فایل `atlas.js` را اصلاح کنید و دوباره اجرا کنید:

```bash
npx wrangler deploy --config wrangler.toml
```

اگر فقط نصب‌کننده تغییر کرد:

```bash
npx wrangler deploy --config wrangler.deployer.toml
```
