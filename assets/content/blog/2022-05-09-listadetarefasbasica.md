{
  "title": "Intro",
  "date": "2022-05-09T06:00:00.000Z",
  "description": "Sobre XML",
  "body": "##Basic Task List
[TOC]

## [Introdución](https://laravel.com/docs/5.1/quickstart#introduction)

Nesta guía de inicio rápido se ofrece unha introdución básica ao framework Laravel e inclúe contido sobre **migracións de bases de datos, Eloquent ORM, enrutamento, validación, vistas e modelos Blade**. Este é un excelente punto de partida se es novo no framework Laravel ou PHP en xeral. Se xa utilizaches Laravel ou outros frameworks PHP, podes consultar un dos nosos inicios rápidos - *quickstart*- máis avanzados. Para mostrar unha selección básica de funcións de Laravel, **crearemos unha lista de tarefas sinxela que podemos usar para rastrexar todas as tarefas que queremos realizar** (o típico exemplo de "lista de tarefas"). O código fonte completo e rematado deste proxecto está dispoñible en GitHub](http://github.com/laravel/quickstart-basic).



## [Instalación](https://laravel.com/docs/5.1/quickstart#installation)

Por suposto, primeiro necesitarás unha nova instalación do framework Laravel. Podes usar a [máquina virtual Homestead](https://laravel.com/docs/5.1/homestead) ou o ambiente PHP local que elixas para executar o marco. Unha vez que o teu ambiente local estea listo, podes instalar o framework Laravel usando Composer:

```
composer create-project laravel/laravel quickstart --prefer-dist
```

Podes ler o resto deste inicio rápido; non obstante, se desexas descargar o código fonte deste inicio rápido e executalo na túa máquina local, podes clonar o repositorio Git e instalar as súas dependencias:

```
git clone https://github.com/laravel/quickstart-basic quickstart
cd quickstart
composer install
php artisan migrate
```

Para obter unha documentación máis completa sobre a construción dun ambiente de desenvolvemento Laravel local, consulta a documentación completa de [Homestead](https://laravel.com/docs/5.1/homestead) e a de [instalación](https://laravel.com/docs/5.1/installation).

## [Preparando a Base de Datos](https://laravel.com/docs/5.1/quickstart#prepping-the-database)

### [Migracións de bases de datos](https://laravel.com/docs/5.1/quickstart#database-migrations)

En primeiro lugar, usaremos a migración para definir unha táboa de base de datos que gardará todas as nosas tarefas. As migracións de bases de datos de Laravel proporcionan un xeito sinxelo de definir a estrutura e modificacións da súa táboa de base de datos mediante código PHP fluido e expresivo. En lugar de dicirlles aos membros do teu equipo que engadan columnas manualmente á súa copia local da base de datos, os teus compañeiros de equipo poden simplemente executar as migracións que introduzas no control de fontes.

Entón, imos construír unha táboa de base de datos que albergará todas as nosas tarefas. Aquí  [Artisan CLI](https://laravel.com/docs/5.1/artisan) pódese usar para xerar unha variedade de clases e aforrarache moito escribir mentres creas os teus proxectos Laravel. Neste caso, usemos o comando ``make:migration`` para xerar unha nova migración de base de datos para a nosa táboa de ``tarefas``&rarr; ``tasks``:

```
php artisan make:migration create_tasks_table --create=tasks
```

A migración colocarase no directorio `database/migrations` do teu proxecto. Como podes ter notado, o comando ``make:migration`` xa engadiu un ID con incremento automático e marcas de tempo ao arquivo de migración. Imos editar este arquivo e engadir unha columna de cadea adicional para o nome das nosas tarefas (ou todos aqueles elementos e o seu tipo para a nova táboa que imos construir):

```
<?php
 
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;
 
class CreateTasksTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('tasks', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
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
        Schema::drop('tasks');
    }
}
```

Cada táboa da base de datos deberá ter o seu propio arquivo de migración coas definicións que atinxen aos elementos desa táboa.

Para executar a migración, usaremos o comando ``migrate`` de Artisan. Se estás a usar Homestead, debes executar este comando desde a túa máquina virtual, xa que a túa máquina host non terá acceso directo á base de datos:

```
php artisan migrate
```

Este comando creará todas as nosas táboas de base de datos. Se inspeccionas as táboas da base de datos, usando o cliente de base de datos que queiras, deberías ver unha nova táboa de  ``tarefas``&rarr; ``tasks`` que contén as columnas definidas na nosa migración. A continuación, estamos preparados para definir un modelo Eloquent ORM para as nosas tarefas.

### [Modelos Eloquent](https://laravel.com/docs/5.1/quickstart#eloquent-models)

[Eloquent](https://laravel.com/docs/5.1/eloquent) é o ORM (object-relational mapper) por defecto de Laravel. Eloquent fai que sexa indoloro recuperar e almacenar datos na súa base de datos utilizando "modelos" claramente definidos. Normalmente, cada modelo Eloquent correspóndese directamente cunha única táboa de base de datos.

Entón, imos definir un modelo de tarefas que se corresponda coa táboa de base de datos de tarefas que acabamos de crear. De novo, podemos usar un comando Artisan para xerar este modelo. Neste caso, usaremos o comando ``make:model``:

```
php artisan make:model Task
```

O modelo colocarase no directorio ``app`` da túa aplicación. Por defecto, a clase do modelo está baleira. A este modelo Eloquent non lle temos que dicir explicitamente a que táboa corresponde porque suporá que a (súa) táboa da base de datos é a forma plural do nome do modelo. Polo tanto, neste caso, suponse que o modelo de ``task`` corresponde á táboa de base de datos de ``tarefas``&rarr; ``tasks`` . Aquí tes como debería ser o noso modelo baleiro:

```
<?php
 
namespace App;
 
use Illuminate\Database\Eloquent\Model;
 
class Task extends Model
{
    //
}
```

Aprenderemos máis sobre como usar modelos Eloquent mentres engadimos rutas á nosa aplicación. Por suposto, non dubides en consultar a [documentación completa de Eloquent](https://laravel.com/docs/5.1/eloquent) para obter máis información.

## [Enrutamento](https://laravel.com/docs/5.1/quickstart#routing)

### [Troceando As Rutas](https://laravel.com/docs/5.1/quickstart#stubbing-the-routes)

A continuación, estamos listos para agregar algunhas rutas á nosa aplicación. As rutas se utilizan para apuntar direccións URL a controladores o funcións anónimas que deben executarse cando un usuario accede a unha páxina determinada. De forma predeterminada, todas as rutas de Laravel se definen no arquivo ``app/Http/routes.php`` que se inclúe en cada ~~novo~~ proxecto.

Para esta aplicación, sabemos que necesitaremos polo menos tres rutas: unha ruta para mostrar unha lista de todas as nosas tarefas, unha ruta para engadir novas tarefas e unha ruta para eliminar tarefas existentes. Entón, introduzamos todas estas rutas no arquivo `app/Http/routes.php`:

```
<?php
 
use App\Task;
use Illuminate\Http\Request;
 
/**
 * Display All Tasks
 */
Route::get('/', function () {
    //
});
 
/**
 * Add A New Task
 */
Route::post('/task', function (Request $request) {
    //
});
 
/**
 * Delete An Existing Task
 */
Route::delete('/task/{id}', function ($id) {
    //
});
```



### [Amosando unha vista](https://laravel.com/docs/5.1/quickstart#displaying-a-view)

A continuación, enchemos a nosa ruta`/`- orixe ou home-. Desde esta ruta, queremos renderizar un deseño HTML que conteña un formulario para engadir novas tarefas, así como unha lista de todas as tarefas actuais.

En Laravel, todos os modelos HTML almacénanse no directorio de ``resources/views``, e podemos usar o asistente de visualización - `view` helper - para devolver un destes deseños da nosa ruta:

```
Route::get('/', function () {
    return view('tasks');
});
```

Por suposto, imos necesitar definir esa vista realmente, así que imos alo!



## [Deseños de construción e vistas](https://laravel.com/docs/5.1/quickstart#building-layouts-and-views)

Esta aplicación só ten unha única vista que contén un formulario para engadir novas tarefas, así como unha lista de todas as tarefas actuais. Para axudarche a visualizar a vista, aquí tes unha captura de pantalla da aplicación rematada co estilo básico CSS de Bootstrap aplicado:

![Application Image](https://laravel.com/assets/img/quickstart/basic-overview.png)



### [Definición do deseño](https://laravel.com/docs/5.1/quickstart#defining-the-layout)

Case todas as aplicacións web comparten o mesmo deseño nas páxinas. Por exemplo, ~~esta aplicación ten~~  unha barra de navegación superior que normalmente estaría presente en todas as páxinas (se tivésemos máis dunha), un pé de páxina, unha zona para distribuir os contidos, ... Laravel facilita compartir estas funcións comúns en todas as páxinas usando deseños Blade.

Como se comentou anteriormente, todas as vistas de Laravel almacénanse en  `resources/views`. Entón, imos definir unha nova vista de deseño en  `resources/views/layouts/app.blade.php`. A extensión ``.blade.php`` indícalle ao framework que use o motor de modelos Blade para renderizar a vista -  [Blade templating engine](https://laravel.com/docs/5.1/blade)-. Por suposto, **podes usar modelos PHP simples** con Laravel. Non obstante, Blade ofrece atallos cómodos para escribir modelos máis limpos e concisos.

A vista `app.blade.php` debería parecerse a seguinte:

```
// resources/views/layouts/app.blade.php
 
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>Laravel Quickstart - Basic</title>
 
        <!-- CSS And JavaScript -->
    </head>
 
    <body>
        <div class="container">
            <nav class="navbar navbar-default">
                <!-- Navbar Contents -->
            </nav>
        </div>
 
        @yield('content')
    </body>
</html>
```

Ter en conta a parte `@yield('content')` do deseño. Esta é unha directiva especial de Blade que especifica **onde todas as páxinas fillas** que amplían o deseño **poden inxectar o seu propio contido**. A continuación, imos definir a vista fillo que utilizará este deseño e proporcionará o seu contido principal.

### [Definición da visión fillo](https://laravel.com/docs/5.1/quickstart#defining-the-child-view)

Xenial, o noso deseño da aplicación está rematado. A continuación, necesitamos definir unha vista que conteña un formulario para crear unha nova tarefa, así como unha táboa que enumera todas as tarefas existentes. Definamos esta visión en `resources/views/tasks.blade.php`.

Imos obviar aquí algúns dos estándares de Bootstrap CSS e centrarémonos só nas cousas que importan. Lembra que podes descargar a fonte completa desta aplicación en [GitHub](https://github.com/laravel/quickstart-basic):

```
// resources/views/tasks.blade.php
 
@extends('layouts.app')
 
@section('content')
 
    <!-- Bootstrap Boilerplate... -->
 
    <div class="panel-body">
        <!-- Display Validation Errors -->
        @include('common.errors')
 
        <!-- New Task Form -->
        <form action="/task" method="POST" class="form-horizontal">
            {{ csrf_field() }}
 
            <!-- Task Name -->
            <div class="form-group">
                <label for="task" class="col-sm-3 control-label">Task</label>
 
                <div class="col-sm-6">
                    <input type="text" name="name" id="task-name" class="form-control">
                </div>
            </div>
 
            <!-- Add Task Button -->
            <div class="form-group">
                <div class="col-sm-offset-3 col-sm-6">
                    <button type="submit" class="btn btn-default">
                        <i class="fa fa-plus"></i> Add Task
                    </button>
                </div>
            </div>
        </form>
    </div>
 
    <!-- TODO: Current Tasks -->
@endsection
```

#### Algunhas notas de aclaratorias

Antes de continuar, imos falar un pouco deste modelo. En primeiro lugar, a directiva `@extends` infórmalle a Blade de que estamos a usar o deseño que definimos en`resources/views/layouts/app.blade.php`. Todo o contido entre `@section('content')` e `@endsection` inxectarase na localización da directiva `@yield('content')` dentro do deseño `app.blade.php`.

Agora definimos un deseño básico e unha vista para a nosa aplicación. Lembra que estamos devolvendo esta vista desde a nosa ruta `/` así:

```
Route::get('/', function () {
    return view('tasks');
});
```

A continuación, estamos preparados para engadir código á nosa ruta `POST /task` para xestionar a entrada do formulario entrante e engadir unha nova *tarefa* á base de datos.

> **Nota:** A directiva `@include('common.errors')` cargará o modelo situado en `resources/views/common/errors.blade.php`. Non definimos este modelo, pero farémolo pronto!

## [Engadindo Tarefas](https://laravel.com/docs/5.1/quickstart#adding-tasks)

### [Validación](https://laravel.com/docs/5.1/quickstart#validation)

Agora que temos un formulario na nosa vista, necesitamos engadir código á nosa ruta `POST /task` para validar a entrada do formulario entrante e crear unha nova tarefa. En primeiro lugar, imos validar a entrada -*input*-.

Para este formulario, faremos o campo de nome obrigatorio e indicaremos que debe conter menos de ``255`` caracteres. Se a validación falla, redirixiremos ao usuario de volta ao URL ``/``, así como efectuaremos un *flash* sobre a entrada antiga e os erros na [session](https://laravel.com/docs/5.1/session):

```
Route::post('/task', function (Request $request) {
    $validator = Validator::make($request->all(), [
        'name' => 'required|max:255',
    ]);
 
    if ($validator->fails()) {
        return redirect('/')
            ->withInput()
            ->withErrors($validator);
    }
 
    // Create The Task...
});
```



#### A variable `$errors` 

Fagamos un momento de pausa para falar da parte `->withErrors($validator)` do exemplo. A chamada `->withErrors($validator)` mostrará os erros da instancia de validación dada na sesión para que se poida acceder a eles a través da variable ``$errors`` na nosa vista.

Lembra que usamos a directiva `@include('common.errors')` dentro da nosa vista para mostrar os erros de validación do formulario. `common.errors` permitiranos mostrar facilmente os erros de validación no mesmo formato en todas as nosas páxinas. Definamos agora o contido desta vista:

```
// resources/views/common/errors.blade.php
 
@if (count($errors) > 0)
    <!-- Form Error List -->
    <div class="alert alert-danger">
        <strong>Whoops! Something went wrong!</strong>
 
        <br><br>
 
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```



> **Nota:** A variable `$errors` é asequible en todas as vistas de Laravel. Simplemente será unha instancia baleira de ViewErrorBag se non hai erros de validación.

### [Creando a tarefa](https://laravel.com/docs/5.1/quickstart#creating-the-task)

Agora que se xestiona a validación da entrada, crearemos unha nova tarefa continuando enchendo a nosa ruta. Unha vez creada a nova tarefa, redirixiremos o usuario de volta ao URL ``/``. Para crear a tarefa, podemos usar o método de gardar - ``save``- despois de crear e configurar propiedades nun novo modelo Eloquent:

```
Route::post('/task', function (Request $request) {
    $validator = Validator::make($request->all(), [
        'name' => 'required|max:255',
    ]);
 
    if ($validator->fails()) {
        return redirect('/')
            ->withInput()
            ->withErrors($validator);
    }
 
    $task = new Task;
    $task->name = $request->name;
    $task->save();
 
    return redirect('/');
});
```

Xenial! Agora podemos crear *tarefas* con éxito. A continuación, sigamos engadindo á nosa vista creando unha lista de todas as *tarefas* existentes.

### [Mostrando tarefas existentes](https://laravel.com/docs/5.1/quickstart#displaying-existing-tasks)

En primeiro lugar, necesitamos editar a nosa ``/`` ruta para pasar todas as tarefas existentes á vista - ``view``-. A función de vista acepta un segundo argumento que é unha matriz de datos que estará dispoñible para a vista, onde cada clave da matriz converterase nunha variable dentro da vista:

```
Route::get('/', function () {
    $tasks = Task::orderBy('created_at', 'asc')->get();
 
    return view('tasks', [
        'tasks' => $tasks
    ]);
});
```

Unha vez que se pasan os datos, podemos percorrer as tarefas na nosa vista ``tasks.blade.php`` e mostralas nunha táboa. A construción ``@foreach`` Blade permítenos escribir bucles concisos que se compilan nun código PHP sinxelo e veloz:

```
@extends('layouts.app')
 
@section('content')
    <!-- Create Task Form... -->
 
    <!-- Current Tasks -->
    @if (count($tasks) > 0)
        <div class="panel panel-default">
            <div class="panel-heading">
                Current Tasks
            </div>
 
            <div class="panel-body">
                <table class="table table-striped task-table">
 
                    <!-- Table Headings -->
                    <thead>
                        <th>Task</th>
                        <th>&nbsp;</th>
                    </thead>
 
                    <!-- Table Body -->
                    <tbody>
                        @foreach ($tasks as $task)
                            <tr>
                                <!-- Task Name -->
                                <td class="table-text">
                                    <div>{{ $task->name }}</div>
                                </td>
 
                                <td>
                                    <!-- TODO: Delete Button -->
                                </td>
                            </tr>
                        @endforeach
                    </tbody>
                </table>
            </div>
        </div>
    @endif
@endsection
```

A nosa aplicación de tarefas está case completa. Pero, non temos forma de eliminar as nosas tarefas existentes cando rematen. Engadamos iso a continuación!

## [Eliminando tarefas](https://laravel.com/docs/5.1/quickstart#deleting-tasks)

### [Engadir o botón eliminar](https://laravel.com/docs/5.1/quickstart#adding-the-delete-button)

Deixamos unha nota "TODO" no noso código onde se supón que está o noso botón de eliminación. Entón, imos engadir un botón de eliminación a cada fila da nosa lista de tarefas na vista ``tasks.blade.php``. Crearemos un pequeno formulario cun só botón para cada tarefa da lista. Cando se fai clic no botón, enviarase unha solicitude de eliminar tarefa - `DELETE /task`-  á aplicación:

```
<tr>
    <!-- Task Name -->
    <td class="table-text">
        <div>{{ $task->name }}</div>
    </td>
 
    <!-- Delete Button -->
    <td>
        <form action="/task/{{ $task->id }}" method="POST">
            {{ csrf_field() }}
            {{ method_field('DELETE') }}
 
            <button>Delete Task</button>
        </form>
    </td>
</tr>
```

#### [Unha nota sobre o método de suplantación - Spoofing](https://laravel.com/docs/5.1/quickstart#a-note-on-method-spoofing)

Ter en conta que o método - `method` - de formulario do botón borrar aparece como ``POST``, aínda que esteamos respondendo á solicitude mediante unha ruta ``Route::delete``. Os formularios HTML só permiten os verbos HTTP ``GET`` e ``POST``, polo que necesitamos un xeito de *falsificar* unha solicitude ``DELETE`` do formulario.

Podemos suplantar unha solicitude ``DELETE`` mostrando os resultados da función ``method_field('DELETE')`` dentro do noso formulario. Esta función xera unha entrada de formulario oculta que Laravel recoñece e usará para anular o método de solicitude HTTP real. O campo xerado terá o seguinte aspecto:

```html
<input type="hidden" name="_method" value="DELETE">
```



### [Eliminando a tarefa](https://laravel.com/docs/5.1/quickstart#deleting-the-task)

Finalmente, imos engadir lóxica á nosa ruta para eliminar realmente a tarefa dada. Podemos usar o método de Eloquent ``findOrFail`` para recuperar un modelo por ID ou lanzar unha excepción 404 se o modelo non existe. Unha vez que recuperemos o modelo, utilizaremos o método de eliminación - `delete` - para eliminar o rexistro. Unha vez que se elimine o rexistro, redirixiremos o usuario de volta ao URL ``/`` :

```
Route::delete('/task/{id}', function ($id) {
    Task::findOrFail($id)->delete();
 
    return redirect('/');
});```\n\n"
}