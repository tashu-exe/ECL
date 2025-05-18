import paho.mqtt.client as mqtt
import Adafruit_DHT
import time

broker = 'localhost'
port = 1883
topic = 'home/sensor/dht11'

client = mqtt.Client()
client.connect(broker, port)

sensor = Adafruit_DHT.DHT11
gpio = 4

while True:
    humidity, temperature = Adafruit_DHT.read_retry(sensor, gpio)
    if humidity is not None and temperature is not None:
        payload = f'Temperature: {temperature:.1f}°C, Humidity: {humidity:.1f}%'
        client.publish(topic, payload)
        print(payload)
    else:
        print("Failed to retrieve data from humidity sensor")
    time.sleep(5)
