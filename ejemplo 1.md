# Ejemplo 1: Un Ejemplo Simple (Reloj) ⏰

Uno de los objetivos principales de **SimPy** es ser fácil de usar. A continuación, se presenta un ejemplo de una simulación sencilla: un proceso de reloj que imprime el tiempo actual de simulación a intervalos regulares. Este es el punto de partida perfecto para entender cómo funcionan los **procesos** y la **pausa de tiempo** en SimPy.

---

## 🎯 Conceptos que abarca

* **Procesos basados en generadores:** Funciones que usan `yield` para pausarse y reanudarse
* **El entorno de simulación:** El motor central que controla el tiempo
* **`env.timeout()`:** La forma de hacer que un proceso "espere" en tiempo simulado
* **Múltiples procesos independientes:** Cómo dos relojes pueden avanzar a velocidades distintas

---

## Código del Ejemplo

```python
import simpy

# Definición del proceso del reloj
def clock(env, name, tick):
    while True:
        print(name, env.now)
        yield env.timeout(tick)

# Crear el entorno de simulación
env = simpy.Environment()

# Crear y registrar los procesos en el entorno
env.process(clock(env, 'fast', 0.5))
env.process(clock(env, 'slow', 1))

# Correr la simulación hasta que el tiempo sea 2
env.run(until=2)
```

---

## 📖 Explicación del Código

### La función `clock(env, name, tick)`

Esta es la **función generadora** que define nuestro proceso:

1. **`while True:`** — El proceso se repite indefinidamente. En SimPy, los procesos son típicamente bucles infinitos que se "despiertan" según eventos.

2. **`print(name, env.now)`** — Imprime el nombre del reloj y el tiempo actual de la simulación (`env.now`). Es importante: `env.now` **no es el tiempo real**, es el tiempo simulado.

3. **`yield env.timeout(tick)`** — Esta es la línea clave. `yield` le dice a SimPy: *"pausa este proceso ahora, y despiértalo después de `tick` unidades de tiempo simulado"*. Cuando se ejecuta `env.run()`, SimPy gestiona automáticamente estas pausas, permitiendo que otros procesos continúen mientras tanto.

### La configuración e inicio

- **`env = simpy.Environment()`** — Crea el entorno de simulación, que es el motor central. Desde aquí, SimPy controla el tiempo simulado (empieza en 0) y coordina todos los procesos.

- **`env.process(clock(env, 'fast', 0.5))`** — Registra el primer reloj. Se llamará `'fast'` y despertará cada 0.5 unidades de tiempo.

- **`env.process(clock(env, 'slow', 1))`** — Registra el segundo reloj. Se llamará `'slow'` y despertará cada 1 unidad de tiempo.

- **`env.run(until=2)`** — Inicia la simulación y la corre hasta que el tiempo simulado llegue a 2. En este punto, SimPy detiene todo.

---

## Salida Esperada (Output)

```
fast 0
slow 0
fast 0.5
slow 1
fast 1.0
fast 1.5
```

### Por qué se ve así

Ambos relojes inician en tiempo 0 y se despiertan inmediatamente. Luego:
- A tiempo 0.5, **fast** despierta (intervalo 0.5)
- A tiempo 1, **fast** despierta de nuevo Y **slow** despierta por primera vez (intervalo 1)
- A tiempo 1.5, **fast** despierta nuevamente
- A tiempo 2, la simulación termina (sin imprimir)

**Observa cómo SimPy alterna automáticamente entre procesos** según cuándo se despiertan. Esto es la esencia de la simulación de eventos discretos.

---

## 💡 Qué Aprendemos

- **Los procesos no son threads:** SimPy no usa paralelismo real; todo ocurre en un solo hilo mediante el uso de generadores.
- **`yield` es el puente:** Es la forma en que un proceso le comunica a SimPy cuándo debe pausa y reanudación.
- **`env.now` es el reloj simulado:** Siempre puedes preguntar "¿qué hora es?" en la simulación.
- **La ejecución es determinista:** Dado el mismo código y la misma semilla aleatoria, la simulación siempre produce el mismo resultado.

---

## 🔗 Siguiente Paso

Ahora que entiendes cómo funcionan los procesos básicos, el siguiente ejemplo ([Ejemplo 2: El Auto](ejemplo%202.md)) te mostrará cómo un proceso puede cambiar entre múltiples estados.
