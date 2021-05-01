---
title: Statistics
description: Instructions on how to integrate statistical sensors into Home Assistant.
ha_category:
  - Utility
  - Sensor
ha_iot_class: Local Polling
ha_release: '0.30'
ha_quality_scale: internal
ha_codeowners:
  - '@fabaff'
ha_domain: statistics
ha_platforms:
  - sensor
---

The `statistics` sensor platform observes the state of a source sensor and provides statistical characteristics about its recent past. This integration can be quite useful in automations, e.g., to trigger an action when the number of brewed coffee over a day gets too high (see `total`) or when the air humidity in the bathroom settles after a hot shower (see `change`).

The created sensor presents one characteristic as its state (the `mean` value by default) and many more as attributes, see below. The period or number of recent state changes to be considered is defined as part of the sensor configuration. The sensor and its characteristics update with every source sensor update.

If the source sensor is a binary sensor, then only state changes are counted.

Assuming the [`recorder`](/integrations/recorder/) integration is running (either configured explicitly or as part of a meta-integration/dependency, e.g., [`default_config`](/integrations/default_config/), [`history`](/integrations/history/), etc.), historical sensor data is read from the database on startup and is available immediately after a restart of the platform. If the [`recorder`](/integrations/recorder/) integration is *not* running, it can take time for the sensor to start reporting data because some attribute calculations require more than one value.

## Characteristics

The following statistical characteristics are provided for the collected source sensor. Pay close attention to the right configuration of `sampling_size` and `max_age`, as most characterists are directly related to the count or time period of processed historic data.

| Identifier | Description |
| ---------- | ----------- |
| `count` | The number of source sensor readings stored. This number is limited by `sampling_size` and can be low within the bounds of `max_age`.
| `total` | The sum of all source sensor measurements stored.
| `mean` | The average value computed for all measurements stored. Be aware that this does not take into account uneven time steps.
| `median` | The [median](https://en.wikipedia.org/wiki/Mode_(statistics)#Comparison_of_mean,_median_and_mode) value computed for all measurements stored.
| `standard_deviation` | The [standard deviation](https://en.wikipedia.org/wiki/Standard_deviation) of an assumed normal distribution of all stored measurements. 
| `variance` | The [variance](https://en.wikipedia.org/wiki/Standard_deviation) of an assumed normal distribution. 
| `min_value` | 
| `max_value` | 
| `min_age` | 
| `max_age` | 
| `change` | 
| `average_change` | 
| `change_rate` | 


## Configuration

To enable the statistics sensor, add the following lines to your `configuration.yaml`:

```yaml
# enable the recorder integration (optional)
recorder:

# Example configuration.yaml entry
sensor:
  - platform: statistics
    entity_id: sensor.cpu
  - platform: statistics
    entity_id: binary_sensor.movement
    max_age:
      minutes: 30
```

{% configuration %}
entity_id:
  description: The source sensor to observe. Only [sensors](/integrations/sensor/) and [binary sensor](/integrations/binary_sensor/) are supported.
  required: true
  type: string
name:
  description: Name of the sensor to use in the frontend.
  required: false
  default: Stats
  type: string
state_characteristic:
  description: The characteristic that should be used as the state of the statistics sensor. All characteristics are provided as attributes.
  required: false
  default: mean
  type: string
sampling_size:
  description: Maximum number of source sensor readings stored. Be sure to choose a reasonably high number if the limit should be driven by `max_age` instead.
  required: false
  default: 20
  type: integer
max_age:
  description: Maximum age of measurements. Setting this to a time interval will cause older values to be discarded. Please note that you might have to increase the [sampling_size](/integrations/statistics#sampling_size) parameter. If you e.g., have a sensor value updated every second you will, by default, only get a max_age of 20s. Furthermore the sensor gets `unkown` if the entity is not updated within the time interval.
  required: false
  type: time
precision:
  description: Defines the precision of the calculated values, through the argument of round().
  required: false
  default: 2
  type: integer
{% endconfiguration %}

<p class='img'>
  <img src='/images/screenshots/stats-sensor.png' />
</p>
