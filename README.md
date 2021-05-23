pandas_s3_redshift
=============

This is a python Package meant to making it easier for bulk uploads, where the procedure for uploading data consists in generating various CSV files, uploading them to an S3 bucket and then calling a copy command on the server, this package helps with all those tasks in encapsulated functions.

Installation
------------

To install the latest Pypi version, you’ll need to execute:

``` r
    pip install pandas_s3_redshift
    or
    python3 -m pip install pandas_s3_redshift
```

If instead you want to install the latest github master version:

``` r
    git clone https://github.com/nit567esh/pandas_s3_redshift.git
    cd <pkg_directory>
    python3 setup.py install
```

Drivers
-------

This library uses **psycopg2** for connecting to Amazon Redshift and **boto3** for connecting to S3 bucket.

Usage
-----

You’ll have available 3 functions fro different kind of operations:
`1. rs_create` - You can create a table from scratch from python and upload the contents of the data frame.
`2. rs_append` - You can insert/append the contents of the pandas data frame into existing redshift table.
`3. rs_upsert` - You can insert and update the existing redshift table using contents of the pandas data frame.

``` r
# Step 1: Configure redshift & S3 authentication
redshift_auth=(host, port, db, user, pswd)
s3_auth=(accesskey, secretkey, bucket)

# Step 2: Use rs_create, rs_append or rs_upsert
rs_create(redshift_auth, sql_ddl)
rs_append(data, redshift_auth, s3_auth, schema=None, table=None)
rs_upsert(data, redshift_auth, s3_auth, schema=None, table=None, upsertkey=('cols',....))
```

##### Notes
1. CSV format is used to write pandas dataframe to s3 bucket (started with ETL_temp*). 
2. If rows in pandas are >3K then pandas dataframe splitted into muliple chunk of csv with equal rows while writting into s3.
1. Upsert method uses transaction block using below setps in order to accomplish insert and update into a table
   - Create staging table (using create table as)
   - Copy data into staging table
   - Delete row from main table which needs to be updated
   - Copy staging table data to main table
