from sense_hat import SenseHat
import paho.mqtt.client as paho #You may use your own Alias
from time import sleep

sense = SenseHat()
sense.clear()


def on_publish(client, userdata, mid):
    print("Publishing the topics: "+str(mid))


#define your function of sensors (sample - define it for other topics)
def read_temp():
    temperature = sense.get_temperature()
    temperature = round(temperature,2)
    return temperature



client = paho.Client()
client.on_publish = on_publish
client.connect("localhost", 1883) #Use your broker details here.
client.loop_start()

while True:
    print ("Publishing the topics............")
    (rc, mid) = client.publish("TempeTopic", "The Temperature is: "+ str(read_temp()), qos=1)
    print("Publishing Temperature "+str (read_temp()) + "\n") # Use the same analogy to publish other topics

    sleep(put your interval here)
