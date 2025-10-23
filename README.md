# SQL-to-SQLAlchemy-Guide
A complete beginner-to-advanced comparison guide between SQL and SQLAlchemy ORM with clear examples.

**🧱 Step 2: Project Structure****

SQL-to-SQLAlchemy-Guide/
│
├── README.md                ← Main tutorial content
├── examples/
│   ├── student_model.py     ← Example SQLAlchemy model
│   ├── queries_example.py   ← CRUD + filters demo
│   └── requirements.txt     ← Libraries list
└── LICENSE


**🧾 Step 3: Write Your README.md**


Here’s a perfectly formatted GitHub-style README (Markdown) ready to paste 👇

**📘 SQL ↔ SQLAlchemy Comparison Guide**

Learn how to translate SQL queries into SQLAlchemy ORM commands with real examples.

**🧠 1️⃣ SELECT (Read all data)**

SELECT * FROM students;

students = session.query(Student).all()


✅ .query(Student) means you’re querying the students table.
✅ .all() gets all rows.

**🧠 2️⃣ SELECT with WHERE (Filtering)**

SELECT * FROM students WHERE age > 18;

students = session.query(Student).filter(Student.age > 18).all()


✅ .filter() works like a WHERE condition.

**🧠 3️⃣ ORDER BY (Sorting)**

SELECT * FROM students ORDER BY age DESC;

students = session.query(Student).order_by(Student.age.desc()).all()


✅ .order_by() sets sorting order.
✅ .desc() means descending.

**🧠 4️⃣ DISTINCT**

SELECT DISTINCT name FROM students;

names = session.query(Student.name).distinct().all()

**🧠 5️⃣ AND / OR / NOT**

SELECT * FROM students WHERE age > 18 AND city = 'Lahore';

from sqlalchemy import and_
students = session.query(Student).filter(and_(Student.age > 18, Student.city == 'Lahore')).all()

**🧠 6️⃣ INSERT**

INSERT INTO students (name, age) VALUES ('Ali', 20);

new_student = Student(name='Ali', age=20)
session.add(new_student)
session.commit()

**🧠 7️⃣ UPDATE**

UPDATE students SET age = 21 WHERE name = 'Ali';

student = session.query(Student).filter_by(name='Ali').first()
student.age = 21
session.commit()

**🧠 8️⃣ DELETE**

DELETE FROM students WHERE name = 'Ali';

student = session.query(Student).filter_by(name='Ali').first()
session.delete(student)
session.commit()

**🧠 9️⃣ Aggregates**

SELECT COUNT(*) FROM students;

from sqlalchemy import func
total = session.query(func.count(Student.id)).scalar()

**🧠 🔟 LIMIT**

SELECT * FROM students LIMIT 5;

students = session.query(Student).limit(5).all()

**🧠 11️⃣ LIKE**

SELECT * FROM students WHERE name LIKE 'A%';

students = session.query(Student).filter(Student.name.like('A%')).all()

**🧠 12️⃣ NULL Values**

SELECT * FROM students WHERE age IS NULL;

students = session.query(Student).filter(Student.age == None).all()

**🧠 13️⃣ BETWEEN**

SELECT * FROM students WHERE age BETWEEN 18 AND 25;

students = session.query(Student).filter(Student.age.between(18, 25)).all()

**🧠 14️⃣ JOIN**

SELECT * FROM students JOIN courses ON students.course_id = courses.id;

result = session.query(Student, Course).join(Course, Student.course_id == Course.id).all()

**🧠 15️⃣ GROUP BY**
SELECT city, COUNT(*) FROM students GROUP BY city;

result = session.query(Student.city, func.count(Student.id)).group_by(Student.city).all()

**🧠 16️⃣ HAVING**

SELECT city, COUNT(*) FROM students GROUP BY city HAVING COUNT(*) > 5;

result = (
    session.query(Student.city, func.count(Student.id))
    .group_by(Student.city)
    .having(func.count(Student.id) > 5)
    .all()
)

**✅ Summary Table**


Concept	SQL	SQLAlchemy ORM
Read	SELECT	.query()
Filter	WHERE	.filter()
Sort	ORDER BY	.order_by()
Insert	INSERT	.add()
Update	UPDATE	Change object + .commit()
Delete	DELETE	.delete()
Join	JOIN	.join()
Group	GROUP BY	.group_by()
Count	COUNT()	func.count()
Limit	LIMIT	.limit()

**🧩 Quick Takeaway**

SQL → Manual queries

SQLAlchemy → Pythonic ORM layer

ORM (Object Relational Mapping) → Treat tables as classes

Core vs ORM

Core → Closer to SQL

ORM → Easier and object-based
