# Uso de IA

Herramienta utilizada: ChatGPT.

**Solicitudes representativas:**
- Ayuda para identificar las causas de los errores de la aplicación Pawnee, sin modificar su estructura original.
- Orientación para comprobar las correcciones con `curl`, el navegador y MongoDB.

### Decisión técnica

Utilicé la IA principalmente como apoyo para encontrar los errores y entender algunas partes del código. No quería cambiar toda la aplicación, sino corregir lo necesario y mantener lo que ya habíamos trabajado en clase.

No todas las indicaciones funcionaron a la primera. En algunos casos tuve que revisar los nombres reales de las funciones, comprobar qué estaba ejecutando y ajustar las soluciones a mi proyecto.

### A-01 · Registro de avistamientos

El primer problema estaba en el formulario. Al revisar los datos que se enviaban, encontré que al crear un avistamiento se utilizaba `criaturaId`, mientras que el backend esperaba `criatura`.

Con ayuda de ChatGPT revisé esa diferencia y cambié el nombre del campo. Después probé creando un avistamiento y comprobé que se guardaba correctamente.

Entendí que el frontend y el backend tienen que manejar los mismos nombres de campos para que la información llegue bien.

### A-02 · Nombre de la criatura

En el listado de avistamientos aparecía el ID de MongoDB en lugar del nombre de la criatura, aunque en la página de detalle sí se veía correctamente.

La IA me sugirió revisar `populate()`, que habíamos utilizado en clase para trabajar con documentos relacionados. Al buscar en mi código, noté que la función se llamaba `listar()` y no como aparecía en el primer ejemplo que recibí. Por eso adapté la corrección a mi archivo real.

Agregué `populate('criatura')` y comprobé el resultado. Al principio seguía apareciendo el ID, así que reinicié el backend y revisé nuevamente la lista. Finalmente, los nombres se mostraron correctamente.

### A-03 · Validación del nivel de peligro

Este fue uno de los errores que más me ayudó a entender la diferencia entre el frontend y el backend.

Primero intenté ingresar 999 desde el formulario, pero el navegador no me dejaba porque el campo tenía un máximo de 10. Entonces entendí que probar solamente desde la interfaz no era suficiente.

Con ayuda de ChatGPT hice una petición directamente a la API utilizando `curl` y comprobé que el backend sí aceptaba el valor inválido.

Revisé `findByIdAndUpdate()` y agregué `runValidators: true`, porque las validaciones del esquema no se estaban aplicando a esa actualización.

La primera vez que repetí la prueba seguía respondiendo HTTP 200. Antes de cambiar más código, revisé qué carpeta estaba utilizando y descubrí que estaba ejecutando otra copia del proyecto. Reinicié el backend correcto y comprobé que el 999 ya era rechazado.

Me quedó claro que las validaciones no deben depender solamente del formulario: el backend también tiene que proteger los datos.

### A-04 · Filtro de criaturas

Al cambiar el filtro de criaturas, por ejemplo a «Mecánica», la lista seguía mostrando todos los tipos. Solamente se actualizaba al presionar el botón «Actualizar lista».

ChatGPT me orientó a revisar las dependencias del `useEffect`. Cuando abrí `ListaCriaturas.tsx`, vi que solamente dependía de `recarga`, pero no del tipo seleccionado.

Agregué `tipo` y dejé las dependencias como `[tipo, recarga]`. No cambié el resto de la función porque la consulta a la API y el botón de actualización ya funcionaban.

Después probé distintos filtros y comprobé que la lista cambiaba automáticamente.

Entendí que `useEffect` necesita saber qué valores deben provocar una nueva consulta.

### Cómo verifiqué los cambios

No me limité a copiar las correcciones. Probé los errores antes y después de modificarlos, tomé capturas y comprobé que los cambios funcionaran en la aplicación.

Para las validaciones también utilicé `curl`, porque necesitaba ver las respuestas reales de la API y no solamente lo que mostraba el navegador.

Finalmente, hice un recorrido por la aplicación para comprobar que las criaturas, los avistamientos, los filtros y las actualizaciones seguían funcionando juntos.

La IA me ayudó a encontrar soluciones más rápido, pero las correcciones se adaptaron al código existente y las comprobé directamente antes de darlas por resueltas.