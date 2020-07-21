# bitcoin-analyzer project

By [Clément Ronzon](https://www.linkedin.com/in/clemrz/). Licensed under [MIT License](https://choosealicense.com/licenses/mit/).

This is the implementation of a Bitcoin price analyzer.
This price analyzer displays a graph with the price trends of the Bitcoin.

It is set to display the last 24 hours by, a datepicker range filter allows the user to change the timeline range.

When the filter is changed, the graph is redrawn to reflect the new filter.

The price analyzer fetches historical Bitcoin prices from a public URL (Yahoo). It stores it into a database (cron job). A REST API serves the data to the front-end.

## Deployment

This repository wraps two submodules:

  - Front-end: [bitcoin-analyzer-front](https://github.com/ClemRz/bitcoin-analyzer-front)
  - Back-end: [bitcoin-analyzer-back](https://github.com/ClemRz/bitcoin-analyzer-back)
  
In each of these repositories you will find instructions on how to deploy them for dev and prod individually.

The following instructions are only if you want to deploy using Docker Compose.

ToDo