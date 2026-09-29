# WOSSOL — CURRENT PRODUCT DEEP STUDY PROTOCOL
## بروتوكول الدراسة العميقة للمنتج الحالي قبل استكمال البراند والهوية البصرية

**Status:** ACTIVE — MANDATORY WORKING PROTOCOL  
**Protocol type:** Living document — وثيقة تشغيلية حيّة تُحدّث عند اكتشاف قاعدة منهجية جديدة  
**Purpose:** Current Product Deep Study → Brand Intelligence → Brand Strategy → Visual Identity  
**Primary Product Source:** `jetshop7/wossol-platform`  
**Brand Intelligence Repository:** `jetshop7/wossol-brand-intelligence`

---

# 1. الهدف من هذه المرحلة

هذه المرحلة ليست Product Audit سريعًا، وليست مراجعة UI فقط، وليست إعادة تلخيص لوثائق Wossol السابقة.

الهدف هو بناء **فهم عميق وشامل للمنتج الحالي Wossol كما يعمل فعليًا الآن** قبل استكمال Brand Strategy والهوية البصرية.

نريد أن نفهم:
- ماذا يفعل كل قسم فعليًا؟
- كيف يستخدمه التاجر؟
- ما المشكلة التي يحلها؟
- ما العمل اليدوي الذي يزيله أو يقلله؟
- كم بحث أو تنقل أو إعادة إدخال أو مطابقة أو حساب يختصر؟
- أين يمنح Control وTransparency وSafety؟
- أين يوفر الوقت والجهد والانتباه؟
- أين يربط معلومات كانت منفصلة؟
- ما القيمة التي لا تظهر إلا عند ربط أكثر من قسم؟
- ما نقاط القوة الصغيرة أو المخفية التي قد تصبح Marketing Assets مهمة؟
- ما الذي يمكن إثباته وعرضه في Website / Demo / Social / Sales؟
- ما نقاط الضعف أو الاحتكاك الحالية؟
- وما الذي يعنيه كل ذلك للـBrand والهوية البصرية؟

> **بعد إنهاء هذه المرحلة يجب أن نعرف Wossol كمنتج ونظام وتجربة تاجر بعمق كافٍ لبناء Brand يعكس قوته الحقيقية، لا تصورًا سطحيًا عنه.**

---

# 2. قاعدة المصدر الأساسي — Code First

الكود الحالي هو المرجع الأساسي للحقيقة التشغيلية.

يجب دراسة ما يلزم من:
- Frontend
- Backend
- Services / Controllers
- Data models / DTOs / Contracts
- Tests
- Integrations
- State transitions
- Permissions
- Audit / History
- Error / Failure behavior
- Cross-module dependencies
- Relevant current technical specifications

لا يجوز الاعتماد على وثيقة قديمة إذا تعارضت مع الكود الحالي المثبت.

---

# 3. دور Screenshots

الـScreenshots مهمة لفهم:
- Current UX
- Information hierarchy
- Merchant-visible functionality
- Navigation / States / Actions / Labels
- Friction
- Visual presentation

لكنها ليست Source of Truth الوحيد.

> **Screenshots explain the visible experience. Code and tests explain the actual system behavior.**  
> الصور تشرح التجربة الظاهرة، والكود والاختبارات يثبتان السلوك الحقيقي.

يجب استخدام الاثنين معًا.

---

# 4. عمق البحث المطلوب

هذه ليست مراجعة سريعة. لا ننتقل للقسم التالي لمجرد فهم واجهته الأساسية.

يجب البحث عن:
- obvious features
- hidden system behavior
- edge cases
- permissions
- safety rules
- automation
- historical truth
- data provenance
- synchronization
- fallback behavior
- failure isolation
- merchant control
- cross-system behavior
- operational consequences
- downstream effects
- upstream dependencies

لا نسأل فقط: **ماذا تفعل هذه الشاشة؟**

بل أيضًا:
- ماذا يحدث خلفها؟
- من يملك الحقيقة؟
- من أين تأتي البيانات وإلى أين تذهب؟
- ماذا يحدث عند الفشل؟
- ما الذي يوفره ذلك على التاجر؟
- ما القيمة التجارية الناتجة؟

---

# 5. سلسلة التحليل الإلزامية لكل Capability

**Feature  
→ Merchant Problem  
→ Wossol Mechanism  
→ Manual Work Removed / Reduced  
→ Merchant Value  
→ Control / Transparency / Safety  
→ Product Strength  
→ Cross-System Value  
→ Proof / Demo Moment  
→ Marketing Angle  
→ Website Use  
→ Brand Relevance**

لا تعتبر Feature مهمة لمجرد وجودها تقنيًا. يجب تفسير **لماذا تهم التاجر**.

---

# 6. لا ندرس الأقسام كجزر منفصلة

Wossol نظام مترابط. بعض أقوى مزاياه لا توجد داخل Module واحد.

عند دراسة قسم جديد يجب مراجعة الأقسام السابقة ذات العلاقة.

أمثلة:
- Orders ← Products / Stores / Advertising / Shopify / Payment
- Inventory ← Products / Orders / External Shipping / Local Pickup
- Confirmation ← Orders / Customers / Products / Team
- Finance ← Orders / Delivery / Inventory Cost / Advertising when relevant
- Analytics ← معظم الأقسام التي تغذيه

---

# 7. Compound Product Advantages

يجب البحث عمدًا عن:

**Compound Product Advantage = ميزة مركبة ناتجة عن ترابط عدة أجزاء من Wossol.**

قد لا توجد الميزة في Products وحده أو Orders وحده، لكن:
**Product + Advertising + Order provenance + Inventory + Confirmation + Delivery + Finance**
قد تنتج قدرة أكبر بكثير من مجموع Features منفصلة.

لكل قسم نفصل بين:
- **Section Strengths** — قوة القسم نفسه.
- **Cross-System / Compound Strengths** — القوة الناتجة عن ارتباطه بأجزاء أخرى.

---

# 8. مراجعة الوثائق السابقة — Discover First, Cross-check Second

وثائق Brand Intelligence القديمة ليست Source of Truth لهذه الدراسة، لكنها مصدر تحقق ثانوي مهم.

الترتيب:
1. Screenshots / Current UX.
2. Current code.
3. Backend behavior.
4. Tests.
5. Current supporting specs where useful.
6. Independent analysis.
7. Cross-section analysis.
8. ثم مراجعة Brand Intelligence السابقة.

> **Discover independently first. Cross-check second.**  
> نكتشف بأنفسنا أولًا، ثم نستخدم البحث السابق للتحقق من أننا لم نفوّت شيئًا.

---

# 9. Mandatory Pre-Document Cross-Check

قبل كتابة الوثيقة النهائية لأي Section نبحث داخل `jetshop7/wossol-brand-intelligence` عن:
- Section Intelligence
- Review corrections
- Master synthesis references
- Product Advantage references
- Marketing Asset references
- Integration supplements
- Director findings
- أي وثيقة مرتبطة فعليًا بالقسم

ثم:
- ما وجدناه ولم تجده الدراسة السابقة → نحتفظ به.
- ما وجدته الدراسة السابقة ولم نجده → نعود للكود الحالي ونتحقق.
- ما أصبح قديمًا → لا نعتمده كحقيقة حالية.
- ما لا يمكن إثباته → نسجله كغير متحقق.
- عند التعارض → Current Code + Current Tests لهما الأولوية في وصف Current Product.

---

# 10. Current ≠ Future

نفصل بوضوح بين:
- **CURRENT:** موجود ويعمل ويمكن إثباته الآن.
- **FUTURE:** Vision / Product Idea / Planned Direction.

Future Vision يفيد لاحقًا في Brand Strategy والهوية طويلة الأجل، لكنه لا يتحول إلى Current Marketing Claim.

---

# 11. Test Product Rule — Current Authoritative Understanding

Test / Real classification مستقلة عن Stock Quantity.

وجود Stock لا يمنع Product من أن يكون Test، لأن التاجر قد يملك كمية صغيرة ويريد إعادة اختبار المنتج قبل زيادة المخزون.

> **Test state describes commercial testing state, not inventory state.**  
> حالة Test تصف حالة الاختبار التجاري، وليست حالة المخزون.

Current understanding to verify against code continuously:
- Test Product may have stock.
- Test Product cannot use Upsells.
- Test Product cannot be used as an Upsell item for a Real Product.
- Shopify orders created for Test Product follow Test Order behavior.
- Incomplete Order recovery is disabled for Test Products.
- Test/Real state is owned by Wossol and propagated where supported.

أي وثيقة قديمة تناقض الكود الحالي لا تفرض نفسها على المنتج.

---

# 12. Merchant Work Reduction

في كل قسم نبحث صراحة عن العمل الذي كان التاجر سيقوم به يدويًا أو خارج Wossol، مثل:
searching, copying, re-entering, matching, reconciling, calculating, checking, monitoring, switching tools, spreadsheets, provider dashboards, comparing, tracking, rebuilding context.

الهدف ليس الادعاء بأن Wossol “سهل”، بل معرفة **كيف ولماذا أصبح العمل أسهل**.

---

# 13. Effort Compression

> **Effort Compression — اختصار الجهد والتعقيد**

لا يعني clicks أقل فقط، بل قد يعني تحويل:
**عدة أدوات + بحث + Excel + IDs + نقل بيانات + حسابات + مقارنة + متابعة**
إلى:
**عدد أقل من الإجراءات ذات المعنى.**

---

# 14. Attention Compression

> **Attention Compression — اختصار الانتباه**

Wossol لا تختصر فقط العمل، بل تحاول تقليل الأشياء التي يحتاج التاجر أن يبحث عنها ويراقبها بنفسه.

مبدأ قيد الاختبار:
> **The merchant should not continuously monitor the system; the system should increasingly bring relevant work to the merchant.**

أي: بدل أن يراقب التاجر النظام باستمرار، يجلب النظام له ما يحتاج تدخله عندما يكون ذلك ممكنًا.

---

# 15. Control

في كل Section نبحث:
- ماذا يرى التاجر؟
- ماذا يختار أو يغير أو يوقف؟
- ماذا يمكنه override؟
- ما الذي يحتاج explicit confirmation؟
- ما الذي يتم آليًا؟
- وما الذي لا يتم آليًا عمدًا؟

> **Power without unnecessary operational burden.**  
> قدرة وسيطرة بدون عبء تشغيلي غير ضروري.

---

# 16. Transparency

نبحث عن:
source, status, history, timestamps, actor, provenance, unavailable/incomplete states, uncertainty, reasons, impact, audit trail.

> **The truth needed for a decision should be available when needed.**  
> الحقيقة اللازمة لاتخاذ القرار يجب أن تكون متاحة بوضوح عند الحاجة.

---

# 17. Safety and Trust

نبحث عن السلوك الذي يمنع:
- wrong assumptions
- accidental mappings
- duplicate operations
- misleading zeroes
- silent failures
- unauthorized actions
- incorrect stock behavior
- false attribution
- stale relationships
- unsafe automation

التفاصيل التقنية التي تحقق ذلك قد تكون أساسًا مهمًا للثقة في Brand.

---

# 18. Proof / Demo Moments

لكل ميزة قوية نسأل: **هل يمكن إثباتها بصريًا أو في Demo؟**

مثال:
بدل “Wossol connects Shopify” فقط:
**Connect Shopify → choose store → Wossol Product ↔ Shopify Product → exact Variant mappings → visible connection status.**

Proof أقوى من claim عام.

---

# 19. Marketing & Website Extraction

كل Section document يجب أن يحتوي قسمًا باسم:

## Marketing & Website Extraction

ويستخرج:
- merchant problem
- value proposition
- strongest benefits
- explainable capabilities
- proof points
- demo moments
- possible website sections / feature pages
- educational / social content
- SEO opportunities
- AEO opportunities

**AEO = Answer Engine Optimization = تحسين المحتوى لمحركات الإجابة.**

الهدف أن يصبح موقع Wossol:
> **أفضل مصدر رسمي لفهم ما تفعله Wossol ولماذا يهم ذلك للتاجر.**

---

# 20. Hidden Value Translation Rule — قاعدة ترجمة القيمة المخفية

هذه قاعدة إلزامية لكل Capability تبدو بسيطة في الواجهة بينما تخفي وراءها منطقًا مهمًا في النظام.

لا نكتفي في الموقع أو التسويق بوصف الفعل المرئي مثل:
- Connect
- Select
- Confirm
- Delete
- Refresh
- Sync

ولا نشرح Backend للتاجر بتعقيد تقني.

بدل ذلك، لكل حالة مهمة نستخرج أربع طبقات:

1. **Visible Merchant Action — ما يراه التاجر ويفعله.**
2. **Hidden System Intelligence / Safety — ما يفعله Wossol خلف الواجهة من دقة، تحقق، حفظ تاريخ، منع أخطاء، مزامنة، أو حماية.**
3. **Merchant Meaning — لماذا يهم ذلك للتاجر عمليًا.**
4. **Simple Public Explanation — كيف نشرح هذه القيمة ببساطة وصدق على Website / Demo / Sales / Social / SEO / AEO.**

مثال Shopify Product/Variant linking:

**Visible:** Select → Confirm → Connected.

**Hidden:** Wossol uses explicit Product and exact Variant mapping rather than silently guessing from similar names/SKUs, keeps mapping state visible, and supports controlled change/end behavior.

**Merchant Meaning:** سهولة الربط لا تأتي على حساب الدقة أو السيطرة، وتقل احتمالات الربط الخاطئ أو الغامض.

**Public Explanation Example:**
> اربط منتجاتك بـShopify بخطوات بسيطة، مع ربط دقيق للمنتجات والـVariants بدل الاعتماد على تخمينات قد تربط العنصر الخطأ. ترى ما تم ربطه وتؤكد الاختيارات المهمة ويمكنك إدارة الربط بوضوح.

المبدأ:

> **Do not market only the button. Explain the valuable system behavior the button safely compresses.**

أي:

> **لا نسوّق الزر فقط؛ نشرح القيمة التي يختصرها هذا الزر خلفه.**

هذا مهم للبشر، ولمحركات البحث، ولمحركات الإجابة والـAI؛ لأن عبارات عامة مثل “One-click integration” وحدها لا تشرح عمق القدرة ولا سبب أهميتها.

يجب تسجيل هذه الحالات أثناء الدراسة، لا تأجيل اكتشافها إلى مرحلة كتابة الموقع.

---

# 21. Claims Discipline

نميز بين:
- Proven Current Capability
- Reasonable Product Interpretation
- Emerging Product Principle
- Future Vision
- Unverified Hypothesis

ولا نستخدم claims مثل unique / first / only / best / fully automated / all-in-one إلا إذا كانت محددة ومثبتة بما يكفي.

---

# 22. Current Weaknesses Matter

لكل Section نبحث أيضًا عن:
friction, confusing UX, missing context, weak visual hierarchy, unnecessary steps, incomplete connections, technical limitations, poor explanation, fragile behavior, missing capability.

الدراسة ليست Marketing exercise فقط.

---

# 23. Visual / UX Observation

نسجل الملاحظات البصرية أثناء الدراسة دون إعادة تصميم المنتج الآن، مثل:
- hierarchy problems
- unclear priorities
- weak status communication
- excessive density
- weak visual expression of intelligence
- inconsistent interaction
- places where product capability is stronger than its visual presentation

ثم نعود إليها في Art Direction / UI / Visual Identity.

---

# 24. Final Section Document

بعد البحث + العلاقات + cross-check ننشئ وثيقة داخل:
`07-current-product-deep-study/`

مثل:
`HOME.md`, `PRODUCTS.md`, `ORDERS.md`, `INVENTORY.md`.

يجب أن تكون reusable source of truth، وتحتوي قدر الإمكان:
1. Scope / evidence basis
2. Section purpose
3. Current feature truth
4. Detailed merchant journeys
5. Hidden backend/system behavior
6. Merchant problems solved
7. Work removed/reduced
8. Control
9. Transparency
10. Safety
11. Reliability
12. Cross-section relationships
13. Compound advantages
14. Proof/demo moments
15. Hidden Value Translation cases
16. Current weaknesses/friction
17. Marketing & Website Extraction
18. Brand relevance
19. Claims allowed / not allowed
20. Open verification gaps
21. Previous Brand Intelligence cross-check findings

---

# 25. Folder Isolation

`07-current-product-deep-study/` مستقل عن:
- `02-section-intelligence/`
- `03-master-synthesis/`
- Review History القديمة

ويمثل:
> **Current Product Deep Study performed specifically to inform the next Brand & Visual Identity stage.**

يجب أن يوضح README دور هذا المجلد ويمنع الالتباس بين النسخ المختلفة.

---

# 26. Study Order

لا نتبع Sidebar بشكل أعمى، بل dependency logic.

Current working sequence:
**Home → Products → Orders → Inventory → ...**

Analytics يدرس قرب النهاية لأنه يعتمد على حقائق من أقسام كثيرة. Market Center يمكن وضعه متأخرًا إذا كان ذلك يخدم فهم Intelligence layer.

---

# 27. Cumulative Knowledge Rule

مع كل Section جديد لا نبدأ من الصفر.

المعرفة الجديدة قد:
- تؤكد ميزة سابقة،
- توسعها،
- تكشف سببها أو أثرها downstream،
- تحول ميزتين إلى Compound Advantage،
- أو تصحح فهمًا سابقًا.

إذا غيّر قسم جديد حقيقة في وثيقة سابقة، نسجل الحاجة لتحديثها.

---

# 28. End-of-Study Synthesis

بعد إنهاء جميع الأقسام نبني أولًا:

# Wossol Current Product Strength & Merchant Value Master

ويجمع:
- strongest current capabilities
- merchant jobs solved
- merchant work removed
- effort compression
- attention compression
- control
- transparency
- trust/safety
- connected-system advantages
- compound advantages
- proof moments
- hidden value translations
- current differentiation evidence
- marketing assets
- website content architecture implications
- UX implications
- product weaknesses
- strongest brand-relevant truths

ثم نضيف Future Vision كطبقة منفصلة.

---

# 29. العودة إلى Branding

بعد فهم Current Product كاملًا:

**Current Product Truth  
+ Current Merchant Value  
+ Compound System Strengths  
+ Future Vision  
+ Market / Competitor Context**

ثم نعود إلى:
**Brand Strategy → Positioning refinement → Brand Concept → Visual Identity Principles → Art Direction → Colors → Typography → Graphic System → Imagery → Motion → Logo strategy/design → Applications → Guidelines.**

---

# 30. Master Principle

> **Do not rush to summarize. Investigate until the product behavior, merchant value and cross-system consequences are understood.**

السؤال الدائم ليس فقط “What feature exists?” بل:

> **What becomes possible, easier, safer, clearer or more controllable for the merchant because this exists — especially when it connects with the rest of Wossol?**

أي:

> **ما الذي أصبح ممكنًا أو أسهل أو أكثر أمانًا أو وضوحًا أو سيطرة للتاجر بسبب هذه القدرة، خصوصًا عندما ترتبط ببقية Wossol؟**

---

# 31. Definition of Done for a Section

لا يعتبر Section مكتملًا حتى:
- [ ] تمت مراجعة Screenshots المتاحة.
- [ ] تمت مراجعة Frontend ذات الصلة.
- [ ] تمت مراجعة Backend ذات الصلة.
- [ ] تمت مراجعة Tests المهمة.
- [ ] تم فحص relevant current specs.
- [ ] تم تتبع dependencies المهمة.
- [ ] تم فحص علاقته بالأقسام السابقة.
- [ ] تم استخراج merchant workflows.
- [ ] تم استخراج manual work removed/reduced.
- [ ] تم استخراج control/transparency/safety.
- [ ] تم البحث عن hidden strengths.
- [ ] تم البحث عن compound advantages.
- [ ] تم استخراج Hidden Value Translation للحالات المهمة.
- [ ] تم تحديد Proof/Demo moments.
- [ ] تم تسجيل weaknesses/friction.
- [ ] تم استخراج Marketing & Website opportunities.
- [ ] تمت مقارنة النتائج بوثائق Brand Intelligence السابقة.
- [ ] تم التحقق من أي معلومة قديمة قبل إعادة استخدامها.
- [ ] تم فصل Current عن Future.
- [ ] تمت كتابة الوثيقة النهائية داخل `07-current-product-deep-study/`.
- [ ] تم تسجيل أي تحديث مطلوب لوثيقة سابقة بسبب معرفة جديدة.

**إذا لم تتحقق هذه الشروط، لا نغلق القسم ولا ننتقل باعتباره مكتملًا.**

---

# 32. Protocol Maintenance Rule

هذه الوثيقة هي المرجع التشغيلي لهذه المرحلة.

إذا ظهرت أثناء العمل قاعدة منهجية جديدة تؤثر على جودة الدراسة أو على كيفية استخراج Product / Merchant / Marketing / Brand value:
1. نقيّم هل هي قاعدة متكررة وليست ملاحظة لحالة واحدة.
2. إذا كانت متكررة، نضيفها مباشرة إلى هذه الوثيقة في GitHub.
3. من التحديث التالي فصاعدًا تصبح جزءًا إلزاميًا من طريقة العمل.
4. لا نعتمد على ذاكرة المحادثة وحدها لحفظ القواعد المهمة.

وبذلك يبقى GitHub هو المرجع الدائم والمشترك لطريقة تنفيذ Current Product Deep Study.
