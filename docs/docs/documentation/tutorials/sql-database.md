---
title: SQL Database Integration - sqlite3, MySQL, PostgreSQL
sidebar_position: 20
---

# SQL Database Integration

This tutorial will guide you through setting up a SQL database agent in Solace Agent Mesh (SAM) that can answer natural language queries about a sample coffee company database. It provides some example data to setup a SQLite database, but you can use the same approach to connect to other database types like MySQL and PostgreSQL.

## Prerequisites

Before starting this tutorial, make sure you have:
- [Installed Solace Agent Mesh and the SAM CLI](../getting-started/installation.md)
- [Created a new Solace Agent Mesh project](../getting-started/quick-start.md)

## Installing the SQL Database Plugin

First, add the SQL Database plugin to your SAM project:

```sh
solace-agent-mesh plugin add sam_sql_database --pip -u git+https://github.com/SolaceLabs/solace-agent-mesh-core-plugins#subdirectory=sam-sql-database
```

Note that you can replace the Solace Agent Mesh CLI command with `sam` as a shortcut.

## Creating a SQL Database Agent

Next, create a new agent instance based on the SQL database template:

```sh
sam add agent abc_coffee_info --copy-from sam_sql_database:sql_database
```

This command will create a new configuration file at `configs/agents/abc_coffee_info.yaml`.

## Downloading Example Data

For this tutorial, we'll use a sample SQLite database for a fictional coffee company called ABC Coffee Co. Follow these steps to download the example data:

1. Visit [this link](https://download-directory.github.io/?url=https%3A%2F%2Fgithub.com%2FSolaceLabs%2Fsolace-agent-mesh-core-plugins%2Ftree%2Fmain%2Fsam-sql-database%2Fexample-data) to download the example data
2. The link will open a page allowing you to download a ZIP file containing the example data
3. Save the ZIP file to your computer
4. Unzip the file to a directory of your choice (preferably in the root directory of your Solace Agent Mesh project)
5. This should create an abc_coffee_co directory with many CSV files inside

## Configuring the Agent

Now you need to update the agent configuration to use the SQLite database and import the CSV files. Open the `configs/agents/abc_coffee_info.yaml` file and make the following changes:

1. Set the database type to SQLite
2. Point to the directory where you unzipped the example data

Here's what you need to modify in the configuration file:

```yaml
      # Find the component_config section for the action_request_processor and update these values:
      - component_name: action_request_processor
        component_config: 
            # Other configuration options (mostly specified via env vars)...
            csv_directories:
                - /path/to/your/unzipped/data
```

Make sure to replace `/path/to/your/unzipped/data` with the actual path where you unzipped the example data. In this example, if you put the zip file in the root directory of your Solace Agent Mesh project, you can use `abc_coffee_co`.

## Setting Environment Variables

The SQL Database agent requires several environment variables. Create or update your `.env` file with the following:

```
ABC_COFFEE_INFO_DB_TYPE=sqlite
ABC_COFFEE_INFO_DB_NAME=abc_coffee.db
ABC_COFFEE_INFO_DB_PURPOSE="ABC Coffee Co. sales and operations database"
ABC_COFFEE_INFO_DB_DESCRIPTION="Contains information about ABC Coffee Co. products, sales, customers, employees, and store locations."
# You can leave other environment variables as unset or empty
```

For SQLite, it just uses a local file with no username or password. If you're using MySQL or PostgreSQL, you'll need to provide the appropriate environment variables for your database.

## Running the Agent

Now you can start Solace Agent Mesh with your new SQL database agent:

```sh
sam run -eb
```

The `-e` flag loads environment variables from the `.env` file, and the `-b` flag will rebuild the sam config files

## Interacting with the Database

Once SAM is running, you can interact with the ABC Coffee database through the web interface at http://127.0.0.1:5001.

You can ask natural language questions about the ABC Coffee Co. database, such as:

- "How many customers does ABC Coffee have?"
- "What are the top-selling products?"
- "Show me the sales by region"

Try creating reports by asking questions like:

- "Create a report of our sales in 2024"

The SQL Database agent will convert your natural language questions into SQL queries, execute them against the database, and return the results.

## Database Schema

The ABC Coffee Co. database contains the following tables:

    customers
    employees
    inventory
    order_history
    order_items
    orders
    product_categories
    product_specifications
    products
    sales_call_logs
    support_ticket_comments
    support_tickets

The schemas for them all will be learned by the agent when it starts up.

## Conclusion

You've successfully set up a SQL Database agent in Solace Agent Mesh that can answer natural language queries about the ABC Coffee Co. database. This same approach can be used to connect to other database types like MySQL and PostgreSQL by adjusting the configuration and environment variables accordingly.

For more information about the SQL Database plugin, see the [plugin README](https://github.com/SolaceLabs/solace-agent-mesh-core-plugins/blob/main/sam-sql-database/README.md).
