# SQL--Analyzing-Student-Performance

## 📖 Introduction
In today's fast-paced and competitive educational environment, understanding the factors that influence student success is more important than ever. Just like a city’s transport system must adapt to serve its residents, schools and educators must evolve to meet the needs of students.

This project explores a dataset containing various aspects of student life—such as study hours, sleep patterns, attendance, and more—to uncover the key drivers of academic performance. By analyzing these factors, we aim to provide insights that can help students, teachers, and policymakers make informed decisions.

---
## 📊 Dataset Overview
The dataset used in this project is stored in a table named `student_performance` and includes the following columns:

| Column                   | Definition                                         | Data Type |
|--------------------------|----------------------------------------------------|-----------|
| `attendance`             | Percentage of classes attended                    | `FLOAT`   |
| `extracurricular_activities` | Participation in extracurricular activities (`Yes`/`No`) | `VARCHAR` |
| `sleep_hours`            | Average number of hours of sleep per night       | `FLOAT`   |
| `tutoring_sessions`      | Number of tutoring sessions attended per month    | `INTEGER` |
| `teacher_quality`        | Quality of the teachers (`Low`, `Medium`, `High`) | `VARCHAR` |
| `exam_score`             | Final exam score                                  | `FLOAT`   |

---
## 🔎 Key Questions & SQL Queries

### **1️⃣ Do More Study Hours and Extracurricular Activities Lead to Better Scores?**

#### 📌 Objective
Analyze how studying more than 10 hours per week while also participating in extracurricular activities impacts exam performance. The goal is to identify whether students balancing both commitments tend to achieve higher exam scores.

#### 📝 Query: `avg_exam_score_by_study_and_extracurricular`
```sql
SELECT hours_studied,
       AVG(exam_score) AS avg_exam_score
FROM student_performance
WHERE hours_studied > 10 AND extracurricular_activities = 'Yes'
GROUP BY hours_studied
ORDER BY hours_studied DESC;
```
#### 📊 Results and Observations
| hours_studied | avg_exam_score |
|--------------|----------------|
| 43           | 78             |
| 39           | 75             |
| 38           | 73.5           |
| 37           | 73             |
| 36           | 70.43          |
| 35           | 72.31          |
| please refer to the files in the repository to see the full results           |          |
- The highest average exam scores are seen among students studying the most hours while engaging in extracurricular activities.
- However, there is a diminishing return—students studying excessively beyond a point do not see a significant increase in scores.

---
### **2️⃣ Is There a "Sweet Spot" for Study Hours?**

#### 📌 Objective
Determine how different ranges of study hours impact exam performance. Students are categorized into four groups:
- **1-5 hours**
- **6-10 hours**
- **11-15 hours**
- **16+ hours**

The goal is to identify if there is an optimal range of study hours that leads to the best academic performance.

#### 📝 Query: `avg_exam_score_by_hours_studied_range`
```sql
SELECT CASE 
           WHEN hours_studied BETWEEN 1 AND 5 THEN '1-5 hours'
           WHEN hours_studied BETWEEN 6 AND 10 THEN '6-10 hours'
           WHEN hours_studied BETWEEN 11 AND 15 THEN '11-15 hours'
           ELSE '16+ hours'
       END AS hours_studied_range,
       AVG(exam_score) AS avg_exam_score
FROM student_performance
GROUP BY hours_studied_range
ORDER BY avg_exam_score DESC;
```
#### 📊 Results and Observations
| hours_studied_range | avg_exam_score |
|--------------------|----------------|
| 16+ hours         | 67.92          |
| 11-15 hours      | 65.20          |
| 6-10 hours       | 64.23          |
| 1-5 hours        | 62.63          |
- Students studying **16+ hours per week** achieved the highest average scores.
- The **11-15 hours per week** range also yielded strong results, suggesting an optimal balance.
- Studying fewer than 10 hours per week correlates with significantly lower exam performance.

---
### **3️⃣ Ranking Students Based on Exam Scores**

#### 📌 Objective
A teacher wants to show students their relative rank in the class without revealing actual scores. This requires using a **window function** to assign ranks based on `exam_score`, ensuring students with the same score share the same rank (using **DENSE_RANK()**).

#### 📝 Query: `student_exam_ranking`
```sql
SELECT attendance, 
       hours_studied,
       sleep_hours,
       tutoring_sessions,
       DENSE_RANK() OVER (ORDER BY exam_score DESC) AS exam_rank
FROM student_performance
ORDER BY exam_rank
LIMIT 30;
```
#### 📊 Results and Observations
| attendance | hours_studied | sleep_hours | tutoring_sessions | exam_rank |
|------------|--------------|-------------|-------------------|-----------|
| 98         | 27           | 6           | 5                 | 1         |
| 89         | 18           | 4           | 3                 | 2         |
| 90         | 14           | 8           | 4                 | 3         |
| 83         | 23           | 4           | 1                 | 3         |
| 96         | 28           | 4           | 1                 | 4         |
| 90         | 28           | 9           | 0                 | 4         |
| please refer to the files in the repository to see the full results         |            |          |                  |          |
- **Sleep matters, but beyond a certain point, there's a diminishin return.**
- The top-ranked students exhibit a balance of **attendance, study hours, and tutoring participation**.

---

## Visualizations
*Please note, all visualizations were done using Python's Pandas, Matplotlib and Seaborn.*

### Average Exam Score vs. Hours Studied by Extracurricular Activities
![image](https://github.com/user-attachments/assets/b40d10e5-580b-487e-a72a-ecaae71e882c)
The data that fuels this graphic can be obtained by running the following query:
```sql
SELECT hours_studied,
       AVG(exam_score) AS avg_exam_score,
	     extracurricular_activities
FROM student_performance
GROUP BY hours_studied, extracurricular_activities;
```
From the visualization we can observe that:
- In general, students which have extracurricular activities have slightly higher exam scores. This might be because those students which have extracurricular activities tend to be somewhat more applied. Or maybe it's just a coincidence.
- In general, the more the student studies, the better scores he gets.
- It seems that studying excesively might lead to worse scores.
- For the extracurricular activities group, it seems there was some data that shows that by studying 1 hour per week, the exam score is quite high. This might be due to data collection mistakes or other factors.

### Average Exam Score vs. Hours Studied Range
![image](https://github.com/user-attachments/assets/f8b5dc45-5665-4eb9-b86c-6cd4514d782d)
The data for this visualization comes from the second query result (`avg_exam_score_by_hours_studied_range`).
As mentioned before, studying more than 16 hours per week seems to lead to the highest exam scores.

### Exam Rank vs. Hours Studied taking Hours of Sleep and Exam Score into account
![image](https://github.com/user-attachments/assets/a3a5c522-2453-4f62-a918-144ae4f6e476)
The data for this visualization comes from the third query result(`student_exam_ranking`).
- In general, we see that there's a sweet spot regarding hours of sleep, and beyond that point there's diminishing return. Besides we can see that higher exam scores are found for students which study more hours, and those same students, logically, have better ranks.

---

## 🔥 Key Takeaways

### **📌 Insights from the Data**
- **Study hours matter**, but there is a diminishing return beyond a certain point.
- **Balancing extracurricular activities with studying** appears beneficial but must be managed well.
- **A sweet spot of study hours (11-15 per week) seems optimal** for most students.
- **Good attendance and tutoring participation** also contribute to higher rankings.

### **🛠 SQL Concepts Used**
- **Aggregation Functions**: Used `AVG()` to analyze trends in student scores.
- **CASE Statements**: Categorized study hours into meaningful ranges.
- **Window Functions**: Implemented `DENSE_RANK()` to fairly rank students.
- **Filtering with WHERE**: Focused on students meeting specific conditions.
- **Sorting & Grouping**: Used `ORDER BY` and `GROUP BY` to structure data effectively.

---
## 🎯 Conclusion
This project demonstrates the power of SQL in analyzing educational performance trends. By leveraging various SQL techniques, we were able to extract meaningful insights from student data. The findings suggest that a **balanced approach to studying, extracurricular activities, and rest is key to success**.

Would you like to contribute or explore further improvements? Feel free to fork this project and experiment with additional factors like sleep hours and teacher quality! 🚀
