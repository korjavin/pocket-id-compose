# Pocket-ID for Portainer

This repository contains the necessary files to run [Pocket-ID](https://github.com/pocket-id/pocket-id) as an OIDC provider in Portainer.

## Usage

1.  **Clone this repository:**

    ```
    git clone https://github.com/korjavin/pocket-id-portainer.git
    cd pocket-id-portainer
    ```

2.  **Create a `.env` file:**

    Copy the `.env.example` file to `.env` and fill in the values for your environment.

    ```
    cp .env.example .env
    ```

3.  **Deploy in Portainer:**

    Use the `docker-compose.yml` file to deploy the stack in Portainer. You will need to add the environment variables from your `.env` file to the Portainer stack configuration.

## Environment Variables

The following environment variables are required:

| Variable                      | Description                                                                 |
| ----------------------------- | --------------------------------------------------------------------------- |
| `POCKET_ID_HOST`              | The hostname of your Pocket-ID instance.                                    |
| `POCKET_ID_PORT`              | The port that Pocket-ID will listen on.                                     |
| `POCKET_ID_LOG_LEVEL`         | The log level for Pocket-ID.                                                |
| `POCKET_ID_OIDC_CLIENT_ID`    | The OIDC client ID.                                                         |
| `POCKET_ID_OIDC_CLIENT_SECRET`| The OIDC client secret.                                                     |
| `POCKET_ID_OIDC_REDIRECT_URI` | The OIDC redirect URI.                                                      |
| `POSTGRES_USER`               | The username for the PostgreSQL database.                                   |
| `POSTGRES_PASSWORD`           | The password for the PostgreSQL database.                                   |
| `POSTGRES_DB`                 | The name of the PostgreSQL database.                                        |

### Generating a strong `POCKET_ID_OIDC_CLIENT_SECRET`

You can generate a strong client secret using `openssl`:

```bash
openssl rand -base64 32
```

This command generates a 32-byte random string, base64 encoded, which is suitable for use as a client secret.

## Traefik Configuration

To expose Pocket-ID through Traefik, you will need to add the following labels to the `pocket-id` service in your `docker-compose.yml` file:

```yaml
services:
  pocket-id:
    # ...
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.pocket-id.rule=Host(`${POCKET_ID_HOST}`)"
      - "traefik.http.routers.pocket-id.entrypoints=websecure"
      - "traefik.http.routers.pocket-id.tls.certresolver=myresolver"
      - "traefik.http.services.pocket-id.loadbalancer.server.port=${POCKET_ID_PORT}"
```
