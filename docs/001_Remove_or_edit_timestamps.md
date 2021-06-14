# Eliminar o editar las columnas timestamps de las migraciones

Una migración y un modelo comúnmente se ven así:

**Modelo *app/Post.php***
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;


// Modelo común
class Post extends Model
{
    use HasFactory;
}
```

**Migración *database/migration/2021_06_14_030656_create_posts_table.php***
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreatePostsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        // Migración común
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('content');
            $table->enum('status', ['published', 'draft']);
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('posts');
    }
}
```

## Eliminar las columnas timestamp

En el modelo, debemos de agregar la variable `$timestamps` con valor `false`.

**La clase del modelo: *app/PostWithoutTimestamp.php***
```php
class PostWithoutTimestamp extends Model
{
    use HasFactory;

    public $timestamps = false;
}

```

En la migración, debemos de eliminar la siguiente línea: 

```php
$table->timestamps();
```

**Función up() en la migración *database/migration/2021_06_14_031510_create_post_without_timestamps_table.php***
```php
    public function up()
    {
        Schema::create('post_without_timestamps', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('content');
            $table->enum('status', ['published', 'draft']);
        });
    }
```

Si desea que en todas las tablas de su proyecto no existan estas columnas, es posible cambiando la variable `$timestamps` de `true` a `false` en el trait `HasTimestamps.php` dentro de la carpeta vendor.

## Editar el nombre de las columnas timestamp

En el modelo, debemos de agregar las variables constantes `CREATED_AT` y `UPDATED_AT` con el nombre como valor asignado.

**La clase del modelo: *app/PostDiffTimestamp.php***
```php
class PostDiffTimestamp extends Model
{
    use HasFactory;

    const CREATED_AT = 'created_date';
    const UPDATED_AT = 'updated_date';
}
```

En la migración, debemos de remplazar `$table->timestamps();` por: 

```php
$table->timestamp('new_name_created')->nullable();
$table->timestamp('new_name_updated')->nullable();
```

**Función up() en la migración *database/migration/2021_06_14_034715_create_post_diff_timestamps_table.php***
```php
    public function up()
    {
        Schema::create('post_diff_timestamps', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('content');
            $table->enum('status', ['published', 'draft']);
            $table->timestamp('created_date')->nullable();
            $table->timestamp('updated_date')->nullable();
        });
    }
```

Si desea colocar un mismo nombre a estas columnas en todo el proyecto, deberá de editar el valor de las constantes `CREATED_AT` y `UPDATED_AT` dentro de la clase abstracta `Model.php` dentro de la carpeta vendor.
