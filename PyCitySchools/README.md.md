

```python
# Dependencies
import pandas as pd
```


```python
# Import files with pandas
school_df = pd.read_csv("generated_data/schools_complete.csv")
student_df = pd.read_csv("generated_data/students_complete.csv")

# Output headers for my use
school_df.head(1)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School ID</th>
      <th>school_name</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Miller High School</td>
      <td>Charter</td>
      <td>2424</td>
      <td>1418040</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Output headers for my use
student_df.head(1)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Student ID</th>
      <th>student_name</th>
      <th>gender</th>
      <th>grade</th>
      <th>school_name</th>
      <th>reading_score</th>
      <th>math_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>April Miller</td>
      <td>F</td>
      <td>9th</td>
      <td>Miller High School</td>
      <td>99</td>
      <td>92</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Make a combined dataframe for later
district_df = pd.merge(student_df, school_df, on="school_name")
district_df.head(1)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Student ID</th>
      <th>student_name</th>
      <th>gender</th>
      <th>grade</th>
      <th>school_name</th>
      <th>reading_score</th>
      <th>math_score</th>
      <th>School ID</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>April Miller</td>
      <td>F</td>
      <td>9th</td>
      <td>Miller High School</td>
      <td>99</td>
      <td>92</td>
      <td>0</td>
      <td>Charter</td>
      <td>2424</td>
      <td>1418040</td>
    </tr>
  </tbody>
</table>
</div>




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
math_passing = student_df["math_score"][student_df["math_score"] >= 60].count()
math_passing = (math_passing / student_total) * 100

# - % Passing Reading
reading_passing = student_df["reading_score"][student_df["reading_score"] >= 60].count()
reading_passing = (reading_passing / student_total) * 100

# - Overall Passing Rate (Average of the above two)
overall_passing = (math_passing + reading_passing) / 2

# Make that table!
district_summary_table = pd.DataFrame({"Total Schools": [school_total],
                                       "Total Students": ["{:,}".format(student_total)],
                                       "Total Budget": ["${:,}".format(budget_total)],
                                       "Average Math Score": ["{:.2f}%".format(math_average)],
                                       "Average Reading Score": ["{:.2f}%".format(reading_average)],
                                       "% Passing Math": ["{:.2f}%".format(math_passing)],
                                       "% Passing Reading": ["{:.2f}%".format(reading_passing)],
                                       "% Overall Passing Rate": ["{:.2f}%".format(overall_passing)]
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
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
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
      <td>82.27%</td>
      <td>82.87%</td>
      <td>100.00%</td>
      <td>92.77%</td>
      <td>96.38%</td>
    </tr>
  </tbody>
</table>
</div>




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
school_students = school_group["student_name"].count()

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
math_pass = school_index["math_score"].loc[school_index.math_score >= 60]
math_pass = pd.DataFrame(math_pass).reset_index().groupby("school_name").count()
math_pass = pd.to_numeric(math_pass["math_score"])
math_pass = ((math_pass / school_students) * 100)

# - % Passing Reading
reading_pass = school_index["reading_score"].loc[school_index.reading_score >= 60]
reading_pass = pd.DataFrame(reading_pass).reset_index().groupby("school_name").count()
reading_pass = pd.to_numeric(reading_pass["reading_score"])
reading_pass = ((reading_pass / school_students) * 100)


# - Overall Passing Rate (Average of the above two)
overall_pass = ((math_pass + reading_pass) / 2)

math_pass = math_pass.map("{:.1f}%".format)
reading_pass = reading_pass.map("{:.1f}%".format)
overall_pass = overall_pass.map("{:.1f}%".format)

# Make this table!
school_summary_table = pd.DataFrame(data={"School Type": school_type,
                                          "Total Students": school_students.map("{:,}".format),
                                          "Total School Budget": school_budget.map("${:,}".format),
                                          "Per Student Budget": per_student_budget.map("${:,}".format),
                                          "Average Math Score": school_math_average.map("{:.2f}%".format),
                                          "Average Reading Score": school_reading_average.map("{:.2f}%".format),
                                          "% Passing Math": math_pass,
                                          "% Passing Reading": reading_pass,
                                          "Overall Passing Rate": overall_pass
                                         })
#school_summary_table = school_summary_table.reset_index()
#school_summary_table = school_summary_table.rename(columns={"school_name": "School Name"})
school_summary_table = school_summary_table[["School Type",
                                            "Total Students",
                                            "Total School Budget",
                                            "Per Student Budget",
                                            "Average Math Score",
                                            "Average Reading Score",
                                            "% Passing Math",
                                            "% Passing Reading",
                                            "Overall Passing Rate"]]
school_summary_table
# I tried a lot of things that didn't work...
#school_students

#math_passing = student_df["math_score"][student_df["math_score"] >= 60].count()
#math_passing = (math_passing / student_total) * 100


#I need total number of students per school - school_students
#I also need how many students passed math
#school_group["math_score"].count()
#district_df["math_score"].loc[district_df["school_name"] == "Campbell High School"]
#school_math_passing = school_group[school_group["math_score"] >= 60].count()
#school_group["math_score"].unique()
#school_math_passing = pd.DataFrame({[key for key in school_group["math_score"].unique()]: [len(key) for key in school_group["math_score"].unique()]})

#school_math_passing
#school_math_scores = school_group["math_score"].unique()
#school_math_pass = {}
#for key in school_math_scores.keys():
#    school_math_pass[key] = []
#    for index in school_math_scores[key]:
#        print(key)
#        print(index)
#        if index >= 60:
#            school_math_pass[key] += [index]
#    print(key)
#    print(len(school_math_pass[key]) / school_group["math_score"].count())
#for i in school_math_scores:
#    print(len(i))
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
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
      <th>Overall Passing Rate</th>
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
      <td>83.59%</td>
      <td>93.77%</td>
      <td>100.0%</td>
      <td>100.0%</td>
      <td>100.0%</td>
    </tr>
    <tr>
      <th>Galloway High School</th>
      <td>Charter</td>
      <td>2,471</td>
      <td>$1,445,535</td>
      <td>$585.0</td>
      <td>83.57%</td>
      <td>94.03%</td>
      <td>100.0%</td>
      <td>100.0%</td>
      <td>100.0%</td>
    </tr>
    <tr>
      <th>Glass High School</th>
      <td>District</td>
      <td>3,271</td>
      <td>$2,155,589</td>
      <td>$659.0</td>
      <td>81.29%</td>
      <td>76.89%</td>
      <td>100.0%</td>
      <td>88.7%</td>
      <td>94.4%</td>
    </tr>
    <tr>
      <th>Gomez High School</th>
      <td>Charter</td>
      <td>2,154</td>
      <td>$1,288,092</td>
      <td>$598.0</td>
      <td>83.84%</td>
      <td>94.03%</td>
      <td>100.0%</td>
      <td>100.0%</td>
      <td>100.0%</td>
    </tr>
    <tr>
      <th>Gonzalez High School</th>
      <td>Charter</td>
      <td>1,855</td>
      <td>$1,192,765</td>
      <td>$643.0</td>
      <td>83.44%</td>
      <td>94.14%</td>
      <td>100.0%</td>
      <td>100.0%</td>
      <td>100.0%</td>
    </tr>
    <tr>
      <th>Hawkins High School</th>
      <td>District</td>
      <td>4,555</td>
      <td>$2,851,430</td>
      <td>$626.0</td>
      <td>81.72%</td>
      <td>77.01%</td>
      <td>100.0%</td>
      <td>88.7%</td>
      <td>94.4%</td>
    </tr>
    <tr>
      <th>Kelly High School</th>
      <td>District</td>
      <td>3,307</td>
      <td>$2,225,611</td>
      <td>$673.0</td>
      <td>81.68%</td>
      <td>76.83%</td>
      <td>100.0%</td>
      <td>88.8%</td>
      <td>94.4%</td>
    </tr>
    <tr>
      <th>Macdonald High School</th>
      <td>Charter</td>
      <td>901</td>
      <td>$550,511</td>
      <td>$611.0</td>
      <td>83.78%</td>
      <td>93.93%</td>
      <td>100.0%</td>
      <td>100.0%</td>
      <td>100.0%</td>
    </tr>
    <tr>
      <th>Miller High School</th>
      <td>Charter</td>
      <td>2,424</td>
      <td>$1,418,040</td>
      <td>$585.0</td>
      <td>83.61%</td>
      <td>94.00%</td>
      <td>100.0%</td>
      <td>100.0%</td>
      <td>100.0%</td>
    </tr>
    <tr>
      <th>Sherman High School</th>
      <td>District</td>
      <td>3,213</td>
      <td>$2,152,710</td>
      <td>$670.0</td>
      <td>81.50%</td>
      <td>77.29%</td>
      <td>100.0%</td>
      <td>89.4%</td>
      <td>94.7%</td>
    </tr>
    <tr>
      <th>Smith High School</th>
      <td>District</td>
      <td>4,954</td>
      <td>$3,210,192</td>
      <td>$648.0</td>
      <td>81.54%</td>
      <td>77.15%</td>
      <td>100.0%</td>
      <td>89.3%</td>
      <td>94.6%</td>
    </tr>
  </tbody>
</table>
</div>


