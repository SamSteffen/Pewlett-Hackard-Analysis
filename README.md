# Pewlett-Hackard-Analysis
## Overview
The following is an analysis of an organization's employee data that uses an Entity Relationship Diagram (ERD) and SQL to determine the number of employees by department who are eligible for retirement, in order to make recommendations for the course of the company's future.

The employee data for the client, Pewlett Hackard, was initially organized into six csv spreadsheets. To visually represent the relationships present in the data, the following ERD was developed:

[ERD picture here]

An initial exploratory analysis of the company's data, which included employee records for 300,024 former and current employees, pictured above, revealed that Pewlett Hackard is an organization comprised of the following:
- Seven employee classifications, or titles (Manager, Technique Leader, Senior Engineer, Senior Staff, Assistant Engineer, Engineer, Staff) dispersed throughout 9 departments, with an average of 4,069 employees per department. 
- The average salary, company-wide, is $52,971, with the potential to earn anywhere between $40,000 to $129,492. The median salary is $48,681.
- 240,124 individuals are currently employed by the company (or were, at the time the data was retrieved).

Our initial pursuit was to discover (1) the number of individuals who would be retiring in the near future and (2) who is eligible for a retirement package. Retirement eligibility was gaged using employee birthdates: any person born between Jan. 1, 1952 and Dec. 31, 1955 is considered retirement-eligible. 
The following shows the result of our query of retirement-ready employees, amended slightly to ensure that the return only reflects employees who have not yet retired:
- 16,981 active PH employees were born in 1952.
- 18,328 active PH employees were born in 1953.
- 18,612 active PH employees were born in 1954.
- 18,537 active PH employees were born in 1955.
- This means 72,458 employees at PH either have recently reached or will be reaching retirement eligiblility over the course of the next few years, roughly 30% of the total PH workforce.

Following this discovery, it was determined that the number of retiring employees could be found by title to gain a better sense of what roles would need to be filled and to see whether certain employees would be eligible for a mentorship program. The subsequent analysis shows the findings of this pursuit.

## Results
To determine the number of retiring employees by title, we first created a Retirement Titles table to hold all the titles of PH employees who were born between January 1, 1952 and December 31, 1955. Because some employees were listed as having multiple titles in the database—for example, due to promotions—the DISTINCT ON statement was utilized to create a table containing the most recent title of each employee. Using the COUNT() function, we created a table showing the number of retirement-age employees by most recent job title. Because we wanted to include only current employees in our analysis, we excluded employees who have already left the company from this analysis. The results of this effort are summarized in the following table:

[retiring_titles table here]

From the above graphic, the following is evident:
- 72,458 total individuals currently employed by PH are eligible for retirement.
- Senior Engineers represent the greatest percentage (36%) of the retirement-eligible population (25,916).
- Managers represent the slightest percentage of the retirement-eligible population (2).
- Assuming that Senior Engineer, Senior Staff, Technique Leader and Manager are all leadership roles, 75% of the retirement-eligible population is classified as leadership (54,447).

Next, to determine who among the retirement-eligible population was eligible for mentorship, we created a mentorship-eligibility table to hold the current employees who were born between January 1, 1965 and December 31, 1965. This dataset yielded the following:
- 1,549 individuals are eligible for a mentorship program, according to the criteria described above.
- Senior staff represents greatest percentage (36%) of mentorship-eligible population by title.
- No managers are eligible for mentorship.
- Assuming Senior Engineer, Senior Staff, Technique Leader and Manger are all leadership roles, 76% of the mentorship-eligible population is classified as leadership (1,175)

## Summary 
The above analysis yields the following takeaways:
- As the "silver tsunami" begins to make an impact, 72,458 positions will need to be filled, assuming the company is looking to maintain its current workforce size and service capacity.
- The purpose of a mentorship program would be to have current employees who are approaching retirement age assume a mentorship role in order to train their would-be replacements on their job duties and responsibilities. To see what kind of shape these departures will leave the company in, it's necessary to compare the number of retirement-ready individuals by title to the total number of active employees by title. To find the total number of active employees by title, the following SQL query can be used:

DROP TABLE IF EXISTS active_titles;
--get total number of unduplicated current employees by emp_no
SELECT DISTINCT ON (e.emp_no) e.emp_no,
    e.first_name,
e.last_name,
de.from_date,
de.to_date,
ti.title
INTO active_titles
FROM employees as e
INNER JOIN dept_emp as de
ON (e.emp_no = de.emp_no)
INNER JOIN titles_refactored as ti
ON (e.emp_no = ti.emp_no)
WHERE (de.to_date = '9999-01-01'); 

-- Next, create a table of the total employee counts by title
SELECT COUNT(at.title), at.title
--INTO active_titles_count
FROM active_titles as at
GROUP BY at.title
ORDER BY count DESC;

The above code, when run in the SQL query tool, yields the following:

[active_titles image here]

It would also be to our advantage to have a count handy of the number of mentors by title generated from our mentorshp_eligibility table. This info can be retrieved using the following SQL query:

SELECT COUNT(me.title), me.title
INTO mentor_count
FROM mentorship_eligibility as me
GROUP BY me.title
ORDER BY count DESC;

The above code yields the following table:

[mentor_count table here]

If we compare the above list showing the total number of active PH employees by title to our earlier finding of the number of retirement-eligible employees by title, the following items are apparent:

* PH Managers
- Of 24 total managers, the loss of 2 of those personnel to retirement represents an 8% loss of that workforce.
- Neither of the retiring managers is eligible for mentorship.

* PH Senior Engineers
- Of 33,064 total Senior Engineers, the loss of 25,916 of those personnel to retirement represents a 78% loss of that workforce.
- 529 of the 25,916 retiring Senior Engineers are eligible for mentorship, meaning they would each need to mentor roughly 49 staff to maintain workforce equilibrium.

* PH Senior Staff
- Of 73,145 total Senior Staff, the loss of 24,926 of those personnel to retirement represents a 34% loss of that workforce.
- 569 of the 24,926 retiring Senior Staff are eligible for mentorship, meaning they would each need to mentor roughly 43 staff to maintain workforce equilibrium.

* PH Technique Leaders
- Of 12,048 total Technique Leaders, the loss of 3,603 of those personnel to retirement represents a 30% loss of that workforce.
- 77 of the 3,603 retiring Technique Leaders are eligible for mentorship, meaning they would each need to mentor roughly 47 staff to maintain workforce equilibrium.

* PH Engineers
- Of 77,132 total Engineers, the loss of 9,285 of those personnel to retirement represents a 12% loss of that workforce. 
- 190 of the 9,285 retiring engineers are eligible for mentorship, meaning they would each need to mentor roughly 90 staff to maintain workforce equilibrium.

* PH Assistant Engineers
- Of 10,314 total Assistant Engineers, the loss of 1,090 of those personnel to retirement represents a 11% loss of that workforce.
- 29 of the 1,090 retiring Assistant Engineers are eligible for mentorship, meaning they would each need to mentor roughly 38 staff to maintain workforce equilibrium.

* PH Staff
- Of 34,397 total Staff, the loss of 7,636 of those personnel to retirement represents a 22% loss of that workforce.
- 155 of the 7,636 retiring Staff are eligible for mentorship, meaning they would each need to mentor roughly 49 staff to maintain workforce equilibrium.

While all titles, with the exception of managers, stand to lose more than 10% of their workforce to the impending wave of retirees, the most egregious loss will be felt by Senior Engineers. However, based on mentorship eligibility, the ratio of employees lost to retirement versus Senior Engineer mentors available to train replacements is relatively close to standard. The engineer mentors, on the other hand, will have roughly double the number of mentees per retiree as compared to mentors with other job titles.  

It would be interesting and potentially necessary for the avoidance of similar "silver tsunami" scenarios in the future, for further analysis, to try to determine the root cause of this sudden wave of retirements by taking a more in-depth look at hiring trends and length-of-employment numbers. It is possible this "silver tsunami" is the result of a hiring boom that took place in the mid-1980s, but further analysis is needed to determine this with certainty.



