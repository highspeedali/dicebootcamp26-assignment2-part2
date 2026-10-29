# Assignment 2 - Part 2 Multi-Container Application Deployment 
### Create a Docker file for the web application container which should include the necessary dependencies to run the web application. The web application should display a simple "Hello, World!" message when accessed from a web browser.


```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ ls
web
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ cd web/
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/web$ ls
Dockerfile  README.md  main.py  pyproject.toml  src  uv.lock
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/web$ cat main.py
import uvicorn
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello, World!"}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8080)
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$
```
&nbsp;
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/web$ cat Dockerfile
FROM python:3.12-slim

# 1. Install uv by copying it from the official image
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# 2. Set the working directory
WORKDIR /app

# 3. Copy configuration files first (helps leverage Docker layer caching)
COPY pyproject.toml uv.lock ./

# 4. Explicitly tell uv to install the declared dependencies
RUN uv sync --frozen --no-cache  --no-install-project

# 5. Copy the rest of your application code
COPY . /app

# 6. Run the FastAPI application using the installed environment
CMD ["uv", "run", "main.py", "--port", "8080", "--host", "0.0.0.0"]
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/web$
```
### Create a Docker file for the database container which should include the necessary dependencies to run the database. You can use any database of your choice (e.g.,MySQL, PostgreSQL, MongoDB, etc.).

```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/db$ pwd
/home/ubuntu/dicebootcamp26-assignment2-part2/db
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/db$ cat Dockerfile
# Use the official MySQL image
FROM mysql:8.4

# Configure MySQL
ENV MYSQL_ROOT_PASSWORD=rootpassword
ENV MYSQL_DATABASE=mydatabase
ENV MYSQL_USER=dbuser
ENV MYSQL_PASSWORD=dbpassword

# Expose MySQL port
EXPOSE 3306

# MySQL starts automatically using the default entrypoint
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/db$
```
&nbsp;
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/db$ docker build -t custom-mysql .
[+] Building 48.1s (5/5) FINISHED                                                                                                                                                                docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                                       0.0s
 => => transferring dockerfile: 315B                                                                                                                                                                       0.0s
 => [internal] load metadata for docker.io/library/mysql:8.4                                                                                                                                               5.1s
 => [internal] load .dockerignore                                                                                                                                                                          0.0s
 => => transferring context: 2B                                                                                                                                                                            0.0s
 => [1/1] FROM docker.io/library/mysql:8.4@sha256:a0225d1ea6631c8bc2385dc4c2faba17bbf1834ecf369e1659a9a382bd8d5dd7                                                                                        22.7s
 => => resolve docker.io/library/mysql:8.4@sha256:a0225d1ea6631c8bc2385dc4c2faba17bbf1834ecf369e1659a9a382bd8d5dd7                                                                                         0.2s
 => => sha256:35515671e46bf4c664fac114fd102c80f36a9971371c823d86de5030b2cc925e 51.67MB / 51.67MB                                                                                                           8.5s
 => => sha256:c12829836f3e8bd1303c214219cfd711c907a9e832ef748c16171bfd773d9dd6 320B / 320B                                                                                                                 0.5s
 => => sha256:c9ef008e7b8be94229f420751e8b23b4f2fe460f16976757be7c2d766d4c2ed1 5.23kB / 5.23kB                                                                                                             1.5s
 => => sha256:fb258f8898e780972b965e2ecc1f62a83446b1706654fe6efddf0887a9fc61a6 132.41MB / 132.41MB                                                                                                        22.4s
 => => sha256:1610bd8e525d37f7b01147980f0f660538a95e978eee8351a908a00d143c2910 47.19MB / 47.19MB                                                                                                          14.0s
 => => sha256:4143b5578588023307b710623d1362a77af4557a51c1c72983aca1fe5d5969b4 885B / 885B                                                                                                                 0.6s
 => => sha256:e752cc14300e79365e777ca9f56fdd5049e87964f423ca9b30cfc2f34d998130 783.57kB / 783.57kB                                                                                                         0.9s
 => => sha256:b2c76224d19e141c050f669edb6788d78760198453320fd6cb3bbc7720911e1b 6.19MB / 6.19MB                                                                                                             2.3s
 => => sha256:4a4f18e225a8e100697eefb85ef71bc2675ac5fc5c7d233725b2557ec3a6e682 2.61kB / 2.61kB                                                                                                             0.5s
 => => sha256:28cc43b6d59c8dd0f25c63f26ea3ded99e9e6ee8b2f7b7e92c1b43134d3016ae 334B / 334B                                                                                                                 0.5s
 => exporting to image                                                                                                                                                                                    42.4s
 => => exporting layers                                                                                                                                                                                    0.0s
 => => exporting manifest sha256:39691d2f85f1ac0602d7f847d9738b4873c560579ae23c0f02a471271971ace8                                                                                                          0.0s
 => => exporting config sha256:dc72fa99edbdfb648aa797b09d2017804ea0533fc7af259e92b1e1eabc360855                                                                                                            0.0s
 => => exporting attestation manifest sha256:2c36f241757ff8b3ae9502f4fe35834c77f5bfa1fc9ae576e67dafe8eb033c11                                                                                              0.0s
 => => exporting manifest list sha256:8bf247318e92d96b1d905f1b9a4621427a3ebf23e2423dcea436d66e27f42b7a                                                                                                     0.0s
 => => naming to docker.io/library/custom-mysql:latest                                                                                                                                                     0.0s
 => => unpacking to docker.io/library/custom-mysql:latest                                                                                                                                                 42.3s

 2 warnings found (use docker --debug to expand):
 - SecretsUsedInArgOrEnv: Do not use ARG or ENV instructions for sensitive data (ENV "MYSQL_ROOT_PASSWORD") (line 5)
 - SecretsUsedInArgOrEnv: Do not use ARG or ENV instructions for sensitive data (ENV "MYSQL_PASSWORD") (line 8)
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/db$
```
&nbsp;
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/db$ docker run -d --name mysql_container -p 3306:3306 custom-mysql
d1d8f3a38f0575de1f663d391d1164e28af6ae91fb153739692d67c839cd0103
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/db$
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/db$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                                                    NAMES
d1d8f3a38f05   custom-mysql   "docker-entrypoint.s…"   8 seconds ago   Up 7 seconds   0.0.0.0:3306->3306/tcp, [::]:3306->3306/tcp, 33060/tcp   mysql_container
```
&nbsp;
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2/db$ docker logs -f mysql_container
2026-10-29 15:47:17+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.13-1.el9 started.
2026-10-29 15:47:18+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2026-10-29 15:47:18+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.13-1.el9 started.
2026-10-29 15:47:18+00:00 [Note] [Entrypoint]: Initializing database files
2026-10-29T15:47:18.304961Z 0 [System] [MY-015017] [Server] MySQL Server Initialization - start.
2026-10-29T15:47:18.306726Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.4.13) initializing of server in progress as process 80
2026-10-29T15:47:18.306728Z 0 [System] [MY-015150] [Server] Server version with update: '8.4.13.0'
2026-10-29T15:47:18.313307Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2026-10-29T15:47:18.616571Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2026-10-29T15:47:19.540711Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
2026-10-29T15:47:21.458302Z 0 [System] [MY-015018] [Server] MySQL Server Initialization - end.
2026-10-29 15:47:21+00:00 [Note] [Entrypoint]: Database files initialized
2026-10-29 15:47:21+00:00 [Note] [Entrypoint]: Starting temporary server
2026-10-29T15:47:21.521635Z 0 [System] [MY-015015] [Server] MySQL Server - start.
2026-10-29T15:47:21.867197Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.4.13) starting as process 119
2026-10-29T15:47:21.867201Z 0 [System] [MY-015150] [Server] Server version with update: '8.4.13.0'
2026-10-29T15:47:21.888472Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2026-10-29T15:47:22.287029Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2026-10-29T15:47:22.611388Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2026-10-29T15:47:22.611477Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2026-10-29T15:47:22.615866Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2026-10-29T15:47:22.647358Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: /var/run/mysqld/mysqlx.sock
2026-10-29T15:47:22.647904Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.4.13'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server - GPL.
2026-10-29 15:47:22+00:00 [Note] [Entrypoint]: Temporary server started.
'/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
2026-10-29 15:47:25+00:00 [Note] [Entrypoint]: Creating database mydatabase
2026-10-29 15:47:25+00:00 [Note] [Entrypoint]: Creating user dbuser
2026-10-29 15:47:25+00:00 [Note] [Entrypoint]: Giving user dbuser access to schema mydatabase

2026-10-29 15:47:25+00:00 [Note] [Entrypoint]: Stopping temporary server
2026-10-29T15:47:25.394479Z 13 [System] [MY-013172] [Server] Received SHUTDOWN from user root. Shutting down mysqld (Version: 8.4.13).
2026-10-29T15:47:26.136763Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.4.13)  MySQL Community Server - GPL.
2026-10-29T15:47:26.136832Z 0 [System] [MY-015016] [Server] MySQL Server - end.
2026-10-29 15:47:26+00:00 [Note] [Entrypoint]: Temporary server stopped

2026-10-29 15:47:26+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.

2026-10-29T15:47:26.446222Z 0 [System] [MY-015015] [Server] MySQL Server - start.
2026-10-29T15:47:26.762269Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.4.13) starting as process 1
2026-10-29T15:47:26.762275Z 0 [System] [MY-015150] [Server] Server version with update: '8.4.13.0'
2026-10-29T15:47:26.768586Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2026-10-29T15:47:27.078426Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2026-10-29T15:47:27.352863Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2026-10-29T15:47:27.352937Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2026-10-29T15:47:27.355790Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2026-10-29T15:47:27.382209Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.4.13'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
2026-10-29T15:47:27.382798Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
```

### Create a docker-compose.yml file that defines the two services - web and database. The web service should be linked to the database service using Docker Compose networking.

```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ pwd
/home/ubuntu/dicebootcamp26-assignment2-part2
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ ls
db  docker-compose.yml  web
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ cat docker-compose.yml
# Commented the version as its deprecated. Was also giving warnings during execution.
#version: '3.9'

services:
  web:
    build: ./web
    image: web:latest
    container_name: web
    ports:
      - "8080:8080"
    networks:
      - my_network
    depends_on:
      - database

  database:
    build: ./db
    image: database:latest
    container_name: database
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: mydatabase
      MYSQL_USER: dbuser
      MYSQL_PASSWORD: dbpassword
    ports:
      - "3306:3306"
    networks:
      - my_network
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:

networks:
  my_network:
    driver: bridge
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$
```

### Use the docker-compose up command to build and run the application. Verify that the web application is accessible from a web browser and that the database is running.

```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker compose up
[+] up 2/2
 ! Image database:latest pull access denied for database, repository does not exist or may require 'docker login'                                                     2.0s
 ! Image web:latest      pull access denied for web, repository does not exist or may require 'docker login'                                                          2.5s
[+] Building 5.7s (21/21) FINISHED
 => [internal] load local bake definitions                                                                                                                           0.0s
 => => reading from stdin 1.05kB                                                                                                                                     0.0s
 => [database internal] load build definition from Dockerfile                                                                                                        0.0s
 => => transferring dockerfile: 315B                                                                                                                                 0.0s
 => [web internal] load build definition from Dockerfile                                                                                                             0.0s
 => => transferring dockerfile: 629B                                                                                                                                 0.0s
 => [database internal] load metadata for docker.io/library/mysql:8.4                                                                                                1.3s
 => [web internal] load metadata for ghcr.io/astral-sh/uv:latest                                                                                                     2.0s
 => [web internal] load metadata for docker.io/library/python:3.12-slim                                                                                              1.3s
 => [database internal] load .dockerignore                                                                                                                           0.0s
 => => transferring context: 2B                                                                                                                                      0.0s
 => CACHED [database 1/1] FROM docker.io/library/mysql:8.4@sha256:a0225d1ea6631c8bc2385dc4c2faba17bbf1834ecf369e1659a9a382bd8d5dd7                                   0.0s
 => => resolve docker.io/library/mysql:8.4@sha256:a0225d1ea6631c8bc2385dc4c2faba17bbf1834ecf369e1659a9a382bd8d5dd7                                                   0.0s
 => [database] exporting to image                                                                                                                                    0.1s
 => => exporting layers                                                                                                                                              0.0s
 => => exporting manifest sha256:6414949a7532054c503e79161fe943639c71c24c36f35cd92e56a319ce3a7428                                                                    0.0s
 => => exporting config sha256:244da48268b9e9b4fd88ee59294a048adfa3ab7fb02adefd326d8c5cd8c7f05f                                                                      0.0s
 => => exporting attestation manifest sha256:c6ca64d22958614843a7e04a2bf095b76d096c5b51d7bb51e72401a079634b08                                                        0.0s
 => => exporting manifest list sha256:a076fe9454b659eaa71bffdbc8136666726d18a7c9f41f483e780455eb85aeff                                                               0.0s
 => => naming to docker.io/library/database:latest                                                                                                                   0.0s
 => => unpacking to docker.io/library/database:latest                                                                                                                0.0s
 => [database] resolving provenance for metadata file                                                                                                                0.0s
 => [web internal] load .dockerignore                                                                                                                                0.0s
 => => transferring context: 2B                                                                                                                                      0.0s
 => [web] FROM ghcr.io/astral-sh/uv:latest@sha256:af22a52f5eafef3d78e551599d405c02df61919d18028f0cea8eda93bc9986d0                                                   0.1s
 => => resolve ghcr.io/astral-sh/uv:latest@sha256:af22a52f5eafef3d78e551599d405c02df61919d18028f0cea8eda93bc9986d0                                                   0.1s
 => [web internal] load build context                                                                                                                                0.1s
 => => transferring context: 76.73kB                                                                                                                                 0.1s
 => [web stage-0 1/6] FROM docker.io/library/python:3.12-slim@sha256:aa943ce411423c7582288f18707ff66eddcbaeb510502d747a72aeb34f30d159                                0.1s
 => => resolve docker.io/library/python:3.12-slim@sha256:aa943ce411423c7582288f18707ff66eddcbaeb510502d747a72aeb34f30d159                                            0.1s
 => CACHED [web stage-0 2/6] COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/                                                                                  0.0s
 => CACHED [web stage-0 3/6] WORKDIR /app                                                                                                                            0.0s
 => CACHED [web stage-0 4/6] COPY pyproject.toml uv.lock ./                                                                                                          0.0s
 => CACHED [web stage-0 5/6] RUN uv sync --frozen --no-cache  --no-install-project                                                                                   0.0s
 => CACHED [web stage-0 6/6] COPY . /app                                                                                                                             0.0s
 => [web] exporting to image                                                                                                                                         3.4s
 => => exporting layers                                                                                                                                              0.0s
 => => exporting manifest sha256:78b75d493d64700b6e9c074fc7e71b7cd4c9ed19505776be968542a44397da21                                                                    0.0s
 => => exporting config sha256:28984478cfdbd1bce206733a5c98c0953ce35966bb7c05eecde708550be1b07f                                                                      0.0s
 => => exporting attestation manifest sha256:c45e2c06590826a4de5792933f4f902e0e18ef96eed6b05a078034cdb5995bc4                                                        0.0s
 => => exporting manifest list sha256:25216ea4a83002a195a9d6212056ab35770397276e907ff45de0cc611b6eb9ba                                                               0.0s
[+] up 6/6ing to docker.io/library/web:latest                                                                                                                        0.0s
 ✔ Image database:latest                               Built                                                                                                          8.4s
 ✔ Image web:latest                                    Built                                                                                                          8.4s
 ✔ Network dicebootcamp26-assignment2-part2_my_network Created                                                                                                        0.1s
 ✔ Volume dicebootcamp26-assignment2-part2_mysql_data  Created                                                                                                        0.0s
 ✔ Container database                                  Created                                                                                                        0.4s
 ✔ Container web                                       Created                                                                                                        0.1s
Attaching to database, web
database  | 2026-10-29 19:53:33+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.13-1.el9 started.
web       | warning: Ignoring existing virtual environment linked to non-existent Python interpreter: `.venv/bin/python` -> `/home/ubuntu/.local/share/uv/python/cpython-3.14-linux-x86_64-gnu/bin/python3.14`
database  | 2026-10-29 19:53:34+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
database  | 2026-10-29 19:53:34+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.13-1.el9 started.
database  | 2026-10-29 19:53:34+00:00 [Note] [Entrypoint]: Initializing database files
database  | 2026-10-29T19:53:34.566748Z 0 [System] [MY-015017] [Server] MySQL Server Initialization - start.
database  | 2026-10-29T19:53:34.568798Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.4.13) initializing of server in progress as process 79
database  | 2026-10-29T19:53:34.568800Z 0 [System] [MY-015150] [Server] Server version with update: '8.4.13.0'
database  | 2026-10-29T19:53:34.589496Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
database  | 2026-10-29T19:53:34.990933Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
web       | Downloading cpython-3.14.10-linux-x86_64-gnu (download) (35.1MiB)
database  | 2026-10-29T19:53:36.346988Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
database  | 2026-10-29T19:53:38.890130Z 0 [System] [MY-015018] [Server] MySQL Server Initialization - end.
database  | 2026-10-29 19:53:38+00:00 [Note] [Entrypoint]: Database files initialized
database  | 2026-10-29 19:53:38+00:00 [Note] [Entrypoint]: Starting temporary server
database  | 2026-10-29T19:53:38.976009Z 0 [System] [MY-015015] [Server] MySQL Server - start.
database  | 2026-10-29T19:53:39.960380Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.4.13) starting as process 118
database  | 2026-10-29T19:53:39.960385Z 0 [System] [MY-015150] [Server] Server version with update: '8.4.13.0'
database  | 2026-10-29T19:53:40.018074Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
web       |  Downloaded cpython-3.14.10-linux-x86_64-gnu (download)
web       | Using CPython 3.14.10
web       | Removed virtual environment at: .venv
web       | Creating virtual environment at: .venv
web       |    Building web @ file:///app
web       | warning: `build_system.requires = ["uv-build>=0.12.19,<0.13.0"]` does not contain the current uv version 0.13.6
database  | 2026-10-29T19:53:41.852471Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
web       |       Built web @ file:///app
database  | 2026-10-29T19:53:42.460051Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
database  | 2026-10-29T19:53:42.460107Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
database  | 2026-10-29T19:53:42.466056Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
database  | 2026-10-29T19:53:42.518057Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.4.13'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server - GPL.
database  | 2026-10-29T19:53:42.518189Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: /var/run/mysqld/mysqlx.sock
database  | 2026-10-29 19:53:42+00:00 [Note] [Entrypoint]: Temporary server started.
database  | '/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
web       | Downloading pydantic-core (2.1MiB)
web       |  Downloaded pydantic-core
web       | Installed 15 packages in 58ms
database  | 2026-10-29 19:53:45+00:00 [Note] [Entrypoint]: Creating database mydatabase
database  | 2026-10-29 19:53:45+00:00 [Note] [Entrypoint]: Creating user dbuser
database  | 2026-10-29 19:53:45+00:00 [Note] [Entrypoint]: Giving user dbuser access to schema mydatabase
database  |
database  | 2026-10-29 19:53:45+00:00 [Note] [Entrypoint]: Stopping temporary server
database  | 2026-10-29T19:53:45.555002Z 13 [System] [MY-013172] [Server] Received SHUTDOWN from user root. Shutting down mysqld (Version: 8.4.13).
web       | INFO:     Started server process [40]
web       | INFO:     Waiting for application startup.
web       | INFO:     Application startup complete.
web       | INFO:     Uvicorn running on http://0.0.0.0:8080 (Press CTRL+C to quit)
database  | 2026-10-29T19:53:46.699499Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.4.13)  MySQL Community Server - GPL.
database  | 2026-10-29T19:53:46.699813Z 0 [System] [MY-015016] [Server] MySQL Server - end.
database  | 2026-10-29 19:53:47+00:00 [Note] [Entrypoint]: Temporary server stopped
database  |
database  | 2026-10-29 19:53:47+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.
database  |
database  | 2026-10-29T19:53:47.637533Z 0 [System] [MY-015015] [Server] MySQL Server - start.
database  | 2026-10-29T19:53:47.937064Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.4.13) starting as process 1
database  | 2026-10-29T19:53:47.937068Z 0 [System] [MY-015150] [Server] Server version with update: '8.4.13.0'
database  | 2026-10-29T19:53:47.943560Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
database  | 2026-10-29T19:53:48.284922Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
database  | 2026-10-29T19:53:48.617934Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
database  | 2026-10-29T19:53:48.618161Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
database  | 2026-10-29T19:53:48.623488Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
database  | 2026-10-29T19:53:48.663919Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.4.13'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
database  | 2026-10-29T19:53:48.664142Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock


w Enable Watch   d Detach

```
&nbsp;
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED              STATUS              PORTS                                                    NAMES
6fe0088e6db0   web:latest        "uv run main.py --po…"   About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp              web
ea4aac84c346   database:latest   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:3306->3306/tcp, [::]:3306->3306/tcp, 33060/tcp   database
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$
```
&nbsp;
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ curl localhost:8080
["Hello, World!"]
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$
```
&nbsp;
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ mysql -h 127.0.0.1 -P 3306 -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.4.13 MySQL Community Server - GPL

Copyright (c) 2000, 2026, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

### Modify the Docker file for the web application container to include a new feature (e.g., a new message, a new page, etc.). Rebuild the web application container and redeploy the application using docker-compose. Verify that the new feature is working as expected.

Added `/ping` route which returns **pong** as a response
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ cat web/main.py
import uvicorn
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello, World!"}

@app.get("/ping")
def health_check():
    return {"pong"}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8080)
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$
```
After making changes in **main.py**. `docker compose build` command is used to rebuild the web container
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker compose down
[+] down 3/3
 ✔ Container web                                       Removed                                                                                                        0.6s
 ✔ Container database                                  Removed                                                                                                        1.4s
 ✔ Network dicebootcamp26-assignment2-part2_my_network Removed            
```
&nbsp;
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker compose build
[+] Building 3.8s (21/21) FINISHED
 => [internal] load local bake definitions                                                                                                                           0.0s
 => => reading from stdin 1.05kB                                                                                                                                     0.0s
 => [web internal] load build definition from Dockerfile                                                                                                             0.0s
 => => transferring dockerfile: 629B                                                                                                                                 0.0s
 => [database internal] load build definition from Dockerfile                                                                                                        0.0s
 => => transferring dockerfile: 315B                                                                                                                                 0.0s
 => [web internal] load metadata for ghcr.io/astral-sh/uv:latest                                                                                                     1.9s
 => [web internal] load metadata for docker.io/library/python:3.12-slim                                                                                              2.0s
 => [database internal] load metadata for docker.io/library/mysql:8.4                                                                                                2.3s
 => [web internal] load .dockerignore                                                                                                                                0.0s
 => => transferring context: 2B                                                                                                                                      0.0s
 => [web] FROM ghcr.io/astral-sh/uv:latest@sha256:af22a52f5eafef3d78e551599d405c02df61919d18028f0cea8eda93bc9986d0                                                   0.1s
 => => resolve ghcr.io/astral-sh/uv:latest@sha256:af22a52f5eafef3d78e551599d405c02df61919d18028f0cea8eda93bc9986d0                                                   0.1s
 => [web internal] load build context                                                                                                                                0.1s
 => => transferring context: 77.00kB                                                                                                                                 0.1s
 => [web stage-0 1/6] FROM docker.io/library/python:3.12-slim@sha256:aa943ce411423c7582288f18707ff66eddcbaeb510502d747a72aeb34f30d159                                0.1s
 => => resolve docker.io/library/python:3.12-slim@sha256:aa943ce411423c7582288f18707ff66eddcbaeb510502d747a72aeb34f30d159                                            0.1s
 => CACHED [web stage-0 2/6] COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/                                                                                  0.0s
 => CACHED [web stage-0 3/6] WORKDIR /app                                                                                                                            0.0s
 => CACHED [web stage-0 4/6] COPY pyproject.toml uv.lock ./                                                                                                          0.0s
 => CACHED [web stage-0 5/6] RUN uv sync --frozen --no-cache  --no-install-project                                                                                   0.0s
 => [web stage-0 6/6] COPY . /app                                                                                                                                    0.2s
 => [database internal] load .dockerignore                                                                                                                           0.0s
 => => transferring context: 2B                                                                                                                                      0.0s
 => [web] exporting to image                                                                                                                                         1.3s
 => => exporting layers                                                                                                                                              0.9s
 => => exporting manifest sha256:0dc8230fd0917e5a928fb5ad0cd1e7fff7768bfd68348a7c3df756b4f4ba87f9                                                                    0.0s
 => => exporting config sha256:cae583878bdae7de830b4845713ce0bc3d5c7906077bbe06ceab83c204cd84c7                                                                      0.0s
 => => exporting attestation manifest sha256:c994dbb19500de9b24fc9a6423fda73be3a63c36cacb65b28b1e002edb3601c3                                                        0.0s
 => => exporting manifest list sha256:85fb6d6d57bccc5e54c0be5d6df4181fbd708767b7e46a1fcb2dbcf0fe2385b2                                                               0.0s
 => => naming to docker.io/library/web:latest                                                                                                                        0.0s
 => => unpacking to docker.io/library/web:latest                                                                                                                     0.3s
 => CACHED [database 1/1] FROM docker.io/library/mysql:8.4@sha256:a0225d1ea6631c8bc2385dc4c2faba17bbf1834ecf369e1659a9a382bd8d5dd7                                   0.1s
 => => resolve docker.io/library/mysql:8.4@sha256:a0225d1ea6631c8bc2385dc4c2faba17bbf1834ecf369e1659a9a382bd8d5dd7                                                   0.1s
 => [database] exporting to image                                                                                                                                    0.2s
 => => exporting layers                                                                                                                                              0.0s
 => => exporting manifest sha256:6414949a7532054c503e79161fe943639c71c24c36f35cd92e56a319ce3a7428                                                                    0.0s
 => => exporting config sha256:244da48268b9e9b4fd88ee59294a048adfa3ab7fb02adefd326d8c5cd8c7f05f                                                                      0.0s
 => => exporting attestation manifest sha256:bb348b288f68652b4e145830d91264cb9a416815f111dc574b49ab0e2ec584a5                                                        0.0s
 => => exporting manifest list sha256:a7c22ccd6d371da08d5e6df3b3e7f7fac16afafcab74b10af231934212c5c020                                                               0.0s
 => => naming to docker.io/library/database:latest                                                                                                                   0.0s
 => => unpacking to docker.io/library/database:latest                                                                                                                0.0s
 => [database] resolving provenance for metadata file                                                                                                                0.0s
 => [web] resolving provenance for metadata file                                                                                                                     0.0s
[+] build 2/2
 ✔ Image database:latest Built                                                                                                                                        4.0s
 ✔ Image web:latest      Built     
```
&nbsp;
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker compose up -d
[+] up 3/3
 ✔ Network dicebootcamp26-assignment2-part2_my_network Created                                                                                                        0.1s
 ✔ Container database                                  Started                                                                                                        0.5s
 ✔ Container web                                       Started                                                                                                        0.9s
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$
```
&nbsp;
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ curl localhost:8080
["Hello, World!"]
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ curl localhost:8080/ping
["pong"]
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$
```

### Implement a backup strategy for the database. This can be achieved using a Docker volume or by running a backup script periodically.
I have used named volume `mysql_data`  for database which is persistent in nature (won't deleted when container gets down or removed)
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker compose down
[+] down 3/3
 ✔ Container web                                       Removed                                                                                                        0.6s
 ✔ Container database                                  Removed                                                                                                        1.2s
 ✔ Network dicebootcamp26-assignment2-part2_my_network Removed                                                                                                        0.2s
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker volume ls
DRIVER    VOLUME NAME
local     563d3d84b8ffca6c89a84784605c0f45235074767e388e951b16b452d6d09dad
local     5023314c9c2e425d35a9dc5bd5e0de1ffdadabacb2b8359f2c74de20d94275f3
local     dicebootcamp26-assignment2-part2_mysql_data
local     fa2295bbce4e1e83d02a4736051a35e0bce4ee7e9be90e7f014693f0ab7c7cb6
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker compose up -d
[+] up 3/3
 ✔ Network dicebootcamp26-assignment2-part2_my_network Created                                                                                                        0.1s
 ✔ Container database                                  Started                                                                                                        0.4s
 ✔ Container web                                       Started                                                                                                        0.8s
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED         STATUS         PORTS                                                    NAMES
1423b9766910   web:latest        "uv run main.py --po…"   4 seconds ago   Up 3 seconds   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp              web
9650339248bd   database:latest   "docker-entrypoint.s…"   4 seconds ago   Up 4 seconds   0.0.0.0:3306->3306/tcp, [::]:3306->3306/tcp, 33060/tcp   database
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker inspect 9650339248bd | grep mysql_data
                "dicebootcamp26-assignment2-part2_mysql_data:/var/lib/mysql:rw"
                "Name": "dicebootcamp26-assignment2-part2_mysql_data",
                "Source": "/var/lib/docker/volumes/dicebootcamp26-assignment2-part2_mysql_data/_data",
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$
```
As you can see in above output, same volume has been re-attached when database container gets removed and recreated again.
### Implement a scaling strategy for the web application. This can be achieved by using the docker-compose scale command to create multiple instances of the web service. Verify that the web application is accessible and working correctly when scaled.
-  To use `docker-compose scale` command, custom name needs to be removed from docker compose file
- Also we need to let docker choose random external port for the web containers since statically defined port (*8080* in this case) will only be assigned to one container and eventually docker will throw an error for the rest of  the containers  

```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ cat docker-compose.yml
# Commented the version as its deprecated. Was also giving warnings during execution.
#version: '3.9'

services:
  web:
    build: ./web
    image: web:latest
#    container_name: web
    ports:
      - "8080"
    networks:
      - my_network
    depends_on:
      - database

  database:
    build: ./db
    image: database:latest
    container_name: database
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: mydatabase
      MYSQL_USER: dbuser
      MYSQL_PASSWORD: dbpassword
    ports:
      - "3306:3306"
    networks:
      - my_network
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:

networks:
  my_network:
    driver: bridge
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$

```
&nbsp;
```
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker compose up -d --scale web=2
[+] up 4/4
 ✔ Network dicebootcamp26-assignment2-part2_my_network Created                                                                                                                          0.1s
 ✔ Container database                                  Started                                                                                                                          0.5s
 ✔ Container dicebootcamp26-assignment2-part2-web-1    Started                                                                                                                          1.5s
 ✔ Container dicebootcamp26-assignment2-part2-web-2    Started                                                                                                                          0.9s
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED         STATUS         PORTS                                                    NAMES
283b7467d66a   web:latest        "uv run main.py --po…"   4 seconds ago   Up 3 seconds   0.0.0.0:32769->8080/tcp, [::]:32769->8080/tcp            dicebootcamp26-assignment2-part2-web-2
3cf31b691f63   web:latest        "uv run main.py --po…"   4 seconds ago   Up 3 seconds   0.0.0.0:32770->8080/tcp, [::]:32770->8080/tcp            dicebootcamp26-assignment2-part2-web-1
94236cdfd014   database:latest   "docker-entrypoint.s…"   5 seconds ago   Up 4 seconds   0.0.0.0:3306->3306/tcp, [::]:3306->3306/tcp, 33060/tcp   database
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ curl http://localhost:32769
["Hello, World!"]
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$ curl http://localhost:32770
["Hello, World!"]
ubuntu@ubuntu:~/dicebootcamp26-assignment2-part2$

```
