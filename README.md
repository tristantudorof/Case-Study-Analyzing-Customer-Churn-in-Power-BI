# Case Study Analyzing Customer Churn in Power BI

A fictitious dataset about churn from a telecom provider (Databel)  

My Task: To discover why customers are churning 


The Databel dataset consists of 29 different columns and has one row per customer. I'll be analyzing a snapshot of the database at a specific moment in time, meaning there is no time dimension.

The dataset contains numerous dimensions, the first one being Customer_id. The Customer_id is a unique ID that identifies an individual customer. The second column is called Churn Label, and it indicates if a customer churned with "Yes" and "No" labels. The dataset contains various other dimensions, such as demographic fields and information about premium plans.

The dataset contains more than just dimensions, so let's look at some measures. The Total Charges column, for example, takes the sum of all monthly charges billed to a customer. They can all be found in the metadata sheet. 



On the Power BI Home tab, I click Get data and choose "Text/CSV", from which I select the file named Databel - Data.csv, and click Open.


# The first step in any analysis is doing a data check.

Metadata

<img width="743" height="630" alt="Screenshot 2025-12-22 at 7 52 56 PM" src="https://github.com/user-attachments/assets/e8f4b4b7-d361-4b28-adcc-0da3cf16f0a0" />



I'll create two measures to check if the count of Customer ids is equal to the count of Unique Customer ids.

This check is particularly important, because in case there are duplicate rows i might double-count costs later.

I'll create two new measures and name them:

Number of Customers

Number of Customers = COUNT('Databel - Data'[Customer ID])


Number of Unique Customers

Number of Unique Customers = DISTINCTCOUNT('Databel - Data'[Customer ID])

Both showed up as "7k" 

<img width="270" height="435" alt="Screenshot 2025-12-22 at 8 11 00 PM" src="https://github.com/user-attachments/assets/6d96fe88-9409-43c5-8557-698f0302a253" />

To fix this i went to the Visualization pane, Callout value, Display units, and changed it from Auto to None. For both visuals.

<img width="227" height="386" alt="Screenshot 2025-12-22 at 8 15 18 PM" src="https://github.com/user-attachments/assets/92806c42-b938-4009-9669-161591dc2eca" />

I can see from this check that there doesn't seem to be any duplicates of customer id, and that it is equal to the count of unique customer ids.

# Calculating Churn

It will be extremely useful to have a measure that calculates churn before deep-diving into the analysis. There is a column called Churn Label that indicates "Yes" or "No", but this column isn't the easiest to work with.

I'll convert this column to a binomial column indicating if the customer churned or not. I need to use that to calculate the churn rate.

The column Churn Label contains "Yes" and "No" values. I will use an IF() statement to convert the Churn Label column into a Churned column. It should contain a 1 if a customer churned, and 0 in case a customer didn't churn.

DAX

IF(<condition>, <value_if_true>, <value_if_false>)

DAX Used

Churn = IF'Databel - Data'[Churn Lable1] = "YES", 1, 0)

<img width="320" height="420" alt="Screenshot 2025-12-22 at 8 36 03 PM" src="https://github.com/user-attachments/assets/a2808c93-ccc5-4250-aef4-0f02f5171fd9" />

Next, I'll create a measure for Number of Churned Customers using the column I just created. 

Number of Churned Customers = SUM(Databel - Data'[Churn])

Creating a new visualization that will display the Churn Rate in a new page.

I'll calculate the Churn Rate and format it as a percentage by dividing the churned customers by the Number of Unique Customers

I make a new measure, 

Churn Rate = DIVIDE([Number of Churned Customers], [Number of Unique Customers] and making sure i set it as a percentage. 

Inserting a card and adding the churn rate shows this result, 26.86%

<img width="193" height="186" alt="Screenshot 2025-12-22 at 9 00 30 PM" src="https://github.com/user-attachments/assets/c014be82-d629-4fef-aa97-cd99ededa63a" />


# Investigating churn reasons

The logical next step is to investigate the different reasons why customers churned. 

I created a column chart listing the different reasons why customers churn in descending order.

<img width="1508" height="546" alt="Screenshot 2025-12-22 at 9 14 52 PM" src="https://github.com/user-attachments/assets/4875d313-a61d-4565-8c1e-3c9d18776584" />

 I can now clearly see the top 3 reasons for Customer Churn 

 - compteritors made better offer
 - compteritors had better devices
 - attitude of support person 



# Digging deeper into churn categories

I made a tree map with churn reasons to find out the top reason and display all churn categories in one clear visualization.

 I used the Churn Category column. ( ex. The "Extra data charges", "Price too high", and other price related reasons are grouped together in the "Price" category) 
 As the Category and Count of Churn Label as a percentage of the total to make the tree map. while also filtering the visual to show churn label "yes" 

 <img width="1325" height="592" alt="Screenshot 2025-12-22 at 9 33 32 PM" src="https://github.com/user-attachments/assets/91d9fc33-348e-42c4-ba02-9f20a59becd2" />

From this, I can clearly see that competitors are the main reason for churn.  


# Use maps to our advantage

Added Map Visual

Add Churn Rate, Number of Customers, and Number of Churned Customers to Tooltips in my visualization. And State to Location.

<img width="1329" height="563" alt="Screenshot 2025-12-22 at 9 39 23 PM" src="https://github.com/user-attachments/assets/9c286e47-ec54-442f-9381-7d4dc75712ec" />

Next, I'll add different color gradients so it's easy to spot states with a higher churn rate.

To do this i add a conditional format 

<img width="914" height="652" alt="Screenshot 2025-12-22 at 9 41 13 PM" src="https://github.com/user-attachments/assets/b8bb7389-fcbf-439e-b419-5c0d5b5e898c" />

<img width="894" height="502" alt="Screenshot 2025-12-22 at 9 41 58 PM" src="https://github.com/user-attachments/assets/9ff3517b-d6b3-41f8-bb23-7e1531d83975" />

From the map with Conditional Formating I can see that CA is the State with the highest churn rate, with 63%.



# Insights Discovered So Far

1. The churn rate for Databel is ~27%
2. ~45% of the reasons why customers churn are related to competitors.
3. The churn rate in California is abnormally high, >60%

# Demographic 

I will gather the categorized demographic variables related to age in a new column called Demographics

Using the IF() function to end up with a column with three categories: "Senior","Under 30", and "Other".

Demographics =
IF(
    'Databel - Data'[Senior] = "Yes",
    "Senior",
    IF(
        'Databel - Data'[Under 30] = "Yes",
        "Under 30",
        "Other"
    )
)

<img width="119" height="189" alt="Screenshot 2026-01-13 at 6 21 19 PM" src="https://github.com/user-attachments/assets/a4a82dc9-fa0d-4fb0-9b14-96fb7e29f812" />

This combines the age data under 30 and senior columns with the additional age data being represented as other into one column. 

I will now use a Stacked column chart to visualize the new column and a Table visual to see the values in rows.


<img width="434" height="559" alt="Screenshot 2026-01-13 at 6 32 17 PM" src="https://github.com/user-attachments/assets/232753ba-5612-4468-8d1a-4cb93f61f4f5" />


From the stacked bar chart, I can see that Seniors have the highest churn rate.

When I put the Churn Rate and Demographics into a Table visual, I can see that Seniors have a 38.46% Churn Rate, while others only have a 24.54%,  and Under 30 is 23%

<img width="149" height="78" alt="Screenshot 2026-01-13 at 6 39 23 PM" src="https://github.com/user-attachments/assets/b962a6d3-9658-479b-aeb9-08db6cec2980" />

This suggests that it might be a good idea to analyze the customer age in general.

# Age Bins

From Age I create a new group and make the bin size 5

<img width="743" height="500" alt="Screenshot 2026-01-13 at 6 57 10 PM" src="https://github.com/user-attachments/assets/90ca2c3a-3292-450e-a13a-cafe21cf7300" />

To visualize this data, I will create a line and stacked column chart.

Age(bins) on the Shared Axis, Number of Customers on the Column Value, and Churn Rate on the Line Value. 

I sorted in ascending order based on age for the chart.

<img width="555" height="559" alt="Screenshot 2026-01-13 at 7 07 01 PM" src="https://github.com/user-attachments/assets/b5b6ba99-2b9f-47b4-8bb6-4cca6b0d1d8c" />


# Inspecting Groups

Databel offers group contracts to customers from the same household. The advantage for the customer is a discounted rate.

I will analyze if customers that are part of a group indeed have a lower phone bill, and if it has an impact on the churn rate

I start by changing the format of the Monthly Charge to "Currency" in the Data view.

Then, I created a simple bar chart that plots the average Monthly Charge by Number of Customers in Group.


<img width="878" height="558" alt="Screenshot 2026-01-14 at 1 30 42 PM" src="https://github.com/user-attachments/assets/809e7991-5539-42f5-b986-4e4b79cf3925" />

I next add Group to the legend to display the difference in price clearly.


<img width="880" height="563" alt="Screenshot 2026-01-14 at 1 32 52 PM" src="https://github.com/user-attachments/assets/cc5a4450-60d8-4bdd-b141-58a4fe5feb84" />

Next, I converted the chart to a line and stacked column chart and added the Churn Rate.

<img width="881" height="557" alt="Screenshot 2026-01-14 at 1 34 46 PM" src="https://github.com/user-attachments/assets/50d5d1da-50c6-449d-b9cb-8a5de7a4db53" />

From the chart, I can clearly see that groups of 6 have the lowest churn rate.


# Contract Types

One Year, Two Year, and Month to Month. It would be a good idea to gather the values of yearly contracts into one. This way, I can observe the difference between the customers who have only yearly contracts and those having monthly contracts.


 The SWITCH() Function 

I will use the Switch Function to categorize the contract types and name it Contract Category.

DAX SWITCH(<expression>, <value>, <result>[, <value>, <result>]…[, <else>])

Contract Category =
SWITCH(
    'Databel - Data'[Contract Type],
    "One Year", "Yearly",
    "Two Year", "Yearly",
    "Month-to-Month", "Monthly",
    "Other"
)

To visualize this, I added a multi-Row Card, then used Contract Category and Chrun Rate in the Fields. 

<img width="197" height="144" alt="Screenshot 2026-01-14 at 2 55 35 PM" src="https://github.com/user-attachments/assets/e6f757b6-84cf-42bc-b04d-f59f555cd3ad" />

 from the data i can see that those who have monthly contracts churn more than the customers who have yearly-based contracts.
 
I am also interested in observing if gender plays a role in the churn rate.

I create a clustered column chart to see how customers differ in terms of churn rate by looking at their contract categories and gender.

<img width="469" height="409" alt="Screenshot 2026-01-14 at 3 01 57 PM" src="https://github.com/user-attachments/assets/9537341c-2917-478d-a767-70008ae52b50" />

From the chart, I can see that Females have the largest Churn Rate on the monthly contract category (our largest category) at 47.31%


# Unlimited plan

Databel has a hypothesis that people who are not on an unlimited data plan are more likely to churn.

My task is to investigate how the Unlimited Data Plan influences the churn rate.

I will create a Table visualization that displays the churn rate for customers who have the unlimited plan and for customers who don't.

I insert a table visual and add Unlimited Data Plan, Churn Rate, and Number of Customers to Values.

<img width="212" height="63" alt="Screenshot 2026-01-14 at 3 28 34 PM" src="https://github.com/user-attachments/assets/8d36c51e-0d9d-4a46-acf3-f0526729c093" />

It appears that customers who are on an unlimited plan are more likely to churn.

To see if it is related to a certain amount of mobile data (GB) being used, 
I create a new column Grouped Consumption that classifies the average monthly GB download in the following groups:

Less than 5 GB.

Between 5 and 10 GB.

10 or more GB.

DAX

Grouped Consumption =
IF (
    [Avg Monthly GB Download] < 5,
    "Less than 5 GB",
    IF (
        [Avg Monthly GB Download] < 10,
        "Between 5 and 10 GB",
        "10 or more GB"
    )
)

I then created a clustered bar chart to see Churn Rate by Unlimited Data Plan and by the different groups.

<img width="568" height="424" alt="Screenshot 2026-01-14 at 3 29 04 PM" src="https://github.com/user-attachments/assets/c178b493-e862-4305-aafa-1439ae5e83fe" />

<img width="558" height="257" alt="Screenshot 2026-01-14 at 3 29 53 PM" src="https://github.com/user-attachments/assets/6a8fdc6c-2af5-49fb-8fca-afed54d9fd0a" />

34.71% churn rate for people on an unlimited plan who consume less than 5 GB of data

#International Calls

The analysis requirement given by Databel includes a request to analyze the international activity of customers and its relationship to churn. 

They are curious about the behavior of customers who call internationally, and if paying for an international plan influences their loyalty.

I create a Matrix Visual, and add Intl Plan to Columns, Intl Active to Rows, and Churn Rate to Values.
Then change the font colors to visually differentiate high values from low values.

<img width="224" height="103" alt="Screenshot 2026-01-14 at 3 49 11 PM" src="https://github.com/user-attachments/assets/7ae02e9d-8d8d-4790-89bc-c0958eaf3780" />

Databel wants to focus on the budget on a state-by-state basis for the promotion of the international plan.

So I change the data category of State to "State or Province". Then, add a map visualization that displays the different churn rates of each state.

<img width="321" height="364" alt="Screenshot 2026-01-14 at 3 43 58 PM" src="https://github.com/user-attachments/assets/6532b6c0-f8a1-4e17-acb8-53a9c5ec4f3d" />

For the map visualization, I added State to the map as Location and Churn Rate as Bubble Size.

<img width="537" height="379" alt="Screenshot 2026-01-14 at 3 49 54 PM" src="https://github.com/user-attachments/assets/9280bc81-7fc1-4bec-83b9-80a344787d02" />

<img width="771" height="423" alt="Screenshot 2026-01-14 at 3 54 55 PM" src="https://github.com/user-attachments/assets/eaf1f4db-b73d-4f76-b4fb-f2392131e8e5" />

In California, 72% of customers who actively make international calls but don't have an international plan churn.

# Contract type

Databel also wants to improve its customer service since there have been some reported issues.

I will investigate three important topics related to customers: the payment method, the contract type, and how many months a person is a customer.

To start, I will evaluate if the churn rate decreases over time. 
Creating a Line chart using the account length and churn rate

<img width="528" height="433" alt="Screenshot 2026-01-14 at 4 02 03 PM" src="https://github.com/user-attachments/assets/c89b5aca-7470-4b38-87ad-5dc43509127f" />

It seems the churn rate does decrease over time. Now, I'll investigate how this decrease behaves through the different types of contracts

<img width="776" height="608" alt="Screenshot 2026-01-14 at 4 04 18 PM" src="https://github.com/user-attachments/assets/eaaecef6-500e-4b0d-8ede-ff4e47e828e9" />

Next, I'll make another visualization to see the number of customers by the types of payment.

Ill use use a Pie chart visualization, adding the number of Costumers and Payment Method

<img width="379" height="231" alt="Screenshot 2026-01-14 at 4 07 29 PM" src="https://github.com/user-attachments/assets/1b7a60dd-1226-42e7-9c6b-1e48d32157d7" />

<img width="771" height="434" alt="Screenshot 2026-01-14 at 4 08 25 PM" src="https://github.com/user-attachments/assets/d3f55a43-f475-4c70-9cef-903fe3597096" />

I can see that from the payment types, direct debit is the most common, and paper checks are the least common. 


# Overview Page

First I'll need to add visualizations that can easily show total customers, churned customers and churn rate.

I'll copy the three visuals I made earlier from the Churned Demographics.

<img width="429" height="242" alt="Screenshot 2026-01-14 at 4 21 30 PM" src="https://github.com/user-attachments/assets/0189333f-7cf7-4f83-ad6b-1f4def20e85c" />

It is very important to see the reasons why customers churn, and their respective percentage of total customers. so ill also copy in the visualization that displays the different churn reasons and the percentage of total number of customers in each group.

<img width="399" height="425" alt="Screenshot 2026-01-14 at 4 26 59 PM" src="https://github.com/user-attachments/assets/5e80767b-8a1c-4734-8da0-83a090ca6489" />


In previous analysis I saw that contract type and churn category have a big impact on churn, so i will also add this to the overview.

<img width="274" height="356" alt="Screenshot 2026-01-14 at 4 30 10 PM" src="https://github.com/user-attachments/assets/943ba4e8-0662-4834-9bd6-72f5fd08631f" />


Finally, knowing the state was also important for the new Databel international service promotion, so I will also add that to the overview. 

<img width="946" height="553" alt="Screenshot 2026-01-14 at 4 34 08 PM" src="https://github.com/user-attachments/assets/43ab0520-e640-4cde-b9a7-22cc807046b3" />


# Second Page

Next, I'll create a second page in my report that portrays insights about the age buckets and groups

I'll start by adding a graph that has Age (bins). 

<img width="473" height="268" alt="Screenshot 2026-01-14 at 4 39 30 PM" src="https://github.com/user-attachments/assets/25d8d71e-4fb6-4ebe-8b83-6d03d9f4bf13" />


Then add a visualization that shows the average monthly charge and churn rate, for group plans.

<img width="402" height="259" alt="Screenshot 2026-01-14 at 4 44 04 PM" src="https://github.com/user-attachments/assets/a62fc681-4e7b-47dd-8bb1-7f31be447645" />

and added a slicer filter using account length in months 

<img width="437" height="103" alt="Screenshot 2026-01-14 at 4 44 30 PM" src="https://github.com/user-attachments/assets/fe57563d-a470-4026-8dee-2119644e07a6" />


<img width="937" height="527" alt="Screenshot 2026-01-14 at 4 47 55 PM" src="https://github.com/user-attachments/assets/398da029-4733-4dd8-979f-b351e3ec58ac" />

For this page, I was asked a question. 

What's the churn rate (in %) for customers who are not in a group plan, that belong to the age group 50, and who have an account length of 12 months or less? 

By using the interactive page, I was able to filter and find the answer.

<img width="947" height="526" alt="Screenshot 2026-01-14 at 4 51 14 PM" src="https://github.com/user-attachments/assets/1a37e0a4-90c1-4a64-8d34-dff7d86f453d" />

50.67%


# Payment method and contract category

While going through all the columns in the table, I notice I haven't investigated customer service calls.
My task is to create a report with a scatter plot visual showing the contract and payment type, and combine it with two card visuals related to customer service calls.


I'll create a new measure that calculates the average customer service calls per customer, and name my new measure Avg Customer Service Calls.

DAX

Avg Customer Service Calls =
DIVIDE(
    SUM([Customer Service Calls]),
    DISTINCTCOUNT([Customer ID])

In a new page I'll create two new card visualization, one with the total customer service calls, and one with the average customer service calls.

<img width="196" height="382" alt="Screenshot 2026-01-14 at 5 04 17 PM" src="https://github.com/user-attachments/assets/cdbf9e12-5d9b-49f5-888a-9e80c38c03d1" />

Next, I'll copy the line chart (Churn rate by Account Length) and turn it into a scatter plot using the average account length. Also adding contract category and payment method to see the churn rates for each category visually.

I'll add Payment Method to Values, average of Account Length to X Axis, Churn Rate to Y Axis, and Contract Category to Legend

And add a slicer to filter the contract category and the payment method.

<img width="688" height="379" alt="Screenshot 2026-01-14 at 5 20 14 PM" src="https://github.com/user-attachments/assets/f27e499a-7bb6-49d5-8389-1e85268b71ad" />

And now, by filtering, we can find specific answers.
Like, what's the average number of customer service calls for customers who are on a monthly contract and pay by direct debit?

1.47 Avg Customer Service Calls with a Churn Rate of 53.90%

<img width="849" height="390" alt="Screenshot 2026-01-14 at 5 21 35 PM" src="https://github.com/user-attachments/assets/e73bf9de-3229-410e-af41-1e24fdbd177b" />



# International and data plan

I'll copy the bar chart I made in the Unlimited Plan page and create two new measures called Avg Extra International Charges and Avg Extra Data Charges.

<img width="740" height="522" alt="Screenshot 2026-01-14 at 5 28 22 PM" src="https://github.com/user-attachments/assets/1fdb1e4d-f15b-4e2f-ad18-aaff8c1a602b" />

Now work on the two new measures.

Dax 

Avg Extra International Charges =
DIVIDE(
    SUM([Extra International Charges]),
    DISTINCTCOUNT([Customer ID]))

And

Dax

Avg Extra Data Charges =
DIVIDE(
    SUM([Extra Data Charges]),
    DISTINCTCOUNT([Customer ID]))

 Now I'll add two cards to your canvas showing the average extra international charges and the average extra data charges.

 <img width="675" height="368" alt="Screenshot 2026-01-14 at 5 39 42 PM" src="https://github.com/user-attachments/assets/3855ef76-3fef-4ee8-b406-27aba882ed16" />

From this, we were asked the question,

What are the average extra data charges for customers who are not on an unlimited data plan and consumed 10 or more gigabytes?

By filtering the chart, the answer was easy to find. 

<img width="675" height="351" alt="Screenshot 2026-01-14 at 5 41 14 PM" src="https://github.com/user-attachments/assets/855ca2e4-07a7-46f5-aba5-eb37b04b7c75" />

31.19



# More insights

Next, I'll check the characteristics of the states regarding the customer service calls and the churn label.


I copied all the card visuals from the Payment and contract and Extra Charges pages and pasted them to a new page.

Then created a Line chart to show the Churn Label and the Avg Customer Service Calls from my data model.

<img width="261" height="375" alt="Screenshot 2026-01-14 at 5 52 55 PM" src="https://github.com/user-attachments/assets/40aaa678-7ab9-436f-ad50-f1f940d298dd" />

Now that I know the general overview I'll elaborate more by adding State to my visualization.

This way, I will be able to observe the average customer service calls for each US state, grouped by churn label.

<img width="671" height="370" alt="Screenshot 2026-01-14 at 5 56 32 PM" src="https://github.com/user-attachments/assets/f4aaa0d6-76ed-477a-bc61-a60610626a4b" />

Next ill copy the map visual from International Calls and change the coloring of the bubbles according to the sum of customer service calls.

<img width="835" height="461" alt="Screenshot 2026-01-14 at 6 03 15 PM" src="https://github.com/user-attachments/assets/8dfd5c76-8ba4-46d6-bc91-c0dcded06646" />

With this,we can answer the following question. 

Which state has the highest churn rate and yet the lowest sum of customer service calls? And what is the number of total customer service calls for that state?

CA with 44 service calls.
