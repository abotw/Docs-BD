# Hive ODS Layer Table Creation Guide

This section contains the SQL scripts required to initialize the automotive data tables. All tables use a comma (`,`) as the field delimiter and are stored in the `TEXTFILE` format.

## 1. Hefei Second-Hand Car Information Table

**Table Name:** `ods_hefei_er_shou_car_info`

This table stores detailed information regarding used cars in the Hefei region.

SQL

```
CREATE TABLE ods_hefei_er_shou_car_info(
    specId string COMMENT 'Vehicle Model ID',
    car_name string COMMENT 'Vehicle Name',
    kilometer string COMMENT 'Mileage',
    car_time string COMMENT 'Purchase Year',
    car_location string COMMENT 'Location',
    sell_price string COMMENT 'Selling Price',
    true_price string COMMENT 'Actual Price'
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```

------

## 2. Mercedes-Benz New Car News Table

**Table Name:** `ods_benchi_new_car_info`

This table tracks monthly news updates and information specific to Mercedes-Benz new vehicles.

SQL

```
CREATE TABLE ods_benchi_new_car_info(
    car_time string COMMENT 'News Month',
    info string COMMENT 'News Content'
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```

------

## 3. Mercedes-Benz Half-Year Ranking Table

**Table Name:** `ods_benchi_car_half_year`

This table monitors the performance, scores, and sales volumes of Mercedes-Benz models over the most recent six-month period.

SQL

```
CREATE TABLE ods_benchi_car_half_year(
    `rank` string COMMENT 'Ranking',
    seriesname string COMMENT 'Model Name',
    priceinfo string COMMENT 'Price Range',
    scorevalue string COMMENT 'Score/Rating',
    salecount string COMMENT 'Sales Volume'
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```

------

## 4. Monthly Car Sales Top Ranking Table

**Table Name:** `ods_month_car_top`

This table stores general monthly automotive sales rankings across different brands and models.

SQL

```
CREATE TABLE ods_month_car_top(
    `rank` string COMMENT 'Ranking',
    seriesname string COMMENT 'Model Name',
    priceinfo string COMMENT 'Price Range',
    scorevalue string COMMENT 'Score/Rating',
    salecount string COMMENT 'Sales Volume'
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```

