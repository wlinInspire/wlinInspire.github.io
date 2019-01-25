---
layout: post
title: How to Build an Automated Trading System using R
mathjax: true
---

This is an interesting post. We are all R zealots and we know we can basically use R to build any data products in a super fast and efficient way. An automated trading system is no exception. Whether you are doing high frequency trading, day trading, swing trading, or even long term trading, you can use R to quickly build a trading robot that trades the stocks or other financial instruments on your behalf. Some of the advantages in building a trading robot are

1. Rules based trading. Emotion can be irrational for most of time and that is why we try to avoid it in the trading decision making process.
2. No rest needed for trading robot. It can watch the price movement at every second across multiple financial instruments and execute the order immediately when the timing is correct.

I decided to build a simple automated trading system in R as I learned more about R. It may not be the most sophisticated system but it works at the end of the day. Here is a step by step guide based of mine:

1. **Brokerage**: You need a broker that supports programming APIs so that we can automate the trades. One of the best brokers in this field is the [Interactive Brokers](https://interactivebrokers.com){:target='blank'} (IB). IB's rich APIs should cover most of your trading needs. IB also offers very competitive commission structure in the industry.

1. **Hardware**: You need to schedule the R script to run during market hours on some sort of computer. You can use a desktop, a laptop, or a server but it has to be reliable. Unix-like systems are usually more reliable and developer friendly, such as a mac or a linux system. I personally use an AWS EC2 server because it runs 24x7 over the cloud and I am quite familiar with EC2 environment. If you want a lower rate, you can even purchase the reserved instance, which is what I did. You can use the reserved instance for other purpose other than the trading to fully utilize it. As for the operating system image choice, I recommend the ubuntu image other than the default AMI image because ubuntu is more friendly to the graphic user interface which we need to install on the server in a later step.

1. **Software**: Once you spin up the server, you can SSH to the server and install the following software in exact order:
    * R: You need to install R for no reason. You can download the latest R for linux system on [CRAN](https://cran.r-project.org/){:target='blank'} and find the installation instructions there.
    * Rstudio Server: I highly recommend you to use [Rstudio Server](https://www.rstudio.com/products/rstudio/download-server/){:target='blank'}, which not only is a great IDE but also helps you manage your server much more effectively, like uploading and downloading files in the server.
    * R Packages:
        * [IBrokers](https://cran.r-project.org/web/packages/IBrokers/IBrokers.pdf){:target='blank'}(must): This package is the bridge between your script and Interactive Brokers. It wraps most of the Interactive Brokers' API calls into R functions.
        * [tidyverse](https://www.tidyverse.org/){:target='blank'}(optional): This package is a combination of several useful packages needed for  most of R users.
        * [data.table](https://github.com/Rdatatable/data.table/wiki){:target='blank'}(optional): data.table is one of my favorite packages. It makes data manipulation much more fun.
    * Ubuntu Desktop: Since you will need to start Interactive Brokers' trading software through desktop, we need to install ubuntu graphic user interface on the server. For detailed procedure, you refer to this excellent [tutorial](https://medium.com/@Arafat./graphical-user-interface-using-vnc-with-amazon-ec2-instances-549d9c0969c5){:target='blank'}.
    * Remote Desktop Software: As you know, the EC2 server has no monitor. To view the ubuntu desktop remotely, we need to spin up the vnc service on the server. The same [tutorial](https://medium.com/@Arafat./graphical-user-interface-using-vnc-with-amazon-ec2-instances-549d9c0969c5){:target='blank'} covers the details on that.
    * Interactive Brokers' Trading Software: You need to install the trading software (Trader Workstation or IB Gateway) from Interactive Brokers to enable the API capability. After downloading and installing the [offline version](https://www.interactivebrokers.com/en/index.php?f=15875){:target='blank'}, you can start the program through remote desktop installed in the previous step.
1. **IBcontroller (Optional)**: Interactive Brokers' trading software (Trader Workstation or IB Gateway) automatically shuts down daily at a specified time. If you don't want to reopen the program everyday, I would suggest you to install [IBcontroller](https://github.com/ib-controller/ib-controller){:target='blank'}. You can set a parameter in IBcontroller to make the trading software "always on".
1. **R Scripting**: It is time to write some R code. Below is a simple R code snippet to place an order.
```r
library(IBrokers)
tws <- twsConnect(verbose = FALSE)
symbol <- 'ABC'
quantity <- 100
lmtPrice <- 10
placeOrder(twsconn=tws,
           Contract=twsSTK(symbol),
           Order=twsOrder(reqIds(tws),
                          "BUY",
                          quantity,
                          "LMT",
                          lmtPrice = as.character(lmtPrice),
                          tif = 'GTC'))
twsDisconnect(tws)
```
What the above script does is simply place a $10 limit buy order of 100 "ABC"  shares. Let's break the functions down: *twsConnect*: This function establishs a connection to the trading software. *twsSTK*: This function is where you can enter the symbol. *reqIds*: This function generates the order ID. *twsOrder*: This function specifies the detail of order. *twsDisconnect*: This function disconnects the connection to the trading software. I am not going to talk about any specific trading strategy. I will defer to you to explore more and build your own trading strategy. Moreover, I highly recommend you to create a version control repository to manage your awesome trading code and sync it remotely to Github or any other providers. Recently, Github announced that they begin to offer free private repository, which should be a good choice for any work considered to be sensitive like what we are doing here.
1. **Task Scheduler**: I use crontab to schedule the server to execute my R script at specific time. The crontab schedule I currently use is
```bash
27 9 * * 1-5 /usr/lib/R/bin/R -f '/home/ubuntu/TraderR/script/trading_robot.R' > '/home/ubuntu/TraderR/script/trading_robot.log' 2>&1
```
which is basically to execute the `trading_robot.R` script at 9:27am Monday to Friday.

1. **Reporting/Dashboad (Optional)**: You can always log into the remote desktop to see your position or account information in the trading software,
however, it requires you to ssh to the server and open the client VNC software. To reduce the time, what I do is build a simple dashboard using [Shiny Server](https://www.rstudio.com/products/shiny/shiny-server/){:target='blank'}. and R package [flexdashboard](https://rmarkdown.rstudio.com/flexdashboard){:target='blank'}. Shiny server can host the dashboard built by flexdashboard on the server. The package flexdashboard provides a much quicker way to build a dashboard than pure shiny application. In my dashboard, it simply shows position, p/l performance, and the log information of trading_robot R script. I can view my dashboard in the browser using the server's URL, which is much faster than the SSH/VNC way.

I hope you enjoy this post/guide and let me know if you have any questions.
