---
title: "Python Bulk Reverse DNS Lookup"
date: "2019-06-08"
categories: ["Technical"]


---


This script performs a reverse DNS lookup against a list of IP addresses. I use it to determine genuine Googlebot requests for log file analysis.

It takes an Excel file (.xslx) called logs.xslx and looks for IPs in a column called ip. Then it performs a reverse lookup on the unique values. It exports an Excel file called validated_logs.xslx which contains all of the data from logs.xslx, with an additional column called 'dns' that lists the domain names.

Anaconda comes with all the packages required to run this script. If you’re not using Anaconda, there are a few dependencies that need installing. See comments in code.


```
import pandas as pd
# pip install dnspython
from dns import resolver,reversename
# pip install xlrd, pip install xlsxwriter
from pandas.io.excel import ExcelWriter

# Import excel called logs.xlsx as dataframe
logs = pd.read_excel('logs.xlsx', sheet_name='Sheet1') 

# Create DF with dupliate ips filtered for check
logs_filtered = logs.drop_duplicates(['ip']).copy() 

# Evaluate DNS from filtered IP's
def reverseDns(ip):
  try: 
    return str(resolver.query(reversename.from_address(ip), 'PTR')[0])
  except: 
    return 'N/A'

# Create DNS column with the reverse IP DNS result 
logs_filtered['dns'] = logs_filtered['ip'].apply(reverseDns)

# Merge DNS column to full logs matching IP
logs_filtered = logs.merge(logs_filtered[['ip','dns']], how='left', on=['ip'])

# output as Excel
writer = ExcelWriter('validated_logs.xlsx', engine='xlsxwriter', options={'strings_to_urls': False})
logs_filtered.to_excel(writer,'Sheet1', index=False)
writer.save()

print('File Succesfully written as validated_logs.xlsx')

```
