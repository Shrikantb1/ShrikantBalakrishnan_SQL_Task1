# ShrikantBalakrishnan
Design and develop a Library Management System using SQL. The project should involve three tables: Books, Members, BorrowingRecords. The system will manage book inventories, member details, and borrowing transactions (Using MySQL) 

-- Library Management System (using SQL)
 
DROP DATABASE Capestone;						-- Drop Database (Optional)
CREATE DATABASE Capestone;						-- Create Database named Capestone
USE Capestone;									-- Use Capestone Database

-- create Table Books
CREATE TABLE Books (BOOK_ID int primary key, 
TITLE Varchar(25), 
AUTHOR Varchar(25), 
GENRE Varchar(25),
YEAR_PUBLISHED Varchar(4), AVAILABLE_COPIES int
);

-- create table Members
CREATE TABLE Members (MEMBER_ID int primary key, NAME Varchar(25),
EMAIL Varchar(25), 
PHONE_NO Varchar(25),
ADDRESS Varchar(25), MEMBERSHIP_DATE date
);

-- create table BorrowingRecords
CREATE TABLE BorrowingRecords (BORROW_ID int primary key, 
MEMBER_ID int, BOOK_ID int, BORROW_DATE date, RETURN_DATE date,
   FOREIGN KEY (BOOK_ID) REFERENCES Books(BOOK_ID),          -- Assigning foreign keys BOOK_ID
   FOREIGN KEY (MEMBER_ID) REFERENCES Members(MEMBER_ID)     -- Assigning foreign keys to Member MEMBER_ID
);

-- Insert sample data of 10 rows into table Books
INSERT INTO Books(BOOK_ID,TITLE,AUTHOR,GENRE,
YEAR_PUBLISHED,AVAILABLE_COPIES)
VALUES 
    (1,'Game of Thrones','George Martin','Fantasy ','1996',50),
    (2,'Twilight','Stephenie Meyer','Romance ','2005',10),
    (3,'A tale of two cities','Charles Dickens','Historical Fiction ','1859',20),
    (4,'Harry Potter','J.K.Rowling','Young Adult ','1997',100),
    (5,'The lord of the Rings','J.R.R.Tolkien','Fantasy ','1954',25),
    (6,'And then there were none','Agatha Christie','Thriller ','1939',10),
    (7,'The Da Vinci Code','Dan Brown','Adventure','2003',33),
    (8,'Alice in Wonderland','Lewis Caroll','Childrens Classic','1865',18),
    (9,'Gone with the wind','Margaret Mitchell','Drama','1936',22),
    (10,'The hunger games','Suzanne Collins','Young Adult','2008',15);
    
select * from Books;     -- Display Table Books  

-- Insert sample data of 10 rows into table Members
INSERT INTO Members (MEMBER_ID,NAME,EMAIL,PHONE_NO,
ADDRESS,MEMBERSHIP_DATE)
VALUES 
    (1,'Liana','Liana@gmail.com','2344580 ','Manhattan','2021-07-15'),
    (2,'Damon','Damon@hotmail.com','6321567 ','Ohio','2024-12-10'),
    (3,'Aria','Aria@rediffmail.com','0937245','Austin','2022-03-12'),
    (4,'Felix','Felix@gmail.com','3829532','Chicago','2023-06-08'),
    (5,'Sienna','Sienna@yahoo.com','8373528','Nashville','2024-05-03'),
    (6,'Abdul','Abdul@yahoo.com','9532235','Seattle','2021-11-19'),
    (7,'Rahul','Rahul@gmail.com','9825376','Orlando','2023-07-22'),
    (8,'Talia','Talia@yahoo.com','5689321','Jersey city','2024-01-27'),
    (9,'Kate','Kate@hotmail.com','4253853','Oklahoma','2022-11-20'),
    (10,'Cyrus','Cyrus@hotmail.com','7625421','Pheonix','2023-12-10');
    
select * from Members;      -- Display Table Members

-- Insert sample data of 20 rows into table BorrowingRecords
INSERT INTO BorrowingRecords(BORROW_ID, MEMBER_ID, BOOK_ID, 
BORROW_DATE, RETURN_DATE)
VALUES 
    (1,6,2,'2025-11-15','2026-01-02'),
    (2,6,5,'2025-02-01','2025-05-04'),
    (3,9,8,'2025-04-03','2025-05-06'),
    (4,9,5,'2025-06-05','2025-07-08'),
    (5,8,2,'2025-08-07','2025-10-10'),
    (6,3,7,'2025-09-09','2025-11-11'),
    (7,1,6,'2025-07-12','2025-08-13'),
    (8,5,2,'2025-03-14','2025-04-15'),
    (9,7,1,'2025-05-16','2025-12-17'),
    (10,4,9,'2025-01-18','2025-04-19'),
    (11,4,3,'2025-01-20','2025-07-20'),
    (12,3,8,'2025-06-21','2025-10-22'),
    (13,8,5,'2025-09-23','2025-11-24'),
    (14,2,1,'2025-08-25','2025-09-26'),
    (15,7,7,'2025-04-27','2025-05-28'),
    (16,1,4,'2025-02-28','2025-10-30'),
    (17,9,6,'2025-03-31','2025-07-29'),
    (18,2,9,'2025-05-30','2025-07-27'),
    (19,5,5,'2025-07-28','2025-12-01'),
    (20,9,2,'2025-07-26','2025-11-03');
        
Select * from BorrowingRecords; -- Display table BorrowingRecords

-- Information Retrieval:
-- a) Retrieve a list of books currently borrowed by a specific member
SELECT b.TITLE,m.NAME, br.RETURN_DATE
FROM 
Books as b
JOIN 
BorrowingRecords as br ON b.BOOK_ID = br.BOOK_ID  
JOIN
Members as m  ON br.MEMBER_ID = m.MEMBER_ID  
WHERE RETURN_DATE > CAST(CURRENT_DATE() AS Date) ;

-- --------------------------------------------------------------------------------------
-- b) Find members who have overdue books (borrowed more than 30 days ago, not returned).
SELECT
    m.NAME AS member_name, -- Adjust column names as per your schema
    br.BORROW_DATE,
    DATEDIFF(CURDATE(), br.BORROW_DATE) AS Days_Overdue
FROM
    Members m
JOIN
    BorrowingRecords br ON m.MEMBER_ID = br.MEMBER_ID
WHERE DATEDIFF(CURDATE(), br.BORROW_DATE) > 30 
AND
RETURN_DATE > CURDATE();
-- -----------------------------------------------------------------------
-- c) Retrieve books by genre along with the count of available copies.
SELECT
    Genre,
    SUM(AVAILABLE_COPIES) AS AvailableCopies
FROM
    Books
GROUP BY
    Genre;
-- ------------------------------------------------------
-- d) Find the most borrowed book(s) overall.

SELECT b.TITLE   -- Get the sum of borrowings
FROM books AS b
JOIN 
BorrowingRecords as br ON b.BOOK_ID = br.BOOK_ID    -- by book_genre
GROUP BY TITLE
ORDER BY Count(br.BORROW_DATE) DESC -- sort by total desc, so highest first  -- take first result
LIMIT 1;							-- Limit the rows to shawcase only the top row
-- ---------------------------------------------------------
-- e) Retrieve members who have borrowed books from at least three different genres.

SELECT m.NAME 
FROM 
Books as b
JOIN 
BorrowingRecords as br ON b.BOOK_ID = br.BOOK_ID  
JOIN
Members as m ON br.MEMBER_ID = m.MEMBER_ID 
GROUP BY M.name
having count(DISTINCT b.GENRE) > 3;
-- ---------------------------------------------------------------------
-- Reporting and Analytics
-- a) Calculate the total number of books borrowed per month.
SELECT
MONTHNAME(BORROW_DATE) AS MonthName,
    COUNT(BORROW_DATE) AS TotalBorrowed
FROM
    BorrowingRecords
GROUP BY
    MONTHNAME(BORROW_DATE),
    MONTH(BORROW_DATE)
ORDER BY
    MONTH(BORROW_DATE),
    MONTH(BORROW_DATE);
-- -------------------------------------------------------------
-- b) Find the top three most active members based on the number of books borrowed.
SELECT 
m.NAME, Count(br.BORROW_DATE) AS TotalBorrowed   -- Get the sum of borrowings
FROM books AS b
JOIN 
BorrowingRecords as br ON b.BOOK_ID = br.BOOK_ID
JOIN
Members as m ON br.MEMBER_ID = m.MEMBER_ID
GROUP BY NAME
ORDER BY Count(br.BORROW_DATE) DESC 
LIMIT 3 ;
-- ---------------------------------------------------------------
-- c) Retrieve authors whose books have been borrowed at least 10 times.
SELECT  
	b.AUTHOR, Count(br.BORROW_ID) AS TotalBorrowed   -- Get the sum of borrowings
FROM books AS b
JOIN 
BorrowingRecords as br ON b.BOOK_ID = br.BOOK_ID 
GROUP BY AUTHOR
HAVING count(br.BORROW_ID) > 10;
-- -------------------------------------------------------------------
-- d) Identify members who have never borrowed a book.
SELECT 
	m.NAME, COUNT(br.BORROW_ID) AS TotalBorrowed
FROM Members AS m
LEFT JOIN BorrowingRecords AS br ON m.MEMBER_ID = br.MEMBER_ID -- INNER JOIN is fine here
GROUP BY m.MEMBER_ID, m.NAME
HAVING COUNT(br.BORROW_ID) < 1; 
