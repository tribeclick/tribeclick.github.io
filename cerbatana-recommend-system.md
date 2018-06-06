# documentación sistema recomendación

### Introducción

El sistema de recomendación es un software que permite prescribir a los usuarios productos, del catálogo del comercio electrónico donde está implatando, con el objetivo de incentivarles en la compra. 

### Integración

La integración del software se realiza a través de las plantillas html. La aplicación se divide en dos procesos claramente diferenciados: el **tracker de eventos** (tracker) y la **visualización de productos recomendados** (recommend).

Para que el sistema pueda funcionar se debe colocar en entre las etiquetas "head" la librería y el identificador del comercio electrónico:

```html
<script type="text/javascript" src="https://cerbatana.[id ecommerce].crbtn.com/api/v1/uuid?[random]"></script>
<script type="text/javascript">
window.crbtn_id = "000000001";
</script>

```

> El identificador del comercio electrónico y la url de la librería serán enviados al administrador del sistema para la implantación. En la librería es obligado añadir un elemento random o timestamp como parámetro con el objetivo de evitar la caché del navegador.



### Integración del Tracker de eventos