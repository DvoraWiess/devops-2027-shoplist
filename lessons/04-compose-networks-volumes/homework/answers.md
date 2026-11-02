<div dir="rtl">

1. ל-backend אין ports, אז הוא לא חשוף למחשב, אבל stats נמצא איתו באותה רשת של Compose ומגיע אליו לפי שם השירות: http://backend:5000. הדפדפן מגיע אליו רק דרך Nginx בפורט 8080, שמעביר בקשות של /api/ ל-backend.

2. אחרי down ו-up המוצרים נשארים, כי הם שמורים ב-named volume (postgres-data) ש-down לא מוחק. אחרי down -v ה-volume נמחק והמוצרים נעלמים. ב-up הבא init.sql רץ שוב ויוצר טבלה ריקה.

3. זה bind mount, כי הצד השמאלי הוא נתיב לקובץ במחשב (מתחיל ב-../) ולא שם של volume. שינוי ב-init.sql אחרי שהמסד כבר נוצר לא ישפיע, כי Postgres מריץ את הקובץ רק כשתיקיית הנתונים ריקה. כדי שהשינוי ייכנס צריך down -v.

4. הרשת היא docker_default וה-volume הוא docker_postgres-data. Compose מוסיף לפני כל שם את שם הפרויקט, שהוא כברירת מחדל שם התיקייה שבה נמצא קובץ ה-Compose (docker).

5. באימג' הסופי יש רק Nginx, קבצי האתר המעובדים מ-dist ו-nginx.conf, בלי Node ובלי build.js. אין CMD כי אימג' הבסיס של Nginx כבר מגדיר CMD שמפעיל את השרת, והאימג' שלנו יורש אותו. ל-stats לא צריך multi-stage כי אין לו שלב בנייה: Node נחוץ לו גם בזמן הריצה.

6. kitchen: 244MB, multi: 92.7MB, single: 92.8MB. multi כמעט בגודל של single כי בשניהם האימג' הסופי הוא Nginx עם קבצי אתר בלבד, וה-Node שבו נבנו הקבצים נשאר בשלב הבנייה ולא הועתק.

7. כשכמה location מתאימים, Nginx בוחר את הקידומת הארוכה והספציפית ביותר, ו-/api/stats ארוך מ-/api/. בגלל ה-/ בסוף proxy_pass, Nginx מחליף את /api/ ב-/, ולכן הבקשה /api/products מגיעה ל-backend בתור /products.

</div>