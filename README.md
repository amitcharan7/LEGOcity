from sense_emu import SenseHat
from time import sleep

sense = SenseHat()
sense.clear()

# Define colors
red = (255, 0, 0)
blue = (0, 0, 255)
green = (0, 255, 0)
yellow = (255, 255, 0)
white = (255, 255, 255)
black = (0, 0, 0)

# Function to convert heading degrees to direction label
def get_direction_label(heading):
    directions = ['N', 'NE', 'E', 'SE', 'S', 'SW', 'W', 'NW']
    index = int((heading + 22.5) / 45) % 8
    return directions[index]

while True:
    # Get readings
    temperature = round(sense.get_temperature(), 1)
    humidity = round(sense.get_humidity(), 1)
    pressure = round(sense.get_pressure(), 1)
    heading = round(sense.get_compass(), 1)
    direction_label = get_direction_label(heading)

    # Print full messages to console
    print(f"The temperature reading is {temperature} degrees Celsius.")
    print(f"The humidity reading is {humidity} percent.")
    print(f"The pressure reading is {pressure} hectopascals.")
    print(f"Direction: {heading}° ({direction_label})")

    # Display scrolling messages with colored text on black background
    sense.show_message(f"Temp: {temperature}C", text_colour=red, back_colour=black, scroll_speed=0.07)
    sense.show_message(f"Humidity: {humidity}%", text_colour=blue, back_colour=black, scroll_speed=0.07)
    sense.show_message(f"Pressure: {pressure}hPa", text_colour=green, back_colour=black, scroll_speed=0.07)
    sense.show_message(f"Dir: {heading}° ({direction_label})", text_colour=yellow, back_colour=black, scroll_speed=0.07)

    sleep(3)  # Wait 3 seconds before next reading cycle
