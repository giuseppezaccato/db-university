
# SELECT
## 1. Selezionare tutti gli studenti nati nel 1990 (160)
```sql
SELECT *
FROM `students`
WHERE YEAR(`date_of_birth`) = 1990;

SELECT * FROM `students` WHERE YEAR(`date_of_birth`) = 1990 LIMIT 0, 1000	160 row(s) returned	0.000 sec / 0.000 sec
```


## 2. Selezionare tutti i corsi che valgono più di 10 crediti (479)
```sql
SELECT *
FROM `courses`
WHERE `cfu` > 10;

SELECT * FROM `courses` WHERE `cfu` > 10 LIMIT 0, 1000	479 row(s) returned	0.000 sec / 0.000 sec
```

## 3. Selezionare tutti gli studenti che hanno più di 30 anni
```sql
SELECT `id`, `name`, `surname`, `date_of_birth`
FROM `students`
WHERE YEAR(`date_of_birth`) <= 1995; --WHERE timestampdiff(YEAR, `date_of_birth`, CURDATE()) > 30;

SELECT `id`, `name`, `surname`, `date_of_birth` FROM `students` WHERE YEAR(`date_of_birth`) <= 1995 LIMIT 0, 1000	1000 row(s) returned	0.000 sec / 0.000 sec
```

## 4. Selezionare tutti i corsi del primo semestre del primo anno di un qualsiasi corso di laurea (286)
```sql
SELECT *
FROM `courses`
WHERE `period` = "I semestre" AND `year` = 1;

SELECT * FROM `courses` WHERE `period` = "I semestre" AND `year` = 1 LIMIT 0, 1000	286 row(s) returned	0.000 sec / 0.000 sec
```

## 5. Selezionare tutti gli appelli d'esame che avvengono nel pomeriggio (dopo le 14) del 20/06/2020 (21)
```sql
SELECT *
FROM `exams`
WHERE date(`date`) = '2020-06-20' AND HOUR(`hour`) > 14;

SELECT * FROM `courses` WHERE `period` = 'I semestre' AND `year` = 1 LIMIT 0, 1000	286 row(s) returned	0.000 sec / 0.000 sec
```

## 6. Selezionare tutti i corsi di laurea magistrale (38)
```sql
SELECT *
FROM `degrees`
WHERE `level` = 'magistrale'; --  WHERE `name` LIKE '%Mag%';

SELECT * FROM `degrees` WHERE `level` = 'magistrale' LIMIT 0, 1000	38 row(s) returned	0.000 sec / 0.000 sec
```

## 7. Da quanti dipartimenti è composta l'università? (12)
```sql
SELECT COUNT(*) AS 'dipartimenti'
FROM `departments`;

SELECT COUNT(*) AS 'dipartimenti' FROM `DEPARTMENTS` LIMIT 0, 1000	1 row(s) returned	0.000 sec / 0.000 sec
```

## 8. Quanti sono gli insegnanti che non hanno un numero di telefono? (50)
```sql
SELECT COUNT(*) AS 'Prof Irraggiungibili'
FROM `teachers`
WHERE `phone` IS "null" ;

SELECT COUNT(*) AS 'Prof Irraggiungibili' FROM `teachers` WHERE `phone` is null LIMIT 0, 1000	1 row(s) returned	0.000 sec / 0.000 sec
```


# GROUP BY
## 1.Contare quanti iscritti ci sono stati ogni anno
```sql
SELECT  COUNT(*) AS `studenti per anno` , YEAR(`enrolment_date`) AS  `anno`
FROM students
GROUP BY `anno`;

SELECT  COUNT(*) AS `studenti per anno` , YEAR(`enrolment_date`) AS  `anno` FROM students GROUP BY `anno` LIMIT 0, 1000	4 row(s) returned	0.016 sec / 0.000 sec
```

## 2. Contare gli insegnanti che hanno l'ufficio nello stesso edificio
```sql
SELECT COUNT(*) AS `numero prof`, `office_address`
FROM teachers
GROUP BY `office_address`;

SELECT COUNT(*) AS `numero prof`, `office_address` FROM teachers GROUP BY `office_address` LIMIT 0, 1000	29 row(s) returned	0.000 sec / 0.000 sec
```

## 3. Calcolare la media dei voti di ogni appello d'esame
```sql
SELECT AVG(`vote`), `exam_id`
FROM `exam_student`
GROUP BY `exam_id`;

SELECT AVG(`vote`), `exam_id` FROM `exam_student` GROUP BY `exam_id` LIMIT 0, 1000	1000 row(s) returned	0.047 sec / 0.000 sec
```

## 4. Contare quanti corsi di laurea ci sono per ogni dipartimento
```sql
SELECT COUNT(*) AS "corsi di laurea" , department_id
FROM degrees
GROUP BY department_id

SELECT COUNT(*) AS "corsi di laurea" , department_id FROM degrees GROUP BY department_id LIMIT 0, 1000	12 row(s) returned	0.000 sec / 0.000 sec
```


# JOIN

## 1. Selezionare tutti gli studenti iscritti al Corso di Laurea in Economia
```sql
SELECT * 
FROM students
JOIN degrees
ON students.degree_id = degrees.id
WHERE website like '%economia%'; --WHERE degrees.website like '%economia%'

SELECT *  FROM students JOIN degrees ON students.degree_id = degrees.id WHERE website like '%economia%' LIMIT 0, 1000	262 row(s) returned	0.000 sec / 0.000 sec
```

## 2. Selezionare tutti i Corsi di Laurea Magistrale del Dipartimento di Neuroscienze
```sql
SELECT * 
FROM degrees
JOIN departments
ON degrees.department_id = departments.id
WHERE departments.name like "%neuroscienze%" AND level = "magistrale";

SELECT *  FROM degrees JOIN departments ON degrees.department_id = departments.id WHERE departments.name like "%neuroscienze%" AND level = "magistrale" LIMIT 0, 1000	1 row(s) returned	0.016 sec / 0.000 sec
```

## 3. Selezionare tutti i corsi in cui insegna Fulvio Amato (id=44)
```sql
SELECT * 
FROM teachers
JOIN course_teacher 
ON teachers.id = course_teacher.teacher_id
JOIN courses 
ON course_teacher.teacher_id = courses.id
WHERE teachers.name = "fulvio" AND teachers.surname = "amato";

SELECT *  FROM teachers JOIN course_teacher  ON teachers.id = course_teacher.teacher_id JOIN courses  ON course_teacher.teacher_id = courses.id WHERE teachers.name = "fulvio" AND teachers.surname = "amato" LIMIT 0, 1000	11 row(s) returned	0.000 sec / 0.000 sec
```

## 4. Selezionare tutti gli studenti con i dati relativi al corso di laurea a cui sono iscritti e il relativo dipartimento, in ordine alfabetico per cognome e nome
```sql
SELECT *
FROM students 
JOIN degrees ON students.degree_id = degrees.id
JOIN departments ON degrees.department_id = departments.id
ORDER BY surname, students.name ASC;

SELECT * FROM students  JOIN degrees ON students.degree_id = degrees.id JOIN departments ON degrees.department_id = departments.id ORDER BY surname, students.name ASC LIMIT 0, 1000	1000 row(s) returned	0.063 sec / 0.031 sec
```

## 5. Selezionare tutti i corsi di laurea con i relativi corsi e insegnanti
```sql
SELECT * --SELECT department_id,degrees.id,degrees.name, courses.name, teachers.name,surname
FROM degrees
JOIN courses ON degrees.id = courses.degree_id 
JOIN course_teacher ON courses.id = course_teacher.course_id
JOIN teachers ON course_teacher.teacher_id = teachers.id;
--ORDER BY degrees.name;

SELECT * FROM degrees JOIN courses ON degrees.id = courses.degree_id  JOIN course_teacher ON courses.id = course_teacher.course_id JOIN teachers ON course_teacher.teacher_id = teachers.id LIMIT 0, 1000	1000 row(s) returned	0.000 sec / 0.015 sec
```

## 6. Selezionare tutti i docenti che insegnano nel Dipartimento di Matematica (54)
```sql
SELECT teachers.id as 'identificativo', teachers.name as 'nome' , surname as 'cognome', departments.name as 'dipartimento'
FROM teachers
JOIN course_teacher ON teachers.id = course_teacher.teacher_id
JOIN courses ON course_teacher.course_id = courses.id
JOIN degrees ON courses.degree_id = degrees.id
JOIN departments ON degrees.department_id = departments.id
WHERE departments.name like "%matematica%" --case insensitive
GROUP BY `identificativo`,`nome`, `cognome`, `dipartimento`; --per togliere le ripetizioni

SELECT teachers.id as 'identificativo', teachers.name as 'nome' , surname as 'cognome', departments.name as 'dipartimento' FROM teachers JOIN course_teacher ON teachers.id = course_teacher.teacher_id JOIN courses ON course_teacher.course_id = courses.id JOIN degrees ON courses.degree_id = degrees.id JOIN departments ON degrees.department_id = departments.id WHERE departments.name like "%matematica%" GROUP BY `identificativo`,`nome`, `cognome`, `dipartimento` LIMIT 0, 1000	54 row(s) returned	0.000 sec / 0.000 sec
```

## 7. BONUS: Selezionare per ogni studente il numero di tentativi sostenuti per ogni esame, stampando anche il voto massimo. Successivamente, filtrare i tentativi con voto minimo 18
```sql
SELECT COUNT(*) as `attempts`, students.registration_number as "matricola", students.name, surname, max(vote)
FROM students
JOIN exam_student ON exam_student.student_id = students.id
JOIN exams ON exam_student.exam_id = exams.id
GROUP BY students.name, surname, students.id;


SELECT COUNT(*) as `attempts`, students.registration_number as "matricola", students.name, surname, max(vote) FROM students JOIN exam_student ON exam_student.student_id = students.id JOIN exams ON exam_student.exam_id = exams.id GROUP BY students.name, surname, students.id LIMIT 0, 1000	1000 row(s) returned	0.250 sec / 0.000 sec
```

