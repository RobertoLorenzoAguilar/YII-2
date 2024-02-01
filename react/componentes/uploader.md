# Uploader

El componente Uploader funciona para el guardado de documentos en la base de datos usando la tabla principal **Media** y una tabla intermedia respectiva dependiendo del módulo donde se requiera el cargado de archivos.

## Requerimientos previos

La tabla de media se compone de los campos:

- id: Identificador único (lleve primaria)
- idUsuario: Identificador del usuario que subió el archivo (llave foránea)
- nombre
- uuid
- extension
- ruta
- creado
- thumbUrl
- type

La tabla media necesita tener estos campos debido a que el componente Upoload utilizado para el Uploader los requiere.

La tabla intermedia se conforma de los campos:

- idMedia
- id{Modulo}

De ser requerido se agregan campos a la tabla intermedia (como un campo de tipo para diferenciar cartegorías/tipo de archivos en distintos apartados de un mismo formulario).

El último requerimiento previo para el funcionamiento del Uploader es un controlador dedicado a la subida de los archivos en la tabla Media.
Ejemplo de controlador:

```
class SubirArchivoController extends AuthController {

  public function actionGuardar() {
    if (!$this->req->isPost) {
      throw new NotFoundHttpException();
    }

    $usuario = $this->usuario;

    $this->res->format = Response::FORMAT_JSON;
    $archivo = UploadedFile::getInstanceByName('archivo');
    if ($archivo === null) {
      return (new Respuesta())
        ->esError()
        ->mensaje("No se recibió el archivo");
    }

    $sec = Yii::$app->getSecurity();
    $base = Yii::getAlias("@app") . "/web/assets/";

    $ruta = "recurso/";
    if(!is_dir($base . $ruta)) {
      mkdir($base . $ruta);
    }

    $ruta .= date("Y/");
    if(!is_dir($base . $ruta)) {
      mkdir($base . $ruta);
    }

    $ruta .= date("m/");
    if(!is_dir($base . $ruta)) {
      mkdir($base . $ruta);
    }

    $dominio = Yii::$app->getRequest()->getHostInfo() . "/assets/";
    do {
      $nombreArchivo = str_replace("-", "", $ruta . $sec->generateRandomString());
      if($archivo->extension) {
        $nombreArchivo .= "." . $archivo->extension;
      }
    } while(is_file($base . $nombreArchivo));
    if(!$archivo->saveAs($base . $nombreArchivo)) {
      return (new Respuesta())
        ->mensaje("Ocurrió un problema al guardar el archivo");
    }

    $uuid = Uuid::uuid1();

    $modelo = new Media();

    $modelo->creado = new Expression('now()');
    $modelo->idUsuario = $usuario->id;
    $modelo->uuid = $uuid->toString();
    $modelo->nombre = $archivo->name;
    $modelo->extension = $archivo->extension;
    $modelo->ruta = $dominio.$nombreArchivo;

    $modelo->load($this->req->getBodyParams(), '');
    if (!$modelo->save()) {
      return (new Respuesta($modelo))
        ->mensaje("Hubo un problema al guardar Media");
    }

    $modelo->refresh();

    return (new Respuesta())
      ->mensaje("Archivo subido correctamente")
      ->detalle($modelo);
  }
}
```

## Implementación

### Funcionamiento

En font end se importa el componente donde se desee utilizar (en caso de no encontrarse el componente en el proyecto éste puede encontrarse en el proyecto de PBR).

Para su funcionamiento se requieren 2 estados.

```
const [listaArchivos, setListaArchivos] = useState([]);
const [referencias, setReferencias] = useState([]);
```

"listaArchivos" almacena los archivos para mostrar en preview, mientras que "referencias" guarda las respuestas de la tabla Media al guardar los archivos.

```
<Uploader
  endPoint={"v1/subir-archivo"}
  setListaArchivos={setListaArchivos}
  listaArchivos={listaArchivos}
  setReferencias={setReferencias}
/>
```

Las propiedades del componente son:

- endPoint: como su nombre lo implica, es el end point del controlador dedicado a carga de archivos.
- setListaArchivos: La propiedad set de un estado de control de la lista de archivos.
- listaArchivos: El estado de control de la lista de archivos. Esta funciona para mostrar el pequeño preview de los archivos cargados.
- setReferencias: La propiedad ser de un estado de control para los datos del archivo cargado. Esta lista nos servirá para enviar los datos de los archivos al servidor y guardar el registro en la tabla intermedia

Para enviar los archivos en el guardado del formulario sólo es necesario agregar la lista de referencias al objeto que se envía al servidor y encargarse de la inserción en la tabla intermedia desde el controlador.

### Cargado de archivos.

Al momento de cargar un formulario editado necesitaremos cargar las listas de archivos al igual que la de referencias, de lo contrario, no mostrará los archivos y tampoco los enviará al servidor, registrando entradas vacías en la tabla intermedia.

Para esto utilizamos una función CallBack que llamaremos dentro del useEffect donde se cargan los datos del formulario/módulo requerido.

```
const insertarMedia = (_modeloMedia) => {
  let _media = [];
  for (let i = 0; i < _modeloMedia?.length; i++) {
    let _obj = _modeloMedia[i];
    _obj.url = _modeloMedia[i].ruta;
    _obj.name = _modeloMedia[i].nombre;
    _media.push(_obj);
  }
  return _media;
};

React.useEffect(() => {
  if (editing && model) {
    let _listaArchivos = model?.media;
    let _media = insertarMedia(_listaArchivos);

    setListaArchivos(_media);
    setReferencias(_lsitaArchivos);
  }
}, [editing, model]);

```
De esta forma el componente mostrará los archivos previamente cargados y tendrá una lista de referencias lista para ser guardada en la tabla intermedia.