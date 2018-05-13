Heroes of Py Moli Analysis


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