# documentación sistema recomendación

### Introducción

El sistema de recomendación es un software que permite prescribir a los usuarios productos, del catálogo del comercio electrónico donde está implatando, con el objetivo de incentivarles en la compra. 

> Para el óptimo funcionamiento del sistema de recomendación es necesario que el tracker de eventos trabaje de forma independiente durante varios días con el objetivo de estudiar los datos. Pasado el periodo de entrenamiento, se deberá integrar el recomendador. 

Previamente a la integración es necesario recopilar el catálogo de productos a través de csv con el siguiente formato:

`product_id,name,price,currency,category_ids,image_url,brand,url,domain`

Puedes ver un ejemplo aqui: [sample.csv](cerbatana_sample_products.csv )

### Integración

La integración del software se realiza a través de las plantillas html. La aplicación se divide en dos procesos claramente diferenciados: el **tracker de eventos** (tracker) y la **visualización de productos recomendados** (recommend).

Para que el sistema pueda funcionar se debe colocar en entre las etiquetas "head" la librería y el identificador del comercio electrónico:

```html
<script type="text/javascript" src="https://cerbatana.crbtn.com/api/v1/uuid?[random]&clientid=[client id]" async></script>

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
>
> `store_uuid->id: identificador del usuario en la tienda (string)`

#### Example

```json
var crbtn_data = {         
    "user": {
    	"store_uuid": "12okjok-12313kof"
    },
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

>  ` orderid: identificador de orden de compra`
>
>  ` products->id: identificador de producto`
>
>  ` products->units: unidades de producto compradas`
>
>  ` products->price: identificador de producto`
>
> `store_uuid->id: identificador del usuario en la tienda (string)`

#### Example

```json
var crbtn_data = { 
    
"orderid" : "12h5784jK",
"user": {
    	"store_uuid": "12okjok-12313kof"
},
"products": [    
    
        {"id" : "2",                 
        "units" : 1,                 
        "price" : 10.5                
        },                 
        {"id" : "6",                 
        "units" : 1,                 
        "price" : 3.0                
        },                 
        {"id" : "10",                 
        "units" : 2,                 
        "price" : 0.5                 
        }                 
	]
};

crbtn.track("purchase", crbtn_data);
```

#### Atributos para las acciones view-category

view-category espera los siguientes atributos:

> ` categoryid: categoria visualizada `
>
> `store_uuid->id: identificador del usuario en la tienda (string)`

#### Example

```json
var crbtn_data = {
    "user": {
    	"store_uuid": "12okjok-12313kof"
    },
    "categoryid" : "2e"
    }

crbtn.track("view-category", crbtn_data);
```

#### Atributos para otro tipo de acciones

Para cualquier otro tipo de eventos (homepage, error, search), cerbatana espera los siguientes atributos:

> ` data: un diccionario con cualquier tipo de dato que se quiera monitorizar
>
> `store_uuid->id: identificador del usuario en la tienda (string)`

#### Example

```json
	 var crbtn_data = {
	     "user": {
    		"store_uuid": "12okjok-12313kof"
    	    },
            "data": {"query": "zapatos de tacón"} 
	};

crbtn.track("search", crbtn_data);
```

```json
	 var crbtn_data = {
	     "user": {
    		"store_uuid": "12okjok-12313kof"
    	    },
            "data": {} 
	};

crbtn.track("homepage", crbtn_data);
```

```json
	 var crbtn_data = {
	     "user": {
    	       "store_uuid": "12okjok-12313kof"
            },
            "data": {"page": "/products/invalid_product.html"} 
	};

crbtn.track("error", crbtn_data);
```

### Integración servicio de recomendación

La implementación del software de recomendación se realiza a través de la colocación del siguiente código donde se quiera visualizar el widget. Para la implementación se necesita añadir entre las etiquetas `<body></body> `la librería de cerbatana. Los productos se mostrarán dentro de la etiqueta.

La llamada a la función que pinta en el html los elementos recomendados es `crbtn.recommend()`. Esta función espera 3 atributos: place (category, product o checkout), id de producto y el nombre de la función donde se devolverá un objeto json.

Para la carga inicial es necesario procesar un archivo csv con las siguientes columnas:

> `id: identificador del producto`
> `name: nombre a mostrar del producto`
> `price: precio del producto (sin rebaja)`
> `categories: listado de id de categorias separados por comas`
> `tags: etiquetas de producto (opcional)` 
> `image_url: url de la imagen del producto a mostrar en la lista de recomendaciones`
> `brand: marca del producto`
> `product_url: url de la pagina del producto (url completa)`
>

#### Ejemplo de implementación:

```html
<script type="text/javascript">
    crbtn.recommend("product","1", "store_uuid", "function_example");
    
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
	"recommendation_type": "category",
	"products": [
 	{
		"categories": ["Camisetas", "Rebajas"],
		"isavailable": true,
		"sku": "SADSQ",
		"price": 18.0,
		"id": "7530",
		"image": "http://example.mycerbatana.com/wp-content/uploads/2018/05/vneck-tee.jpg",
        "name": "Camiseta de cuello en V",
		"url": "http://example.mycerbatana.com/producto/vneck-tee/"
	}, {
		"categories": ["Camisetas"],
		"isavailable": true,
		"sku": "ASDD1231",
		"price": 18,
		"id": "722700",
		"image": "http://example.mycerbatana.com/wp-content/uploads/2018/05/tshirt.jpg",
		"name": "Camiseta",
		"url": "http://example.mycerbatana.com/producto/tshirt/"
	}]
}
```

<u>Nota: Para evitar problemas en la carga es recomendable realizar las llamadas a las funciones una vez cargado el DOM del navegador usando funciones onready, onload o similar</u>

### Dashboard

Para acceder al dashboard necesita visitar la siguiente dirección:

**https://visual.[CLIENT ID].crbtn.com**

El (Client id) se le pasará a través de un email. En este dashboard se podrá ver datos tanto de los eventos producidos en el comercio electrónico, como información relevante extraída de diferentes fuentes. A su vez se le facilitará user y password para acceder al dashboard.

También es posible integrar diferentes fuentes de información como google analytics o fuentes de datos externa para el uso por parte de cualquier departamento de la compañía.

[More info](https://www.tribeclick.com/index.php/contacto/) 
