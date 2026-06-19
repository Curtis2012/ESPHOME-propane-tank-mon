# Propane Tank Monitor

ESPHome project for an ESP8266 connected to an HX711 load-cell board. The goal is to turn a bathroom scale into a propane tank monitor that reports tank weight and fill percentage to Home Assistant.

## Files

- `propane-tank-monitor.yaml`: main ESPHome configuration
- `secrets.yaml`: local secrets used by ESPHome (not committed)

## Hardware

Default pin mapping in the ESPHome config:

- HX711 `DT` -> `GPIO4`
- HX711 `SCK` -> `GPIO5`
- HX711 `VCC` -> `3.3V`
- HX711 `GND` -> `GND`

Connect the bathroom scale load cell bridge to the HX711 in the normal bridge arrangement:

- `E+` / `E-` for excitation
- `A+` / `A-` for the measurement pair

If your board uses different GPIO labels, update the `dout_pin` and `clk_pin` substitutions in the YAML.

## Setup

1. Fill in your Wi-Fi credentials, API key, and OTA password in `secrets.yaml`.
2. Adjust the HX711 calibration points in `propane-tank-monitor.yaml` to match your scale.
3. In Home Assistant, set both tank profiles:
	- Tank 1: `Tank 1 Tare Weight`, `Tank 1 Capacity`, `Tank 1 Regulator Weight`
	- Tank 2: `Tank 2 Tare Weight`, `Tank 2 Capacity`, `Tank 2 Regulator Weight`
4. Select the active profile using `Active Tank`.
5. Optional: turn on `Calibration Mode` and set `Calibration Offset` (positive or negative) to apply a live correction.
6. Flash the device with ESPHome.

## Calibration

The project exposes:

- `Active Tank` profile selector (`Tank 1` or `Tank 2`)
- `Calibration Mode` switch
- `Calibration Offset` in kilograms (signed)
- `Tank Weight` in kilograms
- `Tank 1 Tare Weight`, `Tank 1 Capacity`, and `Tank 1 Regulator Weight`
- `Tank 2 Tare Weight`, `Tank 2 Capacity`, and `Tank 2 Regulator Weight`
- `Propane Remaining` in kilograms
- `Propane Fill Level` as a percentage
- `Sensor Update Period` in seconds

To calibrate the scale, replace the placeholder `calibrate_linear` points with your own known readings. Once the weight reading is correct, set each tank profile using tare weight, propane capacity, and regulator weight.

Current defaults for both tank profiles are initialized from the previous JSON config:

- Tare: `5.8967 kg`
- Capacity: `11.3398 kg`
- Regulator: `0.5 kg`

When `Calibration Mode` is enabled, `Calibration Offset` is added to the measured tank weight before the propane calculations.

The fill calculation uses the active profile:

- Effective empty weight = tare + regulator
- Propane remaining = measured weight - effective empty weight (clamped to 0..capacity)
- Fill level = remaining / capacity

Use `Sensor Update Period` to control how often the HX711 is polled, without reflashing.

## Notes

- The default board is `huzzah` (Adafruit Feather HUZZAH ESP8266). Change it if your hardware uses a different board definition.
- `web_server` is enabled for quick local inspection. Remove it if you do not want the extra web UI.
