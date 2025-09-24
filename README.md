## Unified ETL pipeline

This project demonstrates a production-ready ETL pipeline that downloads sales data using an API from web server, transform the data, and stores it in both PostgreSQL and MySQL databases. The pipeline is scheduled to run automatically via cron every day.

# How it Works

## ETL Job (`etl_pip.py`)

- Downloads raw sales data.  
- Transforms and cleans it.  
- Loads into PostgreSQL tables:  
  - `product_sales` 
  - `store_sales`
  - `category_sales`
  - `brand_sales`
  - `billing_data`

## Update Job (`product_update.py`)

- Runs after ETL to fix inconsistent product and store names.  

**Example:**
-- sql
UPDATE product_sales
SET "productname" = 'Cadbury Dairy Milk Silk Fruit & Nut Chocolate 55g'
WHERE "productname" = 'Cadbury Dairy Milk Silk Fruit & Nut Chocolate Bar 55g';

## Automation via Cron

Jobs run daily on Azure VM.

ETL runs at 04:30 AM IST

Product/Store updates run at 04:32 AM IST`

## 🛠️ Setup

#### 1. Clone Repo
git clone https://github.com/<your-username>/unified-etl.git
cd unified-etl

##### 2. Create Virtual Environment
python3 -m venv vmac
source vmac/bin/activate
pip install -r requirements.txt

#### 3. Configure Database

##### Update product_update.py and azure_db.py with your PostgreSQL connection details:

DB_HOST = "localhost"
DB_NAME = "your_db"
DB_USER = "your_user"
DB_PASSWORD = "your_password"

#### 4. Run ETL Manually
python etl_pip.py

#### 5. Run Updates Manually
python product_update.py

## ⏰ Scheduling with Cron

#### Edit crontab:

crontab -e


#### Add jobs:

##### #Run ETL at 04:30 AM IST
30 4 * * * /home/azureuser/etl/vmac/bin/python /home/azureuser/etl/etl_pip.py >> /home/azureuser/etl/etl_pip.log 2>&1

##### #Run product/store updates at 04:32 AM IST
32 4 * * * /home/azureuser/etl/vmac/bin/python /home/azureuser/etl/update_products.py >> /home/azureuser/etl/update_products.log 2>&1


#### Verify:

crontab -l

