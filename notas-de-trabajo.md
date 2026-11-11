## Parte 1 · Adquisición en vivo
1. **El estado inicial.** 
Hora 16:26 PM 11-11-2026
![[escritorio 1.png]]
En el escritorio solo podemos ver el blog de nota abierto, y el txt de mara

2. **La memoria RAM**
Hora 16:30 PM 11-11-2026
![[RAM.png]]
![[hash-Dumpit.png]]
Para la memoria RAM hemos usado el programa DumpIT, luego hemos comprobado su hash con el siguiente comando 
```
-hashfile "D:\windows-7\windows\memoria\DumpIT\FORENSE-06-20261111-152719.raw" SHA256
```
![[hash.png]]



2. **Los datos volátiles**
Hora 16:35 PM 11-11-2026
![[Datos-volatiles.png]]
he usado el script "respuesta-en-vivo.ps1" con la powershell para acceder a los datos volátiles

3. **El tráfico de red** 
Hora 16:31 PM 11-11-2026
![[red.png]]

Fuera de la maquina virtual  he ejecutado varios comandos ya que los proporcionados me daban error. 
```
1. & "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm "FORENSIC_10" nictracefile1 "C:\Users\Corai\Desktop\CASO-042\red.pcap"
    
2. & "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm "FORENSIC_10" nictrace1 on
    
3. & "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm "FORENSIC_10" nictrace1 off
```

3. **El triaje**
Hora 17:00 PM 11-11-2026
![[KAPE.png]]
Al tener un sistema operativo de windows 7, la herramienta a utilizar no funcionaba asi que use algunos comandos que son los siguientes: 
```
1.Extraer colmenas del Registro:Usuarios, USBs y Sistema.Ejecuta estos tres comandos uno a uno para volcar el registro en vivo: reg save HKLM\SAM "D:\salida\SAM.hiv" reg save HKLM\SYSTEM "D:\salida\SYSTEM.hiv" reg save HKLM\SOFTWARE "D:\salida\SOFTWARE.hiv" Verificación: La consola devolverá el mensaje "La operación finalizó correctamente" para cada colmena. 2.Extraer Visor de Eventos:Inicios de sesión y errores.Utiliza el comando nativo wevtutil para exportar los logs críticos: wevtutil epl System "D:\salida\System.evtx" wevtutil epl Security "D:\salida\Security.evtx" 3.Extraer Prefetch:Rastro de programas ejecutados.El directorio Prefetch no suele estar bloqueado por el sistema, por lo que puedes copiarlo directamente: Copy-Item -Path "C:\Windows\Prefetch*" -Destination "D:\salida\Prefetch" -Recurse
```

4. **El cifrado**
Hora 17:10 PM 11-11-2026
![[Cifrado.png]]
Para el cifrado usamos la herramienta de EDD que nos confirma que no hay archivos cifrados. 

7. **El apagado**
Hora 17:26 PM 11-11-2026
![[Apagado.png]]

Al finalizar se opta por cortar la luz o "tirar del cable" para apagar la máquina, como si fuera un caso real, ya que podría contener scripts de borrado al apagar u otras ejecuciones que beneficien a la perdida o modificación de los datos la máquina virtual.

## Parte 2 · Adquisición post-mortem
1. **Documentación de la maquina.** 
Hora 17:30 PM 11-11-2026
Dentro de la carpeta con ubicación C:\Users\Corai\VirtualBox VMs\FORENSIC_10, no dispone de instantáneas y estos son sus discos:
![[maquina-virtual.png]]

2. **Calcula el Hash.** 
Hora 17:40 PM 11-11-2026
![[hashes-archivos.png]]
Comprobamos los hashes del disco con el siguiente comando:
```
``` Get-ChildItem "C:\Users\Corai\VirtualBox VMs\FORENSIC_10" -Recurse -File | Get-FileHash -Algorithm SHA256 | Format-Table Path, Hash -AutoSize```
```

3. **Imagen del disco.** 
4. Hora 17:150 PM 11-11-2026
![[copia-disco.png]]
![[imagen-disco.png]]
- Abre FTK Imager.
- Entra en `File → Create Disk Image`.
- Selecciona el archivo de imagen `.vdi` como evidencia de origen.
- Selecciona el destino de la imagen.
- Elige formato `E01`.
- Marca Verify images after they are created.
- Inicia la adquisición.

3. **Vuelve a calcular el hash del `.vmdk`**
Hora 18:20 PM 11-11-2026
![[comprobacion-hashdisco.png]]
Para comprobar que no hay cambios volvemos a mirar el Hash del disco, y podemos comprobar que no ha sufrido cambios.
```
Get-FileHash "C:\Users\Corai\VirtualBox VMs\FORENSIC_10\FORENSIC_10_disk0.vdi" -Algorithm SHA256
```

### El pendrive encontrado
Marca: JUB SOLUTIONS 
Sistema: FAT32
Capacidad: 4GB

## Parte 3 · Busca tu marca
1. Abre [`bloqueo-usb.ps1`] y activa el bloqueo **antes** de conectar el pendrive.
2. Conéctalo y pulsa _Comprobar_: tiene que salir **PROTEGIDO**.
![[Captura de pantalla 2026-11-11 193814.png]]

3. En FTK Imager, _File → Create Disk Image → Physical Drive_ → el pendrive → **E01**, con _Verify images after they are created_.
![[imagen-01.png]]

4. Guarda `bloqueo-usb.log` junto a tus evidencias.
5. ![[Pasted image 20261111214054.png]]

He tenido que descargar un pequeño zip String para poder ejecutar el comando en windows en la pawershell.
```
1. .\strings64.exe -nobanner "D:\windows-7\windows\memoria\DumpIT\FORENSE-06-20261111-152719.raw" | Select-String "AFI-"
```

![[Captura de pantalla 2026-11-11 202801.png]]