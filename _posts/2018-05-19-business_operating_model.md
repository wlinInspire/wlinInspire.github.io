---
layout: post
title: Building Business Operating System at Inspire
mathjax: true
---

<img style="float: right; margin-left:10px" src="/images/wei_love_data.jpg" width="250">
I joined Inspire as the first data scientist. I still remember that Blake told me that he had a team of 40 quantitative analysts at his former company but never hired a data scientist before. To him, hiring me as a data scientist is an adventurous decision, while to me, as a rookie in the field of data science, the career path as a data scientist is also adventurous. It turns out that we have been working together pretty well in this adventurous but exciting journey. Together we gradually create a system that incorporates and automates core functionalities for our business, including portfolio valuation (retail and wholesale), acquisition and life cycle pricing, financial forecast, and etc. This system currently serves as our business operating system and we named it "Symphony".

### Motivation
> "If you can't measure it, you can't improve it. --- Peter Drucker"

In my view, our business is facing the following three big challenges:
1. **Risk**: In this competitive retail energy market, there are various risks to undertake as an energy supplier. The first risk layer comes from volatile wholesale energy (electric) market. In major deregulated energy markets, the energy price is a complex function of supply(generation) and demand(load) and can be extremely volatile driven by oil/natural gas price, weather condition, etc. The second risk layer comes from versatile products we offer to our customers. In order to differentiate us from utilities and other energy suppliers and accommodate customer needs, we provide a wide range of products to our customers, including 100% clean energy supply, long-term fixed price contract, monthly subscription energy plan (The first ever real energy subscription product with flat monthly payment with unlimited energy consumption), smart home devices and energy subscription bundles, etc.
1. **Capital**: Our business is actively acquiring new members, which puts high cashflow pressure on our financial planning. Our great sales agents and online channel partners are incentivized to try their best to promote Inspire and its mission and bring new members to us everyday. As a result, we suffer from negative cash balance due to acquisition cost when a new member signs up. As such, it is critical to monitor the financial healthiness for a sustainable business.
1. **Customer Liquidity**: High customer liquidity exists in most deregulated markets because the barrier for customer to switch from one energy supplier to another is nearly zero and customers are well educated and incentivized by signup bonus, gift, cash back, etc. Therefore, customer attrition or customer life time value becomes one of the most important factors to the success of an energy supplier. Monitoring and reporting customer attrition and properly modeling attrition for future growth is key to business operation and planning.

To address above challenges, the **objective of Symphony** is
> to measure every aspect of our business and make data-driven decisions.

To achieve this objective, the design principles of Symphony are
* always use latest and greatest information from all available data source
* always run at individual customers level


### Architecture

At the early stage of Symphony, what I developed are simply individual scripts to realize very basic functionalities. To be honest, we don't really have a clear big picture of what we were building in our mind at that time. It was not until about one year later that we started to realized that we were actually building an operating system that can drive our business. The overall architecture then started to formalize. After a few iterations, the basic architecture of Symphony as of today is as follows (some parts are still under development):

<span style="display:block;text-align:center"><img src="/images/Symphony_framework.svg" width="600">

This whole system is currently executed daily as a compromise for data size and execution time. The data in each block above can be extracted and assumption/logic in each block above can also be adjusted for downstream applications, such as financial planning, life time value, attrition forecast, acquisition pricing, and etc.

### Development

To realize all good things that a business operating system can provide, you need to build a product. This product was originally built in R because it is in fact a data product. As a data scientist, I had to decide between R and python, which were the only two primary programming language choices 2 years ago. I picked R because
1. R is data friendly and designed specifically for statistics analysis and data science.
1. R has one of the best IDEs, Rstudio.
1. R has high quality packages, such as data.table, ggplot2, lubridate, and etc.

When I joined Inspire, I had zero experience in R. It has been roughly a year that I started feeling comfortable with R for efficient data manipulation and analysis. The best R learning experience I had so far is how to develop a R package. An R package makes managing source code much easier, reduces code bug, and increases coding efficiency. After writing many R scripts, I started re-organizing all scripts and functions to package them into our internal R package called "InspireR". This R package is a core component of Symphony eco-system. InspireR consists of hundreds of R functions and has the following three main functionalities:
1. **Data ETL** (extract, transform, and load): Data ingestion from external data source. ETL job is gradually migrating to Airflow.
1. **Operating Model**: Core component for computation, algorithm, and logic.
1. **Reporting & Dashboard**: Rmarkdown reports and Shiny based interactive Symphony User Interface.

<span style="display:block;text-align:center"><img src="/images/Symphony_eco_systems.svg" width="700">

We use EC2 to host computation jobs and as the web server for user interface. Each day, crontab jobs or Airflow jobs in EC2 server are scheduled and the data output is stored in our redshift instance for data warehousing. The refreshed summary data is then presented in the Shiny dashboard based user interface.

Looking back, it is a great experience building a R package, a product, and a system from scratch. Thanks to my boss for giving me the opportunity to learn and practice. Inspire is a great and unique company and our leaders have clear ideas and long term vision for the business. I am lucky to be one of the avengers (that is how we call ourselves at Inspire).
