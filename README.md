# Laboratorio 1 Segundo Cómputo – Semana 9

**Integrantes:**
- Jhoan Mauricio Ortega Ventura
- Jeremías Neftaly Fuentes Méndez

---

## 1. Situación Problemática Real

### Enunciado del Problema

En los aeropuertos modernos, el manejo de equipaje especial o sobredimensionado (como instrumentos musicales, equipos deportivos, cajas frágiles o mascotas) suele generar cuellos de botella en los mostradores principales de chequeo. Los pasajeros a menudo llegan sin saber si sus artículos serán aceptados, cuáles son los requisitos de embalaje o cuánto será el costo del recargo. El personal de la aerolínea en el mostrador principal debe realizar cálculos de peso, verificar políticas específicas y coordinar con el personal de carga, lo que retrasa la fila de chequeo regular. Esta falta de un proceso dedicado y rápido afecta la eficiencia operativa y la experiencia del cliente.

### Sector o Sectores Enfocados

- **Operaciones de Aeropuerto:** Coordinación del manejo de carga especial.
- **Aerolíneas:** Mejora del servicio al cliente y eficiencia operativa.
- **Servicio al Cliente:** Reducción de tiempos de espera.

### Descripción de Funciones y Solución del Programa

Nuestra solución, el "Módulo Rápido de Registro de Equipaje Especial", es un sistema web diseñado para ser utilizado por un operador de la aerolínea en un mostrador dedicado o por un pasajero en un kiosko de autoservicio. El programa resolverá el problema de la siguiente manera:

1. **Interfaz dedicada:** Proporciona un formulario web enfocado únicamente en el ingreso de datos de equipaje especial.
2. **Registro rápido de datos:** Permite ingresar el nombre del pasajero, número de vuelo, tipo de artículo especial y su peso en kg.
3. **Validación instantánea:** El sistema valida los datos ingresados al instante para garantizar que todos los campos obligatorios estén completos y que el peso sea un valor positivo y válido.
4. **Cálculo condicional de peso:** Al registrar, el sistema aplica una regla de negocio. Si el peso excede un límite (por ejemplo, 20 kg), el campo de peso en la tabla se resaltará visualmente en rojo para advertir al personal sobre un artículo pesado.
5. **Visualización de la Lista de Aprobación de Bodega:** La interfaz muestra una tabla con todos los registros exitosos. El personal de carga del aeropuerto puede consultar esta lista para saber exactamente qué artículos especiales y delicados se deben cargar en el avión para un vuelo determinado.
6. **Manipulación de la Lista:** El operador tiene la capacidad de eliminar registros individuales si es necesario.

---

## 7. Respuestas a las Preguntas de Reflexión

### Explique con sus propias palabras qué es Vue.js y cuál es su función dentro de la página web desarrollada.

Vue.js es un framework progresivo de JavaScript utilizado para construir interfaces de usuario. Su función en nuestra página web desarrollada es actuar como el "cerebro" que gestiona la lógica de la interfaz y la presentación de los datos. Vue nos permite crear una aplicación reactiva, lo que significa que cuando los datos en nuestro modelo de JavaScript cambian (por ejemplo, al agregar un nuevo equipaje), la vista web se actualiza automáticamente sin necesidad de recargar la página manualmente. Esto facilita enormemente el desarrollo de aplicaciones web complejas y dinámicas.

### Describa qué variables reactivas utilizó en su aplicación y cuál es la función de cada una dentro del sistema.

Utilizamos las siguientes variables reactivas principales dentro de nuestro componente `ListaEquipajeEspecial.vue`:

1. **`pasajeroNombre` (String):** Almacena el nombre ingresado en el input del formulario, sincronizado mediante `v-model`. Su función es capturar el nombre del pasajero.
2. **`numeroVuelo` (String):** Almacena el número de vuelo ingresado, también sincronizado con `v-model`. Se usa para identificar el vuelo asociado.
3. **`tipoEquipaje` (String):** Almacena la selección realizada en el elemento `<select>`. Identifica qué tipo de artículo especial se está registrando.
4. **`pesoEquipaje` (Number):** Almacena el valor numérico del peso ingresado. Se utiliza para la validación y para el cálculo de estilos dinámicos.
5. **`listaEquipajes` (Array of Objects):** Es la variable reactiva más importante. Almacena todos los registros de equipaje especial que se han agregado exitosamente. Cuando este array cambia (por ejemplo, al pulsar "Registrar Equipaje" o "Eliminar"), Vue vuelve a renderizar automáticamente la tabla para reflejar el estado actual de la lista.
6. **`errores` (Array of Strings):** Almacena los mensajes de error de validación generados. Su función es permitir que el componente renderice condicionalmente una sección de alertas si el formulario se envía incompleto o con datos inválidos.

### Explique la diferencia entre las siguientes directivas utilizadas en su proyecto: `v-bind` y `v-model`.

- **`v-bind` (`:`):** Es una directiva utilizada para vincular reactivamente un atributo de un elemento HTML a un valor en el modelo de datos de Vue. Es un enlace de una sola dirección (*one-way data binding*), del modelo de datos a la vista. Por ejemplo, en nuestra aplicación usamos `:class="{ 'peso-alto': pesoEsHeavy(equipaje.peso) }"` para vincular dinámicamente la clase CSS a una celda de la tabla. El atributo `class` cambia si el resultado de la función `pesoEsHeavy()` cambia. No sirve para capturar datos de inputs del usuario.
- **`v-model`:** Es una directiva de enlace de datos de doble dirección (*two-way data binding*). Se utiliza para vincular el valor de un elemento de formulario (como `<input>`, `<select>` o `<textarea>`) con una variable en el modelo de datos de Vue. Permite capturar lo que el usuario escribe y actualizar el valor de la variable en el modelo, y viceversa. Por ejemplo, `v-model="pasajeroNombre"` asegura que la variable `pasajeroNombre` siempre tenga el valor actual de su campo de input correspondiente. Es la directiva ideal para la manipulación de datos escritos.

### Mencione al menos un ejemplo de evento utilizado dentro de su aplicación.

Un ejemplo claro de evento es `@submit.prevent="registrarEquipaje"`, que se encuentra en la etiqueta `<form>`. Este evento escucha el envío del formulario. El modificador `.prevent` detiene el comportamiento predeterminado del navegador, que es recargar la página, y en su lugar llama al método `registrarEquipaje()` definido en nuestro componente de Vue. Otro ejemplo es `@click="eliminarRegistro(equipaje.id)"` en el botón de eliminar de cada fila de la tabla.

### Explique para qué utilizó la directiva `v-for` dentro de su aplicación.

Utilizamos la directiva `v-for` para renderizar dinámicamente la tabla de registros a partir del array reactivo `listaEquipajes`. El código `<tr v-for="(equipaje, index) in listaEquipajes" :key="equipaje.id">` itera a través de cada objeto en el array. Por cada objeto, Vue crea una nueva fila de tabla (`<tr>`) en el DOM, insertando los valores correspondientes de las propiedades del objeto (pasajero, vuelo, tipo y peso) en las celdas de la tabla. Esto nos permite tener una tabla que crece o decrece automáticamente a medida que el personal agrega o elimina registros de equipaje, sin tener que manipular el DOM de forma manual. El uso de `:key` es crucial para que Vue itere eficientemente.

### Describa en qué situación utilizó `v-if` y qué problema resuelve dentro de su interfaz.

Utilizamos la directiva `v-if` en dos situaciones clave:

1. **Mostrar errores de validación:** `<div v-if="errores.length > 0" class="errores">`. Este bloque de código HTML se inserta en el DOM solo si la longitud del array `errores` es mayor que cero. Resuelve el problema de mostrar alertas condicionales y claras al usuario sobre por qué su formulario no fue aceptado, sin tener una sección de error siempre visible y vacía.
2. **Tabla vacía:** `<p v-if="listaEquipajes.length === 0">`. Muestra un mensaje amigable si no hay registros. En el bloque `v-else` se encuentra la tabla. Esto mejora la experiencia del usuario al proporcionarle feedback claro sobre el estado de la lista.

### Explique cómo se realiza la validación de datos en su aplicación y por qué es importante validar la información ingresada por el usuario.

La validación de datos se realiza dentro del método `registrarEquipaje()` de nuestro componente. Antes de crear el nuevo objeto y agregarlo a `listaEquipajes`, el código realiza una serie de chequeos manuales. Por ejemplo, verificamos si `this.pasajeroNombre.trim()` no está vacío, o si `this.pesoEquipaje` es un número mayor que 0. Si alguna de estas condiciones falla, agregamos un mensaje de error claro al array reactivo `errores`. Al final del método, si el array de errores no está vacío, no se realiza el registro.

La validación es importante por varias razones:

- **Integridad de los datos:** Previene que datos incorrectos, incompletos o inválidos ingresen al sistema. Un registro sin número de vuelo es inútil para la coordinación del aeropuerto.
- **Evitar errores lógicos:** Previene errores de programación que podrían ocurrir si se intenta procesar datos vacíos, como intentar calcular un total de peso si el peso es `null`.
- **Mejora la experiencia del usuario (UX):** Proporciona retroalimentación inmediata y constructiva al usuario, permitiéndole corregir sus errores de forma sencilla antes de continuar, evitando frustración.
- **Seguridad:** Aunque es una validación simple del lado del cliente, ayuda a filtrar posibles entradas malintencionadas.
