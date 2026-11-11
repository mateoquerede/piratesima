# Plan de implementación — LaTrecima

## Objetivo

Construir y validar un servidor de aventuras piratas para Minecraft 1.20.1 y Forge 47.4.29. El plan organiza el mapa, las ubicaciones, los NPC, las misiones y un sistema de barcos comprables por party. Cada etapa debe probarse en desarrollo antes de promover cambios al servidor.

## Reglas de diseño

- Cada party de FTB Teams puede tener como máximo un barco activo.
- Los jugadores no deben poder crear embarcaciones libremente si eso permite eludir el catálogo y su economía. Revisar y configurar las herramientas de construcción, ensamblaje y permisos que puedan hacerlo posible.
- La propiedad del barco pertenece a la party, no al jugador que inició la compra.
- El UUID de Valkyrien Skies/Eureka se obtiene al colocar o ensamblar el barco; nunca se inventa ni se usa como identificador persistente del barco del catálogo.
- La propiedad persiste aunque la entidad física desaparezca o el servidor se reinicie.
- Al hundirse un barco, se conserva su propiedad y se pierde la carga según las reglas del servidor. La party debe poder recuperar el casco.
- No cobrar definitivamente una compra hasta verificar que el barco se puede colocar. Las operaciones deben poder recuperarse de fallos o reinicios sin duplicar monedas, barcos ni carga.
- Usar copias de mundo para desarrollo y pruebas. No experimentar sobre el mundo original.

## Fases

### 0. Preparar copias y entornos

- [x] Identificar el directorio del modpack, el servidor de pruebas, el mundo original y el mapa que se incorporará.
- [x] Instalar y preparar el servidor de pruebas aislado en `server-test/` con Forge 47.4.29 y los mods de servidor/ambos lados de Packwiz.
- [x] Hacer una copia de seguridad verificable del mundo original en `backups/world-original-2026-11-11/`; registrar hashes SHA-256 en `backups/world-original-2026-11-11.sha256`.
- [x] Crear `world-dev/` como copia de trabajo del mapa, sin alterar `world/`.
- [x] Definir qué configuración, scripts, datos de jugadores y datos de mods se promocionarán entre entornos.
- [x] Documentar el proceso previsto de inicio y apagado seguro del servidor de pruebas en [server-test/README.md](../server-test/README.md).

**Estado:** el mapa original permanece intacto; existen copias verificadas para desarrollo y pruebas, y el servidor está instalado de forma aislada. La fase queda parcialmente pendiente hasta instalar Java 17 y realizar el primer arranque.

### 1. Validar mods y compatibilidad

- [x] Confirmar las versiones declaradas en el packwiz: Minecraft 1.20.1 y Forge 47.4.29. Detectar la diferencia con el perfil local, Forge 47.4.28; falta alinear y validar el cliente.
- [x] Agregar VMod 1.9.1 para Forge 1.20.1 al packwiz fuente. El proyecto declara Valkyrien Skies y Architectury API como dependencias; ambos están en el pack. Su changelog dice que debería funcionar con Valkyrien Skies 2.4.11.
- [x] Instalar VMod y el conjunto de mods de servidor/ambos lados en el servidor de pruebas; verificar los hashes de los 36 JAR instalados.
- [ ] Validar el arranque dedicado con Valkyrien Skies, Eureka, VMod, FTB Teams, FTB Quests, Lightman's Currency y Easy NPC.
- [ ] Verificar que la creación, guardado, carga y movimiento de un barco de prueba funcionan tras reiniciar.
- [x] Identificar que VMod ofrece herramientas de esquemas y eliminación de barcos, y que incluye roles de servidor para restringir herramientas. La restricción efectiva y las vías de ensamblaje de Eureka quedan pendientes de comprobar en el servidor.
- [ ] Guardar logs de arranque y registrar incompatibilidades antes de añadir más contenido.

**Estado:** compatibilidad preliminar verificada por metadatos del proyecto y hashes de instalación, no por ejecución. El equipo disponible es Java 25; falta Java 17 y el servidor no se inició. La fase sigue pendiente de validación real.

### 2. Incorporar el mapa

- [ ] Importar el mapa en una copia de desarrollo, conservando intacto el original.
- [ ] Confirmar versión, dimensiones, datapacks, estructuras y requisitos del mapa.
- [ ] Verificar spawn, puertos, islas, costas y coordenadas importantes.
- [ ] Revisar límites de mundo, generación de chunks y posibles conflictos con mods de biomas o estructuras.
- [ ] Registrar coordenadas y dimensión de cada punto de interés.

**Salida / criterio de aceptación:** el mundo carga sin errores y las áreas necesarias para la aventura son accesibles en el entorno de pruebas.

### 3. Crear el catálogo de ubicaciones

- [ ] Definir un identificador estable, nombre, tipo, dimensión y coordenadas para puertos, islas, naufragios, fuertes y encuentros.
- [ ] Añadir condiciones de acceso, NPC asociados, misiones relacionadas y puntos de aparición seguros cuando corresponda.
- [ ] Guardar los datos en un formato editable y versionable bajo `npc-system/` o el directorio de datos acordado.
- [ ] Validar que cada ubicación exista en el mapa y que sus coordenadas sean correctas.

**Salida / criterio de aceptación:** el catálogo no contiene identificadores duplicados y todas las entradas probadas corresponden a lugares reales del mapa.

### 4. Crear y gestionar NPC de referencia

- [x] Crear un preset de bienvenida con Easy NPC 7.18.0 para Nicolas Londero, guardado en el datapack de `world-dev/`, con la skin proporcionada y diálogos de primera visita/retorno.
- [ ] Exportar y versionar su preset y los datos necesarios para reproducirlo.
- [ ] Importar el preset en el mapa y probar primera bienvenida y visitas posteriores.
- [ ] Probar diálogo e interacciones en cliente y servidor dedicado.
- [ ] Documentar cómo actualizar el preset sin duplicar NPCs ni perder cambios.
- [ ] Definir el método de identificación estable de cada NPC y su ubicación.

**Estado:** el preset de bienvenida y la condición por jugador están preparados, pero falta importarlo y validarlo dentro del juego. Aún no se ha creado el NPC administrador del astillero.

**Salida / criterio de aceptación:** los NPCs pueden colocarse de forma repetible, sobreviven a reinicios y ofrecen interacciones solo a jugadores autorizados.

### 5. Construir LaTrecimaNPCManager

- [ ] Definir el formato de las definiciones de NPC y sus ubicaciones.
- [ ] Implementar carga y validación de definiciones, presets y placements.
- [ ] Hacer que la colocación sea idempotente: ejecutar el proceso más de una vez no debe generar NPC duplicados.
- [ ] Informar claramente definiciones inválidas, presets faltantes o ubicaciones incorrectas.
- [ ] Probar carga inicial, actualización y reinicio en el servidor dedicado.

**Salida / criterio de aceptación:** NPCs versionados en archivos pueden cargarse y actualizarse de manera repetible sin duplicados.

### 6. Crear las primeras misiones

- [ ] Organizar FTB Quests por capítulos o arcos narrativos.
- [ ] Crear una misión inicial que pruebe aceptación, progreso, objetivos, recompensas y desbloqueos.
- [ ] Integrar objetivos de exploración, interacción con NPC y combate solo cuando puedan verificarse de forma fiable.
- [ ] Decidir explícitamente si progreso y recompensas son personales o compartidos por party.
- [ ] Probar que abandonar, reiniciar o cambiar de party no duplica recompensas ni deja misiones en un estado inválido.

**Salida / criterio de aceptación:** la misión de prueba se completa correctamente en cliente y servidor dedicado y respeta las reglas de party definidas.

### 7. Construir LaTrecimaCore

- [ ] Delimitar responsabilidades: encuentros, activadores y eventos de proximidad.
- [ ] Definir configuraciones de eventos con identificadores estables, condiciones y acciones.
- [ ] Evitar que eventos de proximidad se disparen repetidamente de forma no deseada.
- [ ] Añadir controles para evitar duplicar recompensas o encuentros tras recargar o reiniciar.
- [ ] Probar eventos con varios jugadores y miembros de una party.

**Salida / criterio de aceptación:** los eventos se activan en las condiciones esperadas, con comportamiento repetible y sin duplicaciones.

### 8. Crear plantillas de barcos

- [ ] Diseñar los modelos iniciales del catálogo y acordar sus dimensiones, bloques, requisitos y costes.
- [ ] Construir y guardar las plantillas usando el flujo compatible confirmado en la fase 1.
- [ ] Definir puntos de salida del astillero con espacio suficiente, agua y zona de seguridad.
- [ ] Probar ensamblaje, UUID generado, movimiento, guardado y carga de cada plantilla.
- [ ] Registrar versión de plantilla y restricciones de colocación.

**Salida / criterio de aceptación:** cada plantilla se puede colocar y recuperar tras reiniciar sin perder bloques ni producir un barco duplicado.

### 9. Diseñar e implementar el sistema Shipyard

#### Datos persistentes

- [ ] Guardar datos del lado del servidor, indexados por UUID de party de FTB Teams.
- [ ] Mantener separados el ID persistente del barco, el ID de plantilla y el UUID físico de Valkyrien Skies/Eureka.
- [ ] Registrar, como mínimo, propiedad, plantilla, estado, UUID físico si existe, daño, carga, posición/dimensión y datos de la operación pendiente.
- [ ] Definir estados válidos, por ejemplo: `owned`, `active`, `stored`, `sunk`, `placing` y `repairing`, y las transiciones permitidas.
- [ ] Persistir los cambios de forma segura y versionar el formato de datos para futuras migraciones.

Ejemplo conceptual (no es un formato definitivo):

```json
{
  "party_id": "ftb_team_uuid",
  "active_ship_id": "ship_0001",
  "ships": [
    {
      "id": "ship_0001",
      "template": "sloop_01",
      "vs_ship_uuid": "uuid_obtenido_del_mod",
      "status": "active",
      "damage": 0,
      "cargo": [],
      "last_position": {
        "dimension": "minecraft:overworld",
        "x": 120,
        "y": 64,
        "z": -240
      }
    }
  ]
}
```

#### Interacción del astillero

- [ ] El NPC debe ofrecer: seleccionar un barco de la party, reparar el barco activo y comprar un barco del catálogo.
- [ ] Mostrar modelo, precio, estado, daño y requisitos antes de confirmar una acción.
- [ ] Autorizar acciones según las reglas de party y la configuración del servidor.
- [ ] Si la integración directa con Easy NPC no es adecuada, mantener el NPC como punto de entrada y delegar la interfaz/transacción a un mecanismo del lado del servidor.

#### Compra y colocación

- [ ] Reservar o bloquear la operación por party para impedir compras simultáneas de dos miembros.
- [ ] Crear un registro persistente de transacción con ID único e idempotente.
- [ ] Validar propiedad/cupo, saldo, plantilla, punto de salida y posibilidad de ensamblaje antes del cobro definitivo.
- [ ] Reservar fondos o usar escrow si Lightman's Currency lo permite; confirmar la operación solo cuando el barco se haya colocado y su UUID físico se haya registrado.
- [ ] Si la colocación falla, cancelar la reserva y dejar la propiedad y el saldo en un estado coherente.
- [ ] Si el servidor se cae a mitad de operación, reconciliar la transacción al iniciar: completar una colocación verificable o revertir una operación incompleta sin duplicar barco ni cobrar dos veces.
- [ ] Antes de retirar o sustituir un barco, guardar su carga y estado persistente.
- [ ] Al seleccionar otro barco, guardar o retirar el activo de forma segura y comprobarlo antes de colocar el siguiente.

#### Hundimiento, recuperación y reparación

- [ ] Detectar el hundimiento según una regla verificable y guardar el evento antes de cambiar el estado.
- [ ] Conservar la propiedad, invalidar o actualizar el UUID físico y aplicar la pérdida de carga definida por el servidor.
- [ ] Permitir recuperar el casco una sola vez, sin generar una copia si el evento se repite o el servidor se reinicia.
- [ ] Determinar cómo calcular daño y coste de reparación con la información que realmente exponga el mod de barcos.
- [ ] Cobrar la reparación solo después de comprobar que el barco puede restaurarse; asegurar reintento o reversión ante fallos.
- [ ] No permitir que un barco dañado o hundido vuelva a navegar hasta completar la reparación o recuperación requerida.

**Salida / criterio de aceptación:** cada party tiene como máximo un barco activo; compras, selecciones, hundimientos y reparaciones sobreviven a reinicios y no permiten duplicar barcos, carga, objetos ni monedas.

### 10. Pruebas de integración y puesta en servidor

- [ ] Probar dos miembros de la misma party iniciando una compra al mismo tiempo.
- [ ] Probar desconexión del jugador y apagado forzado en cada etapa de compra y colocación.
- [ ] Probar punto de salida ocupado, plantilla inexistente, saldo insuficiente y fallo de ensamblaje.
- [ ] Probar selección con barco activo, barco guardado y barco hundido.
- [ ] Probar reinicios antes y después de guardar carga, cambiar UUID físico y confirmar pagos.
- [ ] Probar migración o recuperación de datos persistentes con una copia de seguridad.
- [ ] Revisar logs, errores de servidor, rendimiento y comportamiento con varios jugadores.
- [ ] Promover a staging solo cambios validados; hacer copia de seguridad antes de cada actualización del mundo real.

**Salida / criterio de aceptación:** todos los casos críticos pasan en servidor dedicado y existe un procedimiento documentado para restaurar el estado anterior.

## Decisiones pendientes

- [ ] Confirmar qué mod y versión se denomina “VMod”, su compatibilidad con el conjunto instalado y el mecanismo de plantillas que ofrece.
- [ ] Definir si una party puede poseer varios barcos guardados aunque solo uno esté activo.
- [ ] Definir quién puede comprar, seleccionar, reparar y recuperar barcos dentro de una party.
- [ ] Definir precios, monedas, límites de carga, reglas de pérdida de carga y criterios de hundimiento.
- [ ] Definir el origen y formato del mapa y las ubicaciones iniciales del astillero.
- [ ] Confirmar si el catálogo y el sistema de transacciones se implementarán en un mod Forge propio o mediante una integración alternativa.

## Registro de cambios

Actualizar este plan marcando las tareas terminadas y anotando decisiones de diseño que afecten datos, economía o compatibilidad. No marcar una fase como completada hasta cumplir su criterio de aceptación en el servidor de pruebas.
