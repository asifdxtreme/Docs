# Saga Dashboard

This Dashboard will contains all the necessary information's related transactions happening through Saga.

Currently the [Saga-web](https://github.com/apache/incubator-servicecomb-saga/tree/master/saga-web) module doesn't provides the list of transactions, providing the list of transactions and it's status to the users will be very helpful in development as well as in production.

So here is the proposal to add the Saga Dashboard in [Saga-web](https://github.com/apache/incubator-servicecomb-saga/tree/master/saga-web) module.

#### Technology Used

As the current Saga Web module is based on the combination of AngularJS and SpringBoot App for Backend so we will follow the same to develop the dashboard.

#### API's Used

We will be using the `/events` (GET) of Alpha Server to fetch the list of events from the DB. Based on the output of this API we can manipulate the Data and showcase the Dashboard as well as Transactions list.

#### Web Pages

Saga Web will mainly consist of 2 Pages as per the below hierarchy (This is a Concept, the actual implementation might have some variation based on the feedback from community)

 - DashBoard (Main Menu)
    * Recent Transactions Trends( Last 7 Days)
    * Recent Successful Transactions( Last 7 Days)
    * Recent Failed Transactions( Last 7 Days)
    * Total Transactions
    * Total Failed Transactions
    * Total Successful Transactions
    * Preview of Successful Transaction (Last 24 Hours)
    * Preview of Failed Transaction (Last 24 Hours)
  - Transactions List (Main Menu)
    * List of All Successful Transactions
    * List of All Failed Transactions
    * Search Transactions based on MicroService Name
    * Search Transactions based on Tx ID
    * See the Transaction hierarchy based on Tx ID
#### Dashboard    
![Dashboard](/SagaDashboard.png)    

#### Transactions List
![Transaction](/Transactions.png)

NOTE: Currently I have not considered the Sign ON process, this can be done in later phase if the there is some Single SignON process supported by ServiceComb.

