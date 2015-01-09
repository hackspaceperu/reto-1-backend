# Reto 1 de Backend

[Descarga el código para el reto aquí.](Reto1Backend.zip)

El fin de este reto es aprender, pues veremos nuevas herramientas. Las nuevas herramientas a entender son:

-Uso de package.json
-Servidor RESTful y peticiones CRUD
-MongoDB y Mongoose
-Express


## Entrega del Reto 1

La entrega de este reto serán simplemente **printscreen** de las puebas que hagas al correr el servidor rest, pues la idea es entender como funciona a partir del código, esa es la clave para luego crear tu propia aplicación.

Para este reto deberás insertar tus propios registros (**tvshows**) en la base de datos siguiendo el esquema usado y usando el metodo **POST**. Luego deberas realizar los metodos **GET**, **PUT** y **DELETE** y por cada uno enviar un pantallazo de manera que se aprecie el cambio realizado. Todo ello usando [restconsole.com](restconsole.com), el cual explicaremos luego. 

El formato a enviar es un comprimido (.zip) con los printscreens y cuyo nombre de archivo es el nombre completo del participante. 

## Programas necesarios a instalar

Para que esto funcione en nuestro entorno local, necesitamos tener **instalado MongoDB**. Dependiendo de vuestro sistema operativo de hace de una forma u otra. Los pasos para instalar MongoDB dependen de su sistema operativo: 

### Instalar e Iniciar MongoDB en Windows

-[Instalar MongoDB en Windows](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-windows/#install-mongodb)

a.Descarga y ejecuta con doble click una version adecuada a tu version de Windows en este [link](http://www.mongodb.org/downloads?_ga=1.248042692.235510057.1420180698).

-Para usar luego: [Iniciar MongoDB en Windows](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-windows/#run-mongodb)


### Instalar e Iniciar MongoDB en Linux - Debian - Ubuntu

-[Instalar MongoDB en Linux - Debian - Ubuntu](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-debian/#install-mongodb)

a.Importar la llave publica usada por el sistema gestor de paquetes.

```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv 7F0CEB10
```

b.Crear el archivo /etc/apt/sources.list.d/mongodb.list para MongoDB.

```
echo 'deb http://downloads-distro.mongodb.org/repo/debian-sysvinit dist 10gen' | sudo tee /etc/apt/sources.list.d/mongodb.list
```

c.Recargar la base de datos de paquetes locales.

```
sudo apt-get update
```

d.Instalar los paquetes de MongoDB.

```
sudo apt-get install -y mongodb-org
```

-Para usar luego: [Iniciar MongoDB en Linux - Debian - Ubuntu](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-debian/#run-mongodb)


### Instalar e Iniciar MongoDB en Mac OS

-[Instalar MongoDB en Mac OS](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-os-x/#install-mongodb-with-homebrew)

a.Se instala de la misma manera de Node.js, siguiendo lo siguientes comandos:
 
```
brew update
```

b.Luego:

```
brew install mongodb
```

-Para usar luego: [Iniciar MongoDB en Mac OS](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-os-x/#run-mongodb)


## Creando una API RESTful

En esta entrada explicaremos como crear un Web Server que sirva una API RESTful usando para ello la tecnología de Node.js y todo el JavaScript que podamos, incluyendo la base de datos (MongoDB) y el plugin para conectarnos a ella y mapear los modelos (Mongoose).

Vamos a crear una aplicación CRUD (Create/Read/Update/Delete) para mostrar como trabaja y explicar como desplegarla en un entorno Cloud como Heroku. Vamos allá!

El primer paso es crear un directorio local llamado **"node-api-rest-example"** al cual llamaremos **directorio raíz** de la aplicación.

### Package.json

El primer código que necesitamos escribir en una aplicación Node es el archivo package.json el cual nos indica que dependencias vamos a utilizar en ella. Este archivo va en el **directorio raíz**.

**package.json**

```json
{
  "name" : "node-api-rest-example",
  "version" : "0.0.1",
  "dependencies" : {
    "express" : "3.2.6",
    "mongoose" : "3.6.11"
  }
}
```

Y ahora para descargar las dependencias escribimos lo siguiente en la consola ubicandonos en el **directorio raíz** y NPM (el gestor de paquetes de Node) se encargará de instalarlas.

```
npm install
```

Veremos que se descargan las dependencias en una nueva carpeta en el **directorio raíz** .Con todo listo, podemos comenzar a codear de verdad. Creamos un fichero llamado **app.js** en el **directorio raíz** que será el que ejecute nuestra aplicación y arranque nuestro server. Crearemos en primer lugar un sencillo servidor web para comprobar que tenemos todo lo necesario instalado, y a continuación iremos escribiendo más código.


```js
//app.js
var express  = require("express"),
    app      = express(),
    http     = require("http"),
    server   = http.createServer(app),

app.configure(function () {
  app.use(express.bodyParser());
  app.use(express.methodOverride());
  app.use(app.router);
});

app.get('/', function(req, res) {
  res.send("Hello world!");
});

server.listen(3000, function() {
  console.log("Servidor node corriendo en http://localhost:3000");
});
```

¿Qué hace este código? Muy sencillo, las primeras líneas se encargan de incluir las **dependencias** que vamos a usar, algo así como los includes en C o PHP, o los import de Python. Importamos Express que es un Framework de Node que nos hará la vida má fácil a la hora de crear un servidor de web y hacer llamadas HTTP. Con http creamos el servidor que posteriormente escuchará en el puerto 3000 de nuestro ordenador (O el que nosotros definamos).

Con **app.configure** estamos configurando la aplicación para que pueda realizar métodos REST (Post, Get, etc..) Con bodyParser() permitimos que pueda parsear JSON, methodOverride() nos permite implementar y personalizar métodos HTTP y por último app.router nos permite crear rutas personalizadas.

En este primer ejemplo vamos ha hacer que sólo reciba una **petición GET** del navegador y muestre en el mismo la frase **“Hello World”**

Para ejecutar este pequeño código sólo tienes que escribir en consola lo siguiente y abrir un navegador con la url http://localhost:3000

```
node app.js
```

Si todo va bien, esto es lo que veras:

![Pantallazo Ejemplo 1](http://imageshack.com/a/img912/5177/YnvCVy.png)


### MongoDB y Mongoose

En esta parte vamos a crear un modelo usando [Mongoose](http://mongoosejs.com/) para poder guardar la información en la base de datos siguiendo el esquema. 

Como base de datos vamos a utilizar MongoDB. MongoDB es una base de datos Open Source NoSQL orientada a documentos tipo JSON.

Mongoose es un ODM (Object Data Mapper) en JavaScript diseñado para aplicaciones que usen MongoDB sobre Node.js. Posee un API intuitivo para definir los modelos de datos y el almacenamiento asíncrono (Mongoose ODM).

Para este ejemplo vamos a crear una base de datos de series de TV, por tanto vamos a crear un modelo (**Archivo: models/tvshow.js**, es decir crear una carpeta models) que incluya la información de una serie de TV, como pueden ser su título, el año de inicio, país de producción, una imagen promocional, número de temporadas, género y resumen del argumento:

```js
//models/tvshow.js
var mongoose = require('mongoose'),
    Schema   = mongoose.Schema;

var tvshowSchema = new Schema({
  title:    { type: String },
  year:     { type: Number },
  country:  { type: String },
  poster:   { type: String },
  seasons:  { type: Number },
  genre:    { type: String, enum:
  ['Drama', 'Fantasy', 'Sci-Fi', 'Thriller', 'Comedy']
        },
  summary:  { type: String }    
});

module.exports = mongoose.model('TVShow', tvshowSchema);
```

Con esto ya podemos implementar la conexión a la base de datos en el archivo app.js añadiendo las siguientes líneas:

```
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/tvshows');
```

Quedando así el código de app.js:


```
//app.js
var express  = require("express"),
    app      = express(),
    http     = require("http"),
    server   = http.createServer(app),
    mongoose = require('mongoose'); 

app.configure(function () {
  app.use(express.bodyParser());
  app.use(express.methodOverride());
  app.use(app.router);
});

app.get('/', function(req, res) {
  res.send("Hello world!");
});

mongoose.connect('mongodb://localhost/tvshows', function(err, res) {
	if(err) {
		console.log('ERROR: connecting to Database. ' + err);
	} else {
		console.log('Connected to Database');
	}
});

server.listen(3000, function() {
  console.log("Node server running on http://localhost:3000");
});
```

#### Instalando MongoDB

Para poder **iniciar MongoDB** debes ejecutar en otra terminal:

-[**En Windows**](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-windows/#run-mongodb)

```
md \data\db
```

Luego:

```
C:\Program Files\MongoDB\bin\mongod.exe
```

Si quisieramos detenerlo (no por ahora) usamos **Control+C** en la terminal que corre MongoDB.


-[**En Linux**](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-debian/#run-mongodb)

```
sudo service mongod start
```

Si quisieramos detenerlo (no por ahora) usamos:

```
sudo service mongod stop
```

-[**En Mac OS**](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-os-x/#run-mongodb)

```
mkdir -p /data/db
```

Luego:

```
mongod
```

Si quisieramos (no por ahora) detenerlo usamos **Control+C** en la terminal que corre MongoDB.


Con Mongo arrancado ya podemos ejecutar la aplicación como en la parte anterior con **node app.js** desde la terminal, si todo va bien tendremos algo en la pantalla como esto:

```
node app.js
```

Ahora desde otra terminal, podemos entrar al shell de MongoDB y comprobar que la base de datos se ha creado correctamente. Para ello ejecutamos el comando **mongo**:

```
$ **mongo**
MongoDB shell version: 2.4.1  
connecting to: test  
> **use tvshows**
switched to db tvshows  
> **show dbs**
local    0.078125GB  
tvshows    (empty)  
>_
```

### Peticiones CRUD

Ya tenemos todo configurado y listo para albergar los datos, sólo nos queda crear las rutas que definirán las llamadas a la API para poder guardar y consultar la información

Para mantener el código **modulado** y separado vamos a crear las rutas en un archivo aparte que llamaremos **routes/tvshows.js**. Con **“module.exports”** conseguimos modularizarlo y que pueda ser llamado desde el archivo principal de la aplicación. El código de a continuación es el comienzo del archivo con la primera función que será la que devuelva todos los registros almacenados:


```js
//routes/tvshows.js
module.exports = function(app) {

  var TVShow = require('../models/tvshow.js');

  //GET - Return all tvshows in the DB
  findAllTVShows = function(req, res) {
      TVShow.find(function(err, tvshows) {
          if(!err) {
              res.send(tvshows);
          } else {
              console.log('ERROR: ' + err);
          }
      });
  };

}
```

De esta manera tan sencilla, al llamar a esta función (**findAllTVShows**) se envia como respuesta toda la colección de tvshows almacenada y en formato **JSON**. Si queremos que solo nos devuelva un registro con un identificador único, tenemos que crear una función tal que la siguiente:

##### Método GET

```js
//GET - Retorna un TVShow con un ID especifico
findById = function(req, res) {
  TVShow.findById(req.param.id, function(err, tvshow) {
    if(!err) {
      res.send(tvshow);
    } else {
      console.log('ERROR: ' + err);
    }
  });
};
```
Con las funciones **find()** y **findById()** podemos buscar en la base de datos a partir de un modelo. Ahora vamos a desarrollar el resto de funciones que nos permitirán **insertar**, **actualizar** y **borrar** registros de la base de datos. La función de a continuación sería la correspondiente al método **POST** y nos permitirá añadir un nuevo objeto a la base de datos:

##### Método POST

```js
//POST - Insertar un nuevo TVShow en la base de datos.
addTVShow = function(req, res) {
  console.log('POST');
  console.log(req.body);

  var tvshow = new TVShow({
    title:    req.body.title,
    year:     req.body.year,
    country:  req.body.country,
    poster:   req.body.poster,
    seasons:  req.body.seasons,
    genre:    req.body.genre,
    summary:  req.body.summary  
  });

  tvshow.save(function(err) {
    if(!err) {
      console.log('Created');
    } else {
      console.log('ERROR: ' + err);
    }
  });

  res.send(tvshow);
};
```

Primero creamos un nuevo objeto **tvshow** siguiendo el **patrón del modelo**, recogiendo los valores del cuerpo de la petición (**req.body.xxxxxx**), lo salvamos en la base de datos con el comando **.save()** y por Último lo enviamos en la respuesta de la función (**res.send(tvshow)**).

La siguiente función nos permitirá actualizar un registro a partir de un ID. Primero buscamos en la base de datos el registro dado el ID, y actualizamos sus campos con los valores que trae el cuerpo de la petición

##### Método PUT

```js
//PUT - Actualizar un registro que ya existe
updateTVShow = function(req, res) {
  TVShow.findById(req.params.id, function(err, tvshow) {
    tvshow.title   = req.body.title;
    tvshow.year    = req.body.year;
    tvshow.country = req.body.country;
    tvshow.poster  = req.body.poster;
    tvshow.seasons = req.body.seasons;
    tvshow.genre   = req.body.genre;
    tvshow.summary = req.body.summary;

    tvshow.save(function(err) {
      if(!err) {
      console.log('Updated');
      } else {
      console.log('ERROR: ' + err);
      }

      res.send(tvshow);
    });
  });
}
```

Y por último para completar la funcionalidad CRUD de nuestra API, necesitamos la función que nos permita eliminar registros de la base de datos y eso lo podemos hacer con el código de a continuación:

```js
//DELETE - Elimina un TVShow con un ID especifico
deleteTVShow = function(req, res) {
  TVShow.findById(req.params.id, function(err, tvshow) {
    tvshow.remove(function(err) {
      if(!err) {
      console.log('Removed');
      } else {
      console.log('ERROR: ' + err);
      }
    })
  });
}
```

Como se puede ver, usamos de nuevo el método **.findById()** para buscar en la database y para borrarlo usamos **.remove()** de la misma forma que usabamos el **.save()** para salvar. 


Ahora tenemos que unir estas funciones a las peticiones que seran nuestras llamadas al API. Eso lo hacemos de la siguiente manera:

```js
//Link a las routes y functions
app.get('/tvshows', findAllTVShows);
app.get('/tvshow/:id', findById);
app.post('/tvshow', addTVShow);
app.put('/tvshow/:id', updateTVShow);
app.delete('/tvshow/:id', deleteTVShow);
```


El archivo completo **routes/tvshows.js** seria:

```js
//routes/tvshows.js
module.exports = function(app) {

  var TVShow = require('../models/tvshow.js');

  //GET - Return all tvshows in the DB
  findAllTVShows = function(req, res) {
  	TVShow.find(function(err, tvshows) {
  		if(!err) {
        console.log('GET /tvshows')
  			res.send(tvshows);
  		} else {
  			console.log('ERROR: ' + err);
  		}
  	});
  };

  //GET - Return a TVShow with specified ID
  findById = function(req, res) {
  	TVShow.findById(req.params.id, function(err, tvshow) {
  		if(!err) {
        console.log('GET /tvshow/' + req.params.id);
  			res.send(tvshow);
  		} else {
  			console.log('ERROR: ' + err);
  		}
  	});
  };

  //POST - Insert a new TVShow in the DB
  addTVShow = function(req, res) {
  	console.log('POST');
  	console.log(req.body);

  	var tvshow = new TVShow({
  		title:    req.body.title,
  		year: 	  req.body.year,
  		country:  req.body.country,
  		poster:   req.body.poster,
  		seasons:  req.body.seasons,
  		genre:    req.body.genre,
  		summary:  req.body.summary  
  	});

  	tvshow.save(function(err) {
  		if(!err) {
  			console.log('Created');
  		} else {
  			console.log('ERROR: ' + err);
  		}
  	});

  	res.send(tvshow);
  };

  //PUT - Update a register already exists
  updateTVShow = function(req, res) {
  	TVShow.findById(req.params.id, function(err, tvshow) {
  		tvshow.title   = req.body.petId;
  		tvshow.year    = req.body.year;
  		tvshow.country = req.body.country;
  		tvshow.poster  = req.body.poster;
  		tvshow.seasons = req.body.seasons;
  		tvshow.genre   = req.body.genre;
  		tvshow.summary = req.body.summary;

  		tvshow.save(function(err) {
  			if(!err) {
  				console.log('Updated');
  			} else {
  				console.log('ERROR: ' + err);
  			}
  			res.send(tvshow);
  		});
  	});
  }

  //DELETE - Delete a TVShow with specified ID
  deleteTVShow = function(req, res) {
  	TVShow.findById(req.params.id, function(err, tvshow) {
  		tvshow.remove(function(err) {
  			if(!err) {
  				console.log('Removed');
  			} else {
  				console.log('ERROR: ' + err);
  			}
  		})
  	});
  }

  //Link routes and functions
  app.get('/tvshows', findAllTVShows);
  app.get('/tvshow/:id', findById);
  app.post('/tvshow', addTVShow);
  app.put('/tvshow/:id', updateTVShow);
  app.delete('/tvshow/:id', deleteTVShow);

}
```

Para enlazar este módulo al archivo principal de la aplicación **app.js**, tenemos que añadir la siguiente línea:

```
routes = require('./routes/tvshows')(app);
```

### Servidor RESTful

A continuación vamos a probar una herramienta online que nos permitirá jugar con las llamadas al API y poder consultar y almacenar datos para probarla y ver su funcionamiento un poco más claro.

Para ello nos dirigimos a [restconsole.com](restconsole.com) que es una extensión de Google Chrome, que nos permitira hacer lo que queremos de una manera visual y sencilla.

Antes de probarlo, debemos tener **mongo** y el servidor **node** de nuestra app **corriendo**. Una vez hecho esto introducimos los siguientes datos para hacer una llamada POST que almacene un registro en la base de datos.

a.**Target**: http://localhost:3000/tvshow (Dónde está ejecutándose la aplicación y la llamada al método POST que hemos programado)
b.**Content-Type**: application/json (en los dos input que nos dan)
c.**Request-Payload**: Aquí va el cuerpo de nuestra petición, con el objeto JSON siguiente (por ejemplo):

```json
{
  "title": "LOST",
  "year": 2004,
  "country": "USA",
  "poster": "http://ia.media-imdb.com/images/M/MV5BMjA3NzMyMzU1MV5BMl5BanBnXkFtZTcwNjc1ODUwMg@@._V1_SY317_CR17,0,214,317_.jpg",
  "seasons": 6,
  "genre": "Sci-Fi",
  "summary": "The survivors of a plane crash are forced to live with each other on a remote island, a dangerous new world that poses unique threats of its own."
}
```

![Pantallazo 2](http://imageshack.com/a/img538/4112/NjEC42.png)

Pulsamos SEND y si todo va bien, la petición se realizará y abajo de la aplicación REST Console veremos algo como esto:

![Pantallazo 3](http://imagizer.imageshack.us/a/img661/9765/lxOFxX.png)

¿Cómo comprobamos si se ha guardado en nuestra base de datos? Muy sencillo, en nuestro terminal ejecutamos el Shell de mongo con el comando **mongo** e introducimos los siguiente comandos:

```
$ **mongo**
MongoDB shell version: 2.4.1  
connecting to: test  
> **show databases**
tvshows    0.203125GB  
> **use tvshows**
switched to db tvshows  
> **show collections**
system.indexes  
tvshows  
> **db.tvshows.find()**
{ "title" : "LOST", "year" : 2004, "country" : "USA", "poster" : "http://ia.media-imdb.com/images/M/MV5BMjA3NzMyMzU1MV5BMl5BanBnXkFtZTcwNjc1ODUwMg@@._V1_SY317_CR17,0,214,317_.jpg", "seasons" : 6, "genre" : "Sci-Fi", "summary" : "The survivors of a plane crash are forced to live with each other on a remote island, a dangerous new world that poses unique threats of its own.", "_id" : ObjectId("51b44d7899ac57fb18000002"), "__v" : 0 }
```

Y ahi la tenemos, puedes probar a introducir alguna más, para tener mayor contenido con el que probar. Una vez introduzcas varios registros, puedes probar a llamarlos a través de la petición **GET** que hemos preparado: **http://localhost:3000/tvshows** la cuál te mostrará algo parecido a esto:

![Pantallazo 4](http://imageshack.com/a/img661/4383/LMm1iS.png)

También podemos llamar a un sólo registro gracias a la petición GET tvshows/:id que programamos, si ejecutamos por ejemplo **http://localhost:3000/tvshow/51b44d7899ac57fb18000002** nos devolverá un único objeto:

![Pantallazo 5](http://imageshack.com/a/img905/718/lAPvmS.png)

Los métodos restantes **PUT** y **DELETE** funcionan de manera parecida al **POST** solo que hay que pasarte el valor del ID del objeto que queremos actualizar o borrar. 

Con esto tendríamos el funcionamiento básico y programación de lo que sería una API REST. 

