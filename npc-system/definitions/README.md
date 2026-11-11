# Nicolas Londero

## Archivos fuente

- Preset Easy NPC: `npc-system/definitions/presets/humanoid/nicolas_londero.npc.snbt`
- Skin original: `npc-system/definitions/skins/nicolas_londero.png`
- Resource pack de Minecraft: `resourcepacks/latrecima-npc-skin/`

El preset referencia la textura como `latrecima:textures/entity/nicolas_londero.png`. El PNG está incluido tanto en la carpeta fuente como en el resource pack. Activá **Nicolas Londero** en las opciones de paquetes de recursos del cliente; sin ese paquete Easy NPC mostrará una textura predeterminada.

## Agregar al mundo

Copiá el preset fuente al datapack del mundo que vas a editar, dentro de:

`<mundo>/datapacks/latrecima_npcs/data/latrecima/easy_npc/preset/humanoid/nicolas_londero.npc.snbt`

Para el mundo de desarrollo, ese destino es `world-dev/datapacks/latrecima_npcs/data/latrecima/easy_npc/preset/humanoid/`. Si el datapack no está instalado en el mundo, copiá primero `world-dev/datapacks/latrecima_npcs/`. Después ejecutá `/reload` y, como operador o en creativo, importá el preset una vez:

```mcfunction
/easy_npc preset import_new data latrecima:easy_npc/preset/humanoid/nicolas_londero.npc.snbt ^ ^ ^2
```

`import_new` crea una instancia nueva. Importá el preset una sola vez para mantener un único NPC; volver a importarlo genera otro con su propio registro de bienvenida.

## Bienvenida por jugador

Easy NPC 7.18.0 admite condiciones `EXECUTION_LIMIT` por jugador. El diálogo `first_visit` tiene límite `LIFETIME:1`; Easy NPC registra la apertura en los datos persistentes del mundo. En la primera interacción se muestra el saludo inicial; las siguientes muestran `default`, el saludo de regreso. No hace falta crear un objetivo de scoreboard ni mantener un script externo.

El registro se asocia a la instancia de ese NPC y a cada jugador. Si se elimina y se importa otra instancia, el nuevo NPC volverá a dar la bienvenida a todos.

## Postura

El preset usa la pose personalizada `easy_npc:pose/humanoid/sleeping` y desactiva animaciones para conservarla. Para aplicarla a un NPC ya colocado:

```mcfunction
/easy_npc pose set easy_npc:pose/humanoid/sleeping <NPC>
```

## Edición

El preset es la definición inicial. Una vez importado, editar el NPC existente con la interfaz de Easy NPC; no volver a usar `import_new` para aplicar cambios, porque crearía un duplicado. Después de validar los cambios, exportá la versión aprobada y guardá el preset actualizado en `npc-system/definitions/presets/humanoid/`.
