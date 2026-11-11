# Entorno de servidor de pruebas

## Separación de datos

- El directorio raíz del proyecto contiene el packwiz fuente (`pack.toml`, `index.toml` y metadatos `mods/*.pw.toml`).
- `world/` es el mapa base original. No abrirlo ni modificarlo desde un servidor.
- `world-dev/` es la copia de trabajo del mapa, creada sin el archivo transitorio `session.lock`.
- `backups/world-original-2026-11-11/` es la copia de seguridad independiente del original. Sus 306 archivos se compararon con SHA-256; los hashes están en `backups/world-original-2026-11-11.sha256`.
- `server-test/world/` es una copia independiente de `world-dev/` para ejecutar pruebas. No copiar datos de vuelta al mundo original.
- El datapack `latrecima_npcs` contiene el preset de bienvenida y está copiado en los mundos de desarrollo y pruebas.
- La skin de Nicolas está en `resourcepacks/latrecima-npc-skin/` en el modpack; cada cliente debe activar ese paquete de recursos para verla.

`world-dev/`, su copia de seguridad y `server-test/world/` tienen 306 archivos y 421,524,321 bytes cada una antes de agregar el datapack de bienvenida; los archivos de las copias iniciales se verificaron con SHA-256. `session.lock` se excluyó de las copias porque es un archivo de bloqueo temporal; se conserva en el original.

## Versiones y estado

- Packwiz declara Minecraft 1.20.1 y Forge 47.4.29.
- El perfil de Forge que existe en el cliente local es 47.4.28. Alinear/instalar el cliente con 47.4.29 antes de probar conexión cliente-servidor.
- Forge 1.20.1-47.4.29 está instalado en este directorio y Packwiz instaló 36 JAR para servidor/ambos lados. Se verificaron los hashes declarados en los metadatos de los 36 mods.
- VMod 1.9.1 está instalado en `mods/`. Su hash SHA-512 coincide con el publicado por Modrinth.
- El entorno dispone de Java 25, pero no se encontró Java 17. Forge/Minecraft 1.20.1 debe probarse con Java 17.
- El servidor no se ha iniciado: falta Java 17 y la EULA debe ser revisada y aceptada manualmente por el responsable del servidor.
- VMod 1.9.1 (`VMod-Forge-1.20.1-1.9.1.jar`) quedó agregado al packwiz fuente. Su versión de Modrinth declara Forge y Minecraft 1.20.1; el changelog indica que debería funcionar con Valkyrien Skies 2.4.11, que es la versión declarada por el pack. Ya están en el pack Valkyrien Skies y Architectury API, dependencias indicadas por el proyecto.
- `.packwizignore` excluye del modpack mundos, copias de seguridad, documentación y carpetas de desarrollo/servidor. Esas carpetas no se distribuyen mediante Packwiz.

## Preparación del servidor de pruebas

1. Instalar Java 17 y seleccionarlo en `PATH` o `JAVA_HOME`; no iniciar este servidor con Java 25.
2. Forge 1.20.1-47.4.29 y los mods de servidor ya están instalados. Para actualizar los mods, desde la raíz ejecutar `packwiz serve` y, desde `server-test/`, el instalador oficial de Packwiz Installer Bootstrap con `-g -s server http://localhost:8080/pack.toml`.
3. La copia aislada ya está en `server-test/world/`. No volver a copiar encima de un mundo de pruebas ya iniciado.
4. Revisar/crear `server.properties`, las configuraciones de mods y los logs antes de iniciar. No habilitar acceso público al servidor de pruebas.
5. Leer y aceptar `eula.txt` manualmente antes de iniciar; este procedimiento no acepta la EULA automáticamente.
6. Iniciar con `run.bat nogui`. Para apagar, ejecutar `stop` en la consola y esperar a que el proceso termine antes de copiar o respaldar archivos.

## Política de promoción y respaldo

- Promover al servidor de pruebas primero los metadatos de packwiz y cambios revisados en `config/`, `defaultconfigs/`, `kubejs/`, `quests/`, `npc-system/` y las plantillas aprobadas de `ships/`.
- `npc-system/`, `ships/`, `development/` y otros directorios excluidos por `.packwizignore` requieren una etapa de empaquetado/copia explícita si el servidor necesita esos recursos; no los instala Packwiz automáticamente.
- Respaldar el mundo de pruebas, configuraciones, scripts y datos persistentes de mods antes de cada prueba de migración.
- No sincronizar a producción `playerdata/`, datos de FTB Teams/Quests/Lightman's Currency ni registros de propiedad/estado de barcos desde desarrollo. Son datos de ejecución, no configuración de pack; migrarlos solo mediante un procedimiento probado y un respaldo consistente.
- Nunca reemplazar `world/` con `world-dev/` automáticamente. La promoción de un mundo requiere aprobación, apagado limpio y respaldo previo del mundo destino.

## Seguridad de VMod y criterio de prueba

VMod agrega herramientas para esquemas, eliminación de barcos y otras operaciones de edición. Antes de permitir jugadores, revisar la configuración de roles del servidor y dejar las herramientas y comandos administrativos solo para operadores/autorizados. La versión 1.9.1 documenta que el rol predeterminado no puede usar las herramientas ni los comandos; confirmar ese comportamiento en la configuración efectiva del servidor.

La instalación de VMod en el manifiesto no cuenta como validación. El paso se considera validado solo después de arrancar un servidor dedicado aislado, comprobar ensamblaje y movimiento de un barco de prueba, guardar/cargarlo tras reiniciar y revisar que ningún jugador sin permisos puede crear o retirar embarcaciones fuera del catálogo.
