# Data Anomaly Tracker (DAT)

DAT is a tool for spoting anomalies in table data.
For a table/database containing row-wise records, DAT will spot the records which are unique or have occured only seldom.

This repository provides the python code for the DAT and two files TRAIN.csv and TEST.csv.
TRAIN.csv is a table of 20000 records, these are trades made by one german energy company. Data is slightly changed to avoid revealing any sensitive company information. TRAIN.csv is a table used to created the parameters(here called bins) and TEST.csv is a set of records against which the DAT is ran.

The TRAIN table has multiple columns, not all of which are important. Therefore DAT requires that the relevant columns are defined by the user. Columns used in this example are 'TRADE_COMPANY','TRADE_REASON','TRADER','VOLUME' and 'PRICE'. The idea is that by combination of these features, a trade type can be defined. If a brand new type of trade occurs or a trade with wrong/unusual price or volume is entered in the database, the DAT will notice that the combination of values for the given columns is either unique or is lower than the threshold.

From the technical point, the DAT does the following. For categorical columns the DAT builds one concatenated value (GROUP) for each row of the table. For all rows that share the same GROUP, the DAT takes values from numerical columns (specified by the user) and based on them build intervals (or bins) and assigns each bin a number. By design, every value from numerical columns in the TRAIN set falls into one of the bins, and receives a bin number. Finally GROUP in each row is concatenated with the bin numbers (one bin number for each numerical column) and this final concatenation is reffered to as the CONC. Every row in the TRAIN set holds a CONC and the algorithm counts in how many rows did a particular CONC occur, this count is reffered as the FREQ.  

The same CONC construct is then done for the TEST set but using the bin making parameters from the TRAIN set. We then look what is the FREQ for every CONC (row) in the TEST set. If the FREQ is below the user-defined threshold, such row is cosidered an anomaly.

The elements which the user needs to define are:

*the path to train_file  
*the path to  test_file    
*categorical columns - specify sensible columns whose unusual entries can be used to spot anomalous data
*numerical_columns - specify sensible columns whose unusual values can be used to spot anomalous data
*coefficients - used to build the bins around numerical columns (this values requires fine tuning and several executions before set correct)
*threshold - used to clasify a CONC as anomalous or normal.
*date_column_name - can be used optinaly to extend the tool for temporal data

Running the code with the provided TRAIN.csv and TEST.csv data returns the following output:

  ....  CONC                                   FREQ      group_count   price_count  volume_count  STATUS  
  
0 ....  15_3_COMPANY_1HEDGE_TRADEEvan Black     7           27            7         8             OK  

1 ....  19_3_COMPANY_1HEDGE_TRADEJohn Smith     5           44           11         5             OK  

2 ....  0_0_COMPANY_3DAILY_TRADEALEX M          0            0            0         0             NOT_OK

3 ....  1_23_COMPANY_3DAILY_TRADEMatt Red       312         1779        1748        312           OK

4 ....  1_25_COMPANY_3DAILY_TRADEMatt Red        494        1779        1748         500          OK 


The TEST set contains 5 entries and one of them is obviously anomalous. It is the third entry which also has status_NOT_OK.
The FREQ of this anomalous row is zero, meaning that such entry does not at all exist in the TRAIN set. 
The group_count column is zero meaning that the GROUP = COMPANY_3DAILY_TRADEALEX M does not exist in the TRAIN set. 
Also, since our example has two numerical columns (PRICE and VOLUME), besides the group_count column the output will have columns price_count and volume_count. Values in these column are also zero, meaning that values for price and volume for this entry are very unusual. In fact if we open the TEST.csv file we can see that the price and volume for this entry are quite extreme values.

The DAT tool therefore not only shows if an entry is anomalous, it points out which dimension of the record is anomalous (it could be the concatenation of categorical columns, one of the numerical columns, or both). When either of the "_count"  columns has a value below the defined threshold, the column status will equal 'NOT OK'
