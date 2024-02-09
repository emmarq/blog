+++
title = 'This Was How I Saved My Passwords'
date = 2024-02-08T16:42:53-05:00
draft = true
+++

Antes tenía mis contraseñas guardadas en mi mente. Hacía lo que la mayoría hace, tener un conjunto de palabras que rotaba entre todas mis cuentas. Como unas 8. Así que tenia que recordar cual usaba en cual lugar. Con el tiempo, la cantidad de lugares creció hasta el punto en el que solo tenia como una unica clave pa todos los lugares. ¿Quien se iba a poner a recordar tantas claves?

¡El problema es que eso es un peligro! Hacerlo es peor que tener una casa sin puertas y tener la plata en la sala. Con la existencia de las tablas arco iris, o los numerosos reportes de robo de datos de grandes provedores de servicios, si usas la misma contraseña para todo es lo mismo que no tener ninguna contraseña porque cualquier persona que quiera ya sabe la contraseña.

Es como si crearas una sola llave, y mil candados que se abren con esa llave. No hay problema si la llave nunca te la roban. Pero si alguno persona le toma una foto a la llave, puede hacer una copia de la misma, y abrir todos los mil candados con la copia.

Asi que en un arranque de paranoia me decidi en guardar mis contraseñas en un gestor de contraseñas.

Evaluando algunos me di cuenta que las contraseñas que guardaba podian ser leidas a simple vista. Eso no me pareció muy seguro. Hasta que leyendo en Hacker News alguien compartio a [Pass](https://www.passwordstore.org/).

En pocas palabras, es un administrador de contraseña encriptado con git. Con el detalle de que tienes que poner el repositorio de git, y el conjunto de claves gpg (Digo conjunto porque se siente extraño hablar en singular cuando son 2 partes, en otras partes hablan en singular, la clave o la llave gpg, pero aqui prefiero referirlo como conjunto).

### GIT

Para el repositorio de git use bitbucket. Fue el proveedor de git que usaba en ese momento, y el unico que conocia con repositorios privados. Ahora esta github y gitlab y otros. Crear un repositorio nuevo es muy facil. Menos en bitbucket. :)

Pero como verás mas adelante, este proceso ya lo hice. lo que hare ahora es clonar el repositorio en la carpeta ~/.password-store que es el lugar por defecto donde pass busca las contraseñas encriptadas.

```
git clone https://gitlab.com/(user-name)/password-store.git
```

### GPG

Para lo de las claves gpg si tuve que investigar un poco. Y cada vez que me muevo a un pc o celular nuevo tengo que recordar de nuevo. (Este post lo hago para mi del futuro). Este tema merece un post aparte y dedicado, pero temo que el tiempo que le quiero invertir no es tanto. Por eso queda asi.

Gnu Privacy Guard (GPG) es una herramienta de cifrado asimetrico, o cifrado con llave publica; distintos nombres para lo mismo, encriptacion usando 1 par de claves. un clave se usa para encriptar, esa es la clave publica, y la otra se usa para desencriptar, la clave privada. A la clave privada tambien se le aplica encriptacion, pero simetrica con el apoyo de un passphrase que es como una contraseña.

Al momento de crear el conjunto de claves gpg, el programa le pedira algunos datos. Es convencional usar el correo como id.

Sinceramemte este proceso no lo estoy haciendo al momento de escribir este post, lo hice hace años ya. Asi que no lo estoy repitiendo. Para mas información de este proceso puede revisar la guia https://www.gnupg.org/gph/en/manual/c14.html. Lo que si estoy haciendo es configurar un computador nuevo. Entonces aprovecho para documentar este proceso que no pasa mucho, pero es muy importante.

#### Exportar e importar el conjunto de claves de gpg en una nueva maquina

Si no tienes el archivo con las claves de gpg, pero si tienes un computador con esas ya instaladas puedes exportarlas de nuevo. Eso lo dejare documentado en este articulo. Aunque el deber ser es tener ese archivo disponible en un lugar seguro, y hasta guardado en una caja de seguridad junto con el passphrase que usaste para crearlas para que en el caso de que haya una catastrofe donde TODO SE DAÑE, pueda existir la esperanza de un plan de recuperación. Ya iremos viendo que otras cosas guardar de la misma manera.

##### Exportar

Otro momento de confesión. No me acuerdo como fue que exporté el conjunto claves gpg por primera vez. Lo que sí se es que las he restaurado varias veces en computadores y celulares con un solo archivo. Eso me lleva a pensar que lo hice con un archivo de backup. Es lo que tiene mas sentido en este momento. Al haberlo hecho presuntamente asi, se agrego en el backup toda la información necesaria para restaurar y usar el conjunto de clave como si nada.

```
gpg -o private.gpg --export-options backup --export-secret-keys (mi correo)
```

y para restaurarlo en otro lugar, lo habré transferire usando los medios más seguros posibles (obviamente no me lo pase a mi mismo por whatsapp...), y en su sitio, habré de haber ejecutado algo asi

```
gpg --import-options restore --import private.gpg
```

Es lo que tiene mas sentido ahora tratando de recordar.

Pero esta vez lo hice diferente. Lo hice como vi ahora que volví a repasar esto del gpg. De esta otra manera exporte 2 archivos distintos en ascii, uno para la clave publica:

```
gpg --export -a (mi correo) > pub.key
```

lo que produce un archivo pub.key. Y otro para la clave privada

```
gpg --export-secret-key -a (mi correo) > prv.key
```

lo que produce un archivo prv.key.

Estos archivo los transferi por scp al nuevo equipo.

```
scp prv.key pub.key emmanuel@192.168.0.91:/Users/emmanuel
```

(Para lograr eso en la mac, que fue este caso, hay que ir a configuracion y permitir sesiones remotas)

Eso fue el trabajo de la exportación, ahora es el momento de la importación.

#### Importación

Lo primero es tener instalado gpg en la nueva maquina. Lo puedes instalar como mas te convenga. apt, brew, .exe, .msi, compila, no te voy a decir como instalar algo. Yo lo instalo con nix o con apt depende de donde este. Al momento de esta escritura lo hice con nix pero para el proposito de este articulo ese detalle no te debe de interesar.

Con gpg en el sistema, y los archivos del paso de la exportación, importarlos es muy sencillo:

```
gpg --import pub.key
gpg --import prv.key
```

Eso fue todo con gpg.

https://itslinuxfoss.com/export-gpg-private-key-and-public-key-file/
https://www.jwillikers.com/backup-and-restore-a-gpg-key

### PASS

Pass tambien lo instale con nix

```
nix profile install nixpkgs#pass
```

Con la carpeta restaurada en el ~/.password-store y el conjunto de clave importada en gpg, pass ya puede sin mas configuracion desencriptar las contraseñas y guardar nuevas. Usará la configuración del agente gpg para solicitar el passphrase de la clave gpg para desencriptar la contraseña.

Para guardar uso

```
pass generate (nombre o sitio de internet)
```

```
pass insert (nombre o sitio de internet)
```

Para desencriptar uso

```
pass (nombre que le puse)
```

dependiendo del agente, me pedira o no contraseña. Para mas información de los comandos, ahi esta la pagina de pass.

## Conclusión

Asi guardo las contraseñas, uso algun cliente de pass en el navegador o en el celular para no hacerlo por cli.

### Información para referencia futura

Los datos importantes para replicar este proceso son

1. La contraseña del repositorio de git donde guardo las contraseñas encriptadas.
2. El backup del conjunto de claves de gpg.
3. El passphrase de la clave privada gpg.

Con esos 3 datos es posible restaurar las contraseñas siguiendo las instrucciones de este articulo.
