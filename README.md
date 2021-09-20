# Metricbeat

This is useful tool for monitoring different aspects of a server like CPU/RAM usage, networkings, etc.

## Initial Startup

Clone the metricbeat repo in the target server and go inside its directory.

```bash
git clone git@172.16.17.38:backend/sre-elastic-metricbeat.git metricbeat
cd metricbeat
```

Configuration files need to be owned by the user with UID = 1000 and GID = 1000 because those are the UID and GID of metricbeat user inside the container. This might make working and editing configuration files cumbersome if you're default user is something other than 1000. So, we better at the current user to GID = 1000 so that we can easily change and edit configurations.

```bash
sudo usermod -aG 1000 $USER
```

Change the owner of the "configs" directory to 1000 which is the UID of the metricbeat user inside the container.

```bash
sudo chown -R 1000:1000 ./configs
```

Only ensure only the owner of the "configs" directory has the write access on it as is requested by metricbeat.

```bash
sudo chmod -R go-w ./configs
```

Use the sample environment for creating your own .env file.

```bash
cp .env.sample .env
```

Then, open the file with your preferred editor and fill the password field for both elasticsearch and kibana users. 

Add read and write permissions for others on docker.sock file.
```bash
sudo chmod o+rw /run/docker.sock
```

Up the service.

```bash
docker-compose up -d
```

## File Ownerships

Since metricbeat uses some of the system files, all configuration files should be owned by the uid=1000 (`root` or `adminstrator`) and be only writable by the owner. Run these 2 lines of commands to correct permissions.

```bash
chown -R 1000:1000 ./configs
chmod -R go-w ./configs
```

## Environment Variables

Use one of `.env.sample` or `.env.fish.sample` files to create your own environment variables. You don't need to source your `.env` file because the variables will be picked up by docker upon initialization of the service. But in case you needed to directly source your environment variables, use:

```bash
. .env

# Only for fish shell:
. .env.fish
```

## Visiblity of Host Processes Inside the Container

When running Metricbeat container, it doesn't get started with `-system.hostfs=/hostfs` flag and that results in container not being able to see host processes. We need to add this flag in the command field of compose file.

```bash
services:
    metricbeat:
       image: beats/metricbeat:${VERSION}
       container_name: metricbeat
       network_mode: host
       env_file: .env
       command: ["--strict.perms=false", "-system.hostfs=/hostfs", "-e"]
```
