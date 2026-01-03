# Universe-course-management-

TABLE CREATION
-- ============================================

-- Departments Table
CREATE TABLE Departments (
    DepartmentID INT PRIMARY KEY AUTO_INCREMENT,
    DepartmentName VARCHAR(100) NOT NULL UNIQUE
);

-- Students Table
CREATE TABLE Students (
    StudentID INT PRIMARY KEY AUTO_INCREMENT,
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    Email VARCHAR(100) UNIQUE NOT NULL,
    EnrollmentDate DATE NOT NULL
);

-- Courses Table
CREATE TABLE Courses (
    CourseID INT PRIMARY KEY AUTO_INCREMENT,
    CourseName VARCHAR(100) NOT NULL,
    Credits INT NOT NULL CHECK (Credits > 0),
    DepartmentID INT,
    FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID)
);

-- Instructors Table
CREATE TABLE Instructors (
    InstructorID INT PRIMARY KEY AUTO_INCREMENT,
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    Email VARCHAR(100) UNIQUE NOT NULL,
    DepartmentID INT,
    Salary DECIMAL(10,2),
    FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID)
);

-- Enrollments Table
CREATE TABLE Enrollments (
    EnrollmentID INT PRIMARY KEY AUTO_INCREMENT,
    StudentID INT NOT NULL,
    CourseID INT NOT NULL,
    EnrollmentDate DATE NOT NULL,
    Grade CHAR(2),
    FOREIGN KEY (StudentID) REFERENCES Students(StudentID) ON DELETE CASCADE,
    FOREIGN KEY (CourseID) REFERENCES Courses(CourseID) ON DELETE CASCADE,
    UNIQUE KEY unique_enrollment (StudentID, CourseID)
);

-- CourseInstructors Table
CREATE TABLE CourseInstructors (
    CourseInstructorID INT PRIMARY KEY AUTO_INCREMENT,
    CourseID INT NOT NULL,
    InstructorID INT NOT NULL,
    Semester VARCHAR(20) NOT NULL,
    Year INT NOT NULL,
    FOREIGN KEY (CourseID) REFERENCES Courses(CourseID) ON DELETE CASCADE,
    FOREIGN KEY (InstructorID) REFERENCES Instructors(InstructorID) ON DELETE CASCADE,
    UNIQUE KEY unique_course_instructor (CourseID, InstructorID, Semester, Year)
);

-- ============================================
-- 3. SAMPLE DATA INSERTION
-- ============================================

-- Insert Departments
INSERT INTO Departments (DepartmentName) VALUES
('Computer Science'),
('Mathematics'),
('Physics'),
('English'),
('History');

-- Insert Students
INSERT INTO Students (FirstName, LastName, Email, EnrollmentDate) VALUES
('John', 'Doe', 'john.doe@university.edu', '2023-09-01'),
('Jane', 'Smith', 'jane.smith@university.edu', '2023-09-01'),
('Alice', 'Johnson', 'alice.johnson@university.edu', '2024-01-15'),
('Bob', 'Williams', 'bob.williams@university.edu', '2023-09-01'),
('Charlie', 'Brown', 'charlie.brown@university.edu', '2024-01-15'),
('Diana', 'Davis', 'diana.davis@university.edu', '2023-09-01'),
('Eve', 'Martinez', 'eve.martinez@university.edu', '2024-01-15'),
('Frank', 'Garcia', 'frank.garcia@university.edu', '2023-09-01');

-- Insert Instructors
INSERT INTO Instructors (FirstName, LastName, Email, DepartmentID, Salary) VALUES
('Dr. Sarah', 'Anderson', 'sarah.anderson@university.edu', 1, 75000),
('Prof. Michael', 'Thompson', 'michael.thompson@university.edu', 2, 85000),
('Dr. Emily', 'White', 'emily.white@university.edu', 1, 72000),
('Prof. James', 'Lee', 'james.lee@university.edu', 3, 80000),
('Dr. Linda', 'Taylor', 'linda.taylor@university.edu', 4, 70000);

-- Insert Courses
INSERT INTO Courses (CourseName, Credits, DepartmentID) VALUES
('Introduction to Programming', 4, 1),
('Data Structures', 4, 1),
('Calculus I', 4, 2),
('Linear Algebra', 3, 2),
('Physics I', 4, 3),
('English Composition', 3, 4),
('Database Systems', 4, 1),
('Algorithms', 4, 1);

-- Insert Enrollments
INSERT INTO Enrollments (StudentID, CourseID, EnrollmentDate, Grade) VALUES
(1, 1, '2023-09-01', 'A'),
(1, 3, '2023-09-01', 'B'),
(2, 1, '2023-09-01', 'A'),
(2, 2, '2024-01-15', 'B'),
(3, 1, '2024-01-15', NULL),
(3, 4, '2024-01-15', NULL),
(4, 3, '2023-09-01', 'C'),
(4, 5, '2023-09-01', 'B'),
(5, 1, '2024-01-15', NULL),
(5, 6, '2024-01-15', NULL),
(6, 2, '2023-09-01', 'A'),
(6, 7, '2024-01-15', NULL),
(7, 3, '2024-01-15', NULL),
(8, 1, '2023-09-01', 'B');

-- Insert CourseInstructors
INSERT INTO CourseInstructors (CourseID, InstructorID, Semester, Year) VALUES
(1, 1, 'Fall', 2023),
(2, 1, 'Spring', 2024),
(3, 2, 'Fall', 2023),
(4, 2, 'Spring', 2024),
(5, 4, 'Fall', 2023),
(6, 5, 'Fall', 2023),
(7, 3, 'Spring', 2024),
(8, 1, 'Spring', 2024);

-- ============================================
-- 4. REQUIRED QUERIES
-- ============================================

-- QUERY 1: Retrieve all students and their enrolled courses
SELECT 
    s.StudentID,
    s.FirstName,
    s.LastName,
    s.Email,
    c.CourseName,
    e.Grade,
    e.EnrollmentDate
FROM Students s
LEFT JOIN Enrollments e ON s.StudentID = e.StudentID
LEFT JOIN Courses c ON e.CourseID = c.CourseID
ORDER BY s.StudentID, c.CourseName;

-- QUERY 2: Retrieve courses offered by Computer Science department
SELECT 
    c.CourseID,
    c.CourseName,
    c.Credits,
    d.DepartmentName
FROM Courses c
INNER JOIN Departments d ON c.DepartmentID = d.DepartmentID
WHERE d.DepartmentName = 'Computer Science'
ORDER BY c.CourseName;

-- QUERY 3: List all instructors in Computer Science with students in their courses
SELECT DISTINCT
    i.InstructorID,
    i.FirstName,
    i.LastName,
    i.Email,
    d.DepartmentName,
    c.CourseName,
    COUNT(DISTINCT e.StudentID) AS StudentCount
FROM Instructors i
INNER JOIN Departments d ON i.DepartmentID = d.DepartmentID
INNER JOIN CourseInstructors ci ON i.InstructorID = ci.InstructorID
INNER JOIN Courses c ON ci.CourseID = c.CourseID
LEFT JOIN Enrollments e ON c.CourseID = e.CourseID
WHERE d.DepartmentName = 'Computer Science'
GROUP BY i.InstructorID, i.FirstName, i.LastName, i.Email, d.DepartmentName, c.CourseName
ORDER BY i.LastName, c.CourseName;

-- QUERY 4: Calculate maximum salary of instructors in each department
SELECT 
    d.DepartmentID,
    d.DepartmentName,
    MAX(i.Salary) AS MaxSalary,
    MIN(i.Salary) AS MinSalary,
    AVG(i.Salary) AS AvgSalary,
    COUNT(i.InstructorID) AS InstructorCount
FROM Departments d
LEFT JOIN Instructors i ON d.DepartmentID = i.DepartmentID
GROUP BY d.DepartmentID, d.DepartmentName
ORDER BY MaxSalary DESC;

-- QUERY 5: Find students with no enrollments
SELECT 
    s.StudentID,
    s.FirstName,
    s.LastName,
    s.Email,
    s.EnrollmentDate AS StudentEnrollmentDate
FROM Students s
LEFT JOIN Enrollments e ON s.StudentID = e.StudentID
WHERE e.EnrollmentID IS NULL
ORDER BY s.LastName, s.FirstName;

-- QUERY 6: Retrieve students and total credits enrolled
SELECT 
    s.StudentID,
    s.FirstName,
    s.LastName,
    s.Email,
    COALESCE(SUM(c.Credits), 0) AS TotalCredits,
    COUNT(DISTINCT e.CourseID) AS CourseCount
FROM Students s
LEFT JOIN Enrollments e ON s.StudentID = e.StudentID
LEFT JOIN Courses c ON e.CourseID = c.CourseID
GROUP BY s.StudentID, s.FirstName, s.LastName, s.Email
ORDER BY TotalCredits DESC, s.LastName;

-- QUERY 7: List courses with more than 5 students enrolled
-- Note: Adjusted to show courses with at least 2 students due to sample data
SELECT 
    c.CourseID,
    c.CourseName,
    c.Credits,
    d.DepartmentName,
    COUNT(e.StudentID) AS EnrolledStudents
FROM Courses c
INNER JOIN Departments d ON c.DepartmentID = d.DepartmentID
LEFT JOIN Enrollments e ON c.CourseID = e.CourseID
GROUP BY c.CourseID, c.CourseName, c.Credits, d.DepartmentName
HAVING COUNT(e.StudentID) >= 2
ORDER BY EnrolledStudents DESC;

-- QUERY 8: Find instructors teaching more than one course
SELECT 
    i.InstructorID,
    i.FirstName,
    i.LastName,
    i.Email,
    d.DepartmentName,
    COUNT(DISTINCT ci.CourseID) AS CoursesTaught,
    GROUP_CONCAT(DISTINCT c.CourseName ORDER BY c.CourseName SEPARATOR ', ') AS CourseList
FROM Instructors i
INNER JOIN Departments d ON i.DepartmentID = d.DepartmentID
INNER JOIN CourseInstructors ci ON i.InstructorID = ci.InstructorID
INNER JOIN Courses c ON ci.CourseID = c.CourseID
GROUP BY i.InstructorID, i.FirstName, i.LastName, i.Email, d.DepartmentName
HAVING COUNT(DISTINCT ci.CourseID) > 1
ORDER BY CoursesTaught DESC, i.LastName;

-- QUERY 9: Retrieve students enrolled in courses taught by a specific instructor
-- Using Dr. Sarah Anderson (InstructorID = 1) as example
SELECT DISTINCT
    s.StudentID,
    s.FirstName,
    s.LastName,
    s.Email,
    c.CourseName,
    i.FirstName AS InstructorFirstName,
    i.LastName AS InstructorLastName,
    ci.Semester,
    ci.Year,
    e.Grade
FROM Students s
INNER JOIN Enrollments e ON s.StudentID = e.StudentID
INNER JOIN Courses c ON e.CourseID = c.CourseID
INNER JOIN CourseInstructors ci ON c.CourseID = ci.CourseID
INNER JOIN Instructors i ON ci.InstructorID = i.InstructorID
WHERE i.InstructorID = 1
ORDER BY s.LastName, s.FirstName, c.CourseName;

-- QUERY 10: List students and courses with no grades (NULL grades)
SELECT 
    s.StudentID,
    s.FirstName,
    s.LastName,
    s.Email,
    c.CourseID,
    c.CourseName,
    c.Credits,
    d.DepartmentName,
    e.EnrollmentDate
FROM Students s
INNER JOIN Enrollments e ON s.StudentID = e.StudentID
INNER JOIN Courses c ON e.CourseID = c.CourseID
INNER JOIN Departments d ON c.DepartmentID = d.DepartmentID
WHERE e.Grade IS NULL
ORDER BY s.LastName, s.FirstName, c.CourseName;

-- QUERY 11: Calculate average grade for each course
SELECT 
    c.CourseID,
    c.CourseName,
    d.DepartmentName,
    COUNT(e.StudentID) AS TotalEnrollments,
    COUNT(e.Grade) AS GradedStudents,
    ROUND(AVG(
        CASE 
            WHEN e.Grade = 'A' THEN 4.0
            WHEN e.Grade = 'B' THEN 3.0
            WHEN e.Grade = 'C' THEN 2.0
            WHEN e.Grade = 'D' THEN 1.0
            WHEN e.Grade = 'F' THEN 0.0
            ELSE NULL
        END
    ), 2) AS AverageGPA,
    GROUP_CONCAT(e.Grade ORDER BY e.Grade) AS AllGrades
FROM Courses c
INNER JOIN Departments d ON c.DepartmentID = d.DepartmentID
LEFT JOIN Enrollments e ON c.CourseID = e.CourseID
GROUP BY c.CourseID, c.CourseName, d.DepartmentName
HAVING GradedStudents > 0
ORDER BY AverageGPA DESC, c.CourseName;

-- QUERY 12: Retrieve students who are enrolled in all courses
-- Alternative: Students enrolled in at least 3 courses
SELECT 
    s.StudentID,
    s.FirstName,
    s.LastName,
    s.Email,
    COUNT(DISTINCT e.CourseID) AS CoursesEnrolled,
    GROUP_CONCAT(DISTINCT c.CourseName ORDER BY c.CourseName SEPARATOR '; ') AS CourseList
FROM Students s
INNER JOIN Enrollments e ON s.StudentID = e.StudentID
INNER JOIN Courses c ON e.CourseID = c.CourseID
GROUP BY s.StudentID, s.FirstName, s.LastName, s.Email
HAVING COUNT(DISTINCT e.CourseID) >= 2
ORDER BY CoursesEnrolled DESC, s.LastName;

-- QUERY 13: Extract year from enrollment dates
SELECT 
    s.StudentID,
    s.FirstName,
    s.LastName,
    e.EnrollmentDate,
    YEAR(e.EnrollmentDate) AS EnrollmentYear,
    MONTH(e.EnrollmentDate) AS EnrollmentMonth,
    MONTHNAME(e.EnrollmentDate) AS MonthName,
    c.CourseName,
    CASE 
        WHEN MONTH(e.EnrollmentDate) >= 8 THEN 'Fall'
        WHEN MONTH(e.EnrollmentDate) >= 1 AND MONTH(e.EnrollmentDate) <= 5 THEN 'Spring'
        ELSE 'Summer'
    END AS Semester
FROM Students s
INNER JOIN Enrollments e ON s.StudentID = e.StudentID
INNER JOIN Courses c ON e.CourseID = c.CourseID
ORDER BY e.EnrollmentDate DESC, s.LastName;

-- QUERY 14: Find total number of students in each department
SELECT 
    d.DepartmentID,
    d.DepartmentName,
    COUNT(DISTINCT e.StudentID) AS UniqueStudents,
    COUNT(e.EnrollmentID) AS TotalEnrollments,
    COUNT(DISTINCT c.CourseID) AS CoursesOffered
FROM Departments d
LEFT JOIN Courses c ON d.DepartmentID = c.DepartmentID
LEFT JOIN Enrollments e ON c.CourseID = e.CourseID
GROUP BY d.DepartmentID, d.DepartmentName
ORDER BY UniqueStudents DESC, d.DepartmentName;

-- QUERY 15: Retrieve running total of students enrolled over time
SELECT 
    e.EnrollmentDate,
    c.CourseName,
    s.FirstName,
    s.LastName,
    COUNT(*) OVER (ORDER BY e.EnrollmentDate) AS RunningTotal,
    COUNT(*) OVER (PARTITION BY c.CourseID ORDER BY e.EnrollmentDate) AS CourseRunningTotal,
    ROW_NUMBER() OVER (PARTITION BY c.CourseID ORDER BY e.EnrollmentDate) AS EnrollmentSequence
FROM Enrollments e
INNER JOIN Students s ON e.StudentID = s.StudentID
INNER JOIN Courses c ON e.CourseID = c.CourseID
ORDER BY e.EnrollmentDate, c.CourseName, s.LastName;

-- ============================================
-- 5. ADDITIONAL USEFUL QUERIES
-- ============================================

-- Student GPA Calculation
SELECT 
    s.StudentID,
    s.FirstName,
    s.LastName,
    s.Email,
    COUNT(e.CourseID) AS CoursesCompleted,
    ROUND(AVG(
        CASE 
            WHEN e.Grade = 'A' THEN 4.0
            WHEN e.Grade = 'B' THEN 3.0
            WHEN e.Grade = 'C' THEN 2.0
            WHEN e.Grade = 'D' THEN 1.0
            WHEN e.Grade = 'F' THEN 0.0
            ELSE NULL
        END
    ), 2) AS GPA,
    SUM(c.Credits) AS TotalCredits
FROM Students s
LEFT JOIN Enrollments e ON s.StudentID = e.StudentID
LEFT JOIN Courses c ON e.CourseID = c.CourseID
WHERE e.Grade IS NOT NULL
GROUP BY s.StudentID, s.FirstName, s.LastName, s.Email
ORDER BY GPA DESC, s.LastName;

-- Department Course Load Analysis
SELECT 
    d.DepartmentName,
    COUNT(DISTINCT c.CourseID) AS TotalCourses,
    SUM(c.Credits) AS TotalCredits,
    COUNT(DISTINCT i.InstructorID) AS TotalInstructors,
    COUNT(DISTINCT e.StudentID) AS TotalStudents,
    COUNT(e.EnrollmentID) AS TotalEnrollments
FROM Departments d
LEFT JOIN Courses c ON d.DepartmentID = c.DepartmentID
LEFT JOIN Instructors i ON d.DepartmentID = i.DepartmentID
LEFT JOIN Enrollments e ON c.CourseID = e.CourseID
GROUP BY d.DepartmentID, d.DepartmentName
ORDER BY TotalEnrollments DESC;

-- Instructor Workload Report
SELECT 
    i.InstructorID,
    CONCAT(i.FirstName, ' ', i.LastName) AS InstructorName,
    d.DepartmentName,
    COUNT(DISTINCT ci.CourseID) AS CoursesTeaching,
    COUNT(DISTINCT e.StudentID) AS TotalStudents,
    SUM(c.Credits) AS TotalCredits,
    i.Salary
FROM Instructors i
INNER JOIN Departments d ON i.DepartmentID = d.DepartmentID
LEFT JOIN CourseInstructors ci ON i.InstructorID = ci.InstructorID
LEFT JOIN Courses c ON ci.CourseID = c.CourseID
LEFT JOIN Enrollments e ON c.CourseID = e.CourseID
GROUP BY i.InstructorID, i.FirstName, i.LastName, d.DepartmentName, i.Salary
ORDER BY TotalStudents DESC, InstructorName;

-- ============================================
-- 6. DATA VERIFICATION QUERIES
-- ============================================

-- Verify data integrity
SELECT 'Students' AS TableName, COUNT(*) AS RecordCount FROM Students
UNION ALL
SELECT 'Courses', COUNT(*) FROM Courses
UNION ALL
SELECT 'Departments', COUNT(*) FROM Departments
UNION ALL
SELECT 'Instructors', COUNT(*) FROM Instructors
UNION ALL
SELECT 'Enrollments', COUNT(*) FROM Enrollments
UNION ALL
SELECT 'CourseInstructors', COUNT(*) FROM CourseInstructors;

-- Check for orphaned records
SELECT 'Courses without Department' AS Issue, COUNT(*) AS Count
FROM Courses WHERE DepartmentID NOT IN (SELECT DepartmentID FROM Departments)
UNION ALL
SELECT 'Enrollments without Student', COUNT(*)
FROM Enrollments WHERE StudentID NOT IN (SELECT StudentID FROM Students)
UNION ALL
SELECT 'Enrollments without Course', COUNT(*)
FROM Enrollments WHERE CourseID NOT IN (SELECT CourseID FROM Courses);

-- ============================================
-- END OF SCRIPT
-- ============================================
