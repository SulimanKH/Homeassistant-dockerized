Here's the updated `README.md` for your `docker-compose.yml` setup:

---

# Home Assistant, Portainer, and Z-Wave JS2MQTT Docker Compose Setup

This repository contains a Docker Compose configuration to run the following services:

- **Portainer**: A web-based Docker management interface.
- **Home Assistant**: An open-source home automation platform.
- **Z-Wave JS2MQTT**: A Z-Wave to MQTT bridge for managing Z-Wave devices.

## Requirements

- Docker and Docker Compose installed on your system.
- A Z-Wave USB stick connected to your machine for the **Z-Wave JS2MQTT** service.
- The following services will run in containers on the same Docker network.

## Services Overview

### 1. **Portainer**

Portainer provides a graphical interface for managing Docker containers. You can access it via the web interface.

- **Port**: `9000`
- **Environment Variable**: 
  - `TZ=Europe/London` (Time Zone)
- **Volumes**: 
  - `/var/run/docker.sock:/var/run/docker.sock` (Allows Portainer to manage Docker)
  - `./portainer:/data` (Persistent data for Portainer)

### 2. **Home Assistant**

Home Assistant is an open-source platform for home automation. It runs in a container and exposes a web interface for managing smart home devices.

- **Port**: `8123`
- **Volumes**: 
  - `./homeassistant/config:/config` (Home Assistant configuration)
  - `/etc/localtime:/etc/localtime:ro` (Ensure correct time zone)
  - `/run/dbus:/run/dbus:ro` (Required for some integrations)
- **Privileged**: `true` (Needed for Home Assistant to access hardware like USB sticks)
- **Restart Policy**: `unless-stopped`

### 3. **Z-Wave JS2MQTT**

Z-Wave JS2MQTT provides an MQTT interface for Z-Wave devices, allowing Home Assistant to communicate with Z-Wave networks.

- **Port for Web Interface**: `8091`
- **Port for WebSocket Server**: `3000`
- **Environment Variables**:
  - `SESSION_SECRET=${ZWAVE_SECRET}` (Replace with a secure secret, set the `ZWAVE_SECRET` environment variable)
  - `ZWAVEJS_EXTERNAL_CONFIG=/usr/src/app/store/.config-db` (Configuration path)
  - `TZ=Europe/London` (Time Zone)
- **Devices**: 
  - `${ZWAVE_STICK_PATH}:/dev/zwave` (Z-Wave USB stick - replace `${ZWAVE_STICK_PATH}` with your actual device path, e.g., `/dev/serial/by-id/usb-...`)
- **Volumes**: 
  - `./z-wave-js/:/usr/src/app/store` (Persistent storage for Z-Wave JS)

## Usage

### 1. Clone the Repository

```bash
git clone <repository-url>
cd <repository-directory>
```

### 2. Set Environment Variables

Before starting the services, set the environment variables for your Z-Wave secret and USB stick path:

```bash
export ZWAVE_SECRET="your_secret_key"
export ZWAVE_STICK_PATH="/dev/serial/by-id/your-stick-id"
```

### 3. Start the Services

To bring up the containers, run:

```bash
docker-compose up -d
```

This will start all three services in the background. You can check the logs for any errors:

```bash
docker-compose logs -f
```

### 4. Access the Services

- **Portainer**: Access via `http://<your-server-ip>:9000`
- **Home Assistant**: Access via `http://<your-server-ip>:8123`
- **Z-Wave JS2MQTT**: Access via `http://<your-server-ip>:8091` (Web Interface)

### 5. Stopping the Services

To stop the containers, run:

```bash
docker-compose down
```

This will stop and remove the containers but will keep the volumes intact.

---

## Customization

- **Time Zone**: Change the `TZ` environment variable for each service to match your local time zone.
- **Z-Wave USB Stick**: Replace `${ZWAVE_STICK_PATH}` with the actual device path for your Z-Wave USB stick.
  - To find the correct path, use `ls /dev/serial/by-id/` or `ls /dev/cu.*` to identify your Z-Wave stick.
- **Z-Wave Configuration**: You can change the persistent storage path for Z-Wave JS by modifying the `./z-wave-js/` volume.

---

## Troubleshooting

- **Z-Wave Device Not Recognized**: Ensure your Z-Wave USB stick is properly connected and recognized by the system. You can check this with `ls /dev/cu.*` or `ls /dev/serial/by-id/`.
- **Permissions Issues**: Ensure Docker has permission to access the USB stick by running the containers with `sudo` if needed or setting appropriate permissions on `/dev/ttyUSB0` or `/dev/cu.usbserial-xxxx`.

---

## License

This project is licensed under the MIT License.

---

This `README.md` file provides a clear guide for setting up and running the services in your `docker-compose.yml` file, as well as customization options and troubleshooting tips.