

**PyCitySchool Analysis**

* 1) The amount of funding a school has per student does not have an effect on the average test score of the student. In fact it appears that the schools with more funding per student actually have lower test scores
* 2) The size of the school has an effect on average test scores. In general, the smaller the school the better the average test scores in reading and math.
* 3) Charter schools, on average, perform much better than district schools.


```python
import pandas as pd
import os
```


```python
school_path=os.path.join('raw_data','schools_complete.csv')
school_df=pd.read_csv(school_path)
school_df.head(1)
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
      <th>name</th>
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
  </tbody>
</table>
</div>




```python
student_path=os.path.join('raw_data','students_complete.csv')
student_df=pd.read_csv(student_path)
student_df.head(1)
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
      <th>Student ID</th>
      <th>name</th>
      <th>gender</th>
      <th>grade</th>
      <th>school</th>
      <th>reading_score</th>
      <th>math_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Paul Bradley</td>
      <td>M</td>
      <td>9th</td>
      <td>Huang High School</td>
      <td>66</td>
      <td>79</td>
    </tr>
  </tbody>
</table>
</div>




```python
school_student_df=pd.merge(school_df,student_df,right_on='school',left_on='name',how='outer')
school_student_df.head(1)
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
      <th>name_x</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>Student ID</th>
      <th>name_y</th>
      <th>gender</th>
      <th>grade</th>
      <th>school</th>
      <th>reading_score</th>
      <th>math_score</th>
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
      <td>0</td>
      <td>Paul Bradley</td>
      <td>M</td>
      <td>9th</td>
      <td>Huang High School</td>
      <td>66</td>
      <td>79</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Trying to rename columns name_x and name_y

school_student_df.rename(index=str,columns={'name_x':'School_master','name_y':'Student'},inplace=True)
school_student_df.head(1)
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
      <th>School_master</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>Student ID</th>
      <th>Student</th>
      <th>gender</th>
      <th>grade</th>
      <th>school</th>
      <th>reading_score</th>
      <th>math_score</th>
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
      <td>0</td>
      <td>Paul Bradley</td>
      <td>M</td>
      <td>9th</td>
      <td>Huang High School</td>
      <td>66</td>
      <td>79</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Set Overall Variables
district_schools=school_student_df.groupby('School_master').nunique()
district_schools=district_schools.count()
district_schools=district_schools['Student ID']
district_students=school_student_df.count()
district_students=district_students['Student ID']
unique_budget=school_student_df['budget'].unique()
sum_budget=unique_budget.sum()
district_avg_math=school_student_df['math_score'].mean()
district_avg_read=school_student_df['reading_score'].mean()
district_math_pass=school_student_df[school_student_df['math_score']>69].count()
district_read_pass=school_student_df[school_student_df['reading_score']>69].count()
district_math_pct=district_math_pass/district_students
district_math_pct=district_math_pct['Student ID']
district_read_pct=district_read_pass/district_students
district_read_pct=district_read_pct['School ID']
overall_district_percent=(district_math_pct+district_read_pct)/2
```


```python
#Create Overall Summary DataFrame
data_summary_table = pd.DataFrame({
    "Total Schools": district_schools,
    "Total Students": district_students,
    "Total School Budget": sum_budget,
    "Average Math Score": district_avg_math,
    "Average Reading Score": district_avg_read,
    "% Passing Math": district_math_pct,
    "% Passing Reading": district_read_pct,
    "Overall Passing Rate": overall_district_percent
},index=['District Total'])
data_summary_table
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
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>Overall Passing Rate</th>
      <th>Total School Budget</th>
      <th>Total Schools</th>
      <th>Total Students</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>District Total</th>
      <td>0.749809</td>
      <td>0.858055</td>
      <td>78.985371</td>
      <td>81.87784</td>
      <td>0.803932</td>
      <td>24649428</td>
      <td>15</td>
      <td>39170</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Group Data Frame by School
grouped_school=school_student_df.groupby('School_master')
```


```python
#Define variables for schools summary

total_students=grouped_school['Student'].count()
average_math=grouped_school['math_score'].mean()
average_reading=grouped_school['reading_score'].mean()
school_type=grouped_school['type'].unique()
school_type=school_type
school_budget=grouped_school['budget'].unique()
student_budget=school_budget/total_students
math_pass = school_student_df[school_student_df["math_score"]>69].groupby('School_master').count()
total_stu=school_student_df.groupby('School_master').count()
math_per=math_pass/total_stu
math_per=math_per['School ID']
math_per=math_per*100
reading_pass=school_student_df[school_student_df["reading_score"]>69].groupby('School_master').count()
reading_per=reading_pass/total_stu
reading_per=reading_per['School ID']
reading_per=reading_per*100
overall_av=(reading_per+math_per)/2
```


```python
# Create School Summary Data Frame
school_summary_table = pd.DataFrame({
    "School Type": school_type,
    "Total Students": total_students,
    "Total School Budget": school_budget,
    "Per Student Budget": student_budget,
    "Average Math Score": average_math,
    "Average Reading Score": average_reading,
    "% Passing Math": math_per,
    "% Passing Reading": reading_per,
    "Overall Passing Rate": overall_av
})
school_summary_table = school_summary_table[['School Type','Total Students','Total School Budget','Per Student Budget','Average Math Score','Average Reading Score','% Passing Math','% Passing Reading','Overall Passing Rate']]
school_summary_table
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
    <tr>
      <th>School_master</th>
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
      <th>Bailey High School</th>
      <td>[District]</td>
      <td>4976</td>
      <td>[3124928]</td>
      <td>[628.0]</td>
      <td>77.048432</td>
      <td>81.033963</td>
      <td>66.680064</td>
      <td>81.933280</td>
      <td>74.306672</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>[Charter]</td>
      <td>1858</td>
      <td>[1081356]</td>
      <td>[582.0]</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>94.133477</td>
      <td>97.039828</td>
      <td>95.586652</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>[District]</td>
      <td>2949</td>
      <td>[1884411]</td>
      <td>[639.0]</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>65.988471</td>
      <td>80.739234</td>
      <td>73.363852</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>[District]</td>
      <td>2739</td>
      <td>[1763916]</td>
      <td>[644.0]</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>68.309602</td>
      <td>79.299014</td>
      <td>73.804308</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>[Charter]</td>
      <td>1468</td>
      <td>[917500]</td>
      <td>[625.0]</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>93.392371</td>
      <td>97.138965</td>
      <td>95.265668</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>[District]</td>
      <td>4635</td>
      <td>[3022020]</td>
      <td>[652.0]</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>66.752967</td>
      <td>80.862999</td>
      <td>73.807983</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>[Charter]</td>
      <td>427</td>
      <td>[248087]</td>
      <td>[581.0]</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>92.505855</td>
      <td>96.252927</td>
      <td>94.379391</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>[District]</td>
      <td>2917</td>
      <td>[1910635]</td>
      <td>[655.0]</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>65.683922</td>
      <td>81.316421</td>
      <td>73.500171</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>[District]</td>
      <td>4761</td>
      <td>[3094650]</td>
      <td>[650.0]</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>66.057551</td>
      <td>81.222432</td>
      <td>73.639992</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>[Charter]</td>
      <td>962</td>
      <td>[585858]</td>
      <td>[609.0]</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>94.594595</td>
      <td>95.945946</td>
      <td>95.270270</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>[District]</td>
      <td>3999</td>
      <td>[2547363]</td>
      <td>[637.0]</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>66.366592</td>
      <td>80.220055</td>
      <td>73.293323</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>[Charter]</td>
      <td>1761</td>
      <td>[1056600]</td>
      <td>[600.0]</td>
      <td>83.359455</td>
      <td>83.725724</td>
      <td>93.867121</td>
      <td>95.854628</td>
      <td>94.860875</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>[Charter]</td>
      <td>1635</td>
      <td>[1043130]</td>
      <td>[638.0]</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>93.272171</td>
      <td>97.308869</td>
      <td>95.290520</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>[Charter]</td>
      <td>2283</td>
      <td>[1319574]</td>
      <td>[578.0]</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>93.867718</td>
      <td>96.539641</td>
      <td>95.203679</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>[Charter]</td>
      <td>1800</td>
      <td>[1049400]</td>
      <td>[583.0]</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>93.333333</td>
      <td>96.611111</td>
      <td>94.972222</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Top Performing Schools
Top_schools = school_summary_table.sort_values(by="Overall Passing Rate",ascending=False)
Top_schools
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
    <tr>
      <th>School_master</th>
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
      <th>Cabrera High School</th>
      <td>[Charter]</td>
      <td>1858</td>
      <td>[1081356]</td>
      <td>[582.0]</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>94.133477</td>
      <td>97.039828</td>
      <td>95.586652</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>[Charter]</td>
      <td>1635</td>
      <td>[1043130]</td>
      <td>[638.0]</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>93.272171</td>
      <td>97.308869</td>
      <td>95.290520</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>[Charter]</td>
      <td>962</td>
      <td>[585858]</td>
      <td>[609.0]</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>94.594595</td>
      <td>95.945946</td>
      <td>95.270270</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>[Charter]</td>
      <td>1468</td>
      <td>[917500]</td>
      <td>[625.0]</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>93.392371</td>
      <td>97.138965</td>
      <td>95.265668</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>[Charter]</td>
      <td>2283</td>
      <td>[1319574]</td>
      <td>[578.0]</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>93.867718</td>
      <td>96.539641</td>
      <td>95.203679</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>[Charter]</td>
      <td>1800</td>
      <td>[1049400]</td>
      <td>[583.0]</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>93.333333</td>
      <td>96.611111</td>
      <td>94.972222</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>[Charter]</td>
      <td>1761</td>
      <td>[1056600]</td>
      <td>[600.0]</td>
      <td>83.359455</td>
      <td>83.725724</td>
      <td>93.867121</td>
      <td>95.854628</td>
      <td>94.860875</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>[Charter]</td>
      <td>427</td>
      <td>[248087]</td>
      <td>[581.0]</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>92.505855</td>
      <td>96.252927</td>
      <td>94.379391</td>
    </tr>
    <tr>
      <th>Bailey High School</th>
      <td>[District]</td>
      <td>4976</td>
      <td>[3124928]</td>
      <td>[628.0]</td>
      <td>77.048432</td>
      <td>81.033963</td>
      <td>66.680064</td>
      <td>81.933280</td>
      <td>74.306672</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>[District]</td>
      <td>4635</td>
      <td>[3022020]</td>
      <td>[652.0]</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>66.752967</td>
      <td>80.862999</td>
      <td>73.807983</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>[District]</td>
      <td>2739</td>
      <td>[1763916]</td>
      <td>[644.0]</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>68.309602</td>
      <td>79.299014</td>
      <td>73.804308</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>[District]</td>
      <td>4761</td>
      <td>[3094650]</td>
      <td>[650.0]</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>66.057551</td>
      <td>81.222432</td>
      <td>73.639992</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>[District]</td>
      <td>2917</td>
      <td>[1910635]</td>
      <td>[655.0]</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>65.683922</td>
      <td>81.316421</td>
      <td>73.500171</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>[District]</td>
      <td>2949</td>
      <td>[1884411]</td>
      <td>[639.0]</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>65.988471</td>
      <td>80.739234</td>
      <td>73.363852</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>[District]</td>
      <td>3999</td>
      <td>[2547363]</td>
      <td>[637.0]</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>66.366592</td>
      <td>80.220055</td>
      <td>73.293323</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Bottom Performing Schools
Bottom_schools=school_summary_table.sort_values(by="Overall Passing Rate")
Bottom_schools
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
    <tr>
      <th>School_master</th>
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
      <th>Rodriguez High School</th>
      <td>[District]</td>
      <td>3999</td>
      <td>[2547363]</td>
      <td>[637.0]</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>66.366592</td>
      <td>80.220055</td>
      <td>73.293323</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>[District]</td>
      <td>2949</td>
      <td>[1884411]</td>
      <td>[639.0]</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>65.988471</td>
      <td>80.739234</td>
      <td>73.363852</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>[District]</td>
      <td>2917</td>
      <td>[1910635]</td>
      <td>[655.0]</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>65.683922</td>
      <td>81.316421</td>
      <td>73.500171</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>[District]</td>
      <td>4761</td>
      <td>[3094650]</td>
      <td>[650.0]</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>66.057551</td>
      <td>81.222432</td>
      <td>73.639992</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>[District]</td>
      <td>2739</td>
      <td>[1763916]</td>
      <td>[644.0]</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>68.309602</td>
      <td>79.299014</td>
      <td>73.804308</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>[District]</td>
      <td>4635</td>
      <td>[3022020]</td>
      <td>[652.0]</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>66.752967</td>
      <td>80.862999</td>
      <td>73.807983</td>
    </tr>
    <tr>
      <th>Bailey High School</th>
      <td>[District]</td>
      <td>4976</td>
      <td>[3124928]</td>
      <td>[628.0]</td>
      <td>77.048432</td>
      <td>81.033963</td>
      <td>66.680064</td>
      <td>81.933280</td>
      <td>74.306672</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>[Charter]</td>
      <td>427</td>
      <td>[248087]</td>
      <td>[581.0]</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>92.505855</td>
      <td>96.252927</td>
      <td>94.379391</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>[Charter]</td>
      <td>1761</td>
      <td>[1056600]</td>
      <td>[600.0]</td>
      <td>83.359455</td>
      <td>83.725724</td>
      <td>93.867121</td>
      <td>95.854628</td>
      <td>94.860875</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>[Charter]</td>
      <td>1800</td>
      <td>[1049400]</td>
      <td>[583.0]</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>93.333333</td>
      <td>96.611111</td>
      <td>94.972222</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>[Charter]</td>
      <td>2283</td>
      <td>[1319574]</td>
      <td>[578.0]</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>93.867718</td>
      <td>96.539641</td>
      <td>95.203679</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>[Charter]</td>
      <td>1468</td>
      <td>[917500]</td>
      <td>[625.0]</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>93.392371</td>
      <td>97.138965</td>
      <td>95.265668</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>[Charter]</td>
      <td>962</td>
      <td>[585858]</td>
      <td>[609.0]</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>94.594595</td>
      <td>95.945946</td>
      <td>95.270270</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>[Charter]</td>
      <td>1635</td>
      <td>[1043130]</td>
      <td>[638.0]</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>93.272171</td>
      <td>97.308869</td>
      <td>95.290520</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>[Charter]</td>
      <td>1858</td>
      <td>[1081356]</td>
      <td>[582.0]</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>94.133477</td>
      <td>97.039828</td>
      <td>95.586652</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Group data by school and grade
group_school_grade=school_student_df.groupby(['School_master','grade'])
```


```python
#Average math score by grade
group_school_grade_math=group_school_grade['math_score'].mean()
group_school_grade_math.head()
```




    School_master        grade
    Bailey High School   10th     76.996772
                         11th     77.515588
                         12th     76.492218
                         9th      77.083676
    Cabrera High School  10th     83.154506
    Name: math_score, dtype: float64




```python
#Average Reading Score by grade
group_school_grade_read=group_school_grade['reading_score'].mean()
group_school_grade_read.head(1)
```




    School_master       grade
    Bailey High School  10th     80.907183
    Name: reading_score, dtype: float64




```python
#Create bins and use for group of per student budget
bins = [575,600,625,650,675]
fundgroup_names=['critical','low','moderate','high']
school_summary_table['Funding Groups']=pd.cut(school_summary_table["Per Student Budget"],bins,labels=fundgroup_names)
funding_schools=school_summary_table.groupby('Funding Groups')
funding_schools=funding_schools.mean()
funding_schools.drop(columns=['Total Students'])
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
      <th>Overall Passing Rate</th>
    </tr>
    <tr>
      <th>Funding Groups</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>critical</th>
      <td>83.436210</td>
      <td>83.892196</td>
      <td>93.541501</td>
      <td>96.459627</td>
      <td>95.000564</td>
    </tr>
    <tr>
      <th>low</th>
      <td>83.595708</td>
      <td>83.930728</td>
      <td>93.993483</td>
      <td>96.542455</td>
      <td>95.267969</td>
    </tr>
    <tr>
      <th>moderate</th>
      <td>78.032719</td>
      <td>81.416375</td>
      <td>71.112408</td>
      <td>83.453814</td>
      <td>77.283111</td>
    </tr>
    <tr>
      <th>high</th>
      <td>76.959583</td>
      <td>81.058567</td>
      <td>66.218444</td>
      <td>81.089710</td>
      <td>73.654077</td>
    </tr>
  </tbody>
</table>
</div>




```python
#School Size Summary Table
size_bins=[0,1250,2501,5001]
sizegroup_names=['Small','Medium','Large']
school_summary_table['School Size']=pd.cut(school_summary_table["Total Students"],size_bins,labels=sizegroup_names)
school_size_df=school_summary_table.groupby(['School Size'])
school_size_df=school_size_df.mean()
school_size_df.drop(columns=['Total Students'])
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
      <th>Overall Passing Rate</th>
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
      <th>Small</th>
      <td>83.821598</td>
      <td>83.929843</td>
      <td>93.550225</td>
      <td>96.099437</td>
      <td>94.824831</td>
    </tr>
    <tr>
      <th>Medium</th>
      <td>83.357937</td>
      <td>83.885280</td>
      <td>93.644365</td>
      <td>96.748840</td>
      <td>95.196603</td>
    </tr>
    <tr>
      <th>Large</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>66.548453</td>
      <td>80.799062</td>
      <td>73.673757</td>
    </tr>
  </tbody>
</table>
</div>




```python
#School Type Summary Table
school_type_df=school_summary_table
school_type_df=school_type_df.drop(columns=['Total Students','Total School Budget','Per Student Budget'])
school_type_df['School Type'] = school_type_df['School Type'].astype(str)
school_type_df=school_type_df.groupby('School Type')
school_type_df.mean()
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
      <th>Overall Passing Rate</th>
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
      <th>['Charter']</th>
      <td>83.473852</td>
      <td>83.896421</td>
      <td>93.620830</td>
      <td>96.586489</td>
      <td>95.103660</td>
    </tr>
    <tr>
      <th>['District']</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>66.548453</td>
      <td>80.799062</td>
      <td>73.673757</td>
    </tr>
  </tbody>
</table>
</div>


