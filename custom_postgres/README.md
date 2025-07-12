Welcome to your new dbt project!

### Using the starter project

Try running the following commands:
- dbt run
- dbt test


### Resources:
- Learn more about dbt [in the docs](https://docs.getdbt.com/docs/introduction)
- Check out [Discourse](https://discourse.getdbt.com/) for commonly asked questions and answers
- Join the [chat](https://community.getdbt.com/) on Slack for live discussions and support
- Find [dbt events](https://events.getdbt.com) near you
- Check out [the blog](https://blog.getdbt.com/) for the latest news on dbt's development and best practices

# dbt Integration with PostgreSQL

This folder sets up a `dbt` project to connect and transform data in our destination PostgreSQL container.

### What we’ve done so far:
- Created a dbt project inside the `custom_postgres` folder using `dbt init`
- Set up the `profiles.yml` file to connect to the destination Postgres container
- Updated `dbt_project.yml` with project-specific configuration
- Verified the connection to the database

### What’s next:
- Define and test models in the `/models` folder
- Use `dbt run`, `dbt test`, and `dbt build` to transform and validate data
- Begin documenting models and creating sources