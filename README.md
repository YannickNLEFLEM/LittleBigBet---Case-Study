# LittleBigBet---Case-Study
import pandas as pd
import numpy as np
[bets.csv](https://github.com/YannickNLEFLEM/LittleBigBet---Case-Study/files/10955367/bets.csv)
[customers.csv](https://github.com/YannickNLEFLEM/LittleBigBet---Case-Study/files/10955370/customers.csv)
[pricing.csv](https://github.com/YannickNLEFLEM/LittleBigBet---Case-Study/files/10955372/pricing.csv)

# Load the data from CSV files
bet_df = pd.read_csv('bet.csv')
customers_df = pd.read_csv('customers.csv')
pricing_df = pd.read_csv('pricing.csv')

# Check for missing values
print(bet_df.isnull().sum())
print(customers_df.isnull().sum())
print(pricing_df.isnull().sum())

# Remove irrelevant data
bet_df = bet_df.drop(['commission'], axis=1)
customers_df = customers_df.drop(['date'], axis=1)

# Convert data types
bet_df['date'] = pd.to_datetime(bet_df['date'])
customers_df['test_new_service'] = customers_df['test_new_service'].astype(bool)

# Merge the dataframes
merged_df = pd.merge(bet_df, customers_df, on='customer_id')

# Drop duplicates
merged_df = merged_df.drop_duplicates()

# Rename columns
pricing_df = pricing_df.rename(columns={'type': 'account_type', 'price per month': 'price'})

# Merge with the pricing dataframe
merged_df = pd.merge(merged_df, pricing_df, on='account_type')

# Create a new column for total winnings
merged_df['total_winnings'] = np.where(merged_df['win']==1, merged_df['bet_value'], 0)

# Group by customer ID and sum the total winnings
grouped_df = merged_df.groupby('customer_id').agg({'total_winnings': 'sum'})

# Drop customers with no winnings
grouped_df = grouped_df[grouped_df['total_winnings'] > 0]

# Drop the unnecessary columns
grouped_df = grouped_df.drop(['win', 'account_type', 'bet_value'], axis=1)

# Print the cleaned and preprocessed dataframe
print(grouped_df.head())
