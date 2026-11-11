# RecoverKit for PHP

The RecoverKit server SDK and read-only connector, for Laravel, WordPress/WooCommerce, CodeIgniter, Symfony or plain PHP.
This repository is generated from [Light8749/recoverkit](https://github.com/Light8749/recoverkit) (`sdk/php`); please open issues there.

```bash
composer require light8749/recoverkit
```

Add the three values from the business's RecoverKit dashboard (**Developers** page) to your `.env`:

```
RK_PUBLIC_KEY=rk_pub_...
RK_SECRET_KEY=rk_sec_...      # server only
RK_ENDPOINT=https://the-business-recoverkit-server
```

## Record real sales

```php
$rk = new RecoverKit(getenv('RK_PUBLIC_KEY'), getenv('RK_SECRET_KEY'), getenv('RK_ENDPOINT'));

$rk->identify(['email' => $user->email, 'phone' => $user->phone, 'firstName' => $user->first_name]); // after sign-up or login
$rk->trackPurchase(['email' => $user->email, 'amount' => 150000, 'ref' => $order->reference]);     // a real sale, in kobo
$rk->walletFunded(['email' => $user->email, 'amount' => 500000]);                                  // a deposit is not a sale
```

Calls time out after 3 seconds and never throw, so RecoverKit can't break a checkout.

## Bring in past customers (read-only)

```bash
vendor/bin/recoverkit-connector setup --app=.    # reads table names only, suggests a mapping, dry run, asks before saving
vendor/bin/recoverkit-connector sync --app=.     # first run sends history, then only what's new
```

Cron, every 15 minutes: `*/15 * * * * cd /path/to/app && vendor/bin/recoverkit-connector sync --app=. >> recoverkit-sync.out 2>&1`

It finds your database login in your app's config (Laravel, WordPress and WooCommerce, CodeIgniter, Django, Rails, Prisma, Spring Boot, ASP.NET, or any `.env` with `DATABASE_URL`), uses a read-only session, logs every query to `recoverkit-connector.log`, and sends only hashes, dates and amounts.

Full docs: `/docs` on the business's RecoverKit server. MIT License.
