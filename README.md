# DataGateOpenVpnServer

**DataGateOpenVpnServer** is a Dockerized OpenVPN server with integrated EasyRSA PKI and a .NET Web API for automated certificate management.

## Features

- OpenVPN server running inside a lightweight Alpine-based container
- EasyRSA v3 for internal PKI management
- Automatic initialization of PKI and OpenVPN server configuration on first startup
- TLS-crypt key and CRL generation
- Built-in ASP.NET Core Web API for:
  - Creating client certificates
  - Revoking certificates
  - Generating `.ovpn` client configuration files
- Runs OpenVPN and Web API in the same container
- Customizable via environment variables

---

## Getting Started

### Prerequisites
- Docker
- Linux host with TUN device support (`/dev/net/tun`)

### Build the Docker Image
```bash
docker build -t datagate-openvpn-server .
```

### Run the Container
```bash
docker run -d \
  --cap-add=NET_ADMIN \
  --device /dev/net/tun \
  -p 1194:1194/udp \
  -p 5000:80 \
  -e DATA_DIR=/openvpn-data \
  -v openvpn_data:/openvpn-data \
  --name datagate_openvpn \
  datagate-openvpn-server
```

---

## Environment Variables

| Variable     | Default  | Description                           |
|--------------|----------|---------------------------------------|
| `PORT`       | `1194`   | OpenVPN server port                   |
| `PROTO`      | `udp`    | Protocol to use (`udp` or `tcp`)      |
| `MGMT_PORT`  | `5092`   | OpenVPN management interface port     |
| `DATA_DIR`   | `/mnt`   | Directory to store PKI and config     |

---

## API Endpoints

| Method | Endpoint                     | Description                        |
|--------|------------------------------|------------------------------------|
| POST   | `/api/cert/create`          | Create a new client certificate    |
| POST   | `/api/cert/revoke`          | Revoke an existing certificate     |
| GET    | `/api/cert/export/{name}`   | Export `.ovpn` config for a client |

> The API is available at `http://localhost:5000`. Optionally, Swagger UI can be added for exploration.

---

## Volumes

- `openvpn_data:/openvpn-data`: stores all persistent data (PKI, logs, server config)

---

## License

MIT License

---

## Maintainer

Created and maintained by **Ivan Kolganov**, built with love on top of Kyle Manna's OpenVPN approach.
