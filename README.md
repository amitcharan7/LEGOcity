from sense_emu import SenseHat
import paho.mqtt.client as paho
import os

# Define colors
red = (255, 0, 0)
blue = (0, 0, 255)
green = (0, 255, 0)
yellow = (255, 255, 0)
white = (255, 255, 255)
black = (0, 0, 0)

sense = SenseHat()
sense.clear()

# Directory path to store CSV files
directory = "/home/raspberry/Desktop/raspberry/DNCT702_A2_Practical_Assessment_8500001274_1/Task 3"

# Define MQTT broker details
broker = "broker.emqx.io"  # Update with your broker address
port = 1883  # Default MQTT port

# Define functions for subscribing, receiving messages, and unsubscribing
#def on_subscribe(client, userdata, mid, granted_qos):
#    print("The subscribed topic is: "+ str(mid) + " " + str(granted_qos))

# Get rid of on subscribe and add on connect.

def on_message(client, userdata, msg):
    # Store data in separate CSV files based on topic name
    if msg.topic == "sensor/temperature":  # Match the topic from the publisher
        write_to_csv("Temperature.csv", msg.payload)
        print(f"Temperature topic: {msg.payload}")
    
    elif msg.topic == "sensor/pressure":  # Match the topic from the publisher
        write_to_csv("Pressure.csv", msg.payload)
        print(f"Pressure topic: {msg.payload}")
    
    elif msg.topic == "sensor/humidity":  # Match the topic from the publisher
        write_to_csv("Humidity.csv", msg.payload)
        print(f"Humidity topic: {msg.payload}")
    
    elif msg.topic == "sensor/magnetometer":  # Match the topic from the publisher
        write_to_csv("Magnetometer.csv", msg.payload)
        print(f"Magnetometer topic: {msg.payload}")

# Function to write data to CSV file
def write_to_csv(file_name, data):
    file_path = os.path.join(directory, file_name)
    with open(file_path, mode="a") as file:
        file.write(str(data) + "\n")  # Append data with a newline

# Assign values to the MQTT client
client = paho.Client()
client.on_subscribe = on_subscribe
client.connect(broker, port, 60)  # Now using the defined broker and port
client.on_message = on_message
client.loop_start()

# Printing initial instructions for the user
print("...........................Program Starts................\n")
print("....Use the Joystick to Subscribe a topic......: \n")
print("Joystick control: Up will show you a Temperature Topic")
print("Joystick control: Down will show you a Pressure Topic ")
print("Joystick control: Left will show you a Humidity Topic")
print("Joystick control: Right will show you a Magnetometer Topic\n")

while True:
    for event in sense.stick.get_events():
        # The user checks directions with the joystick
        if event.direction == 'up':
            print("Joystick button UP pressed and released. The temperature: sensor/temperature\n")
            sense.show_letter("U", text_colour=yellow)
            client.subscribe("sensor/temperature", qos=0)  # Subscribe to the correct topic
            client.unsubscribe("sensor/pressure")
            client.unsubscribe("sensor/humidity")
            client.unsubscribe("sensor/magnetometer")
        
        elif event.direction == 'down':
            print("Joystick button DOWN pressed and released. The pressure: sensor/pressure\n")
            sense.show_letter("D", text_colour=blue)
            client.subscribe("sensor/pressure", qos=0)  # Subscribe to the correct topic
            client.unsubscribe("sensor/temperature")
            client.unsubscribe("sensor/humidity")
            client.unsubscribe("sensor/magnetometer")
        
        elif event.direction == 'left':
            print("Joystick button LEFT pressed and released. The humidity: sensor/humidity\n")
            sense.show_letter("L", text_colour=green)
            client.subscribe("sensor/humidity", qos=0)  # Subscribe to the correct topic
            client.unsubscribe("sensor/temperature")
            client.unsubscribe("sensor/pressure")
            client.unsubscribe("sensor/magnetometer")
        
        elif event.direction == 'right':
            print("Joystick button RIGHT pressed and released. The magnetometer: sensor/magnetometer\n")
            sense.show_letter("R", text_colour=red)
            client.subscribe("sensor/magnetometer", qos=0)  # Subscribe to the correct topic
            client.unsubscribe("sensor/temperature")
            client.unsubscribe("sensor/pressure")
            client.unsubscribe("sensor/humidity")
        
        elif event.direction == 'middle':
            print("Joystick button MIDDLE pressed and released")
            sense.show_letter("M", text_colour=red, back_colour=white)
            client.unsubscribe("sensor/temperature")
            client.unsubscribe("sensor/pressure")
            client.unsubscribe("sensor/humidity")
            client.unsubscribe("sensor/magnetometer")

