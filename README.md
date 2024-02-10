# MEILISEARCH DOCUMENTATION

Meilisync is used to sync data between databases and Meilisearch indexes.<br />
### 🌐 Step 1: Installation Meilisearch with Docker Compose.
````
services:
  meilisearch:
    image: getmeili/meilisearch:latest
    volumes:
      - ./data.ms:/data.ms
    ports:
      - "7700:7700"
    environment:
      - MEILI_MASTER_KEY=${MEILI_MASTER_KEY:-ms}
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:7700"]
      interval: 10s
      timeout: 5s
      retries: 5
````
You can get master key by create the account and open the dashboard at [meilisearch](https://cloud.meilisearch.com/login)
_______

### 🌐 Step 2: Installation Meilisync with Docker Compose.
Create config.yml and docker-compose.yml, ensuring they are mounted with the Docker service.
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

````
services:
  meilisync:
    platform: linux/amd64
    image: long2ice/meilisync
    volumes:
      - ./config.yml:/meilisync/config.yml
      - ./progress.json:/meilisync/progress.json
````
If you're using MongoDB as the data source, you should create a progress.json file that contains
````
{}
````
to your project and mounted with the Docker service.
_______
### 📝 Step 3: Meilisync Configuration.
##### 3.1 Progress
You should select the progress storage to record the last index position.
````
- type : `file` or `redis`, if set to file, another option `path` is required.
- path : the file path to store the progress, default is `progress.json`.
- key  : the redis key to store the progress, default is `meilisync:progress`.
- dsn  : the redis dsn, default is `redis://localhost:6379/0`.
````

I think Redis has a bug. I suggest using a file, as it might be a better option.
If you choose to use a file type, you should specify the path (optional) and mount this file to a Docker Compose volume (if you are running with Docker Compose).

##### 3.2 Source
Source database configuration, currently only support MySQL and PostgreSQL and MongoDB.
````
- type       : `mysql` or `postgres` or `mongo`.
- server_id  : the server id for MySQL binlog, default is `1`.
- database   : the database name.
- other keys : the database connection arguments,
````
MySQL see [asyncmy](https://github.com/long2ice/asyncmy),
PostgreSQL see [psycopg2](https://www.psycopg.org/docs/usage.html),
MongoDB see [motor](https://motor.readthedocs.io/en/stable/).

If you choose to use MongoDB, you should open MongoDB Replica to stream the documents with Meilisync.<br/>
Hint: You can use MongoDB Atlas to set up the upstream because it's very easy to test your code, and you won't need to set up MongoDB yourself.

##### 3.3 Meilisearch configuration.
````
- api_url         : the Meilisearch API URL.
- api_key         : the Meilisearch API key.
- insert_size     : insert after collecting this many documents, optional.
- insert_interval : insert after this many seconds have passed, optional.
````
If nether `insert_size` nor `insert_interval` is set, it will insert each document immediately.

If you prefer performance, just set and increase `insert_size` and `insert_interval`. The insert will be made as long as
one of the conditions is met.

##### 3.4 Sync
The sync configuration, you can add multiple sync tasks.
````
- table   : the database table name or collection name.
- index   : the Meilisearch index name, if not set, it will use the table name.
- full    : whether to do a full sync, default is `false`.
- fields  : the fields to sync, if not set, it will sync all fields.
- plugins : the table level plugins, optional.
````
_______
### 🅰️ Step 4: Setup Meilisearch Admin UI
We will use open source for easier and faster implementation: https://github.com/riccox/meilisearch-ui?ref=producthunt <br/>

Docker
````
docker pull riccoxie/meilisearch-ui:latest

docker run -d --restart=always --name="meilisearch-ui" -p <your-port>:24900 riccoxie/meilisearch-ui:latest
````


For Development
````
git clone git@github.com:riccox/meilisearch-ui.git

cd meilisearch-ui

pnpm install

pnpm run dev
````
_______
### 💻 Step 5: Setup Instantsearch (Front-end)












