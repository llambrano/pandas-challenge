# pandas-challenge
###### by Libardo Lambrano

## Overview <a name="top"></a>

Using python scripting language to analyze students' district-wide standardized test results.

* [Summary Assignment](#pycityschools)
* [Solution](#solution)
* [Link to Jupyter Notebook](PyCitySchools/standarized-test-results.ipynb)

---
![](/Images/education.png)
## PyCitySchools 

---
> **Given**

Access to every student's math and reading scores `schools_complete.csv`, as well as various information on the schools they attend `schools_complete.csv`. 

---
> **Tasks**

**District Summary**

Create a high level snapshot (in table form) of the district's key metrics, including:

* Total Schools
* Total Students
* Total Budget
* Average Math Score
* Average Reading Score
* % Passing Math (The percentage of students that passed math.)
* % Passing Reading (The percentage of students that passed reading.)
* % Overall Passing (The percentage of students that passed math and reading.)

[view solution](#summary_district)

---
**School Summary**

Create an overview table that summarizes key metrics about each school, including:

* School Name
* School Type
* Total Students
* Total School Budget
* Per Student Budget
* Average Math Score
* Average Reading Score
* % Passing Math (The percentage of students that passed math.)
* % Passing Reading (The percentage of students that passed reading.)
* % Overall Passing (The percentage of students that passed math and reading.)

[view solution](#school_summary)

---
**Top Performing Schools (By % Overall Passing)**

Create a table that highlights the top 5 performing schools based on % Overall Passing. Include:
* School Name
* School Type
* Total Students
* Total School Budget
* Per Student Budget
* Average Math Score
* Average Reading Score
* % Passing Math (The percentage of students that passed math.)
* % Passing Reading (The percentage of students that passed reading.)
* % Overall Passing (The percentage of students that passed math and reading.)

---
**Bottom Performing Schools (By % Overall Passing)**

Create a table that highlights the bottom 5 performing schools based on % Overall Passing. Include all of the same metrics as above.

---
**Math Scores by Grade** 

Create a table that lists the average Math Score for students of each grade level (9th, 10th, 11th, 12th) at each school.

---
**Reading Scores by Grade**

Create a table that lists the average Reading Score for students of each grade level (9th, 10th, 11th, 12th) at each school.

---
**Scores by School Spending**

Create a table that breaks down school performances based on average Spending Ranges (Per Student). Use 4 reasonable bins to group school spending. Include in the table each of the following:

* Average Math Score
* Average Reading Score
* % Passing Math (The percentage of students that passed math.)
* % Passing Reading (The percentage of students that passed reading.)
* % Overall Passing (The percentage of students that passed math and reading.)

---
**Scores by School Size**

Repeat the above breakdown, but this time group schools based on a reasonable approximation of school size (Small, Medium, Large).

---
**Scores by School Type**

Repeat the above breakdown, but this time group schools based on school type (Charter vs. District).

---
**Observable Trends**

* Include a written description of at least two observable trends based on the data.

[Back to the top](#top)

---
> ### Solution 
---
<a name="summary_district"></a>
**District Summary** 

![School Summary](Images/01_district_summary.png)

<details><summary>click here to view steps</summary>

1. Import modules, read source files

    ```
    schools_data_path = "../Resources/schools_complete.csv"
    students_data_path = "../Resources/students_complete.csv"
    schools_df = pd.read_csv(schools_data_path)
    students_df = pd.read_csv(students_data_path)
    ```

2. Combine datasets

    ```
    school_students_df = pd.merge(students_df, schools_df, how='left', on=['school_name', 'school_name'])
    school_students_df
    ```

![Files Combined](Images/01a_combined_files.png)

3. Calculate totals, averages, and % passing scores

    ```
    # Finding total schools 
    schools_count = len(school_students_df['school_name'].unique())

    # finding total students
    students_count = len(school_students_df['student_name'])

    # finding total budget
    total_budget = sum(school_students_df['budget'].unique())

    # finding average math score
    ave_math_score = (school_students_df['math_score'].mean())

    # finding average reading score
    ave_reading_score = (school_students_df['reading_score'].mean())

    # finding % passing math
    num_passing_math = school_students_df.loc[school_students_df['math_score'] >=70]['math_score'].count()
    per_passing_math = num_passing_math/students_count
    per_passing_math

    # finding % passing reading 
    num_passing_reading = school_students_df.loc[school_students_df['reading_score'] >=70]['reading_score'].count()
    per_passing_reading = num_passing_reading/students_count

    # finding overal passing 
    overall_passing = school_students_df[(school_students_df['reading_score'] >= 70) & (school_students_df['math_score'] >= 70)]['student_name'].count()/students_count
    ```
4. Assign values to dataframe and apply format to the numbers

    ```
    summary_df = pd.DataFrame({
                            'Total Schools': [schools_count],
                            'Total Students': [students_count],
                            'Total Budget': [total_budget],
                            'Average Math Score': [ave_math_score],
                            'Average Reading Score': [ave_reading_score],
                            '% Passing Math': [per_passing_math],
                            '% Passing Reading': [per_passing_reading],
                            'Overall Passing': [overall_passing]
                            })

    summary_df.style.format({'Total Schools':'{:,}', 
                            'Total Students':'{:,}',
                            'Total Budget':'${:,}', 
                            'Average Math Score':'{:.1f}', 
                            'Average Reading Score':'{:.1f}', 
                            '% Passing Math':'{:.1%}', 
                            '% Passing Reading':'{:.1%}', 
                            'Overall Passing':'{:.1%}'})

    summary_df
    ```

[Back to District Summary solution](#summary_district)
</details>

[Back to the top](#top)

---
<a name="school_summary"></a>
**School Summary**

![school summary](Images/02_school_summary.png)

<details><summary>click here to view steps</summary>

1. Group data by school
    
    ```
    sc_school = school_students_df.set_index('school_name').groupby(['school_name'])
    ```

2. Calculate totals, averages, and % passing scores

    ```
    # school Name
    sc_school = school_students_df.set_index('school_name').groupby(['school_name'])

    # school type
    sc_type = schools_df.set_index('school_name')['type']

    # Total Students
    sc_students = sc_school['Student ID'].count()

    # Total School Budget
    sc_budget = schools_df.set_index('school_name')['budget']

    # Per Student Budget
    st_budget = schools_df.set_index('school_name')['budget']/schools_df.set_index('school_name')['size']

    # Average Math Score
    avg_math = sc_school['math_score'].mean()

    # Average Reading Score
    avg_read = sc_school['reading_score'].mean()

    # % Passing Math
    pass_math = school_students_df[school_students_df['math_score'] >= 70].groupby('school_name')['Student ID'].count()/sc_students 

    # % Passing Reading
    pass_read = school_students_df[school_students_df['reading_score'] >= 70].groupby('school_name')['Student ID'].count()/sc_students

    # % Overall Passing (The percentage of students that passed math and reading.)
    overall = school_students_df[(school_students_df['reading_score'] >= 70) & (school_students_df['math_score'] >= 70)].groupby('school_name')['Student ID'].count()/sc_students

3. Assign values to dataframe

    ```
    summary_sc = pd.DataFrame({
        'School Type': sc_type, 
        'Total Students' : sc_students,
        'Total School Budget': sc_budget,
        'Per Student Budget' : st_budget,
        'Average Math Score': avg_math,
        'Average Reading Score' : avg_read,
        '% Passing Math': pass_math,
        '% Passing Reading': pass_read,
        'Overall Passing Rate': overall
    })
    ```

4. Format numbers 

    ```
    summary_sc.style.format({'Total Students': '{:,}',
                           'Total School Budget': '${:,}', 
                           'Per Student Budget': '${:.0f}', 
                           'Average Math Score': '{:.1f}', 
                           'Average Reading Score': '{:.1f}', 
                           'Total School Budget': '${:,}', 
                           '% Passing Math': '{:.1%}', 
                           '% Passing Reading': '{:.1%}', 
                           'Overall Passing Rate': '{:.1%}'})

    summary_sc
    ```

[Back to School Summary solution](#school_summary)
</details>

[Back to the top](#top)

---
<a name="top_schools"></a>
**Top Performing Schools (By % Overall Passing)**

![Top Schools](Images/03_top_schools.png)

<details><summary>click here to view steps</summary>

1. Get top schools by `overall passing rate`
    
    ```
    top_5 = summary_sc.sort_values('Overall Passing Rate', ascending = False)
    ```

2. Format dataframe numbers 
    ```
    top_5.head(5).style.format({'Total Students': '{:,}',
                           'Total School Budget': '${:,}', 
                           'Per Student Budget': '${:.0f}', 
                           'Average Math Score': '{:.1f}', 
                           'Average Reading Score': '{:.1f}', 
                           'Total School Budget': '${:,}', 
                           '% Passing Math': '{:.1%}', 
                           '% Passing Reading': '{:.1%}', 
                           'Overall Passing Rate': '{:.1%}'})
    ```
 
[BBack to 'Top Performing Schools' solution)](#top_schools)
</details>

[Back to the top](#top)

---
<a name="bottom_schools"></a>
**Bottom Performing Schools (By % Overall Passing)**

![Bottom Schools](Images/04_bottom_schools.png)

<details><summary>click here to view steps</summary>

1. Get bottom schools by `overall passing rate`

    ```
    bottom_5 = summary_sc.sort_values('Overall Passing Rate', ascending = True)
    ```

2. Format dataframe numbers 

    ```bottom_5.head(5).style.format({'Total Students': '{:,}',
                           'Total School Budget': '${:,}', 
                           'Per Student Budget': '${:.0f}', 
                           'Average Math Score': '{:.1f}', 
                           'Average Reading Score': '{:.1f}', 
                           'Total School Budget': '${:,}', 
                           '% Passing Math': '{:.1%}', 
                           '% Passing Reading': '{:.1%}', 
                           'Overall Passing Rate': '{:.1%}'})
    ```

[Back to 'Bottom Performing Schools' solution](#bottom_schools)
</details>

[Back to the top](#top)

---
<a name="math_scores_by_grade"></a>
**Math Scores by Grade**

![Image Desc](Images/05_math_scores_by_grade.png)

<details><summary>click here to view steps</summary>

1. Calculate average of math scores by grade 

    ```
    ninth_math = students_df.loc[students_df['grade'] == '9th'].groupby('school_name')['math_score'].mean()
    tenth_math = students_df.loc[students_df['grade'] == '10th'].groupby('school_name')['math_score'].mean()
    eleventh_math = students_df.loc[students_df['grade'] == '11th'].groupby('school_name')['math_score'].mean()
    twelfth_math = students_df.loc[students_df['grade'] == '12th'].groupby('school_name')['math_score'].mean()

    math_scores = pd.DataFrame({
            '9th': ninth_math,
            '10th': tenth_math,
            '11th': eleventh_math,
            '12th': twelfth_math
    })
    ```

2. Assign values to dataframe and set index to `school name`
    ```
    math_scores = math_scores[['9th', '10th', '11th', '12th']]
    math_scores.index.name = "school_name"
    ```

3. Format numners 
    ```
    math_scores.style.format({'9th': '{:.1f}', 
                          "10th": '{:.1f}', 
                          "11th": "{:.1f}", 
                          "12th": "{:.1f}"})
    ```
 
[Back to 'title' solution](#math_scores_by_grade)
</details>

[Back to the top](#top)


