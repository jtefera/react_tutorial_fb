React Tutorial
==============

#Installation
From (Facebook)[https://facebook.github.io/react/docs/tutorial.html].
Begin by creating a server.js file on the work directory that contains the code from [https://github.com/reactjs/react-tutorial/blob/master/server.js].

Install in that directory express and body-parser through npm:
````
npm install express
npm install body-parser
````
Call on node the server file
````
node server.js
````
The port of that server is the 3000

Follow the rest of the tutorial!

___
Hemos creado una variable que contiene una clase de React que llamado como React.createClass. Es un objeto.
El único método de dicha clase es render que contiene una función que lo único que hace es devolver un JSX, es decir un código que es a medio camino JS y a medio camino HTML

Una vez acabado dicha función, al final del código llamamaos a una función ReactDOM.render dentro de la cual ponemos JSX en el que tenemos un elemento del tipo HTML con su tag name el nombre de la variable anterior una coma, y luego el elemento del DOM en el que el código anterior se renderizará
````
 // tutorial1.js
  var CommentBox = React.createClass({
    render: function() {
      return (
        <div className="commentBox">
          Hello, world! I am a CommentBox.
        </div>
      );
    }
  });
  ReactDOM.render(
    <CommentBox />,
    document.getElementById('content')
  );
````

El objeto creado por React.createClass crea un un componente de React cuyo método más importante es render que crea un árbol React que renderizará en HTML

En realidad, aunque lo parezca no estamos escribiendo HTML dentro de React sino que estamos creando marcadores que el motor de React sabe como crear para transformar, por ejemplo, esos divs en elementos de React del  mismo tipo anterior.

El arbol que hay dentro de render puede estar formado por elmeentos "html" o por elementos creados por nosotrosmismos como <CommentBox />.

ReactDOM.render() inicializa la raiz sobre la cual se cimentarán todos los árboles hechos con React. Se coloca la final del todo una vez que los componentes como CommentBox han sido ya definidos. No Hoisting!. Despuez de iniciar la raiz, empieza el framework y añade los markup creados y renderizados dentro deun elemento DOM real que se le pasa como su segundo argument.

Es por tanto entender la naturaleza de componentes que tiene React en el que vamos creando elementos y renderizandolos.

El proyecto que estamos haciendo es una app que tiene una lista de comentarios. Debajo del todo, tenemos un formulario donde tenmos inputs que nos permintiran poner nuestro nombre y el comentario que deseamos y añadirlos a dicha lista de comentarios.

Por lo tanto, los elementos que formaran nuestro arbol son:
````
- CommentBox
    -CommentList
        -Comment
        -Comment
    -CommentForm
````

Donde_
* CommentBox es el container que guardará nuestra app. 
* CommentList es el container que guardará todos los comentarios añadidos
* Comment es un comentario cualquiera. Contiene el nombre y el comentario de la persona.
* CommentForm contiene el formulario con el que podremos añadir más comentarios.

Vamos a comenzar creando los Componentes de CommentList y CommentForm que de momento serán solo dos elementos <div>

````
var CommentList = React.createClass({
   render: function() {
    return (
        <div className="commentList">
            Hello to the CommentList
        </div>
    );
   } 
});

var CommentForm = React.createClass({
    render: function() {
        return (
            <div className="commentForm">
                This is the CommentForm
            </div>
        );
    }
});
````

Vamos a modificar el componente CommentBox para que represente el arbol mostrado más arriba
````
var CommentBox = React.creatClass({
   render: function() {
    return (
      <div className="commentBox">
        <CommentList />
        <CommentForm />
      </div>
    );
   } 
});

````

Vamos a modificar el RenderDom.render para que añada dichos elementos siguiendo el arbol que hemos definido más arriba:
````
RenderDOM.render(
    <CommentBox />
, document.getElementById('content'));
````

Algo poderoso que tienen los componentes de React es que podemos pasarle argumentos. La forma en la que se pasan los argumentos es muy similar a la forma en la se escriben los elementos en HTML:
    
    <NombreDelComponente nombreArgumento = "valorArgumento">
        children
    </NombredelComponente>

La forma que tienen los componentes de obtener dichos argumentos es a traves de this.props.nombreArgumento el cual devuelvo el valor. props es por tanto, una propiedad de cada objeto de la clase de React. Para poder hacer uso de ellos(y en general, para poder usar cualquier código javascript) dento del JSX, hay que escribirlo entre llaves {}. Como ejemplo de esto, vamos a crear el componente Comments, el único que nos falta del arbol anterior, que va a recibir un argumento el de author. Aparte, una propiedad importante de props es la de children que nos devuelve lo que hay entre el tag de apertura y el de cerrar como el mostrado arriba:

````
var Comment = React.createClass({
   render: function() {
    return (
        <div className="comment">
            <h2 className="commentAuthor"> 
                {this.props.author}
            </h2>
            <p> 
                {this.props.children}
            </p>
        </div>
    )
   } 
});
````

Vamos a añadir dos comentarios aleatorios. Como vemos en el arbol de arriba, los comentarios están dentro de CommentList por lo que tendremos que modificarlo:

````
var CommentList = React.createClass({
    render: function() {
        return (
            <div className="commentList">
                <Comment author="Jonathan Tefera">
                    Este es el children de este commentario
                </Comment>
                <Comment author="Nahum Tefera">
                    Este es el segundo comentario
                </Comment>
            </div>
        );
    }
}); 
````

React nos permite también aplicar cierto Markdown sobre el texto, como por ejemplo añadir enfasis poniendo un texto entre dos asteriscos(*). Para interpretarlo dichos markdowns, usamos la libreria marked que viene ya incluida en el index. Se utiliza pasandole un texto con dichas marcas a la función marked(). El elemento this.props.children y el elemento this.props.author no son strings sino que son objetos especiales de React. Para convertirlos a string tenemos que pasarles el método toString:
````
var Comment = React.createClass({
   render: function() {
    return (
        <div className="comment">
            <h2 className="commentAuthor"> 
                {this.props.author}
            </h2>
            <p> 
                {marked(this.props.children.toString())}
            </p>
        </div>
    )
   } 
});
````

Vamos a diseñar ahora un metodo que coja un array de autores y comentarios y crea los componentes Comment adecuados.

Definimos primero dicho array formado por objetos JSON:
````
var data = [
    {id:1, author:"Jonathan Tefera", text:"Hola al primer comentario"},
    {id:2, author:"Nahum Tefera", text:"Este es el segundo"}
];
````

La metodología con la que deberíamos trabajar con React es la de modulos/componentes. Toda la información que llega a un componente debe venir de su componente inmediatemente superior. Por ello, aun cuando data nos hace falta en CommentList, primero debemos pasarla a CommentBox para que esta la pase. A su vez, la componente superior a CommentBox es la raiz misma por lo que pasamos data desde ReactDom.render:

````
ReactDOM.render(
    <CommentBox data={data} />,
    document.getElementById('content')
);
````

A su vez, modificamos el componente CommentBox para pasar este nuevo argumento al componente CommentList:
````
var CommentBox = React.createClass({
    render: function() {
        return (
          <div className="commentBox">
            <h1>CommentariosBox</h1>
            <CommentList data={this.props.data} />
            <CommentForm />
          </div>
        );
    }
});
````

Tenemos que iterar dentro de CommentList para obtener todos los comentarios del array. Para ello, formamos dentro de CommentList una variable formada por elementos return de React formados por mapeo de los datos del array. Cada uno de dichos comments es un nodo hijo de CommentList. Cuando se crean de esta forma, es necesario que cada uno de ellos tenga un "key" distinto.
````
var CommentList = React.createClass({
    render: function() {
        //Recibimos el array data desde CommentBox 
        //a través del argumento data
        //Creamos ramas Comment que añadiremos sobre el box de CommentList
        //Creamos los nodos de dichas ramas:
        var commentNodes = this.props.data.map(function(comment){
            //commentNodes es por tanto un array formado por componente
            //Comment formado por el mapeo de los datos del array data
            return (
              <Comment author={comment.author} key={comment.id}>
                {comment.text}
              </Comment>  
            );
        });
        //El return de CommentList
        //Llamamos a los nodos commentNodes
        return (
            <div className="commentList">
                {commentNodes}
            </div>
        );
    }    
});
````
Todo lo hecho hasta aquí se puede encontrar en el archivo /public/tutorial1.html.
Las siguientes modificaciones no estarán alli.

Uno de los fuertes de React es su capacidad de refrescar toda la página de forma fluida. Esto permite que haya una conexión con un archivo, por ejemplo json, del cual cada cierto tiempo se comprueba si hay cambios. Si hay, se refresca toda la app para permitir dichos cambios.

Para empezar con ello, vamos con la primera parte y es recibir los comentarios desde una página en vez desde el array que hemos definido.

El directorio que contendrá el archivo json con los comentario será /api/comments y cambiaremos la propiedad data a url en CommentBox:
````
ReactDOM.render(
  <CommentBox url="/api/comments" />,
  document.getElementById('content');
);
````

Lo que vamos a hacer aquí es transformar la componente CommentBox para trabajar con estados. Dentro de cada componente están las propiedades props y state desde la cual obtenemos datos. props vienen dados por el nodo padre y la componente no puede hacer nada para cambiarlos mientras que state es propio del componente(no viene dado por el padre) y por lo tanto se puede cambiar. Los estados son muy importantes dentro de react ya que cuando hay cambios, activan el metodo render sobre la componente refrescandolo.

Para poder acceder a datos del states usamos la misma metodología que lo hacíamos con los props, es decir, this.state.argumento. Al igual que props, state es un array.

Para poder modificar los states, tenemos el método setState sobre la componente al que tenemos que pasarle un objeto con las claves, los argumentos que deseamos cambiar y los valores, los valores que queremos para dichos parametros.

Para este proyecto, vamos a llamar a través de ajax a la dirección pasada a CommentBox a través del argumento url.

El tipo de datos que recibiremos serán json, desactivamos el cache y cuando recibimos el archivo, actualizamos el state. Para ello, primero definimos el estado inicial. Dicho estado se puede crear añadiendo el método getInitialState:
````
var CommentBox = React.createClass({
    getInitialState: function() {
        return {data: []};
    },
    render: function() {
        return (
          <div className="commentBox">
            <CommentList data={this.state.data} />
            <CommentForm />
          </div>
        );
    }
});
````
La obtención de los archivos se hará a través de ajax. Las llamadas ajax en jQuery vienen dadas por:
````
$.ajax({
   url: "url del archivo",
   dataType: "Tipo del archivo" , //opcional
   cache: true/false, //opcional
   success: function(data) {
    //Que hacer cuando se reciben datos
   },
   error: function(xhr, status, err){
    //que hacer cuando hay error
   }
});
````

Para nuestro CommentBox, la url viene dada dentro de props, el tipo de archivo será json, no queremos que cachee los datos. La función que queremos que llame en caso se exito es que haga una actualización del estado. Para ello tenemos que pasarle la componente CommentBox para que tenga acceso a sus metodos. Tener en cuenta que el this dentro de dicha función no es el this del CommentBox. Para que lo sea, temos que hacer un binding en el que pasamos el this del Commetn Box. Como error queremos que muestre la url del archivo json, el tipo de error y el texto.

El método componentDidMount se activa cuando el componente se renderiza por primera vez.

Vamos a crear una función que hace la llamada a ajax y vamos a llamar a dicha función desde componentDidMount. También vamos a crear un intervalo que se actualiza cada cierto tiempo dado por el nodo padre a través del argumento en props de pollInterval:
````
//Container de todo. Recibe del script el array con comentarios
//Crea como hijos a CommentList y CommentForm
var CommentBox = React.createClass({
  loadCommentsFromServer: function(){
      console.log("Ajax Dandy!");
      //No es un método del prototipo de la clase
      //es creado por nosotros
      $.ajax({
          url: this.props.url,
          dataType: 'json',
          cache: false,
          success: function(data){
              this.setState({'data': data});
          }.bind(this),
          error: function(xhr, status, err){
              console.error(this.props.url, status, err.toString());
          }
      });
  },
  getInitialState: function() {
      //Es llamado cuando se hace el primer render
      return {data: []}
  },
  componentDidMount: function() {
     //Es llamado al finalizar del primer render
     //Llama a la función con el ajax y crea un intervalo para 
     //que dicha función sea llamada cada cierto tiempo 
     this.loadCommentsFromServer();
     console.log(this.props.pullInterval);
     setInterval(this.loadCommentsFromServer, this.props.pullInterval);
  },
  render: function(){
      //Función principal:
      return (
        <div className="commentBox">
          <CommentList data={this.state.data} />
          <CommentForm />
        </div>
      );
  }        
}); 
````
Modificamos también el ReactDOM:
````
ReactDOM.render(
    <CommentBox url="api/comments" pullInterval={2000} />
, document.getElementById('content'));
````

Creamos un archivo json inicial que añadiremos en la carpeta donde está el server con el nombre de 'comments.json'. Lo añdimos a la carpeta del servidor ya que el servidor está configurado de tal manera que todas las llamadas GET al url 'api/comments' apunten a la archivo /comments.json
````
[
    {
        "id": 1,
        "author": "Jonny Teferino",
        "text": "Que LoL!"
    },
    {
        "id": 2,
        "author": "Stifu Tefera",
        "text": "Hey Gangsta!"
    }
]
````

Si ponemos en marcha el servidor y accedemos a la página de inicio, nos aparecerá la página con los dos comentarios arriba descritros. Si mientras la página esta encencdida, modificamos el archivo comments.json para añadir un comentario más, cuando el setInterval llame otra vez al ajax veremos que la página cambia añadiendo el nuevo comentario:
````
[
    {
        "id": 1,
        "author": "Jonny Teferino",
        "text": "Que LoL!"
    },
    {
        "id": 2,
        "author": "Stifu Tefera",
        "text": "Hey Gangsta!"
    },
    {
        "id": 3,
        "author": "Teferino",
        "text": "Hello Dandy!"
    }
]
````

Ahora un paso importante es construir CommentForm para que podamos añadir comentarios a dicho archivo comments. CommentForm será un formulario con tres inputs, los dos primeros de tipo text que contendrán al autor y al texto y un último que será el botón de submit:
````
//Formulario que nos permitira crear nuevos comentarios
var CommentForm = React.createClass({
  render: function(){
      return (
          <form className="commentForm">
              <input 
                  type="text"
                  className="author"
                  placeholder="Your name"
                  value="Jonathan"
              />
              <input
                  type="text"
                  className="comment"
                  placeholder="Your comment"
                  value="Commentario"
              />
              <input
                  type="Submit"
                  value="Post!"
              />
          </form>
      );
  }
});
````

Los formularios y los inputs tienen propiedades especiales en React. Lanzan los siguientes eventos:
* onChange: Se lanza cuando se escribe un caracter en input o text area, se chequea un checkbox o radio o se seleciona en un selected
* onInput: NO USAR. Parecido a onChange
* onSubmit: Cuando se presióna el botón de submit

Como se ha dicho antes, el modo con el que  React trabaja es con states. Un state describe cada parte del app por lo que cuando alguien escribe en uno de los inputs, a no ser que el state sea actualizado, lo que escribe no será escrito sino que estará todo el tiempo el value a cada input asignado  arriba.

La solución a esto es trabajar otra vez con states que se actualizan cada vez que hay un onChange event. Los estados que serán cambiados serán el de autor y el del texto. Para ello, a cada input de texto le añadimos el eventHandler de html onChange y lo acoplaremos a la función que se encargará de tomar el valor del texto que se está introduciendo y actualizarlo en el estado. A su vez, el valor de cada input vendrá dado por el valor de su estado correspondiente.

El evento change que onChange capta tiene las la propiedad importante de target que hace referencia al elemento que lo ha lanzado. Por tanto, tendrá también acceso a todas las propiedades de dicho elemento:

````
//Formulario que nos permitira crear nuevos comentarios
var CommentForm = React.createClass({
    //Inicializamos el formulario con los valores de autor y texto vacios:
    getInitialState: function() {
        return {
            author: '',
            text: ''
        };
    },
    //Función que será llamada cada vez que hay un onChange event para el autor:
    handleAuthorChange: function(e) {
        //e de event
        //this apunto a nuestro componente
        //Cambiamos el estado del autor al texto que hay añadido al input
        //Al cambiar el estado, se llama a render
        this.setState({author: e.target.value});
    },
    //Función que será llamada cada vez que hay un onChange event para el autor:
    handleTextChange: function(e) {
        //e de event
        //this apunto a nuestro componente
        //Cambiamos el estado del texto al texto que hay añadido al input
        //Al cambiar el estado, se llama a render
        this.setState({text: e.target.value});
    },
    render: function() {
        return (
            <form className="CommentForm">
                <input
                    type="text"
                    className="author"
                    placeholder="Your name"
                    onChange={this.handleAuthorChange}
                    value={this.state.author}
                />  
                <input 
                    type="text"
                    className="text"
                    placeholder="Your text"
                    onChange={this.handleTextChange}
                    value={this.state.text}
                />
                <input 
                    type="submit"
                    value="Post it!"
                />
            </form>
        );
    }
});

````

Cuando presionamos un botón submit, el navegador envía una solicitud GET o POST(según el método especificado) al servidor a la url que se le designa. En este caso, no se ha designado ni método ni dirección por lo que se hará por GET a la página actual. Eso hace que la página se actualice. Lo que nosotros deseamos es que eso no ocurra sino que sea React mismo la encargada de trabajar con actualizar y enviar el formulario. Para ello, tenemos que prevenir primera mente las acciones por defecto.

Para ello usaremos el eventHandler de onSubmit para captar cuando se presiona submit. Asigaremos dicho método al formulario y su proposito es que prevenga de momento dicha accióna y que reininice los campos de los inputs:
````
//Formulario que nos permitira crear nuevos comentarios
var CommentForm = React.createClass({
    //Inicializamos el formulario con los valores de autor y texto vacios:
    getInitialState: function() {
        return {
            author: '',
            text: ''
        };
    },
    //Función que será llamada cada vez que hay un onChange event para el autor:
    handleAuthorChange: function(e) {
        //e de event
        //this apunto a nuestro componente
        //Cambiamos el estado del autor al texto que hay añadido al input
        //Al cambiar el estado, se llama a render
        this.setState({author: e.target.value});
    },
    //Función que será llamada cada vez que hay un onChange event para el autor:
    handleTextChange: function(e) {
        //e de event
        //this apunto a nuestro componente
        //Cambiamos el estado del texto al texto que hay añadido al input
        //Al cambiar el estado, se llama a render
        this.setState({text: e.target.value});
    },
    handleSubmit: function(e){
        //e de event de submit
        //this apunto a nuestro componente
        //Prevenimos la acción por defecto de submit con el método
        //preventDefault()
        //Reininciamos los estados de autor y texto del componente
        e.preventDefault();
        this.setState({
            author: "",
            text: ""
        });
    },
    render: function() {
        return (
            <form className="CommentForm" onSubmit={this.handleSubmit}>
                <input
                    type="text"
                    className="author"
                    placeholder="Your name"
                    onChange={this.handleAuthorChange}
                    value={this.state.author}
                />  
                <input 
                    type="text"
                    className="text"
                    placeholder="Your text"
                    onChange={this.handleTextChange}
                    value={this.state.text}
                />
                <input 
                    type="submit"
                    value="Post it!"
                />
            </form>
        );
    }
});

````

Cuando enviamos el comentario, queremos que nuestra app suba el comentario al archivo json y que refresque la lista de comentarios para que añada dicho comentario.

Ese último requerimiento lo tiene que hacer CommentBox ya que es el encargado de tanto renderizar la app como dar datos al CommentList. POr lo tanto, tenemos que hacer algo para que cuando enviemos el comentario, lanze un método en CommentBox que lo renderice. Es aquí donde entramos el pasar callbacks como props.

Con ello queremos decir es que definimos un método dentro de CommentBox que reciba como argumento un objeto comentario(que incluira el autor y el texto). Pasamos a ese método como un props a CommentForm para que cuando se produzca el submit event lanze tanto su función de que trata con submits como el método que le hemos pasado.

La función dentro de CommentBox que vamos a crear por su parte deberá subir por POST dicho comentario. Llamaremos a dicho método handleCommentSubmit y le asignaremos al CommentForm como un prop al argumento onCommentSubmit
````
//Container de todo. Recibe del script el array con comentarios
//Crea como hijos a CommentList y CommentForm
var CommentBox = React.createClass({
  loadCommentsFromServer: function(){
      console.log("Ajax Dandy!");
      //No es un método del prototipo de la clase
      //es creado por nosotros
      $.ajax({
          url: this.props.url,
          dataType: 'json',
          cache: false,
          success: function(data){
              this.setState({'data': data});
          }.bind(this),
          error: function(xhr, status, err){
              console.error(this.props.url, status, err.toString());
          }
      });
  },
  getInitialState: function() {
      //Es llamado cuando se hace el primer render
      return {data: []}
  },
  componentDidMount: function() {
     //Es llamado al finalizar del primer render
     //Llama a la función con el ajax y crea un intervalo para 
     //que dicha función sea llamada cada cierto tiempo 
     this.loadCommentsFromServer();
     console.log(this.props.pullInterval);
     setInterval(this.loadCommentsFromServer, this.props.pullInterval);
  },
  handleCommentSubmit: function(comment) {
    //TODO: subir al server y refrescar la lista
  },
  render: function(){
      //Función principal:
      return (
        <div className="commentBox">
          <CommentList data={this.state.data} />
          <CommentForm onCommentSubmit={this.handleCommentSubmit} />
        </div>
      );
  }        
}); 
````
Por su parte, CommentForm debe llamar a dicho método cuando se produzca el submit:
````
var CommentForm = React.createClass({
  //Inicializamos el formulario con los valores de autor y texto vacios:
  getInitiaSltate: function() {
      return {
          author: '',
          text: ''
      };
  },
  //Función que será llamada cada vez que hay un onChange event para el autor:
  handleAuthorChange: function(e) {
      //e de event
      //this apunto a nuestro componente
      //Cambiamos el estado del autor al texto que hay añadido al input
      //Al cambiar el estado, se llama a render
      this.setState({author: e.target.value});
  },
  //Función que será llamada cada vez que hay un onChange event para el autor:
  handleTextChange: function(e) {
      //e de event
      //this apunto a nuestro componente
      //Cambiamos el estado del texto al texto que hay añadido al input
      //Al cambiar el estado, se llama a render
      this.setState({text: e.target.value});
  },
  handleSubmit: function(e){
      //e de event de submit
      //this apunto a nuestro componente
      //Prevenimos la acción por defecto de submit con el método
      //preventDefault()
      //Reininciamos los estados de autor y texto del componente
      e.preventDefault();
      //Llamamos al método de CommentBox que trata con comment submits:
      this.props.onCommentSubmit({
        author: this.state.author,
        text: this.state.text
      });
      this.setState({
          author: "",
          text: ""
      });
  },
  render: function() {
      return (
          <form className="CommentForm" onSubmit={this.handleSubmit}>
              <input
                  type="text"
                  className="author"
                  placeholder="Your name"
                  onChange={this.handleAuthorChange}
                  value={this.state.author}
              />  
              <input 
                  type="text"
                  className="text"
                  placeholder="Your text"
                  onChange={this.handleTextChange}
                  value={this.state.text}
              />
              <input 
                  type="submit"
                  value="Post it!"
              />
          </form>
      );
  }
});
````

Ahora subiremos el comentario al servidor. Si vemos el servidor vemos que tenemos un código basado en Express que dice:
````
app.post('/api/comments', function(req, res) {
  fs.readFile(COMMENTS_FILE, function(err, data) {
    if (err) {
      console.error(err);
      process.exit(1);
    }
    var comments = JSON.parse(data);
    // NOTE: In a real implementation, we would likely rely on a database or
    // some other approach (e.g. UUIDs) to ensure a globally unique id. We'll
    // treat Date.now() as unique-enough for our purposes.
    var newComment = {
      id: Date.now(),
      author: req.body.author,
      text: req.body.text,
    };
    comments.push(newComment);
    fs.writeFile(COMMENTS_FILE, JSON.stringify(comments, null, 4), function(err) {
      if (err) {
        console.error(err);
        process.exit(1);
      }
      res.json(comments);
    });
  });
});
````
EL cual nos indica lo que hay que hacer para un POST requests a '/api/comments'.  Abre en modo r+(lectura/escritura con error si el archivo no existe) el archivo definido por la ruta COMMENT_FILE que viene definido dentro del servidor como:
````
var COMMENTS_FILE = path.join(__dirname, 'comments.json');
````
Es decir, nuestro archivo json que contiene todos los archivos. El argumento data que pasamos a la función anónima en readFile es un string que viene de un JSON al que se le ha aplicado el método stringify. Por tanto, debemos transforma data a json. 

Posteriormente, creamos la función newComment que creara un objeto del commetario que le pasaremos por ajax. Añade dicho comentario al json y escirbe dicho json al comments.json. res.body hace referencia a lo que pasamos en la propiedad data de ajax. COmo respuesta, el servidor vuelve a enviarnos el el json de comments.json con el nuevo comentario añadido

La llamada será por tanto:
````
$.ajax({
        url: '/api/comments',
        dataType: 'json',
        type: 'POST',
        data: comment,
        success: function(data){
            //Esto se añadirá a CommentBox.
            //data contiene la lista de comentarios
            //Debe formar parte del state data de CommentBox
            //this en este caso hace referencia a este objeto,
            //hacemos el bind para que haga referencia al CommentBox
            this.setState({'data': data});
        }.bind(this),
        error: function(xhr, status, err){
            console.error(this.props.url, status, err.toString());
        }.bind(this)
});
````
El cual deberá ser llamado dentro de CommentBox cuando se hace el submit:
````
//Container de todo. Recibe del script el array con comentarios
//Crea como hijos a CommentList y CommentForm
var CommentBox = React.createClass({
loadCommentsFromServer: function(){
    //No es un método del prototipo de la clase
    //es creado por nosotros
    $.ajax({
        url: this.props.url,
        dataType: 'json',
        cache: false,
        success: function(data){
            this.setState({'data': data});
        }.bind(this),
        error: function(xhr, status, err){
            console.error(this.props.url, status, err.toString());
        }
    });
},
getInitialState: function() {
    //Es llamado cuando se hace el primer render
    return {data: []}
},
componentDidMount: function() {
   //Es llamado al finalizar del primer render
   //Llama a la función con el ajax y crea un intervalo para 
   //que dicha función sea llamada cada cierto tiempo 
   this.loadCommentsFromServer();
   setInterval(this.loadCommentsFromServer, this.props.pullInterval);
},
handleCommentSubmit: function(comment) {
    
    //Enviando al servidor el comentario. 
    //Tras acabar, actualiza el stado con la nueva lista de comentarios
    $.ajax({
        url: this.props.url,
        dataType: 'json',
        type: 'POST',
        data: comment,
        success: function(data){
            //Esto se añadirá a CommentBox.
            //data contiene la lista de comentarios
            //Debe formar parte del state data de CommentBox
            //this en este caso hace referencia a este objeto,
            //hacemos el bind para que haga referencia al CommentBox
            this.setState({'data': data});
        }.bind(this),
        error: function(xhr, status, err){
            console.error(this.props.url, status, err.toString());
        }.bind(this)
    });
},
render: function(){
    //Función principal:
    return (
      <div className="commentBox">
        <CommentList data={this.state.data} />
        <CommentForm onCommentSubmit={this.handleCommentSubmit} />
      </div>
    );
}        
}); 

````

Una última optimización que podemos hacer es lo que se llama como "optimistic update", o actualización optimista. Cuando enviamos el comentario, antes de que se actualice la lista pasan un par de cosas. Primero hay que enviarlo a través de ajax y luego hay que esperar la respuesta. Esto podría tardar su tiempo y hacer que el flujo de escrtura no se sienta normal para el usuario. Una solución es actualizar la data añadiendo el comentario al array hasta que el array tenga el comentario real. De esta forma, el comentario añadido ocupa su lugaar. El cambió es muy sencillo ya que soloo hay que modificar la función handleCommentSubmit de CommentBox:
````
//Container de todo. Recibe del script el array con comentarios
//Crea como hijos a CommentList y CommentForm
var CommentBox = React.createClass({
loadCommentsFromServer: function(){
  //No es un método del prototipo de la clase
  //es creado por nosotros
  $.ajax({
      url: this.props.url,
      dataType: 'json',
      cache: false,
      success: function(data){
          this.setState({'data': data});
      }.bind(this),
      error: function(xhr, status, err){
          console.error(this.props.url, status, err.toString());
      }
  });
},
getInitialState: function() {
  //Es llamado cuando se hace el primer render
  return {data: []}
},
componentDidMount: function() {
 //Es llamado al finalizar del primer render
 //Llama a la función con el ajax y crea un intervalo para 
 //que dicha función sea llamada cada cierto tiempo 
 this.loadCommentsFromServer();
 setInterval(this.loadCommentsFromServer, this.props.pullInterval);
},
handleCommentSubmit: function(comment) {
  //Optimistic update
  //Actualizamos la lista de comentarios añadiendo este comentario al array
  //hasta que el array sea actualizado por el ajax:
  var comments = this.state.data;
  comment.id = Date.now();
  var newComments = comments.concat([comment]);
  this.setState({'data': newComments});
  
  //Enviando al servidor el comentario. 
  //Tras acabar, actualiza el stado con la nueva lista de comentarios
  $.ajax({
      url: this.props.url,
      dataType: 'json',
      type: 'POST',
      data: comment,
      success: function(data){
          //Esto se añadirá a CommentBox.
          //data contiene la lista de comentarios
          //Debe formar parte del state data de CommentBox
          //this en este caso hace referencia a este objeto,
          //hacemos el bind para que haga referencia al CommentBox
          this.setState({'data': data});
      }.bind(this),
      error: function(xhr, status, err){
          console.error(this.props.url, status, err.toString());
          console.error(this.props.url, status, err.toString());
      }.bind(this)
  });
},
render: function(){
  //Función principal:
  return (
    <div className="commentBox">
      <CommentList data={this.state.data} />
      <CommentForm onCommentSubmit={this.handleCommentSubmit} />
    </div>
  );
}        
}); 
````