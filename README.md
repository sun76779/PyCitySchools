

```python
# Import Dependencies
import pandas as pd
import numpy as np
```


```python
# Create a reference the CSV file desired
sch_file = "raw_data/schools_complete.csv" 
stu_file = "raw_data/students_complete.csv" 

# Read the School File into a Pandas DataFrame
sch_df = pd.read_csv(sch_file, dtype='unicode')
stu_df = pd.read_csv(stu_file, dtype='unicode')

# rename school name to shool, create Link for future merge
sch_df.rename(columns={'name': 'school'}, inplace = True)

# merge student table and school table
merge_df = pd.merge(stu_df, sch_df, on="school", how = "left")
```


```python
sch_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School ID</th>
      <th>school</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
    </tr>
  </tbody>
</table>
</div>




```python
#convert budget and size data type to integer 
sch_df["budget"] = pd.to_numeric(sch_df["budget"])
sch_df["size"] = pd.to_numeric(sch_df["size"])

#convert score / size / budget data type to integer 
merge_df["reading_score"] = pd.to_numeric(merge_df["reading_score"])
merge_df["math_score"] = pd.to_numeric(merge_df["math_score"])
merge_df["size"] = pd.to_numeric(merge_df["size"])
merge_df["budget"] = pd.to_numeric(merge_df["budget"])

# calculate total school
total_school = len(merge_df["School ID"].unique())

# calculate total school
total_student = len(merge_df["Student ID"].unique())

#calculate total budget
total_budget = sch_df["budget"].sum()

# calculate Average Math Score
avg_math = merge_df["math_score"].mean()

# calculate Average Reading Score
avg_reading = merge_df["reading_score"].mean()

# % passing math
passing_math_percent = merge_df.loc[merge_df["math_score"]>=70]["math_score"].count()/total_student

# % passing reading
passing_reading_percent = merge_df.loc[merge_df["reading_score"]>=70]["reading_score"].count()/total_student

# overall Passing Rate
overall_passing_rate = (passing_reading_percent + passing_math_percent)/2

```


```python
## District Summary
```


```python
#create a new dataframe for summary
district_summary = pd.DataFrame({"Total Schools":[total_school],
                                 "Total Students":[total_student],
                                 "Total Budget":[total_budget],
                                 "Average Math Score":[avg_math],
                                 "Average Reading Score":[avg_reading],
                                 "% Passing Math":[passing_math_percent],
                                 "% Passing Reading":[passing_reading_percent],
                                 "Overall Passing Rate":[overall_passing_rate]                            
                                })

#rearrange columns
district_summary = district_summary[["Total Schools",
                                 "Total Students",
                                 "Total Budget",
                                 "Average Math Score",
                                 "Average Reading Score",
                                 "% Passing Math",
                                 "% Passing Reading",
                                 "Overall Passing Rate"                            
                                    ]]

#change format 
district_summary['Total Budget'] = district_summary['Total Budget'].map("${:,.2f}".format)
district_summary['Average Math Score'] = district_summary['Average Math Score'].map("{:,.2f}".format)
district_summary['Average Reading Score'] = district_summary['Average Reading Score'].map("{:,.2f}".format)
district_summary['Total Students'] = district_summary['Total Students'].map("{:,}".format)
district_summary['Overall Passing Rate'] = district_summary['Overall Passing Rate'].map("{:.1%}".format)
district_summary['% Passing Math'] = district_summary['% Passing Math'].map("{:.1%}".format)
district_summary['% Passing Reading'] = district_summary['% Passing Reading'].map("{:.1%}".format)


district_summary
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
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
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39,170</td>
      <td>$24,649,428.00</td>
      <td>78.99</td>
      <td>81.88</td>
      <td>75.0%</td>
      <td>85.8%</td>
      <td>80.4%</td>
    </tr>
  </tbody>
</table>
</div>




```python
'''
School Name
School Type
Total Students
Total School Budget
Per Student Budget
Average Math Score
Average Reading Score
% Passing Math
% Passing Reading
Overall Passing Rate (Average of the above two)

'''
#school name
school_name = merge_df.set_index('school').groupby(['school'])

#school types
sch_types = sch_df.set_index('school')['type']

#Total Students
total_stu = school_name["Student ID"].count()


#Total School Budget
total_budg = sch_df.set_index('school')['budget']

#Per Student Budget
per_stu_budget = total_budg/total_stu

#Average Math Score
avg_math_score = school_name["math_score"].mean()

#Average Reading Score
avg_reading_score = school_name["reading_score"].mean()

# % Passing Math
percent_pass_math = merge_df.loc[merge_df["math_score"] >= 70].groupby('school')['Student ID'].count()/total_stu

# % Passing reading
percent_pass_reading = merge_df.loc[merge_df["reading_score"] >= 70].groupby('school')['Student ID'].count()/total_stu

# Overall Passing Rate
overall_pass_rate = (percent_pass_math + percent_pass_reading)/2

```


```python
## School Summary
```


```python
#create school summary dataframe

school_summary = pd.DataFrame({
                               "School Type" : sch_types,
                               "Total Students" : total_stu,
                               "Total School Budget" : total_budg,
                               "Per Student Budget" : per_stu_budget,
                               "Average Math Score" : avg_math_score,
                               "Average Reading Score" : avg_reading_score,
                               "% Passing Math": percent_pass_math,
                               "% Passing Reading" : percent_pass_reading,
                               "Overall Passing Rate" : overall_pass_rate
    
                            })



#rearrange the order

school_summary = school_summary [[ "School Type", 
                                   "Total Students", 
                                   "Total School Budget",
                                   "Per Student Budget",
                                   "Average Math Score",
                                   "Average Reading Score",
                                   "% Passing Math",
                                   "% Passing Reading",
                                   "Overall Passing Rate"
                                   
                                   ]]
    

    
#format the df

school_summary['Total Students'] = school_summary['Total Students'].map("{:,}".format)
school_summary['Total School Budget'] = school_summary['Total School Budget'].map("{:,}".format)
school_summary['Per Student Budget'] = school_summary['Per Student Budget'].map("{:.1f}".format)
school_summary['Average Math Score'] = school_summary['Average Math Score'].map("{:.1f}".format)
school_summary['Average Reading Score'] = school_summary['Average Reading Score'].map("{:.1f}".format)
school_summary['% Passing Math'] = school_summary['% Passing Math'].map("{:.2%}".format)
school_summary['% Passing Reading'] = school_summary['% Passing Reading'].map("{:.2%}".format)
school_summary['Overall Passing Rate'] = school_summary['Overall Passing Rate'].map("{:.2%}".format)



school_summary 

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
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
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>District</td>
      <td>4,976</td>
      <td>3,124,928</td>
      <td>628.0</td>
      <td>77.0</td>
      <td>81.0</td>
      <td>66.68%</td>
      <td>81.93%</td>
      <td>74.31%</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1,858</td>
      <td>1,081,356</td>
      <td>582.0</td>
      <td>83.1</td>
      <td>84.0</td>
      <td>94.13%</td>
      <td>97.04%</td>
      <td>95.59%</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2,949</td>
      <td>1,884,411</td>
      <td>639.0</td>
      <td>76.7</td>
      <td>81.2</td>
      <td>65.99%</td>
      <td>80.74%</td>
      <td>73.36%</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2,739</td>
      <td>1,763,916</td>
      <td>644.0</td>
      <td>77.1</td>
      <td>80.7</td>
      <td>68.31%</td>
      <td>79.30%</td>
      <td>73.80%</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1,468</td>
      <td>917,500</td>
      <td>625.0</td>
      <td>83.4</td>
      <td>83.8</td>
      <td>93.39%</td>
      <td>97.14%</td>
      <td>95.27%</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>District</td>
      <td>4,635</td>
      <td>3,022,020</td>
      <td>652.0</td>
      <td>77.3</td>
      <td>80.9</td>
      <td>66.75%</td>
      <td>80.86%</td>
      <td>73.81%</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>Charter</td>
      <td>427</td>
      <td>248,087</td>
      <td>581.0</td>
      <td>83.8</td>
      <td>83.8</td>
      <td>92.51%</td>
      <td>96.25%</td>
      <td>94.38%</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2,917</td>
      <td>1,910,635</td>
      <td>655.0</td>
      <td>76.6</td>
      <td>81.2</td>
      <td>65.68%</td>
      <td>81.32%</td>
      <td>73.50%</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4,761</td>
      <td>3,094,650</td>
      <td>650.0</td>
      <td>77.1</td>
      <td>81.0</td>
      <td>66.06%</td>
      <td>81.22%</td>
      <td>73.64%</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>585,858</td>
      <td>609.0</td>
      <td>83.8</td>
      <td>84.0</td>
      <td>94.59%</td>
      <td>95.95%</td>
      <td>95.27%</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3,999</td>
      <td>2,547,363</td>
      <td>637.0</td>
      <td>76.8</td>
      <td>80.7</td>
      <td>66.37%</td>
      <td>80.22%</td>
      <td>73.29%</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>Charter</td>
      <td>1,761</td>
      <td>1,056,600</td>
      <td>600.0</td>
      <td>83.4</td>
      <td>83.7</td>
      <td>93.87%</td>
      <td>95.85%</td>
      <td>94.86%</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1,635</td>
      <td>1,043,130</td>
      <td>638.0</td>
      <td>83.4</td>
      <td>83.8</td>
      <td>93.27%</td>
      <td>97.31%</td>
      <td>95.29%</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2,283</td>
      <td>1,319,574</td>
      <td>578.0</td>
      <td>83.3</td>
      <td>84.0</td>
      <td>93.87%</td>
      <td>96.54%</td>
      <td>95.20%</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>Charter</td>
      <td>1,800</td>
      <td>1,049,400</td>
      <td>583.0</td>
      <td>83.7</td>
      <td>84.0</td>
      <td>93.33%</td>
      <td>96.61%</td>
      <td>94.97%</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Top Performing Schools (By Passing Rate)
```


```python
# sort df by passing rate and print the top 5 rows

sorting_sch = school_summary.sort_values(['Overall Passing Rate'], ascending=[False])
sorting_sch.head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
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
  </thead>
  <tbody>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1,858</td>
      <td>1,081,356</td>
      <td>582.0</td>
      <td>83.1</td>
      <td>84.0</td>
      <td>94.13%</td>
      <td>97.04%</td>
      <td>95.59%</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1,635</td>
      <td>1,043,130</td>
      <td>638.0</td>
      <td>83.4</td>
      <td>83.8</td>
      <td>93.27%</td>
      <td>97.31%</td>
      <td>95.29%</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1,468</td>
      <td>917,500</td>
      <td>625.0</td>
      <td>83.4</td>
      <td>83.8</td>
      <td>93.39%</td>
      <td>97.14%</td>
      <td>95.27%</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>585,858</td>
      <td>609.0</td>
      <td>83.8</td>
      <td>84.0</td>
      <td>94.59%</td>
      <td>95.95%</td>
      <td>95.27%</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2,283</td>
      <td>1,319,574</td>
      <td>578.0</td>
      <td>83.3</td>
      <td>84.0</td>
      <td>93.87%</td>
      <td>96.54%</td>
      <td>95.20%</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Bottom Performing Schools (By Passing Rate)
```


```python
# print last 5 row from sorted df
sorting_sch.tail(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
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
  </thead>
  <tbody>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2,739</td>
      <td>1,763,916</td>
      <td>644.0</td>
      <td>77.1</td>
      <td>80.7</td>
      <td>68.31%</td>
      <td>79.30%</td>
      <td>73.80%</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4,761</td>
      <td>3,094,650</td>
      <td>650.0</td>
      <td>77.1</td>
      <td>81.0</td>
      <td>66.06%</td>
      <td>81.22%</td>
      <td>73.64%</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2,917</td>
      <td>1,910,635</td>
      <td>655.0</td>
      <td>76.6</td>
      <td>81.2</td>
      <td>65.68%</td>
      <td>81.32%</td>
      <td>73.50%</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2,949</td>
      <td>1,884,411</td>
      <td>639.0</td>
      <td>76.7</td>
      <td>81.2</td>
      <td>65.99%</td>
      <td>80.74%</td>
      <td>73.36%</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3,999</td>
      <td>2,547,363</td>
      <td>637.0</td>
      <td>76.8</td>
      <td>80.7</td>
      <td>66.37%</td>
      <td>80.22%</td>
      <td>73.29%</td>
    </tr>
  </tbody>
</table>
</div>




```python
'''
Math Scores by Grade**
Create a table that lists the average Math Score for students of each grade level (9th, 10th, 11th, 12th) at each school.
'''
#check how many grades
#merge_df['grade'].unique()

# create table to store average math score for each grade by school
math_9th = merge_df.loc[merge_df["grade"] == '9th'].groupby('school')['math_score'].mean()
math_10th = merge_df.loc[merge_df["grade"] == '10th'].groupby('school')['math_score'].mean()
math_11th = merge_df.loc[merge_df["grade"] == '11th'].groupby('school')['math_score'].mean()
math_12th = merge_df.loc[merge_df["grade"] == '12th'].groupby('school')['math_score'].mean()

#create math averge by grade dataframe

avg_math_df = pd.DataFrame({"9th": math_9th,
                            "10th": math_10th,
                            "11th": math_11th,
                            "12th": math_12th,
    
                            })

#rearragne the order
avg_math_df = avg_math_df[["9th","10th","11th","12th"]]

avg_math_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>77.083676</td>
      <td>76.996772</td>
      <td>77.515588</td>
      <td>76.492218</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.094697</td>
      <td>83.154506</td>
      <td>82.765560</td>
      <td>83.277487</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.403037</td>
      <td>76.539974</td>
      <td>76.884344</td>
      <td>77.151369</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.361345</td>
      <td>77.672316</td>
      <td>76.918058</td>
      <td>76.179963</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>82.044010</td>
      <td>84.229064</td>
      <td>83.842105</td>
      <td>83.356164</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77.438495</td>
      <td>77.337408</td>
      <td>77.136029</td>
      <td>77.186567</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.787402</td>
      <td>83.429825</td>
      <td>85.000000</td>
      <td>82.855422</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>77.027251</td>
      <td>75.908735</td>
      <td>76.446602</td>
      <td>77.225641</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>77.187857</td>
      <td>76.691117</td>
      <td>77.491653</td>
      <td>76.863248</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.625455</td>
      <td>83.372000</td>
      <td>84.328125</td>
      <td>84.121547</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>76.859966</td>
      <td>76.612500</td>
      <td>76.395626</td>
      <td>77.690748</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83.420755</td>
      <td>82.917411</td>
      <td>83.383495</td>
      <td>83.778976</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.590022</td>
      <td>83.087886</td>
      <td>83.498795</td>
      <td>83.497041</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.085578</td>
      <td>83.724422</td>
      <td>83.195326</td>
      <td>83.035794</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.264706</td>
      <td>84.010288</td>
      <td>83.836782</td>
      <td>83.644986</td>
    </tr>
  </tbody>
</table>
</div>




```python
# create table to store average reading score for each grade by school
reading_9th = merge_df.loc[merge_df["grade"] == '9th'].groupby('school')['reading_score'].mean()
reading_10th = merge_df.loc[merge_df["grade"] == '10th'].groupby('school')['reading_score'].mean()
reading_11th = merge_df.loc[merge_df["grade"] == '11th'].groupby('school')['reading_score'].mean()
reading_12th = merge_df.loc[merge_df["grade"] == '12th'].groupby('school')['reading_score'].mean()

#create reading averge by grade dataframe

avg_reading_df = pd.DataFrame({"9th": reading_9th,
                            "10th": reading_10th,
                            "11th": reading_11th,
                            "12th": reading_12th,
    
                            })
#rearragne the order
avg_reading_df = avg_reading_df[["9th","10th","11th","12th"]]

avg_reading_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81.303155</td>
      <td>80.907183</td>
      <td>80.945643</td>
      <td>80.912451</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.676136</td>
      <td>84.253219</td>
      <td>83.788382</td>
      <td>84.287958</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.198598</td>
      <td>81.408912</td>
      <td>80.640339</td>
      <td>81.384863</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>80.632653</td>
      <td>81.262712</td>
      <td>80.403642</td>
      <td>80.662338</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.369193</td>
      <td>83.706897</td>
      <td>84.288089</td>
      <td>84.013699</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>80.866860</td>
      <td>80.660147</td>
      <td>81.396140</td>
      <td>80.857143</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.677165</td>
      <td>83.324561</td>
      <td>83.815534</td>
      <td>84.698795</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>81.290284</td>
      <td>81.512386</td>
      <td>81.417476</td>
      <td>80.305983</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>81.260714</td>
      <td>80.773431</td>
      <td>80.616027</td>
      <td>81.227564</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.807273</td>
      <td>83.612000</td>
      <td>84.335938</td>
      <td>84.591160</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>80.993127</td>
      <td>80.629808</td>
      <td>80.864811</td>
      <td>80.376426</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>84.122642</td>
      <td>83.441964</td>
      <td>84.373786</td>
      <td>82.781671</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.728850</td>
      <td>84.254157</td>
      <td>83.585542</td>
      <td>83.831361</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.939778</td>
      <td>84.021452</td>
      <td>83.764608</td>
      <td>84.317673</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.833333</td>
      <td>83.812757</td>
      <td>84.156322</td>
      <td>84.073171</td>
    </tr>
  </tbody>
</table>
</div>




```python
'''
Scores by School Spending
Create a table that breaks down school performances based on average Spending Ranges (Per Student). Use 4 reasonable bins to group school spending. Include in the table each of the following:

Average Math Score
Average Reading Score
% Passing Math
% Passing Reading
Overall Passing Rate (Average of the above two)
'''
#school_summary['Per Student Budget'].min()
# output 578.0

#school_summary['Per Student Budget'].max()
# output 655.0

school_summary["Average Math Score"] = pd.to_numeric(school_summary["Average Math Score"])
school_summary["Average Reading Score"] = pd.to_numeric(school_summary["Average Reading Score"])
school_summary["Per Student Budget"] = pd.to_numeric(school_summary["Per Student Budget"])

school_summary["% Passing Math"] = school_summary["% Passing Math"].replace('%', '', regex=True).astype('float')
school_summary["% Passing Math"] = pd.to_numeric(school_summary["% Passing Math"])

school_summary["% Passing Reading"] = school_summary["% Passing Reading"].replace('%', '', regex=True).astype('float')
school_summary["% Passing Reading"] = pd.to_numeric(school_summary["% Passing Reading"])

```


```python
# create the bins in which Data will be held
bins = [0, 585, 615, 645, 675]
# create group of categories
group_name = ['< $585', "$585 - 614", "$615 - 644", "> $644"]

# putting data into categories
school_summary['Spending Ranges (Per Student)'] = pd.cut(school_summary['Per Student Budget'], bins = bins, labels = group_name)

# group the data set by spending ranges
by_spending_range = school_summary.groupby(['Spending Ranges (Per Student)'])

# calculate score and passing rate by Spending Ranges (Per Student)
mean_math_score = by_spending_range["Average Math Score"].mean()
mean_reading_score = by_spending_range["Average Reading Score"].mean()
mean_percent_math = by_spending_range["% Passing Math"].mean()
mean_percent_reading = by_spending_range["% Passing Reading"].mean()
overall_percent_pass = (mean_percent_math + mean_percent_reading)/2



#create a df to store Scores by School Spending
scores_by_school_spending = pd.DataFrame({"Average Math Score":mean_math_score,
                                          "Average Reading Score":mean_reading_score,
                                          "% Passing Math":mean_percent_math,
                                          "% Passing Reading":mean_percent_reading,
                                          "% Overall Passing Rate":overall_percent_pass
                                         })

# reformating
scores_by_school_spending['Average Math Score'] = scores_by_school_spending['Average Math Score'].map("{:,.2f}".format)
scores_by_school_spending['Average Reading Score'] = scores_by_school_spending['Average Reading Score'].map("{:,.2f}".format)
scores_by_school_spending['% Passing Math'] = scores_by_school_spending['% Passing Math'].map("{:.2f}".format)
scores_by_school_spending['% Passing Reading'] = scores_by_school_spending['% Passing Reading'].map("{:.2f}".format)
scores_by_school_spending['% Overall Passing Rate'] = scores_by_school_spending['% Overall Passing Rate'].map("{:.2f}".format)

#rearrage columns
scores_by_school_spending = scores_by_school_spending [["Average Math Score",
                            "Average Reading Score",
                            "% Passing Math",
                            "% Passing Reading",
                            "% Overall Passing Rate"]]

# print out the table
scores_by_school_spending                




```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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
      <th>&lt; $585</th>
      <td>83.47</td>
      <td>83.95</td>
      <td>93.46</td>
      <td>96.61</td>
      <td>95.03</td>
    </tr>
    <tr>
      <th>$585 - 614</th>
      <td>83.60</td>
      <td>83.85</td>
      <td>94.23</td>
      <td>95.90</td>
      <td>95.06</td>
    </tr>
    <tr>
      <th>$615 - 644</th>
      <td>79.07</td>
      <td>81.87</td>
      <td>75.67</td>
      <td>86.11</td>
      <td>80.89</td>
    </tr>
    <tr>
      <th>&gt; $644</th>
      <td>77.00</td>
      <td>81.03</td>
      <td>66.16</td>
      <td>81.13</td>
      <td>73.65</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Scores by School Size
```


```python
# create the bins in which Data will be held
bins1 = [0, 1000, 2000, 5000]
# create group of categories
group_name1 = ['Small (<1000)', "Medium (1000-2000)", "Large (2000-5000)"]

# putting data into categories
school_summary["Total Students"] = school_summary["Total Students"].replace(',', '', regex=True).astype('float')
school_summary['Total Students'] = pd.to_numeric(school_summary['Total Students'])
school_summary['School Size'] = pd.cut(school_summary['Total Students'], bins = bins1, labels = group_name1)

# group the data set by school size
by_size = school_summary.groupby(['School Size'])

# calculate score and passing rate by school size
mean_math_score = by_size["Average Math Score"].mean()
mean_reading_score = by_size["Average Reading Score"].mean()
mean_percent_math = by_size["% Passing Math"].mean()
mean_percent_reading = by_size["% Passing Reading"].mean()
overall_percent_pass = (mean_percent_math + mean_percent_reading)/2



#create a df to store Scores by School Size
by_size_df = pd.DataFrame({"Average Math Score":mean_math_score,
                                          "Average Reading Score":mean_reading_score,
                                          "% Passing Math":mean_percent_math,
                                          "% Passing Reading":mean_percent_reading,
                                          "% Overall Passing Rate":overall_percent_pass
                                         })

# reformating
by_size_df['Average Math Score'] = by_size_df['Average Math Score'].map("{:,.2f}".format)
by_size_df['Average Reading Score'] = by_size_df['Average Reading Score'].map("{:,.2f}".format)
by_size_df['% Passing Math'] = by_size_df['% Passing Math'].map("{:.2f}".format)
by_size_df['% Passing Reading'] = by_size_df['% Passing Reading'].map("{:.2f}".format)
by_size_df['% Overall Passing Rate'] = by_size_df['% Overall Passing Rate'].map("{:.2f}".format)

#rearrage columns
by_size_df = by_size_df [["Average Math Score",
                            "Average Reading Score",
                            "% Passing Math",
                            "% Passing Reading",
                            "% Overall Passing Rate"]]

# print out the table
by_size_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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
      <td>83.80</td>
      <td>83.90</td>
      <td>93.55</td>
      <td>96.10</td>
      <td>94.83</td>
    </tr>
    <tr>
      <th>Medium (1000-2000)</th>
      <td>83.40</td>
      <td>83.86</td>
      <td>93.60</td>
      <td>96.79</td>
      <td>95.19</td>
    </tr>
    <tr>
      <th>Large (2000-5000)</th>
      <td>77.74</td>
      <td>81.34</td>
      <td>69.96</td>
      <td>82.77</td>
      <td>76.37</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Scores by School Type
```


```python
# group the data set by school type
by_school_type = school_summary.groupby(['School Type'])

# calculate score and passing rate by school type
mean_math_score = by_school_type["Average Math Score"].mean()
mean_reading_score = by_school_type["Average Reading Score"].mean()
mean_percent_math = by_school_type["% Passing Math"].mean()
mean_percent_reading = by_school_type["% Passing Reading"].mean()
overall_percent_pass = (mean_percent_math + mean_percent_reading)/2



#create a df to store Scores by School Type
by_shool_type_df = pd.DataFrame({"Average Math Score":mean_math_score,
                                          "Average Reading Score":mean_reading_score,
                                          "% Passing Math":mean_percent_math,
                                          "% Passing Reading":mean_percent_reading,
                                          "% Overall Passing Rate":overall_percent_pass
                                         })

# reformating
by_shool_type_df['Average Math Score'] = by_shool_type_df['Average Math Score'].map("{:,.2f}".format)
by_shool_type_df['Average Reading Score'] = by_shool_type_df['Average Reading Score'].map("{:,.2f}".format)
by_shool_type_df['% Passing Math'] = by_shool_type_df['% Passing Math'].map("{:.2f}".format)
by_shool_type_df['% Passing Reading'] = by_shool_type_df['% Passing Reading'].map("{:.2f}".format)
by_shool_type_df['% Overall Passing Rate'] = by_shool_type_df['% Overall Passing Rate'].map("{:.2f}".format)

#rearrage columns
by_shool_type_df = by_shool_type_df [["Average Math Score",
                            "Average Reading Score",
                            "% Passing Math",
                            "% Passing Reading",
                            "% Overall Passing Rate"]]

# print out the table
by_shool_type_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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
      <td>83.49</td>
      <td>83.89</td>
      <td>93.62</td>
      <td>96.59</td>
      <td>95.10</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.94</td>
      <td>80.96</td>
      <td>66.55</td>
      <td>80.80</td>
      <td>73.67</td>
    </tr>
  </tbody>
</table>
</div>


