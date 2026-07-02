# Atlas Panel

Atlas یک پنل Cloudflare Workers برای مدیریت کاربران، تولید لینک‌های VLESS، کنترل مصرف، مدیریت اتصال همزمان، مانیتورینگ درخواست‌ها و کار با دیتابیس D1 است.

## قابلیت‌های اصلی

- مدیریت کاربر با حجم، زمان اعتبار، سقف درخواست و وضعیت فعال/غیرفعال.
- کنترل اتصال همزمان برای هر کاربر.
- تولید لینک ساب‌اسکریپشن متنی و کانفیگ مستقیم VLESS.
- پشتیبانی از Cloudflare D1 برای ذخیره‌سازی اطلاعات پنل و کاربران.
- مانیتورینگ مصرف روزانه و ماهانه درخواست‌ها از Cloudflare GraphQL.
- انتخاب و مدیریت Clean IP.
- رابط کاربری واکنش‌گرا با پشتیبانی از Dark Mode.
- امکان تغییر رمز پنل و مدیریت تنظیمات از داخل پنل.

## فایل‌های مهم پروژه

```text
atlas.js                  Worker اصلی پنل Atlas
atlas-deployer.js         Worker نصب‌کننده و مدیریت دیپلوی
ips.txt                   لیست IPهای آماده برای انتخاب‌گر IP
wrangler.toml             تنظیمات دیپلوی پنل اصلی
wrangler.deployer.toml    تنظیمات دیپلوی نصب‌کننده
package.json              اسکریپت‌های کمکی Wrangler
```

## آماده‌سازی سورس برای آپدیت خودکار

اگر می‌خواهید قابلیت آپدیت خودکار داخل پنل کار کند، فایل‌های پروژه را در ریپوی خودتان قرار دهید و مقدار `ATLAS_SOURCE_URL` را در Cloudflare Worker به آدرس خام فایل `atlas.js` همان ریپو تنظیم کنید.

نمونه مقدار:

```text
https://raw.githubusercontent.com/YOUR_GITHUB_USERNAME/atlas/refs/heads/main/atlas.js
```

برای لیست IP نیز در صورت نیاز می‌توانید مسیر خام `ips.txt` را در کد یا ریپو نگه دارید.

## دیپلوی سریع با Wrangler

1. وارد Cloudflare شوید و یک API Token با دسترسی‌های لازم بسازید.
2. Wrangler را نصب و لاگین کنید.
3. یک D1 Database بسازید.
4. مقدار `database_id` را در `wrangler.toml` جایگزین کنید.
5. Worker اصلی را دیپلوی کنید.
6. در اولین ورود به مسیر `/panel` رمز پنل را تنظیم کنید.

دستورهای پیشنهادی:

```bash
npm install
npx wrangler login
npx wrangler d1 create atlas-db
npx wrangler deploy --config wrangler.toml
```

برای دیپلوی نصب‌کننده:

```bash
npx wrangler deploy --config wrangler.deployer.toml
```

## مسیرهای اصلی

```text
/panel       ورود و مدیریت پنل
/login       صفحه ورود
/sub/<user>  لینک ساب‌اسکریپشن متنی
/feed/<user> خروجی جایگزین ساب‌اسکریپشن
/status/<user> صفحه وضعیت کاربر
```

## نکته امنیتی

توکن Cloudflare را فقط در محیط امن استفاده کنید. برای دیپلوی نهایی، دسترسی توکن را حداقلی نگه دارید و پس از اتمام کار در صورت عدم نیاز آن را revoke کنید.
