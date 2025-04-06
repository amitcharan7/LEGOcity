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
    print(f"Connected with result code {rc}")

# Create MQTT client and connect
client = paho.Client()
client.on_connect = on_connect
client.connect(broker, port, 60)
client.loop_start()

while True:
    # Get sensor readings
    temperature = read_temp()
    pressure = read_pressure()
    humidity = read_humidity()
    heading, direction = read_magnetometer()

    # Publishing and printing the topics with line breaks
    print("Publishing the topics............")
    client.publish("sensor/temperature", payload=f"The temperature is {temperature}°C", qos=1)
    print(f"Publishing Temperature: {temperature}°C\n")

    print("Publishing the topics............")
    client.publish("sensor/pressure", payload=f"The barometric pressure is {pressure} hPa", qos=1)
    print(f"Publishing Pressure: {pressure} hPa\n")

    print("Publishing the topics............")
    client.publish("sensor/humidity", payload=f"The humidity level is {humidity}%", qos=1)
    print(f"Publishing Humidity: {humidity}%\n")

    print("Publishing the topics............")
    client.publish("sensor/magnetometer", payload=f"The magnetometer heading is {heading}° ({direction})", qos=1)
    print(f"Publishing Magnetometer: {heading}° ({direction})\n")

    # Sleep for 1 second before publishing again
    sleep(1)
