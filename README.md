SQL queries generated by Eloquent ORM reference

[Get](#get)

[Where](#where)

[Create](#create)

[Update](#update)

[Delete](#delete)

[One to One](#one-to-one)

[One to Many](#one-to-many)

[Many to Many](#many-to-many)

[Aggregate functions](#aggregate-functions)

[Miscellaneous](#miscellaneous)
&nbsp;


## Get

`get` (`all`)

<pre lang=php>User::get()</pre>
<pre lang=sql>SELECT * FROM `users`</pre>
&nbsp;

`get` (`all`) with soft deletes on
<pre lang=php>User::get()</pre><pre lang=sql>SELECT * FROM `users` WHERE `users`.`deleted_at` IS NULL</pre>
&nbsp;

`get` with soft deleted rows
<pre lang=php>User::withTrashed()->get()</pre><pre lang=sql>SELECT * FROM `users`</pre>

`first`
<pre lang=php>User::first()</pre><pre lang=sql>SELECT * FROM `users` LIMIT 1</pre>

`find`
<pre lang=php>User::find(2)</pre><pre lang=sql>SELECT * FROM `users` WHERE `users`.`id` = 2 LIMIT 1</pre>

`findMany`
<pre lang=php>User::findMany([1, 2, 3])</pre><pre lang=sql>SELECT * FROM `users` WHERE `users`.`id` IN (1, 2, 3)</pre>

`value`
<pre lang=php>User::where('name', 'John Smith')->value('email')</pre><pre lang=sql>SELECT `email` FROM `users` WHERE `name` = 'John Smith' LIMIT 1</pre>

`paginate`
<pre lang=php>User::paginate()</pre><pre lang=sql>SELECT COUNT(*) AS aggregate FROM `users`</pre><pre lang=sql>SELECT * FROM `users` LIMIT 15 OFFSET 0</pre>



## Where

`where`
<pre lang=php>User::where('age', '>', 32)->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE `age` > 32</pre>

`orWhere`
<pre lang=php>User::where('age', '>', 40)->orWhere('age', '<', 20)->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE `age` > 40 OR `age` < 20</pre>

`whereIn`
<pre lang=php>User::whereIn('age', [20, 30, 40])->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE `age` IN (20, 30, 40)</pre>

`whereBetween`
<pre lang=php>User::whereBetween('age', [20,40])->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE `age` BETWEEN 20 AND 40</pre>

`whereDate`
<pre lang=php>User::whereDate('created_at', now())->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE DATE(`created_at`) = '2023-10-04'</pre>



## Create

`create`
<pre lang=php>User::create(['name' => 'John', 'email' => 'john@smith.com'])</pre><pre lang=sql>INSERT INTO `users` (`name`, `email`, `updated_at`, `created_at`) VALUES ('John', 'john@smith.com', '2023-10-04 15:30:34', '2023-10-04 15:30:34')</pre>

`firstOrCreate`
<pre lang=php>User::firstOrCreate(['email' => 'john@smith.com'], ['name' => 'John'])</pre><pre lang=sql>SELECT * FROM `users` WHERE (`email` = 'john@smith.com') LIMIT 1</pre><pre lang=sql>INSERT INTO `users` (`email`, `name`, `updated_at`, `created_at`) values ('john@smith.com', 'John', '2023-10-04 16:17:59', '2023-10-04 16:17:59')</pre>



## Update

`UPDATE`
<pre lang=php>User::where('status', 'some_status')->update(['type' => 'some_type'])</pre><pre lang=sql>UPDATE `users` SET `type` = 'some_type', `users`.`updated_at` = '2023-10-04 15:36:44' WHERE `status` = 'some_status'</pre>

Update single row
<pre lang=php>$user->update(['name' => 'John', 'email' => 'john@smith.com'])</pre><pre lang=sql>UPDATE `users` SET `name` = 'John', `users`.`updated_at` = '2023-10-04 16:12:30' WHERE `id` = 1</pre>

`updateOrCreate`
<pre lang=php>User::updateOrCreate(['email' => 'john@smith.com'], ['name' => 'James']);</pre><pre lang=sql>SELECT * FROM `users` WHERE (`email` = 'john@smith.com') LIMIT 1</pre><pre lang=sql>UPDATE `users` SET `name` = 'James', `users`.`updated_at` = '2023-10-04 16:14:18' WHERE `id` = 1</pre>



## Delete

`delete`
<pre lang=php>User::where('status', 'some_status')->delete()</pre><pre lang=sql>DELETE FROM `users` WHERE `status` = 'some_status'</pre>

Delete with soft deletes on
<pre lang=php>User::where('status', 'some_status')->delete()</pre><pre lang=sql>UPDATE `users` SET `deleted_at` = '2023-10-25 12:58:26', `users`.`updated_at` = '2023-10-25 12:58:26' WHERE `status` = 'some_status' AND `users`.`deleted_at` IS NULL</pre>

Delete single row
<pre lang=php>$user->delete()</pre><pre lang=sql>DELETE FROM `users` WHERE `id` = 1</pre>

Delete single row with soft deletes on
<pre lang=php>$user->delete()</pre><pre lang=sql>UPDATE `users` SET `deleted_at` = '2023-10-25 12:59:52', `users`.`updated_at` = '2023-10-25 12:59:52' WHERE `id` = 1</pre>

`destroy`
<pre lang=php>User::destroy(1, 2, 3)</pre><pre lang=sql>SELECT * FROM `users` WHERE `id` IN (1, 2, 3)</pre><pre lang=sql>DELETE FROM `users` WHERE `id` = 1</pre><pre lang=sql>DELETE FROM `users` WHERE `id` = 2</pre><pre lang=sql>DELETE FROM `users` WHERE `id` = 3</pre>

Destroy with soft deletes on
<pre lang=php>User::destroy(1, 2, 3)</pre><pre lang=sql>SELECT * FROM `users` WHERE `id` IN (1, 2, 3) AND `users`.`deleted_at` IS NULL</pre><pre lang=sql>UPDATE `users` SET `deleted_at` = '2023-10-25 12:54:53', `users`.`updated_at` = '2023-10-25 12:54:53' WHERE `id` = 1</pre><pre lang=sql>UPDATE `users` SET `deleted_at` = '2023-10-25 12:54:53', `users`.`updated_at` = '2023-10-25 12:54:53' WHERE `id` = 2</pre><pre lang=sql>UPDATE `users` SET `deleted_at` = '2023-10-25 12:54:53', `users`.`updated_at` = '2023-10-25 12:54:53' WHERE `id` = 3</pre>

Restore soft deleted row
<pre lang=php>$user->restore()</pre><pre lang=sql>UPDATE `users` SET `deleted_at` = '', `users`.`updated_at` = '2023-10-25 13:07:24' WHERE `id` = 1</pre>



## One to One

`profiles` table has `id`, `user_id`, `city`

User `hasOne` profile, profile `belongsTo` user

For nested relationships examples another one to one relationship is used. Profile `hasOne` passport, passport `belongsTo` profile

Get user's profile
<pre lang=php>$user->profile</pre> or <pre lang=php>$user->profile()->first()</pre><pre lang=sql>SELECT * FROM `profiles` WHERE `profiles`.`user_id` = 2 AND `profiles`.`user_id` IS NOT NULL LIMIT 1</pre>

Get user with profile
<pre lang=php>User::with('profile')->find(2)</pre><pre lang=sql>SELECT * FROM `users` WHERE `users`.`id` = 2 LIMIT 1</pre><pre lang=sql>SELECT * FROM `profiles` WHERE `profiles`.`user_id` IN (2)</pre>

Get user's passport (nested one to one relationships)
<pre lang=php>$user->profile->passport</pre><pre lang=sql>SELECT * FROM `users` WHERE `users`.`id` = 2 LIMIT 1</pre><pre lang=sql>SELECT * FROM `profiles` WHERE `profiles`.`user_id` = 2 AND `profiles`.`user_id` IS NOT NULL LIMIT 1</pre><pre lang=sql>SELECT * FROM `passports` WHERE `passports`.`profile_id` = 4 AND `passports`.`profile_id` IS NOT NULL LIMIT 1</pre>

Get user with profile AND passport (nested one to one relationships)
<pre lang=php>User::with('profile.passport')->find(2)</pre><pre lang=sql>SELECT * FROM `users` WHERE `users`.`id` = 2 LIMIT 1</pre><pre lang=sql>SELECT * FROM `profiles` WHERE `profiles`.`user_id` IN (2)</pre><pre lang=sql>SELECT * FROM `passports` WHERE `passports`.`profile_id` IN (4)</pre>

Get users who have profiles
<pre lang=php>User::has('profile')->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE EXISTS (SELECT * FROM `profiles` WHERE `users`.`id` = `profiles`.`user_id`)</pre>

Get users who have passport (nested one to one relationships)
<pre lang=php>User::has('profile.passport')->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE EXISTS (SELECT * FROM `profiles` WHERE `users`.`id` = `profiles`.`user_id` AND EXISTS (SELECT * FROM `passports` WHERE `profiles`.`id` = `passports`.`profile_id`))</pre>

Get users from Adelaide
<pre lang=php>User::whereHas('profile', function ($q) { $q->where('city', 'Adelaide'); })->get();</pre><pre lang=sql>SELECT * FROM `users` WHERE EXISTS (SELECT * FROM `profiles` WHERE `users`.`id` = `profiles`.`user_id` AND `city` = 'Adelaide')</pre>

Get users who have profiles and load their profiles
<pre lang=php>User::has('profile')->with('profile')->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE EXISTS (SELECT * FROM `profiles` WHERE `users`.`id` = `profiles`.`user_id`)</pre><pre lang=sql>SELECT * FROM `profiles` WHERE `profiles`.`user_id` IN (1, 2, 3)</pre>



## One to Many

`orders` table has `id`, `user_id`, `comment`

User has many orders. Order `belongsTo` user

For nested relationships examples another one to many relationship is used. Order `hasMany` support tickets, support ticket `belongsTo` order.

Get user's orders
<pre lang=php>$user->orders or $user->orders()->get()</pre><pre lang=sql>SELECT * FROM `orders` WHERE `orders`.`user_id` = 1 AND `orders`.`user_id` IS NOT NULL</pre>

Get users with orders
<pre lang=php>User::with('orders')->get()</pre><pre lang=sql>SELECT * FROM `users`</pre><pre lang=sql>SELECT * FROM `orders` WHERE `orders`.`user_id` IN (1, 2, 3)</pre>

Get users with orders and support tickets (nested one to many relationships)
<pre lang=php>User::with('orders.supportTickets')->get()</pre><pre lang=sql>SELECT * FROM `users`</pre><pre lang=sql>SELECT * FROM `orders` WHERE `orders`.`user_id` IN (1, 2, 3)</pre><pre lang=sql>SELECT * FROM `support_tickets` WHERE `support_tickets`.`order_id` IN (7, 8, 9)</pre>

Load companies for user
<pre lang=php>$user->load('orders')</pre><pre lang=sql>SELECT * FROM `orders` WHERE `orders`.`user_id` IN (1)</pre>

Get users who have orders
<pre lang=php>User::has('orders')->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE EXISTS (SELECT * FROM `orders` WHERE `users`.`id` = `orders`.`user_id`)</pre>

Get users who have support tickets (nested one to many relationships)
<pre lang=php>User::has('orders.supportTickets')->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE EXISTS (SELECT * FROM `orders` WHERE `users`.`id` = `orders`.`user_id` AND EXISTS (SELECT * FROM `support_tickets` WHERE `orders`.`id` = `support_tickets`.`order_id`))</pre>

Get users who has orders with empty comment
<pre lang=php>User::whereHas('orders', function ($q) { $q->WHERENull('comment'); })->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE EXISTS (SELECT * FROM `orders` WHERE `users`.`id` = `orders`.`user_id` AND `comment` IS NULL)</pre>

Get users who have orders and load their orders
<pre lang=php>User::has('orders')->with('orders')->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE EXISTS (SELECT * FROM `orders` WHERE `users`.`id` = `orders`.`user_id`)</pre><pre lang=sql>SELECT * FROM `orders` WHERE `orders`.`user_id` IN (1, 2, 3)</pre>



## Many to Many

`companies` table has `id`, `name`

`company_user` is a pivot table, it has `company_id`, `user_id` columns

User `belongsToMany` companies, company `belongsToMany` users

For nested relationships examples another many to many relationship is used. Company `belongsToMany` employees, employee `belongsToMany` companies

Get user's companies
<pre lang=php>$user->companies</pre> or <pre lang=php>user->companies()->get()</pre><pre lang=sql>SELECT `companies`.*, `company_user`.`user_id` AS `pivot_user_id`, `company_user`.`company_id` AS `pivot_company_id` FROM `companies` INNER JOIN `company_user` ON `companies`.`id` = `company_user`.`company_id` WHERE `company_user`.`user_id` = 1</pre>

Get users with companies
<pre lang=php>User::with('companies')->get()</pre><pre lang=sql>SELECT `companies`.*, `company_user`.`user_id` AS `pivot_user_id`, `company_user`.`company_id` AS `pivot_company_id` FROM `companies` INNER JOIN `company_user` ON `companies`.`id` = `company_user`.`company_id` WHERE `company_user`.`user_id` IN (1, 2, 3)</pre>

Get users with employees (nested many to many relationships)
<pre lang=php>User::with('companies.employees')->get()</pre><pre lang=sql>SELECT * FROM `users`</pre><pre lang=sql>SELECT `companies`.*, `company_user`.`user_id` AS `pivot_user_id`, `company_user`.`company_id` AS `pivot_company_id` FROM `companies` INNER JOIN `company_user` ON `companies`.`id` = `company_user`.`company_id` WHERE `company_user`.`user_id` IN (1, 2, 3)</pre><pre lang=sql>SELECT `employees`.*, `company_employee`.`company_id` AS `pivot_company_id`, `company_employee`.`employee_id` AS `pivot_employee_id` FROM `employees` INNER JOIN `company_employee` ON `employees`.`id` = `company_employee`.`employee_id` WHERE `company_employee`.`company_id` IN (7, 8, 9)</pre>

Load companies for user
<pre lang=php>$user->load('companies')</pre><pre lang=sql>SELECT `companies`.*, `company_user`.`user_id` AS `pivot_user_id`, `company_user`.`company_id` AS `pivot_company_id` FROM `companies` INNER JOIN `company_user` ON `companies`.`id` = `company_user`.`company_id` WHERE `company_user`.`user_id` IN (1)</pre>

Load companies AND employees for user (nested many to many relationships)
<pre lang=php>$user->load('companies. employees')</pre><pre lang=sql>SELECT `companies`.*, `company_user`.`user_id` AS `pivot_user_id`, `company_user`.`company_id` AS `pivot_company_id` FROM `companies` INNER JOIN `company_user` ON `companies`.`id` = `company_user`.`company_id` WHERE `company_user`.`user_id` IN (2)</pre><pre lang=sql>SELECT `employees`.*, `company_employee`.`company_id` AS `pivot_company_id`, `company_employee`.`employee_id` AS `pivot_employee_id` FROM `employees` INNER JOIN `company_employee` ON `employees`.`id` = `company_employee`.`employee_id` WHERE `company_employee`.`company_id` IN (7, 8, 9)</pre>

Get users who have companies
<pre lang=php>User::has('companies')->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE EXISTS (SELECT * FROM `companies` INNER JOIN `company_user` ON `companies`.`id` = `company_user`.`company_id` WHERE `users`.`id` = `company_user`.`user_id`)</pre>

Get users who have companies with employees (nested many to many relationships)
<pre lang=php>User::has('companies.employees')->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE EXISTS (SELECT * FROM `companies` INNER JOIN `company_user` ON `companies`.`id` = `company_user`.`company_id` WHERE `users`.`id` = `company_user`.`user_id` AND EXISTS (SELECT * FROM `employees` INNER JOIN `company_employee` ON `employees`.`id` = `company_employee`.`employee_id` WHERE `companies`.`id` = `company_employee`.`company_id`))</pre>

Get users who have companies with empty description
<pre lang=php>User::whereHas('companies', function ($q) { $q->whereNull('description'); })->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE EXISTS (SELECT * FROM `companies` INNER JOIN `company_user` ON `companies`.`id` = `company_user`.`company_id` WHERE `users`.`id` = `company_user`.`user_id` AND `description` IS NULL)</pre>

Get users who have companies and load their companies
<pre lang=php>User::has('orders')->with('orders')->get()</pre><pre lang=sql>SELECT * FROM `users` WHERE EXISTS (SELECT * FROM `orders` WHERE `users`.`id` = `orders`.`user_id`)</pre><pre lang=sql>SELECT * FROM `orders` WHERE `orders`.`user_id` IN (1, 2, 3)</pre>



## Aggregate functions

`count`
<pre lang=php>User::count()</pre><pre lang=sql>SELECT COUNT(*) AS aggregate FROM `users`</pre>

`max` (`min`, `avg`, `sum`)
<pre lang=php>User::max('age')</pre><pre lang=sql>SELECT MAX(`age`) AS aggregate FROM `users`</pre>

`withSum` (`withAvg`, `withMin`, `withMax`, `withCount`)
<pre lang=php>User::withSum('orders', 'total')->get();</pre><pre lang=sql>SELECT `users`.*, (SELECT SUM(`orders`.`total`) FROM `orders` WHERE `users`.`id` = `orders`.`user_id`) AS `orders_sum_total` FROM `users`</pre>



## Miscellaneous

`latest` (`oldest``)
<pre lang=php>User::latest()->get()</pre><pre lang=sql>SELECT * FROM `users` ORDER BY `created_at` desc</pre>

`withExists`
<pre lang=php>User::withExists('orders')->get()</pre><pre lang=sql>SELECT `users`.*, EXISTS(SELECT * FROM `orders` WHERE `users`.`id` = `orders`.`user_id`) AS `orders_exists` FROM `users`</pre>

`increment` (`decrement`)
<pre lang=php>User::where('id', 2)->increment('bonus', 12)</pre><pre lang=sql>UPDATE `users` SET `bonus` = `bonus` + 12, `users`.`updated_at` = '2023-10-25 14:58:10' WHERE `id` = 2</pre>
