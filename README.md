# Daikin Alira Home Assistant Integration

**Repository:** https://github.com/cambot1901/ha-daikin-alira

A custom Home Assistant integration to control and monitor Daikin Alira air conditioners over HTTP.

## Features

### Sensors

| Entity | Description | Unit |
|--------|-------------|------|
| `sensor.daikin_alira_setpoint` | Current setpoint temperature | °C |
| `sensor.daikin_alira_temp` | Indoor temperature | °C |
| `sensor.daikin_alira_humidity` | Indoor humidity | % |
| `sensor.daikin_alira_fan_speed` | Fan speed (1–5, Auto, Quiet) | — |
| `sensor.daikin_alira_mode` | Operation mode (Auto, Cool, Heat, Fan only, Dry) | — |
| `sensor.daikin_alira_power` | Power state (On or Off) | — |

Data is polled every 30 seconds. All sensors share a single `DataUpdateCoordinator` to minimise HTTP requests.

### Climate Entity

`climate.daikin_alira_<host>` — full AC control with:

- **On/Off** — turn the AC on or off
- **Target Temperature** — 16 °C to 30 °C in 0.5 °C increments
- **Fan Modes** — 1, 2, 3, 4, 5, Auto, Quiet
- **Operation Modes** — Auto, Cool, Heat, Fan only, Dry

Commands are sent over HTTP to the `/dsiot/multireq` endpoint on the Daikin unit.

## Repository Structure

```
custom_components/daikin_alira/
├── __init__.py       # Sets up the integration and forwards entries to platforms
├── manifest.json     # Integration metadata
├── const.py          # Constants, including domain and configuration keys
├── config_flow.py    # UI configuration flow (asks only for host)
├── sensor.py         # Sensor entities and data-fetch logic
├── climate.py        # ClimateEntity for full AC control
├── logo.png          # (optional) 128×128 PNG integration icon
└── README.md         # This file
```

## Installation

1. Copy the `daikin_alira` folder into your Home Assistant custom components directory:
   ```
   /config/custom_components/daikin_alira/
   ```
2. Restart Home Assistant.
3. Go to **Settings → Integrations**, click **+**, and search for *Daikin Alira*.
4. Enter the IP address or hostname of your Daikin Alira unit and click **Submit**.

Sensors and the Climate entity will be created automatically. Optionally, add `logo.png` (128×128 PNG) to the folder for a custom integration icon.

## Configuration

After installation, go to **Settings → Devices & Services → Integrations**, find *Daikin Alira*, click **Configure**, and enter:

- **Host:** the IP address or hostname of your Daikin unit

The integration will set up automatically, creating a device named *Daikin Alira* with all related sensors and a Climate entity named *Daikin AC*.

### YAML (Advanced)

This integration is designed for UI configuration. If you prefer YAML, add the following to `configuration.yaml`:

```yaml
daikin_alira:
  host: 192.168.1.123  # Replace with your device IP
```

Then remove any UI-based configuration for this integration to avoid duplicates.

## Usage

### Lovelace Cards

Sensors appear as individual entities (temperature, humidity, fan speed, etc.).

The Climate entity appears as a thermostat card with:

- On/Off toggle
- Target temperature slider (16 °C–30 °C)
- Fan mode dropdown (1–5, Auto, Quiet)
- Operation mode dropdown (Auto, Cool, Heat, Fan only, Dry)

### Device Page

All sensors and the Climate entity share a single device under **Settings → Devices & Services → Devices**.

## Troubleshooting

**Two devices appearing?** Ensure both `sensor.py` and `climate.py` use the exact same `device_info` identifiers tuple: `(DOMAIN, "daikin_alira-<host>")`. Otherwise Home Assistant will treat them as separate devices.

**Network or timeout issues?** Verify Home Assistant can reach the Daikin unit's IP address with no firewall blocking it. You can increase the timeout in `sensor.py` by adjusting the `async_timeout` setting.

**Wrong endpoint?** This integration assumes `http://<host>/dsiot/multireq`. If your unit requires HTTPS or a different path, edit `fetch_status` and the payload URLs in `sensor.py` accordingly.

Check Home Assistant logs (**Developer Tools → Logs**) for errors from `custom_components/daikin_alira`. Common issues include invalid JSON responses, incorrect host/IP, and unsupported hex codes.

## Development

Clone this repository into your local `custom_components` folder. Key entry points:

- **`sensor.py`** — creates `DataUpdateCoordinator`, fetches status, defines sensors
- **`climate.py`** — defines `DaikinClimate` and handles HVAC commands (`set_hvac_mode`, `set_temperature`, `set_fan_mode`)
- **`config_flow.py`** — provides a minimal UI for entering the host

## Credits

Author: [@cambot1901](https://github.com/cambot1901)

This integration follows Home Assistant's `ClimateEntity` and `CoordinatorEntity` patterns. Feel free to open issues or submit pull requests on GitHub.

---

*Last updated: May 31, 2025*
