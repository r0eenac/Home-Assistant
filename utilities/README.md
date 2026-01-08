## 🦠 Mold & Insulation Analysis ProjectThis project adds advanced logic to Home Assistant to monitor wall insulation quality ($f_{Rsi}$) 
and predict mold growth risk based on real-time data and weather forecasts.

## 📂 File Structure
Ideally, place these files under a packages/utilities folder or combine them into your templates.yaml.

mold_and_insulation.yaml: The main configuration file containing the sensor logic.

(Conceptually covers logic for Mold_Risk.yaml if you chose to split them).

## 🧩 Logic Flow

```mermaid
graph TD
    %% Inputs
    Sensors[📡 Physical Sensors] -->|Temp: In, Out, Wall| fRsi_Calc
    Weather[🌤️ Weather Forecast] -->|Tomorrow's Min Temp| Prediction
    
    %% Step 1: Insulation Factor
    subgraph "Step 1: Determine Wall Quality"
        fRsi_Calc(🧮 Calculate fRsi Factor) -->|Result: 0.1 - 0.9| fRsi_Sensor{fRsi Sensor}
        fRsi_Sensor --"Value (e.g. 0.72)"--> Mold_Calc
        fRsi_Sensor --"Value"--> Prediction
    end
    
    %% Step 2: Real Time Risk
    subgraph "Step 2: Real-Time Risk"
        Mold_Calc(🦠 Calculate Dew Point vs Wall Temp) --> Risk_Sensor[⚠️ Current Mold Risk %]
    end

    %% Step 3: Future Prediction
    subgraph "Step 3: Future Warning"
        Prediction(🔮 Forecast Wall Temp Tomorrow) --> Warning[🔔 Binary Sensor: Warning Tomorrow]
    end

    style fRsi_Sensor fill:#f9f,stroke:#333,stroke-width:2px
    style Risk_Sensor fill:#ffcccc,stroke:#f00
    style Warning fill:#ffeebb,stroke:#d48806
  ```


  ## 🛠️ The Sensors
  1. Wall Insulation Factor ($f_{Rsi}$)This sensor calculates the thermal quality of your wall empirically.
  File: mold_and_insulation.yamlPhysics: It measures how well the wall resists the outside cold relative to 
  the inside heat.Formula:$$f_{Rsi} = \frac{T_{Wall} - T_{Out}}{T_{Room} - T_{Out}}$$


  Why is this a Template? While the wall's insulation property is theoretically constant, environmental readings fluctuate. 
  Continuous calculation allows us to filter out noise and only get valid readings when the temperature difference ($\Delta T$) is sufficient (>0.5°C).

  Interpretation:

  > 0.7: Good Insulation. The wall stays warm (close to room temp).

  < 0.7: Thermal Bridge. The wall is cold (leaking heat), high risk of mold.




   ```yaml
    - sensor:
    - name: "Bedroom Wall Insulation Factor (fRsi)"
      unique_id: bedroom_wall_frsi
      unit_of_measurement: "fRsi"
      state: >
        {% set t_wall = states('sensor.bedroom_wall_temperature') | float(0) %}
        {% set t_out = states('sensor.outdoor_temperature') | float(0) %}
        {% set t_in = states('sensor.bedroom_temperature') | float(0) %}
        {% set delta_temp = t_in - t_out %}
        
        {% if delta_temp | abs > 0.5 %}
          {{ ((t_wall - t_out) / delta_temp) | round(2) }}
        {% else %}
          unavailable
        {% endif %}

   ```

2. Current Mold Risk (%)
Calculates the probability of mold growth right now.

Logic: Mold begins to form when the relative humidity on the wall surface exceeds 80%. This happens when the wall temperature drops near the Dew Point.

Output: 0% (Safe) to 100% (Critical/Condensation).

 ```yaml
- sensor:
    - name: "Bedroom Mold Risk"
      unit_of_measurement: "%"
      state: >
        ... (See full code in mold_and_insulation.yaml)

   ```

3. Forecast Warning (Tomorrow)Predicts if mold conditions will occur tonight/tomorrow morning using the calculated $f_{Rsi}$ and weather forecast.
   Logic:Take the known $f_{Rsi}$ of the wall.
   Take the lowest forecasted temperature for tonight.
   Calculate what the wall temperature will be.
   Compare against expected Dew Point.

 ```yaml
   - binary_sensor:
    - name: "Mold Warning Tomorrow"
      state: >
        ... (See full code in mold_and_insulation.yaml)

   ```

## 🚀 Installation
Ensure you have a packages folder mapped in configuration.yaml:
 ```yaml
homeassistant:
  packages: !include_dir_named packages
   ```


Create packages/utilities/mold_and_insulation.yaml.

Paste the template codes.

Restart Home Assistant.
