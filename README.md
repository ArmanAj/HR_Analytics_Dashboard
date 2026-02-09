# HR Analytics Dashboard

A comprehensive Power BI solution for analyzing employee data, engagement metrics, and workforce trends.

## 📊 Project Overview

This HR Analytics Dashboard provides actionable insights into workforce management, employee engagement, and organizational health. The dashboard enables HR professionals and leadership to make data-driven decisions regarding talent management, retention strategies, and employee satisfaction.

## 🎯 Key Features

- **Employee Demographics & Status Tracking**: Monitor active vs. terminated employees with detailed employee information
- **Engagement Analysis**: Track employee engagement scores, satisfaction levels, and work-life balance metrics
- **Tenure & Age Analytics**: Analyze workforce tenure, age distribution, and career progression
- **Performance Monitoring**: Evaluate employee performance scores and ratings across departments
- **Time-Based Trending**: Visualize workforce metrics over time with dynamic calendar integration
- **Department Analysis**: Break down metrics by department type for targeted insights

## 📁 Data Model Structure

### Main Tables

#### 1. **employee_data**
The core employee information table containing:
- **Fields**: 
  - `EmpID` (Employee ID)
  - `FullName` (Combined First and Last Name)
  - `StartDate` (Employment Start Date)
  - `ExitDate` (Employment End Date)
  - `EmployeeStatus` (Active/Terminated)
  - `EmployeeType` (Employment Classification)
  - `DepartmentType` (Department)
  - `BirthDate` (Date of Birth)
  - `Performance Score` (Performance Rating)
  - `Current Employee Rating` (Latest Rating Score)

- **Calculated Columns**:
  - `IsActive`: Binary indicator (1 = Active, 0 = Not Active)
    ```DAX
    IsActive = IF(employee_data[EmployeeStatus] = "Active", 1, 0)
    ```
  
  - `TenureYears`: Years of service (calculates up to exit date or current date)
    ```DAX
    TenureYears = 
    VAR EndDate = IF(ISBLANK('employee_data'[ExitDate]), TODAY(), 'employee_data'[ExitDate])
    RETURN DATEDIFF(employee_data[StartDate], EndDate, YEAR)
    ```
  
  - `Age`: Current age in years
    ```DAX
    Age = DATEDIFF(employee_data[BirthDate], TODAY(), YEAR)
    ```
  
  - `isTerminated`: Binary indicator for terminated employees
    ```DAX
    isTerminated = IF(ISBLANK(employee_data[ExitDate]), 0, 1)
    ```

#### 2. **employee_engagement_survey_data**
Employee engagement survey responses containing:
- **Fields**:
  - `EmpID` (Employee ID - links to employee_data)
  - `Survey Date` (Date of survey completion)
  - `Engagement` (Engagement Score: 1-5 scale)
  - `Satisfaction` (Satisfaction Score: 1-5 scale)
  - `Work-Life Balance` (Work-Life Balance Score: 1-5 scale)

- **Measures**:
  - `% AverageEngagment`: Average engagement as percentage
    ```DAX
    % AverageEngagment = AVERAGE(employee_engagement_survey_data[Engagement]) / 5
    ```
    Format: `0 %`
  
  - `% AverageSatisfaction`: Average satisfaction as percentage
    ```DAX
    % AverageSatisfaction = AVERAGE(employee_engagement_survey_data[Satisfaction])/5
    ```
    Format: `0.00 %`
  
  - `% Average WL Balance`: Average work-life balance as percentage
    ```DAX
    % Average WL Balance = AVERAGE(employee_engagement_survey_data[Work-Life Balance]) / 5
    ```
    Format: `0 %`

#### 3. **Calendar**
Dynamic date table for engagement survey analysis:
- **Source**: Generated from engagement survey date range
  ```DAX
  Calendar = CALENDAR(MIN('employee_engagement_survey_data'[Survey Date]), MAX('employee_engagement_survey_data'[Survey Date]))
  ```
- **Calculated Columns**:
  - `MonthYear`: Formatted month-year display (e.g., "Jan-2024")
    ```DAX
    MonthYear = FORMAT('Calendar'[Date], "MMM-YYYY")
    ```
  - `MonthStart`: First day of each month
    ```DAX
    MonthStart = DATE(YEAR('Calendar'[Date]), MONTH('Calendar'[Date]), 1)
    ```

#### 4. **TerminationCalendar**
Date table specifically for termination analysis:
- **Source**: Generated from employee exit date range
  ```DAX
  TerminationCalendar = CALENDAR(MIN('employee_data'[ExitDate]), MAX('employee_data'[ExitDate]))
  ```
- **Calculated Columns**: Same as Calendar table (MonthYear, MonthStart)

## 🔗 Data Relationships

The model uses a snowflake schema with the following relationships:

1. **employee_data ↔ employee_engagement_survey_data**
   - Type: One-to-One
   - Key: `EmpID`
   - Cross-filtering: Bi-directional
   - Enables seamless analysis between employee demographics and engagement metrics

2. **employee_engagement_survey_data → Calendar**
   - Type: Many-to-One
   - Key: `Survey Date` → `Date`
   - Cross-filtering: Single direction
   - Enables time-based trending of engagement metrics

3. **employee_data → TerminationCalendar**
   - Type: Many-to-One
   - Key: `ExitDate` → `Date`
   - Cross-filtering: Single direction
   - Enables termination trend analysis

4. **employee_data → LocalDateTable (StartDate)**
   - Type: Many-to-One
   - Automatic date table for StartDate intelligence

5. **employee_data → LocalDateTable (BirthDate)**
   - Type: Many-to-One
   - Automatic date table for BirthDate intelligence

## 🔧 Data Sources

### Source Files
The dashboard connects to two CSV files:

1. **Employee Data CSV** (`filePath_EmloyeeData` parameter)
   - Contains 26 original columns including personal info, employment details, performance data
   - Cleaned and transformed to 10 core columns + 4 calculated columns

2. **Employee Engagement Survey CSV** (`filePath_EmployeEngagment` parameter)
   - Contains survey responses with 5 columns
   - Encoding: Windows-1250
   - Includes engagement, satisfaction, and work-life balance scores

### Data Transformation
Power Query transformations include:
- Header promotion and type conversion
- Column renaming for consistency
- Name concatenation (FirstName + LastName → FullName)
- Column selection to focus on relevant fields
- Date formatting standardization

## 📈 Key Metrics & KPIs

### Employee Metrics
- Total Employee Count (Active & Terminated)
- Active Employee Count
- Terminated Employee Count
- Average Tenure
- Average Age
- Turnover Rate

### Engagement Metrics
- Average Engagement Score (%)
- Average Satisfaction Score (%)
- Average Work-Life Balance Score (%)
- Engagement trends over time
- Department-level engagement comparison

### Performance Metrics
- Performance Score Distribution
- Employee Rating Analysis
- Performance by Department

## 🛠️ Technical Specifications

- **Power BI Compatibility Level**: 1567
- **Default Mode**: Import
- **Culture**: English (United States)
- **Time Intelligence**: Enabled
- **Last Modified**: February 2026
- **Model Size**: ~3.19 MB

## 📊 Dashboard Capabilities

The model supports analysis across multiple dimensions:

### Temporal Analysis
- Monthly and yearly trends
- Cohort analysis based on hire dates
- Termination patterns over time
- Survey response trends

### Departmental Analysis
- Department-wise employee distribution
- Engagement scores by department
- Performance metrics by department
- Turnover analysis by department

### Demographic Analysis
- Age distribution
- Tenure segmentation
- Employee type breakdown
- Status analysis (Active/Terminated)

## 🚀 Getting Started

### Prerequisites
- Microsoft Power BI Desktop (Latest version recommended)
- Access to source CSV files:
  - Employee Data CSV
  - Employee Engagement Survey CSV

### Setup Instructions

1. **Open the Dashboard**
   ```
   Open HR-analysis.pbix in Power BI Desktop
   ```

2. **Update Data Source Paths**
   - Navigate to Transform Data → Data Source Settings
   - Update `filePath_EmloyeeData` parameter with your employee data CSV path
   - Update `filePath_EmployeEngagment` parameter with your engagement survey CSV path

3. **Refresh Data**
   - Click "Refresh" in the Home ribbon
   - Wait for data load completion

4. **Verify Relationships**
   - Navigate to Model view
   - Ensure all relationships are active and correctly configured

## 🔄 Data Refresh Schedule

### Manual Refresh
- Update CSV source files
- Open Power BI Desktop
- Click Refresh

### Automated Refresh (Power BI Service)
- Publish to Power BI Service
- Configure gateway connection
- Set up scheduled refresh (e.g., daily at 6:00 AM)

## 📋 Data Requirements

### Employee Data CSV Format
Required columns (minimum):
- Employee ID
- First Name, Last Name
- Start Date
- Exit Date (can be blank for active employees)
- Employee Status
- Employee Type
- Department Type
- Date of Birth
- Performance Score
- Current Employee Rating

### Engagement Survey CSV Format
Required columns:
- Employee ID
- Survey Date
- Engagement Score (1-5)
- Satisfaction Score (1-5)
- Work-Life Balance Score (1-5)

## 🎨 Visualization Recommendations

Suggested visual types for optimal insights:

1. **KPI Cards**: Total employees, active count, average engagement
2. **Line Charts**: Engagement trends, termination patterns over time
3. **Bar Charts**: Department comparison, performance distribution
4. **Pie/Donut Charts**: Employee status, employee type breakdown
5. **Tables**: Detailed employee listings with filters
6. **Slicers**: Department, date range, employee status filters
7. **Gauge Charts**: Engagement, satisfaction, work-life balance percentages

## 🔍 Analysis Use Cases

### HR Operations
- Monitor workforce size and composition
- Track termination rates and patterns
- Analyze tenure distribution
- Evaluate employee lifecycle

### Talent Management
- Identify engagement trends
- Assess satisfaction levels across departments
- Evaluate work-life balance initiatives
- Monitor performance distributions

### Strategic Planning
- Forecast workforce needs based on trends
- Identify retention risk factors
- Plan succession based on tenure data
- Optimize department structures

## ⚠️ Known Limitations

1. **Data Granularity**: Survey data is at the individual survey response level, not aggregated by employee
2. **Historical Data**: Calendar tables are dynamically generated based on available data range
3. **Real-time Updates**: Requires manual or scheduled refresh; not real-time
4. **Performance**: Large datasets (>100K employees) may require performance optimization

## 🔐 Data Privacy & Security

- Ensure compliance with GDPR, HIPAA, or relevant data protection regulations
- Implement Row-Level Security (RLS) if deploying to Power BI Service
- Restrict access to sensitive employee information
- Regularly audit data access logs

## 📞 Support & Maintenance

### Troubleshooting Common Issues

**Issue**: Data not refreshing
- **Solution**: Verify CSV file paths, check file permissions, ensure CSV files are not open in another application

**Issue**: Relationships broken
- **Solution**: Verify EmpID consistency across tables, check for null values in key columns

**Issue**: Incorrect calculations
- **Solution**: Verify data types, check for blank/null handling in DAX formulas

## 📊 Model Statistics

- **Tables**: 7 (2 fact tables, 2 dimension tables, 3 date tables)
- **Calculated Columns**: 8
- **Measures**: 3
- **Relationships**: 5
- **Data Sources**: 2 CSV files

## 🎯 Future Enhancements

Potential improvements for future versions:

1. **Additional Metrics**
   - Diversity & Inclusion metrics
   - Compensation analysis
   - Recruitment funnel metrics
   - Training & development tracking

2. **Advanced Analytics**
   - Predictive turnover modeling
   - Sentiment analysis from survey comments
   - What-if scenario analysis
   - Cohort retention curves

3. **Integration**
   - Direct database connections
   - API integration with HRIS systems
   - Azure Synapse Analytics integration
   - Real-time data streaming

4. **Enhanced Visualizations**
   - Organization chart visualization
   - Heat maps for department performance
   - Custom tooltips with employee photos
   - Drill-through pages for detailed analysis

## 📄 License

This project is part of Power BI Training materials.

## 👥 Contributors

- **Created**: January 2026
- **Last Updated**: February 2026

---

**Version**: 1.0  
**Last Modified**: February 9, 2026  
**Compatible with**: Power BI Desktop (Latest Version)
