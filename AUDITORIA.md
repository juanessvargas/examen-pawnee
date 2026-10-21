# Auditoría · Versión A
Código: 00334845
Repositorio público propio: https://github.com/juanessvargas/examen-pawnee.git
Rama: examen/334845

## A-01 · No se registra el avistamiento
### Síntoma
Al intentar registrar un avistamiento, el formulario no lograba guardar correctamente la información de la criatura seleccionada.

### Causa
En FormularioAvistamiento.tsx, el frontend enviaba criaturaId al crear un avistamiento, pero el backend esperaba recibir el campo criatura. Esta diferencia impedía que los datos llegaran correctamente a la API.

### Cambio
Corregí el objeto datos para enviar criatura: form.criatura tanto al crear como al editar un avistamiento, sin modificar las validaciones existentes.

### Prueba
Se debe registrar un avistamiento seleccionando una criatura existente, comprobar que se guarde correctamente y recargar la página para verificar su persistencia en MongoDB.

También queda pendiente comprobar que una referencia inexistente responda con HTTP 404.

### Estado
Resuelto 

## A-02 · La lista no identifica a la criatura
### Síntoma
Cuando entraba a la lista de avistamientos, en lugar de aparecer el nombre de la criatura, aparecía su ID de MongoDB. Sin embargo, al entrar al detalle del avistamiento sí podía ver el nombre correctamente.

### Causa
Revisé el servicio de avistamientos y encontré que en la función listar() faltaba utilizar populate('criatura'). Por eso, el backend solamente enviaba el ID de la criatura y no su información completa.

### Cambio
Agregué .populate('criatura') a la consulta de listar(), sin modificar los filtros ni el orden de los resultados.

### Prueba
Primero comprobé el error en la lista y tomé una captura donde aparecían los IDs. Después hice el cambio, reinicié el backend y revisé la respuesta de la API. Finalmente, actualicé la página y comprobé que ahora aparecían los nombres de las criaturas en lugar de sus IDs.

### Estado
Resuelto 

## A-03 · Se acepta un peligro inválido
### Síntoma
Al intentar poner un nivel de peligro de 999 desde el formulario, el navegador no me dejaba guardar porque el campo tenía un máximo de 10. Sin embargo, cuando hice la prueba directamente desde la terminal con curl, el backend sí aceptó el 999 y respondió con HTTP 200.

### Causa
El problema estaba en criaturas.service.ts, dentro de la función actualizar(). La consulta utilizaba findByIdAndUpdate() sin activar las validaciones de Mongoose. Por eso, aunque el modelo establecía que el nivel de peligro debía estar entre 1 y 10, se podían guardar valores fuera de ese rango.

### Cambio
Agregué runValidators: true a las opciones de findByIdAndUpdate(). No fue necesario modificar el modelo ni las validaciones que ya existían.

### Prueba
Primero envié una petición PUT con nivelPeligro: 999 y comprobé que respondía HTTP 200, aunque no debía permitirlo.

Después de realizar la corrección y reiniciar el backend correcto, repetí la petición y comprobé que el valor inválido ya era rechazado. También utilicé un valor válido de 7 para comprobar que la actualización normal seguía funcionando.

### Estado
RESUELTO. 

## A-04 · El filtro no actualiza la pantalla
### Síntoma
Cuando cambiaba el filtro de criaturas, por ejemplo de «Todas» a «Mítica», la lista seguía mostrando criaturas de otros tipos. Solamente se actualizaba cuando presionaba el botón «Actualizar lista».

### Causa
El problema estaba en ListaCriaturas.tsx. Al revisar el useEffect, encontré que solamente tenía recarga en su arreglo de dependencias. Por eso, React no volvía a consultar la API cuando cambiaba el valor de tipo.

### Cambio
Agregué tipo a las dependencias del useEffect, dejando [tipo, recarga]. Así, React vuelve a ejecutar la consulta cuando cambia el filtro, pero también mantiene funcionando el botón «Actualizar lista».

### Prueba
Primero seleccioné el filtro «Mecánica» y comprobé que seguían apareciendo criaturas espectrales, míticas y elementales. La lista solamente cambiaba al presionar «Actualizar lista».

Después de corregir el código, probé los filtros de criaturas y comprobé que la lista se actualizaba automáticamente al seleccionar otro tipo, sin necesidad de presionar el botón.

### Estado
RESUELTO. 

