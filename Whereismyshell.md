
# Writeup: [Nombre de la Máquina]

## 1. Despliegue y Reconocimiento
Comenzamos desplegando la máquina y verificando conectividad.

![Despliegue](images/Pasted%20image%2020260116223601.png)

Realizamos un escaneo de puertos con **nmap**:

![Escaneo Nmap](images/Pasted%20image%2020260116223759.png)

Los resultados muestran que solo el **puerto 80** está abierto, así que procedemos a enumerar la página web.

## 2. Enumeración Web
Al acceder al servicio web, vemos la página de inicio que, a primera vista, parece normal.

![Web Home](images/Pasted%20image%2020260117175251.png)

Sin embargo, en el pie de página (footer) encontramos una pequeña pista. No sabemos si es un *rabbit hole*, pero la tendremos en cuenta.

![Pista Footer](images/Pasted%20image%2020260117175748.png)

Utilizando **Gobuster** para la enumeración de directorios, encontramos `/warning.html` y `/shell.php`.

![Gobuster](images/Pasted%20image%2020260117175834.png)

Al revisar `/warning.html` encontramos un mensaje. Lo primero que viene a la mente es atacar el archivo `shell.php` mediante *fuzzing* de parámetros.

![Warning Page](images/Pasted%20image%2020260117175922.png)

## 3. Explotación (RCE)
Tras realizar fuzzing, encontramos el parámetro correcto:

![Fuzzing Parametro](images/Pasted%20image%2020260117180910.png)

Lo probamos y confirmamos que tenemos **RCE** (Ejecución Remota de Comandos).

![RCE Test](images/Pasted%20image%2020260117180945.png)

Preparamos un *listener* en nuestra máquina atacante y ejecutamos el payload para entablar la conexión.

![Payload](images/Pasted%20image%2020260117181558.png)

Logramos acceso a la máquina:

![Acceso](images/Pasted%20image%2020260117181616.png)

## 4. Escalada de Privilegios
Una vez dentro, recordamos la pista encontrada en la web inicial. Revisamos el directorio `/tmp` y encontramos un archivo que contiene la contraseña de **root**.

![Password Root](images/Pasted%20image%2020260117181944.png)

Utilizamos la contraseña y logramos escalar privilegios a root.

![Root Access](images/Pasted%20image%2020260117182020.png)

