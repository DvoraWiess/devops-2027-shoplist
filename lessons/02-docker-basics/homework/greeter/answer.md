1. docke build -t greeter :v1 devopps/lessons/02/hw/greeter
2. docker run -d --name greeter -p 9000:8000 greeter:v1
-e GREETING=Shalom
4.{"message": "Shalom, Rivka!"}
- COPY app.py . וכל מה שאחריו

למה: דוקר בודק כל שלב לפי הסדר. כל עוד הקבצים שהשלב מעתיק לא השתנו, הוא משתמש ב-cache.
requirements.txt לא השתנה, ולכן גם התקנת התלויות נלקחת מה-cache.
app.py כן השתנה, ולכן COPY . . רץ מחדש, ומהרגע שהשלב הזה נבנה מחדש, כל השלבים שאחריו נבנים מחדש גם הם.
זו בדיוק הסיבה שמעתיקים את requirements.txt ומתקינים לפני העתקת הקוד
