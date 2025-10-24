# SQL-to-SQLAlchemy-Guide
A complete beginner-to-advanced comparison guide between SQL and SQLAlchemy ORM with clear examples.


<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/b7b41f10-7368-49fe-aab1-e4b523f2a209" />




<img width="775" height="538" alt="image" src="https://github.com/user-attachments/assets/d724d272-3d3d-490d-957c-174340082673" />


**📘 SQL ↔ SQLAlchemy Comparison Guide**

Learn how to translate SQL queries into SQLAlchemy ORM commands with real examples.

**1️⃣ SELECT (Read all data)**

SELECT * FROM students;

students = session.query(Student).all()


✅ .query(Student) means you’re querying the students table.

✅ .all() gets all rows.

**🧠 2️⃣ SELECT with WHERE (Filtering)**

SELECT * FROM students WHERE age > 18;

students = session.query(Student).filter(Student.age > 18).all()


✅ .filter() works like a WHERE condition.

**3️⃣ ORDER BY (Sorting)**

SELECT * FROM students ORDER BY age DESC;

students = session.query(Student).order_by(Student.age.desc()).all()


✅ .order_by() sets sorting order.

✅ .desc() means descending.

**4️⃣ DISTINCT**

SELECT DISTINCT name FROM students;

names = session.query(Student.name).distinct().all()

**5️⃣ AND / OR / NOT**

SELECT * FROM students WHERE age > 18 AND city = 'Lahore';

from sqlalchemy import and_
students = session.query(Student).filter(and_(Student.age > 18, Student.city == 'Lahore')).all()

**6️⃣ INSERT**

INSERT INTO students (name, age) VALUES ('Ali', 20);

new_student = Student(name='Ali', age=20)
session.add(new_student)
session.commit()

**7️⃣ UPDATE**

UPDATE students SET age = 21 WHERE name = 'Ali';

student = session.query(Student).filter_by(name='Ali').first()
student.age = 21
session.commit()

**8️⃣ DELETE**

DELETE FROM students WHERE name = 'Ali';

student = session.query(Student).filter_by(name='Ali').first()
session.delete(student)
session.commit()

**9️⃣ Aggregates**

SELECT COUNT(*) FROM students;

from sqlalchemy import func
total = session.query(func.count(Student.id)).scalar()

**🔟 LIMIT**

SELECT * FROM students LIMIT 5;

students = session.query(Student).limit(5).all()

**11️⃣ LIKE**

SELECT * FROM students WHERE name LIKE 'A%';

students = session.query(Student).filter(Student.name.like('A%')).all()

**12️⃣ NULL Values**

SELECT * FROM students WHERE age IS NULL;

students = session.query(Student).filter(Student.age == None).all()

**13️⃣ BETWEEN**

SELECT * FROM students WHERE age BETWEEN 18 AND 25;

students = session.query(Student).filter(Student.age.between(18, 25)).all()

**🔹 INNER JOIN**

SELECT books.title, authors.name
FROM books
INNER JOIN authors ON books.author_id = authors.id;

result = session.query(Book.title, Author.name).join(Author).all()


✅ .join() creates an INNER JOIN between tables.
✅ It only returns records with matching keys in both tables.

**🔹 LEFT JOIN**

SELECT authors.name, books.title
FROM authors
LEFT JOIN books ON authors.id = books.author_id;

result = session.query(Author.name, Book.title).outerjoin(Book).all()


✅ .outerjoin() performs a LEFT JOIN.
✅ Returns all authors even if they don’t have a book.

**🔹 RIGHT JOIN**

(SQLAlchemy doesn’t have direct right join — reverse the order)

result = session.query(Book.title, Author.name).join(Author, isouter=True).all()

**🔹 FULL JOIN**

(SQLAlchemy Core supports it, ORM doesn’t)

from sqlalchemy import select, union_all

q1 = select(Book.title, Author.name).join(Author)
q2 = select(Book.title, Author.name).outerjoin(Author)
result = session.execute(q1.union_all(q2)).all()

**🔹 SELF JOIN**

SELECT e1.name AS Employee, e2.name AS Manager
FROM employees e1
JOIN employees e2 ON e1.manager_id = e2.id;

e1 = aliased(Employee)
e2 = aliased(Employee)

result = (
    session.query(e1.name.label("Employee"), e2.name.label("Manager"))
    .join(e2, e1.manager_id == e2.id)
    .all()
)

**🧱 SQL UNION & UNION ALL**

**🔹 UNION**

SELECT name FROM authors
UNION
SELECT name FROM editors;

from sqlalchemy import select

q1 = select(Author.name)
q2 = select(Editor.name)
union_q = q1.union(q2)
session.execute(union_q).all()


✅ .union() removes duplicates.

**🔹 UNION ALL**

SELECT name FROM authors
UNION ALL
SELECT name FROM editors;

union_all_q = q1.union_all(q2)
session.execute(union_all_q).all()


✅ .union_all() keeps duplicates.

**🧮 GROUP BY and HAVING**

🔹 GROUP BY
SELECT authors.name, COUNT(books.id)
FROM authors
JOIN books ON authors.id = books.author_id
GROUP BY authors.name;

from sqlalchemy import func

result = (
    session.query(Author.name, func.count(Book.id))
    .join(Book)
    .group_by(Author.name)
    .all()
)

**🔹 HAVING**

SELECT authors.name, COUNT(books.id)
FROM authors
JOIN books ON authors.id = books.author_id
GROUP BY authors.name
HAVING COUNT(books.id) > 1;

result = (
    session.query(Author.name, func.count(Book.id))
    .join(Book)
    .group_by(Author.name)
    .having(func.count(Book.id) > 1)
    .all()
)

**🔍 EXISTS**

SELECT * FROM authors
WHERE EXISTS (SELECT id FROM books WHERE price > 600);

from sqlalchemy import exists

subq = session.query(Book.id).filter(Book.price > 600).exists()
session.query(Author).filter(subq).all()

**🔸 ANY / ALL**

SELECT * FROM books WHERE price > ANY (400, 600);

from sqlalchemy.sql import any_, all_

session.query(Book).filter(Book.price > any_([400, 600])).all()


✅ any_() checks if the condition matches any element.
✅ all_() checks if condition matches all elements.

**⚙️ SELECT INTO / INSERT INTO SELECT**

**🔹 SELECT INTO**

SELECT * INTO new_table FROM books WHERE price > 500;


(SQLAlchemy doesn’t directly create a new table — use ORM model creation)

**🔹 INSERT INTO SELECT**

INSERT INTO old_books (title, price)
SELECT title, price FROM books WHERE price < 400;

from sqlalchemy import insert, select

stmt = insert(OldBook).from_select(
    ["title", "price"],
    select(Book.title, Book.price).where(Book.price < 400)
)
session.execute(stmt)
session.commit()

**🧠 CASE Statement**

SELECT title,
CASE
    WHEN price > 600 THEN 'Expensive'
    ELSE 'Cheap'
END AS price_tag
FROM books;

from sqlalchemy import case

case_query = session.query(
    Book.title,
    case(
        (Book.price > 600, "Expensive"),
        else_="Cheap"
    ).label("price_tag")
).all()

**NULL Functions**

SELECT COALESCE(price, 0) FROM books;

from sqlalchemy import func

session.query(func.coalesce(Book.price, 0)).all()


✅ func.coalesce() replaces NULL values with a default.

**STORED PROCEDURES**

CALL get_books_by_author('John');

from sqlalchemy import text

session.execute(text("CALL get_books_by_author(:name)"), {"name": "John"})

**SQL Comments**

-- This is a comment
SELECT * FROM books;

from sqlalchemy import text

session.execute(text("-- Comment\nSELECT * FROM books"))

