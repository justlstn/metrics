# Grafana + Prometheus with Caddy and Docker

This repository provides a Dockerized setup for Grafana and Prometheus with Caddy as a reverse proxy, utilizing SSL provided by Caddy. This configuration also enables Prometheus to use remote write to receive data from remote and store.

## Prerequisites

Before you begin, make sure you have Docker and Docker Compose installed on your machine.

## Setup

1. Clone this repository:

```bash
git clone https://github.com/justlstn/metrics.git
```

2. Copy the example environment file and adjust the values:

```bash
cp .env.example .env
```

Update the required environment variables in the `.env` file, specifically `DOMAIN` and `SSL_EMAIL`.

## Configuration

Ensure that you have configured the necessary settings in the `.env` file. The required variables are:

- `DOMAIN`: Your domain name for the services.
- `SSL_EMAIL`: Your email address for SSL certificate registration with Let's Encrypt.

By default, Prometheus uses basic authentication to secure access to its web interface. Follow these steps to update the basic auth credentials:

1. Open the `./config/prometheus/web_config.yml` file in your preferred text editor.

2. Create a bcrypt-hashed password. To do so you could use a `apache2-utils` package or use other tools.

```bash
htpasswd -nBC 10 "" | tr -d ':\n'
```

3. Locate the `basic_auth_users` section and update the credentials. Example:

```yaml
basic_auth_users:
  username: password_hash
```

## Running the Services

To start the services, run:

```bash
docker compose up -d
```

This command will pull the necessary Docker images and start Grafana, Prometheus, and Caddy in the background.

## Accessing Grafana

Once the services are up and running, you can access Grafana at:

```bash
https://metrics.example.dev/
```

Default credentials:

- Username: admin
- Password: admin

**Note**: It is recommended to change the default password after the initial login.

## Stop and Remove Services

To stop and remove the Docker containers, run:

```bash
docker compose down
```

This will stop and remove the containers, but it will retain the persistent data and volumes.

Certainly! Here's a section you can add to your README.md file to explain how to use the Prometheus remote write feature to push metrics from different servers onto one metrics UI instance:

## Using Prometheus Remote Write Feature

Follow these steps to configure remote write on servers sending metrics:

1. Open the Prometheus configuration file on the server you want to monitor. Typically, the configuration file is named `prometheus.yml`.

2. Add the following `remote_write` configuration in the `/etc/prometheus/prometheus.yml` file:

```yaml
remote_write:
  - url: https://metrics.example.dev/api/v1/write
    basic_auth:
      username: <username>
      password: <password>
```

Replace `https://metrics.example.dev/api/v1/write` with the actual URL of your metrics instance. Update `username` and `password` with the basic auth credentials configured for remote write.

3. Save the changes to the configuration file.

4. Restart Prometheus on the server to apply the new configuration:

```bash
systemctl restart prometheus
```

(Note: The command might vary based on your operating system and Prometheus installation method.)

Now, Prometheus on the server is configured to push metrics to the specified remote write endpoint. Repeat these steps for each server you want to monitor centrally.

On the metrics UI instance, you can visualize and analyze metrics from all the configured servers.

## Contributing

If you encounter issues or have suggestions for improvements, feel free to create an issue or submit a pull request.