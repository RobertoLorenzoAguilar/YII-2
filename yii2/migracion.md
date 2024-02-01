# Migraciones

[Documentación oficial](https://www.yiiframework.com/doc/guide/2.0/es/db-migrations)

- [Crear una migración](#crear-una-migración)
- [Ejecutar migracion](#ejecutar-migración)
- [Métodos disponibles](#lista-de-métodos-disponibles-para-la-migración)

## Crear una migración

```
php yii migrate/create :nombre_migración:
```

donde:
*:nombre_migracion:* es el nombre con el que se creará la migración

ejemplo:

```
php yii migrate/create catalogo_producto
```

lo anterior generará un archivo PHP con el nombre m221017_115001_catalogo_producto.php con el siguiente esqueleto de código:

```
<?php

use yii\db\Migration;

class m221017_115001_catalogo_producto extends Migration {

  /*
  public function up() {
  }

  public function down() {
  }
  */

  // Siempre usar safeUp y safeDown porque se ejecuta con transacción de base de datos
  public function safeUp() {
  }

  public function safeDown() {
  }

}
```
En el método *safeUp* debe contener los cambios de la base de datos y en el método *safeDown* se deben 
revertir los cambios de *safeUp*.

## Ejecutar migración

Para aplicar los cambios escritos en el método safeUp usar el comando:
```
php yii migrate
```

Para revertir los cambios aplicados usar el comando:
```
php yii migrate/down
```

## Lista de métodos disponibles para la migración
Debajo hay una lista de todos los métodos de acceso a la base de datos:
  - [execute()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#execute()-detail): ejecuta una declaración SQL
  - [insert()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#insert()-detail): inserta un único registro
  - [batchInsert()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#batchInsert()-detail): inserta múltiples registros
  - [update()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#update()-detail): actualiza registros
  - [delete()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#delete()-detail): elimina registros
  - [createTable()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#createTable()-detail): crea una nueva tabla
  - [renameTable()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#renameTable()-detail): renombra una tabla
  - [dropTable()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#dropTable()-detail): elimina una tabla
  - [truncateTable()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#truncateTable()-detail): elimina todos los registros de una tabla
  - [addColumn()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#addColumn()-detail): agrega una columna
  - [renameColumn()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#renameColumn()-detail): renombra una columna
  - [dropColumn()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#dropColumn()-detail): elimina una columna
  - [alterColumn()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#alterColumn()-detail): modifica una columna
  - [addPrimaryKey()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#addPrimaryKey()-detail): agrega una clave primaria
  - [dropPrimaryKey()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#dropPrimaryKey()-detail): elimina una clave primaria
  - [addForeignKey()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#addForeignKey()-detail): agrega una clave foránea
  - [dropForeignKey()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#dropForeignKey()-detail): elimina una clave foránea
  - [createIndex()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#createIndex()-detail): crea un índice
  - [dropIndex()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#dropIndex()-detail): elimina un índice
  - [addCommentOnColumn()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#addCommentOnColumn()-detail): agrega un comentario a una columna
  - [dropCommentFromColumn()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#dropCommentFromColumn()-detail): elimina un comentario de una columna
  - [addCommentOnTable()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#addCommentOnTable()-detail): agrega un comentario a una tabla
  - [dropCommentFromTable()](https://www.yiiframework.com/doc/api/2.0/yii-db-migration#dropCommentFromTable()-detail): elimina un comentario de una tabla
