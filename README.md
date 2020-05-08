Cost model for a multi-tenant analytics platform
================================================

![](media/image1.png){width="0.6166666666666667in" height="0.17777777777777778in"}

Background
----------

An Azure customer, Contoso Inc, would like to build a multi-tenant analytics platform which will allow customers of Contoso to consume data, analytics and services sold by Contoso. Customers can consume raw data and/or analytics via a number of methods such as streaming or batch, on-platform or off-platform, provisioned computed and services, or BYO compute and services.

Contoso therefore has to ingest the data required to support this platform and they too need to ingest streaming data and historical data before any customers can be on-boarded on to the platform. They would like to stagger the number of data sets and associated workloads that are brought on to the platform and as such will offer a limited number of data sets and analytics packages in the beginning to only a few customers. Gradually the platform needs to support more data sets, workloads and customers over-time as the value, throughput and stability has been demonstrated.

It is likely that Contoso will charge their customers an account fee to use the platform and then a license fee per user of the platform. The account fee will cover the costs to "stand-up" necessary services to prepare each customer environment and then the user fee will cover the scaling of various services to support consuming of data and analytics.

Contoso have asked for a flexible cost model which will allow them run a number of "what-if" scenarios by changing various parameters in the model, thereby allowing them to forecast costs over time. Ultimately they would like a set of charts which they can present to their board for approval.

Understanding the cost model
----------------------------

This cost model is flexible and can be tailored to cater for multiple scenarios, but the initial purpose of this model was to forecast costs over time for a typical lambda architecture that supports high throughput and low latency. The set of possible services chosen is by no means be limited to those already provided in this "default" model, but should be chosen to suit your requirements. These should be entered accordingly in the "Monthly Component Cost" tab along with their associated costs. Various indicators are used to denote whether a specific service is used in a certain scenario but these are covered in more detail later.

Note: For the avoid of doubt, any reference to customers below will be in the context of customers of Contoso unless explicitly stated.

The goal is to forecast the costs associated with running the platform as more workloads, use-cases, customers and users are on-boarded over time. Specifically it divides the costs into various categories to demonstrate how they may change over time as the platform is ramped up to full capacity. This is not a mandatory feature of the cost model and it can be adapted to suit your scenario. It was designed this way as most customers will likely opt for a phased approach to building a large analytics platform rather than trying to "boil the ocean" on day one.

The model has the following characteristics and makes certain "default" assumptions which can be changed as required:

-   the analytics platform is to host a number customers and each customer has users

-   there is a *base cost* for hosting the platform which is incurred regardless of whether any customers have been onboarded

-   the *base* cost can be split by *streaming* and non-streaming options

-   non-base (i.e. customer incurred) streaming costs can be split into a customer charge and a per user charge. This is denoted by specifying the cost in the "Price per customer" column vs the "Price per user" column

-   Up to 5 use-cases can be supported. Use-cases require a mix of Azure services (specified in the "Monthly Component Cost" tab using the indicators) and customers can subscribe to any of the use-cases which is denoted by specifying a number of users against each use-case. The use-cases can incur either a customer cost or a per-user cost. The former is the cost associated with standing up the customer environment and the latter is associated with the scaling of the services to support increased users and consumption

-   the platform will have a number of non-prod environments which are influenced by the number of customers and users on the platform

-   a total cost including all environments can be calculated but the difference between this and the base costs is known as variable costs

-   variance is built into the model to account for variations in costs aka a cost "buffer"

-   a "shared" PBI embedded service utilised by all customers rather a separate service provision for each customer. This multi-tenant solution will be achieved using the row-level security (RLS) feature of PBI.

> *Base Streaming*

-   This is the cost of running the streaming platform before any customers are onboarded. This may typically involve ingesting the streaming data into an event hub (producer application), persisting it to storage and a database. The different services involved in this component are set by adding costs to the "Monthly Component Cost" tab column O "Base Streaming"

> *Streaming Users*

-   This is the cost of consumption per user (users subscribing to the stream). This typically requires a consumption event hub and some compute to run the consumer process. This cost is set by adding costs in the "Monthly Component Cost" tab to columns P & Q representing costs to the customer and per user respectively.

Getting started
---------------

Once you have defined the Azure services that will be used in the architecture, list them in the "Monthly Component Cost" tab, and their associated costs (from the Azure calculator) in columns N, P, and Q depending on whether these form part of the base costs, customer costs or per user costs. For example, if a service is used to host the platform (i.e. a cost that Contoso will have to bear in order to ingest data) then this should be entered in as a base cost in the "Base Price" column (N). If it is a service which will be used to support streaming ingestion then indicate this with a 1 in the "Base Streaming" column (O). If the services is necessary to stand-up a customer environment, then this should be represented in the "Price per customer" column (P). If the service will be used by users consuming data and analytics then this should reflected in the "Price per user" column (Q).

Next indicate which services will be used in different customer/user scenarios or use-cases as indicated by 1 in the columns shaded in grey (U-AA). This indicator will be used to sum up all the costs associated to these service to support that scenario or use-case.

Refer to the next section to understand the formulas used to populate the forecast.

Explanation of the main formulas
--------------------------------

All formulas below can be found on the "Monthly Cost Forecast" tab. There are comments associated with most columns or cells, please refer to those also as well as the explanation below.

[Base cost]{.underline}

This is the cost of hosting the platform before any customers are onboarded

\'Monthly Component Cost\'!\$N\$33: This is the total cost of all base services excluding streaming. This is taken from the SUM formula at the bottom of the "Monthly Component Cost" tab.

IF(I2=0,\'Monthly Component Cost\'!\$N\$23,0): If I2 is zero i.e. there are is no PBI embedded requirement then the cost of this service needs to be deducted from the base cost. It is assumed that there will be a "shared" PBI embedded service utilised by all customers rather a separate service provision for each customer. The multi-tenancy will be achieved using the row-level security (RLS) feature of PBI.

\*B2: The above is multiplied by the number of datasets/workloads onboarded

\+ (((\'Monthly Component Cost\'!\$N\$33 - IF(I2=0,\'Monthly Component Cost\'!\$N\$23,0))\*B2)\*\$C\$9\*(\$C\$10+(E2\*\$C\$11))): In addition to the above the non-prod environments will be added to the base cost.

\$B\$9\*(\$B\$10+(D2\*\$B\$11)): This formula component calculates the base cost of non prod environments (before any customers are onboarded)

[Base Streaming]{.underline}

SUMPRODUCT(\'Monthly Component Cost\'!\$N\$10:\$N\$15\*\'Monthly Component Cost\'!\$O\$10:\$O\$15)\*B2

The sumproduct function sums the product of all the indicators in column O on the Monthly Component Cost tab with the associated component/service cost in Q, multiplied by the number of datasets / workloads. The indicator value is 1 therefore the formula is actually only calculating the total cost of all the services which will be used to support that scenario, which in this case is base streaming.

[Streaming Price]{.underline}

SUMPRODUCT(\'Monthly Component Cost\'!\$U\$16:\$U\$26\*\'Monthly Component Cost\'!\$Q\$16:\$Q\$26)\*G2

The sumproduct function sums the product of all the indicators in column U on the Monthly Component Cost tab with the associated component/service cost in Q, multiplied by the number of users specified. The indicator value is 1 therefore the formula is actually only calculating the total cost of all the services which will be used to support that scenario, which in this case is streaming consumption.

[PBI Embedded Price (Q\*V)]{.underline}

SUMPRODUCT(\'Monthly Component Cost\'!\$V\$16:\$V\$26\*\'Monthly Component Cost\'!\$Q\$16:\$Q\$26)\*I2

The sumproduct function sums the product of all the indicators in column V on the Monthly Component Cost tab with the associated component/service cost in Q, multiplied by the number of users specified (I). The indicator value is 1 therefore the formula is actually only calculating the total cost of all the services which will be used to support that scenario, which in this case is PBI Embedded.

[Use Cases 1-5 Price]{.underline}

All the use-case costs use similar logic to many of the other formulas above and it is repeated through the use-case options 1-5.

SUMPRODUCT(\'Monthly Component Cost\'!\$W\$16:\$W\$26\*\'Monthly Component Cost\'!\$Q\$16:\$Q\$26)\*K2

The sumproduct function sums the product of all the indicators in column W on the Monthly Component Cost tab with the associated component/service cost in Q, multiplied by the number of users specified (I). The indicator value is 1 therefore the formula is actually only calculating the total cost of all the services which will be used to support that scenario.

[Total Costs (incl all environments)]{.underline}

U3+(C3\*\$C\$9\*\$C\$10)+(C3\*\$C\$9\*\$C\$10\*E4\*\$C\$11): Take the monthly cost add to it the cost of the non-prod environments which is made up of base (C3) multiplied by the number of non-prod environments (C9), multiplied by the percent cost of non-prod environments (C10) add to this itself again multiplied by the % effect additional customers (E4) will have on the cost of non-prod environments (C11)

Important Column definitions
----------------------------

There are comments in most column headings in the spreadsheet but below are column definitions for the most important fields.

Tab: Monthly Component Cost

Column: P

Field name: Price per customer

Description: This column stores the cost of a service if it is attributable to the customer subscription. These costs are typically to "stand-up" the services and data to support users on the platform.

Column Q

Field name: Price per user

Description: This column stores the cost of a service based on consumption of data and services by users in their environment.
