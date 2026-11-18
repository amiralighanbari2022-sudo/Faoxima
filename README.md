# Faoxima - Telegram VPN Bot Panel

ربات تلگرام برای مدیریت پنل‌های VPN (Marzban, x-ui, Hiddify, Marzneshin, WGDashboard, IBSng, Mikrotik و ...)

## 🚀 Deploy در Railway

### پیش‌نیازها
- اکانت [GitHub](https://github.com)
- اکانت [Railway](https://railway.app) (با GitHub لاگین کنید)
- توکن ربات تلگرام (از @BotFather)

### مراحل Deploy

#### 1. Fork این ریپو
ریپو را در GitHub خودتان Fork کنید.

#### 2. ساخت پروژه در Railway
1. در Railway: **New Project** → **Empty Project**
2. نام پروژه: `faoxima`
3. **New** → **Database** → **Add MySQL**
4. در تب **Variables** دیتابیس MySQL، این متغیرها را اضافه کنید:
   ```
   MYSQL_DATABASE=faoxima
   MYSQL_USER=faoxima
   MYSQL_PASSWORD=یک-رمز-قوی-بسازید
   MYSQL_ROOT_PASSWORD=همون-رمز-بالا
   ```
5. در تب **Settings** دیتابیس، در بخش **Volumes** یک Volume با مسیر `/var/lib/mysql` بسازید.
6. **مهم**: نام سرویس دیتابیس را دقیقاً `MySQL` بگذارید.

#### 3. اضافه کردن سرویس ربات
1. در پروژه Railway: **+ New** → **GitHub Repo** → ریپوی Fork شده خودتان را انتخاب کنید.
2. Railway به صورت خودکار `nixpacks.toml` را می‌خواند و PHP 8.2 + extensions را نصب می‌کند.
3. در تب **Variables** سرویس ربات، این متغیرها را اضافه کنید:
   ```
   COMPOSER_NO_SECURITY_BLOCKING=1
   RAILPACK_PHP_EXTENSIONS=gd,zip,mysqli,pdo_mysql,intl,soap,bcmath,sockets
   ```
4. در تب **Settings** → **Networking** → **Generate Domain** بزنید تا آدرس عمومی بگیرید (مثال: `https://faoxima-xxxx.up.railway.app`)

#### 4. اجرای Installer
1. در مرورگر به آدرس زیر بروید:
   ```
   https://آدرس-ریلوی-شما/installer/
   ```
2. فرم را پر کنید:
   - **آیدی عددی ادمین**: از @userinfobot بگیرید
   - **توکن ربات**: از @BotFather
   - **اطلاعات دیتابیس**: همان مقادیری که در MySQL Variables گذاشتید
   - **هاست دیتابیس**: `mysql.railway.internal` (سخت‌کد شده در installer)
   - **آدرس وب‌هوک ربات**: `https://آدرس-ریلوی-شما/index.php`
3. دکمه **شروع نصب** را بزنید.
4. پیام `✅ ربات با موفقیت نصب شد` را باید ببینید.

#### 5. ست کردن Webhook تلگرام
در مرورگر این آدرس را باز کنید:
```
https://api.telegram.org/bot<TOKEN_شما>/setWebhook?url=https://آدرس-ریلوی-شما/index.php
```
باید `{"ok":true,"result":true,"description":"Webhook was set"}` برگرداند.

#### 6. تست
در تلگرام به ربات `/start` بفرستید. منوی ربات باید بیاید.

---

## 📁 ساختار پروژه

```
.
├── index.php              # Entry point
├── config.php             # Database config (پر می‌شود توسط installer)
├── table.php              # Schema & migrations
├── composer.json          # PHP dependencies
├── nixpacks.toml          # Railway build config
├── railway.json           # Railway deploy config
├── Procfile               # Process definition
├── version                # Version file
├── installer/             # Web installer (auto-deleted after install)
├── re/                    # Refactored core
│   ├── rx/
│   │   ├── index/         # Main bot logic
│   │   ├── function/      # Helper functions
│   │   ├── admin/         # Admin panel
│   │   └── keyboard/      # Keyboard layouts
├── api/                   # REST API for Mini App
├── app/                   # Mini App frontend
├── panel/                 # Admin web panel
├── cronbot/               # Scheduled tasks
├── payment/               # Payment gateways
├── vendor/                # Composer dependencies (gitignored)
└── storage/               # Cache & logs (gitignored)
```

---

## ⚙️ پیکربندی‌های مهم

### متغیرهای محیطی Railway (Service Variables)
| متغیر | مقدار | توضیح |
|--------|--------|-------|
| `COMPOSER_NO_SECURITY_BLOCKING` | `1` | برای نصب composer در Railway |
| `RAILPACK_PHP_EXTENSIONS` | `gd,zip,mysqli,pdo_mysql,intl,soap,bcmath,sockets` | اکستنشن‌های PHP مورد نیاز |

### دیتابیس
- **Host**: `mysql.railway.internal` (داخلی Railway)
- **Port**: 3306 (پیش‌فرض)
- نام دیتابیس، یوزر، پسورد: از Variables دیتابیس MySQL

---

## 🔧 توسعه محلی

```bash
# نصب وابستگی‌ها
composer install

# اجرای سرور توسعه
php -S localhost:8000 -t .
```

سپس به `http://localhost:8000/installer/` بروید برای نصب.

---

## 📝 نکات مهم

1. **پوشه `installer/`** بعد از نصب موفق به‌صورت خودکار حذف می‌شود (برای امنیت).
2. **Webhook URL** باید HTTPS باشد (Railway به صورت پیش‌فرض HTTPS می‌دهد).
3. **دیتابیس MySQL** باید Volume داشته باشد تا داده‌ها با ری‌استارت پاک نشوند.
4. **نام سرویس دیتابیس** باید `MySQL` باشد تا hostname داخلی `mysql.railway.internal` کار کند.
5. اگر پس از نصب ربات جواب نداد، `config.php` را چک کنید که `mysql.railway.internal` ست شده باشد.

---

## 🆘 عیب‌یابی رایج

| مشکل | راه‌حل |
|--------|--------|
| بیلد fail می‌شود | مطمئن شوید `nixpacks.toml` و `composer.json` در ریشه پروژه هستند |
| دیتابیس connect نمی‌شود | چک کنید نام سرویس MySQL است و Variables درست ست شده |
| Installer باز نمی‌شود | مطمئن شوید `installer/index.php` وجود دارد و HTTPS فعال است |
| ربات پیام نمی‌دهد | Webhook را چک کنید: `https://api.telegram.org/bot<TOKEN>/getWebhookInfo` |
| Extensions گم شده | `RAILPACK_PHP_EXTENSIONS` در Variables سرویس ربات ست شده باشد |

---

## 📄 لایسنس
MIT License - به فایل [LICENSE](LICENSE) مراجعه کنید.

---

## 🔗 لینک‌ها
- [مخزن اصلی](https://github.com/x4gpanell/Faoxima)
- [کانال تلگرام](https://t.me/faoxima)
- [راهنمای Railway](https://docs.railway.app)