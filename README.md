[mega_prompt_antigravity_v7.md](https://github.com/user-attachments/files/33291943/mega_prompt_antigravity_v7.md)
# MEGA-PROMPT v7: "APP TO THE TOP" (GODOT 4.7.2 / ANTIGRAVITY / WINDOWS)

> v7 = v6 íntegra + parches de la auditoría física y sistémica. Todo cambio está marcado **[ESPEC-CORREGIDA]** y listado en el Anexo D.

Etiquetas usadas en este documento:
- **[CAMBIO]**: corrige o reemplaza algo de versiones previas (detalle en el Anexo B).
- **[RESTAURADO]**: requisito de diseño recuperado que versiones intermedias omitían.
- **[SUPUESTO]**: valor o regla de diseño propuesta para eliminar ambigüedades. El agente puede implementarlo directamente y listarlo en su informe de fase.
- **[BLOQUEANTE]**: decisión residual crítica que el agente debe confirmar con el usuario en la Fase 0 antes de codificar la lógica dependiente.
- **[A CONFIRMAR]**: decisión no bloqueante; el agente asume el valor indicado y lo reporta en su informe.
- **[APROBADO]**: función aprobada de diseño; ahora es requisito mandatorio (trazabilidad en el Anexo A).
- **[ESPEC]**: corrección técnica de ingeniería de software, arquitectura o física matemática extraída de la auditoría rigurosa anti-vibe-coding. Prevalece sobre cualquier texto previo (trazabilidad en el Anexo C).
- **[ESPEC-CORREGIDA]**: corrección v7 de la auditoría física y sistémica. Prevalece sobre cualquier [ESPEC] previa en conflicto (trazabilidad en el Anexo D).

---

# 0. ROL, OBJETIVO Y GLOSARIO

## 0.1 Rol
Eres un Ingeniero Principal de Software y Desarrollador Senior de Videojuegos especializado en arquitectura de sistemas en Godot Engine, gráficos en tiempo real, física de vehículos por raycasts, simulación sistémica, IA de tráfico y multitudes, e interfaces de usuario. Trabajas en el entorno **Antigravity**, con **acceso de lectura y escritura a los archivos y ejecución en la terminal del proyecto**.
Si en algún momento no puedes ejecutar comandos o pruebas, DETENTE y comunícalo explícitamente: nunca declares una prueba como ejecutada o un requisito como cumplido sin haber corrido el comando y verificado su salida literal.

Plataforma objetivo: **solo Windows x86_64**. Motor: **Godot 4.7.2 (Standard Edition)**. Lenguaje: **GDScript con tipado estricto habilitado como error**. Framework de pruebas: **GUT 9.7.x**.
**[ESPEC-CORREGIDA]** Las versiones de motor y GUT se verifican en la Fase 0 (`Godot_*_console.exe --version` y `addons/gut/plugin.cfg`) y se fijan en `docs/adr/ADR-0001-versions.md`. Si la versión instalada difiere, se usa la instalada y se reporta.

## 0.2 Concepto
Videojuego híbrido que fusiona **simulación de conducción en primera persona, gestión y tycoon de flota, narrativa urbana interactiva y progresión rogue-lite**. El jugador encarna a **Mateo**, un joven humilde y padre soltero en la metrópolis ficticia de **Ditroiffe** a finales de los años 2000. Mateo comienza únicamente con un skate tradicional y trabaja en la aplicación de transporte **To The Top** para financiar la educación escolar privada de su pequeña hija **Sofía**.

Alcance de la capa de gestión [APROBADO]:
- **Garaje comercial de 10 vehículos operativos** (más 1 vehículo narrativo exclusivo de la misión final del Acto 4).
- Mantenimiento mecánico por zonas, pólizas de seguro con deducible, árbol de mejoras permanentes con Honor y economía dual (Dinero vs. Honor).
- **El Imperio (Tycoon completo):** garajes por ciudad, conductores contratados con rasgos y fatiga, simulación abstracta determinista de turnos (`AbstractTripSim`), finanzas empresariales, reputación de marca y app rival corporativa ("VíaDirecta").
- Apartamento evolutivo en 5 niveles (con zona de asueto activo anti-estancamiento), cuota escolar periódica coercitiva de Sofía, ingesta de alcohol con degradación psicomotriz y modo sin alcohol ("Aturdimiento"), pasajeros con arcos narrativos y diálogos en tiempo real, recados a pie en primera persona, mutadores y perks por run, y reto diario determinista con semilla fija.

## 0.3 Glosario canónico [CAMBIO]
Queda estrictamente prohibido mezclar términos de divisas o calificaciones. Código, recursos `.tres`, esquemas de guardado, logs y UI utilizan exclusivamente estos identificadores:

| Término | Significado en Gameplay | Identificador en Código |
|---|---|---|
| Servicio | Viaje de pasajeros, encargo de paquetería o comida a domicilio aceptado en la app | `service` |
| Calificación | Puntuación de 1 a 5 estrellas otorgada por el cliente al finalizar un servicio | `rating` |
| Honor | Moneda permanente de prestigio y meta-progresión. Se gana por servicios, se pierde por penalizaciones, y se invierte en la PC | `honor` |
| Dinero | Billetera operativa corriente (combustible, reparaciones, comida, multas, sueldos) | `money` |
| Jornada | Turno de trabajo compuesto por un cupo de 5 a 8 servicios | `shift` |
| Run | Ciclo completo de juego entre dos activaciones de Honor Top (prestigio) | `run` |
| Honor Top | Mecánica de prestigio: reinicio cíclico a cero con retención matemática y multiplicador | `honor_top` |
| Estrés | Nivel de tensión de 0.0 a 100.0 del pasajero y del conductor | `stress_passenger`, `stress_driver` |
| Inventario | 3 ranuras rápidas de consumibles + 1 ranura pasiva de recado/misión en el avatar | `inventory` |
| Nevera | Almacén doméstico estático acotado a 20 slots de consumibles en el apartamento | `fridge_inventory` |
| Cuota escolar | Pago periódico obligatorio de la matrícula y colegiatura de Sofía | `tuition` |
| Alcoholizado | Estado fisiológico de deterioro psicomotriz (renombrado a "Aturdimiento" en modo sin alcohol) | `impairment` |
| Mutador | Modificador global de la run asignado por RNG determinista al inicio del ciclo | `mutator` |
| Perk | Ventaja de gameplay seleccionable entre jornadas, válida únicamente durante la run | `perk` |
| Imperio | Capa de simulación tycoon: garajes, choferes contratados y finanzas de flota | `empire` |
| Reto diario | Jornada única de 8 servicios generada bajo la semilla matemática inmutable del día | `daily_challenge` |
| Integridad de blindaje | Salud estructural del vehículo presidencial durante el convoy (100.0 a 0.0) [ESPEC] | `armor_integrity` |
| Estrés del VIP | Medidor de tensión del dignatario durante la misión final (0.0 a 100.0) [ESPEC] | `vip_stress` |
| Náusea del VIP | Medidor de cinetosis del dignatario durante la misión final (0.0 a 100.0) [ESPEC] | `vip_nausea` |

### 0.3.1 Escalas físicas y temporales canónicas [ESPEC-CORREGIDA]
- **Escala temporal pivote (1×):** `1 s real = 1 min de juego` (`GameClock.GAME_MINUTES_PER_REAL_SECOND = 1.0`), así que 1 hora de juego son 60 s reales y 1 día de juego son 24 min reales. Toda tasa metabólica, de fatiga, de recarga y de vencimiento (cuota escolar, pólizas, suspensiones) se expresa en unidades de tiempo de juego y se convierte con esta constante. El paso físico (60 Hz) **no** se escala nunca: los aceleradores de tiempo (asueto 5×, sueño) multiplican solo el reloj lógico.
- **Unidades:** 1 unidad Godot = 1 m; masa en kg; fuerza en N; impulso en N·s; velocidades internas en m/s (km/h solo en UI y en umbrales documentados, convertidos con `/ 3.6`).

---

# 1. REGLAS DE INGENIERÍA RIGUROSAS (ANTI "VIBE CODING")

1. **Especificación formal antes del código.** Cada fase entrega obligatoriamente en primer lugar:
   - Contratos de datos (clases `Resource` fuertemente tipadas con `class_name`).
   - Jerarquía exacta de nodos de las escenas (`.tscn`).
   - Decisiones de arquitectura de software (ADRs concisos documentando alternativas y justificación).
   - Plan de pruebas unitarias y de integración para GUT.
   Solo tras presentar esta arquitectura se procede a generar el código de la lógica.
2. **Una sola fase por respuesta.** El agente nunca avanza a la siguiente fase sin la confirmación y aprobación explícita del usuario.
3. **Cero relleno y cero código ficticio.** Quedan prohibidos los comentarios tipo `# TODO`, las funciones con `pass` (salvo métodos virtuales base obligados), las implementaciones vacías, los cubos grises sin texturizar como arte final y los parámetros hardcodeados. Cualquier elemento temporal no abordable en la fase actual debe declararse explícitamente en el informe bajo las etiquetas **STUB** o **PLACEHOLDER**, indicando la fase exacta donde se completará.
4. **Evidencia o no existe.** Ningún requisito o prueba se da por cumplido sin adjuntar la salida literal completa del comando de ejecución de GUT en consola o, en el caso de métricas físicas y de render, la captura de valores numéricos de monitores del motor.
5. **Arquitectura estricta en capas desacopladas.**
   - *Capa de Datos:* Clases `Resource` con `class_name` (`VehicleConfigResource`, `ServiceDefResource`, `PassengerProfileResource`, `WeatherProfileResource`, `HiredDriverResource`, etc.) y tablas de balance. El estado mutable de la partida vive estrictamente separado de las definiciones de configuración estáticas.
   - *Capa de Simulación:* Lógica pura desacoplada de la escena visual (`VehiclePhysicsBody3D`, `SurvivalManager`, `EconomySystem`, `RatingSystem`, `TuitionSystem`, `AbstractTripSim`), ejecutable y verificable en modo `--headless`. La física corre estrictamente en `_physics_process` y `_integrate_forces`.
   - *Capa de Presentación:* Controladores de cámara (`CockpitCamera3D`), mallas 3D, shaders espaciales y de post-proceso, efectos de sonido y nodos de UI (`Control`).
   - *Comunicación:* Exclusivamente mediante señales tipadas y un bus de eventos central (`EventBus`). Autoloads mínimos autorizados: `EventBus`, `GameState`, `Settings`, `PlatformServices`, `AudioManager`, `Loc`. Prohibido el uso de rutas frágiles tipo `get_node("../../..")`; se exige `@export` tipado y nombres únicos de nodos en escena (`%NombreNodo`).
6. **Cero números mágicos.** Todo parámetro cinemático, masa, fricción, tarifa, tasa metabólica, penalización de estrés, probabilidad estocástica y multiplicador de Honor debe residir en archivos `.tres` o diccionarios tipados con validadores en carga (`@tool` o aserciones). Los tests fallan inmediatamente si un archivo de datos omite un campo o define un valor fuera de rango admisible.
7. **Determinismo estricto.** Todo proceso probabilístico utiliza instancias dedicadas de `RandomNumberGenerator` con semilla explícita, manteniendo **un stream independiente por subsistema**:
   - `rng_map`: Generación procedural del grafo vial y arquitectura.
   - `rng_traffic`: Densidad y comportamiento de vehículos y peatones NPC.
   - `rng_weather`: Transiciones del clima dinámico.
   - `rng_passengers`: Perfiles de clientes y solicitudes de viaje.
   - `rng_loot`: Objeto olvidado y tiradas de ruleta cosmética.
   - `rng_fleet`: Simulación abstracta del Imperio al dormir.
   - `rng_events`: Eventos dinámicos e imprevistos de ruta.
   Queda terminantemente prohibido invocar `randi()` o `randf()` globales en la lógica de gameplay.
8. **Pruebas unitarias headless obligatorias (GUT 9.7.x).** Comando de validación canónico en PowerShell:
   `.\Godot_v4.7.2-stable_win64_console.exe --headless -d -s res://addons/gut/gut_cmdln.gd -gdir=res://test -ginclude_subdirs -gexit`
   Cada entrega de fase debe pegar la salida literal de la consola (total de pruebas, pasadas y fallidas).
9. **Instrumentación de depuración desde la Fase 1 (Debug Builds):**
   - `F3`: Telemetría del motor (FPS, frame time, draw calls, polígonos, memoria RAM/VRAM, velocidad en km/h y m/s, RPM, marcha actual, deslizamiento longitudinal/lateral por rueda, coordenadas globales, hambre, sed, estamina, fatiga, estrés, estado de la FSM activa y semilla activa).
   - `F4`: Visor de IA (estados de comportamiento, caminos A*, reservas de intersección).
   - `F5`: Gizmos de depuración física (raycasts de suspensión, vectores de fricción de neumáticos, centro de masa).
   - `F6`: Panel de ajuste dinámico de variables cinemáticas en caliente.
   - Consola de comandos: Manipulación de clima, hora del día, inyección de dinero/Honor, teletransporte y forzado de eventos.
10. **Principio de cambio mínimo.** Toda corrección toca exclusivamente los scripts y recursos defectuosos, preservando contratos de interfaces y firmas de señales preexistentes.
11. **Cero emojis y política de iconografía pixel-perfect.** Prohibidos emojis Unicode o glifos no ASCII en código, logs, UI, comentarios y archivos de localización. La iconografía del juego es **pixel art procedural o dibujado**: matrices de datos numéricos con índices de paleta horneadas a texturas PNG con filtro *nearest* y escaladas por múltiplos enteros.
12. **Política de assets honesta.**
    - Prioridad 1: Generación procedural por código con semilla horneada a recursos.
    - Prioridad 2: Conversión determinista de matrices de datos a recursos PNG/tres mediante scripts de editor (`@tool`).
    - Prioridad 3: Emisión de una **Hoja de pedido de assets** formal (especificación de polígonos, texel density, formato y referencias) marcando el asset como **PLACEHOLDER**.
    - Prioridad 4: Assets externos con licencia permisiva demostrable (CC0 o MIT), registrados en `ASSETS.md`. Prohibido enlazar dependencias remotas.
13. **Calidad visual verificable por código.** La exigencia de calidad estética se valida mediante scripts de test automáticos (densidad poligonal, balance cromático, cantidad de props por tramo vial).
14. **Protocolo ante ambigüedades en Fase 0.** El agente puede plantear un máximo de **3 preguntas de diseño** exclusivamente sobre los puntos marcados `[BLOQUEANTE]`. Para todo aspecto técnico o de balance no bloqueante, el agente debe asumir la alternativa más rigurosa, etiquetarla `[SUPUESTO]` y avanzar.
15. **No inventar APIs del motor.** Utilizar estrictamente la API oficial de Godot 4.7.2 (`StandardMaterial3D`, `ShaderMaterial`, sintaxis moderna de sombreadores espaciales y de canvas, `PhysicsServer3D`). Nunca usar clases obsoletas de Godot 3 (`SpatialMaterial`, `Spatial`).
16. **Optimización con justificación empírica.** Toda optimización se sustenta con comparativas antes/después del Profiler del motor. El uso de C# o GDExtension se restringe a cuellos de botella masivos comprobados mediante ADR.
17. **Integridad de escenas.** Los archivos `.tscn` y `.tres` descritos como texto deben ser sintácticamente válidos para Godot 4.7.2. Si un nodo requiere configuración manual en el inspector, debe detallarse en una lista numerada de **Pasos manuales**.
18. **Persistencia y migraciones incrementales.** Cada cambio en la estructura de datos persistentes debe acompañarse de su método de migración de versión y prueba de carga hacia atrás.
19. **Localización nativa desde la Fase 1.** Todo texto de interfaz o diálogo pasa obligatoriamente por el sistema de traducción (`tr()`), manteniendo soporte completo para español (`es`) e inglés (`en`).
20. **Universo 100% original.** Todas las marcas comerciales, vehículos, personajes, empresas, aplicaciones, cadenas de comida rápida y topónimos son ficticios.

---

# 2. PLATAFORMA Y STACK TÉCNICO

- **Motor:** Godot Engine **4.7.2** (x86_64, edición estándar). Versión declarada en `project.godot` y `README.md`.
- **Pipeline de Render:** **Forward+** obligatorio (requerido para niebla volumétrica física, SDFGI, SSR, SSAO/SSIL y pases de pantalla completa con `CompositorEffect`).
- **Motor de Física:** **Jolt Physics integrado en Godot** (`physics/3d/physics_engine = "Jolt Physics"` en `project.godot`) [ESPEC-CORREGIDA]: no se exige versión externa de Jolt. Bucle físico a 60 Hz con subpasos o 120 Hz fijo con interpolación de transformaciones visuales.
- **Vehículos:** Modelo de simulación física por raycasts en cada rueda implementado sobre `RigidBody3D`. Prohibido el uso ingenuo de `VehicleBody3D` sin justificación por ADR.
- **Lenguaje:** GDScript con advertencias tratadas como errores (`untyped_declaration = 2`, `unsafe_property_access = 2`, `unsafe_method_access = 2`). Formato validado con `gdtoolkit`. Límite de ~400 líneas por script.
- **Simulación Masiva de Tráfico:** Arquitectura orientada a datos (DoD) mediante arreglos empaquetados (`PackedFloat32Array`, `PackedInt32Array`), cálculo multi-hilo con `WorkerThreadPool` y actualización escalonada por distancia al jugador. **[ESPEC-CORREGIDA]** Los workers operan solo sobre copias de arreglos empaquetados y jamás leen ni escriben nodos del SceneTree. El hilo principal empaqueta, despacha, recoge y vuelca en bloque al MultiMesh (detalle en 8.3).
- **Precisión de coma flotante [SUPUESTO]:** float32 por defecto. Límite de mundo `WORLD_HALF_EXTENT = 8000.0 m` desde el origen por ciudad. Si algún modo (gravedad cero extendida) permite superarlo, se implementa *origin shifting* (re-centrado del mundo al cruzar 4000 m).
- **Plataforma Target:** Windows 10/11 x86_64, exportación en ejecutable binario nativo `.exe`.
- **Capa de Abstracción de Plataforma (`PlatformServices`):**
  - `LocalPlatform`: Guardado en directorio `user://`, logros internos y tablas de puntuación locales.
  - `SteamPlatform`: Integración con GodotSteam (GDExtension) para Steam Cloud, Steam Leaderboards (Reto Diario y Competencia Semanal), Steam Input y logros.
  - *Sistema de Guardado:* 3 ranuras independientes, versionado de esquema, escritura atómica en archivo temporal con rotación de copia de seguridad (`.bak`) y firma HMAC para detección de corrupción.
  - *Entrada:* Mapeo unificado para Teclado/Ratón y Gamepad XInput con zonas muertas radiales configurables, curvas de respuesta y adaptación dinámica de glifos en pantalla.
- **Objetivos de Rendimiento (Hardware de Referencia GTX 1060 6GB / CPU 6 núcleos / 16GB RAM a 1080p):**
  - 60 FPS estables en preset de calidad Medio.
  - **Draw Calls inferiores a 1.200** en preset Medio dentro del centro urbano densamente poblado.
  - Polígonos visibles en pantalla inferiores a 1.500.000 triángulos.
  - Tiempo de procesamiento de IA de multitudes y tráfico $\le 3.0\text{ ms}$ por frame.
  - Generación procedural de un distrito $\le 15.0\text{ s}$ en segundo plano con pantalla de carga interactiva.
  - Presupuesto de memoria RAM total inferior a 4.0 GB.

## 2.1 Clase base de pruebas físicas headless (`VehiclePhysicsTestCase`) [ESPEC-CORREGIDA]
Permite ejecutar simulaciones físicas reproducibles y deterministas en entornos de pruebas automatizadas por línea de comandos sin display. `PhysicsServer3D.space_step()` (usado en v6) **no** despacha `_integrate_forces()` ni las notificaciones del SceneTree, lo que producía falsos positivos. El avance se hace cediendo frames físicos reales:

```gdscript
# res://test/fixtures/vehicle_physics_test_case.gd
class_name VehiclePhysicsTestCase
extends GutTest

var vehicle: RigidBody3D

func before_each() -> void:
	var res: PackedScene = load("res://scenes/vehicles/base_vehicle.tscn") as PackedScene
	assert_not_null(res, "No se encontro el recurso base_vehicle.tscn")
	vehicle = res.instantiate() as RigidBody3D
	assert_not_null(vehicle, "No se pudo instanciar base_vehicle.tscn como RigidBody3D")
	add_child_autofree(vehicle)

# Respeta _integrate_forces: cada iteracion es un tick fisico real del SceneTree.
func step_physics(seconds: float) -> void:
	var iterations: int = ceili(seconds * Engine.physics_ticks_per_second)
	for i: int in range(iterations):
		await get_tree().physics_frame
```
Uso en tests: `await step_physics(2.0)`. El test debe ser `async` implícito (contener `await`).

---

# 3. DIRECCIÓN DE ARTE, CÁMARA Y MUNDO

## 3.1 Estilo visual y shaders retro
- **Identidad estética:** Atmósfera inspirada en el acabado analógico VHS/CRT urbano de finales de los 2000 en la metrópolis ficticia de **Ditroiffe**. Tonos otoñales, ocres y niebla volumétrica densa con iluminación industrial.
- **Presupuesto poligonal y texturizado [SUPUESTO]:**
  - Elementos urbanos y mobiliario: 200 a 2.000 triángulos.
  - Peatones NPC: 600 a 1.200 triángulos.
  - Vehículos de tráfico civil: 1.500 a 4.000 triángulos.
  - Flota del jugador (con cabina interior completa): 3.000 a 6.000 triángulos.
  - Texturas optimizadas en atlas de 256 a 512 píxeles con filtro *nearest* en magnificación y mipmaps en minificación.
- **Validadores automáticos de arte:** Pruebas unitarias que verifican que ninguna textura tenga desviación cromática nula y que cada edificio posea al menos 4 materiales y 6 elementos modulares de fachada.
- **Post-proceso analógico configurable:**
  - Shader CRT: curvatura cóncava suave, líneas de exploración (*scanlines*) configurables y aberración cromática en bordes.
  - Shader VHS: sangrado horizontal de color (*color bleeding*), grano fílmico procedural y desincronización de cuadro (*tracking glitch* en menús).
  - Cada parámetro cuenta con slider de 0% a 100% y opción de desactivación total.
  - Modo obligatorio de accesibilidad: "Reducir destellos" para eliminar relámpagos y parpadeos estroboscópicos.

## 3.2 Cámara en primera persona con Alt-Look (Estilo Rust)
- **Perspectiva:** Punto de vista anclado en los ojos del conductor dentro del habitáculo. En vehículos con cabina se modela el salpicadero funcional, volante con rotación física, retrovisores laterales/central y asiento posterior.
- **Máquina de estados de la cámara:**
  - `DRIVE_LOCKED`: Alineada con el eje frontal del vehículo con amortiguación inercial exponencial [ESPEC-CORREGIDA]: `q = q.slerp(q_frente, 1.0 - exp(-delta * 15.0))` (λ = 15.0 en `CameraConfigResource`; independiente del framerate).
  - `FREE_LOOK`: Manteniendo presionada la tecla `Alt` (o gatillo `LB` en gamepad), permite giro libre de 360° en acimut (yaw) y $\pm 80^\circ$ en elevación (pitch).
  - `RETURNING`: Al soltar la tecla, regresa al frente con la misma ley exponencial (λ = 15.0 recorre ≈ 97.6 % del ángulo en 0.25 s). Pasa a `DRIVE_LOCKED` cuando `q.angle_to(q_frente) < 0.01 rad`.
- **Micro-movilidad sin cabina:** En skate, bicicleta y moto, el ángulo de yaw se restringe físicamente a $\pm 120^\circ$ con inclinación natural del cuello.
- **Retrovisores:** Renderizados mediante `SubViewport` a resolución reducida actualizados a 30 Hz (desactivables en preset Bajo para maximizar rendimiento).

## 3.3 Objetos olvidados y ruleta cosmética
- Al inicio de un servicio, el stream `rng_loot` evalúa la probabilidad de olvido según el perfil del pasajero.
- Al desembarcar el cliente, si se genera un objeto, se activa en la esquina superior del HUD una alerta visual naranja-rojiza palpitante con señal acústica.
- El jugador entra en modo `FREE_LOOK`, gira la vista hacia el asiento trasero, enfoca el objeto olvidado y pulsa la tecla de interacción (`E` / botón `A`), desplegando la interfaz de la **Ruleta Cosmética**.
- **Distribución de probabilidades de rareza:**
  - Común: 55%
  - Poco común: 25%
  - Rara: 12%
  - Épica: 6%
  - Legendaria: 2%
- El premio se resuelve matemáticamente mediante PRNG **antes** de comenzar la animación de giro. Todos los premios son 100% cosméticos (tapicerías, ambientadores, vinilos, llantas, pinturas, accesorios de salpicadero, tonos y fondos de pantalla del móvil). Prohibido el uso de dinero real o microtransacciones.

### 3.3.1 Despawn y persistencia del Loot Box [ESPEC]
- **Instanciación:** El nodo `LootBoxPickup3D` se emparenta en el marcador de asiento trasero del vehículo.
- **Condiciones de eliminación definitiva:**
  1. *Por distancia:* Si el vehículo de Mateo se aleja más de **200.0 metros euclidianos** del punto de desembarco del cliente sin haber recogido el objeto, el nodo ejecuta `queue_free()` de forma silenciosa y la alerta desaparece.
  2. *Por nuevo servicio:* Si Mateo acepta un nuevo viaje en la app *To The Top*, el objeto pendiente es retirado inmediatamente simulando su reclamo por el centro de soporte.

### 3.3.2 Objetos olvidados especiales para Sofía [ESPEC]
- Ciertos pasajeros con historias secundarias pueden olvidar juguetes retro, estuches escolares o libros infantiles.
- El jugador dispone de una decisión ética:
  - Vender el objeto en la PC de casa exclusivamente por **Dinero**.
  - Llevar el objeto a casa y colocarlo en la mesita de luz de la habitación de Sofía.
- Regalárselo a Sofía desbloquea diálogos de afecto familiar, dibujos infantiles en la pared del apartamento y progreso en los logros de Steam.

## 3.4 Diseño del mundo urbano (Ditroiffe)
- Prohibidas las vías desiertas: cada 100 metros de calle deben contener como mínimo 40 props en el Centro, 25 en Suburbios y 15 en Zona Industrial (postes con tendido eléctrico en catenaria, hidrantes, contenedores, paradas con marquesina, parquímetros, bancos y vegetación).
- Infraestructura vial: carriles de 3.0 a 3.5 m, aceras de 2.0 a 4.0 m con cordones de 15 cm y vados para peatones, rotondas, cruces semafóricos sincronizados en onda verde y fallos eléctricos nocturnos.
- Edificios con kit modular de 4 capas: planta baja, cuerpo, cornisa y remate, integrando ventanas con profundidad geométrica real, escaleras de incendio y mapeo de interiores falsos (*interior mapping*).
- **Zonificación y escalas:**
  - Suburbio residencial: ~2.0 km²
  - Gran centro metropolitano: ~3.0 km²
  - Zona industrial y portuaria: ~3.0 km²
- **Ciudades adicionales y costes de compra en Honor [ESPEC]:**
  - *Puerto Marea*: 250 Honor (costera, puentes colgantes y niebla salina).
  - *Valle Alto*: 600 Honor (montañosa, calzadas sinuosas y nieve).
  - *La Capital*: 1.200 Honor (distrito gubernamental con trazado urbano handcrafted con semilla fija para el convoy final).

## 3.5 Optimización HLOD por cuadrantes espaciales de 64 × 64 m [ESPEC]
- La ciudad organiza el mobiliario y la vegetación en bloques espaciales de 64 × 64 metros. Índice de celda [ESPEC-CORREGIDA]: `Vector2i(floori(pos.x / 64.0), floori(pos.z / 64.0))`.
- Cada cuadrante fusiona sus elementos estáticos mediante nodos `MultiMeshInstance3D` con desvanecimiento alfa por distancia (`visibility_range_end = 120.0 m`), garantizando el objetivo de menos de 1.200 draw calls en hardware GTX 1060.
- El preset gráfico **DEMENCIAL** habilita pases de iluminación global SDFGI a resolución nativa, reflejos SSR de alta precisión, oclusión ambiental SSAO/SSIL y texturas de 1024 px.

---

# 4. FÍSICA, MICRO-MOVILIDAD Y FLOTA COMERCIAL DE 10 VEHÍCULOS

## 4.1 Simulación física realista y reglas anti "vibe coding" [ESPEC]
- La simulación física se procesa estrictamente en `_integrate_forces(state)` para todo lo que modifica el cuerpo rígido (fuerzas, pares, impulsos). `_physics_process` solo lee input y estado. Prohibido escribir `global_position`, `rotation`, `linear_velocity` o `angular_velocity` desde fuera de `state`.
- **Vehículos de 4 ruedas:** Raycast de suspensión independiente en cada rueda (cálculo de fuerza elástica por ley de Hooke y amortiguación viscosa), transferencia de peso longitudinal por cabeceo en aceleración/frenado, balanceo lateral en curva y barras estabilizadoras transversales.
- **Dirección de los raycasts bajo gravedad alterada [ESPEC-CORREGIDA]:** en cada paso, `var g: Vector3 = state.get_total_gravity()`. Si `g.length_squared() > 1e-4`, la dirección de suspensión es `g.normalized()`; si no (gravedad ≈ 0), se usa `-state.transform.basis.y`. Así la suspensión responde a las `Area3D` que alteran la gravedad. La longitud del rayo es `rest_length + wheel_radius` (datos en `VehicleConfigResource`).
- **Estabilización de vehículos de 2 y 3 ruedas (Inercia Compuesta) [ESPEC-CORREGIDA]:**
  Queda estrictamente prohibido manipular de forma directa las variables `rotation`, `angular_velocity` o la matriz `transform.basis` de un chasis en movimiento (práctica de *vibe coding* que rompe colisiones y causa atravesamientos). La estabilidad de motos, bicicletas y el motocarro (3 ruedas) debe lograrse aplicando exclusivamente pares compensatorios dentro de `_integrate_forces()`.
  `state.get_inverse_inertia_tensor()` devuelve un `Basis` 3×3 **simétrico** expresado **ya en espacio global**. No es un escalar ni debe rotarse otra vez. El controlador PD produce una aceleración angular deseada sobre el eje frontal local, que se transforma a espacio global y se multiplica por el tensor de inercia global:
  ```gdscript
  var inertia_world: Basis = state.get_inverse_inertia_tensor().inverse()
  var fwd: Vector3 = -state.transform.basis.z
  var up: Vector3 = state.transform.basis.y
  var target_up: Vector3 = ground_normal  # normal media de los contactos de rueda; o -gravedad normalizada en el aire
  var delta_roll: float = up.signed_angle_to(target_up.slide(fwd).normalized(), fwd)
  var omega_roll: float = state.angular_velocity.dot(fwd)
  var alpha_world: Vector3 = fwd * (cfg.k_p * delta_roll - cfg.k_d * omega_roll)  # rad/s^2
  state.apply_torque(inertia_world * alpha_world)
  ```
  $$\vec\tau_{\text{estabilizador}} = \mathbf{I}_{\text{global}} \cdot \hat f \left( k_p \, \Delta\theta_{\text{roll}} - k_d \, \omega_{\text{roll}} \right)$$
  (El signo de `delta_roll` ya apunta hacia la corrección; `k_p`, `k_d` > 0 viven en `VehicleConfigResource`. El par se limita a `cfg.max_stabilizer_torque` para no anular las caídas legítimas.)
- **Fricción de neumáticos (confinada a raycasts) [ESPEC-CORREGIDA]:**
  La combinación de fricción por defecto de Jolt (media geométrica $\sqrt{\mu_1 \cdot \mu_2}$) **no se modifica globalmente**: el solver de contactos de Jolt es C++ interno y alterarlo exigiría callbacks de contacto costosos. El chasis conserva la fricción estándar de Jolt para choques, roces y vuelcos. Los neumáticos **no** son colisionadores con fricción: son raycasts cuyas fuerzas longitudinal y lateral se calculan por código, limitadas por el círculo de fricción con el coeficiente efectivo:
  $$\mu_{\text{efectivo}} = \min(\mu_{\text{neumático}},\, \mu_{\text{superficie}}) \qquad |\vec F_{\text{long}} + \vec F_{\text{lat}}| \le \mu_{\text{efectivo}} \cdot F_{\text{normal,suspensión}}$$
  $\mu_{\text{superficie}}$ se lee del `PhysicsMaterial`/metadato de la superficie golpeada por el raycast, modulado por el clima (`WeatherProfileResource`).
  - Asfalto seco: $\mu = 1.0$
  - Asfalto mojado: $\mu \approx 0.65$
  - Nieve compacta: $\mu \approx 0.35$
  - Hielo negro: $\mu \approx 0.15$
- **Arrastre aerodinámico y rodadura [ESPEC-CORREGIDA]:** fuerza vectorial opuesta a la velocidad, aplicada en el centro de masa:
  $$\vec F_d = -\frac{1}{2} \rho \, C_d \, A \, |\vec v| \, \vec v \qquad \vec F_{rr} = -C_{rr} \, F_{\text{normal}} \, \hat v_{\text{plano}}$$
  ```gdscript
  var v: Vector3 = state.linear_velocity
  state.apply_central_force(-0.5 * cfg.air_density * cfg.drag_coefficient * cfg.frontal_area * v.length() * v)
  ```
  ($\rho = 1.225$ kg/m³ [SUPUESTO]). La velocidad terminal emerge de la interacción entre potencia motriz y resistencia al avance, sin limitadores artificiales de velocidad. Con gravedad nula o negativa (zonas de antigravedad), el arrastre vectorial es lo que garantiza una velocidad acotada.

## 4.2 Micro-movilidad realista
- Velocidades sustentadas en la potencia metabólica y la inercia del ciclista/patinador. Potencia humana base sostenida: ~100 a 150 W.
- Vulnerabilidad física: Desniveles o bordillos superiores a 5 cm impactados a más de 12 km/h expulsan al avatar del skate o bicicleta, provocando caída física, pérdida de tiempo y daño a los paquetes transportados.

## 4.3 Progresión completa de la flota: 10 vehículos comerciales + 1 de misión [ESPEC]
El garaje comercial del jugador almacena hasta 10 unidades (Vehículos 1 al 10). El Vehículo 11 es estrictamente narrativo y se desbloquea como trofeo de conducción libre tras finalizar la campaña.

| # | Vehículo | Masa con conductor | Potencia motriz | Coste de compra | Velocidad máxima | Aceleración objetivo | Particularidades técnicas |
|---|---|---|---|---|---|---|---|
| **1** | **Skate** | 80 kg | Humana (~100 W) | **Inicial ($0)** | 15 km/h (35 bajada) | 0 a 12 km/h en ~6 s | Tracción por estamina; sin motor; máxima agilidad peatonal |
| **2** | **Bicicleta** | 95 kg | Humana (~120 W) | **$150** | 25 km/h (40 bajada) | 0 a 20 km/h en ~8 s | Tracción por estamina; cadencia progresiva |
| **3** | **Monopatín Eléctrico** | 90 kg | 0.35 kW eléctrico | **$400** | 25 km/h | 0 a 20 km/h en ~5 s | Batería Li-Ion 0.45 kWh; inmovilizable a 0 kWh |
| **4** | **Bici Moto** | 110 kg | 1.5 kW (2T) + pedal | **$900** | 45 km/h | 0 a 40 km/h en ~9 s | Depósito 2.5 L; híbrido utilitario de bajo coste |
| **5** | **Moto Urbana 150cc** | 210 kg | 9.0 kW térmico | **$2.000** | 100 km/h | 0 a 60 km/h en ~6 s | Depósito 11.0 L; filtra atascos; alta penalización por caída |
| **6** | **Carro Moto (Tuk-Tuk)**| 520 kg | 8.0 kW térmico | **$3.500** | 55 km/h | 0 a 40 km/h en ~8 s | Depósito 14.0 L; 3 ruedas delta; requiere tensor inercial |
| **7** | **Auto Usado 90s (Mal)** | 1.350 kg | 62 kW efectivos | **$1.200** | 150 km/h | 0 a 100 km/h en ~15 s | Depósito 45.0 L; carburador desajustado, alineación desviada |
| **8** | **Auto Usado Estándar** | 1.300 kg | 85 kW inyección | **$4.500** | 185 km/h | 0 a 100 km/h en ~10.5 s | Depósito 50.0 L; balance óptimo de consumo y confort |
| **9** | **Auto Gama Alta** | 1.550 kg | 150 kW inyección | **$18.000** | 220 km/h | 0 a 100 km/h en ~7.5 s | Depósito 60.0 L; frenos ABS y climatización automática |
| **10**| **Van Ejecutiva / Carga**| 2.200 kg | 120 kW diésel | **$18.000** | 175 km/h | 0 a 100 km/h en ~12 s | Depósito 75.0 L; alto volumen de carga; centro de masa elevado |
| **11**| **Convoy Presidencial** | 3.400 kg | 300 kW V12 blindado| **Incomprable** | 200 km/h | 0 a 100 km/h en ~9.5 s | Depósito 110.0 L; exclusivo de la misión final del Acto 4 |

## 4.4 Daño zonal y reparaciones
- Modelo de deformación y fallo distribuido en cuatro zonas: frontal, trasera, lateral izquierda y lateral derecha.
- Los impactos alteran el reglaje de alineación de las ruedas directrices, reducen la compresión del motor y provocan efectos estéticos (deformación de vértices de chapa, fractura de lunas y emisión de humo).
- Reparaciones menores de carrocería en talleres de servicio (coste en dinero y tiempo); reparaciones integrales de motor disponibles desde la PC del apartamento.

## 4.5 Fórmulas de consumo energético y dinámica de recarga [ESPEC]
Prohibido el uso de temporizadores lineales arbitrarios. El consumo energético deriva del trabajo mecánico efectivo y se integra explícitamente en cada paso físico [ESPEC-CORREGIDA]: $E_{t+\Delta t} = \max(0,\, E_t - \text{Consumo}\cdot\Delta t)$, con $\Delta t$ = paso físico (1/60 s) multiplicado por el factor del reloj de juego cuando corresponda (sección 0.3). Se usa $|\tau_{\text{actual}}|$ para que el freno motor no "devuelva" combustible.
- **Motor Térmico:**
  $$\text{Consumo (L/s)} = \left( \frac{\text{RPM}}{\text{RPM}_{\max}} \times \text{Tasa Base} \right) + \left( \frac{\tau_{\text{actual}}}{\tau_{\max}} \times \text{Factor Carga} \right)$$
- **Tracción Eléctrica:**
  $$\text{Consumo (kWh/s)} = \left( \frac{P_{\text{eléctrica (W)}}}{3.600.000} \right) \times \frac{1.0}{\eta_{\text{inversor}}}$$
- **Tracción Metabólica (Estamina humana) [ESPEC]:**
  - El skate consume estamina exclusivamente durante la patada de impulso activa ($5.0\text{ pts}$ por acción de aceleración). El rodaje libre por inercia (*coasting*) o en pendiente descendente tiene un coste de $0.0\text{ pts/s}$.
  - La bicicleta consume $1.0\text{ pts/s}$ en pedaleo plano y $5.0\text{ pts/s}$ en cuestas pronunciadas o sprints, reduciéndose a $0.0\text{ pts/s}$ sin pedalear.
- **Régimen del Monopatín Eléctrico [ESPEC]:**
  No admite combustible líquido en gasolineras. Su recarga se efectúa exclusivamente en el parking del apartamento conectando el cable de pared interactivo (4 horas de juego) o durmiendo. Si la batería cae a $0.0\text{ kWh}$, el motor se apaga y el vehículo queda inmovilizado, forzando a Mateo a desplazarse a pie o solicitar el remolque a casa desde la app por $40.

---

# 5. TELÉFONO MÓVIL Y APLICACIONES (PIXEL ART)

El teléfono inteligente de Mateo se despliega en pantalla con estética pixel art retro y escalado entero. Máquina de estados: `CLOSED`, `OPENING`, `OPEN`, `CLOSING`. El juego **no se pausa** durante su manipulación.

## 5.1 App Mapa interactivo
- Animación de despliegue inspirada en un papiro vectorial retro.
- Muestra puntos de recogida, clientes en espera, rutas de entrega y objetivos de campaña.
- **Minimapa en HUD:** Al trazar una ruta, el mapa se minimiza en un micro-teléfono fijado en la esquina inferior del visor con guía paso a paso.
- **Excepción de seguridad [ESPEC]:** Durante la misión final del Convoy Presidencial (Acto 4), el GPS queda totalmente inhabilitado (*"SEÑAL BLOQUEADA POR PROTOCOLO MILITAR"*), forzando navegación verbal pura.

## 5.2 App "To The Top"
- **Radar Común:** Solicitudes de transporte urbano regular con variabilidad estándar de pasajeros.
- **Radar Premium:** Desbloqueado con Honor en la PC; clientes ejecutivos de alto pago y baja tolerancia a infracciones.
- **Encargos:** Envíos de paquetería sellada con plazos de entrega estrictos.
- **Servicios?!:** Viajes nocturnos clandestinos de alta remuneración, disponibles exclusivamente de 00:00 a 05:00.
- **Comida a domicilio:** Transporte de comida caliente; las aceleraciones bruscas y choques reducen la calidad de entrega.
- **Recados a pie [APROBADO]:** Misiones secundarias de 3 a 8 minutos en primera persona. Mínimo de 24 recados redactados a mano (8 por zona).
- **Fórmula de Honor por servicio [SUPUESTO]:**
  $$\text{honor} = \text{round}\left( \text{honor\_base} \times \text{factor\_calificación} \times (1.0 + \text{multiplicador\_permanente}) \right)$$
  - Factores: 1 estrella = 0.0 | 2 estrellas = 0.4 | 3 estrellas = 0.7 | 4 estrellas = 1.0 | 5 estrellas = 1.25.

## 5.3 App Tiempo y Pronóstico
Sincronizada con el sistema climático real. Fundamental para anticipar tormentas o nevadas y preacondicionar la cabina.

## 5.4 App Reglas Top y Mensajería vial
Compendio legal de límites de velocidad por distrito. Los cinemómetros y radares de tráfico notifican multas económicas inmediatas por exceso de velocidad al ser sobrepasados.

## 5.5 App Flota (Imperio) [APROBADO]
Desbloqueada en el nivel 3 del apartamento. Permite gestionar choferes, asignación de vehículos, visualización de cuota de mercado frente a "VíaDirecta" y pólizas de flota.

## 5.6 Pipeline pixel-perfect del teléfono con escalamiento entero [ESPEC]
- El teléfono se renderiza en un `SubViewport` independiente a resolución nativa de **240 × 320 píxeles** con `texture_filter = TEXTURE_FILTER_NEAREST` y su propio sombreador CRT plano de curvatura cero.
- **Control de distorsión fraccionaria [ESPEC]:** Para erradicar el parpadeo de píxeles (*pixel shimmer*), la bandera de escalamiento automático de Godot se desactiva. El nodo `CanvasLayer` calcula por código el factor entero exacto según la altura de la ventana:
  $$\text{escala} = \max\left(1,\, \lfloor \text{viewport\_height} / 320 \rfloor \right)$$
  Multiplicando las dimensiones en pantalla estrictamente por $1\times, 2\times, 3\times, 4\times$.
- **Implementación [ESPEC-CORREGIDA]:** `SubViewportContainer.stretch = false` (evita que el contenedor redimensione el `SubViewport` y reintroduzca filtrado) y `texture_filter = TEXTURE_FILTER_NEAREST`. Un nodo `Control` gestor escucha `get_viewport().size_changed` y aplica:
  ```gdscript
  var win: Vector2 = get_viewport_rect().size
  var factor: int = maxi(1, floori(win.y / 320.0))
  %PhoneContainer.scale = Vector2(factor, factor)
  %PhoneContainer.position = Vector2(win.x - 240.0 * factor, win.y - 320.0 * factor).floor()  # anclado abajo a la derecha
  ```
  En Godot 4 la propiedad es `scale`; `rect_scale` (Godot 3) no existe.

---

# 6. JORNADA, SUPERVIVENCIA, CLIMA Y ECONOMÍA

## 6.1 Jornada, turnos y ciclo de descanso
- Jornada laboral estructurada en un cupo de 5 a 8 servicios (seleccionado por el jugador al salir).
- Turnos: Diurno (inicia a las 07:00) y Nocturno (inicia a las 20:00).
- Al completar el cupo de servicios o superar la hora máxima del turno, la aplicación emite la orden obligatoria: *"Jornada finalizada: Vuelve a casa a descansar"*.
- El jugador puede regresar conduciendo (obteniendo un bonus de Honor si no sufre daños) o utilizar el viaje rápido cinematográfico.

### 6.1.1 Restricción biológica de sueño y Zona de Asueto Activo [ESPEC]
Para erradicar exploits de acumulación pasiva de ingresos durmiendo repetidamente:
1. **Regla de precedencia:** El estado `SHIFT_COMPLETED` habilita la cama de inmediato, ignorando restricciones fisiológicas.
2. **Condición normal de descanso:** Fuera de fin de jornada, dormir solo es accesible si:
   $$\text{fatigue} \ge 50.0 \quad \lor \quad \Delta t_{\text{vigilia}} \ge 10.0\text{ horas}$$
   De lo contrario, Mateo emite el bark en pantalla: *"No tengo sueño todavía, tengo que salir a trabajar"*.
3. **Mecánica de Asueto Activo (Anti-Softlock) [ESPEC]:**
   Si el jugador decide no trabajar pero no puede dormir, puede interactuar con el sofá/televisor del apartamento. Esta acción acelera el reloj de juego a $5\times$, multiplicando por 5 la tasa de consumo de hambre y sed, a cambio de incrementar la fatiga a razón de $15.0\text{ pts/min}$ real hasta superar el umbral de 50.0, permitiendo dormir de forma orgánica.
   **Anclaje [ESPEC-CORREGIDA]:** con la escala base (0.3) de 1 s real = 1 min de juego, el asueto avanza 5 min de juego por segundo real. Se implementa como multiplicador del reloj lógico (`GameClock.time_multiplier = 5.0`), **no** con `Engine.time_scale`, para no alterar el paso físico de Jolt. La fatiga sube $15.0/60$ pts por segundo real, así que desde 0 se llega a 50 en ≈ 200 s reales.

## 6.2 Supervivencia: hambre, sed y fatiga
- Hambre y sed (0.0 a 100.0) crecen de forma continua, acelerándose en horas pico (desayuno 07:00-09:00, almuerzo 12:00-14:00, cena 20:00-22:00). Avisos en HUD a 60, 80 y 90 puntos.
- **Desmayo:** Si el hambre o la sed alcanzan 100.0, Mateo sufre un colapso físico y despierta en su cama. Consecuencias: la jornada concluye con calificación 0 estrellas y se pierde el 100% del Honor acumulado en ese turno.

## 6.3 Confort de cabina bajo clima extremo
- Clima extremo: Lluvia intensa, nieve o temperatura $\ge 40^\circ\text{C}$.
- **Máquina de estados de quejas:**
  1. Si la cabina no está acondicionada (ventanillas cerradas y A/C o calefacción encendida), tras $T_{\text{aviso1}}$ el pasajero emite el **Aviso 1**.
  2. Si transcurren $G_1$ segundos sin corregir: penalización registrada de **-1 estrella**.
  3. Tras $T_{\text{aviso2}}$, emite el **Aviso 2**. Si transcurren $G_2$ segundos: penalización total de **-2 estrellas** y tope máximo de calificación limitado a **3 estrellas**.

## 6.4 Estaciones de servicio y Drive-Thru "Auto-Bite"
- Surtidores con playero NPC interactivo: parada en bahía, repostaje asistido de 5 segundos con deducción monetaria.
- Ventanilla Drive-Thru con consumibles:
  - Hamburguesa: -50 Hambre
  - Gaseosa: -60 Sed
  - Café / Energizante: Reduce fatiga y otorga aceleración temporal
  - Chicles: Reduce estrés y mejora adherencia temporal
  - Cigarros: Reduce estrés del conductor
  - Cerveza: Aplica estado Alcoholizado (sección 6.7)

## 6.5 Inventario estructurado de 4 ranuras y Nevera acotada [ESPEC]
- **Ranuras 1, 2 y 3 (Bolsillos rápidos):** Exclusivas para consumibles y kit de reparación rápida.
- **Ranura 4 Pasiva (Compartimento de misión):** Activada automáticamente al portar paquetes de recados o entregas especiales.
- **Regla de caída física al suelo:** Si Mateo recibe un objeto físico y sus 3 ranuras rápidas están llenas, el objeto cae al suelo como `RigidBody3D` con shader de destello pixel art. Para recogerlo debe liberar una ranura consumiendo o descartando un ítem.
  - **Especificación [ESPEC-CORREGIDA]:** impulso inicial `body.apply_central_impulse((-camera.global_basis.z * 1.5 + Vector3.UP).normalized() * 2.5)` (N·s). Destello en el shader: `EMISSION = albedo * (sin(TIME * 5.0) * 0.5 + 0.5);` (sin timers en GDScript; se atenúa con "Reducir destellos"). Despawn con `queue_free()` a ≥ 200.0 m del jugador o al aceptar un nuevo servicio (misma regla que 3.3.1).
- **Nevera doméstica limitada [ESPEC]:** La nevera del apartamento cuenta con un inventario estricto de **20 casillas (slots)**. No es una fuente gratuita e infinita de alimentos; el jugador debe abastecerla comprando provisiones en tiendas o estaciones de servicio.

## 6.6 Economía dual y deducción formal de Honor [ESPEC]
- **Dinero:** Moneda operativa fungible.
- **Honor:** Moneda permanente de prestigio.
- **Deducción matemática por castigos [ESPEC-CORREGIDA]:**
  $$|\vec p| = \Big|\sum_i \texttt{state.get\_contact\_impulse}(i)\Big| \qquad \text{daño\_normalizado} = \text{clamp}\left(\frac{|\vec p|}{m_{\text{vehículo}} \cdot \Delta v_{\text{umbral}}},\, 0.0,\, 1.0\right)$$
  $$\text{honor\_deducido} = \text{roundi}(15.0 \times \text{daño\_normalizado})$$
  Con $\Delta v_{\text{umbral}} = 10.0$ m/s [SUPUESTO] (el cociente impulso/masa equivale a la variación de velocidad del impacto). El impulso se acumula en `_integrate_forces` (requiere `contact_monitor = true` y `max_contacts_reported >= 4`) y se evalúa una vez por evento de colisión (enfriamiento de 0.5 s por cuerpo colisionado). Al estar acotado a [0, 1], el `min(15, ·)` de v6 resultaba redundante.
  Las cancelaciones voluntarias deducen el 50% del honor base del servicio. La destrucción de carga deduce el 80%. El tope máximo de deducción por colisión se fija en **-15 puntos de Honor**, impidiendo balances irrecuperables por bucles de choque.

## 6.7 Alcohol, mareo y retenes de alcoholemia [APROBADO]
- Cerveza suma unidades de alcohol; estado **Alcoholizado** escala de nivel 1 a 3 y decae exponencialmente en el tiempo.
- Efectos de control: Latencia de dirección añadida de 60 a 150 ms y ruido de baja frecuencia en el ángulo del volante.
  - **Especificación del ruido [ESPEC-CORREGIDA]:** `FastNoiseLite` con `noise_type = TYPE_SIMPLEX`, `frequency = 1.2`, semilla derivada de `rng_passengers` al inicio del servicio. $\theta_{\text{ruido}} = \text{noise.get\_noise\_1d}(t_{\text{sim}}) \cdot 0.25\text{ rad} \cdot (\text{nivel}/3)$, sumado al input de dirección. $t_{\text{sim}}$ es el tiempo acumulado de simulación (nunca el reloj del sistema). Latencia: búfer circular de inputs con retardo lineal `60 + 45·(nivel-1)` ms.
  - Umbral del retén: $|\vec v| > 20\text{ km/h}$ se evalúa como `linear_velocity.length() > 20.0 / 3.6`.
- Efectos de presentación: Doble imagen temporal (*ghosting*), aberración cromática radial y filtro paso bajo en audio.
- Bonificación "Pulso firme": Si el viaje concluye sin choques ni infracciones bajo alcohol, otorga bono extra de calificación y dinero.
- **Resolución sistémica de retenes (Cero combate) [ESPEC]:**
  Los retenes policiales embudan el tráfico con conos hacia un sensor `Area3D`. Si Mateo se detiene, se practica el test de alcoholemia (multa de $180 y -10 Honor si da positivo). Si Mateo embiste los conos o supera el control a más de 20 km/h, la cámara registra la matrícula: multa automática de $300 por evasión, -15 Honor y suspensión temporal de la app *To The Top* por 2 horas de juego, sin tiroteos ni persecuciones armadas.
- Modo sin alcohol: Sustituye la cerveza por bebida de malta y renombra el estado a "Aturdimiento".

## 6.8 Cuota escolar de Sofía (`TuitionSystem`) [ESPEC]
- **Vencimiento periódico:** Cada **7 días de juego** a las 08:00 AM.
- **Escala de montos obligatorios:**
  - Semanas 1 a 4: **$350**
  - Semanas 5 a 8: **$600**
  - Semanas 9 en adelante: **$1.000**
- **Línea de penalizaciones por impago (Sin Game Over):**
  - *Día +1:* Notificación telefónica de aviso.
  - *Día +3:* Recargo punitorio acumulativo del 10% sobre la deuda.
  - *Día +7:* Notificación física en casa. Sofía permanece en silencio. Estrés basal de Mateo aumenta permanentemente en $+15.0\text{ pts}$.
  - *Día +14 (Retención Coercitiva Judicial):* La app *To The Top* retiene automáticamente el **30% de los ingresos brutos** de cada servicio completado hasta liquidar el pasivo escolar.
  - **Guardas matemáticas [ESPEC-CORREGIDA]:** la retención se evalúa **solo si** `ingreso_bruto > 0`. Monto: `retencion = mini(roundi(ingreso_bruto * 0.30), deuda_pendiente)`, de modo que la deuda nunca queda negativa. Servicios cancelados, a 0 estrellas o con ingreso 0 no disparan la retención. Los días se cuentan en días de juego según la escala de la sección 0.3.

## 6.9 Pólizas de seguro vehicular [APROBADO]
Póliza Básica (daños menores) o Completa (cubre siniestros totales con deducible). Sistema bonus-malus en la prima según historial de siniestros. Carencia de 1 jornada tras la contratación. Quedan excluidos los accidentes provocados bajo el estado Alcoholizado.

---

# 7. VALORACIÓN, ESTRÉS, CASTIGOS Y MUTADORES

## 7.1 Puntuación de servicios
Puntaje de 0 a 100 distribuido en: Tiempo (25), Seguridad vial (25), Suavidad de manejo (20), Confort climático (15) y Estrés final del cliente (15). Mapeo: $\ge 90 \to 5\star$, $\ge 75 \to 4\star$, $\ge 60 \to 3\star$, $\ge 40 \to 2\star$, $< 40 \to 1\star$. El ítem "Caramelo" eleva a 5 estrellas si el puntaje supera 75 y no hubo faltas graves.

## 7.2 Dinámica de estrés y fatiga
- **Estrés del pasajero (0-100):** Se incrementa por choques, aceleraciones bruscas, retraso frente al ETA y música no deseada. A 60 puntos anula propinas; a 80 puntos cancela el viaje exigiendo desembarque inmediato.
- **Estrés del conductor (0-100):** Provoca viñeta oscura en bordes y amortiguación de audio. Se alivia consumiendo chicles, cigarros o sintonizando emisoras relajantes.
- **Fatiga:** Reduce la agilidad motriz tras jornadas largas; se contrarresta temporalmente con café o energizantes.

## 7.3 Pasajeros con arcos y diálogos contextuales [APROBADO]
Clase `PassengerProfileResource` con rasgos de personalidad. Mínimo de 60 perfiles únicos y 20 personajes recurrentes con arcos de 3 a 5 apariciones. Diálogos interactivos con 2 o 3 opciones en tiempo real seleccionables con la cruceta o teclado sin pausar la marcha.

## 7.4 Dificultad, mutadores y perks de run
- **Dificultades:** Turista (Fácil), Conductor (Normal), Profesional (Difícil, mandatorio para desbloquear la misión final) y Leyenda (Extremo).
- **Mutadores de Run (Rogue-lite):** Sorteo de 2 o 3 mutadores al iniciar una run desde una tabla de $\ge 20$ reglas especiales (ej. huelga de transporte, inspectores de radar masivos, lluvias torrenciales continuas).
- **Perks de jornada:** Al culminar un turno, Mateo elige 1 de 3 ventajas de una tabla de $\ge 40$ modificadores (neumáticos de lluvia, termo de café, amortiguadores reforzados).

---

# 8. IA DE TRÁFICO Y PEATONES

## 8.1 Variedad de agentes
- Al menos 50 vehículos NPC distintos con 18 mallas base (sedanes, pickups, buses, taxis, ambulancias, patrullas).
- Al menos 50 peatones NPC con 12 cuerpos base (estudiantes, oficinistas, obreros, ancianos, turistas).

## 8.2 Arquitectura de IA en tres niveles
1. *Nivel Estratégico:* Planificación de itinerarios mediante búsqueda A* sobre el grafo vial y aceras.
2. *Nivel Táctico:* Máquinas de estados jerárquicas (incorporaciones, ceda el paso, cruces peatonales, huida ante accidentes).
3. *Nivel Operacional:* Control cinemático mediante *Intelligent Driver Model* (IDM) y cambios de carril MOBIL para tráfico; evitación de colisiones ORCA para peatones.

## 8.3 Simulación escalonada (LOD de 3 niveles)
- LOD 0 (Radio < 50 m): Simulación física completa con raycasts y colisionadores. Máximo 40 vehículos y 60 peatones.
- LOD 1 (Radio 50 a 150 m) [ESPEC-CORREGIDA]: Agentes cinemáticos sin cuerpo físico sobre `Curve3D` del grafo vial: $s(t+\Delta t) = s(t) + v\,\Delta t$ (con $v$ dada por IDM); transformada vía `curve.sample_baked_with_rotation(s)`; dibujado en lote con `MultiMeshInstance3D`. Hasta 200 vehículos.
- LOD 2 (Radio > 150 m) [ESPEC-CORREGIDA]: Sin transformadas ni geometría. Cada agente es un registro escalar (`segmento`, `s`, `v`, `destino`) en `PackedFloat32Array`/`PackedInt32Array`.
- **Arquitectura multihilo (DoD) [ESPEC-CORREGIDA]:** el hilo principal copia el estado a arreglos empaquetados y los despacha a `WorkerThreadPool`; los workers calculan IDM/MOBIL/ORCA y A* **solo** sobre esos arreglos y nunca acceden al SceneTree ni a nodos. Al terminar (`wait_for_group_task_completion` en el frame siguiente), el hilo principal escribe en bloque al buffer del MultiMesh (`RenderingServer.multimesh_set_buffer`). Las transiciones LOD 0 ↔ 1 instancian/liberan cuerpos desde un pool preasignado.

---

# 9. CLIMA Y ATMÓSFERA DINÁMICA

Simulación determinista basada en el stream `rng_weather`:
- Estados climáticos: Despejado, Nublado, Llovizna, Tormenta eléctrica, Nieve y Ola de calor.
- Lluvia física con partículas colisionables, gotas dinámicas en el parabrisas interactuando con los limpiaparabrisas y charcos con reflejos en tiempo real.
- Tormentas eléctricas con relámpagos sincronizados con truenos según la velocidad del sonido ($343\text{ m/s}$) y parpadeo de farolas urbanas.
- **Retardo acústico [ESPEC-CORREGIDA]:** $\Delta t_{\text{trueno}} = |\vec p_{\text{rayo}} - \vec p_{\text{jugador}}| / 343.0$ s, programado con `get_tree().create_timer(dt, false, true)` (respeta pausa y tick físico). Posición del rayo y momento del destello se sortean con `rng_weather`. Con "Reducir destellos" activo se suprime el flash, no el trueno.

---

# 10. CAMPAÑA, CASA, HONOR TOP, IMPERIO Y COMPETENCIA

## 10.1 Campaña narrativa: "Camino a la Cima"
- **Acto 1:** Mateo pierde su empleo fabril; Don Tito lo tutela y construye su primera bici-moto.
- **Acto 2:** Ingreso a los *Servicios?!*; descubrimiento del entramado de desahucios liderado por "El Presidente".
- **Punto Medio (50% de misiones):** Desbloqueo de compra de ciudades comerciales y acceso al prestigio **Honor Top**.
- **Acto 3:** Expansión a *Puerto Marea* y *Valle Alto*; traslado de fiscales y periodistas clave.
- **Acto 4:** Misión final del Convoy Presidencial en *La Capital*.

### 10.1.1 Misión final: Convoy Presidencial (Acto 4) [ESPEC]
- **Cero Armamento y Cero Combate:** Los bloqueos se resuelven exclusivamente mediante inercias y físicas de colisión (apartar furgonetas de prensa de 2.200 kg a más de 60 km/h, contrarrestar maniobras PIT de sedanes atacantes de 1.300 kg).
- **Ceguera de GPS:** Aplicación de Mapa inhabilitada por interferencia de seguridad militar.
- **Trazado urbano fijo:** *La Capital* se genera con semilla inmutable handcrafted.
- **Guiado verbal seguro (Anti-Broadphase Saturation) [ESPEC]:**
  Queda terminantemente prohibido utilizar proyecciones volumétricas (`ShapeCast3D`) para activar los comandos de voz del dignatario, ya que saturan el broadphase de Jolt a altas velocidades. El sistema evalúa de forma puramente matemática la distancia al cuadrado (`distance_squared_to`) entre el vehículo y las coordenadas estáticas de los nodos de cruce:
  $$d_{\text{trigger}}^2 \le \left( \frac{v_{\text{km/h}}}{3.6} \times 3.5\text{ s} \right)^2$$
- **Telemetría crítica:** `armor_integrity` (falla a 0.0), `vip_stress` y `vip_nausea` (subtítulos 100% legibles garantizados).

## 10.2 Honor Top (Prestigio) [ESPEC]
- Se activa en la PC de casa tras superar el 50% de la campaña.
- **Vuelta estricta a cero:** Mateo reaparece en el Suburbio de Ditroiffe únicamente con el Skate (Vehículo 1) y **$0 en la billetera**. Las ciudades comerciales se bloquean debiendo ser adquiridas nuevamente en la app libre. Las banderas de historia y el nivel del apartamento se conservan.
- **Retención matemática canónica:**
  $$\text{honor\_retenido} = \lfloor \text{honor\_acumulado} \times 0.20 \rfloor$$
- **Multiplicador permanente:** Otorga un bono aditivo acumulativo de **+0.25** a los ingresos de Dinero y Honor por cada run, hasta un tope máximo de **+2.00**.

## 10.3 Base del jugador: apartamento evolutivo
- Controlador peatonal en primera persona (`OnFootController` en `CharacterBody3D`).
- 5 niveles de vivienda: desde departamento ruinoso con goteras hasta Penthouse con sala de operaciones.
- PC de casa: terminal para transferencias escolares, pólizas, compra de ciudades, árbol de mejoras y Honor Top.
- Rutina de Sofía: Presente de 16:00 a 20:30; si Mateo pasa 3 jornadas consecutivas sin interactuar, sufre una penalización de estrés de $+10.0\text{ pts}$.

## 10.4 Competencia semanal asíncrona
Tablas de clasificación semanales en Steam Leaderboards basadas en el Honor total invertido durante la semana en curso (UTC).

## 10.5 Imperio (Tycoon de flota) [APROBADO]
- Desbloqueado en el Nivel 3 del apartamento. Permite gestionar una flota de hasta 60 unidades asignadas entre los Vehículos 3 al 10.
- Choferes contratados (`HiredDriverResource`): Salario compuesto ($35/día base + 20% comisión bruta), fatiga acumulable y lealtad.
- Simulación abstracta (`AbstractTripSim`): Resuelve los trayectos de los empleados en segundo plano al dormir el jugador.
- Mecánica de crisis: Si la empresa permanece con saldo negativo 3 días seguidos, se embarga el último vehículo adquirido.
- **Rivalidad callejera con "VíaDirecta" [ESPEC]:** Taxis rivales patrullan las calles; si un rival llega a menos de 8.0 m de un cliente en la ventana de disputa (45-90 s), el servicio se cancela y se pierde el cobro.

## 10.6 Reto diario determinista [APROBADO]
- Jornada única de 8 servicios en modo sandbox aislado de la partida principal.
- **Derivación determinista de semilla [ESPEC-CORREGIDA]:**
  ```gdscript
  var utc: Dictionary = Time.get_datetime_dict_from_system(true)
  var date_str: String = "%04d-%02d-%02d" % [utc.year, utc.month, utc.day]
  var daily_seed: int = ("top_challenge_" + date_str).hash() & 0x7FFFFFFF
  ```
  Garantiza un entero positivo idéntico en todas las plataformas y ejecuciones de test. Nota: `get_date_string_from_system(true)` de v6 ya era UTC y equivalente; se usa la forma explícita por legibilidad. `String.hash()` es determinista pero **no criptográfico**: si las tablas de clasificación requieren resistencia a manipulación, derivar con `HashingContext` (SHA-256) y tomar los primeros 31 bits. La semilla diaria del reto alimenta los streams `rng_*` del sandbox mediante offsets fijos por subsistema.

---

# 11. AUDIO Y RADIO

- Banda sonora original Lo-Fi para interfaces y apartamento.
- Radio del vehículo con emisoras de pop de los años 2000, rock alternativo, lo-fi y una estación de noticias con barks contextuales según eventos de la ciudad.
- Sonido posicional `AudioStreamPlayer3D` para motores diésel/gasolina modulados por RPM y carga física.

---

# 12. MENÚS, AJUSTES Y CALIDAD GRÁFICA

## 12.1 Menú principal
Estética VHS/CRT analógica con tres opciones principales: Modo Historia, Ajustes de Juego y Reportar Bugs.

## 12.2 Ajustes y accesibilidad
Remapeo completo de controles, slider de intensidad visual de mareo, conmutador "Reducir destellos", modo sin alcohol y selector de idioma (es/en).

## 12.3 Sistema de reporte de bugs [ESPEC]
Empaqueta un archivo local en `user://bug_reports/report_YYYYMMDD_HHMMSS.zip` con archivo `metadata.json` (versión, hardware, SO, semilla, coordenadas del avatar, estado FSM), los últimos 500 registros de `engine.log` y una captura de pantalla a 720p. Si se define un endpoint en `Settings`, se transmite mediante `HTTPRequest` en formato `multipart/form-data`.

## 12.4 Presets gráficos
- Bajo, Medio y Alto: Escalan distancias de visibilidad, sombras, partículas y resolución volumétrica.
- **DEMENCIAL:** Activa pases completos de SDFGI, reflejos SSR, oclusión SSAO/SSIL y materiales PBR de 1024 px.

---

# 13. PROGRESO, ONLINE, LOCALIZACIÓN Y ASPECTOS LEGALES

- Guardado automático al finalizar cada servicio y al cerrar la jornada.
- Soporte para 50 logros de Steam.
- Localización integral en español e inglés desde el primer archivo.
- Declaración responsable de contenidos en Steam (presencia de alcohol ficticio, tabaco en Drive-Thru y narrativa con una menor).

---

# 14. TUTORIAL DIDÁCTICO INTEGRADO

Pasos concisos de máximo 8 palabras por instrucción que requieren acción física obligatoria:
1. Prólogo interactivo: Mateo, Sofía y el aviso escolar.
2. Desplazamiento en skate hasta la gasolinera de Don Tito.
3. Montaje de la primera bici-moto con combustible mínimo.
4. Repostaje asistido con el playero (5 s).
5. Apertura del teléfono y aceptación del primer servicio.
6. Navegación guiada con minimapa temporal.
7. Cobro y evaluación de calificación.
8. Gestión del hambre en el Drive-Thru "Auto-Bite".
9. Avisos contextuales en primera ocurrencia (Alt-Look, objetos olvidados, clima, fatiga, seguro y tutoría de Honor Top).

---

# 15. PLAN DE IMPLEMENTACIÓN POR FASES (0 A 27)

El agente debe implementar el proyecto fase a fase. Si una fase excede el límite de salida, se divide en subfases informándolo claramente (ej. 4a, 4b):

- **Fase 0: Arquitectura y contratos de datos.** Clases `Resource`, jerarquía de escenas, ADRs, árbol de tests GUT, hoja de pedido de assets inicial y ADR-0001 de versiones verificadas (la escala temporal ya queda fijada en 0.3.1 [ESPEC-CORREGIDA]). Sin código de juego funcional.
- **Fase 1: Cimientos y utilidades.** Proyecto en Godot 4.7.2 (Forward+, Jolt Physics, tipado estricto), Autoloads (`EventBus`, `GameState`), `PlatformServices` (Local), localización es/en, overlay `F3` de telemetría y cámara de cabina con `Alt-Look`.
- **Fase 2: Pipeline retro y validadores.** Sombreadores CRT/VHS, bake de texturas pixel art, validadores automáticos y benchmark de draw calls.
- **Fase 3: Micro-movilidad y avatar a pie.** Skate, bicicleta, monopatín eléctrico, estamina metabólica y `OnFootController`.
- **Fase 4: Vehículos motorizados y físicas Jolt.** Raycasts de suspensión, tensor estabilizador de 2 y 3 ruedas en `_integrate_forces()`, fricción `min()` confinada a los raycasts de rueda, arrastre vectorial, suspensión orientada por gravedad, panel `F6` y clase `VehiclePhysicsTestCase` con `await physics_frame` [ESPEC-CORREGIDA].
- **Fase 5: Generación urbana del Suburbio.** Grafo vial, manzanas, iluminación nocturna, cuadrantes HLOD de 64 × 64 m y validadores de conectividad.
- **Fase 6: Apartamento (Nivel 0) y ciclo de jornada.** Interior explorable, cama, PC básica, zona de asueto activo y restricción de sueño (6.1.1).
- **Fase 7: Teléfono móvil y Mapa.** Animación de papiro, minimapa en HUD y pipeline pixel-perfect con escala entera matemática.
- **Fase 8: App "To The Top", calificación y estrés.** Radar Común, encargos, comida, multas, choques y tutorial pasos 1 a 9. **Hito A (Vertical Slice).**
- **Fase 9: Supervivencia e inventario estructurado.** Hambre, sed, fatiga, consumibles de 3 ranuras y nevera acotada a 20 slots.
- **Fase 10: IA de tráfico y peatones v1.** Grafo A*, IDM/MOBIL, semáforos y LOD de 3 niveles.
- **Fase 11: Estaciones de servicio y Drive-Thru.** Repostaje de 5 s y tienda "Auto-Bite".
- **Fase 12: Clima dinámico y confort de cabina.** Lluvia, tormentas, app Tiempo y máquina de penalizaciones de confort.
- **Fase 13: Cinemómetros, multas y seguridad vial.** Radares de velocidad, código vial y patrullas de control.
- **Fase 14: Objetos olvidados y ruleta cosmética.** Despawn estricto a 200 m, ruleta desacelerada y juguetes para Sofía.
- **Fase 15: Economía avanzada y cuota escolar.** `TuitionSystem` con retención judicial al día +14 y pólizas de seguro con deducible.
- **Fase 16: Alcohol, mareo y retenes.** Niveles de alcoholismo, degradación de manejo, efectos de presentación, retén sistémico sin combate y modo sin alcohol.
- **Fase 17: Pasajeros ampliados y diálogos.** 60 perfiles, 20 arcos recurrentes y árbol de diálogo en tiempo real.
- **Fase 18: Recados a pie y Ranura 4 pasiva.** Misiones secundarias a pie, ranura de recado y física de caída al suelo.
- **Fase 19: Audio dinámico y sintonizador de radio.** Emisoras temáticas, audio de motor por capas y efectos ambientales.
- **Fase 20: Centro e Industrial, densidad total e IA v2.** Distritos mayores, eventos dinámicos e imprevistos. **Hito B (Alpha).**
- **Fase 21: Garaje comercial de 10 vehículos y PC.** Calibración cinemática completa y árbol de mejoras permanentes.
- **Fase 22: Apartamento evolutivo completo.** Los 5 niveles de vivienda y sus condiciones de desbloqueo.
- **Fase 23: El Imperio y app Flota.** Garajes, choferes contratados, `AbstractTripSim`, rivalidad en vivo con "VíaDirecta" y sala de operaciones.
- **Fase 24: Campaña completa, Honor Top y Convoy Presidencial.** Misiones de historia, vuelta a cero estricta con 20% retenido, convoy final con guiado verbal seguro (`distance_squared_to`). **Hito C (Beta).**
- **Fase 25: Integración online y Reto Diario.** Adaptador Steam, tablas de clasificación y semilla inmutable (`hash() & 0x7FFFFFFF`).
- **Fase 26: Menús, ajustes, reporte de bugs y preset DEMENCIAL.** Formulario de reporte empaquetado en ZIP y configuración gráfica extrema.
- **Fase 27: Tutorial completo, QA final y exportación Windows.** Batería completa de pruebas GUT y exportación binaria en Godot 4.7.2.

---

# 16. FORMATO OBLIGATORIO DE CADA RESPUESTA DE FASE

Cada respuesta del agente para una fase debe estructurarse estrictamente bajo esta plantilla:
1. **Resumen de objetivos:** Máximo 5 líneas describiendo lo implementado.
2. **ADRs y supuestos:** Decisiones de diseño y supuestos técnicos etiquetados `[SUPUESTO]`.
3. **Árbol de archivos:** Rutas exactas en `res://` con la responsabilidad de cada script o escena.
4. **Código fuente completo:** Código GDScript con tipado estricto, sin elipsis (`...`), sin `# TODO` y sin código placeholder.
5. **Pasos manuales en el editor:** Lista numerada con configuraciones requeridas en el inspector o ajustes del proyecto.
6. **Guía de ejecución y pruebas en Windows:** Comando exacto de GUT y **salida literal de la consola**.
7. **Mediciones de rendimiento:** Métricas de FPS, draw calls y memoria comparadas con la fase previa.
8. **Tabla de auditoría de requisitos:**
   | Requisito de la fase | Script / Escena | Prueba GUT asociada | Salida obtenida | Estado (Implementado / Parcial / No verificado) |
   |---|---|---|---|---|
9. **Registro de STUBs y PLACEHOLDERs:** Lista de elementos pendientes con su fase de resolución.
10. **Solicitud de aprobación:** *"¿Aprobado para avanzar a la siguiente fase?"*

---

# ANEXO A: TRAZABILIDAD DE FUNCIONES APROBADAS

| ID | Función Aprobada | Sección | Fase |
|---|---|---|---|
| A1 | Cuota escolar periódica de Sofía | 6.8 | 15 |
| A2 | Cerveza, mareo psicomotriz, retenes sistémicos y Modo sin alcohol | 6.7 | 16 |
| A3 | Pasajeros con arcos narrativos (60 perfiles, 20 arcos) y diálogos en ruta | 7.3 | 17 |
| A4 | El Imperio (Tycoon de flota), app Flota y rivalidad con VíaDirecta | 10.5 | 23 |
| A5 | Apartamento evolutivo en 5 niveles y zona de asueto activo | 10.3 y 6.1.1 | 6 y 22 |
| A6 | Recados y entregas a pie en primera persona | 5.2 | 18 |
| A7 | Mutadores y perks de run rogue-lite | 7.4 | 24 |
| A8 | Reto diario con semilla fija determinista | 10.6 | 25 |
| A9 | Pólizas de seguro vehicular con deducible | 6.9 | 15 |

---

# ANEXO B: REGISTRO DE CAMBIOS Y EVOLUCIÓN ARQUITECTÓNICA

## De v5.5 a v6 (Consolidación Definitiva Anti-Vibe Coding)
- **Eliminación total de ambigüedades bloqueantes:** Se elimina la matriz de Honor Top de las dudas de Fase 0; la vuelta a cero estricta con retención del 20% queda sellada como contrato inmutable.
- **Flota comercial acotada:** Se define el garaje del jugador con exactamente 10 plazas comerciales (Vehículos 1 al 10); el Vehículo 11 (Convoy Presidencial) queda tipificado como vehículo especial de misión que se desbloquea en el garaje de casa como trofeo de conducción libre.
- **Física de Jolt rigurosa:** Incorporación obligatoria del cálculo del par estabilizador en `_integrate_forces()` multiplicado por el inverso del tensor de inercia para vehículos de 2 y 3 ruedas, prohibiendo hacks de rotación cinemática.
- **Sobreescritura de fricción en Jolt:** Sustitución de la media geométrica por `min(friccion_neumatico, friccion_superficie)` para garantizar pérdida real de tracción sobre agua y nieve.
- **Escalado entero de UI de teléfono:** Implementación matemática por código con `floor(viewport_height / 320)` para erradicar el parpadeo de píxeles en 2D.
- **Optimización de triggers narrativos en Convoy:** Sustitución de `ShapeCast3D` por evaluaciones escalares de distancia al cuadrado (`distance_squared_to`) contra coordenadas estáticas para evitar sobrecarga del broadphase de Jolt a altas velocidades.
- **Ciclo de descanso y asueto activo:** Creación de la zona de asueto en el apartamento que quema nutrientes a $5\times$ para generar fatiga y desbloquear el sueño si el jugador no desea trabajar, evitando soft-locks fisiológicos.
- **Nevera delimitada:** Asignación estricta de 20 slots a la nevera doméstica, requiriendo abastecimiento comercial activo.
- **Semilla criptográfica del reto diario:** Algoritmo unificado `("top_challenge_" + date_str).hash() & 0x7FFFFFFF` garantizando determinismo absoluto entre plataformas.
- **Protocolo de reporte de bugs:** Empaquetado formal en formato ZIP con metadatos JSON, logs y captura WebP a 720p.

---

# ANEXO C: TRAZABILIDAD DE LA ESPECIFICACIÓN TÉCNICA FORMAL

| Requisito Técnico Formal | Destino en v7 | Efecto sobre versiones anteriores |
|---|---|---|
| Clase base headless `VehiclePhysicsTestCase` | Sección 2.1 | Permite pruebas unitarias de `RigidBody3D` en CLI sin servidor de display |
| Despawn estricto de Loot Box a 200 m o nuevo viaje | Sección 3.3.1 | Erradica persistencia infinita de objetos en memoria |
| Objetos especiales para Sofía | Sección 3.3.2 | Incorpora dilema moral (dinero vs. afecto familiar) y logros de Steam |
| Cuadrantes HLOD de 64 × 64 m con MultiMesh | Sección 3.5 | Mantiene draw calls por debajo de 1.200 en GTX 1060 |
| Estabilización física de chasis en Jolt | Sección 4.1 | Evita colapso gravitacional de motos y motocarros sin falsear la cinemática |
| Override de fricción condicional | Sección 4.1 | Elimina el agarre excesivo de la media geométrica de Jolt en lluvia |
| Consumo energético por trabajo mecánico | Sección 4.5 | Reemplaza temporizadores artificiales por integración física real |
| Inmovilización de monopatín a 0 kWh | Sección 4.5 | Fuerza rescate por grúa o desplazamiento peatonal |
| Pipeline pixel-perfect de teléfono | Sección 5.6 | Escalamiento entero matemático sin distorsión bilineal |
| Zona de asueto anti-estancamiento | Sección 6.1.1 | Evita soft-locks fisiológicos si el jugador no sale a trabajar |
| Límite de 20 slots en nevera | Sección 6.5 | Destruye el exploit de comida gratuita e infinita |
| Retención coercitiva del 30% en colegiatura | Sección 6.8 | Implementa consecuencias económicas graves sin provocar game over |
| Anti-ShapeCast en convoy final | Sección 10.1.1 | Reemplaza colisionadores dinámicos por `distance_squared_to` |
| Vuelta estricta a cero en Honor Top | Sección 10.2 | Sella la pérdida de vehículos comerciales y recompra de ciudades |
| Semilla determinista de 31 bits | Sección 10.6 | Sincroniza la competencia online en Steam Leaderboards |
| Paquete de diagnóstico de bugs en ZIP | Sección 12.3 | Estandariza la captura de telemetría y logs para QA |

---

# ANEXO D: REGISTRO v6 → v7 (AUDITORÍA FÍSICA Y SISTÉMICA) [ESPEC-CORREGIDA]

Base: v6 íntegra (secciones 0 a 16 y anexos A a C preservados). Solo se modificaron los puntos listados.

| # | Sección | Problema en v6 | Corrección v7 |
|---|---|---|---|
| D1 | 0.1, 2 | Versiones de Godot / GUT / Jolt asumidas; mención a un modelo concreto. | Versión verificada en Fase 0 (ADR-0001). Jolt es el integrado en Godot (sin versión externa). |
| D2 | 0.3 | Sin escala temporal unitaria. | `1 s real = 1 min de juego`; unidades SI. |
| D3 | 2.1 | `PhysicsServer3D.space_step` no despacha `_integrate_forces`. | `await get_tree().physics_frame`; `extends GutTest`. |
| D4 | 3.2 | "Amortiguación inercial suave". | `slerp(q, 1 - exp(-Δt·15))` (≈97.6 % en 0.25 s, coherente con `RETURNING`). |
| D5 | 4.1 | Tensor de inercia tratado como escalar. | `Basis` simétrico en espacio global; sin doble rotación. |
| D6 | 4.1 | Override global de fricción de Jolt (inviable). | `min(μ)` solo en fuerzas de raycast de rueda; chasis con fricción Jolt estándar. |
| D7 | 4.1 | $F_d = \tfrac12\rho C_d A v^2$ escalar sin dirección. | $\vec F_d = -\tfrac12\rho C_d A|\vec v|\vec v$. |
| D8 | 4.1 | Raycasts fijos a -Y local ante zonas de gravedad alterada. | Dirección = `get_total_gravity().normalized()` con fallback a `-basis.y` si g ≈ 0. |
| D9 | 4.5 | Consumo "por segundo" sin anclaje al paso físico. | Integración explícita por `Δt` físico. |
| D10 | 5.6 | `stretch` y escalado no especificados. | `stretch = false`, `container.scale` entero, filtro Nearest (no `rect_scale`). |
| D11 | 6.5 | "Física de caída con shader de destello". | Impulso `(-cam.z·1.5 + UP).normalized()·2.5 N·s`; `EMISSION` con `sin(TIME·5)`. |
| D12 | 6.6 | `daño_normalizado` sin derivación. | Desde `get_contact_impulse`, clamp [0,1], umbral Δv = 10 m/s. |
| D13 | 6.7 | "Ruido de baja frecuencia". | `FastNoiseLite` Simplex 1.2 Hz × 0.25 rad × nivel, tiempo de simulación. |
| D14 | 6.8 | Retención del 30 % con ingreso nulo. | Solo si ingreso bruto > 0; nunca deja deuda negativa. |
| D15 | 8.3 | "Simulación estadística de flujo". | LOD1 `Curve3D` cinemático + MultiMesh; LOD2 arreglos empaquetados en `WorkerThreadPool`, sin acceso al SceneTree. |
| D16 | 9 | Trueno sin fórmula. | `Δt = d / 343.0` con timer físico. |
| D17 | 10.6 | Descrito como "criptográfico"; supuesta race condition de huso horario. | La API con `utc=true` ya es UTC (no había race). Se explicita el diccionario UTC; `hash()` declarado no criptográfico (SHA-256 opcional). |
| D18 | 2 | Sin salvaguarda de precisión float32. | `WORLD_HALF_EXTENT = 8000 m`; origin shifting si se excede. |
| D19 | Anexos A/C | Referencias a secciones inexistentes (7.5, 7.7, 4.5.2, 10.1.1.2). | Corregidas a 7.3, 7.4, 4.5, 10.1.1. |
| D20 | Todo | Fórmulas LaTeX corruptas (`\t`, `\f`, `\a`, `\r` convertidos en caracteres de control). | Restauradas. |
