# Data definition

We are providing you with a small set of simplified real-world data. A
database dump is provided that includes the following information:

## Customers

Information about customers that provide their prices, including:

* Customer name

## Ports

Information about ports, including:

* 5-character port code
* Port name

## Prices

Individual customer-provided daily prices between ports, in USD.

* 5-character origin port code
* 5-character destination port code
* The day for which the price is valid
* The price in USD
* Customer name

# Assignment

The task ahead of you comprises 2 main subtasks. Please read both of them carefully and remember to work on both.

1. Develop an [HTTP-based API](#task-1-http-based-api) capable of handling the GET and POST requests described
below in [GET Request Task](#get-request-task) and [POST Request Task](#post-request-task). Our stack is based on Flask, but you are free to choose
anything you like. All data returned is expected to be in JSON format. Please demonstrate your knowledge of SQL (as opposed to using ORM querying tools) in at least one part.
2. Describe the system you would design for handling the [Batch Processing Task](#task-2-batch-processing-task).


## Task 1: HTTP-based API

### GET Request Task

Implement an API endpoint that takes the following parameters:

* day
* customer

And returns a list with the prices that given customer has on the given day, compared against average price across all customers for the same orig/dest combo on the same day. Comparison should include absolute difference of customer price versus average, as well as percentage difference.

    curl "http://127.0.0.1/my-prices?day=2016-01-01&customer=Acme%20Inc."

    [
        {
            "orig_code": "CNSGH",
            "dest_code": "NLRTM",
            "price": 645,
            "average": 1000,
            "absolute_diff": 355,
            "percent_diff": 35.5
        },
        {
            "orig_code": "CNSGH",
            "dest_code": "NLRTM",
            "price": 645,
            "average": 1000,
            "absolute_diff": 355,
            "percent_diff": 35.5
        },
        ...
    ]


### POST Request Task

Implement an API endpoint where you can upload a price, including the following parameters:

* day
* origin_code
* destination_code
* price
* currency

API endpoint should accept prices in different currencies. Convert into USD before
comparing to the average. [https://openexchangerates.org/](https://openexchangerates.org/) provides
a free API for retrieving currency exchange information.

API endpoint should compare uploaded price against average price for the same orig/dest combo on the same day.
Response should include absolute difference of the uploaded price versus average, as well as percentage difference.


    curl --request POST -F "day=2020-01-01" -F "origin_code=CNSGH" -F "destination_code=NLRTM" -F "price=645" -F "currency=USD" "http://127.0.0.1/compare-price"

    {
        "average": 1000,
        "absolute_diff": 355,
        "percent_diff": 35.5
    }


## Task 2: Batch Processing Task

Following up on the [POST Request Task](#post-request-task), imagine if you had to receive and compare tens of thousands of prices, conforming to a similar format. Describe, using a couple of paragraphs, how you would design the system to be able to handle those requirements. Which factors do you need to take into consideration?


# Extra details

* Keep your solution in a Version Control System of your
  choice. *Provide the solution as a public repository that can be
  easily cloned by our development team.*

* Provide any instructions needed to set up the system in `README.md`.

* Ensure the API handles errors and edge cases properly.

* Use dates in YYYY-MM-DD format for the API. There is no need for more
  complicated date processing.

* You are encouraged to modify or extend the database schema if you think a different model fits the task better.

* If you have any questions, please don't hesitate to contact us at tech-recruitment@xeneta.com

* Please let us know how much time you spent on the task, and of any difficulties that you ran into.


# Initial setup

We have provided a simple Docker setup for you, which will start a
PostgreSQL instance populated with the assignment data. You don't have
to use it, but you might find it convenient. If you decide to use
something else, make sure to include instructions on how to set it up.

You can execute the provided Dockerfile by running:

```bash
docker build -t xeneta-my-rates .
```

This will create a container with the name *xeneta-my-rates*, which you can
start in the following way:

```bash
docker run -p 0.0.0.0:5432:5432 --name xeneta-my-rates xeneta-my-rates
```

You can connect to the exposed Postgres instance on the Docker host IP address,
usually *127.0.0.1* or *172.17.0.1*. It is started with the default user `postgres` and `xeneta-my-rates` password.

```bash
PGPASSWORD=xeneta-my-rates psql -h 127.0.0.1 -U postgres
```

alternatively, use `docker exec` if you do not have `psql` installed:

```bash
docker exec -e PGPASSWORD=xeneta-my-rates -it xeneta-my-rates psql
```

Keep in mind that any data written in the Docker container will
disappear when it shuts down. The next time you run it, it will start
with a clean state.
