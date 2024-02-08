+++
title = 'This Was How I Setted Up A Workstation'
date = 2024-02-08T16:42:3-05:00
draft = true
+++

Llegar a un nuevo equipo de computo puede ser abrumador. Actualmente me dicen que es asi para mi trabajo (yo no lo comparto, pero aja). Es desarrollo movil en react native. En una mac. Antes de chip intel. Al momento de escribir esto ahora tengo una mac con chip M1. Y como es una mac, se podran imaginar que los despliegues son para android, y iOS tambien. Yo admito que son un monton de cosas que hay que configurar. Pero no es el fin del mundo. Por ahora.

Entonces, como me acaba de llegar esta nueva mac, (y M1). Aprovechare para documentar de una vez el proceso que ha pasado por varias iteraciones: primero expo, luego el paso a paso con la guia de react native, luego tambien metiendo la de fastlane, luego sin android studio, luego con nix, luego con nix flakes, y ahora de nuevo con nix solo (viendo si le vuelvo a meter flakes). Este post va a ser denso. Tendre que escribir un poco sobre Nix, xcode, fastlane, certificados y perfiles, simuladores, android tools, emuladores, appcenter... etc, y el proyecto en sí de react native. Tal vez tengan razon en que es abrumador. Pero bueno, empecemos.

### Tareas iniciales

El nuevo equipo de computo es una mac modelo A2442, es pequeña, con un chip M1 y recien formateada. Lo primero que hice fue abrir safari para descargar firefox.

Luego de abrir firefox, busque el instalador de nix de Determinate Systems porque es mas ergonomico de usar, a pesar de que hace practicamente lo mismo que el oficial, pero con mejor desinstalación.
