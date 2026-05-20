# Propane Tank Monitor

ESPHome project for an ESP8266 connected to an HX711 load-cell board. The goal is to turn a bathroom scale into a propane tank monitor that reports tank weight and fill percentage to Home Assistant.

## Files

- `propane-tank-monitor.yaml`: main ESPHome configuration
- `secrets.yaml`: local secrets used by ESPHome (not committed)

## Hardware

Default pin mapping in the ESPHome config:

- HX711 `DT` -> `D6`
- HX711 `SCK` -> `D5`
- HX711 `VCC` -> `3.3V`
- HX711 `GND` -> `GND`

Connect the bathroom scale load cell bridge to the HX711 in the normal bridge arrangement:

- `E+` / `E-` for excitation
- `A+` / `A-` for the measurement pair

If your board uses different GPIO labels, update the `dout_pin` and `clk_pin` substitutions in the YAML.

## Setup

1. Fill in your Wi-Fi credentials, API key, and OTA password in `secrets.yaml`.
3. Adjust the HX711 calibration points in `propane-tank-monitor.yaml` to match your scale.
4. Set the empty and full tank weights from Home Assistant once you know the tank and cylinder totals.
5. Flash the device with ESPHome.

## Calibration

The project exposes:

- `Tank Weight` in kilograms
- `Propane Remaining` in kilograms
- `Propane Fill Level` as a percentage
- `Sensor Update Period` in seconds

To calibrate the scale, replace the placeholder `calibrate_linear` points with your own known readings. Once the weight reading is correct, use the `Tank Empty Weight` and `Tank Full Weight` entities to define the fill range.

Use `Sensor Update Period` to control how often the HX711 is polled, without reflashing.

## Notes

- The default board is `nodemcuv2`. Change it if your ESP8266 uses a different board definition.
- `web_server` is enabled for quick local inspection. Remove it if you do not want the extra web UI.
