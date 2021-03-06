
3 Trends Observed:
1 - Go to a charter school if you want to pass reading and do better in math.
2 - Having more students is a good way to lessen their chances of success.
3 - Higher per student spending leads to lower passing rates.
4 - Incidentally, charter schools all have fewer students and lower per student spending.


```python
# Dependencies
import pandas as pd
```


```python
# Import files with pandas
school_df = pd.read_csv("generated_data/schools_complete.csv")
student_df = pd.read_csv("generated_data/students_complete.csv")
```


```python
# Make a combined dataframe for later
district_df = pd.merge(student_df, school_df, on="school_name")
```

DISTRICT SUMMARY


```python
# DISTRICT SUMMARY
#> Create a high level snapshot (in table form) of the district's key metrics, including:
# - Total Schools
school_total = school_df["school_name"].count()

# - Total Students
student_total = student_df["student_name"].count()

# - Total Budget
budget_total = school_df["budget"].sum()

# - Average Math Score
math_average = student_df["math_score"].mean()

# - Average Reading Score
reading_average = student_df["reading_score"].mean()

# - % Passing Math
math_passing = student_df["math_score"][student_df["math_score"] >= 70].count()
math_passing = (math_passing / student_total) * 100

# - % Passing Reading
reading_passing = student_df["reading_score"][student_df["reading_score"] >= 70].count()
reading_passing = (reading_passing / student_total) * 100

# - Overall Passing Rate (Average of the above two)
overall_passing = (math_passing + reading_passing) / 2

# Make that table!
district_summary_table = pd.DataFrame({"Total Schools": [school_total],
                                       "Total Students": ["{:,}".format(student_total)],
                                       "Total Budget": ["${:,}".format(budget_total)],
                                       "Average Math Score": math_average,
                                       "Average Reading Score": reading_average,
                                       "% Passing Math": math_passing,
                                       "% Passing Reading": reading_passing,
                                       "% Overall Passing Rate": overall_passing
                                       })
district_summary_table = district_summary_table[["Total Schools",
                                                "Total Students",
                                                "Total Budget",
                                                "Average Math Score",
                                                "Average Reading Score",
                                                "% Passing Math",
                                                "% Passing Reading",
                                                "% Overall Passing Rate"]]
district_summary_table
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>11</td>
      <td>29,376</td>
      <td>$18,648,468</td>
      <td>82.269846</td>
      <td>82.865877</td>
      <td>86.856618</td>
      <td>78.220316</td>
      <td>82.538467</td>
    </tr>
  </tbody>
</table>
</div>



SCHOOL SUMMARY


```python
# SCHOOL SUMMARY
#> Create an overview table that summarizes key metrics about each school, including:

# - School Name
# Make a nice groupby by school_name
school_group = district_df.groupby("school_name")

# - School Type
school_type = school_group["type"].unique()
school_type = school_type.astype('str').str.replace("[", "")
school_type = school_type.str.replace("]", "")
school_type = school_type.str.replace("'", "")

# - Total Students
school_students = school_group["size"].count()

# - Total School Budget
school_budget = school_df.groupby("school_name")
school_budget = school_budget["budget"].sum()

# - Per Student Budget
per_student_budget = school_budget / school_students

# - Average Math Score
school_math_average = school_group["math_score"].mean()

# - Average Reading Score
school_reading_average = school_group["reading_score"].mean()

# - % Passing Math
school_index = district_df.set_index("school_name")
math_pass = school_index["math_score"].loc[school_index.math_score >= 70]
math_pass = pd.DataFrame(math_pass).reset_index().groupby("school_name").count()
math_pass = pd.to_numeric(math_pass["math_score"])
math_pass = ((math_pass / school_students) * 100)

# - % Passing Reading
reading_pass = school_index["reading_score"].loc[school_index.reading_score >= 70]
reading_pass = pd.DataFrame(reading_pass).reset_index().groupby("school_name").count()
reading_pass = pd.to_numeric(reading_pass["reading_score"])
reading_pass = ((reading_pass / school_students) * 100)


# - Overall Passing Rate (Average of the above two)
overall_pass = ((math_pass + reading_pass) / 2)

#math_pass = math_pass.map("{:.1f}%".format)
#reading_pass = reading_pass.map("{:.1f}%".format)
#overall_pass = overall_pass.map("{:.1f}%".format)

# Make this table!
school_summary_table = pd.DataFrame(data={"School Type": school_type,
                                          "Total Students": school_students.map("{:,}".format),
                                          "Total School Budget": school_budget.map("${:,}".format),
                                          "Per Student Budget": per_student_budget.map("${:,}".format),
                                          "Average Math Score": school_math_average,
                                          "Average Reading Score": school_reading_average,
                                          "% Passing Math": math_pass,
                                          "% Passing Reading": reading_pass,
                                          "% Overall Passing Rate": overall_pass
                                         })
school_summary_table = school_summary_table[["School Type",
                                            "Total Students",
                                            "Total School Budget",
                                            "Per Student Budget",
                                            "Average Math Score",
                                            "Average Reading Score",
                                            "% Passing Math",
                                            "% Passing Reading",
                                            "% Overall Passing Rate"]]
school_summary_table
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>school_name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Campbell High School</th>
      <td>Charter</td>
      <td>271</td>
      <td>$157,993</td>
      <td>$583.0</td>
      <td>83.594096</td>
      <td>93.771218</td>
      <td>95.202952</td>
      <td>100.000000</td>
      <td>97.601476</td>
    </tr>
    <tr>
      <th>Galloway High School</th>
      <td>Charter</td>
      <td>2,471</td>
      <td>$1,445,535</td>
      <td>$585.0</td>
      <td>83.566168</td>
      <td>94.029543</td>
      <td>94.212869</td>
      <td>100.000000</td>
      <td>97.106435</td>
    </tr>
    <tr>
      <th>Glass High School</th>
      <td>District</td>
      <td>3,271</td>
      <td>$2,155,589</td>
      <td>$659.0</td>
      <td>81.293183</td>
      <td>76.888108</td>
      <td>82.329563</td>
      <td>67.349434</td>
      <td>74.839499</td>
    </tr>
    <tr>
      <th>Gomez High School</th>
      <td>Charter</td>
      <td>2,154</td>
      <td>$1,288,092</td>
      <td>$598.0</td>
      <td>83.838440</td>
      <td>94.027391</td>
      <td>93.964717</td>
      <td>100.000000</td>
      <td>96.982358</td>
    </tr>
    <tr>
      <th>Gonzalez High School</th>
      <td>Charter</td>
      <td>1,855</td>
      <td>$1,192,765</td>
      <td>$643.0</td>
      <td>83.442588</td>
      <td>94.140701</td>
      <td>93.207547</td>
      <td>100.000000</td>
      <td>96.603774</td>
    </tr>
    <tr>
      <th>Hawkins High School</th>
      <td>District</td>
      <td>4,555</td>
      <td>$2,851,430</td>
      <td>$626.0</td>
      <td>81.723820</td>
      <td>77.005928</td>
      <td>83.907794</td>
      <td>66.915477</td>
      <td>75.411636</td>
    </tr>
    <tr>
      <th>Kelly High School</th>
      <td>District</td>
      <td>3,307</td>
      <td>$2,225,611</td>
      <td>$673.0</td>
      <td>81.678258</td>
      <td>76.829755</td>
      <td>83.398851</td>
      <td>66.555791</td>
      <td>74.977321</td>
    </tr>
    <tr>
      <th>Macdonald High School</th>
      <td>Charter</td>
      <td>901</td>
      <td>$550,511</td>
      <td>$611.0</td>
      <td>83.779134</td>
      <td>93.932297</td>
      <td>94.339623</td>
      <td>100.000000</td>
      <td>97.169811</td>
    </tr>
    <tr>
      <th>Miller High School</th>
      <td>Charter</td>
      <td>2,424</td>
      <td>$1,418,040</td>
      <td>$585.0</td>
      <td>83.610149</td>
      <td>93.997525</td>
      <td>93.564356</td>
      <td>100.000000</td>
      <td>96.782178</td>
    </tr>
    <tr>
      <th>Sherman High School</th>
      <td>District</td>
      <td>3,213</td>
      <td>$2,152,710</td>
      <td>$670.0</td>
      <td>81.502023</td>
      <td>77.290694</td>
      <td>82.944289</td>
      <td>67.289138</td>
      <td>75.116713</td>
    </tr>
    <tr>
      <th>Smith High School</th>
      <td>District</td>
      <td>4,954</td>
      <td>$3,210,192</td>
      <td>$648.0</td>
      <td>81.539160</td>
      <td>77.146952</td>
      <td>83.165119</td>
      <td>66.370610</td>
      <td>74.767864</td>
    </tr>
  </tbody>
</table>
</div>



TOP PERFORMING SCHOOLS (BY PASSING RATE)


```python
# TOP PERFORMING SCHOOLS (BY PASSING RATE)
#> Create a table that highlights the top 5 performing schools based on Overall Passing Rate.  Include:
top_performing_school = school_summary_table
top_performing_school = top_performing_school.sort_values("% Overall Passing Rate", ascending=False)
top_performing_school.head(5)
# - School Name
# - School Type
# - Total Students
# - Total School Budget
# - Per Student Budget
# - Average Math Score
# - Average Reading Score
# - % Passing Math
# - % Passing Reading
# - Overall Passing Rate (Average of the above two)

# Oh that was easy
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>school_name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Campbell High School</th>
      <td>Charter</td>
      <td>271</td>
      <td>$157,993</td>
      <td>$583.0</td>
      <td>83.594096</td>
      <td>93.771218</td>
      <td>95.202952</td>
      <td>100.0</td>
      <td>97.601476</td>
    </tr>
    <tr>
      <th>Macdonald High School</th>
      <td>Charter</td>
      <td>901</td>
      <td>$550,511</td>
      <td>$611.0</td>
      <td>83.779134</td>
      <td>93.932297</td>
      <td>94.339623</td>
      <td>100.0</td>
      <td>97.169811</td>
    </tr>
    <tr>
      <th>Galloway High School</th>
      <td>Charter</td>
      <td>2,471</td>
      <td>$1,445,535</td>
      <td>$585.0</td>
      <td>83.566168</td>
      <td>94.029543</td>
      <td>94.212869</td>
      <td>100.0</td>
      <td>97.106435</td>
    </tr>
    <tr>
      <th>Gomez High School</th>
      <td>Charter</td>
      <td>2,154</td>
      <td>$1,288,092</td>
      <td>$598.0</td>
      <td>83.838440</td>
      <td>94.027391</td>
      <td>93.964717</td>
      <td>100.0</td>
      <td>96.982358</td>
    </tr>
    <tr>
      <th>Miller High School</th>
      <td>Charter</td>
      <td>2,424</td>
      <td>$1,418,040</td>
      <td>$585.0</td>
      <td>83.610149</td>
      <td>93.997525</td>
      <td>93.564356</td>
      <td>100.0</td>
      <td>96.782178</td>
    </tr>
  </tbody>
</table>
</div>



BOTTOM PERFORMING SCHOOLS (BY PASSING RATE)


```python
# BOTTOM PERFORMING SCHOOLS
#> Same as above but the bottom 5
bottom_performing_school = school_summary_table
bottom_performing_school = bottom_performing_school.sort_values("% Overall Passing Rate")
bottom_performing_school.head(5)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>school_name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Smith High School</th>
      <td>District</td>
      <td>4,954</td>
      <td>$3,210,192</td>
      <td>$648.0</td>
      <td>81.539160</td>
      <td>77.146952</td>
      <td>83.165119</td>
      <td>66.370610</td>
      <td>74.767864</td>
    </tr>
    <tr>
      <th>Glass High School</th>
      <td>District</td>
      <td>3,271</td>
      <td>$2,155,589</td>
      <td>$659.0</td>
      <td>81.293183</td>
      <td>76.888108</td>
      <td>82.329563</td>
      <td>67.349434</td>
      <td>74.839499</td>
    </tr>
    <tr>
      <th>Kelly High School</th>
      <td>District</td>
      <td>3,307</td>
      <td>$2,225,611</td>
      <td>$673.0</td>
      <td>81.678258</td>
      <td>76.829755</td>
      <td>83.398851</td>
      <td>66.555791</td>
      <td>74.977321</td>
    </tr>
    <tr>
      <th>Sherman High School</th>
      <td>District</td>
      <td>3,213</td>
      <td>$2,152,710</td>
      <td>$670.0</td>
      <td>81.502023</td>
      <td>77.290694</td>
      <td>82.944289</td>
      <td>67.289138</td>
      <td>75.116713</td>
    </tr>
    <tr>
      <th>Hawkins High School</th>
      <td>District</td>
      <td>4,555</td>
      <td>$2,851,430</td>
      <td>$626.0</td>
      <td>81.723820</td>
      <td>77.005928</td>
      <td>83.907794</td>
      <td>66.915477</td>
      <td>75.411636</td>
    </tr>
  </tbody>
</table>
</div>



MATH SCORE BY GRADE


```python
# MATH SCORES BY GRADE
#> Create a table that lists the average Math Score for students of each grade level (9th, 10th, 11th, 12th) at each school
math_by_grade = school_index.groupby(["school_name","grade"])
math_by_grade = math_by_grade["math_score"].mean()
math_by_grade = pd.DataFrame(math_by_grade)
math_by_grade = math_by_grade.reset_index()
math_by_grade = math_by_grade.pivot(index="school_name", columns="grade")
math_by_grade.columns = math_by_grade.columns.droplevel(0)
math_by_grade = math_by_grade[["9th", "10th", "11th", "12th"]]
math_by_grade
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>grade</th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school_name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Campbell High School</th>
      <td>83.842857</td>
      <td>84.269663</td>
      <td>83.940000</td>
      <td>82.064516</td>
    </tr>
    <tr>
      <th>Galloway High School</th>
      <td>83.534384</td>
      <td>83.551630</td>
      <td>83.975425</td>
      <td>83.204724</td>
    </tr>
    <tr>
      <th>Glass High School</th>
      <td>81.867647</td>
      <td>81.044652</td>
      <td>81.390935</td>
      <td>80.823120</td>
    </tr>
    <tr>
      <th>Gomez High School</th>
      <td>83.676568</td>
      <td>83.966817</td>
      <td>83.874468</td>
      <td>83.828916</td>
    </tr>
    <tr>
      <th>Gonzalez High School</th>
      <td>83.548263</td>
      <td>83.952118</td>
      <td>83.201970</td>
      <td>82.840206</td>
    </tr>
    <tr>
      <th>Hawkins High School</th>
      <td>81.667758</td>
      <td>81.475371</td>
      <td>81.885770</td>
      <td>81.938296</td>
    </tr>
    <tr>
      <th>Kelly High School</th>
      <td>81.789659</td>
      <td>81.881168</td>
      <td>81.497283</td>
      <td>81.453920</td>
    </tr>
    <tr>
      <th>Macdonald High School</th>
      <td>84.255507</td>
      <td>83.813953</td>
      <td>83.482906</td>
      <td>83.516484</td>
    </tr>
    <tr>
      <th>Miller High School</th>
      <td>83.823713</td>
      <td>83.624661</td>
      <td>83.635838</td>
      <td>83.304183</td>
    </tr>
    <tr>
      <th>Sherman High School</th>
      <td>81.496614</td>
      <td>81.526882</td>
      <td>81.232117</td>
      <td>81.735955</td>
    </tr>
    <tr>
      <th>Smith High School</th>
      <td>81.909804</td>
      <td>80.997980</td>
      <td>81.832724</td>
      <td>81.548182</td>
    </tr>
  </tbody>
</table>
</div>



READING SCORES BY GRADE


```python
# READING SCORES BY GRADE
#> Copy the above and replace all instances of 'reading' with 'reading'
reading_by_grade = school_index.groupby(["school_name","grade"])
reading_by_grade = reading_by_grade["reading_score"].mean()
reading_by_grade = pd.DataFrame(reading_by_grade)
reading_by_grade = reading_by_grade.reset_index()
reading_by_grade = reading_by_grade.pivot(index="school_name", columns="grade")
reading_by_grade.columns = reading_by_grade.columns.droplevel(0)
reading_by_grade = reading_by_grade[["9th", "10th", "11th", "12th"]]
reading_by_grade
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>grade</th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school_name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Campbell High School</th>
      <td>93.471429</td>
      <td>93.876404</td>
      <td>94.080000</td>
      <td>93.709677</td>
    </tr>
    <tr>
      <th>Galloway High School</th>
      <td>94.065903</td>
      <td>93.961957</td>
      <td>93.979206</td>
      <td>94.129921</td>
    </tr>
    <tr>
      <th>Glass High School</th>
      <td>76.444570</td>
      <td>77.319834</td>
      <td>77.128895</td>
      <td>76.618384</td>
    </tr>
    <tr>
      <th>Gomez High School</th>
      <td>94.186469</td>
      <td>93.972851</td>
      <td>93.808511</td>
      <td>94.130120</td>
    </tr>
    <tr>
      <th>Gonzalez High School</th>
      <td>94.042471</td>
      <td>94.103131</td>
      <td>94.416256</td>
      <td>94.036082</td>
    </tr>
    <tr>
      <th>Hawkins High School</th>
      <td>76.518003</td>
      <td>77.174355</td>
      <td>77.526621</td>
      <td>76.852106</td>
    </tr>
    <tr>
      <th>Kelly High School</th>
      <td>76.367803</td>
      <td>77.267875</td>
      <td>76.637228</td>
      <td>76.966988</td>
    </tr>
    <tr>
      <th>Macdonald High School</th>
      <td>94.048458</td>
      <td>94.135659</td>
      <td>93.799145</td>
      <td>93.670330</td>
    </tr>
    <tr>
      <th>Miller High School</th>
      <td>93.897036</td>
      <td>94.039295</td>
      <td>94.238921</td>
      <td>93.823194</td>
    </tr>
    <tr>
      <th>Sherman High School</th>
      <td>77.292325</td>
      <td>77.111828</td>
      <td>77.312409</td>
      <td>77.501404</td>
    </tr>
    <tr>
      <th>Smith High School</th>
      <td>76.861176</td>
      <td>76.805387</td>
      <td>77.338208</td>
      <td>77.749091</td>
    </tr>
  </tbody>
</table>
</div>



SCORES BY SCHOOL SPENDING


```python
# SCORES BY SCHOOL SPENDING
#> Create a table that breaks down school performances based on average Spending Ranges (Per Student).
#  Use 4 reasonable bins to group school spending.  Include in the table...
# - Average Math Score
# - Average Reading Score
# - % Passing Math
# - % Passing Reading
# - Overall Passing Rate (Average of the above two)
scores_by_spending = school_summary_table
scores_by_spending["Per Student Budget"] = per_student_budget
bins = [0, 600, 630, 660, 1000]
bin_names = ["<$600", "$600-630", "$630-660", "$660+"]
scores_by_spending["Spending Ranges (Per Student)"] = pd.cut(scores_by_spending["Per Student Budget"], bins, labels=bin_names)
scores_by_spending = scores_by_spending.reset_index()
scores_by_spending = scores_by_spending.groupby("Spending Ranges (Per Student)")
scores_by_spending = scores_by_spending[["Average Math Score", "Average Reading Score", "% Passing Math", "% Passing Reading", "% Overall Passing Rate"]]
scores_by_spending.mean()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>Spending Ranges (Per Student)</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;$600</th>
      <td>83.652213</td>
      <td>93.956419</td>
      <td>94.236224</td>
      <td>100.000000</td>
      <td>97.118112</td>
    </tr>
    <tr>
      <th>$600-630</th>
      <td>82.751477</td>
      <td>85.469112</td>
      <td>89.123708</td>
      <td>83.457739</td>
      <td>86.290723</td>
    </tr>
    <tr>
      <th>$630-660</th>
      <td>82.091643</td>
      <td>82.725253</td>
      <td>86.234076</td>
      <td>77.906681</td>
      <td>82.070379</td>
    </tr>
    <tr>
      <th>$660+</th>
      <td>81.590141</td>
      <td>77.060225</td>
      <td>83.171570</td>
      <td>66.922464</td>
      <td>75.047017</td>
    </tr>
  </tbody>
</table>
</div>




```python
# SCORES BY SCHOOL SIZE
#> Repeat the above breakdown, but this time based on a reasonable approximation of school size (small, medium, large)
school_size = district_df.groupby("school_name")
school_size = school_size["student_name"].count()

bins = [0, 1000, 2000, 5000]
bin_names = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]
scores_by_size = school_summary_table
scores_by_size["School Size"] = pd.cut(school_size, bins, labels=bin_names)
scores_by_size = scores_by_size.groupby("School Size")
scores_by_size = scores_by_size[["Average Math Score", "Average Reading Score", "% Passing Math", "% Passing Reading", "% Overall Passing Rate"]]
scores_by_size.mean()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Small (&lt;1000)</th>
      <td>83.686615</td>
      <td>93.851758</td>
      <td>94.771287</td>
      <td>100.000000</td>
      <td>97.385644</td>
    </tr>
    <tr>
      <th>Medium (1000-2000)</th>
      <td>83.442588</td>
      <td>94.140701</td>
      <td>93.207547</td>
      <td>100.000000</td>
      <td>96.603774</td>
    </tr>
    <tr>
      <th>Large (2000-5000)</th>
      <td>82.343900</td>
      <td>83.401987</td>
      <td>87.185945</td>
      <td>79.310056</td>
      <td>83.248000</td>
    </tr>
  </tbody>
</table>
</div>



SCORE BY SCHOOL TYPE


```python
score_by_type = school_summary_table.reset_index()
score_by_type = score_by_type.groupby("School Type")
score_by_type = score_by_type[["Average Math Score", "Average Reading Score", "% Passing Math", "% Passing Reading", "% Overall Passing Rate"]]
score_by_type.mean()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.638429</td>
      <td>93.983112</td>
      <td>94.082011</td>
      <td>100.00000</td>
      <td>97.041005</td>
    </tr>
    <tr>
      <th>District</th>
      <td>81.547289</td>
      <td>77.032287</td>
      <td>83.149123</td>
      <td>66.89609</td>
      <td>75.022607</td>
    </tr>
  </tbody>
</table>
</div>


