from sense_hat import SenseHat
import paho.mqtt.client as paho
from time import sleep

sense = SenseHat()
sense.clear()

# Define the MQTT broker details
broker = "broker.emqx.io"  # Update with your broker address
port = 1883  # Default MQTT port

# Define your sensor reading functions
def read_temp():
    temperature = sense.get_temperature()
    return round(temperature, 2)

def read_pressure():
    pressure = sense.get_pressure()
    return round(pressure, 2)

def read_humidity():
    humidity = sense.get_humidity()
    return round(humidity, 2)

def read_magnetometer():
    heading = sense.get_compass()
    # Get the compass direction (N, NE, E, SE, S, SW, W, NW)
    directions = ['N', 'NE', 'E', 'SE', 'S', 'SW', 'W', 'NW']
    direction_label = directions[int((heading + 22.5) / 45) % 8]
    return round(heading, 2), direction_label

# MQTT callbacks
def on_connect(client, userdata, flags, rc):
    if rc == 0:
        print("Connected to MQTT broker successfully!")
    else:
        print(f"Failed to connect, return code {rc}")

def on_publish(client, userdata, mid):
    print(f"Publishing message with ID: {mid}")

# Create MQTT client and connect
client = paho.Client()
client.on_connect = on_connect
client.on_publish = on_publish
client.connect(broker, port, 60)

# Start the loop to manage connections and messages
client.loop_start()

while True:
    # Get sensor readings
    temperature = read_temp()
    pressure = read_pressure()
    humidity = read_humidity()
    heading, direction = read_magnetometer()

    # Publish sensor data to different topics with descriptive text
    temp_message = f"Topic: sensor/temperature - The temperature is {temperature}°C."
    client.publish("sensor/temperature", payload=temp_message, qos=1)
    print(f"\n{temp_message}")

    pressure_message = f"Topic: sensor/pressure - The barometric pressure is {pressure} hPa."
    client.publish("sensor/pressure", payload=pressure_message, qos=1)
    print(f"\n{pressure_message}")

    humidity_message = f"Topic: sensor/humidity - The humidity level is {humidity} %."
    client.publish("sensor/humidity", payload=humidity_message, qos=1)
    print(f"\n{humidity_message}")

    magnetometer_message = f"Topic: sensor/magnetometer - The magnetometer heading is {heading}° ({direction})."
    client.publish("sensor/magnetometer", payload=magnetometer_message, qos=1)
    print(f"\n{magnetometer_message}")

    # Line break for readability with custom text
    print("\n------------------------------ Publishing the topics ------------------------------")

    # Sleep for 1 second before publishing again
    sleep(1)
