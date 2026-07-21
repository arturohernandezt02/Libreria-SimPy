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

## Conceptos Clave

Para entender SimPy, es fundamental conocer sus tres pilares principales:

### 1. El Entorno (`simpy.Environment`)
Es el motor central. Administra el tiempo simulado (que empieza en `0`), gestiona la cola de eventos y coordina la ejecución de todos los procesos.

### 2. Procesos y `yield`
A diferencia de un script normal que corre de principio a fin, los procesos en SimPy se pueden pausar. 
* Cuando usas `yield env.timeout(5)`, el proceso le dice al entorno: *"despiértame dentro de 5 unidades de tiempo"*.

### 3. Recursos Compartidos (`simpy.Resource`)
Simulan puntos del sistema con **capacidad limitada** donde se generan filas o tiempos de espera:
* **`Resource`:** Servidores o cajas de atención con capacidad fija (ej. 2 barberos, 3 servidores web).
* **`Container`:** Para modelar cantidades homogéneas (ej. un tanque de gasolina, inventario).
* **`Store`:** Para almacenar objetos específicos (ej. una bodega de productos).

---

## 📊 Ventajas de usar SimPy

* **Simplicidad:** No requiere librerías externas complejas; corre sobre Python puro.
* **Escalabilidad:** Permite desde simulaciones simples de una sola fila hasta redes complejas de logística o tráfico.
* **Orientado a Procesos:** Modelar entidades como funciones independientes hace que el código sea muy intuitivo y fácil de mantener.

---

## Instalación

SimPy está implementado en Python puro y no tiene dependencias. SimPy se ejecuta en Python 3 (>= 3.8). PyPy3 también es compatible. Si tienes pip instalado, solo escribe:
```bash
$ python -m pip install simpy
```
También es posible descargar e instalar SimPy manualmente:
```
$ cd where/you/put/simpy/
$ python -m build
$ python -m pip install dist/simpy-*.whl
```

Para probar si se ha instalado correctamente, ejecuta:
```
$ python -m tox
```

---

## 📚 Fuentes y Referencias

* **Documentación oficial de SimPy:** [SimPy Documentation - PyPI](https://pypi.org/project/simpy/)
* **Documentación sobre Simulación de Eventos Discretos:** [SimPy Read the Docs](https://simpy.readthedocs.io/)
