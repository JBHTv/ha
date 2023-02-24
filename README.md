blueprint:
  name: Contact Sensor Left Open Notification
  description: "Notifies you when a door or window is left open. 

    For more information on setting the click url, please visit the companion docs:
    https://companion.home-assistant.io/docs/notifications/notifications-basic/#opening-a-url

    If you'd like to notify multiple people, use a notify group: 
    https://www.home-assistant.io/integrations/notify.group/

    "
mqtt:
  broker: 192.168.1.84
  port: 1883
  username: mqtt
  password: Solcuat3

  domain: automation
  input:
    entity:
      name: Entity
      description: The entity that will trigger the automation. This must be a binary sensor. (ie. binary_sensor.fridge)
      selector:
        entity:
          domain: binary_sensor
    friendly_name:
      name: Device Friendly Name
      description: What should we call the entity in the notification? (ie. Fridge)
      default: ""
      selector:
        text:
    issue_state:
      name: Issue State
      description: The state that triggers the automation. Because this is a binary sensor, it can only be on or off. Use the developer tools to find the correct setting.
      default: "on"
      selector:
        select:
          options:
            - "on"
            - "off"
    seconds:
      name: Seconds before alert
      description: The amount of time in seconds that the device can be in the issue state before an alert is triggered.
      default: "150"
      selector:
        number:
          min: 1
          max: 3600
          mode: box
          unit_of_measurement: seconds
    mqtt_broker:
  name: MQTT Broker
  description: The MQTT broker to connect to. This should match the broker configured in Home Assistant's MQTT integration.
  selector:
    entity:
      domain: mqtt

mqtt_topic:
  name: MQTT Topic
  description: The topic to which the message will be published.
  selector:
    text:

variables:
  friendly_name: !input friendly_name
  entity: !input entity
  mqtt_broker: !input mqtt_broker
  mqtt_topic: !input mqtt_topic

trigger:
  platform: state
  entity_id: !input entity
  to: !input issue_state
  for:
    hours: 0
    minutes: 0
    seconds: !input seconds
action:
- service: mqtt.publish
  data:
    topic: !input mqtt_topic
    payload_template: '{{ message }}'
