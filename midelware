import socket
import time
import threading
import sys

def main():
    server_thread = threading.Thread(target=servidor)
    server_thread.start()

    while True:
        print("\nSistema Distribuido")
        print("-----------------------------------------")
        print("Lista de opciones:")
        print("1.Conexion\n2.Conversacion\n3.Salir")
        print("-----------------------------------------")

        opc = input("\nElija la opcion a realizar:")

        if opc == '1':
            instruccion_datos(conexion())
        elif opc == '2':
            print("\nConexiones")
            guardados_datos()
        elif opc == '3':
            print("Saliendo del programa...")
            sys.exit(0)
        else:
            print("\nError")

def conexion():
    try:
        with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as s:
            s.connect(("8.8.8.8", 80))
            #ip_address = s.getsockname()[0]
            return s.getsockname()[0]
    except Exception as e:
        print("Se ha detectado un error:", e)
        return None

def servidor():
    try:
        with open("catalogo.txt", "r") as file:
            server_info = [line.strip().split() for line in file.readlines() if line.strip().split()[0] == conexion()]
        if server_info:
            ip, puerto = server_info[0]
            puerto = int(puerto)
        else:
            print("IP denegada")
            return
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as server_socket:
            server_socket.bind((ip, puerto))
            server_socket.listen(5)
            while True:
                conecta_cliente, client_address = server_socket.accept()
                print(f"\nConexion: {client_address} \nTiempo: {time.strftime('%Y/%m/%d %H:%M:%S')}")
                client_thread = threading.Thread(target=cliente, args=(conecta_cliente,))
                client_thread.start()
    except Exception as e:
        print("\nError", e)

def instruccion_datos(ip_host):
    try:
        with open("catalogo.txt", "r") as file:
            remote_servers = [line.strip().split() for line in file.readlines() if not line.strip().split()[0] == ip_host]
        for i, (ip, puerto) in enumerate(remote_servers, 1):
            print(f"{i} : dir {ip} puerto {puerto}")
        op = int(input("\nElija con quien desea la coneccion:"))
        if 1 <= op <= len(remote_servers):
            remote_address, puerto = remote_servers[op - 1]
            puerto = int(puerto)
            with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as conecta_cliente:
                conecta_cliente.connect((remote_address, puerto))
                print("Direccion IP conectada:", remote_address, "\nPuerto ", puerto)
                while True:
                    instruccion = input("\nDesea enviar un dato (0 si desea salir):")
                    if instruccion.lower() == '0':
                        break
                    message_with_timestamp = f"[{time.strftime('%Y/%m/%d %H:%M:%S')}] {instruccion}"
                    conecta_cliente.sendall(message_with_timestamp.encode())
                    almacen_datos(ip_host, message_with_timestamp)
                    respuestad = conecta_cliente.recv(1024)
                    print("\n", respuestad.decode())
        else:
            print("Error")
    except Exception as e:
        print("Error, no se conecta", e)

def cliente(conecta_cliente):
    try:
        while True:
            datoc = conecta_cliente.recv(1024)
            if not datoc:
                break
            print("Dato entrante", datoc.decode())
            if '[' in datoc.decode() and ']' in datoc.decode():
                timestamp = datoc.decode().split('[')[1].split(']')[0]
                print("Timestamp:", timestamp)
            almacen_datos(conecta_cliente.getpeername()[0], datoc.decode())
            if datoc.decode().strip().lower() == '0':
                break
            conecta_cliente.sendall("Dato recibido".encode())
    except Exception as e:
        print("Error", e)
    finally:
        conecta_cliente.close()
        print("\nFin de la conexion externa")

def almacen_datos(ip_address, instruccion):
    with open("almacena.txt", "a") as file:
        file.write(f"Datos: {instruccion}, Dir IP: {ip_address}, Timestamp: {time.strftime('%Y/%m/%d %H:%M:%S')}\n")

def guardados_datos():
    try:
        with open("almacena.txt", "r") as file:
            print(file.read())
    except FileNotFoundError:
        print("VACIO")

main()
