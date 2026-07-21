# ¿Qué es SimPy? #
**SimPy** es un framework de simulación de eventos discretos basado en procesos, construido sobre Python estándar. En SimPy, los procesos se definen mediante funciones generadoras (generators), lo que permite modelar fácilmente componentes activos del sistema como clientes, vehículos o agentes.

Además, incluye diversos tipos de recursos compartidos para simular puntos de congestión con capacidad limitada, tales como servidores, cajas de cobro o túneles.

---

## ¿Cómo funciona SimPy?

En SimPy, los elementos del sistema interactúan en un **entorno de simulación** (*Environment*). 

1. **Componentes Activos (Procesos):** Se modelan usando **funciones generadoras** de Python (`yield`). Representan entes del mundo real que realizan acciones a lo largo del tiempo (clientes, vehículos, paquetes, pacientes).
2. **Eventos:** Son los cambios de estado en el sistema (ej. llegar a una fila, iniciar una atención, completar una entrega).
3. **Pausa y Reanudación:** La sentencia `yield` de Python le dice a SimPy: *"pausa este proceso hasta que pase cierto tiempo o se cumpla una condición"*.

---
