3 points:
1 - More than 80% of players male
2 - Though males do spend more (because there are more obviously), females spend more on average
3 - 14-26 year olds spent the most, but 34-38 year olds spent the most on average
4 - Jiskjask76 is the closest thing this game has to a whale
5 - Items with dramatic sounding names seem to be more popular





```python
# Dependencies
import pandas as pd
import numpy as np
```


```python
# Import files using pandas
items_df = pd.read_csv("generated_data\items_complete.csv")
players_df = pd.read_csv("generated_data\players_complete.csv")
purchase_df = pd.read_csv("generated_data\purchase_data_3.csv")
```


```python
# Get the total number of players with .count(), using SN column b/c its short
player_count = players_df["SN"].count()

# Output as a table
player_count_table = pd.DataFrame([player_count], columns=["Total Players"])
player_count_table
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1163</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Get the total count of unique items with len() since .unique() returns a list
unique_item_count = len(items_df["Item Name"].unique())

# Get the average purchase price by dividing the sum of the price column by total number of items
average_purchase_price = purchase_df["Price"].sum() / len(purchase_df["Price"])

# Get the total number of purchases
total_purchases = purchase_df["Price"].count()

# Get total revenue by adding up the revenue from the purchase_df
total_revenue = purchase_df["Price"].sum()

# Output as a table
purchase_analysis_table = pd.DataFrame([unique_item_count], columns=["Number of Unique Items"])
purchase_analysis_table["Average Price"] = "${:.2f}".format(average_purchase_price)
purchase_analysis_table["Number of Purchases"] = total_purchases
purchase_analysis_table["Total Revenue"] = "${:.2f}".format(total_revenue)
purchase_analysis_table
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>186</td>
      <td>$2.92</td>
      <td>78</td>
      <td>$228.10</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Get % and # of male players
number_male = len(players_df.loc[players_df["Gender"] == "Male",:])

percent_male = (number_male / player_count)
percent_male = "{:.2%}".format(percent_male)

# Get % and # of female players
number_female = len(players_df.loc[players_df["Gender"] == "Female",:])

percent_female = (number_female / player_count)
percent_female = "{:.2%}".format(percent_female)

# Get % and # of other / non-disclosed players
number_other = len(players_df.loc[players_df["Gender"] == "Other / Non-Disclosed",:])

percent_other = (number_other / player_count)
percent_other = "{:.2%}".format(percent_other)

# Output as a table
index = ["Male","Female","Other / Non-Disclosed"]
columns = ["Percentage of Players", "Total Count"]
gender_demographics_table = pd.DataFrame(index=index, columns=columns)
gender_demographics_table["Percentage of Players"] = [percent_male, percent_female, percent_other]
gender_demographics_table["Total Count"] = [number_male, number_female, number_other]
gender_demographics_table
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>82.03%</td>
      <td>954</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>16.08%</td>
      <td>187</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.89%</td>
      <td>22</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Set up some dataframes etc to use for purchase analysis
male_df = purchase_df.loc[purchase_df["Gender"] == "Male",:]
female_df = purchase_df.loc[purchase_df["Gender"] == "Female",:]
other_df = purchase_df.loc[purchase_df["Gender"] == "Other / Non-Disclosed",:]
```


```python
# Get purchase count broken by gender
male_purchase = len(male_df)
female_purchase = len(female_df)
other_purchase = len(other_df)

# Get total purchase value broken by gender
male_total_value = male_df["Price"].sum()
female_total_value = female_df["Price"].sum()
other_total_value = other_df["Price"].sum()

# Get average purchase price broken by gender
male_average_price = male_total_value / male_purchase
female_average_price = female_total_value / female_purchase
other_average_price = other_total_value / other_purchase

# Get normalized totals broken by gender, with code repurposed from stackoverflow
male_purchase_normalized = male_df["Price"].sum() / total_revenue

female_purchase_normalized = female_df["Price"].sum() / total_revenue

other_purchase_normalized = other_df["Price"].sum() / total_revenue

# Output as a table
index = ["Male", "Female", "Other / Non-Disclosed"]
columns = ["Purchase Count", "Average Purchase Price", "Total Purchase Value", "Normalized Totals"]
purchasing_analysis_table = pd.DataFrame(index=index, columns=columns)
purchasing_analysis_table["Purchase Count"] = [male_purchase, female_purchase, other_purchase]
purchasing_analysis_table["Average Purchase Price"] = [male_average_price, female_average_price, other_average_price]
purchasing_analysis_table["Total Purchase Value"] = [male_total_value, female_total_value, other_total_value]
purchasing_analysis_table["Normalized Totals"] = [male_purchase_normalized, female_purchase_normalized, other_purchase_normalized]

# Formatting to look nice
purchasing_analysis_table["Average Purchase Price"] = purchasing_analysis_table["Average Purchase Price"].map("${:.2f}".format)
purchasing_analysis_table["Total Purchase Value"] = purchasing_analysis_table["Total Purchase Value"].map("${:.2f}".format)
purchasing_analysis_table["Normalized Totals"] = purchasing_analysis_table["Normalized Totals"].map("${:.2f}".format)
purchasing_analysis_table
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>64</td>
      <td>$2.88</td>
      <td>$184.60</td>
      <td>$0.81</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>13</td>
      <td>$3.18</td>
      <td>$41.38</td>
      <td>$0.18</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1</td>
      <td>$2.12</td>
      <td>$2.12</td>
      <td>$0.01</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Prepare to analyze age demographics
# first join purchase and players tables and clean it
players_purchase_joined_df = pd.merge(purchase_df, players_df, on="SN")
players_purchase_joined_df = players_purchase_joined_df.rename(columns={"Age_x": "Age",
                                                                       "Gender_x": "Gender"})
players_purchase_joined_clean_df = players_purchase_joined_df[["Purchase ID",
                                                             "SN",
                                                             "Age",
                                                             "Gender",
                                                             "Item ID",
                                                             "Item Name",
                                                             "Price",
                                                             "Player ID"]]
players_purchase_joined_clean_df.head(1)

# make bins and group names
bins = [0, 10, 14, 18, 22, 26, 30, 34, 38, 200]
bin_names = ["< 10", "10-14", "14-18", "18-22", "22-26", "26-30", "30-34", "34-38", "38+"]
players_purchase_joined_clean_df["Age Groups"] = pd.cut(players_purchase_joined_clean_df["Age"], bins, labels=bin_names)
players_purchase_joined_clean_df.head()

# Create a dataframe grouped by age groups
players_purchase_groups = players_purchase_joined_clean_df.groupby("Age Groups")
```

    c:\users\t0nb3\Anaconda3\envs\PythonData\lib\site-packages\ipykernel\__main__.py:19: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    


```python
# Get purchase count by age groups
purchase_count_age_groups = players_purchase_groups["Purchase ID"].count()

# Get average purchase price by age groups
average_purchase_age_groups = players_purchase_groups["Price"].mean()

# Get total purchase value by age groups

total_purchase_age_groups = players_purchase_groups["Price"].sum()

# Get normalized totals by age group apparently just sum / total sum?
normalized_purchase_age_groups = players_purchase_groups["Price"].sum() / total_revenue

# Output to a table
age_demographics_table = pd.DataFrame(purchase_count_age_groups)
age_demographics_table = age_demographics_table.rename(columns={"Purchase ID": "Purchase Count"})
age_demographics_table["Average Purchase Price"] = average_purchase_age_groups.map("${:.2f}".format)
age_demographics_table["Total Purchase Value"] = total_purchase_age_groups.map("${:.2f}".format)
age_demographics_table["Normalized Totals"] = normalized_purchase_age_groups.map("${:.2f}".format)
age_demographics_table
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Groups</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt; 10</th>
      <td>5</td>
      <td>$2.76</td>
      <td>$13.82</td>
      <td>$0.06</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>3</td>
      <td>$2.99</td>
      <td>$8.96</td>
      <td>$0.04</td>
    </tr>
    <tr>
      <th>14-18</th>
      <td>11</td>
      <td>$2.76</td>
      <td>$30.41</td>
      <td>$0.13</td>
    </tr>
    <tr>
      <th>18-22</th>
      <td>21</td>
      <td>$3.09</td>
      <td>$64.93</td>
      <td>$0.28</td>
    </tr>
    <tr>
      <th>22-26</th>
      <td>23</td>
      <td>$2.94</td>
      <td>$67.61</td>
      <td>$0.30</td>
    </tr>
    <tr>
      <th>26-30</th>
      <td>4</td>
      <td>$2.69</td>
      <td>$10.77</td>
      <td>$0.05</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>5</td>
      <td>$2.03</td>
      <td>$10.17</td>
      <td>$0.04</td>
    </tr>
    <tr>
      <th>34-38</th>
      <td>5</td>
      <td>$3.74</td>
      <td>$18.72</td>
      <td>$0.08</td>
    </tr>
    <tr>
      <th>38+</th>
      <td>2</td>
      <td>$3.65</td>
      <td>$7.30</td>
      <td>$0.03</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Prepare to analyze top spenders
# take cleaned table from above and group by SN to find top spenders SNs
players_purchase_sn_groups = players_purchase_joined_clean_df.groupby("SN")

sorted_top_spender_SN = pd.DataFrame(players_purchase_sn_groups["Price"].sum()).sort_values(["Price"], ascending=False)
top_five_spender_SN = sorted_top_spender_SN.head(5)
```


```python
# get top 5 spender purchase count
SN_purchase_count = pd.DataFrame(players_purchase_sn_groups["SN"].count())
top_five_count = SN_purchase_count.loc[top_five_spender_SN.index.tolist()]
top_five_count = top_five_count.rename(columns={"SN":"Purchase Count"})

# get top 5 spender total purchase value
SN_purchase_price = pd.DataFrame(players_purchase_sn_groups["Price"].sum())
top_five_total = SN_purchase_price.loc[top_five_spender_SN.index.tolist()]

# get top 5 spender average purchase price
# have to first convert column to numeric data
top_five_counts = pd.to_numeric(top_five_count["Purchase Count"])
top_five_totals = pd.to_numeric(top_five_total["Price"])
top_five_average = top_five_totals / top_five_counts

# Spender Report
spender_report = top_five_spender_SN.rename(columns={"Price":"Total Price"})
spender_report["Purchase Count"] = top_five_count
spender_report["Average Purchase Price"] = top_five_average.map("${:.2f}".format)
spender_report["Total Purchase Value"] = top_five_total
spender_report = spender_report[["Purchase Count", "Average Purchase Price", "Total Purchase Value"]]
spender_report["Total Purchase Value"] = spender_report["Total Purchase Value"].map("${:.2f}".format)
spender_report
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Jiskjask76</th>
      <td>2</td>
      <td>$4.59</td>
      <td>$9.18</td>
    </tr>
    <tr>
      <th>Sundaky74</th>
      <td>2</td>
      <td>$3.71</td>
      <td>$7.41</td>
    </tr>
    <tr>
      <th>Aidaira26</th>
      <td>2</td>
      <td>$2.56</td>
      <td>$5.13</td>
    </tr>
    <tr>
      <th>Eusty71</th>
      <td>1</td>
      <td>$4.81</td>
      <td>$4.81</td>
    </tr>
    <tr>
      <th>Chanirra64</th>
      <td>1</td>
      <td>$4.78</td>
      <td>$4.78</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Prepare to analyze items and purchases
# first join the two tables and get rid of uninteresting
items_purchase_joined_df = pd.merge(purchase_df, items_df, on="Item ID")
items_purchase_joined_df = items_purchase_joined_df.rename(columns={"Item Name_x": "Item Name",
                                                                   "Price_x": "Price"})
items_purchase_joined_clean_df = items_purchase_joined_df[["Item ID",
                                                          "Purchase ID",
                                                          "Item Name",
                                                          "Price"]]
```


```python
# ID 5 most popular items by purchase count
items_groups = items_purchase_joined_clean_df.groupby("Item ID")
sorted_popular_items = pd.DataFrame(items_groups.count())
sorted_popular_items = sorted_popular_items.rename(columns={"Item Name":"Times Purchased"})
sorted_popular_items = sorted_popular_items[["Times Purchased"]]
sorted_popular_items = sorted_popular_items.sort_values(["Times Purchased"], ascending=False)
top_five_popular_items = sorted_popular_items.head(5)

```


```python
# Get item name for top five item IDs
item_names = pd.DataFrame(items_groups["Item Name"].unique())
top_five_item_names = item_names.loc[top_five_popular_items.index.tolist()]
top_five_item_names2 = top_five_item_names
top_five_item_names2 = top_five_item_names2["Item Name"].astype('str').str.replace("]", "")
top_five_item_names2 = top_five_item_names2.str.replace("[", "")
top_five_item_names2 = top_five_item_names2.str.replace("'","")
top_five_item_names2 = top_five_item_names2.str.replace('"','')
top_five_item_names = top_five_item_names2

# Get item prices for top five item IDs
item_prices = pd.DataFrame(items_groups["Price"].unique())
top_five_item_prices = item_prices.loc[top_five_popular_items.index.tolist()]
top_five_item_prices = top_five_item_prices["Price"].astype('str').str.replace("[","")
top_five_item_prices = top_five_item_prices.str.replace("]","")
top_five_item_prices = pd.to_numeric(top_five_item_prices)

# Get total purchase value for top five item IDs
item_value = pd.DataFrame(items_groups["Price"].sum())
top_five_item_values = item_value.loc[top_five_popular_items.index.tolist()]

# Output to a table
popular_items_table = pd.DataFrame(top_five_item_names)
popular_items_table["Purchase Count"] = top_five_popular_items
popular_items_table["Item Price"] = top_five_item_prices.map("${:.2f}".format)
popular_items_table["Total Purchase Value"] = top_five_item_values
popular_items_table["Total Purchase Value"] = popular_items_table["Total Purchase Value"].map("${:.2f}".format)
#popular_items_table["Item Price"] = popular_items_table["Item Price"].str.replace("[","")
popular_items_table
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item Name</th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>94</th>
      <td>Mourning Blade</td>
      <td>3</td>
      <td>$3.64</td>
      <td>$10.92</td>
    </tr>
    <tr>
      <th>90</th>
      <td>Betrayer</td>
      <td>2</td>
      <td>$4.12</td>
      <td>$8.24</td>
    </tr>
    <tr>
      <th>111</th>
      <td>Miserys End</td>
      <td>2</td>
      <td>$1.79</td>
      <td>$3.58</td>
    </tr>
    <tr>
      <th>64</th>
      <td>Fusion Pummel</td>
      <td>2</td>
      <td>$2.42</td>
      <td>$4.84</td>
    </tr>
    <tr>
      <th>154</th>
      <td>Feral Katana</td>
      <td>2</td>
      <td>$4.11</td>
      <td>$8.22</td>
    </tr>
  </tbody>
</table>
</div>




```python
# ID 5 most profitable items by total purchase value
profit_items_group = items_purchase_joined_clean_df.groupby("Item ID")
sorted_profit_items = pd.DataFrame(profit_items_group.sum()).sort_values(["Price"], ascending=False)
sorted_profit_items = sorted_profit_items.rename(columns={"Price": "Total Value"})
sorted_profit_items = sorted_profit_items[["Total Value"]]
top_five_profit_items = sorted_profit_items.head(5)

```


```python
# Get item name for top five profitable items
items_names = pd.DataFrame(profit_items_group["Item Name"].unique())
top_five_profit_names = items_names.loc[top_five_profit_items.index.tolist()]
top_five_profit_names = top_five_profit_names["Item Name"].astype('str').str.replace("[","")
top_five_profit_names = top_five_profit_names.str.replace("]", "")
top_five_profit_names = top_five_profit_names.str.replace("'", "")

# Get purchase count for top five profitable items
items_counts = pd.DataFrame(profit_items_group["Item Name"].count())
top_five_profit_counts = items_counts.loc[top_five_profit_items.index.tolist()]
top_five_profit_counts = top_five_profit_counts.rename(columns={"Item Name": "Purchase Count"})

# Get item price for top five profitable items
items_prices = pd.DataFrame(profit_items_group["Price"].unique())
top_five_profit_prices = items_prices.loc[top_five_profit_items.index.tolist()]
top_five_profit_prices = top_five_profit_prices["Price"].astype('str').str.replace("[","")
top_five_profit_prices = top_five_profit_prices.str.replace("]","")
top_five_profit_prices = pd.to_numeric(top_five_profit_prices)

items_values = pd.DataFrame(profit_items_group["Price"].sum())
top_five_profit_values = items_values.loc[top_five_profit_items.index.tolist()]

# Output to a table
profit_items_table = pd.DataFrame(top_five_profit_items)
profit_items_table["Item Price"] = top_five_profit_prices.map("${:.2f}".format)
profit_items_table["Purchase Count"] = top_five_profit_counts
profit_items_table["Item Name"] = top_five_profit_names
profit_items_table["Total Purchase Value"] = top_five_profit_values["Price"].map("${:.2f}".format)
profit_items_table = profit_items_table[["Item Name", "Purchase Count", "Item Price", "Total Purchase Value"]]
profit_items_table
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item Name</th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>94</th>
      <td>Mourning Blade</td>
      <td>3</td>
      <td>$3.64</td>
      <td>$10.92</td>
    </tr>
    <tr>
      <th>117</th>
      <td>Heartstriker, Legacy of the Light</td>
      <td>2</td>
      <td>$4.71</td>
      <td>$9.42</td>
    </tr>
    <tr>
      <th>93</th>
      <td>Apocalyptic Battlescythe</td>
      <td>2</td>
      <td>$4.49</td>
      <td>$8.98</td>
    </tr>
    <tr>
      <th>90</th>
      <td>Betrayer</td>
      <td>2</td>
      <td>$4.12</td>
      <td>$8.24</td>
    </tr>
    <tr>
      <th>154</th>
      <td>Feral Katana</td>
      <td>2</td>
      <td>$4.11</td>
      <td>$8.22</td>
    </tr>
  </tbody>
</table>
</div>


