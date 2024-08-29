# End-to-End Data Analysis Project (Excel, SQL, & Power BI)

## Project Overview
This project is designed to fulfill a client requirement as described in the following user story:

**User Story:**  
"As the Head of Marketing, I want to identify the top YouTubers in the UK based on subscriber count, videos uploaded, and views accumulated, so that I can decide on which channels would be best to run marketing campaigns to generate a good ROI."

The source dataset for this analysis is included in the `datasets` folder of this repository.

## Project Stages

### 1. Data Cleaning
The first stage involved cleaning the dataset to ensure that only relevant and accurate data was used in the analysis. The following steps were taken:
- **Removing Unnecessary Data:** Unneeded columns and rows were removed.
- **Extracting Channel Names:** SQL was used to extract channel names from the first column.

*SQL Code for Removing Unnecessary Data & Extracting Channel Names:*  
```sql
/*
# 1. Select the required columns
# 2. Extract the channel name from the 'NOMBRE' column
*/
-- 1.
SELECT
    SUBSTRING(NOMBRE, 1, CHARINDEX('@', NOMBRE) -1) AS channel_name,  -- 2.
    total_subscribers,
    total_views,
    total_videos

FROM
   youtubers_influencers_uk
```

### 2. Data Transformation
After cleaning the data, SQL was utilized to transform the data into a format suitable for analysis. A SQL view was created to streamline this process.

*SQL Code for Data Transformation:*  
```sql
/*
# 1. Create a view to store the transformed data
# 2. Cast the extracted channel name as VARCHAR(100)
# 3. Select the required columns from the top_uk_youtubers_2024 SQL table 
*/

-- 1.
CREATE VIEW view_uk_youtubers_2024 AS

-- 2.
SELECT
    CAST(SUBSTRING(NOMBRE, 1, CHARINDEX('@', NOMBRE) -1) AS VARCHAR(100)) AS channel_name, -- 2. 
    total_subscribers,
    total_views,
    total_videos

-- 3.
FROM
    youtubers_influencers_uk
```

### 3. Data Validation: The cleaned & transformed data was tested to ensure it met the required criteria:
  - 4 columns
  - 100 rows
  - No duplicate channel names
  - Correct data types for each column

*SQL Code for Data Validation:*
*1.Row count check:*
```sql
/*
# Count the total number of records (or rows) are in the SQL view
*/

SELECT
    COUNT(*) AS no_of_rows
FROM
    view_youtubers_influencers_uk;
```

*2.Column count check:*
```/*
# Count the total number of columns (or fields) are in the SQL view
*/

SELECT
    COUNT(*) AS column_count
FROM
    INFORMATION_SCHEMA.COLUMNS
WHERE
    TABLE_NAME = 'view_youtubers_influencers_uk';

*3.Data type check:*
```sql
/*
# Check the data types of each column from the view by checking the INFORMATION SCHEMA view
*/

-- 1.
SELECT
    COLUMN_NAME,
    DATA_TYPE
FROM
    INFORMATION_SCHEMA.COLUMNS
WHERE
    TABLE_NAME = 'view_youtubers_influencers_uk';
```

*4.Duplicate count check:*
```sql
/*
# 1. Check for duplicate rows in the view
# 2. Group by the channel name
# 3. Filter for groups with more than one row
*/

-- 1.
SELECT
    channel_name,
    COUNT(*) AS duplicate_count
FROM
    view_youtubers_influencers_uk

-- 2.
GROUP BY
    channel_name

-- 3.
HAVING
    COUNT(*) > 1;
```

### 4. Data Visualization
The transformed data was connected to Power BI for visualization. The visualization helped identify key trends and insights regarding the top YouTubers in the UK.

*Power BI Visualization:*  
![Power BI Visualization](link_to_your_gif.gif)  
*I will add the Power BI GIF here.*

### 4. Data Analysis
Finally, the data was analyzed using both SQL and Excel. This analysis provided actionable insights for the client, aiding in the decision-making process for selecting the best YouTube channels for marketing campaigns.

*SQL Code for Data Analysis:*  
```sql
/* 
# 1. Define variables 
# 2. Create a CTE that rounds the average views per video 
# 3. Select the column you need and create calculated columns from existing ones 
# 4. Filter results by Youtube channels
# 5. Sort results by net profits (from highest to lowest)
*/

-- 1. 
DECLARE @conversionRate FLOAT = 0.02;		-- The conversion rate @ 2%
DECLARE @productCost FLOAT = 5.0;			-- The product cost @ $5
DECLARE @campaignCost FLOAT = 50000.0;		-- The campaign cost @ $50,000	


-- 2.  
WITH ChannelData AS (
    SELECT 
        channel_name,
        total_views,
        total_videos,
        ROUND((CAST(total_views AS FLOAT) / total_videos), -4) AS rounded_avg_views_per_video
    FROM 
        view_youtubers_influencers_uk
)

-- 3. 
SELECT 
    channel_name,
    rounded_avg_views_per_video,
    (rounded_avg_views_per_video * @conversionRate) AS potential_units_sold_per_video,
    (rounded_avg_views_per_video * @conversionRate * @productCost) AS potential_revenue_per_video,
    ((rounded_avg_views_per_video * @conversionRate * @productCost) - @campaignCost) AS net_profit
FROM 
    ChannelData


-- 4. 
WHERE 
    channel_name in ('NoCopyrightSounds', 'DanTDM', 'Dan Rhodes')    


-- 5.  
ORDER BY
	net_profit DESC
```

## Conclusion
Based on the viewershp and views per subscriber, Dan Rhodes appears to be the best option to advance with because there's a higher return on investment with Dan Rhodes compared to the other channels.


