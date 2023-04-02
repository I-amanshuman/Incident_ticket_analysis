# IT firm Incident_ticket_analysis

In this project, we had to understand ticket trends of an IT company and forecast staffing/resource requirements, 
based on factors like monthly tickets created, tickets closed, product failures, shift patterns, product usage, etc. We use Python 
for ETL, pre-processing, EDA, and finally use ML algorithms to train the model, to achieve the business requirements

-- Loading the dataset --
![image](https://user-images.githubusercontent.com/77953290/229372013-cd73dc02-4a27-4876-9537-f977e87ca26c.png)
The raw data consisted of 2096908 rows and 41 columns





-- Exploratory Data Analysis --




As a requirement we started doing exploratory data analysis of the data, identifying null values, count of variables, data operations: like extracting, date, month etc

Created a new columns 'Priority' and used it with aggregate function to get the count of product issues by priority

![image](https://user-images.githubusercontent.com/77953290/229372202-d8dfb6c4-c379-45c4-b476-0687fb5b67ae.png)

-- Creating a checkpoint -- Copying the dataframe --

Continuing with the EDA, we are also looking at the shift in which the ticket was created. This will help us to identify the team efficiency in the long run, and thus help with the staffing requirements

![image](https://user-images.githubusercontent.com/77953290/229372389-c8910614-4871-4173-a083-6d645d2d2ece.png)

Creating three shifts, based on the hour of day, S1, S2, S3, with 8 hour each

Extracted quarter info from both the tickets created and tickets closed, and merged it with the year. This will help us in plotting the graphs, since day level data will be very granular and we might loose certain insights

Saving the dataset as out pre-processed dataset, before we move ahead with the further exploration

-- Loading the final dataset --
![image](https://user-images.githubusercontent.com/77953290/229372668-b1b4ea3e-5037-457c-b095-6a205ff08786.png)

Now, we use this data to get some information about the spread of data:
a. Tickets raised every quarter

![image](https://user-images.githubusercontent.com/77953290/229372720-d5851439-315e-485a-8336-ff6806fb2ad7.png)

b. Plotted the graphy of ticket distribution over years, based on monthly data 

![image](https://user-images.githubusercontent.com/77953290/229372797-7614bec9-f92e-41e8-a33c-90aaaa76a709.png)

c. Plotting bar charts for tickets raised with respect to each product

Defining a function, to print bar plots of count of tickets with respect to a product per month

def bar_plot(df, x, y, title, xlabel, ylabel,dpi=100):
    plt.figure(figsize=(20,20),dpi=dpi)
    df.plot.bar(x,y)
    plt.gca().set(title=title,xlabel=xlabel,ylabel=ylabel)
    plt.show()
print("P0 Incident Tickets w.r.t each product -")
print("")
for i in df_count_P0['product'].unique():
    print(i)
    bar_plot(df_count_P0[df_count_P0['product'] == i],x="created_mmm_yy",y="created_number",title="Tickets w.r.t Month",xlabel="Month-Year",ylabel="Ticket Count")

![image](https://user-images.githubusercontent.com/77953290/229372988-852e1dab-4195-4b24-bf4c-d9e781cc44ab.png)

-Similary we find the relationship with P2 level requests and P3 level

After plotting the necessary graphs to understand the behavior of raising tickets,
We created two new dataframes, w.r.t created dates and closed dates

df_create = df_copy.groupby(['created_mmm_yy','product','shift','priority']).agg({'index':'count','assigned_to':pd.Series.nunique}).reset_index()
df_create.rename(columns = {'created_mmm_yy':'date'}, inplace = True)
df_create

df_close = df_copy.groupby(['closed_mmm_yy','product','shift','priority']).agg({'index':'count'}).reset_index()
df_close.rename(columns = {'closed_mmm_yy':'date'}, inplace = True)
df_close

![image](https://user-images.githubusercontent.com/77953290/229373512-78ea694e-0230-4b05-bf14-c3927af4949f.png)

Then we merge the two dataframes using Outer Join

Checked the new data for null values

![image](https://user-images.githubusercontent.com/77953290/229373558-d6d481c1-6e1b-448f-8564-c574fce7637d.png)

Replaced the null values with 0,  with 0 as there were no tickets created for the respective combination of Date, Product, Shift and Priority

Null values of closed_count are also replaced with 0 as there were no tickets closed for the respective combination of Date, Product, Shift and Priority




--- Modelling ---





For business purposes, we were only required to look after last one year of data: that is from : June 2021 to June 2022

df_2021 = df_final[df_final['date'] >= '2021-06']
df_2021

- Using pair plots to understand the behaviour of the features with the target variable

![image](https://user-images.githubusercontent.com/77953290/229373759-21e05bd9-dda3-46d3-8d35-351dc384fc7f.png)


- We used to line plots with each of created count, closed count, and assigned to count for the last 1 year, to understand the data 

- Correlation analysis between the variables, using heatmap from seaborn library

![image](https://user-images.githubusercontent.com/77953290/229374300-d12e7066-f5e7-485e-b162-69909f8713a1.png)


The data is now split into train and test using traintestsplit from scikit learn package, which will be used to train the data and then predict the create_count numbers, which will help us in forecasting the count of tickets

- One hot encoding of the categorical variables in the train dataset (manual encoding)

- Training the model and computing regression coefficients

![image](https://user-images.githubusercontent.com/77953290/229374446-384bc8c9-4ee3-4b8d-920e-7c9e17913787.png)


- Computing R2 score: To understand the variance explained by the model
![image](https://user-images.githubusercontent.com/77953290/229374479-b443ed1a-263b-490e-9fa0-98984ef4c252.png)


-- After linear regression, we compared the actual values with the predicted values from the regression model

-- MAE ~ 6.78

![image](https://user-images.githubusercontent.com/77953290/229374745-9c7d6875-033b-4a51-b515-5c12c547f1aa.png)

-- Thank You -- 



