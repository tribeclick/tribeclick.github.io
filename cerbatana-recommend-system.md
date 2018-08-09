

# documentación sistema recomendación

### Introducción

El sistema de recomendación es un software que permite prescribir a los usuarios productos, del catálogo del comercio electrónico donde está implatando, con el objetivo de incentivarles en la compra. 

> Para el óptimo funcionamiento del sistema de recomendación es necesario que el tracker de eventos trabaje de forma independiente durante varios días con el objetivo de estudiar los datos. Pasado el periodo de entrenamiento, se deberá integrar el recomendador. 

### Integración

La integración del software se realiza a través de las plantillas html. La aplicación se divide en dos procesos claramente diferenciados: el **tracker de eventos** (tracker) y la **visualización de productos recomendados** (recommend).

Para que el sistema pueda funcionar se debe colocar en entre las etiquetas "head" la librería y el identificador del comercio electrónico:

```html
<script type="text/javascript" src="https://cerbatana.crbtn.com/api/v1/uuid?[random]&clientid=[client id]"></script>

```

> El [client id] será facilitado al administrador del sistema para la implantación. En la librería es obligado añadir un elemento random o timestamp como parámetro con el objetivo de evitar la caché del navegador.



### Tracker de eventos

#### Atributos para las acciones view, add-cart y delete-cart

Tanto view, add-cart como delete-cart estarán esperando los siguientes atributos:

> `product->id: es el identificador único de producto. (string)`
>
> `product->price: es el precio del producto (string)`
>
> `product->currency: tipo de moneda (string ejemplo: "EUR")`
>
> `product->isavailable: Disponibilidad del producto (boolean)`
>
> `product->sku: SKU del producto`
>
> `product->categories: listado de categorias del producto. Separado por comas y sin espacios. (array de strings)`
>
> `product->description : Descripción del producto (string)`
>
> `product->brand: Nombre de la marca (string)`
>
> `product->tags: listado de tags, separado por comas y sin espacios (array string)`
>
> `product->url: url del detalle del producto (string) `
>
> `product->image: url de la imagen principal del producto (string)`

#### Example

```json
var crbtn_data = {         
    "product":{
        
        "id":"2",                 
        "price": 10.5,                 
        "currency": "EUR",                
        "isavailable": true,                 
        "sku" :"12212121212",                 
        "tags": ["azul","ropa","caballo"],                 
        "name":"zapatillas de deporte con ilustracion caballo",
        "categories":["12","3"],             
        "image": "https://www.example.com/3720880.jpg",                
        "url":"https://www.example.com/novedades/rouge-g.html"
        
        }};

    crbtn.track("view-product", crbtn_data);
```

#### Atributos para las acciones purchase o checkout

Tanto para los eventos de purchase como para checkout está esperando los siguientes atributos :

>  ` product->id: identificador de producto`
>
>  ` product->units: unidades de producto compradas`
>
>  ` product->price: identificador de producto`
>
>  

#### Example

```json
var crbtn_data = { 
    
"orderid" : "12h5784jK",    
"products": [    
    
        {"id" : "2",                 
        "unit" : 1,                 
        "price" : 10.5                
        },                 
        {"id" : "6",                 
        "unit" : 1,                 
        "price" : 3.0                
        },                 
        {"id" : "10",                 
        "unit" : 2,                 
        "price" : 0.5                 
        }                 
	]
};

crbtn.track("purchase", crbtn_data);
```



#### Atributos para las acciones category

Category espera los siguientes atributos:

> ` categoryid: categoria visualizada `



#### Example

```javascript
var crbtn_data = {"categoryid" : "2"}
```

### Integración servicio de recomendación

La implementación del software de recomendación se realiza a través de la colocación del siguiente código donde se quiera visualizar el widget. Para la implementación se necesita añadir entre las etiquetas `<body></body> `la librería de cerbatana. Los productos se mostrarán dentro de la etiqueta.

La llamada a la función que pinta en el html los elementos recomendados es `crbtn.recommend()`. Esta función espera 3 atributos: place (category, product o checkout), id de producto y el nombre de la función donde se devolverá un objeto json.

#### Ejemplo de implementación:

```html
<script type="text/javascript">
    crbtn.recommend("product","1","function_example");
    
    function function_example(data){
        
        /*
        * el parámetro data contendrá el objecto con los productos a mostrar. 
        */      
    }
</script>

```

#### Ejemplo de json devuelto por la función crbtn.recommend

Aqui se muestra un ejemplo de la estructura devuelta por la función recommend. 

```json
{  
   "title":"TOP VENTAS",
   "products":[  
      {  
         "name":"Rouge G",
         "brand":"GUERLAIN",
         "price": 20.56 ,
         "url_image":"https://www.example.com/media/catalog/product/cache/1/small_image/178x/9df78eab33525d08d6e5fb8d27136e95/3/7/3720880.jpg",
         "url":"https://www.example.com/novedades/rouge-g.html?utm_source=cerbatana"
      },
      {  
         "name":"Rouge G III",
         "brand":"GUERLAIN",
         "price": 23.75,
         "url_image":"https://www.example.com/media/catalog/product/cache/1/small_image/178x/9df78eab33525d08d6e5fb8d27136e95/3/7/3720880.jpg",
         "url":"https://www.example.com/novedades/rouge-g.html?utm_source=cerbatana"
      },
      {  
         "name":"Rouge G II",
         "brand":"GUERLAIN",
         "price": 20.03,
         "url_image":"https://www.example.com/media/catalog/product/cache/1/small_image/178x/9df78eab33525d08d6e5fb8d27136e95/3/7/3720880.jpg",
         "url":"https://www.example.com/novedades/rouge-g.html?utm_source=cerbatana"
      },
      {  
         "name":"Rouge G !!",
         "brand":"GUERLAIN",
         "price": 10.26,
         "url_image":"https://www.example.com/media/catalog/product/cache/1/small_image/178x/9df78eab33525d08d6e5fb8d27136e95/3/7/3720880.jpg",
         "url":"https://www.example.com/novedades/rouge-g.html?utm_source=cerbatana"
      },
      {  
         "name":"Rouge G",
         "brand":"GUERLAIN",
         "price": 9.12,
         "url_image":"https://www.example.com/media/catalog/product/cache/1/small_image/178x/9df78eab33525d08d6e5fb8d27136e95/3/7/3720880.jpg",
         "url":"https://www.example.com/novedades/rouge-g.html?utm_source=cerbatana"
      }
   ]
}
```



### Dashboard

Para acceder al dashboard necesita visitar la siguiente dirección:

**https://visual.[CLIENT ID].crbtn.com**

El (Client id) se le pasará a través de un email. En este dashboard se podrá ver datos tanto de los eventos producidos en el comercio electrónico, como información relevante extraída de diferentes fuentes. A su vez se le facilitará user y password para acceder al dashboard.

También es posible integrar diferentes fuentes de información como google analytics o fuentes de datos externa para el uso por parte de cualquier departamento de la compañía.

[More info](https://www.tribeclick.com/index.php/contacto/) 