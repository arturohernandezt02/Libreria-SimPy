# Ejemplo 2: Nuestro Primer Proceso (El Auto) 🚗

En este segundo ejemplo veremos cómo modelar el comportamiento de un objeto con **múltiples estados**. 

Modelaremos un auto que alterna continuamente entre dos acciones: **estacionar** durante 5 unidades de tiempo y **conducir** durante 2 unidades de tiempo. Cada vez que inicia una acción, imprimirá el tiempo actual de la simulación.

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

## Salida Esperada (Output)
```
Comienza a estacionar en el tiempo 0
Comienza a conducir en el tiempo 5
Comienza a estacionar en el tiempo 7
Comienza a conducir en el tiempo 12
Comienza a estacionar en el tiempo 14
```
