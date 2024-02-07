# MEILISEARCH + MEILISYNC

Meilisync is used to sync data between databases and Meilisearch indexes.<br />
Installation:
````
debug: true
plugins:
  - meilisync.plugin.Plugin
progress:
  type:
source:
  type: 
  host: 
  username: 
  password: 
  database: 
meilisearch:
  api_url: 
  api_key: 
  insert_size: 
  insert_interval: 
sync:
  - table: 
    index: 
    pk: 
    full: 
sentry:
  dsn: ""
  environment: "production"
````

#### Step 1.
You should select the progress storage to record the last index position.
- `type`: `file` or `redis`, if set to file, another option `path` is required.
- `path`: the file path to store the progress, default is `progress.json`.
- `key`: the redis key to store the progress, default is `meilisync:progress`.
- `dsn`: the redis dsn, default is `redis://localhost:6379/0`.

I think Redis has a bug. I suggest using a file, as it might be a better option.
If you choose to use a file type, you should specify the path (optional) and mount this file to a Docker Compose volume (if you are running with Docker Compose).

#### Step 2.
Source database configuration, currently only support MySQL and PostgreSQL and MongoDB.
- `type`: `mysql` or `postgres` or `mongo`.
- `server_id`: the server id for MySQL binlog, default is `1`.
- `database`: the database name.
- `other keys`: the database connection arguments, MySQL see [asyncmy](https://github.com/long2ice/asyncmy), PostgreSQL
  see [psycopg2](https://www.psycopg.org/docs/usage.html), MongoDB see [motor](https://motor.readthedocs.io/en/stable/).






















