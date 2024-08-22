
# Luna E-Commerce Platform Dashboard 

This project is done from the perspective of a Chief of Staff for an eCommerce platform called Luna. The CEO has requested a dashboard to provide them on general statistics of their platform. They are also looking to use the information from the dashboard to help them decide which categories to invest in, which ones to monitor, and which ones to divest from. 

## Data Sources

This data is an original dataset from The Commons and can be found in the <a href="https://github.com/teresa-le/luna_ecommerce_dashboard/tree/main/resources">Resources</a> folder. 

## 🛠 Skills
Power BI, DAX Queries, Data Visualization, PgAdmin, SQL, Data Modeling, Python, ETL  

## Actions

### Creating a Database & ETL 
To create the database, I created a schema of the database. Then based on that, I created a logical entity relationship diagram. After I completed planning how the database would be structured, I created the database and tables using PgAdmin.

To create the csv files, I used python, and I imported the csv files into the tables. 

<img src= "https://github.com/teresa-le/Luna_Ecommerce_Dashboard/blob/main/Logical%20ERD%20Diagram.PNG"> 

There are two tables: 
* Order_Details
* Sales_Target

For Order_Details, I created a surrogate primary key called Order_Item_ID because Order_ID is not unique. One person can order multiple items that are part of the same order i.e. have the same Order_ID. For Sales_Target, because no one column is sufficient as a primary key, I made two columns the primary key (composite primary key). I edited the original dataset accordingly, created csv files and then loaded the data into the database. 

### SQL Queries

Query: What is the profit margin for each category and sub-category in a given month and year?
```
SELECT 
    EXTRACT(MONTH FROM order_date) AS order_month,
    EXTRACT(YEAR FROM order_date) AS order_year,
    category, 
    subcategory, 
    ROUND((SUM(profit)::decimal / SUM(amount) * 100),2) AS profit_margin
FROM order_details
GROUP BY 
    order_month,
    order_year, 
    category, 
    subcategory
ORDER BY 
    order_year,
    order_month,
    profit_margin;
```

Below is a snapshot of the results: 
<img src="https://github.com/teresa-le/Luna_Ecommerce_Dashboard/blob/main/resources/Results%20Query%201.PNG"> 


Query: What percentage of sales does each category make up? 

```
WITH sales_summary AS (
    SELECT 
        category,
        SUM(amount) as Total_Sales
    FROM 
        order_details
    GROUP BY 
        category
)
SELECT 
    category,
    Total_Sales,
    ROUND((Total_Sales / SUM(Total_Sales) OVER ()),2) * 100 as Percentage_of_Total
FROM 
    sales_summary
ORDER BY 
    Total_Sales DESC;
```

Below is a snapshot of the results: <br> 
<img src="https://github.com/teresa-le/Luna_Ecommerce_Dashboard/blob/main/resources/Results%20Query%202.PNG"> 


Query: Which months and categories met the target, and which ones didn't?

```

WITH sales_summary AS (
    SELECT 
        category,
        TO_CHAR(order_date, 'MM-YYYY') AS order_date,
        SUM(amount) AS total_sales
    FROM 
        order_details
    GROUP BY 
        category, 
        order_date
), 
sales_target AS (
    SELECT 
        category,
        TO_CHAR(month_order_date, 'MM-YYYY') AS month_order_date,
        target
    FROM 
        sales_target
)
SELECT 
    ss.category,
    ss.order_date, 
    ss.total_sales AS total_sales_category_date,
    st.month_order_date,
    st.target, 
    CASE 
        WHEN ss.total_sales >= st.target THEN 'Yes'
        ELSE 'No'
    END AS target_met
FROM 
    sales_summary ss
LEFT JOIN 
    sales_target st ON ss.category = st.category 
    AND ss.order_date = st.month_order_date
ORDER BY 
    ss.category,
    ss.order_date;

```

### Dashboarding & Data Visualization 
I performed the following actions prior to creating the dashboard: 
* Defined the users and their main objectives
* Defined the insights users are trying to gain 
* Defined the KPIs and metrics users are trying to track 
* Created a wireframe mockup of the dashboard 

For more details on the planning process, you can view the file called <a href="https://github.com/teresa-le/luna_ecommerce_dashboard/blob/main/Dashboard%20Design%20Plan.xlsx">Dashboard Design Plan</a>. 

I then created a dashboard based on the wireframe mockup, which initially didn't have space for all the KPIs I had listed. I decided to make the dashboard two pages, and I placed the visualizations that displayed detailed drill downs that a SME, such as a Category Manager, may use on the second page. I placed the most important and highest level KPIs at the top. To create some of the visualizations, I created DAX queries to obtain the values that I was looking for. 


## Results 

<img src="https://github.com/teresa-le/luna_ecommerce_dashboard/blob/main/resources/Dashboard%20Overview.jpg"> 

<img src="https://github.com/teresa-le/luna_ecommerce_dashboard/blob/main/resources/Dashboard%20Drilldowns.jpg">

Luna sells three types of products: Electronics, furniture and clothing. In 2018 and 2019, electronics and furniture hit their sales targets. More information is needed to understand why clothing missed its sales targets and what can be done to help it meet its sales targets e.g. improved marketing efforts, price optimization, etc. Overall, all product categories are profitable with electronics being the most profitable type of product. Despite this fact, clothing makes up 2/3 of Luna's orders. 

There is also potential room for growth by investing more to increase the sales of electronics, which appear to be more profitable. In the drilldowns, we can see that the two product subcategories that aren't producing a profit are electronics and tables. 45% or almost half of the sales on tables and chairs did not result in a profit so the cost of acquisition may be too high given their price points. It may be worthwhile to continue to sell chairs and tables depending on whether it aligns with the strategic goals of the business and can generate value long-term. 


## Usage 

To view the dashboard using Power BI, you need to have Power BI installed onto your computer and need to download the file <a href="https://github.com/teresa-le/luna_ecommerce_dashboard/blob/main/Luna%20Performance%20Dashboard.pbix">Luna Performance Dashboard</a>. 
