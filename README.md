/* =========================================================
   Assignment Queries: Q1 to Q7 (Oracle SQL, ANSI JOINs)
   Assumptions:
   - Tables exist: departments, courses, students,
                   faculty (self-ref supervisor_id),
                   customers, flights, tickets
   - flights.depart_ts and tickets.booking_ts are TIMESTAMP
   - All PK/FK constraints created per prior schema
   ========================================================= */

------------------------------------------------------------
-- Q1) Roll_no, student_name, course_name, department_name
------------------------------------------------------------
SELECT
  s.roll_no,
  s.student_name,
  c.course_name,
  d.dept_name AS department_name
FROM students   s
JOIN courses    c ON c.course_id = s.course_id
JOIN departments d ON d.dept_id   = c.dept_id;

------------------------------------------------------------
-- Q2) Faculty employee name & salary + supervisor name & salary
------------------------------------------------------------
SELECT
  e.emp_name AS employee_name,
  e.salary   AS employee_salary,
  m.emp_name AS supervisor_name,
  m.salary   AS supervisor_salary
FROM faculty e
LEFT JOIN faculty m
  ON m.emp_id = e.supervisor_id;

------------------------------------------------------------
-- Q3) Faculty who joined before their supervisor
--     (show employee and manager name)
------------------------------------------------------------
SELECT
  e.emp_name AS employee_name,
  m.emp_name AS manager_name
FROM faculty e
JOIN faculty m
  ON m.emp_id = e.supervisor_id
WHERE e.hire_date < m.hire_date;

------------------------------------------------------------
-- Q4A) All passengers + their ticket booking + flight details
------------------------------------------------------------
SELECT
  c.cust_id,
  c.cust_name,
  t.ticket_id,
  t.booking_ts,
  f.flight_id,
  f.src,
  f.dst,
  f.depart_ts
FROM customers c
JOIN tickets   t ON t.cust_id  = c.cust_id
JOIN flights   f ON f.flight_id = t.flight_id;

------------------------------------------------------------
-- Q4B) Idle customers (never bought any ticket)
------------------------------------------------------------
SELECT c.*
FROM customers c
LEFT JOIN tickets t
  ON t.cust_id = c.cust_id
WHERE t.ticket_id IS NULL;

------------------------------------------------------------
-- Q4C) Flights completely unsold till now
--      (no tickets exist for the flight)
------------------------------------------------------------
SELECT f.*
FROM flights f
LEFT JOIN tickets t
  ON t.flight_id = f.flight_id
WHERE t.ticket_id IS NULL;

------------------------------------------------------------
-- Q5) Personal details of each supervisor faculty (id, name, salary)
--     along with the personal info of their corresponding supervisor
--     Include only supervisors (have at least one subordinate)
------------------------------------------------------------
SELECT DISTINCT
  s.emp_id   AS supervisor_emp_id,
  s.emp_name AS supervisor_name,
  s.salary   AS supervisor_salary,
  m.emp_id   AS supervisors_supervisor_id,
  m.emp_name AS supervisors_supervisor_name,
  m.salary   AS supervisors_supervisor_salary
FROM faculty s
JOIN faculty sub
  ON sub.supervisor_id = s.emp_id
LEFT JOIN faculty m
  ON m.emp_id = s.supervisor_id;

------------------------------------------------------------
-- Q6) Customer, departure date and return date if both trips
--     (outbound and return) are within 24 hours for a city pair
------------------------------------------------------------
SELECT DISTINCT
  c.cust_id,
  c.cust_name,
  f1.src AS out_src,
  f1.dst AS out_dst,
  f1.depart_ts AS depart_time,
  f2.depart_ts AS return_time
FROM customers c
JOIN tickets  t1 ON t1.cust_id = c.cust_id
JOIN flights  f1 ON f1.flight_id = t1.flight_id
JOIN tickets  t2 ON t2.cust_id = c.cust_id AND t2.ticket_id <> t1.ticket_id
JOIN flights  f2 ON f2.flight_id = t2.flight_id
WHERE f2.src = f1.dst
  AND f2.dst = f1.src
  AND f2.depart_ts BETWEEN f1.depart_ts
                       AND f1.depart_ts + NUMTODSINTERVAL(24,'HOUR');

------------------------------------------------------------
-- Q7) Flights departing between midnight and noon in a month
--     Example: August (month = 8). Change 8 to another month if needed.
------------------------------------------------------------
SELECT f.*
FROM flights f
WHERE EXTRACT(MONTH FROM f.depart_ts) = 8
  AND f.depart_ts >= TRUNC(f.depart_ts)
  AND f.depart_ts <  TRUNC(f.depart_ts) + NUMTODSINTERVAL(12,'HOUR');
