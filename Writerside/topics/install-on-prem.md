# Installing %product%

The %product% application is a software that runs on your host. It is responsible for executing the analysis of your log events and
lines and generating a report that can be accessed using the UI.

The beck-end and front-end (ui) services are packaged as container images, they support both `dockerd` and `containerd` runtimes.
Images are available for `amd64 (64-bit x86)` architecture.

%product% uses a `postgresql` database  and a `rabbitmq` broker, both need to be accessible by the
application.

## Compatibility Requirements {id="compatibility-requirements"}

<table id="infrastructure-dependencies-versions" style="header-row">
<tr>
    <td>Dependency</td>
    <td>Version</td>
</tr>
<tr>
    <td>Postgresql</td>
    <td>9.2+</td>
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

> For the application we recommend using an `c5.xlarge (4 VCPUs / 8GB RAM)` instance or equivalent for production environments

The host also requires to have internet access to download images and outbound traffic must be able to use ports
`80`, `443`, `7148` and `7149` to reach our authentication and authorization endpoints. 

> The hardware requirements may increase in the
> future as we introduce new features into the product
> {style="note"}


## Environment Variables {id="environment-variables"}

<table id="application-environment-variable" style="header-row">
<tr>
    <td>Env</td>
    <td>Description</td>
</tr>
<tr>
    <td>COL_RABBITMQ_USER</td>
    <td>The rabbitmq user (requires write on '/' vhost)</td>
</tr>
<tr>
    <td>COL_RABBITMQ_PASSWORD</td>
    <td>The rabbitmq user password</td>
</tr>
<tr>
    <td>COL_POSTGRES_USER</td>
    <td>The postgres user</td>
</tr>
<tr>
    <td>COL_POSTGRES_PASSWORD</td>
    <td>The postgres user password</td>
</tr>
</table>

## Installing {id="installing"}

> During private beta all scripts will be provided by our team and are subject to change!

> The usage of docker volumes is discouraged due to potentially causing data losses
> {style="warning"}
 
The deployment of the %product% is divided into two docker compose files.

- `docker-compose-infrastructure.yaml`
- `docker-compose.yaml`

The `docker-compose-infrastructure.yaml` describes both `rabbitmq` and `postgresql` environments.
This file will require a few changes!

<procedure>
<p>Configuring the host</p>
<step>Install a compatible container runtime on the host</step>
<step>Create the ubat container network</step>
<step>Set postgresql username and password</step>
<step>Set rabbitmq username and password</step>
<step>Update the infrastructure docker compose file to include both postgresql and rabbitmq user credentials</step>
<step>Create a folder on the host to store postgresql data</step>
<step>Create a folder on the host to store rabbitmq data</step>
<step>Update the infrastructure docker compose file to include the paths of the previously created folders</step>
<step>Run the infrastructure docker compose file</step>
<step>Configure <a anchor="configuring-infra-services">the infrastructure services</a></step>
</procedure>


### Configuring the infrastructure services {id="configuring-infra-services"} 

#### Configuring postgresql {id="configuring-postgresql"}

Create the application postgresql user and run the migration scripts on the `postgresql` instance.
  
### Configuring rabbitmq {id="configuring-rabbitmq"}

Create the application user on the rabbitmq broker and give the write user access to the `/` virtual host.