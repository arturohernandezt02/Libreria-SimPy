# Ejemplo 3: Abandono de Filas en el CINE (Movie Renege) 🍿🎬

Este ejemplo muestra un caso de simulación más avanzado. Modela un cine con una sola taquilla vendiendo boletos para tres películas distintas.

## 🎯 Conceptos que abarca
* **Recursos compartidos (`simpy.Resource`):** Representa la taquilla con capacidad limitada.
* **Eventos condicionales y compartidos:** Usar el operador OR (`|`) para esperar a que ocurra *el primer evento* entre dos opciones (obtener el turno en la fila **O** que la película se agote).
* **Abandono de fila (*Reneging*):** Clientes que abandonan la cola de espera cuando se activa un evento de "agotado" (*sold out*).

---

## 📋 Escenario

El cine vende boletos para 3 películas. Los clientes llegan a tiempos aleatorios e intentan comprar entre 1 y 6 boletos. 

* Si una película **se agota**, se activa un evento que hace que **todas las personas en la fila** esperando para esa película la abandonen inmediatamente.
* Si el cliente llega a la taquilla pero no hay suficientes boletos para su grupo, discute brevemente con el cajero y se retira.
* Si quedan menos de 2 boletos tras una compra, la película se declara oficialmente **agotada**.

---

## 💻 Código de la Simulación

```python
from __future__ import annotations
import random
from typing import Dict, List, NamedTuple, Optional
import simpy

# Parámetros de la simulación
RANDOM_SEED = 42
TICKETS = 50             # Boletos disponibles por película
SELLOUT_THRESHOLD = 2    # Menos de estos boletos activa el "sold out"
SIM_TIME = 120           # Tiempo total a simular (minutos)


def moviegoer(env, movie, num_tickets, theater):
    """Proceso que representa a un espectador intentando comprar boletos.
    
    Espera su turno en la taquilla O a que la película se agote.
    Si se agota antes de su turno, abandona la fila.
    """
    with theater.counter.request() as my_turn:
        # Espera hasta que sea su turno O hasta que la película se agote
        result = yield my_turn | theater.sold_out[movie]

        # Verificar si la película se agotó antes de ser atendido
        if my_turn not in result:
            theater.num_renegers[movie] += 1
            return

        # Verificar si quedan suficientes boletos
        if theater.available[movie] < num_tickets:
            # El cliente se va molesto tras discutir con el cajero
            yield env.timeout(0.5)
            return

        # Compra exitosa de boletos
        theater.available[movie] -= num_tickets
        
        # Si quedan menos boletos que el umbral, se declara agotada
        if theater.available[movie] < SELLOUT_THRESHOLD:
            # Activa el evento "sold out" para esta película
            theater.sold_out[movie].succeed()
            theater.when_sold_out[movie] = env.now
            theater.available[movie] = 0
            
        yield env.timeout(1)


def customer_arrivals(env, theater):
    """Genera la llegada continua de clientes a la taquilla."""
    while True:
        # Tiempo aleatorio entre llegadas (distribución exponencial)
        yield env.timeout(random.expovariate(1 / 0.5))

        movie = random.choice(theater.movies)
        num_tickets = random.randint(1, 6)
        
        if theater.available[movie]:
            env.process(moviegoer(env, movie, num_tickets, theater))


class Theater(NamedTuple):
    """Estructura para almacenar los datos centrales del cine."""
    counter: simpy.Resource
    movies: List[str]
    available: Dict[str, int]
    sold_out: Dict[str, simpy.Event]
    when_sold_out: Dict[str, Optional[float]]
    num_renegers: Dict[str, int]


# --- Configuración e inicio de la simulación ---
print('--- Simulación: Abandono en el Cine (Movie Renege) ---')
random.seed(RANDOM_SEED)
env = simpy.Environment()

# Crear el cine y sus películas
movies = ['Python Unchained', 'Kill Process', 'Pulp Implementation']
theater = Theater(
    counter=simpy.Resource(env, capacity=1),
    movies=movies,
    available=dict.fromkeys(movies, TICKETS),
    sold_out={movie: env.event() for movie in movies},
    when_sold_out=dict.fromkeys(movies),
    num_renegers=dict.fromkeys(movies, 0),
)

# Iniciar proceso de llegadas y correr la simulación
env.process(customer_arrivals(env, theater))
env.run(until=SIM_TIME)

# --- Análisis de Resultados ---
for movie in movies:
    if theater.sold_out[movie]:
        sellout_time = theater.when_sold_out[movie]
        num_renegers = theater.num_renegers[movie]
        print(
            f'Película "{movie}" AGOTADA {sellout_time:.1f} minutos '
            f'después de abrir la taquilla.'
        )
        print(f'  Personas que abandonaron la fila al agotarse: {num_renegers}')
```
---
## Salida Esperada (Output)
```
--- Simulación: Abandono en el Cine (Movie Renege) ---
Película "Python Unchained" AGOTADA 38.0 minutos después de abrir la taquilla.
  Personas que abandonaron la fila al agotarse: 16
Película "Kill Process" AGOTADA 43.0 minutos después de abrir la taquilla.
  Personas que abandonaron la fila al agotarse: 5
Película "Pulp Implementation" AGOTADA 28.0 minutos después de abrir la taquilla.
  Personas que abandonaron la fila al agotarse: 5
```
