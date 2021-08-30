## Backend
Found [here](https://github.com/redis-developer/NR-digital-auction-backend)

## Problem
The idea we present is to create a SaaS platform for conducting next generation Digital Auctions.

## Proposed Solution
* A multi-tenant platform where the auctioneer can signup, set up bidding and conduct auctions.
* Any authorized user can place bids and view the bidding updates realtime.
* Dynamic auction closing based on last bid time.

## Features
* Auctioneer signup and set up the site's look and feel.
* Create new auctions (specify minimum bid amount, bid increment, slot start and end time, etc.)
* User - Signup, Sign in and Forgot Password
* View all Auctions & Place Bid
* Real time updates for Biddings placed by various users

## POC
* In POC, we have created a NodeJS based application which connects to Redis Cloud Database. 
* The Redis database is using RedisJSON module to enable usage of JSON data. 
* Real time update of bidding dashboard is enabled usin Socket.IO
* We were able to build an Auction engine with Dynamic Closing and Real time events.
* We have built a single monolithic application to test how we can make use of Redis Cloud.
* We are working on modules for integrating the multi-tenant mode.

## Future Enhancements
1. Enable multi-tenancy - White labelling to allow auctioneers to sign up and set up landing page and create auctions on their behalf.
2. Session Management - Integrate with Redis for user management.
3. Enable reporting and analytics
4. Integrate with third party Payment Gateway for settling auctions.
5. Enable notification - using SNS, SES Notifications
6. ETL module - Data to Amazon S3 Data Lake or RedShift for future audits
7. Microservices implementation 
8. CI/CD pipelines for deployments


## Technology Stack
* A React frontend website hosted using Amazon S3 and served via AWS CloudFront
* Backend - NodeJS
* Authentication - Amazon Cognito
* Database - Redis Cloud
* Redis Modules - RedisJSON
* Real time event handling - Socket.IO
* Compute - Lambda
* Notifications - Amazon SNS/Amzon SES


## How it Works
### How the data is stored
* We are using Redis Cloud Database equipped with RedisJSON module for storing data.
   * Auctions
     * type - Redis Hash 
     * used for storing auctions' data. 
     * UUID generated from backend serves as the key.
     * JSON data which includes keys - auctionId, auctionItemName, description,lotNo, quantity, buyersPremium, itemUnit, minBidAmount, bidIncrement, startDateTime, endDateTime, images, currentBid - servers as the value for Auctions hash.   
     * NodeJS connects to Redis Cloud database. Frontend communicates with NodeJS backend through API calls.
     * POST : /api/auctions
     * request body has JSON data to be inserted to database.
     * NodeJS uses 'redis' module to work with Redis Cloud. The redis client is created using the Redis credentials and hmset() equivalent of HMSET command is used to push data to Redis database.       
   * Biddings
     * type - Redis Hash  
     * used for storing biddings placed on each auction item
     * NodeJS connects to Redis Cloud database. Frontend communicates with NodeJS backend through API calls.
     * POST : /api/bidding
     * request body has JSON data to be inserted to database.
     * AuctionId from request body servers as the key
     * JSON data which includes keys - currentBid, currentBidTime, currentBidEndTime,  and biddings array (id, auctionId, userId, username, bidAmount, bidTime) - servers as value
     * Bidding array has all the biddings placed for a particular auction item
     * Based on current BidEndTime and BidTime - Auction end date is extended based on Dynamic Closing concept. 
     * Current Dynamic closing logic - If a new bid is placed within the last 5 mins of auction end time, the end time is extended by 1 hour.
     * This will be configurable in the SaaS solution.
     * NodeJS uses 'redis' module to work with Redis Cloud. The redis client is created using the Redis credentials and hmset() equivalent of HMSET command is used to push data to Redis database.
   * ProfileSettings
     * type - string
     * JSON data which includes keys - - serves as value 
     * NodeJS connects to Redis Cloud database. Frontend communicates with NodeJS backend through API calls.
     * POST : /api/settings
     * request body has JSON data to be inserted to database.
     * NodeJS uses 'redis' module to work with Redis Cloud. The redis client is created using the Redis credentials and set() equivalent of SET command is used to push data to Redis database.
   * Users
     * type - Redis Hash
     * used for storing user details
     * NodeJS connects to Redis Cloud database. Frontend communicates with NodeJS backend through API calls.
     * POST : /api/users
     * request body has JSON data to be inserted to database.
     * email id serves as the key
     * JSON data which includes keys -- serves as value
     * NodeJS uses 'redis' module to work with Redis Cloud. The redis client is created using the Redis credentials and hmset() equivalent of HMSET command is used to push data to Redis database. 
### How the data is accessed
  * All auctions
      * NodeJS connects to Redis Cloud database. Frontend communicates with NodeJS backend through API calls.
      * GET : /api/auctions fetches all the keys from Auctions Hash
      * NodeJS uses 'redis' module to work with Redis Cloud. The redis client is created using the Redis credentials and hmget() equivalent of HMGET command is used to get data from Redis database.
  * Each auction
      * GET : /api/auctions/{auctionId} fetches each auction item by id
      * NodeJS uses 'redis' module to work with Redis Cloud. The redis client is created using the Redis credentials and hmget() equivalent of HMGET command is used to get data from Redis database.
  * All bidding data of an auction item
      * GET : /api/bidding/{auctionId}
      * NodeJS uses 'redis' module to work with Redis Cloud. The redis client is created using the Redis credentials and hmget() equivalent of HMGET command is used to get data from Redis database.
  * Profile settings
      * GET : /api/settings
      * NodeJS uses 'redis' module to work with Redis Cloud. The redis client is created using the Redis credentials and get() equivalent of GET command is used to get data from Redis database.
  * User info
      * GET : /api/users/{email}
      * NodeJS uses 'redis' module to work with Redis Cloud. The redis client is created using the Redis credentials and hmget() equivalent of HMGET command is used to get data from Redis database.

## Installation
Installation steps:
### Prerequisites

- Node JS
- NPM

* To run the App,<br> 
    **1.** npm install.<br>
    **2.** npm build.<br>
    **3.** npm start.<br>


## App Details

* Screenshots <br>
  * Home page
    ![Screenshot](https://raw.githubusercontent.com/redis-developer/NR-digital-auction-backend/main/screenshots/HomePage.png)
  * SignUp
    ![Screenshot](https://raw.githubusercontent.com/redis-developer/NR-digital-auction-backend/main/screenshots/Signup.png)
  * SignIn
    ![Screenshot](https://raw.githubusercontent.com/redis-developer/NR-digital-auction-backend/main/screenshots/SignIn.png)
  * Dashboard
    ![Screenshot](https://raw.githubusercontent.com/redis-developer/NR-digital-auction-backend/main/screenshots/Dashboard.png)
  * Auction Items
    ![Screenshot](https://raw.githubusercontent.com/redis-developer/NR-digital-auction-backend/main/screenshots/AuctionItems.png)
  * Bidding Page
    ![Screenshot](https://raw.githubusercontent.com/redis-developer/NR-digital-auction-backend/main/screenshots/ItemBiddingPage.png)
  * Bidding Realtime Updates
    ![Screenshot](https://raw.githubusercontent.com/redis-developer/NR-digital-auction-backend/main/screenshots/BiddingRealTimeUpdate.png)
