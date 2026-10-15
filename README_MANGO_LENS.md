# MANGO LENS — Product Intelligence v1.7 Stability & Security

Arabic-first Flutter app by MANGO Company for identifying products from images, researching current prices when requested, and preparing seller-oriented reports.

## What's included in v1.7
- **Provider switch:** Google Gemini or OpenAI Responses API. Each provider has its own API key.
- **Editable model ID:** enter the exact model identifier in Settings; model names can change over time without changing app code. Gemini model discovery is available through the connection test.
- **Single-call product report:** image analysis, one structured report, price search when enabled, offers, rarity signals and source extraction are combined in one generation API call. Web search is enabled on the first v1.6 launch and can be switched off by the user; built-in search tooling may still have separate service quota limits.
- **Usage guard:** blocks overlapping analyses, enforces a 15-second minimum gap between requests, persists the last-request time across restarts, and pauses requests for at least 90 seconds after an HTTP 429 (up to the provider's `Retry-After` value, capped at one hour). No automatic retries are made.
- **API key storage:** provider keys are stored through `flutter_secure_storage` using Android's platform-backed encrypted storage. Existing keys in SharedPreferences are migrated when secure storage is available, then removed from plain preferences.
- **Local cache:** reuses the most recent report for the same image, provider, model, market, condition, category, detail level, web-search setting and extra instructions. Cached prices are clearly labelled as not freshly checked.
- **Lower token budget:** fast reports use up to 1,100 output tokens; detailed reports up to 2,400. Uploaded photos are resized and compressed by image_picker.
- Arabic/English interface, coordinated orange branding across light/dark themes, dedicated Home/Product/Prices/Rarity pages, mobile-aware price and rarity tables, an Amazon.eg product link when verified or a clearly-labelled Amazon.eg search fallback, currency selection (EGP/USD/SAR/EUR), scan history, report copy, and a local seller profit calculator.

## Configure and run
1. Install Flutter stable and Android tooling.
2. Run `flutter pub get`.
3. Run `flutter run`, or build with `flutter build apk --release`.
4. Open Settings, choose Gemini or OpenAI, enter that provider's API key, type a model ID, then save. Gemini's connection test can fetch model IDs; OpenAI's test checks the key via the models endpoint. Neither test generates an analysis.

## API key safety
Provider keys are stored locally with `flutter_secure_storage`, which is safer than plain SharedPreferences for a personal/testing build. This does **not** make a key impossible to extract from a device or a distributed app. For a public commercial MANGO LENS release, keep company/provider secrets on a server-side proxy, authenticate users, and enforce per-user quotas. Never ship a shared company API key inside the APK. If you previously used an API key in an APK shared with others, rotate that key in the provider dashboard.

## Important quota note
Client-side throttling and caching can reduce unnecessary requests but cannot increase provider quota or guarantee that HTTP 429 will never occur. Limits are enforced by each provider and may be model-, project-, account-, minute-, token-, or day-based. An API key test is not proof that generation quota is available.

## Build verification
This source archive has not been built with Flutter in this environment because the Flutter/Dart SDK is unavailable here. Use the included GitHub Actions workflow or run the build commands above to verify the APK.


## MANGO GEMINI FREE MODE (v1.5.0)

- يطلب التطبيق تقرير المنتج كاملًا في استدعاء تحليل واحد بدل تقسيمه إلى عدة استدعاءات منفصلة.
- عند تفعيل البحث، يرفق أداة البحث المدعومة داخل طلب التحليل نفسه؛ وقد تنفّذ الخدمة عمليات بحث داخلية وحدودها مستقلة عن عدد طلبات التطبيق.
- يطلب التقرير بصيغة JSON موحّدة ثم يحوّله التطبيق إلى أقسام Markdown للعرض. توجد عودة احتياطية للنص الأصلي إذا لم يلتزم نموذج مخصص بتنسيق JSON.
- يعيد استخدام آخر نتيجة مطابقة محفوظة محليًا بدل إرسال طلب جديد، مع تنبيه أن السعر لم يُفحص من جديد.
- يمنع الضغط المتكرر/الطلبات المتزامنة، يطبق فاصلًا زمنيًا بين التحليلات، ويحترم استجابة 429 مع فترة توقف.
- يعرض عدادًا محليًا لاستدعاءات التحليل المرسلة والناجحة والفاشلة ومرات استخدام الكاش. اختبار مفتاح API غير مشمول في هذا العداد.

ملاحظة: هذه الإجراءات تقلل الاستدعاءات التي يرسلها التطبيق، لكنها لا تضمن تجاوز أو تغيير حصص Gemini المجانية. الحصص تتغير حسب النموذج والمشروع والحساب، والبحث المؤسس على الويب قد تكون له حدود مستقلة. لم يتم تشغيل بناء Flutter في بيئة التعديل لعدم توفر Flutter SDK؛ يلزم اختبار البناء والطلبات الفعلية قبل النشر.


## MANGO LENS v1.6 — interface and currency update

- The interface has been reorganized into four focused bottom-navigation pages: Home, Product, Prices, and Rarity. The orange brand accent is shared by light and dark themes for a consistent Android-style visual system.
- Users can select a reporting currency: EGP, USD, SAR, or EUR. Prices remain in the source currency in the offers table; a converted price is shown only when the AI returns a source-backed conversion rather than guessing an exchange rate.
- Product price comparison uses a structured offers list. Rarity signals are delivered as a separate structured table. Both come from the same analysis response.
- If Gemini finds a direct Amazon.eg product page, the Prices page offers it. If not, the app offers a clearly-labelled Amazon.eg search link generated locally, without another API call and without claiming the item is in stock.
- The local cached result key includes the selected currency, so reports from a different currency choice are not incorrectly reused.

## Testing status

Flutter/Dart SDK is not installed in the editing environment, so the Android build and live provider responses could not be executed here. Source delimiter checks and ZIP integrity checks were performed; run `flutter pub get` and `flutter build apk --release` before distributing.

## v1.7 validation notes

- Fixed the non-constant dropdown item list that caused the Dart compile error in the currency selector.
- Bumped the app version to `1.7.0+8`.
- Migrates legacy Gemini/OpenAI keys from SharedPreferences into secure storage where available.
- **Build status:** not compiled in this environment because the Flutter/Dart SDK is unavailable. The GitHub Actions workflow is the source of truth for a real APK build.
- **Release status:** the Android Gradle release build currently uses debug signing so CI can produce a test APK. That APK is for testing only, not a Play Store/public release. Configure a private release keystore and GitHub Secrets before publishing.
- Price listings and rarity remain AI-assisted estimates. Confirm source URL, item condition, availability, shipping, and checked date before making a buying/selling decision.
