প্রথমে সাধারণ কাজ যেমন লারাভেল ইনস্টল করে নিলাম । এর পর ব্রিজ ইনস্টল করে নিবে।

ইনস্টল করার সময় এই সমস্যাটির সম্মুখিন হই।

```bash
C:\Users\mejba\OneDrive\Desktop\GitHub\Laravel-Big-Project\Laravel-Big-Project\basic>php artisan breeze:install
Xdebug: [Step Debug] Time-out connecting to debugging client, waited: 200 ms. Tried: localhost:9003 (through xdebug.client_host/xdebug.client_port).

  Which Breeze stack would you like to install?
  Blade with Alpine .......................................................................................................................... blade
  Livewire (Volt Class API) with Alpine ................................................................................................... livewire
  Livewire (Volt Functional API) with Alpine ................................................................................... livewire-functional
  React with Inertia ......................................................................................................................... react
  Vue with Inertia ............................................................................................................................. vue
  API only ..................................................................................................................................... api
❯ blade
blade

  Would you like dark mode support? (yes/no) [no]
❯ no

  Which testing framework do you prefer? [PHPUnit]
  PHPUnit ........................................................................................................................................ 0
  Pest ........................................................................................................................................... 1
❯ 0
0

   INFO  Installing and building Node dependencies.


added 142 packages, and audited 143 packages in 15s

36 packages are looking for funding
  run npm fund for details

found 0 vulnerabilities

> build
> vite build

    vite v4.5.5 building for production...
transforming...
    ✓ 5 modules transformed.
✓ built in 191ms
    (node:18072) Warning: To load an ES module, set "type": "module" in the package.json or use the .mjs extension.
(Use node --trace-warnings ... to show where the warning was created)
[vite:css] Failed to load PostCSS config (searchPath: C:/Users/mejba/OneDrive/Desktop/GitHub/Laravel-Big-Project/Laravel-Big-Project/basic): [SyntaxError] Unexpected token 'export'
C:\Users\mejba\OneDrive\Desktop\GitHub\Laravel-Big-Project\Laravel-Big-Project\basic\postcss.config.js:1
export default {
^^^^^^

SyntaxError: Unexpected token 'export'
    at wrapSafe (node:internal/modules/cjs/loader:1378:20)
    at Module._compile (node:internal/modules/cjs/loader:1428:41)
    at Module._extensions..js (node:internal/modules/cjs/loader:1548:10)
    at Module.load (node:internal/modules/cjs/loader:1288:32)
    at Module._load (node:internal/modules/cjs/loader:1104:12)
    at cjsLoader (node:internal/modules/esm/translators:346:17)
    at ModuleWrap.<anonymous> (node:internal/modules/esm/translators:286:7)
    at ModuleJob.run (node:internal/modules/esm/module_job:234:25)
    at async ModuleLoader.import (node:internal/modules/esm/loader:473:24)
    at async importDefault (file:///C:/Users/mejba/OneDrive/Desktop/GitHub/Laravel-Big-Project/Laravel-Big-Project/basic/node_modules/vite/dist/node/chunks/dep-b2890f90.js:36556:18)
file: C:\Users\mejba\OneDrive\Desktop\GitHub\Laravel-Big-Project\Laravel-Big-Project\basic\resources\css\app.css
error during build:



   INFO  Breeze scaffolding installed successfully.

Xdebug: [Step Debug] Time-out connecting to debugging client, waited: 200 ms. Tried: localhost:9003 (through xdebug.client_host/xdebug.client_port).
```

### সমাধান হয় নিচের পদ্ধতির মাধ্যমে।

---

The error you're encountering is due to a syntax issue in your `postcss.config.js` file. It seems that you're using ES module syntax (`export default`), but Node.js expects CommonJS syntax by default, especially for files with a `.js` extension.

Here's the error in question:

```
SyntaxError: Unexpected token 'export'
```

### Solution: Use CommonJS Syntax

You can fix this by modifying the `postcss.config.js` file to use CommonJS syntax instead of ES modules. Replace the `export default` line with `module.exports`.

### Step-by-Step Fix:

1. Open `postcss.config.js` in your project.
2. Change the `export default` statement to use `module.exports`.

#### Before (ES Module Syntax):

```javascript
export default {
    plugins: {
        tailwindcss: {},
        autoprefixer: {},
    },
};
```

#### After (CommonJS Syntax):

```javascript
module.exports = {
    plugins: {
        tailwindcss: {},
        autoprefixer: {},
    },
};
```

This change ensures compatibility with Node.js when running with the `.js` extension.

### Alternative: Use ES Modules

If you prefer to use ES module syntax, you'll need to add `"type": "module"` to your `package.json`. This will tell Node.js to treat the entire project as an ES module.

#### Add to `package.json`:

```json
{
    "type": "module"
}
```

However, using CommonJS syntax in `postcss.config.js` is the simpler and more compatible solution.

After making this change, try running your build process again.

---

# ব্রিজ ইনস্টলের পরে আমি npm install এবং পরে npm run dev দিয়ে npm চালু করি। এর পর আমি project1 নামে একটি ডাটাবেইজ তৈরি করে নি এবং .env ফাইলে ডাটাবেইজটি কনফিগ করে দি। এবং php artisan migrate কমান্ড দি।

যখন আমরা লারাভেল ব্রিজ ইনস্টল করি তখন কিছু ডিফল্ট টেবিল তৈরি করার দরকার হয় যেগুলি php artisan migrate এর মাধ্যমে তৈরি হয়। এগুলো তৈরি হয় ডাটাবেইজ এর মাইগ্রেশন ফোল্ডারে।

# আমরা যখন লারাভেল ব্রিজ ইনস্টল করলাম তবে কি ভাবে এত সুন্দর ভাবে অথেনটিকেশনের সব সহজে হচ্ছে তা আলোচনা করবো। ব্রিজ ইনস্টলের সাথে সাথে আমাদের প্রোজেক্টে কিচু বিষয় ঘটে। প্রথমে আমরা web.php ফাইলে লক্ষ্য করলে দেখবো সেখানে সর্বশেষে `require __DIR__.'/auth.php';` লাইনটি আাছে যা আমাদের ব্রিজের ই তৈরি করা auth.php ফাইল কে ব্যবহার করতে সুযোগ দেয় । অথ ফাইলের ভেতরে অথেনটিকেশন সম্পর্কিত সকল কিছু একসাথে থাকে। যেমন register, login, forgot-password, reset-password ইত্যাদি সবই বিদ্যমান। এবং এগুলোর সাথে সাথে এগুলোর জন্য প্রয়োজনিয় সকল ডিফল্ট কন্ট্রোলার ও তৈরি করে দেওয়া হয় । অথেনটিকেশনের জন্য যে ফাইল গুলো ব্যবহার হচ্ছে সেগুলোকে আমরা রিসোর্স এর ভিউ ফোল্ডারের মাঝে অথ ফোল্ডারে পাচ্ছি।
