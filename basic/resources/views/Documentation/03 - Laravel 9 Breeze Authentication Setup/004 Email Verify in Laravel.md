বন্ধু, স্বাগতম। আগের আলোচনায় আমি কীভাবে পাসওয়ার্ড ভুলে গেলে তা পুনরুদ্ধার করতে হয়, তা আলোচনা করেছি। আশা করি, সেটি তোমার জন্য পরিষ্কার হয়েছে। এখন, এই আলোচনায় আমি ইমেইল যাচাইকরণের পদ্ধতি নিয়ে কথা বলতে চাই। ইমেইল যাচাই করা আসলেই খুব সহজ।

```php
Route::get('/dashboard', function () {
    return view('dashboard');
})->middleware(['auth', 'verified'])->name('dashboard');
```

প্রথমে আমাদের প্রকল্পে যেতে হবে। সেখানে গেলে `web.php` ফাইলে ডিফল্টভাবে `auth` (যেটা হচ্ছে `middleware(['auth'])->name('dashboard');`) মিডলওয়্যার ব্যবহার করা হয়। যদি তুমি ড্যাশবোর্ডে অ্যাক্সেস করতে চাও, তাহলে ডিফল্টভাবে মিডলওয়্যার হিসেবে `auth` ব্যবহার করা হচ্ছে। যখন `Breeze` ইনস্টল করা হয়, তখন আরও একটি মিডলওয়্যার তৈরি হয়। যদি তুমি মিডলওয়্যারে যাও এবং `Kernel.php` তে দেখ, সেখানে একটি `verified` ( যেটা হচ্ছে `'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,`) নামে ফাইল থাকে। এই `verified` মিডলওয়্যার ইমেইল যাচাইকরণের জন্য ব্যবহার করা হয়। প্রথমে আমাদের এটি ডিক্লেয়ার করতে হবে।

`web.php` তে ডিক্লেয়ার করা হয়েছে ( `middleware(['auth', 'verified'])->name('dashboard');` তবে বর্তমানে এই অংশটি অটো হয়ে থাকে ), এবং আমরা এটিকে `verified` নামে অ্যাড করবো । তারপর আমাদের মডেল আপডেট করতে হবে। যদি তুমি `User` মডেলে যাও, সেখানে ডিফল্টভাবে `MustVerifyEmail` ( যেটা হচ্ছে `use Illuminate\Contracts\Auth\MustVerifyEmail;` তবে বর্তমানে এটি কমেন্ট করা থাকে তাই এর সামনে থেকে // উঠাতে হবে মানে কমেন্ট আউট করতে হবে। ) নামে একটি ইন্টারফেস ইমপ্লিমেন্ট করা হয়েছে। আমরা এটি আমাদের `User` মডেলে ইমপ্লিমেন্ট করবো।

```php
<?php

namespace App\Models;

//use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Laravel\Sanctum\HasApiTokens;
class User extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable;
```

এভাবে হবে।

```php
<?php

namespace App\Models;

use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable implements MustVerifyEmail
{
    use HasApiTokens, HasFactory, Notifiable;
```

`MustVerifyEmail` ইন্টারফেসটি ইমপ্লিমেন্ট করার পর, সবকিছু ঠিকঠাক কাজ করবে। এখন, যদি তুমি নতুন ব্যবহারকারী তৈরি করো, যেমন আমি এখন একটি নতুন রেজিস্ট্রেশন করছি, সিস্টেমটি ইমেইল যাচাই করার জন্য নির্দেশনা দেবে। এটি একটি ভিউ (`verify-email.blade.php`) ফাইল থেকে লোড হয়, যেখানে যাচাইকরণ বার্তাটি প্রদর্শিত হয়।

```php
<x-guest-layout>
    <div class="mb-4 text-sm text-gray-600">
        {{ __('Thanks for signing up! Before getting started, could you verify your email address by clicking on the link we just emailed to you? If you didn\'t receive the email, we will gladly send you another.') }}
    </div>
```

তুমি চাইলে বার্তাটি পরিবর্তন করতে পারো।

ইমেইল যাচাইকরণের জন্য একটি নতুন লিংক পাঠানো হবে, যা তুমি ইমেইলে পাবে। যখন ইমেইল যাচাই করা হবে, তখন সিস্টেমে ব্যবহারকারী হিসেবে লগইন করার অনুমতি দেবে এবং ড্যাশবোর্ড অ্যাক্সেস করা যাবে। আমাদের ডাটাবেসেও দেখা যাবে যে ইমেইলটি যাচাইকৃত হয়েছে।

এই প্রক্রিয়া সহজে সম্পন্ন করার জন্য, আমাদের `User` মডেলে `MustVerifyEmail` ইন্টারফেসটি ইমপ্লিমেন্ট করতে হবে এবং `web.php` তে `verified` মিডলওয়্যার যোগ করতে হবে। এরপর নতুন ব্যবহারকারী রেজিস্ট্রেশন করলে ইমেইল যাচাইয়ের বার্তা প্রদর্শিত হবে।

তুমি যদি `Laravel Breeze` ইনস্টল করো, তখন `login request` এও কিছু নিয়ম থাকবে, যেমন কতবার ভুল পাসওয়ার্ড দিলে অ্যাকাউন্টটি ব্লক করা হবে। এই নিয়মগুলো `rate limiting` নামে পরিচিত। তুমি চাইলে এটি কনফিগার করতে পারো, যেমন কতবার লগইন চেষ্টা করার পর এটি ব্লক হবে।

আশা করি, ইমেইল যাচাইকরণের পুরো প্রক্রিয়া তোমার জন্য পরিষ্কার হয়েছে। পরবর্তী আলোচনায় আমি দেখাবো কীভাবে লগইন পেজের ডিফল্ট লোগো পরিবর্তন করা যায় এবং অন্যান্য কাস্টমাইজেশন করা যায়। ধন্যবাদ!
