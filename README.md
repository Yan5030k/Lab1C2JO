# Laboratorio 1 Segundo Cómputo – Semana 9

**Integrantes:**
- Jhoan Mauricio Ortega Ventura
- Jeremías Neftaly Fuentes Méndez

---

## 1. Situación Problemática Real

**Enunciado del Problema:**
En los aeropuertos, el chequeo de equipaje especial o sobredimensionado (instrumentos, tablas de surf, mascotas) retrasa las filas principales. El personal debe calcular tarifas manualmente y comunicarse por radio con la pista, lo que genera desorganización y cuellos de botella.

**Sectores Enfocados:**
Operaciones Aeroportuarias, Aerolíneas y Servicio al Cliente.

**Solución del Programa:**
Creamos un "Módulo Rápido de Registro de Equipaje Especial (OOG)". Sus funciones principales son:
1. **Registro Rápido:** Formulario para capturar pasajero, vuelo, tipo de equipaje y peso.
2. **Cálculo de Tarifas:** Calcula automáticamente el cobro extra según reglas de peso excedente.
3. **Control de Capacidad:** Bloquea el registro si se supera el límite físico del avión (ej. máximo 3 mascotas).
4. **Tracking de Estado:** Permite cambiar el estado del equipaje ("En Mostrador", "En Bodega", "Cargado") con indicadores visuales.
5. **Buscador en Tiempo Real:** Filtra la lista de bodega por número de vuelo para rápida consulta.

---

## 7. Respuestas a las Preguntas de Reflexión

### Explique con sus propias palabras qué es Vue.js y cuál es su función dentro de la página web.
Vue.js es un framework progresivo de JavaScript para construir interfaces de usuario. Su función es gestionar la lógica y la reactividad de la página: permite que cuando los datos cambian (como al registrar un equipaje o buscar un vuelo), la vista HTML se actualice automáticamente sin tener que recargar la página.

### Describa qué variables reactivas utilizó y su función.
1. **`pasajeroNombre` (String):** Captura el nombre ingresado en el formulario.
2. **`numeroVuelo` (String):** Captura el código del vuelo.
3. **`tipoEquipaje` (String):** Almacena la selección del tipo de artículo.
4. **`pesoEquipaje` (Number):** Almacena el peso para calcular tarifas y validar.
5. **`listaEquipajes` (Array):** Almacena todos los registros exitosos para renderizar la tabla.
6. **`errores` (Array):** Almacena mensajes de alerta si el usuario se equivoca.
7. **`filtroVuelo` (String):** Captura el texto de la barra de búsqueda para filtrar la tabla en tiempo real.

### Diferencia entre `v-bind` y `v-model`.
- **`v-bind` (`:`):** Es de una sola dirección (del código a la vista). Lo usamos en `:class="obtenerClaseEstado(equipaje.estado)"` para cambiar dinámicamente el color de la etiqueta según su estado (amarillo, azul o verde).
- **`v-model`:** Es de doble dirección. Lo usamos en los `input` (ej. `v-model="numeroVuelo"`) para capturar lo que el usuario escribe y actualizar la variable en tiempo real.

### Ejemplo de evento utilizado.
Usamos `@submit.prevent="registrarEquipaje"` en el formulario. Intercepta el evento de envío, evita que la página se recargue (con `.prevent`) y ejecuta nuestra función de registro. También usamos `@click="cambiarEstado(equipaje)"` en los botones de la tabla.

### ¿Para qué utilizó la directiva `v-for`?
Para renderizar la tabla dinámicamente. Usamos `<tr v-for="equipaje in equipajesFiltrados" :key="equipaje.id">`. Iteramos sobre la propiedad computada `equipajesFiltrados` para que, por cada objeto en la lista, Vue genere automáticamente una fila HTML con sus datos.

### Situación en la que utilizó `v-if` y problema que resuelve.
Lo usamos en `<div v-if="errores.length > 0" class="errores">`. Esto resuelve el problema de la interfaz limpia: el panel rojo de errores solo existe y se muestra en la pantalla si realmente hay errores de validación; de lo contrario, se oculta completamente.

### ¿Cómo se realiza la validación y por qué es importante?
La validación se hace en la función `registrarEquipaje()` antes de guardar. Verificamos mediante condicionales `if` que los campos no estén vacíos, que el peso sea mayor a cero, y que no se superen las 3 mascotas por vuelo. Si algo falla, el error se empuja al array `errores` y el proceso se detiene. 
Es vital para asegurar la integridad de los datos, evitar que el sistema falle al procesar valores nulos y darle feedback claro al usuario para que corrija su entrada.