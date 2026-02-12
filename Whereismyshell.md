
Maquina

![[Pasted image 20260116223601.png]]
Desplegamos la maquina 

Reconocimiento

![[Pasted image 20260116223759.png]]
Aqui tenemos los resultados del escaneo de nmap, vemos que solo tiene el puerto 80 asi que me ire a enumerar la pagina web

![[Pasted image 20260117175251.png]]
Aqui tenemos el inicio de la pagina web, no veo nada fuera de lo normal

![[Pasted image 20260117175748.png]]
Abajo del todo veo esta pequena pista, no se si sera un rabbit hole asi que seguire enumerando

![[Pasted image 20260117175834.png]]
Con gobuster encontre estos directorios, hay un warning.html y un shell.php que se ven interesantes

![[Pasted image 20260117175922.png]]
En /warning.html nos encontramos este mensaje, lo primero que se me viene a la mente es el directorio shell.php y hacerle fuzzing, asi que lo intentare


![[Pasted image 20260117180910.png]]
Y despues de un rato aqui tenemos el parametro 

![[Pasted image 20260117180945.png]]
Lo pruebo y ya tenemos RCE

![[Pasted image 20260117181558.png]]
Usando este payload me pongo en escucha en mi maquina

![[Pasted image 20260117181616.png]]
Y estamos dentro

![[Pasted image 20260117181944.png]]
Ya dentro recuertdo de la pista del inicio, y me meto a /tmp y encuentro la contrasena de root

![[Pasted image 20260117182020.png]]
y asi de facil somos root

# Que aprendi

Sintaxis de wfuzz:

```
wfuzz -c --hc=404,500 -t 200 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u "http://172.17.0.2/shell.php?FUZZ=id"
```

### Desglose del Comando

1. **`wfuzz`**
    
    - Es el nombre del programa que estás ejecutando.
        
2. **`-c`**
    
    - Significa **Color**. Le indica a wfuzz que muestre la salida con colores, lo cual facilita mucho la lectura para diferenciar rápidamente entre códigos de estado (ej. verde para éxito, rojo para errores).
        
3. **`--hc=404,500`**
    
    - Significa **Hide Code** (Ocultar Código).
        
    - Esto filtra los resultados. Le estás diciendo a la herramienta: _"No me muestres ninguna respuesta que tenga un código de estado HTTP 404 (No encontrado) o 500 (Error del servidor)"_.
        
    - Esto es vital para limpiar la salida y ver solo lo que es diferente o interesante.
        
4. **`-t 200`**
    
    - Significa **Threads** (Hilos).
        
    - Establece el número de conexiones simultáneas en 200. Esto hace que el escaneo sea mucho más rápido, aunque una cifra tan alta podría saturar un servidor frágil.
        
5. **`-w /usr/share/seclists/.../directory-list-2.3-medium.txt`**
    
    - Significa **Wordlist** (Lista de palabras).
        
    - Indica la ruta del archivo de texto que contiene las palabras que se van a probar. En este caso, estás usando una lista muy famosa llamada _SecLists_ (versión medium), que contiene miles de nombres de directorios y parámetros comunes.
        
6. **`-u "http://172.17.0.2/shell.php?FUZZ=id"`**
    
    - Significa **URL**. Es el objetivo del ataque.
        
    - **Lo más importante aquí es la palabra clave `FUZZ`**.
        
    - Wfuzz reemplazará la palabra `FUZZ` por cada una de las palabras de tu _wordlist_.




