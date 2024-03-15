## ds101

A small boilerplate/project with docker for data science.

### Getting started

```sh
cp config/.env.jupyter.example .env.jupyter
cp config/.env.minio.example .env.minio
cp config/.env.postgres.example .env.postgres
cp config/.env.database.example .env.database
```

Provide appropriate values in the .env files and then run

```sh
docker-compose up -d
```

if you want to persist jupyter settings, make sure to commit the container before taking it down. So

```sh
docker commit jupyter
# and then
docker-compose down
```

Otherwise you can simply stick to `docker-compose start` and `stop`.

**Services:**

- `jupyter`: Jupyter notebooks and jupyter lab where you can do fancy stuff
- `minio`: A key value file store like aws
- `postgres`: Database store
- `metabase`: Cool data science stuff
- `superset`: Another cool visualisations service
- `airflow`: Scheduler and task runner

**Structure:**

- `config`: Contains environment variables, keys, secrets etc
- `jupyter`: Contains notebooks
- `dags`: Task runners

Data folders:
- `data`: Contains data which is mounted on to minio
- `db_data`: Postgres database persistant volumne
- `metabase_data`: Metabase data persistant volume

## Code style

- Use yapf with facebook style
- Pylint for linting

---

Inspired by [data-science-stack](https://github.com/jgoerner/data-science-stack-cookiecutter)
