# UART Data Handling with MicroPython

This repository contains various scripts to demonstrate UART communication using MicroPython. The examples include reading data from UART, converting it to different formats, and showcasing different ways to handle UART data.

## Table of Contents

1. [Introduction](#introduction)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [Scripts](#scripts)
   - [Basic UART Reader](#basic-uart-reader)
   - [UART Data to Dictionary](#uart-data-to-dictionary)
   - [Storing UART Data to File](#storing-uart-data-to-file)
   - [Sending UART Data over Network](#sending-uart-data-over-network)
   - [UART Data with Timestamp](#uart-data-with-timestamp)
   - [UART Data Filtering](#uart-data-filtering)
   - [UART Data Logging](#uart-data-logging)
5. [Contributing](#contributing)
6. [License](#license)

## Introduction

This project demonstrates how to use UART communication with MicroPython on ESP32. It includes multiple scripts to showcase different methods and approaches for handling UART data.

## Requirements

- ESP32 board
- MicroPython firmware
- USB cable
- UART compatible device

## Installation

1. Flash MicroPython firmware onto your ESP32.
2. Make the script in Arduino UNO board and print status.
3. Copy the respective code from github what you need.
4. Connect ESP32 and Arduino UNO with respect to RX, TX pin accordingly.
5. Save the script into the hardware and than run.

## Scripts

```python
import machine
import time

uart = machine.UART(1, baudrate=9600, tx=17, rx=16)

while True:
    if uart.any():
        data = uart.readline()
        print(data)
    time.sleep(1)
```

### UART Data to Dictionary
This script converts UART data to a dictionary format.

```python
import machine
import time

uart = machine.UART(1, baudrate=9600, tx=17, rx=16)

def uart_to_dict(data):
    try:
        # Assuming data format is key1:value1,key2:value2,...
        data_str = data.decode('utf-8').strip()
        data_pairs = data_str.split(',')
        data_dict = {pair.split(':')[0]: pair.split(':')[1] for pair in data_pairs}
        return data_dict
    except Exception as e:
        print("Error converting data to dictionary:", e)
        return {}

while True:
    if uart.any():
        data = uart.readline()
        data_dict = uart_to_dict(data)
        print(data_dict)
    time.sleep(1)
```

### Storing UART Data to File

This script stores UART data into a file on the ESP32 filesystem.

```python
import machine
import time

uart = machine.UART(1, baudrate=9600, tx=17, rx=16)

while True:
    if uart.any():
        data = uart.readline()
        with open('uart_data.txt', 'a') as f:
            f.write(data.decode('utf-8'))
            f.write('\n')
    time.sleep(1)
```

### Sending UART Data over Network

This script sends UART data over a network using a simple socket connection.

```python
import machine
import time
import socket

uart = machine.UART(1, baudrate=9600, tx=17, rx=16)

def send_over_network(data):
    addr = ('<IP_ADDRESS>', 8080)  # Replace <IP_ADDRESS> with the target IP address
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect(addr)
    s.send(data)
    s.close()

while True:
    if uart.any():
        data = uart.readline()
        send_over_network(data)
    time.sleep(1)
```

### UART Data with Timestamp

This script adds a timestamp to the UART data before printing.

```python
import machine
import time

uart = machine.UART(1, baudrate=9600, tx=17, rx=16)

while True:
    if uart.any():
        data = uart.readline()
        timestamp = time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())
        print(f"{timestamp}: {data.decode('utf-8').strip()}")
    time.sleep(1)
```

### UART Data Filtering

This script filters the UART data based on a specific keyword.

```python
import machine
import time

uart = machine.UART(1, baudrate=9600, tx=17, rx=16)

keywords = ['TEMP', 'HUM'] # Filteration

while True:
    if uart.any():
        data = uart.readline()
        data_str = data.decode('utf-8').strip()
        for keyword in keywords:
            if keyword in data_str:
                print(data_str)
                break
    time.sleep(1)

```

### UART Data Logging

This script logs UART data to the console and a file.

```python
import machine
import time

uart = machine.UART(1, baudrate=9600, tx=17, rx=16)

while True:
    if uart.any():
        data = uart.readline()
        data_str = data.decode('utf-8').strip()
        print(data_str)
        with open('uart_log.txt', 'a') as f:
            f.write(data_str + '\n')
    time.sleep(1)
```
### Send Data to PythonAnywhere

This script sends data to PythonAnywhere.

```python
import machine
import time
import urequests

uart = machine.UART(1, baudrate=9600, tx=17, rx=16)

def parse_data(data):
    try:
        return dict(x.split('=') for x in data.decode().strip().split(','))
    except Exception as e:
        print("Error parsing data:", e)
        return {}

while True:
    if uart.any():
        data = uart.readline()
        data_dict = parse_data(data)
        print(data_dict)
        try:
            response = urequests.post('http://yourpythonanywheresite.com/api/data', json=data_dict)
            print(response.text)
        except Exception as e:
            print("Error sending data:", e)
    time.sleep(1)
```
### Send Data to Firebase Realtime Database

This script sends data to Firebase Realtime Database.

```python
import machine
import time
import urequests

uart = machine.UART(1, baudrate=9600, tx=17, rx=16)
firebase_url = 'https://your-firebase-database.firebaseio.com/data.json'

def parse_data(data):
    try:
        return dict(x.split('=') for x in data.decode().strip().split(','))
    except Exception as e:
        print("Error parsing data:", e)
        return {}

while True:
    if uart.any():
        data = uart.readline()
        data_dict = parse_data(data)
        print(data_dict)
        try:
            response = urequests.post(firebase_url, json=data_dict)
            print(response.text)
        except Exception as e:
            print("Error sending data:", e)
    time.sleep(1)
```

## Contributing
Feel free to fork this repository, create new branches, and submit pull requests. Please make sure to update the documentation as needed.

## License
This project is licensed under the MIT License.
