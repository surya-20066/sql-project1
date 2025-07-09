📘 Student Grading and GPA Management System:
This project is a SQLite-based academic records management system for handling student grades, calculating GPA, and summarizing semester results. It includes tables for students, courses, semesters, grades, and GPA. The system also integrates triggers and views for automation and reporting.

🗂️ Database Structure
1. Students
Stores basic student information.

sql
Copy
Edit
student_id INTEGER PRIMARY KEY
name TEXT
dept TEXT
batch_year INTEGER
2. Courses
Stores course-related data.

sql
Copy
Edit
course_id TEXT PRIMARY KEY
course_name TEXT
credits INTEGER
3. Semesters
Stores semester identifiers and names.

sql
Copy
Edit
semester_id INTEGER PRIMARY KEY
semester_name TEXT
4. Grades
Stores student performance per course per semester.

sql
Copy
Edit
grade_id INTEGER PRIMARY KEY AUTOINCREMENT
student_id INTEGER REFERENCES Students(student_id)
course_id TEXT REFERENCES Courses(course_id)
semester_id INTEGER REFERENCES Semesters(semester_id)
marks INTEGER
grade TEXT
5. GPA
Stores computed GPA per student per semester.

sql
Copy
Edit
student_id INTEGER
semester_id INTEGER
gpa REAL
PRIMARY KEY (student_id, semester_id)
📥 Sample Data Insertions
Includes test data for:

3 students

3 courses

2 semesters

9 grade records

📊 Features and Queries
🎓 GPA Calculation
Calculates GPA based on letter grades:

sql
Copy
Edit
A → 10, B → 8, C → 6, D → 4, F → 0
sql
Copy
Edit
SELECT student_id, semester_id,
       AVG(CASE grade
            WHEN 'A' THEN 10
            WHEN 'B' THEN 8
            WHEN 'C' THEN 6
            WHEN 'D' THEN 4
            ELSE 0 END) AS GPA
FROM Grades
GROUP BY student_id, semester_id;
✅ Pass/Fail Statistics
Determines the number of passed and failed subjects per student.

sql
Copy
Edit
SELECT student_id,
       COUNT(CASE WHEN grade = 'F' THEN 1 END) AS fails,
       COUNT(CASE WHEN grade != 'F' THEN 1 END) AS passes
FROM Grades
GROUP BY student_id;
🔁 Automatic GPA Update Trigger
A trigger that recalculates and updates GPA when new grades are inserted.

sql
Copy
Edit
CREATE TRIGGER trg_gpa_calc
AFTER INSERT ON Grades
BEGIN
    INSERT OR REPLACE INTO GPA (student_id, semester_id, gpa)
    SELECT student_id, semester_id,
           AVG(CASE grade
               WHEN 'A' THEN 10
               WHEN 'B' THEN 8
               WHEN 'C' THEN 6
               WHEN 'D' THEN 4
               ELSE 0 END)
    FROM Grades
    WHERE student_id = NEW.student_id AND semester_id = NEW.semester_id;
END;
📄 View: Semester Result Summary
Presents a readable format of each student's grade across courses and semesters.

sql
Copy
Edit
CREATE VIEW SemesterResultSummary AS
SELECT s.student_id, s.name, sem.semester_name, c.course_name, g.grade
FROM Grades g
JOIN Students s ON g.student_id = s.student_id
JOIN Courses c ON g.course_id = c.course_id
JOIN Semesters sem ON g.semester_id = sem.semester_id;
🧪 How to Use
Run the SQL script in any SQLite-compatible interface (e.g., DB Browser for SQLite).

The schema will auto-create all necessary tables and insert sample data.

Use SELECT queries to explore GPA, pass/fail stats, and student summaries.

Insert new records into Grades to trigger GPA auto-update.

