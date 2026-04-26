# Ride Sharing Data Analysis (SQL)

## Tools Used
- SQL (Joins, Group By, Queries)

## Description
This project analyzes ride-sharing data using SQL queries to extract meaningful insights.

## Key Insights
- Most active locations
- Peak ride times
- Customer usage patterns

## SQL Concepts Used
- Joins
- Aggregations (SUM, COUNT)
- Group By

## Files
[ride_sharing_project.sql](https://github.com/user-attachments/files/27097275/ride_sharing_project.sql)

CREATE DATABASE ride_project;
USE ride_project;
# TABLE CREATION 
CREATE TABLE rides_raw (
    ride_id INT,
    driver_name VARCHAR(50),
    customer_name VARCHAR(50),
    city VARCHAR(50),
    fare VARCHAR(20),
    distance_km VARCHAR(20),
    ride_status VARCHAR(20),
    payment_method VARCHAR(20),
    ride_date VARCHAR(20)
);
# CHECK DATA
SELECT * FROM rides_raw;
# CLEAN THE DATA (1.FIX FARE COLUMN)
UPDATE rides_raw
SET fare = null
WHERE fare = '' OR fare = 'unknown';
# (2.FIX DISTANCE COLUMN)
UPDATE rides_raw
SET distance_km = null
WHERE distance_km = '' OR distance_km = 'NA';
# (3.FIX MISSING NAMES)
UPDATE rides_raw
SET driver_name = 'unkmown'
WHERE driver_name IS NULL OR driver_name = '';
UPDATE rides_raw
SET customer_name = 'unkmown'
WHERE customer_name IS NULL OR customer_name = '';
# (4.FIX RIDE STATUS)
UPDATE rides_raw
SET ride_status = 'unkmown'
WHERE ride_status IS NULL OR ride_status = '';
# CREATE CLEAN TABLE
CREATE TABLE rides_clean AS
SELECT 
    ride_id,
    driver_name,
    customer_name,
    city,
    CAST(fare AS SIGNED) AS fare,
    CAST(distance_km AS FLOAT) AS distance,
    ride_status,
    payment_method,
    ride_date
FROM rides_raw;
# VERIFY CLEAN DATA
SELECT * FROM rides_clean;
# PERFORMING ANALYSIS (Total Rides)
SELECT COUNT(*) AS total_rides FROM rides_clean;
# (Total Revenue)
SELECT SUM(fare) AS total_revenue FROM rides_clean;
# (Ride Status Count)
SELECT ride_status, COUNT(*) FROM rides_clean GROUP BY ride_status;
# (Average Fare)
SELECT AVG(fare) FROM rides_clean;
# ADVANCED QUERIES (Cancellation Rate)
SELECT 
COUNT(CASE WHEN ride_status = 'Cancelled' THEN 1 END) * 100.0 / COUNT(*) AS cancel_rate
FROM rides_clean;
# (TOP 5 DRIVERS)
SELECT driver_name, COUNT(*) AS total_rides
FROM rides_clean
GROUP BY driver_name
ORDER BY total_rides DESC
LIMIT 5;
# (Revenue by Payment Method)
SELECT payment_method, SUM(fare)
FROM rides_clean
GROUP BY payment_method;

# Create Drivers Table
CREATE TABLE drivers AS 
SELECT DISTINCT driver_name FROM rides_clean;
# Create Customers Table
CREATE TABLE customers AS 
SELECT DISTINCT customer_name FROM rides_clean;

# INNER JOIN 
SELECT d.driver_name, COUNT(r.ride_id) AS total_rides
FROM drivers d
INNER JOIN rides_clean r
ON d.driver_name = r.driver_name
GROUP BY d.driver_name;
# LEFT JOIN 
SELECT d.driver_name, COUNT(r.ride_id) AS total_rides
FROM drivers d
LEFT JOIN rides_clean r
ON d.driver_name = r.driver_name
GROUP BY d.driver_name;
# RIGHT JOIN 
SELECT r.ride_id, d.driver_name
FROM drivers d
RIGHT JOIN rides_clean r
ON d.driver_name = r.driver_name;
# JOIN with Aggregation (JOIN+GROUP BY)
SELECT d.driver_name, SUM(r.fare) AS total_earnings
FROM drivers d
JOIN rides_clean r
ON d.driver_name = r.driver_name
GROUP BY d.driver_name
ORDER BY total_earnings DESC
LIMIT 5;
<img width="1920" height="1080" alt="Screenshot (18)" src="https://github.com/user-attachments/assets/195c1a4e-168b-4284-89a3-0ada3cd6d0bb" />
<img width="1920" height="1080" alt="Screenshot (17)" src="https://github.com/user-attachments/assets/fb35506e-9436-4644-903a-2ea657dbbd5d" />
