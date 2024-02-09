+++
title = 'This Was How I Setted Up A Workstation'
date = 2024-02-08T16:42:3-05:00
draft = true
+++

Llegar a un nuevo equipo de computo puede ser abrumador. Actualmente me dicen que es asi para mi trabajo (yo no lo comparto, pero aja). Es desarrollo movil en react native. En una mac. Antes de chip intel. Al momento de escribir esto ahora tengo una mac con chip M1. Y como es una mac, se podran imaginar que los despliegues son para android, y iOS tambien. Yo admito que son un monton de cosas que hay que configurar. Pero no es el fin del mundo. Por ahora.

Entonces, como me acaba de llegar esta nueva mac, (y M1). Aprovechare para documentar de una vez el proceso que ha pasado por varias iteraciones: primero expo, luego el paso a paso con la guia de react native, luego tambien metiendo la de fastlane, luego sin android studio, luego con nix, luego con nix flakes, y ahora de nuevo con nix solo (viendo si le vuelvo a meter flakes). Este post va a ser denso. Tendre que escribir un poco sobre Nix, xcode, fastlane, certificados y perfiles, simuladores, android tools, emuladores, appcenter... etc, y el proyecto en sí de react native. Tal vez tengan razon en que es abrumador. Pero bueno, empecemos.

### Tareas iniciales

El nuevo equipo de computo es una mac modelo A2442, es pequeña, con un chip M1 y recien formateada. Lo primero que hice fue abrir safari para descargar firefox.

#### NIX

Luego de abrir firefox, busque [el instalador de nix de Determinate Systems](https://github.com/DeterminateSystems/nix-installer) porque es mas ergonomico de usar, a pesar de que hace practicamente lo mismo que el oficial, pero con mejor desinstalación. Como es un comando se ejecuta en la terminal.

```
curl --proto '=https' --tlsv1.2 -sSf -L https://install.determinate.systems/nix | sh -s -- install
```

Eso realizará una serie de pasos explicados uno a uno.

Mientras se instala, hablemos un poquito de nix https://nixos.org/. Nix es una herramienta con muchos propositos. Existe un sistema operativo NixOS; un repositorio de paquetes, nixpkgs; un administrador de dichos paquetes, un lenguaje de programación nix y tal vez algo mas que se me esté pasando. La gracia es que nix (en teoría) puede automatizar la obtención e instalación de paquetes, construcción de paquetes nuevos de mis desarrollos de software, y la creación de ambientes aislados, todo de manera reproducible, es decir, no importa en la maquina que estes, puedes acceder facilmente a todo lo que necesitas para trabajar en tu proyecto sin pasar de nuevo por la configuración del sistema y debería ser exactamente igual. Es un paradigma interesante. Te puede recordar a Docker, pero nix no trabaja con virtualización (aunque puedes crear una imagen de docker con nix, lo vi por ahi), y docker no trabaja con contenedores de mac con acceso a xcode sin tener que hacer una empanada (hack extremo). Nix, aunque teoreticamente puro, puede crear lo que llamo derivaciones impuras, que permiten acceder a xcode. Para mas información sobre nix, hay demasiada información dividida. Ese es el gran problema de nix, pero no me ha impedido usarlo.

Despues de instalar nix, lo uso para instalar la mayoria de paquetes que pueda con el y no tener un reguero (muchas opciones) de formas para instalar otras cosas, uniformidad. Lo primero que instalo es gpg y de manera persistente para usarlo desde cualquier lugar como primer ciudadano del sistema. Por persistente quiero decir fijo en el sistema de la mac para que, cuando algun programa quiera usarlo, no tenga problemas accediendo a el. Esto lo hare con la [nueva cli de nix](https://nixos.org/manual/nix/stable/command-ref/new-cli/nix)

```
nix profile install nixpkgs#gnupg
```

Eso instalara gpg en el perfil por defecto de nix, lo cual es suficiente para mi. Todo lo que se instale en ese perfil estara disponible system-wide. Luego hago lo que escribi en [como guardo mis contraseñas]({{< ref "/posts/this-was-how/i-saved-my-passwords" >}})

Una de las razones por las que uso nix basico en lugar de flakes es porque quiero ser capaz de cambiar unas variables de entorno para que se vean reflejadas en el ambiente de desarrollo sin tener que agregarlas a git. Si no estan en git, los flakes no la ven :( . Si alguien sabe un modo de saltarse eso, me avisa. Otra es porque sigue siendo experimental a dia de hoy, aunque ya [mucha gente lo usa igual](https://determinate.systems/posts/experimental-does-not-mean-unstable)
