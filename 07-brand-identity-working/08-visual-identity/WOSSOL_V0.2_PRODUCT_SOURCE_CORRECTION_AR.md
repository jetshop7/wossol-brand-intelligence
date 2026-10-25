# تصحيح مصدر المنتج والتحقق من تطبيقات الهوية V0.2

**التاريخ:** 2026-10-25

**الحالة:** حزمة التحقق والتطبيق المصححة جاهزة لمراجعة المالك؛ لا اعتماد للهوية.

**الفرع:** مستودع الذكاء `codex/brand-identity-complete`.

**النطاق:** تصحيح اختيار مصدر المنتج السابق، والتحقق الموجّه من أسطح منصة التجار، وإنتاج تطبيقات المنتج والتسويق. لا يشمل إعادة تصميم الشعار.

## خلاصة التصحيح

كان اختيار المصدر في الجولة السابقة خطأً: `jetshop7/wossol-app` يخص تطبيق Wossol Export وليس منصة التجار المحددة في حوكمة المشروع. لذلك أُعيدت التطبيقات على المصدر المعتمد `jetshop7/wossol-platform`. احتُفظ بلوحتي Export والتقرير السابق كأثر تاريخي معلّم بوضوح **خارج نطاق منصة التجار**؛ لا تعتمد هذه الوثيقة استنتاجاتهما عن المنتج أو العلامة.

## تحديد خط الأساس المعتمد

قررت عدم استنتاج خط أساس النشر من اسم فرع أو من نسخة محلية:

| الحالة | المرجع | التفسير |
|---|---|---|
| مستودع المنتج الرسمي | `jetshop7/wossol-platform` | مصدر المنتج وفق `AGENTS.md` في الفرع المرجعي. |
| الفرع المرجعي الحالي | `dev/wossol-integration` | تنص تعليمات المستودع صراحة على أن `origin/dev/wossol-integration` هو ذاكرة المشروع المرجعية بين المهام. |
| الرأس البعيد الذي فُحص | `3ed1386fa88496a2e80b5e144b14a198f6e1bc63` | تحقق مباشر بـ`git ls-remote` على GitHub؛ معاينة المسارات والملفات كانت من شجرة هذا الرأس. |
| `origin/main` | `6de3a31959585cd6d8f3ff03179cae863bf1a98a` | رأس مختلف؛ لم يُختر بدل الفرع الذي تحدده الحوكمة. |
| هل ثبت أنه إنتاج منشور؟ | **لا** | الحوكمة تثبت خط أساس التكامل/المصدر المرجعي، لا تطابقًا مع نشر حي. وسجل التنفيذ يحوي بوابات قبول runtime/browser غير مغلقة. لا أصفه بأنه إنتاج فعلي. |

**حالة النسخة المحلية وقت الفحص:** checkout المنتج كان على `codex/analytics-persistence-foundation` عند `fd075fd4`، مع أربع ملفات Analytics معدلة وغير ملتزمة (`migration.sql`, `schema.prisma`, `analytics-outcome-monitoring.service.ts`, `analytics.module.ts`). لم أستخدم تلك التعديلات كدليل؛ قارنت بصمات ملفات واجهة التاجر الرئيسية مع كائنات `origin/dev/wossol-integration` ووجدتها متطابقة، واعتمدت المحتوى المثبت عند SHA أعلاه. لم أنفذ أي كتابة أو checkout أو stage أو تعديل في `wossol-platform`، أو `wossol-app`، أو `main`.

## خريطة الأدلة من المصدر المثبت

الروابط أدناه مثبتة على SHA `3ed1386fa88496a2e80b5e144b14a198f6e1bc63`، لا على رأس فرع متحرك.

| ما تدعمه اللوحة | ملفات المصدر ذات الصلة |
|---|---|
| حوكمة الفرع/حالة التنفيذ | [`AGENTS.md`](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/AGENTS.md)، [`docs/PROJECT_EXECUTION_CONTROL.md`](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/docs/PROJECT_EXECUTION_CONTROL.md) |
| غلاف التاجر، قائمة الأقسام، أوامر الطلبات، السياق/القائمة المتنقلة، وعلامة W المؤقتة | [`MerchantShell.tsx`](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/apps/frontend/src/app/merchant/MerchantShell.tsx)، [`globals.css`](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/apps/frontend/src/app/globals.css) |
| Home: Quick actions، Needs attention، Recommended actions، Today، Current work، Performance snapshot، Recent updates | [`merchant/page.tsx`](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/apps/frontend/src/app/merchant/page.tsx)، [مواصفة Home](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/docs/ui/merchant/MERCHANT_HOME_SHELL_UI_SPEC.md) |
| Orders: القائمة، التفاصيل، الإنشاء، الاستيراد، captures | [`merchant/orders/`](https://github.com/jetshop7/wossol-platform/tree/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/apps/frontend/src/app/merchant/orders)، [مواصفة Orders](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/docs/ui/merchant/MERCHANT_ORDERS_UI_SPEC.md) |
| Products: المنتجات، الإنشاء/التعديل، التفاصيل والنسخ | [`merchant/products/`](https://github.com/jetshop7/wossol-platform/tree/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/apps/frontend/src/app/merchant/products)، [مواصفة Products](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/docs/ui/merchant/MERCHANT_PRODUCTS_UI_SPEC.md) |
| Inventory: available/expected stock، الطلب، inbound، الجودة ومراجعة النقص | [`inventory/page.tsx`](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/apps/frontend/src/app/merchant/inventory/page.tsx)، [مواصفة Inventory](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/docs/ui/merchant/MERCHANT_INVENTORY_UI_SPEC.md) |
| Customers: البحث، تاريخ التاجر/السجل، Reputation | [`customers/page.tsx`](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/apps/frontend/src/app/merchant/customers/page.tsx)، [مواصفة Customers](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/docs/ui/merchant/MERCHANT_CUSTOMERS_UI_SPEC.md) |
| Advertising: Meta وTikTok، وحدود النتائج/الأداء | [`merchant/advertising/`](https://github.com/jetshop7/wossol-platform/tree/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/apps/frontend/src/app/merchant/advertising)، [مواصفة Advertising](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/docs/ui/merchant/MERCHANT_ADVERTISING_UI_SPEC.md) |
| Analytics: أداء تشغيلي وRecovery وإشارات محددة | [`analytics/page.tsx`](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/apps/frontend/src/app/merchant/analytics/page.tsx)، [مواصفة Analytics](https://github.com/jetshop7/wossol-platform/blob/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/docs/ui/merchant/MERCHANT_ANALYTICS_UI_SPEC.md) |
| بقية أسطح التاجر الظاهرة في التنقل | `merchant/applications/`, `confirmation/`, `external-shipping/`, `local-pickup/`, `finance/`, `market-center/`, `team/`, `notifications/`, `support/`, `settings/` تحت [`apps/frontend/src/app/merchant/`](https://github.com/jetshop7/wossol-platform/tree/3ed1386fa88496a2e80b5e144b14a198f6e1bc63/apps/frontend/src/app/merchant). تقييد Market Center بليبيا وتقييد Team بالدور/الصلاحية موضحان في `MerchantShell.tsx`. |

هذه معاينة مصدرية للمسارات والمكونات والعقود، وليست جلسة متصفح مسجلة. لا أزعم أن كل قسم متاح لكل تاجر أو أن كل حالة اتصال/بيانات حية قد اختُبرت.

## حدود الحقيقة التي تظهر في النسخة المصححة

- الواجهة تعرض القوائم ضمن MerchantShell وسياق Workspace/Store؛ تظهر الأقسام بحسب الصلاحية والسياق. شريط التنقل المنكمش عرضه 88px ويعرض علامة W 36×36 حاليًا، بينما الوضع الموسع يعرض الاسم الكامل. هذه **علامة مؤقتة في المنتج** وليست اعتمادًا للشعار؛ وبذلك تُصحح نتيجة V0.2 السابقة التي قالت إن المنصة التي فُحصت لا تحتاج موضع علامة مدمجة.
- المخزون يفصل Available عن Expected ويعرض ضغط الطلب/الطلبات المنتظرة ومشكلات البيانات؛ لا تحوّل اللوحة المخزون المتوقع إلى مخزون متاح.
- صفحة العملاء تتيح سجل التاجر ورؤية Wossol Reputation بحدودها؛ لا تقدمها اللوحة كتنبؤ أو كموافقة/consent.
- في Meta تُفصل نتائج مزود الإعلان عن Outcomes لطلبات Wossol المثبتة بإسناد دقيق. لا يوجد ادعاء causal lift أو ROAS/CPA مالي كامل. TikTok في المصدر الحالي لإدارة الاتصال والمعلنين المصرح بهم، لا لتقارير أداء.
- Analytics يعرض مقاييس/تشخيصات تشغيلية محددة ومقيدة، لا حلقة تعلم تلقائية ولا ضمانًا لصحة القرار أو النتيجة.
- القيم الظاهرة في الرسوم فقط placeholders أو `—`، وليست بيانات متاجر أو عملاء حقيقية.

## التحقق من استخبارات المنتج واستراتيجية العلامة

قارنت الواجهات بسجل [`PRODUCT_ROUTE_BACKEND_COVERAGE_RECONCILIATION`](../../03-master-synthesis/PRODUCT_ROUTE_BACKEND_COVERAGE_RECONCILIATION.md) وبالمقاطع المحدثة V1.2 في [`ORDERS`](../../02-section-intelligence/ORDERS.md)، [`PRODUCTS`](../../02-section-intelligence/PRODUCTS.md)، [`INVENTORY`](../../02-section-intelligence/INVENTORY.md)، [`CUSTOMERS`](../../02-section-intelligence/CUSTOMERS.md)، [`ADVERTISING`](../../02-section-intelligence/ADVERTISING.md)، و[`ANALYTICS / DECISION CENTER`](../../02-section-intelligence/ANALYTICS_DECISION_CENTER.md). واختبرت صياغة التسويق في ضوء [`Brand Strategic Evidence Master`](../../03-master-synthesis/WOSSOL_BRAND_STRATEGIC_EVIDENCE_MASTER.md) و[`Marketing Asset Master`](../../03-master-synthesis/WOSSOL_MARKETING_ASSET_MASTER.md).

التوافق الاستراتيجي: إبراز سير العمل والسياق والأدلة المحدودة، لا قائمة مزايا عامة أو وعود ذكاء/نمو. تظل «ترابط الحقيقة التجارية»، والوكالة المحدودة، وتقليل إعادة بناء السياق موضوعات مدعومة في استخبارات المنتج؛ لكنها ليست شعارًا أو positioning معتمدًا. كما أن مصادر استخبارات المنتج أقدم من رأس الشيفرة الحالي؛ لذلك استخدمتُها كمرجع استراتيجي متقاطع، ورجّحت تحقق ملفات الواجهة الحالية عند `3ed1386` لأي وصف شاشة.

## المخرجات المصححة

- [لوحة منصة التجار — SVG](WOSSOL_V0.2_MERCHANT_PLATFORM_APPLICATIONS.svg) و[PNG](WOSSOL_V0.2_MERCHANT_PLATFORM_APPLICATIONS.png): التنقل الحقيقي ومشهد Home، وOrders، وProducts، وInventory، وCustomers، وAdvertising، وAnalytics، وبقية الوحدات. التمثيل المفاهيمي معلّم وليس screenshot.
- [لوحة التسويق لمنصة التجار — SVG](WOSSOL_V0.2_MERCHANT_MARKETING_APPLICATIONS.svg) و[PNG](WOSSOL_V0.2_MERCHANT_MARKETING_APPLICATIONS.png): صفحة تقديم، منشور عضوي، وإعلان مقترح مبنية على عمل التاجر، مع عبارات غير معتمدة وحدود أدلة الإعلانات.
- [تقرير V0.2 السابق](WOSSOL_V0.2_REAL_PRODUCT_VALIDATION_REPORT_AR.md) ولوحتا Export السابقتان موسومة الآن تاريخيًا وخارج نطاق منصة التجار، ومحفوظة كما هي في محتواها.

رُسمت ملفات SVG إلى PNG باستخدام الخطوط المحلية نفسها، ثم أُجري فحص بصري ثابت. ألوان Harbor/Night/Lime وخطوط Inter/Noto من المقترح البصري المؤقت، وليست وصفًا لرموز CSS الحالية في التطبيق. لا يوجد اختبار runtime أو screenshot، ولا تحقق من النشر الإنتاجي أو مشاركة مستخدمين. بقي اتجاه A/Clear Stroke والألوان والخطوط كما هي بوصفها مقترحًا؛ لا إعادة استكشاف للشعار ولا اعتماد للهوية.

## قرار المالك المطلوب

مراجعة ما إذا كان اتجاه A والتطبيقات البصرية مناسبًا فعليًا لمنصة التجار؛ تحديد متطلبات العلامة المدمجة للـsidebar المنكمش بدل الـW المؤقت؛ ثم السماح باختبار واجهات حية/أجهزة فعلية ومراجعة عربية وإتاحة الوصول. لا تُنشر لوحات الحملات ولا تُستبدل علامة المنتج قبل هذا القرار.
