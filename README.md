# FOODIVE — Online Food Order System (PHP)

The first version of **FOODIVE**, a food-ordering site: a customer signs up with
a mobile OTP step, browses restaurants and dishes, fills a cart and checks out,
while restaurants and delivery staff can apply to join and an admin panel
manages restaurants, menus, users and orders.

This is the earlier PHP prototype. It was later rebuilt, and finished, as
[Online-Restaurant-Food-Order-System-ASP.NET](https://github.com/sahil-ladola/Online-Restaurant-Food-Order-System-ASP.NET).

## What is actually in this repository

Worth reading before you clone:

- The repository **root** holds only the public landing page and its modals —
  `index.php`, `Footer.php`, `OTP.php`, `Forgot_password.php`, `ResetPass.php`.
  These are Bootstrap 5 markup with no back end behind them.
- **The application itself is inside `Project_demo.zip`** (~25 MB). Unzip it to
  get the `Customer/`, `Restaurant/`, `Delivery/` and `Admin/` folders and the
  shared `Conn.php`.
- **No SQL dump is included.** `Conn.php` expects a MySQL database named `fd`,
  but the schema has to be recreated by reading the queries in the code.

## Stack

- **PHP** with `mysqli`, procedural style
- **MySQL** (XAMPP / WAMP), database `fd`, connected as `root` with an empty
  password
- **Bootstrap 5** on the customer side; the admin panel ships its own template
  with DataTables, Chart.js/C3, FullCalendar and friends

## How far each part got

| Area | State |
| --- | --- |
| `index.php`, OTP / forgot-password / reset modals | Markup only — the OTP boxes and buttons are not wired to any sender or verifier |
| `Customer/Sign_up.php` → `reg_check.php` | Working. Rejects an existing email, enforces a password rule (8+ characters, an upper-case letter, a number, a special character), hashes with `password_hash()` and inserts into `tbl_reg` |
| `Customer/Login.php` → `LoginCheck.php` | Working. `password_verify()` against the stored hash, then a `Login` session with `reg_id`, `username` and `email` |
| `Customer/profile.php`, `uprofile.php`, `changepassword.php` | Working, reading and updating `tbl_reg` |
| `Customer/Home.php`, `Menu.php`, `Category.php`, `AddToCart.php`, `Checkout.php`, `PlaceOrder.php`, `Order.php` | **Static pages.** They check the session and render a hardcoded menu and cart; no dishes, cart rows or orders are read from or written to the database |
| `Restaurant/`, `Delivery/` | Onboarding forms (restaurant details, image upload, staff and referral info) as markup, not yet submitting anywhere |
| `Admin/` | The most complete area: login plus add / update / delete / list screens for restaurants, menus, categories, users and orders, working against `restaurant`, `dishes`, `res_category`, `users`, `users_orders`, `order` and `remark` |

The customer side and the admin side therefore use **two different sets of
tables** — `tbl_reg` for customers, `users` for the admin's user list — and were
never joined up.

## Running it

1. Install XAMPP (PHP 7.4 works; the admin template predates PHP 8) and start
   Apache and MySQL.
2. Unzip `Project_demo.zip` into `htdocs` so the app lives at
   `htdocs/Project_demo/`. The folder name matters: paths throughout are
   absolute, like `/Project_demo/Customer/Login.php`.
3. Create a MySQL database named `fd`, or edit `Conn.php` to point at another
   one, and create the tables the code expects (`tbl_reg` at minimum for sign-up
   and login).
4. Open `http://localhost/Project_demo/`.

## Credits

The admin panel is built on a third-party PHP admin template bundled inside the
zip; its original author's notes are still in
`Admin/js/lib/01 LOGIN DETAILS & PROJECT INFO.txt`. The customer-facing pages,
the registration and login flow and the FOODIVE design are this project's own.

## Known limitations

- **Queries are built by string interpolation** of `$_POST` and `$_SESSION`
  values, so the pages that do touch the database are open to SQL injection.
  Passwords are the one thing handled properly — they are hashed.
- Database credentials are hardcoded in `Conn.php` and repeated in
  `Customer/reg_check.php`.
- The ordering flow — menu, cart, checkout, order history — is unfinished, so
  the site cannot actually take an order end to end.
- OTP verification is UI only; any code is accepted because nothing checks it.
- The application lives in a committed zip rather than in version control, so
  its history is not tracked.
