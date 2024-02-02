import bluetooth
from plyer import notification
import time
import math

def calculate_distance(rssi, tx_power):
    if rssi == 0:
        return -1.0  # if we cannot determine distance, return -1.
    ratio = rssi * 1.0 / tx_power
    if ratio < 1.0:
        return math.pow(ratio, 10)
    else:
        return (0.89976) * math.pow(ratio, 7.7095) + 0.111

def notify():
    notification.notify(
        title='Bluetooth Tracking',
        message='Device moved more than 5 meters away!',
        app_name='Bluetooth Tracking App'
    )

def track_devices():
    target_address = '00:00:00:00:00:00'  # Replace with the address of the other device
    while True:
        nearby_devices = bluetooth.discover_devices()
        for addr in nearby_devices:
            if addr == target_address:
                rssi = bluetooth.lookup_name(addr)
                distance = calculate_distance(rssi, tx_power=-59)  # tx_power is a constant and varies for devices
                if distance > 5:
                    notify()
        time.sleep(1)  # Check every 1 second

if __name__ == "__main__":
    track_devices()
