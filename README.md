# PLAYGATE — متجر الألعاب الرقمية المرخصة

موقع ثابت (HTML/CSS/JS) + Supabase. لا يحتاج خطوة بناء، ويُنشر على Netlify أو Vercel أو Cloudflare Pages أو GitHub Pages.

## التشغيل
1. أنشئ مشروعاً في https://supabase.com
2. SQL Editor ← الصق محتوى `supabase/schema.sql` ← Run.
3. Authentication ← Users ← Add user (بريدك وكلمة مرور قوية).
4. اجعله مديراً: شغّل `supabase/make_admin.sql` (البريد فيه مضبوط مسبقاً).
5. Project Settings ← API ← انسخ Project URL وanon key إلى `js/config.js`. (لا تضع service_role).
6. افتح `index.html` محلياً: `python3 -m http.server 8000` ثم http://localhost:8000
   (بدون إعداد Supabase يعمل بوضع تجربة بمنتجات وهمية.)
7. ادخل `/admin.html` وأضف منتجاتك وصورها، وعدّل رقم واتساب وبيانات التحويل من «الإعدادات».

## النشر
- Netlify: اسحب مجلد المشروع إلى app.netlify.com/drop.
- أو ارفعه إلى GitHub واربطه بـ Vercel / Cloudflare Pages (Build command فارغ، المجلد الجذر).
- في Supabase ← Authentication ← URL Configuration أضف رابط موقعك.
- أضف دومينك واستخدم HTTPS (يوفره المنصة تلقائياً).

## تدفق الطلب والأمان
- الأسعار تُحسب في قاعدة البيانات (`create_order`) فلا يمكن التلاعب بها من المتصفح.
- لا أحد غير المدير يقرأ الطلبات أو العملاء أو الدفعات (RLS).
- trigger يمنع «تم تأكيد الدفع» و«مكتمل» ما لم يوجد سجل دفع موثّق، وصورة التحويل لا تُعد إثباتاً.
- لا تُخزَّن بيانات بطاقات ولا كلمات مرور بنكية.
- لا تبع إلا منتجات تملك حق بيعها وتوافق شروط المنصات.

## ما يحتاج ربطاً من طرفك (يتطلب حسابات تجارية)
- **بوابة الدفع** (Moyasar / Tap / HyperPay): أنشئ Supabase Edge Function تستقبل webhook البوابة، تتحقق من التوقيع، ثم تُدخل صف في `payments` بـ `verified=true` وتحدّث الطلب (باستخدام service_role داخل الـ Function فقط).
- **WhatsApp Business API**: من نفس الـ Function أو عبر trigger على `orders` أرسل إشعار تغيّر الحالة وأكواد التسليم بعد `completed`. حالياً يعمل زر واتساب مع رسالة جاهزة برقم الطلب.
- لتسليم الأكواد تلقائياً أضف جدول `product_keys` مرتبطاً بالطلبات المكتملة.
