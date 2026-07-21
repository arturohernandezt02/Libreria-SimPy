# Ejemplo 1: Un Ejemplo Simple (Reloj)

Uno de los objetivos principales de **SimPy** es ser fácil de usar. A continuación, se presenta un ejemplo de una simulación sencilla: un proceso de reloj que imprime el tiempo actual de simulación en cada paso.

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

## Salida Esperada (Output)
```
fast 0
slow 0
fast 0.5
slow 1
fast 1.0
fast 1.5
```
