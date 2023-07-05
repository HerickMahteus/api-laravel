# api-laravel
- Inicie o Apache e o MySQL no XAMPP
# Digite no terminal:
- php artisan migrate
- php artisan serve
# Postman
### Crie uma nova Collection
### Adicione 5 requests:
- GET: Listar todas as tarefas
- GET: Obter detalhes de uma tarefa específica
- POST: Criar uma nova tarefa
- PUT: Atualizar os dados de uma tarefa existente
- DELETE: Excluir uma tarefa
### Insira o URL que foi gerado no comando php artisan serve:
- GET: URL/tasks
- GET: URL/tasks/{task}
- POST: URL/tasks
- PUT: URL/tasks/{task}
- DELETE: URL/tasks/{task}
### Nos requests POST e PUT vá em body/row, selecione JSON e adicione:

```
{
    "title": "Titulo da tarefa",
    "description": "Descrição da tarefa",
    "status": true
}
```

### Nos seguintes códigos, faça as seguintes alterações:
- app/Http/Controllers/TaskController.php

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Task;

class TaskController extends Controller
{
   public function index()
   {
       $tasks = Task::all();

       return response()->json($tasks);
   }

   public function show($id)
   {
       $task = Task::find($id);

       if ($task) {
           return response()->json($task);
       } else {
           return response()->json(['error' => 'Tarefa não encontrada'], 404);
       }
   }

   public function store(Request $request)
   {
       $request->validate([
           'title' => 'required',
           'description' => 'required',
       ]);

       $task = new Task;
       $task->title = $request->input('title');
       $task->description = $request->input('description');
       $task->status = $request->input('status', false);
       $task->save();

       return response()->json($task, 201);
   }

   public function update(Request $request, $id)
   {
       $task = Task::find($id);

       if ($task) {
           $request->validate([
               'title' => 'required',
               'description' => 'required',
           ]);

           $task->title = $request->input('title');
           $task->description = $request->input('description');
           $task->status = $request->input('status', $task->status);
           $task->save();

           return response()->json($task);
       } else {
           return response()->json(['error' => 'Tarefa não encontrada'], 404);
       }
   }

   public function destroy($id)
   {
       $task = Task::find($id);

       if ($task) {
           $task->delete();

           return response()->json(['message' => 'Tarefa excluída com sucesso']);
       } else {
           return response()->json(['error' => 'Tarefa não encontrada'], 404);
       }
   }
}
```

- database/migrations/2023_06_27_221132_create_tasks_table.php

```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateTasksTable extends Migration
{
   public function up()
   {
       Schema::create('tasks', function (Blueprint $table) {
           $table->id();
           $table->string('title');
           $table->text('description');
           $table->boolean('status')->default(false);
           $table->timestamps();
       });
   }

   public function down()
   {
       Schema::dropIfExists('tasks');
   }
}
```
- routes/api.php

```
<?php

use App\Http\Controllers\TaskController;
use Illuminate\Support\Facades\Route;

Route::get('/tasks', [TaskController::class, 'index']);
Route::post('/tasks', [TaskController::class, 'store']);
Route::get('/tasks/{task}', [TaskController::class, 'show']);
Route::put('/tasks/{task}', [TaskController::class, 'update']);
Route::delete('/tasks/{task}', [TaskController::class, 'destroy']);
```
- routes/web.php

```
<?php

use App\Http\Controllers\TaskController;
use Illuminate\Support\Facades\Route;

Route::get('/tasks', [TaskController::class, 'index']);
Route::post('/tasks', [TaskController::class, 'store']);
Route::get('/tasks/{task}', [TaskController::class, 'show']);
Route::put('/tasks/{task}', [TaskController::class, 'update']);
Route::delete('/tasks/{task}', [TaskController::class, 'destroy']);
```
