# PIZZA PIONEERS: A COMPREHENSIVE PERFORMANCE ANALYSIS

## Overview

This project presents key insights and discusses potential solutions for a Pizzeria based in the US, Pizza Pioneers, by offering actionable recommendations aimed at optimizing operational processes, improving efficiency, and addressing current challenges. <br>
<br> Key questions explored encompass metrics such as consumer taste preferences, order breakdowns, and temporal variations. <br>
<br> The analysis is based on sales data from the year 2015, extracted from an online database.

Power BI has been employed to execute this analysis, leveraging DAX (Data Analysis Expressions) to create calculated columns and measures for comprehensive data exploration.

The interactive Power BI dashboard can be viewed [here](https://app.powerbi.com/reportEmbed?reportId=edfc5f62-7f87-4f3a-a923-5069e59df299&autoAuth=true&ctid=57fd4a92-02c1-47b7-b55f-20a3039e76c3), or alternatively downloaded in .pbix format [here](Pizza_Pioneers_Dashboard.pbix). <br>
If you have any trouble opening the dashboard, please do not hesitate to contact me at stefano.vertua@gmail.com. <br>
The original database, consisting of four distinct Excel files, can be downloaded [here](Pizza+Place+Sales.zip).

## **Table of Contents** <br>
- [Dataset](#dataset) <br>
- [Methodology](#methodology)
- [Executive Summary](#executive-summary)
- [Recommendations](#recommendations)
- [Limitations](#limitations)

## Dataset

The data was sourced from the online database in the Resources section of Maven Analytics’ website. <br> The dataset comprises four different Excel files:


+ “pizza_type_id”  lists all 32 pizzas available on the menu, detailing the ingredients and categorizing them into one of the four available groups: Classic, Chicken, Supreme, and Veggie
+ “order_details_id" records all orders along with the corresponding quantities
+ “pizza_id" provides a comprehensive list of pizzas in all available sizes (Small, Medium, Large, Extra Large, and Extra Extra Large) along with their respective prices in USD
+ “order_id” documents the date and time of each order


A relationship diagram can be seen in the picture below:
![image1](https://github.com/user-attachments/assets/6defd404-b45d-4479-972b-d28df9a32813)

The dataset encompasses orders from the entirety of the year 2015.

## Methodology

The methodology employed for this project comprised the following steps: defining the requirements, data collection, data cleaning and transformation, data modeling, visualization design, testing and validation, and ultimately publishing and sharing insights.

Initially, after importing the CSV file, I analyzed the available columns and data, leveraging Power Query. <br> I noticed that in the “pizza_types” file, row 1 contained header information rather than actual data, as illustrated in the image below. Consequently, I promoted row 1 to serve as the header.

![image2](https://github.com/user-attachments/assets/8c609d5c-2dac-465a-b0cf-b2aec81a8c41)

The remainder of the data across the tables was validated, revealing no invalid entries, incorrect data types, or null values. For numeric columns, all values were confirmed to be accurately numeric. <br> Similarly, textual and temporal columns were validated as correct. <br>
To further ensure data accuracy, I utilized the “Column Quality” feature under the View tab. This functionality allows for a quick verification, confirming that 100% of the data is valid (see image below).

![image3](https://github.com/user-attachments/assets/11ae2fe8-012e-44eb-ac8a-359a6e7be99d)

The next step involved creating a new table, “date_table”, where I added columns for years, months, quarters, days, etc. This data table is useful for establishing correlations between dates and purchases, likely seasonalities, or to find out peak ordering times.

I created a new relationship between the Orders Table and the newly created date table using the date columns. <br> The DAX expression used for creating the date table is as follows:
```
date_table =
ADDCOLUMNS(CALENDAR(MIN(orders[date]),MAX(orders[date])),
"Year", YEAR([Date]),
"Month", FORMAT( [Date], "mmm"),
"Month No", MONTH([Date]),
"Quarter", FORMAT([Date], "QQ"),
"Day", FORMAT([Date], "ddd"),
"Day no", WEEKDAY([Date],2))
```

After completing this, I was able to create new measures, with the first one calculating the total sales, as illustrated below:
```
total_sales=SUMX('order_details','order_details'[quantity]*RELATED(pizzas[price]))
```

Another important metric defines the total number of orders. The formula is: 
```
orders=DISTINCTCOUNT('order_details'[order_id])
```

To determine the number of orders per day, I created a new measure using this calculation:
```
average_orders_daily = DIVIDE(DISTINCTCOUNT(order_details[order_id]), DISTINCTCOUNT(orders[date]))
```
Next, I brainstormed additional measures that might enhance my analysis. <br> Given that I have precise timestamps for each purchase, I can categorize orders into specific time intervals: morning, lunch, afternoon, and dinner. “morning” corresponds to the time from the opening until 11:30, “lunch” ranges from 11:30 to 15:00, “afternoon” is between 15:00 to 18:00, while “dinner”  covers the remaining hours until midnight.
The expression is:
```
Time_of_the_day = 
SWITCH(TRUE(),
orders[time]>= TIME(8,0,0) && orders[time]<=TIME(11,30,0), "morning",
orders[time]>= TIME (11,30,0) && orders[time]<=TIME(15,0,0), "lunch",
orders[time]>= TIME (15,0,0) && orders[time]<=TIME(18,0,0), "afternoon",
orders[time]>= TIME (18,0,0) && orders[time]<=TIME(23,59,59), "dinner")
```

I could now calculate the average number of pizzas per order using the following expression:
```
average pizzas in an order = 
DIVIDE(DISTINCTCOUNT(order_details[order_details_id]),DISTINCTCOUNT(orders[order_id]))
```
In this calculation, the numerator counts the unique order detail IDs, representing distinct pizza orders, while the denominator counts the unique order IDs from the orders table.

Once all the calculations had been completed, I built an interactive dashboard to highlight key business metrics and insights. <br>
When creating the report, I ensured it was designed with simplicity in mind, while providing at the same time the most effective metrics and KPIs. <br> Consistency was taken into account, making sure the fonts, the sizes, and all the other graphic elements were harmonized throughout the report. <br>
In the top-right corner, two interactive filters have been added. One is a date picker, and the second one allows to select the different types of pizzas.


## Executive Summary

Below are the major insights that emerged from the analysis:
+ In 2015, Pizza Pioneers achieved a total of $817.9K in sales, collecting more than 20K orders and 60 orders per day
+ Customers predominantly prefer large and medium pizzas, with nearly half of the sales coming from large pizzas. Smaller and extra-large pizzas are less popular.
+ The pizzeria offers four types of pizza categories (Classic, Supreme, Chicken and Veggie), each of them offering from 6 to 9 choices. All those 4 categories are almost equally popular, with no single category overly dominating.
+ Peak orders are from 12:00 to 13:00 and from 16:00 to 19:00, with Friday being the busiest day. Although the afternoon period is not as busy as lunch and dinner, it still attracts a considerable number of orders.
+ Working days are busier at lunch than the weekend, with Monday at 12:00 being the busiest time. On the other hand, dinner service is stronger during the weekend, although it remains a popular option during the week as well.
+ Seasonal trends show peak sales in the summer followed by a decline in the fall
+ Holidays contribute to increased sales. The days with most orders are November 26th (Thanksgiving), November 27th (Black Friday), and July 4th (Independence Day).

![image1](https://github.com/user-attachments/assets/129c377b-49db-42df-8636-0f65afa19aba)

## Recommendations

Based on the analysis, the following recommendations are proposed:
+ Opening times. Since dinner is the most popular dining period and weekends are particularly busy, ensure adequate staffing levels during those peak times, to maintain service quality and reduce waiting times. On the other hand, the restaurant could be open from 11:30 AM, as only a fraction of the clients order in the morning. Similarly, except for the weekend, the pizzeria may reduce the closing hours, since order volumes after 10 PM are typically low.
+ Menu Optimization. The 4 main pizza categories are approximately equally popular. However, some changes could be made to the rest of the menu. Consider removing the five least-performing pizzas, which come mostly from the Supreme and Veggie category, as well as the XXL size, as it got just 0.1% of the total sales. Additionally, explore strategies to promote the XL size, which also represents a mere fraction of the sales.
+ Adding new options. Given that a quarter of sales derives from veggie pizzas, there is substantial potential to expand the vegetarian and vegan menu. Introducing innovative plant-based options, as well as 100% vegan pizzas, could appeal to a growing demographic and boost sales in this category. Additionally, consider offering lactose-free, halal-friendly, and gluten-free pizzas to establish Pizza Pioneers as an inclusive pizzeria catering to diverse dietary preferences.
+ Customer Loyalty Program. Implement a customer loyalty program that rewards frequent diners, particularly those who order during off-peak times.
+ Promotional Strategies. The Marketing Department could launch special promotions and discounts on Sundays, the least busy day. For example, a “Sunday Funday” deal could help boost Sunday sales.

# Limitations

While the analysis provides valuable insights, there are a few limitations to consider that might impact the effectiveness of the recommendations.
+ The database lacks detailed customer demographics (age, gender, location). Without knowing who the customers are, it is difficult to create targeted marketing strategies or understand the customer segments that drive the most sales. Understanding the demographics could also help improve product offerings tailored to specific customer needs.
+ The analysis primarily focuses on sales and order data, lacking insights into customer satisfaction metrics (feedback, reviews, satisfaction levels). Recommendations may be ineffective without understanding customer satisfaction levels.
+ The report does not provide a breakdown of delivery, dine-in, or takeaway orders. Knowing the distributions of order types could influence decisions related to staffing, logistics, and promotional strategies.
+ There is no detail on repeat versus new customers, and knowing this information could provide different outcomes. If repeat customers drive most of the sales, then the business may want to focus on customer retention strategies. On the other hand, if the business is seeing many first-time customers but low repeat rates, this could indicate an issue with customer satisfaction or the quality of the product.

