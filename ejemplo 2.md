# Ejemplo 2: Nuestro Primer Proceso (El Auto) 🚗

En este segundo ejemplo veremos cómo modelar el comportamiento de un objeto con **múltiples estados**. 

Modelaremos un auto que alterna continuamente entre dos acciones: **estacionar** durante 5 unidades de tiempo y **conducir** durante 2 unidades de tiempo. Cada vez que inicia una acción, imprimimos un mensaje mostrando en qué momento ocurre. Este ejemplo es el puente entre la simplicidad del reloj y casos más complejos.

---

## 🎯 Conceptos que abarca

* **Máquinas de estados simples:** Un proceso que puede estar en diferentes "modos" (estacionando vs conduciendo)
* **Transiciones entre estados:** Cómo cambiar de un estado a otro usando `yield`
* **Timers de duración variable:** Diferentes duraciones para cada estado
* **Trazabilidad de eventos:** Usar `print()` para rastrear cuándo ocurren las transiciones

---

## Código del Ejemplo

```python
import simpy

# Definición del proceso del auto
def car(env):
    while True:
        print('Comienza a estacionar en el tiempo %d' % env.now)
        parking_duration = 5
        # El proceso se pausa durante el tiempo de estacionado
        yield env.timeout(parking_duration)

        print('Comienza a conducir en el tiempo %d' % env.now)
        trip_duration = 2
        # El proceso se pausa durante el tiempo de viaje
        yield env.timeout(trip_duration)

# 1. Crear el entorno de simulación
env = simpy.Environment()

# 2. Registrar el proceso del auto en el entorno
env.process(car(env))

# 3. Correr la simulación durante 15 unidades de tiempo
env.run(until=15)
```

---

## 📖 Explicación del Código

### La función `car(env)`

Este proceso modelar el ciclo de vida repetido de un auto:

1. **`while True:`** — El auto entra en un ciclo infinito de estacionamiento y conducción. En la práctica, los procesos reales terminarían cuando se cumpla alguna condición, pero para este ejemplo, el auto sigue andando.

2. **Primera sección (estacionamiento):**
   - `print('Comienza a estacionar...')` — Se registra el evento en el tiempo actual
   - `parking_duration = 5` — Definimos cuánto tiempo estará estacionado
   - `yield env.timeout(parking_duration)` — **Pausa el proceso 5 unidades de tiempo simulado**. Durante este tiempo, SimPy puede ejecutar otros procesos.

3. **Segunda sección (conducción):**
   - Similar al estacionamiento, pero con una duración de 2 unidades
   - Después de `yield env.timeout(2)`, el proceso vuelve al inicio del `while` y el ciclo se repite

### Configuración e inicio

- **`env = simpy.Environment()`** — Crea el entorno (igual que en el Ejemplo 1)
- **`env.process(car(env))`** — Registra **un solo proceso** (el auto) en el entorno
- **`env.run(until=15)`** — Corre la simulación 15 unidades de tiempo

---

## Salida Esperada (Output)

```
Comienza a estacionar en el tiempo 0
Comienza a conducir en el tiempo 5
Comienza a estacionar en el tiempo 7
Comienza a conducir en el tiempo 12
Comienza a estacionar en el tiempo 14
```

### Desglose de la Ejecución

Veamos paso a paso qué ocurre en la simulación:

| Tiempo | Evento | Duración | Próximo Evento |
|--------|--------|----------|----------------|
| **0**  | Comienza a estacionar | 5 unidades | Despierta en t=5 |
| **5**  | Comienza a conducir | 2 unidades | Despierta en t=7 |
| **7**  | Comienza a estacionar | 5 unidades | Despierta en t=12 |
| **12** | Comienza a conducir | 2 unidades | Despierta en t=14 |
| **14** | Comienza a estacionar | 5 unidades | Despierta en t=19 (no se muestra, simulación termina en t=15) |
| **15** | — | — | **Simulación termina** |

La última línea no incluye "Comienza a conducir" porque la simulación termina antes de que el auto despierte de su estacionamiento en tiempo 14.

---

## 💡 Qué Aprendemos

- **Los procesos son máquinas de estado:** Un mismo proceso puede estar en diferentes "modos" (estados), cambiando entre ellos con `yield`.

- **Las duraciones pueden variar:** Cada `yield env.timeout()` puede tener una duración diferente. Esto es útil para modelar procesos con fases o etapas.

- **El tiempo simulado avanza:** Cuando un proceso hace `yield`, su reloj de tiempo se adelanta. Después de `yield env.timeout(5)` en tiempo 0, el tiempo actual es 5.

- **Un proceso no bloquea a otros:** Si tuviéramos múltiples autos (procesos), mientras uno está pausado en `yield`, SimPy puede ejecutar las acciones de otros.

- **Simplicidad escalable:** Este patrón (pausa, evento, pausa, evento, ...) es la base de simulaciones mucho más complejas con decenas o miles de entidades.

---

## 🔗 Siguiente Paso

Ahora que entiendes cómo un proceso puede tener múltiples estados, el siguiente ejemplo ([Ejemplo 3: Abandono en el Cine](ejemplo%203.md)) introduce conceptos avanzados como **recursos compartidos**, **eventos condicionales** y **abandono de filas**, que son cruciales para modelar sistemas más realistas.
