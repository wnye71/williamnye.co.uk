---
title: "Python Bulk Reverse DNS Lookup"
date: "2019-06-08"
categories: ["Technical"]


---

This script performs a reverse DNS lookup against a list of IP addresses. I use it to determine genuine Googlebot requests for log file analysis.

It takes an Excel file (.xslx) called logs.xslx and looks for IPs in a column called ip. Then it performs a reverse lookup on the unique values. It exports an Excel file called validated_logs.xslx which contains all of the data from logs.xslx, with an additional column called 'dns' that lists the domain names.


```
import pandas as pd
from dns import resolver,reversename
from pandas.io.excel import ExcelWriter

logs = pd.read_excel('logs.xlsx', sheet_name='Sheet1') #Import excel called logs.xlsx as dataframe
logs_filtered = logs.drop_duplicates(['ip']).copy() #create DF with dupliate ips filtered for check

def reverseDns(ip):
	try:
		rev_name = reversename.from_address(ip)
		reversed_dns = str(resolver.query(rev_name,"PTR")[0])
		return(reversed_dns)
	except:
		return('N/A')

logs_filtered['dns'] = logs_filtered['ip'].apply(reverseDns)

logs_merged = pd.merge(logs, logs_filtered[['ip','dns']], how='left', on=['ip'])


# output as Excel

writer = ExcelWriter('validated_logs.xlsx', engine='xlsxwriter', options={'strings_to_urls': False})
logs_merged.to_excel(writer,'Sheet1',index=False)

writer.save()

print('File Succesfully written as validated_logs.xlsx') 

```
