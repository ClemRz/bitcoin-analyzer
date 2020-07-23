# Design document - Bitcoin price analyzer

## The ask

### Description

You must implement a Bitcoin price analyzer. This must be implemented as a Web application with a frontend and backend component. The price analyzer must fetch historical Bitcoin prices from a public URL and display a graph with the price trends.

The default filter is to display the last 24 hours, but the UI must allow the user to change the range, making sure the range stays within sane values (start date older than end date, dataset not too big as to slow down the UI, etc). All entry must be validated and human readable error messages must be displayed when necessary.

When the display filter is changed, the graph must be redrawn to reflect the new filter.

### Technical Requirements

  - The backend can be implemented in any language of your choosing. Use of frameworks is allowed.
  - The frontend must be implemented in JavaScript. Use of frameworks is allowed.
  - Historical prices for Bitcoin can be fetched from any publicly available source, such as https://www.kaggle.com/mczielinski/bitcoin-historical-data (please note that we are only using kaggle.com as an example source. Kaggle offers their own data analyzing service, but we don't want you to use it. We are only interested in using their public feed of the bitcoin historical data)
  - Prices must be refreshed automatically every day.
  - The application must take care of always having historical data ready, before allowing requests. This is to prevent users from attempting to use the application with an empty dataset.
  - Refreshing the historical data must not block or interfere with the display of said data.
  - Architecture must allow for future growth. Think this application will be a smash hit and will need to be deployed to thousands of servers.

### Other Requirements

  - Application must be delivered with a set of instructions on how to run it locally.
  - Special care must be taken to make sure that it is easy for other developers to run your application locally. The easier it is, the better.
  - Document any assumption. For example if you decide to use JSON as the data interchange format, document the reason why.
  - Code it as if it was a real production application. The purpose behind the code challenge is for us to get a sense of your design and development skills. Don't over engineer, but also don't underengineer.
  - Your solution(s) should be virtualized so it can be executed in a macOS or Linux development machine. If using Docker, create Dockerfiles for each component. The Dockerfiles should be based on images from public sources such as open source projects. Do not use your personal Docker base images or closed source Docker images. If your implementation works with multiple Docker containers, please use Docker Compose to orchestrate your solution properly. If not virtualized, please deploy this somewhere public where we can see it in action.

### Deliverables

  - Zip file with all the code assets.
  - Instructions on how to deploy locally. Consider a macOS/Linux target.
  - A text document briefly describing your solution as well as your main architectural and design decisions.
  - Any additional documentation that might help us understand your code (diagrams, assumptions, etc).

---
  
# Grooming

I broke down the work into several tasks, here is an estimate for each one of them:

  - 2h - Dev. env. setup
  - 12h - Front-end:
     + 2h - model/http query
     + 6h - controllers & views
     + 4h - styling
  - 14h - Back-end:
     + 2h - DB initialization
     + 2h - DB transactions module
     + 2h - http transactions module
     + 2h - caching module
     + 5h - API
     + 1h - Cron job setup
  - 2h - Documentation

This sums 30h, I'd add a 20% more for unforeseen situations which brings it up to 36 hours (4.5d).

---

# Design

## Assumptions

The following assumptions were made during development of the MVP. For some of them the code allows an easy resolution.

 - The app makes available only one currency: USD, assuming our market is domestic (US). It would be fairly easy to evolve the application for more currencies;
 - the value of the bitcoin (BTC) price is rounded to 3 decimal digits. More digits would be hardly readable but it would be easy to change that;
 - the app displays only the "close" value (V.S. high, low, open values). It would be fairly easy to add more values and display/serve them;
 - the app will display a timeline translated to the user's browser time zone;
 - the information served by the API doesn't require authentication nor encryption. The data is not sensitive at all (already present publicly);

## Out of scope

The following items are out of scope of the MVP:

 - unit testing, integration testing and UI testing;
 - CI and CD pipelines;
 - accessibility (a11y);
 - internationalization (i18n);
 - responsiveness.

## Architecture

The architectural decision have been strongly influenced by the available tools and products at the moment.

The back-end is coded in PHP version `7.3` running on a Apache server.
The cached values are coming from Yahoo (third party) chart api and are stored in a MySQL database version `8.0`.
The duo PHP-MySQL is economic and robust for a starter. 

The front-end is a React application, version `3.4.1` compiled with Node version `3.12`.
React is, as I wrote, one of the most advanced front-end frameworks IMO.

For easy setup and scaling it has been chosen to virtualize those 3 main modules in orchestrated Docker containers (Docker version `19.03.0`).
Documentation for both development and production environments is available in the [repository](https://github.com/ClemRz/bitcoin-analyzer).

To facilitate future collaboration, all the code and instructions are versioned using git and available to the public on [Github](https://github.com).

## Back-end

The back-end application has three entry points:

 - a REST API meant to serve data to web applications;
 - an initialization script meant to be executed via CLI or by the Docker orchestration script;
 - an updater script meant to be executed periodically via a Cron job to keep the database up to date. It can also be executed by CLI when needed.

The database is composed of three very simple tables, each one containing a different granularity of data. All of them have two columns for unix timestamps and bitcoin USD value.

The granularity of data stored, one day, one hour and one minute intervals, allows for an easy clusterization and light data transfer and display.

The interaction between the entry points (API, CLI, cron) and the database or the third-party service (Yahoo) is performed in a MVC design pattern.

The API serves the data via the `json` format which, along with `xml` is very popular. PHP has a set of core functions to translate form and to `json` which makes it handy. The JavaScript interprets it naturally. Nevertheless, adding another format (like `xml`) would be very easy thanks to the architectural runway in place.

[ToDo back-end classes and db diagram]

## Front-end

The front-end is a one-page application (no router needed) which leverages asynchronous JavaScript to obtain the numerical data which then is displayed as a chart. 

The chart component as well as the date-picker are external dependencies and most of the primitive components (alerts, accordion, etc.) are React bootstrap components.

A home-made wrapper for the `fetch` statement (`Query` component) allows reusability an versatility. It also solves race-conditions related issues by performing in-flight abortion of a request before sending another. 

The chart component is also wrapped in a layer (`ChartQuery` component) that controls the data fetched before it is displayed.

Finally the custom alert (`CustomAlert` component) translates usage and API errors into readable UI messages with optionally more in-depth information. 

# What's next

Having more time I would certainly have secured the front and the back ends with SSL protocol.

I am not sure PHP/MySQL is the most efficient choice for this kind of application. For instance the relational aspect of MySQL is not used at all, a non-relational database could be a better choice. Also a functional programming language might be faster when it comes to treat a good amount of data.

The out-of-scope items could be treated, starting with testing and CI pipeline. When more developers contribute to a project it helps keeping the quality of the product under control.

A11y, i18n and responsiveness are becoming quite important subjects and would be good enhancements to this application.

-- Thank you for reading --

---

July 24, 2020, by [Clément Ronzon](https://www.linkedin.com/in/clemrz/). Licensed under [MIT License](https://choosealicense.com/licenses/mit/).