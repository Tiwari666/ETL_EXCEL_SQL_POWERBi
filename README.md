# Building a Data Analysis Project:
A) Create a Database-Extracting data from the excel to SQL

B) Combining the Data from the three different tables--Transform--This merging of data from different sources transforms the separate datasets into a unified view.

i) Query and analyze data with SQL

C) Integrate Power BI with a Database--Loading data from one 

D) Create Data Visualizations Using Power BI --Business Intelligence



# A) Create a Database-Extracting data from the excel to SQL:

# Steps:

i) Open SQL Server Management Studio

ii) Copy the Server Name for later use, then click on Connect to get the SSMS window.

iii) Right click on "Databases" and select "New Database" from the drop down.

iv) Assign a Database Name in New Database window and click OK.

v) Expand the Databases to verify that a new database is created.

vi) In the Databases panel, right-click on the newly created database and select "Import Data" from "Task" drop down list.

vii) In the Import Data dialog box, select the data source that you want to import from.

viii) Browse data file or enter path.

ix) Choose the excel version you want to use and click Next.

x) Select a destination and verify the server and database name-write down the same new database name here.

xi) Select tables that you want to import.

Note: if you get the following error, please folllow the following video to fix it:

"The 'Microsoft.ACE.OLEDB.16.0' provider is not registered on the local machine" Error in importing process of xlsx to a sql server."

Link: https://www.youtube.com/watch?v=gjykOuT_xwQ

xii) Click on "Run immediately" to import  all the data  into the environment.

xiii) We can now view the data in the new database by expanding the Tables node in the Databases panel.

# B)  Combining the Data from the three different tables

To combine the data from the three tables, let's simply use the UNION operator among these commands.

select * from dbo.['2018$']
union
select * from dbo.['2019$']
union
select * from dbo.['2020$']


# EDA IN SQL---------------------------------------------------------

