# WOSSOL — NEW CHAT RESTART INSTRUCTIONS (2026-10-09)

## User-facing ready-to-paste Arabic prompt

نحن نواصل مشروع **WOSSOL Current Product Deep Study → Brand Intelligence → Brand Strategy → Visual Identity**. المحادثة السابقة وصلت إلى حد الطول، لذا **لا تبدأ من الصفر ولا تعتبر Products مكتملًا**.

**مستودع الكود الأساسي:** https://github.com/jetshop7/wossol-platform — الفرع `dev/wossol-integration`.
**مستودع البحث:** https://github.com/jetshop7/wossol-brand-intelligence — الفرع `main`.

**أولًا، قبل أي تحليل، افتح واقرأ هذه الملفات الموجودة في GitHub قراءة فعلية:**
1. `07-current-product-deep-study/CURRENT_PRODUCT_DEEP_STUDY_PROTOCOL.md`
2. `07-current-product-deep-study/DEEP_STUDY_EXECUTION_AND_QUALITY_GATES.md`
3. `07-current-product-deep-study/PRODUCTS_WORKING_EVIDENCE_AND_CROSS_SECTION_REGISTER.md` — سجل ضخم متراكم للدفعات 01–12 والعلاقات وQA؛ اقرأه بأجزاء إن لزم، ولا تكتفِ بأول الملف.
4. `07-current-product-deep-study/PRODUCTS_RESEARCH_CONTINUITY_BATCHES_13_TO_16_AND_NEXT.md` — **ملف الانتقال الحاسم** لكل اكتشافات الدفعات 13–16 ونقطة الاستئناف.
5. `07-current-product-deep-study/PRODUCTS_QA_TEST_ORDERS_AND_FIFO_COVERAGE_V0.1.md` — ملاحظات Test Orders وCOGS الحرجة.
6. راجع `07-current-product-deep-study/HOME.md` فقط كنموذج عمق وصياغة القيمة التجارية، وليس كبديل عن البروتوكول.

**حالة الدراسة:** Independent Products Discovery مستمر، **لا توجد وثيقة PRODUCTS.md نهائية بعد**. أنجزنا بالفعل مراحل متعددة من Product creation/provider activation, Variants, listing/effective stock, Shopify/Meta mappings, Pricing/Payment, Test/Real, Categories/Images, Provider recovery, Product Analytics/Finance risks, Product Detail/Support, Delete/archive, Product delivery pricing/Shopify COD, Offers/Upsells. لا تعِد دراسة كل هذا؛ اقرأ الأدلة، ثم تقدم من نقطة التوقف.

**نقطة الاستئناف الدقيقة الآن:** تحقق من العلاقة بين **Offers free Variant reward (Order Line بسعر 0)** و**Inventory reservation / shipment / FIFO cost / OrderItem origin / Merchant Order Details**. في المحادثة السابقة كنا على وشك فحص ما إذا كانت الوحدات المجانية تحجز مخزونًا حقيقيًا وتُحمّل تكلفة بضاعة، وهل تظهر للتاجر باعتبارها مكافأة Offer، وكيف يتم إظهار Upsell المقبول وسعره وسبب إضافته إلى الطلب. راجع الكود والاختبارات في Products/Shopify/Commerce/Orders/Inventory/Confirmation/Merchant Order Details، لا تفترض النتيجة من وجود حقل واحد. آخر رسالة كانت تعلن بدء هذا الفحص، **ولم يُستكمل بعد**.

**المنهج الذي قرره المستخدم شخصيًا:**
- اكتشاف مستمر عميق، Source-code-first، Screenshots للـUX، Backend + tests + permissions + state + errors + failure behavior + data lineage.
- عند دراسة Products نفحص علاقته بالأقسام الأخرى **في الوقت نفسه**، لا نؤجل العلاقات إلى آخر المشروع. إذا فحصنا كود الطرفين وتأكدنا من السلوك والقيمة، نسجل Verified Compound Advantage. إذا لم يُدرس القسم الآخر بما يكفي، نسجل Pending Cross-Section Verification ونعود إليه عند دراسته، وقد نكتشف مزايا إضافية.
- قبل الوثيقة النهائية، نعمل Cross-check مع **الوثائق السابقة في Brand Intelligence**، ونراجع كل اختلاف في الكود الحالي. Discover First, Historical Cross-check Second.
- لا تكتفِ بوصف الوظيفة تقنيًا. لكل قدرة استخرج: Feature → Merchant Problem → Wossol Mechanism → Manual Work Removed/Reduced → Merchant Value → Control/Transparency/Safety → Product Strength → Cross-System Value → Proof/Demo Moment → Marketing Angle → Website Use → Brand Relevance.
- **لا تنشئ ملفًا وCommit بعد كل رسالة.** نواصل اكتشاف عدة دفعات؛ عندما تتكون حزمة مهمة أو خطر يحتاج حفظًا، نجمعها في تحديث واحد إلى GitHub. هذه الملفات هي نقطة انتقال محفوظة بسبب حد طول المحادثة. لا تكررها.
- لا تعدّل `wossol-platform` أثناء Brand research إلا بتكليف منفصل. لا تقفز إلى Logo/Colors. لا تنشئ `PRODUCTS.md` قبل اكتمال البحث والـCross-check والجودة.
- التزم بالتمييز بين Code Verified، Tests Verified، Merchant UX Observed، Merchant Benefit Inferred، Potential Risk، Pending, Rejected، Production Not Verified. لا تَدَّعِ اختبارات أو تشغيلًا إنتاجيًا لم يحدث. تجنب أرقام توفير وقت أو زيادة مبيعات دون قياس.
- أجب بالعربية المهنية وبمجموعات اكتشافات تجارية مفيدة، مع إبراز القيمة للتاجر، العلاقات، حدودها، أدلتها، والخطوة التالية. لا تُغرق المحادثة في تقرير ملفات أو Commit كل مرة.

**الخطوة العملية الآن:** افتح ملفات GitHub الخمسة، تحقق من أحدث SHA لفرع الكود، ثم **ابدأ فورًا** فحص free reward → Order Item → Inventory Reservation → FIFO COGS → Merchant Order Details، مع إبقاء بقية العلاقات المعلقة موثقة. لا تسألني عن نقطة البداية ولا تطلب إعادة إرسال محتوى المحادثة السابقة.

**هدفنا النهائي:** وثيقة Products عميقة ومثبتة وذات قيمة تجارية واستراتيجية، مع Section Strengths وCompound Advantages وProof/Demo وMarketing/Website/Brand relevance، ثم استكمال بقية الأقسام وMaster Synthesis قبل بناء Brand Strategy والهوية البصرية.
