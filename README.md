# Data Anomaly Tracker (DAT)

DAT is a tool for spoting anomalies in table data.
For a table/database containing row-wise records, DAT will spot the records which are unique or have occured only seldom.

This repository provides the python code for the DAT and two files TRAIN.csv and TEST.csv.
TRAIN.csv is a table of 20000 records, these are trades made by one german energy company. The data is changed to avoid revealing any company information. TRAIN.csv is a table use to created the parameters(here called bins) and TEST.csv is a set of records against which the DAT is ran.

The TRAIN table has multiple columns, not all of which are important. Therefore DAT requires that the relevant columns are defined by the user. Columns used in this example are 'TRADE_COMPANY','TRADE_REASON','TRADER','VOLUME' and 'PRICE'. The idea is that by combination of these features, a trade type can be defined. If a brand new type of trade occurs or a trade with wrong price or volume is entered in the database, the DAT will notice that the combination of values for the given columns is either unique or is lower than the threshold.

From the technical point, the DAT does the following. For categorical columns the DAT builds one concatenated value (GROUP) for each row of the table. For all rows that share the same GROUP, the DAT will then take values from numerical columns (specified by the user) and based on them build intervals (or bins) and assign each such bin a number. By design, every value from numerical columns in the TRAIN set falls into one of the bins, and receives a bin number. Finally GROUP in each row is concatenated with the bin numbers (one bin number for each numerical column) and this final concatenation is reffered to as the CONC. Every row in the TRAIN set holds a CONC and we count in how many rows did a particular CONC occur, this count is reffered as the FREQ.  

The same CONC building is then done for the TEST set but using the bin making parameters from the TRAIN set. We then look what is the FREQ for every CONC (row) in the TEST set. If the FREQ s below the user defined threshold, such row is cosidered an anomaly.

The elements which the user needs to define are:

*the path to train_file  
*the path to  test_file    
*categorical columns - specify sensible columns whose unusual entries can be used to spot anomalous data
*numerical_columns - specify sensible columns whose unusual values can be used to spot anomalous data
*coefficients - used to build the bins around numerical columns (this values requires fine tuning and several executions before set correct)
*threshold - used to clasify a CONC as anomalous or normal.
*date_column_name - can be used optinaly to extend the tool for temporal data

Running the code with the provided TRAIN.csv and TEST.csv data will return the following results:

0 ....  CONC                                   FREQ      group_count   price_count  volume_vount  STATUS        
0 ....  15_3_COMPANY_1HEDGE_TRADEEvan Black     7           27            7         8             OK  
1 ....  19_3_COMPANY_1HEDGE_TRADEJohn Smith     5           44           11         5             OK  
2 ....  0_0_COMPANY_3DAILY_TRADEALEX M     0            0            0         0             NOT OK

3 ....  1_23_COMPANY_3DAILY_TRADEMatt Red   312         1779         1748         312           OK

4 ....  1_25_COMPANY_3DAILY_TRADEMatt Red   494         1779         1748         500           OK 







