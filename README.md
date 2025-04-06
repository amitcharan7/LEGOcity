from sense_hat import SenseHat
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
directory = "/home/raspberry/Desktop/raspberry/DNCT702_A2_Practical Assessment_8500001274_1/Task 3"

# Define functions for subscribing, receiving messages, and unsubscribing
def on_subscribe(client, userdata, mid, granted_qos):
    print("The subscribed topic is: "+ str(mid) + " " + str(granted_qos))

def on_message(client, userdata, msg):
    # Store data in separate CSV files based on topic name
    if msg.topic == "TempeTopic":
        write_to_csv("Temperature.csv", msg.payload)
        print(f"Temperature topic: {msg.payload}")
    
    elif msg.topic == "PressureTopic":
        write_to_csv("Pressure.csv", msg.payload)
        print(f"Pressure topic: {msg.payload}")
    
    elif msg.topic == "HumidityTopic":
        write_to_csv("Humidity.csv", msg.payload)
        print(f"Humidity topic: {msg.payload}")
    
    elif msg.topic == "MagnetometerTopic":
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
client.connect("broker_address", 1883, 60)  # Change "broker_address" to the actual MQTT broker address
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
            print("Joystick button UP pressed and released. The temperature: TempeTopic\n")
            sense.show_letter("U", text_colour=yellow)
            client.subscribe("TempeTopic", qos=0)
            client.unsubscribe("PressureTopic")
            client.unsubscribe("HumidityTopic")
            client.unsubscribe("MagnetometerTopic")
        
        elif event.direction == 'down':
            print("Joystick button DOWN pressed and released. The pressure: PressureTopic\n")
            sense.show_letter("D", text_colour=blue)
            client.subscribe("PressureTopic", qos=0)
            client.unsubscribe("TempeTopic")
            client.unsubscribe("HumidityTopic")
            client.unsubscribe("MagnetometerTopic")
        
        elif event.direction == 'left':
            print("Joystick button LEFT pressed and released. The humidity: HumidityTopic\n")
            sense.show_letter("L", text_colour=green)
            client.subscribe("HumidityTopic", qos=0)
            client.unsubscribe("TempeTopic")
            client.unsubscribe("PressureTopic")
            client.unsubscribe("MagnetometerTopic")
        
        elif event.direction == 'right':
            print("Joystick button RIGHT pressed and released. The magnetometer: MagnetometerTopic\n")
            sense.show_letter("R", text_colour=red)
            client.subscribe("MagnetometerTopic", qos=0)
            client.unsubscribe("TempeTopic")
            client.unsubscribe("PressureTopic")
            client.unsubscribe("HumidityTopic")
        
        elif event.direction == 'middle':
            print("Joystick button MIDDLE pressed and released")
            sense.show_letter("M", text_colour=red, back_colour=white)
            client.unsubscribe("TempeTopic")
            client.unsubscribe("PressureTopic")
            client.unsubscribe("HumidityTopic")
            client.unsubscribe("MagnetometerTopic")
