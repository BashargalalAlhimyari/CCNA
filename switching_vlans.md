أكيد. الصور تشرح Switching وبالتحديد موضوعين مهمين جدًا:

1. VLAN و Access / Trunk Ports


2. حالات منافذ السويتش و DTP (Dynamic Trunking Protocol)



وسأشرحها من الصفر حتى تفهم لماذا نستخدم Access ومتى نستخدم Trunk، وكيف يتقرر وضع المنفذ تلقائيًا.


---

أولًا: ما هو الـ Switch؟

السويتش يربط الأجهزة داخل الشبكة المحلية LAN، مثل:

PC1 ───┐
PC2 ───┤
PC3 ───┤ Switch
PC4 ───┘

لكن عندما يكون لدينا أقسام مختلفة داخل نفس السويتش، نستخدم VLAN لتقسيم الشبكة منطقيًا.

مثلاً:

VLAN 10 → الطلاب
VLAN 20 → الموظفين
VLAN 30 → الإدارة
VLAN 40 → الصوت/VoIP

رغم أن جميع الأجهزة متصلة بنفس السويتش، فإن كل VLAN تعتبر شبكة منطقية منفصلة.


---

ثانيًا: ما هي VLAN؟

VLAN اختصار:

Virtual Local Area Network

أي:

> شبكة LAN افتراضية.



بدل أن نشتري سويتشًا مستقلًا لكل قسم، نستطيع تقسيم سويتش واحد إلى عدة شبكات منطقية.

مثلاً:

SWITCH
              /     |     \
             /      |      \
        VLAN 10   VLAN 20   VLAN 30
        الطلاب    الموظفين   الإدارة

الأجهزة الموجودة في VLAN 10 لا تتواصل مباشرة مع VLAN 20 على Layer 2.


---

ثالثًا: أنواع الـ VLAN المذكورة في الصورة

الصورة تذكر:

1. Default VLAN

وهي:

VLAN 1

وهي الـ VLAN الافتراضية الموجودة في السويتش.


---

2. Data VLAN

وهي VLAN مخصصة لنقل بيانات المستخدمين.

مثلاً:

VLAN 10 → Data VLAN

ونضع فيها أجهزة الكمبيوتر والطابعات وغيرها.


---

3. Management VLAN

وهي VLAN نستخدمها لإدارة أجهزة الشبكة.

مثلاً:

VLAN 99 → Management VLAN

ونستخدمها للوصول إلى السويتش عن طريق:

SSH

Telnet

الإدارة الشبكية



---

4. Voice VLAN

تستخدم لأجهزة الهاتف الشبكي IP Phone.

مثلاً:

VLAN 40 → Voice VLAN

وهذا مهم جدًا في الشبكات التي تستخدم VoIP.


---

5. Native VLAN

هذه مهمة جدًا عندما نتحدث عن Trunk.

الـ Native VLAN هي الـ VLAN التي يتم إرسال إطاراتها على الـ Trunk بدون VLAN tag في الوضع التقليدي لـ 802.1Q.

مثلاً:

Native VLAN = 99

وسأشرحها بالتفصيل بعد قليل.


---

رابعًا: Access Port

هذه من أهم النقاط الموجودة في الصورة.

الـ Access Port هو منفذ في السويتش ينتمي عادةً إلى VLAN واحدة لحركة البيانات.

مثلاً:

PC
 │
 │
 ▼
Switch
Fa0/1
VLAN 10

هنا نقول:

Fa0/1 = Access Port
VLAN = 10

أي أن الجهاز المتصل بهذا المنفذ ينتمي إلى VLAN 10.

مثلاً:

PC1 ─── Fa0/1
         │
         └── VLAN 10


---

لماذا نستخدم Access Port؟

لأن الأجهزة العادية مثل:

PC

Printer

بعض الأجهزة الطرفية


غالبًا لا تحتاج إلى استقبال عدة VLANs عبر نفس المنفذ.

لذلك يكون المنفذ:

Access

ومرتبطًا بـ VLAN محددة.

مثال:

interface fa0/1
 switchport mode access
 switchport access vlan 10

معنى الأوامر:

switchport mode access

اجعل المنفذ Access.

و:

switchport access vlan 10

ضع المنفذ داخل VLAN 10.


---

خامسًا: Trunk Port

الـ Trunk مختلف تمامًا عن Access.

الـ Trunk يسمح بمرور عدة VLANs عبر نفس الرابط.

مثلاً عندنا سويتشان:

Switch 1                    Switch 2

VLAN 10 ─┐                ┌─ VLAN 10
         │                │
VLAN 20 ─┤──── TRUNK ─────┤─ VLAN 20
         │                │
VLAN 30 ─┘                └─ VLAN 30

بدل أن نعمل رابطًا منفصلًا لكل VLAN، نستخدم رابطًا واحدًا:

TRUNK

ويحمل:

VLAN 10
VLAN 20
VLAN 30
...


---

الفرق الأساسي بين Access و Trunk

احفظ هذه الجملة:

> Access = VLAN واحدة
Trunk = عدة VLANs



مثال:

PC ───── Access ───── Switch
              VLAN 10

أما:

Switch ───── Trunk ───── Switch
            VLAN 10
            VLAN 20
            VLAN 30


---

سادسًا: لماذا نحتاج Trunk؟

تخيل أن لدينا قسمين:

Switch 1
   │
   ├── VLAN 10
   └── VLAN 20

و Switch 2:

Switch 2
   │
   ├── VLAN 10
   └── VLAN 20

نحتاج أن تنتقل VLAN 10 من السويتش الأول إلى الثاني، وكذلك VLAN 20.

لو استخدمنا Access، لن نستطيع تمرير عدة VLANs على نفس الرابط بالشكل المطلوب.

لذلك:

Switch 1 ======== Switch 2
             TRUNK


---

سابعًا: كيف يعرف السويتش إلى أي VLAN ينتمي Frame؟

هنا يأتي موضوع VLAN Tagging.

عندما يكون لدينا Trunk، نحتاج إلى طريقة لتمييز:

هذا Frame تابع لـ VLAN 10
وهذا تابع لـ VLAN 20
وهذا تابع لـ VLAN 30

وهنا يأتي معيار:

IEEE 802.1Q

ويتم إضافة Tag إلى الإطار ليحدد الـ VLAN.

بشكل مبسط:

Frame
  ↓
802.1Q Tag
  ↓
VLAN ID

مثلاً:

VLAN ID = 10

يعني أن الـ Frame تابع لـ VLAN 10.


---

ثامنًا: Native VLAN

الـ Native VLAN مرتبطة بالـ Trunk.

مثلاً:

Switch 1 ======== Switch 2
          Trunk
          Native VLAN 99

في 802.1Q، الإطارات الخاصة بالـ Native VLAN تُرسل عادةً بدون Tag.

لذلك يجب أن يكون إعداد الـ Native VLAN متطابقًا على طرفي الـ Trunk.

مثلاً:

Switch 1
Native VLAN = 99

        │
        │ Trunk
        │

Switch 2
Native VLAN = 99

ولا ينبغي أن يكون:

Switch 1 → Native VLAN 99
Switch 2 → Native VLAN 10

لأن ذلك يؤدي إلى مشكلة Native VLAN mismatch.


---

الآن نأتي إلى الجزء المهم في الصورة: حالات منافذ السويتش

الصورة تتحدث عن DTP.

DTP اختصار:

Dynamic Trunking Protocol

وهو بروتوكول من Cisco يساعد السويتشات على التفاوض حول ما إذا كان الرابط سيصبح Trunk أم لا.

وهنا لدينا أوضاع مهمة:

Access
Trunk
Dynamic Auto
Dynamic Desirable

وهذه هي النقطة التي يمثلها الجدول في الصورة.


---

1. Access

عندما تقول للسويتش:

switchport mode access

فأنت تقول له:

> لا أريد لهذا المنفذ أن يصبح Trunk. أريده Access.



مثلاً:

interface fa0/1
 switchport mode access

النتيجة:

Fa0/1 → Access


---

2. Trunk

عندما تقول:

switchport mode trunk

فأنت تقول للسويتش:

> اجعل هذا المنفذ Trunk.



مثلاً:

interface fa0/24
 switchport mode trunk

فيصبح:

Fa0/24 → Trunk

وغالبًا نستخدمه بين:

Switch ↔ Switch

أو:

Switch ↔ Router

في بعض تصميمات الشبكات مثل Router-on-a-Stick.


---

3. Dynamic Auto

هذا الوضع معناه تقريبًا:

> أنا لا أطلب أن أصبح Trunk، لكن إذا طلب الطرف الآخر التفاوض معي فقد أصبح Trunk.



أي أنه سلبي Passive نسبيًا.

مثلاً:

Switch A                Switch B
Dynamic Auto ─────────── Trunk

قد يصبح الرابط Trunk لأن الطرف الآخر يريد ذلك.

لكن:

Dynamic Auto ───────── Dynamic Auto

لن يتحول الرابط عادةً إلى Trunk، لأن كلا الطرفين ينتظر الآخر.

احفظها:

> Auto ينتظر.




---

4. Dynamic Desirable

هذا الوضع عكس Auto تقريبًا.

هو وضع Active يحاول التفاوض مع الطرف الآخر لجعل الرابط Trunk.

أي:

Dynamic Desirable
       ↓
يحاول إنشاء Trunk

مثلاً:

Desirable ───── Auto

يمكن أن يصبح:

Trunk

لأن Desirable يبدأ التفاوض، وAuto يستجيب.


---

جدول مهم جدًا للحفظ

الفكرة الأساسية:

الطرف الأول	الطرف الثاني	النتيجة

Dynamic Auto	Dynamic Auto	Access
Dynamic Auto	Access	Access
Dynamic Auto	Trunk	Trunk
Dynamic Auto	Dynamic Desirable	Trunk
Access	Access	Access
Access	Trunk	Access
Access	Dynamic Desirable	Access
Trunk	Trunk	Trunk
Trunk	Dynamic Desirable	Trunk
Trunk	Dynamic Auto	Trunk
Dynamic Desirable	Dynamic Desirable	Trunk


ملاحظة مهمة: الجدول في الصورة يبدو أن الكتابة اليدوية جعلت بعض الخانات غير واضحة، لكن هذه هي القاعدة القياسية لأوضاع Cisco/DTP.


---

أهم 4 حالات يجب أن تحفظها

لو جاءك في الاختبار:

Auto + Auto

Auto ───── Auto

النتيجة:

Access

لأن الاثنين ينتظران.


---

Auto + Desirable

Auto ───── Desirable

النتيجة:

Trunk

لأن Desirable يبدأ التفاوض.


---

Desirable + Desirable

Desirable ───── Desirable

النتيجة:

Trunk

لأن الاثنين يحاولان إنشاء Trunk.


---

Access + أي وضع

إذا كان أحد الطرفين Access بشكل ثابت، فلن يصبح الرابط Trunk بسبب DTP.

مثلاً:

Access ───── Desirable

النتيجة:

Access


---

تاسعًا: ما هو Disable DTP؟

في الصورة يوجد:

Disable Auto

والغالب أن المقصود في سياق الدرس هو تعطيل التفاوض الديناميكي، مثل استخدام:

switchport nonegotiate

هذا يمنع المنفذ من إرسال DTP frames.

مثلاً إذا كنت تعرف مسبقًا أن المنفذ يجب أن يكون Trunk:

interface fa0/24
 switchport mode trunk
 switchport nonegotiate

هنا أنت تقول:

> أنا أعرف أن هذا Trunk، ولا أريد DTP أن يتفاوض على الوضع.




---

لماذا قد نستخدم nonegotiate؟

لأسباب أمنية وتصميمية.

بدل أن تعتمد على التفاوض الديناميكي:

DTP
 ↓
Negotiation
 ↓
Trunk

تحدد الوضع يدويًا:

switchport mode trunk

وتعطل DTP:

switchport nonegotiate

وهذا يجعل الإعداد أكثر وضوحًا وتحكمًا.


---

عاشرًا: كيف تتخيل الشبكة كاملة؟

مثلاً عندنا:

TRUNK
       ┌─────────────────┐
       │                 │
   Switch 1            Switch 2
       │                 │
   ┌───┼───┐          ┌───┼───┐
   │   │   │          │   │   │
  PC1 PC2 PC3        PC4 PC5 PC6
   │   │   │          │   │   │
 VLAN10 VLAN20       VLAN10 VLAN20

المنافذ التي توصل أجهزة المستخدم:

PC ── Access ── Switch

أما الرابط بين السويتشين:

Switch ── Trunk ── Switch

وبالتالي يستطيع الـ Trunk نقل:

VLAN 10
VLAN 20
VLAN 30
...


---

مثال عملي

افترض أن لدينا:

VLAN 10 = Students
VLAN 20 = Employees

في Switch 1:

Fa0/1 → PC1 → VLAN 10
Fa0/2 → PC2 → VLAN 20
Fa0/24 → Switch 2

إعداد المنافذ:

interface fa0/1
 switchport mode access
 switchport access vlan 10

و:

interface fa0/2
 switchport mode access
 switchport access vlan 20

أما الرابط بين السويتشين:

interface fa0/24
 switchport mode trunk

فتصبح الصورة:

PC1
 │
 │ Access
 │
VLAN 10
 │
 ├─────────────┐
               │
            Switch 1
               │
               │ Trunk
               │ VLAN 10,20
               │
            Switch 2
               │
        ┌──────┴──────┐
        │             │
      VLAN 10       VLAN 20


---

نقطة مهمة جدًا: Access لا يعني أنه لا توجد VLAN

هذه غلطة شائعة.

عندما نقول:

Access Port

لا يعني أن المنفذ خارج الـ VLAN.

بل بالعكس، المنفذ يكون Access داخل VLAN محددة.

مثلاً:

Fa0/1
Mode = Access
Access VLAN = 10

أي أن الجهاز المتصل به موجود في VLAN 10.


---

الفرق بين Access VLAN و Native VLAN

لا تخلط بينهما:

Access VLAN

تستخدم مع:

Access Port

مثلاً:

switchport access vlan 10


---

Native VLAN

تستخدم مع:

Trunk Port

مثلاً:

switchport trunk native vlan 99


---

الخلاصة التي أنصحك بحفظها

احفظ هذا التسلسل:

VLAN
 │
 ├── Access Port
 │      └── VLAN واحدة
 │
 └── Trunk Port
        └── عدة VLANs

ثم:

DTP
 │
 ├── Dynamic Auto
 │      └── ينتظر
 │
 └── Dynamic Desirable
        └── يحاول إنشاء Trunk

وأهم الحالات:

Auto + Auto          → Access
Auto + Desirable     → Trunk
Desirable + Desirable → Trunk
Auto + Trunk         → Trunk
Access + أي وضع      → Access

وأخيرًا:

Native VLAN
    ↓
مرتبطة بالـ Trunk
    ↓
الإطارات الخاصة بها تكون Untagged

إذا فهمت هذه العلاقات الأربعة: VLAN → Access → Trunk → DTP، فأنت فهمت الجزء الأساسي من درس Switching الموجود في الصورة.