Heroes of Py Moli Analysis
Males make up the majority (81.2% of our sample size)
No one has spent over $20 on a single purchase
Though the 25-29 year olds purchase pricier items, the 20-24 year olds generate the most revenue.
The most popular items are all priced below the average purchase price.

#dependencies
import os
import pandas as pd
import numpy as np

#load in data
file = os.path.join('purchase_data.json')
pur_df = pd.read_json(file)
pur_df.head()

#show total number of players only
total_players = len(pur_data['SN'].unique())
total_players_df = pd.DataFrame([{'Total Players': total_players}])
total_players_df.set_index('Total Players', inplace = True)
total_players_df

#purcahse analysis (total)

#number of unique items
no_multiples = pur_df.drop_duplicates(['Item ID'], keep = 'last')
total_unique = len(no_multiples)

#total number of purchases
total_pur = pur_df['Price'].count

#total revenue
total_rev = round(pur_df['Price'].sum(),2)

#average purchase price (total revenue/total purchases)
avg_price = round(total_rev/total_pur(), 2)


#create data frame
pur_analysis = pd.DataFrame([{
    
    "Number of Unique Items": total_unique,
    'Average Purchase Price': avg_price,
    'Total Purchases': total_pur,
    'Total Revenue': total_rev}])

#check data frame
print(pur_analysis)

#format purchase analysis (total) data
pur_analysis.style.format({'Average Purchase Price': '${:.2f}', 'Total Revenue': '${:,.2f}'})




#Gender Demographics
# Percentage and count of players 
no_dup_players = pur_df.drop_duplicates(['SN'], keep ='last')
gender_count = no_dup_players['Gender'].value_counts().reset_index()
gender_count['% of Players'] = gender_count['Gender']/total_players * 100
#rename columns
gender_count.rename(columns = {'index': 'Gender', 'Gender': '# of Players'}, inplace = True)
#fix index
gender_count.set_index(['Gender'], inplace = True)
gender_count.style.format({"% of Players": "{:.1f}%"})

#Purchasing Analysis (Gender)
#purchase count by gender
pur_count_gen = pd.DataFrame(pur_df.groupby('Gender')['Gender'].count())

#Total Purchase Value
total_pur_val_gen = pd.DataFrame(pur_df.groupby('Gender')['Price'].sum())
#merge purchase count and total value
pur_analysis_gen = pd.merge(pur_count_gen, total_pur_val_gen, left_index = True, right_index = True)
#rename columns
pur_analysis_gen.rename(columns = {'Gender': '# of Purchases', 'Price':'Total Purchase Value'}, inplace=True)
#new column - average purchase price
pur_analysis_gen['Average Purchase Price'] = pur_analysis_gen['Total Purchase Value']/pur_analysis_gen['# of Purchases']
#add gender count
pur_analysis_gen = pur_analysis_gen.merge(gender_count, left_index = True, right_index = True)
#normalized totals (total purchase value/unique # of players by gender)
pur_analysis_gen['Normalized Totals'] = pur_analysis_gen['Total Purchase Value']/pur_analysis_gen['# of Players']
pur_analysis_gen
#remove number/percent of players. we don't need those columns.
del pur_analysis_gen['% of Players']
del pur_analysis_gen['# of Players']
pur_analysis_gen
#fix formatting
pur_analysis_gen.style.format({'Total Purchase Value': '${:.2f}', 'Average Purchase Price': '${:.2f}', 'Normalized Totals': '${:.2f}'})

#age demographics
#create rows based on age ranges
pur_df.loc[(pur_df['Age'] < 10), 'age_bin'] = "< 10"
pur_df.loc[(pur_df['Age'] >= 10) & (pur_df['Age'] <= 14), 'age_bin'] = "10 - 14"
pur_df.loc[(pur_df['Age'] >= 15) & (pur_df['Age'] <= 19), 'age_bin'] = "15 - 19"
pur_df.loc[(pur_df['Age'] >= 20) & (pur_df['Age'] <= 24), 'age_bin'] = "20 - 24"
pur_df.loc[(pur_df['Age'] >= 25) & (pur_df['Age'] <= 29), 'age_bin'] = "25 - 29"
pur_df.loc[(pur_df['Age'] >= 30) & (pur_df['Age'] <= 34), 'age_bin'] = "30 - 34"
pur_df.loc[(pur_df['Age'] >= 35) & (pur_df['Age'] <= 39), 'age_bin'] = "35 - 39"
pur_df.loc[(pur_df['Age'] >= 40), 'age_bin'] = "> 40"

#pur_data[['age_bin', 'Age']].count() to check
# count purchases by age bin - usingscreen names (non-unique)
pur_count_age = pd.DataFrame(pur_df.groupby('age_bin')['SN'].count())
#find averageg price of purchases by age bin
avg_price_age = pd.DataFrame(pur_df.groupby('age_bin')['Price'].mean())
#finds total purchase value by age bin
tot_pur_age = pd.DataFrame(pur_df.groupby('age_bin')['Price'].sum())
#delete duplicate screen names while only keeping last, then counts # of unique players by age bin
no_dup_age = pd.DataFrame(pur_df.drop_duplicates('SN', keep = 'last').groupby('age_bin')['SN'].count())
#merge
merge_age = pd.merge(pur_count_age, avg_price_age, left_index = True, right_index = True).merge(tot_pur_age, left_index = True, right_index = True).merge(no_dup_age, left_index = True, right_index = True)
#renames column
merge_age.rename(columns = {"SN_x": "# of Purchases", "Price_x": "Average Purchase Price", "Price_y": "Total Purchase Value", "SN_y": "# of Purchasers"}, inplace = True)

#calculates normalized totals
merge_age['Normalized Totals'] = merge_age['Total Purchase Value']/merge_age['# of Purchasers']
#fix index and format
merge_age.index.rename("Age", inplace = True)
merge_age.style.format({'Average Purchase Price': '${:.2f}', 'Total Purchase Value': '${:.2f}', 'Normalized Totals': '${:.2f}'})


#top spenders
#Identify the the top 5 spenders in the game by total purchase value, then list (in a table):
#Group by sn to find, total purchase per person, number of purchases per person, and average price price per person
purchase_amt_by_SN = pd.DataFrame(pur_df.groupby('SN')['Price'].sum())
num_purchase_by_SN = pd.DataFrame(pur_df.groupby('SN')['Price'].count())
avg_purchase_by_SN = pd.DataFrame(pur_df.groupby('SN')['Price'].mean())

#merge df's
merged_top5 = pd.merge(purchase_amt_by_SN, num_purchase_by_SN, left_index = True, right_index = True).merge(avg_purchase_by_SN, left_index=True, right_index=True)
# rename columns
merged_top5.rename(columns = {'Price_x': 'Total Purchase Value', 'Price_y':'Purchase Count', 'Price':'Average Purchase Price'}, inplace = True)
#sort highest to lowest purchase value
merged_top5.sort_values('Total Purchase Value', ascending = False, inplace=True)
#fix formatting
merged_top5 = merged_top5.head()
merged_top5.style.format({'Total Purchase Value': '${:.2f}', 'Average Purchase Price': '${:.2f}'})

#Most Popular Items
#Identify the 5 most popular items by purchase count, then list (in a table)
# get a count of each item by grouping by Item ID and number of occurances
top5_items_ID = pd.DataFrame(pur_df.groupby('Item ID')['Item ID'].count())
#sort from high to low total purchase count
top5_items_ID.sort_values('Item ID', ascending = False, inplace = True)
#keep the first 5 rows
top5_items_ID = top5_items_ID.iloc[0:5][:]
#top5_items_ID
#total purchase value of each item
top5_items_total = pd.DataFrame(pur_df.groupby('Item ID')['Price'].sum())
#top5_items_ID
#merge purcahse count with total purcahse value and drop dups
top5_items = pd.merge(top5_items_ID, top5_items_total, left_index = True, right_index = True)
no_dup_items = pur_df.drop_duplicates(['Item ID'], keep = 'last')
#top5_items

# merge to get all other info from the top 5 using the no dup df
top5_merge_ID = pd.merge(top5_items, no_dup_items, left_index = True, right_on = 'Item ID')
top5_items_ID = top5_merge_ID[['Item ID', 'Item Name', 'Item ID_x', 'Price_y', 'Price_x']]
top5_merge_ID.set_index(['Item ID'], inplace = True)
top5_merge_ID.rename(columns =  {'Item ID_x': 'Purchase Count', 'Price_y': 'Item Price', 'Price_x': 'Total Purchase Value'}, inplace=True)
top5_merge_ID.style.format({'Item Price': '${:.2f}', 'Total Purchase Value': '${:.2f}'})

#Most Profitable Items - Identify the 5 most profitable items by total purchase value, then list (in a table)
top_5_profit = pd.DataFrame(pur_df.groupby('Item ID')['Price'].sum())
top_5_profit.sort_values('Price', ascending = False, inplace = True)
top_5_profit = top_5_profit.iloc[0:5][:]
pur_count_profit = pd.DataFrame(pur_df.groupby('Item ID')['Item ID'].count())

top_5_profit = pd.merge(top_5_profit, pur_count_profit, left_index = True, right_index = True, how = 'left')
top_5_merge_profit = pd.merge(top_5_profit, no_dup_items, left_index = True, right_on = 'Item ID', how = 'left')
top_5_merge_profit = top_5_merge_profit[['Item ID', 'Item Name', 'Item ID_x', 'Price_y','Price_x']]
top_5_merge_profit.set_index(['Item ID'], inplace=True)
top_5_merge_profit.rename(columns = {'Item ID_x': 'Purchase Count', 'Price_y': 'Item Price', 'Price_x': 'Total Purchase Value'}, inplace = True)
top_5_merge_profit.style.format({'Item Price': '${:.2f}', 'Total Purchase Value': '${:.2f}'})
