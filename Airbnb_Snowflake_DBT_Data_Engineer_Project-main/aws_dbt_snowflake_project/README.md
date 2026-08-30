# Airbnb dbt Project

This dbt project transforms Airbnb booking, host, and listing data stored in Snowflake.

The project follows a Bronze → Silver → Gold architecture and includes:

- Incremental dbt models
- SCD Type 2 snapshots
- Custom dbt macros
- Data quality tests
- Jinja templating
- Analytics-ready Gold models
