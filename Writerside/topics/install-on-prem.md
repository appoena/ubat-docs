# Installing %product%

The %product% application is a software that runs on your host. It is responsible for executing the analysis of your log
events and lines and generating a report that can be accessed using the UI.

The beck-end and front-end (ui) services are packaged as container images, they support both `dockerd` and `containerd`
runtimes.
Images are available for `amd64 (64-bit x86)` architecture.

%product% uses a `postgresql` database and a `rabbitmq` broker, both need to be accessible by the
application.

## Compatibility Requirements {id="compatibility-requirements"}

<table id="infrastructure-dependencies-versions" style="header-row">
<tr>
    <td>Dependency</td>
    <td>Version</td>
</tr>
<tr>
    <td>Postgresql</td>
    <td>17+</td>
</tr>
<tr>
    <td>Rabbitmq</td>
    <td>3.10.0+</td>
</tr>
<tr>
    <td>Docker (dockerd)</td>
    <td>23+</td>
</tr>
<tr>
    <td>Containerd</td>
    <td>1.6+</td>
</tr>
<tr>
    <td>Docker compose</td>
    <td>3</td>
</tr>
</table>

## Hardware Requirements {id="hardware-requirements"}

Tests were made on an Amazon EC2 machine `t3.micro` instance (1 VCPU/ 2GB RAM) with the infrastructure services running
on a different instance of the same size.

You can see the average resource usage of our tests below:

- CPU: `~85%` of peak cpu usage on average
- Memory: `~700MB` of RAM usage (RSS memory)
- Network bandwidth: `~150 KiB/s` received | `~210 KiB/s` sent

> For the application we recommend using an `c5.xlarge (4 VCPUs / 8GB RAM)` instance or equivalent for production
> environments

### Network Requirements

The host requires to have internet access to download images. It also requires to have outbound traffic to
go through four different ports in order to reach our authentication and authorization endpoints.

Host outbound traffic ports:

- `80`
- `443`
- `7148`
- `7149`

The inbound traffic flows through three different ports. These can be changed in the docker compose file.

Default hosts inbound traffic ports:

- `3000`
- `7001`
- `7002`

> The hardware requirements may increase in the
> future as we introduce new features into the product
> {style="note"}

## Components {id="components"}

### Backend {id="backend"}

The backend is built using dotnet 7, and it is responsible for executing the analysis and compiling the results into
a meaningful report.

#### Environment Variables {id="backend-env-vars"}

<table id="backend-environment-variables" style="header-row">
<tr>
    <td>Variable</td>
    <td>Description</td>
    <td>Default</td>
</tr>
<tr>
    <td>COL__RABBITMQ__USER</td>
    <td>The rabbitmq user (requires writing on vhost)</td>
    <td><code>rabbitmq</code></td>
</tr>
<tr>
    <td>COL__RABBITMQ__PASSWORD</td>
    <td>The rabbitmq user password</td>
    <td>-</td>
</tr>
<tr>
    <td>COL__RABBITMQ__VHOST</td>
    <td>The rabbitmq virtual host</td>
    <td><code>/</code></td>
</tr>
<tr>
    <td>COL__RABBITMQ__PORT</td>
    <td>The rabbitmq port</td>
    <td><code>5672</code></td>
</tr>
<tr>
    <td>COL__RABBITMQ__HOST</td>
    <td>The rabbitmq host address</td>
    <td><code>localhost</code></td>
</tr>
<tr>
    <td>COL__POSTGRES__USER</td>
    <td>The postgres user</td>
    <td><code>postgres</code></td>
</tr>
<tr>
    <td>COL__POSTGRES__PASSWORD</td>
    <td>The postgres user password</td>
    <td>-</td>
</tr>
<tr>
    <td>COL__POSTGRES__DATABASE</td>
    <td>The postgres database to connect to</td>
    <td><code>ubat</code></td>
</tr>
<tr>
    <td>COL__POSTGRES__SCHEMA</td>
    <td>The postgres database schema to use</td>
    <td><code>public</code></td>
</tr>
<tr>
    <td>COL__POSTGRES__PORT</td>
    <td>The postgres connection port</td>
    <td><code>5432</code></td>
</tr>
<tr>
    <td>COL__POSTGRES__HOST</td>
    <td>The postgres host address</td>
    <td><code>localhost</code></td>
</tr>
<tr>
    <td>UBAT_SKIP_DB_MIGRATIONS</td>
    <td>Enables/Disables database migrations deployments</td>
    <td><code>false</code></td>
</tr>
<tr>
    <td>UBAT_SKIP_DB_SEEDING</td>
    <td>Enables/Disables database seeding</td>
    <td><code>false</code></td>
</tr>
     <tr>
    <td>UBAT_DEBUG_MIGRATIONS</td>
    <td>Enables/Disables debug log level during migrations deployment</td>
    <td><code>false</code></td>
</tr>
</table>

### Frontend {id="frontend"}

The frontend is built using Next.js 15, and it is the main way you interact with the service.

#### Environment Variables {id="frontend-env-vars"}

<table id="front-environment-variables" style="header-row">
<tr>
    <td>Variable</td>
    <td>Description</td>
    <td>Default</td>
</tr>
<tr>
    <td>EXTERNAL_ADDRESS</td>
    <td>The address of the spa. E.g.: <code>https://ubat.company</code></td>
    <td><code>https://localhost:3000</code></td>
</tr>
</table>

### Cli

For information about the CLI, go to the [CLI documentation page](run-the-cli.md)

## Installing {id="installing"}

> During beta all scripts will be provided by our team and are subject to change!

The deployment of the %product% is done using a docker compose file called `docker-compose-all.yaml`.

This compose file provides all the necessary images to run the application in a single package.

All the containers run as `unprivileged` and should not be allowed to run as root.

### Configuring the host machine

First you need to make sure docker is installed in the target machine.

You can check out how to install docker following
the [official docker documentation](https://docs.docker.com/engine/install/).

#### Create storage directories

Although %product% is a stateless application, it uses `postgresql` and `rabbitmq` to store and transfer information
about the analysis, and they both require volumes to store information.

> The usage of docker volumes is discouraged due to risk of data loss
> {style="warning"}

##### Create data directory

- On windows create a folder on the `C:\` drive called `data`.

<code-block>
    mkdir C:\data
</code-block>

- On Linux create a folder on the `/`(root) called `data`.

<code-block>
    sudo mkdir /data 
</code-block>

#### Configure postgres

On Windows:
<procedure>
<step>
    Create a folder in the <code>C:\data</code> directory called <code>pg</code>
    <code-block>
        mkdir C:\data\pg
    </code-block>
</step>
<step>
    Set the <code>ubat-postgres</code> service volume in the docker compose file
    <code-block>
    volumes:
    - C:\data\pg:/bitnami/postgresql
    </code-block>
</step>
</procedure>

On Linux and OSX:
<procedure>
<step>
    Create a folder in the <code>/data</code> directory called <code>pg</code>
    <code-block>
        mkdir /data\pg
    </code-block>
</step>
<step>
    Set the <code>ubat-postgres</code> service volume in the docker compose file
    <code-block>
    volumes:
    - /data/pg:/bitnami/postgresql
    </code-block>
</step>
</procedure>

##### Configure postgres password

<procedure>
<step>
    Create a strong password for the <code>ubat-postgres</code> service
</step>
<step>
    Update the <code>ubat-postgres</code> container environment variable <code>POSTGRES_PASSWORD</code>
    <code-block>
        - POSTGRES_PASSWORD=
    </code-block>
</step>
<step>
    Update the <code>ubat-api</code> container environment variable <code>COL_POSTGRES_PASSWORD</code>
    <code-block>
        - COL_POSTGRES_PASSWORD=
    </code-block>
</step>
</procedure>

#### Configure rabbitmq

On Windows:
<procedure>
<step>
    Create a folder in the <code>C:\data</code> directory called <code>mq</code>
    <code-block>
        mkdir C:\data\mq
    </code-block>
</step>
<step>
    Set the <code>ubat-rabbitmq</code> service volume in the docker compose file
    <code-block>
    volumes:
    - C:\data\mq:/bitnami/rabbitmq/mnesia
    </code-block>
</step>
</procedure>

On Linux and OSX:
<procedure>
<step>
    Create a folder in the <code>/data</code> directory called <code>mq</code>
    <code-block>
        mkdir /data\mq
    </code-block>
</step>
<step>
    Set the <code>ubat-rabbitmq</code> service volume in the docker compose file
    <code-block>
    volumes:
    - /data/mq:/bitnami/rabbitmq/mnesia
    </code-block>
</step>
</procedure>

##### Configure rabbitmq password

<procedure>
<step>
    Create a strong password for the <code>ubat-rabbitmq</code> service
</step>
<step>
    Update the <code>ubat-rabbitmq</code> container environment variable <code>RABBITMQ_PASSWORD</code>
    <code-block>
        - RABBITMQ_PASSWORD=
    </code-block>
</step>
<step>
    Update the <code>ubat-api</code> container environment variable <code>COL_RABBITMQ_PASSWORD</code>
    <code-block>
        - COL_RABBITMQ_PASSWORD=
    </code-block>
</step>
</procedure>

#### Give directory access to the containers

Since all containers run as `unprivileged`, you are to allow the containers `read`, `write` and `execute`
access to the newly created directories.

- On Linux run the following commands

<code-block>
    sudo chown -R $user:docker /data 
</code-block>
<code-block>
    sudo chmod -R u=rwx,go=rwx /data 
</code-block>

If there are security concerns or requirements, you can set the `UID` and `GID`
on the docker compose service definition and set the directory's permissions accordingly.

Example:
<code-block>
ubat-api:
  image: docker.io/appoena/ubat-api:1.0.0-beta.1
  container_name: ubat-api
  user: 1001:1001 #add this
</code-block> 

> Verify all directories created are accessible by the containers
> {style="warning"}

### Run the docker compose

After finishing the configuration, you can run the docker compose file by using the command

<code-block>
    docker compose up
</code-block>

During the first time the application starts, all the database migrations will be executed. If everything
goes well, you should not see any errors, and you can stop the compose execution by pressing <code>crtl+c</code>.

After that you can run the same command with the <code>-d</code> flag, so that the container's outputs are not attached
to your
terminal.
<code-block>
    docker compose up -d
</code-block>

#### Troubleshooting

If you see any error in the log output during the startup, verify if the directories are with the correct
permissions and that the containers have `read`, `write` and `execute` permissions on them.

Also check if the created passwords were correctly set.


