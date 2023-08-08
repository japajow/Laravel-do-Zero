# Laravel-do-Zero

## Aprendendo Laravel
 Criando o projeto

 composer create-project laravel/laravel my-app

Validando parametros
 validamos com expresao regular com numeros

```php
Route::get('/user/{id}', function($id){
  return 'User '.$id
})->where('id', expression: '[0-9]+')
```

 validando aceitando apenas strings

 adicionamos mais um where

 ```php
 ->where('name','[a-zA-Z]+')
 ```
 Podemos passar um array para nao ficar adicionando um where  e outro where

 ```php
 ->where([
    'id' => '[0-9]+',
    'name' => '[a-z]+',
    'etc'
 ])
 ```
 Podemos passar tambem whereNumber ao inves de usar expresao regulares

 ```php

 ->whereNumber('token')

 ```
 Podemos passar o Alpha , para usar alfabetos
 somente strings

 ```php
 ->whereAlpha('token')

 ```
 Ou podemos passar tbm strings e numbres usando whereAlphaNumeric

 ```php
 ->whereAlphaNumeric('token')

 ```

Podemos passar tbm uuid para validar o uuid mesmo

```php
->whereUuid('token')

```

## Validando parametros globalmente


Criamos um padrao no arquivo
app/Providers/RouteServiceProvider.php

```php
Route::pattern('id', '[0-9]+');
```

## Agrupando rotas usando prefixo

Criamos uma funcao rotas prefix

```php
 Route::prefix('user')->group(function(){
    //Aqui dentro passamos as rotas que vamos utilizar o prefix

    Route::get('',function(){
        return 'Hello world';
    });
    Route::get('{id?}',function(){
        return 'Hello world';
    });
    Route::post('{id?}',function(){
        return 'Hello world';
    });
 });

```

Podemos tambem nomear as rotas usando name()

```php
Route::prefix('user')->name('admin.')->group(function(){

    Route::get('',function(){
        return 'Hello World';
    })->name('users');

    Route::get('{id?}',function($id=null){
        return 'Hello World '.$id;
    })->name('user');

});

```

## Agrupamento rotas com middleware

```php
vamos utilizar a middleware usando uma classe cujo nome e policia-federal
 somente e redirecionado nas rotas , caso a middleware liberar


Route::middleware('policia-federal')->group(function(){
    //aqui vai as rotas
});

Podemos utilizar em uma rota especificada

Route::get('teste',function(){
    return 'teste';
})->middleware('signed');

```

## Agrupando rotas com sub dominios

```php
 criando um sub dominio
Route::domain('{user}.aqui-vai-dominio.com')->group(function(){
    aqui criamos as rotas // podemos pegar o sub dominio passando na funcao
    Route::get('',function($user){
        return 'rotas';
    });
});
```


## Fallback -  backup .
Caso nao conseguir acessar nenhuma rota , ele vai ao fallback

```php

 Route::fallback(function(){
    return 'Fallback is action';
 });
```

## Injecao de dependencia

injecao de dependencia depende de uma classe A com a classe B

```php

Route::get('/',function(\Illuminate\Http\Request $request){
    dd($request); // var_dump in die em php
    return $request;
});

Conseguimos pegar varios dados do request

Illuminate\Http\Request {#43 ▼ // routes/web.php:45
  +attributes: Symfony\Component\HttpFoundation\ParameterBag {#45 ▶}
  +request: Symfony\Component\HttpFoundation\InputBag {#44 ▶}
  +query: Symfony\Component\HttpFoundation\InputBag {#51 ▶}
  +server: Symfony\Component\HttpFoundation\ServerBag {#47 ▶}
  +files: Symfony\Component\HttpFoundation\FileBag {#48 ▶}
  +cookies: Symfony\Component\HttpFoundation\InputBag {#46 ▶}
  +headers: Symfony\Component\HttpFoundation\HeaderBag {#49 ▼
    #headers: array:15 [▼
      "host" => array:1 [▶]
      "connection" => array:1 [▶]
      "sec-ch-ua" => array:1 [▶]
      "sec-ch-ua-mobile" => array:1 [▶]
      "sec-ch-ua-platform" => array:1 [▶]
      "upgrade-insecure-requests" => array:1 [▶]
      "user-agent" => array:1 [▶]
      "accept" => array:1 [▶]
      "sec-fetch-site" => array:1 [▶]
      "sec-fetch-mode" => array:1 [▶]
      "sec-fetch-user" => array:1 [▶]
      "sec-fetch-dest" => array:1 [▶]
      "accept-encoding" => array:1 [▶]
      "accept-language" => array:1 [▶]
      "cookie" => array:1 [▶]
    ]
    #cacheControl: []
  }
  #content: null
  #languages: null
  #charsets: null
  #encodings: null
  #acceptableContentTypes: null
  #pathInfo: "/request"
  #requestUri: "/request"
  #baseUrl: ""
  #basePath: null
  #method: "GET"
  #format: null
  #session: Illuminate\Session\Store {#289 ▶}
  #locale: null
  #defaultLocale: "en"
  -preferredFormat: null
  -isHostValid: true
  -isForwardedValid: true
  #json: null
  #convertedFiles: null
  #userResolver: Closure($guard = null) {#251 ▶}
  #routeResolver: Closure() {#260 ▶}
  basePath: ""
  format: "html"
}
```

## Injetando model na rota

php artisan migrate no ssh , criamos as tabelas

php artisan migrate:fresh --seed // vai popular com fakes

## criando multiplos middleware

php artisan make:miidleware CheckToken

apos criar a class middleware
incluimos ela no Kernel.php
```php

$routeMiddleware =[
    'checkToken' => \App\Http\Middleware\CheckToken::class,
]
```
inlcuimos a middleware na rota

```php
Route::middleware(['userAgent', 'checkToken'])->group(function(){

});
```

## Criando controllers

php artisan make:controller UserController

```php
No arquivo app/Http/Controllers
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    public function index(){

        dd('xxx');
    }
}

```
Vamos na route e conectar o controller com a rota

```php
use Illuminate\Support\Facades\Route;

use \App\Http\Controllers\UserController;

Route::get('/user',[UserController::class, 'index']);

```

## Controller com parametros

passamos o parametro na funcao que criamos dentro do controller

```php
 public function show($id){
        dd("show-{$id}");
    }
```

na route passamos somente o array com controller

```php
Route::get('/user/{id}',[UserCOntroller::class , 'show']);
```
//resposta
"show-id do usuario passado na url "

## Criando resources no controller

php artisan make:controller UserController --resource

criando com model

php artisan make:controller UserController --resource --model=User

passando os resources na rota

```php

Route::resource('user', passamos a classe userControler::class );
```

## definindo metodos na rota

Voce pode definir apenas uma rota que for usar usando metodo only ou except

```php
Route::resource('user', passamos a classe userControler::class )->only('destroy');

Gera todos menos o destroy
Route::resource('user', passamos a classe userControler::class )->except('destroy');

```

## Multiplos resources

podemos usar um array e passar varias rotas com o resources no plural

```php
Route:: resources([
    'user' => userControler::class,
    'post' => postUserControler::class,
]);
```

## resource para alinhamento rotas

quando queremos usar uma rota
user/{user_id}/comments/{comments_id}
usamos o user.comments ele vai criar a rota completa

```php
Route::get('user.comments',UserController::class);

```
quando queremos somente pegar o comments usamos o shallow()
ele ira excluir o user/{user_id}

```php
Route::get('user.comments',UserController::class)->shallow();
```

## nomeando as rotas

podemos nomear as rotas da seguinte forma

```php
Route::get('users', UserController::class)->names([
    'create' => 'usuario.criar',
    'update' => 'usuario.atualizar',
    'destroy' => 'usuario.deletar'
]);

```

## renomeando os parametros do resource

php artisan make:controller PostController --resource

```php

 Route::get('posts', PostController::class)->parameters(
    [
        'posts' => 'admin_post'
    ]
 );

```

## scope controllers

para pegarmos outros dados fora o id passamos na rota o :email por exemplo

```php
Route::get('user/{user:email}',UserController::class);
ai pegamos pelo email assim

```
quando queremos pegar pelo resorce usamos o scoped()

```php
Route::resource('users',UserControlle::class)->scoped(
    [
        'email' => 'email'
    ]
);
```

## Alterando o nome dos metodos resource
localize resource resourceVerbs();
```php
Route::resourceVerbs(
    [
        'create' => 'criar',
        'edit' => 'editar'
    ]
);

Route::get('user/{user:email}',UserController::class);
```

## adicionando rotas novas no resource

```php
Route::get('usuarios/posts', [UserController::class, 'posts']);
Route::resource('users',UserControlle::class);
```

## acessando dados da ulr request

```php
Route::get('user',function(\Illuminate\Http\Request $request){

    dd($request->path());
    // dd($request->url());
    // dd($request->fullUrl());
    // dd($request->fullUrlWithQuery());
    // dd($request->fulUrlIs());
    // dd($request->is());
    // dd($request->routeIs('users'));
    // dd($request->method());
    // dd($request->isMethod('get'));

});

```
## Acessando os dados do input

```php
Route::get('user',function(\Illuminate\Http\Request $request){

    dd($request->input('token'));
    dd($request->input('curso', 'laravel'));
    dd($request->input('product.name'));
    dd($request->input());

    dd($request->query('product'));
    dd($request->query('curso', 'laravel'));
    dd($request->query());

    dd($request->token);
    dd($request->only());
    dd($request->except());

});

```

## checagem nos input

```php

Route::get('user',function(\Illuminate\Http\Request $request){

    // verifica se existe um token na url
   if($request->has('token')){
    dd('token existe');
   }

      // verifica se existe um token na url em array
   if($request->has(['token', 'curso'])){
    dd('token existe e curso existe');
   }

       // verifica se existe um token
   $request->whenHas('token', function($input){

       dd('faca alguma coisa');
   });

    if($request->hasAny(['token', 'curso'])){
        dd('faca alguma coisa');
    }

    if($request->filled('curso')){
        dd('faca alguma coisa');
    }

    $request->whenFilled('token', function($input){

       dd('faca alguma coisa');
   });

   if($request->missing('curso')){
        dd('curso esta faltando ');
    }





});
```

## Criando a primeira view

```php

Route::get('/',function(){
    return view('welcome');
});
```

quando criamos na view/user/profile e da seguinte forma que podemos acessar

```php
Route::get('profile',function(){
    return view('user.profile');
});
```

tambem podemos usar o helpers exists() e first usando o View

## views e controller

criamos a controller

php artisan make:controller UserController

```php
passamos o controle criado na rota
route::get('usuario',[UserController::class, 'index']);

```

no arquivo UserController criamos a funcao que pegaa view

```php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
   public function index(){
    return view('user.index');
   }
}


```

## passando dados para a view

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
   public function index(){
    return view('user.index',[
        'aqui vai algo ' => 'aqui vai o valor '
    ]);
   }
}
```

podemos usar tambe o with() para renderizar

```php

class UserController extends Controller
{
   public function index(){
    return view('user.index',)->with([
        'aqui vai algo ' => 'aqui vai o valor '
    ]);
   }
}
```
## Incluindo subviews

criamos um heading.blade.php

e incluimos com
@include('user.heading')

tambem podemos uar o
@includeIf()
ele vai incluir somente a view se ela existir

temos o
@includeWhen(true,'user.heading')
inclua se for verdadeira

temos tbm o
@includeUnless('false','user.heading')
incluie se for falso

@includeFirst(['user.heading', 'user.heading2']);
ele vai mostrar a primeira, e sempre a que existe



## passando dados para a subviews

```php
passamos em um array e incluimos na views {{'title'}}
@include('heading',['title' => 'Usuarios']);

```
## renderizandoo a lista de usuarios

```php

@foreach($users as $user)
    @include('user.user',['user', $user])
@endforeach
```

## renderizandoo a lista de usuarios 2

com o each renderizamos o loop facilmente em laravel

```php
1 seria s view
2 seria a variavel que contem os dados
3 seria a variavel para renderizar na view
@each('user.user',$users,'user')


```

## Criando layouts usando heranca
layouts.blade.php

```php


@section('content')
    conteudo padrao
@show
```

Para usar a layout usamo o @extends()

```php

@extends('layouts.default')
```

Para usar a section que criamos utilizamos da seguinte forma

```php
@section('content')
  meu conteudo diferente
@endsection

```

## Criando layouts usando heranca 2

```php


 @yield('yield', 'Conteudo padrao')


```

## Criando layouts defauts


 na pasta criamos
  resource/views/layouts/default.blade.php

  chamamos ele como

  ```php

  @extends('layouts.default')
  @section('title','Lista de usuario')

    @section('menu')
    Lista de menus
    @parent
    @endsection

    @section('content')
    @endsection
  ```

## Renderizando componentes

criando um novo componente com subpasta

php artisan make:component User/UserList

chamamos no index.blade.php assim

```php

 <x-user.user-list></x-user.user-list>

```

## Passando dados para o components

```php
Passamos parametro
 <x-user.user-list
    type="card"

 ></x-user.user-list>

```
vamos na classe do component e criar uma variavel com mesmo nome type

e incluimos ele no constructor

```php

  public $type;
    /**
     * Create a new component instance.
     */
    public function __construct($type)
    {
        $this->type = $type;
        //
    }
```
Agora se formos no
 user-list.blade.php  podemos usar
o type

```php
{{$type}}

```

## Criando formulario novo projeto

composer create-project laravel/laravel myapp2

criando uma rota de login

```php

 Route::get('login',[])
```

Criando o controller auth
php artisan make:controller AuthController

app/Http/Controllers

vamos incluir o controller na nossa rota

```php
importamos a rota da nossa classe
use App\Http\Controllers\AuthController;

incluimos na rota nosso controller
Route::get('login',[AuthController::class],'login')
```
Criamos a funcao login no controller

```php

 public function login(){
    dd('login');
 }

```

vamos incluir a nossa view no login()

```php
 public function login(){
    return view('auth.login');
 }

```

vamos criar na view o auth/login.blade.php

vamos criar o layout


views/layouts/auth.blade.php

icluimos o html no auth.blade.php

e chamamos ele no nosso login.blade.php

```php

@extends('layouts.auth')
```

pegamos o css do bootstrap

<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-4bw+/aepP/YC94hEpVNVgiZdgIC5+VKNBQNGCHeKRQN+PtmoHDEXuppvnDJzQIu9" crossorigin="anonymous">

incluindo a imagem
https://getbootstrap.com/docs/5.3/assets/brand/bootstrap-logo.svg

vamos agora quebrar o html em partes

```php

@section('content')
<main class="form-signin w-100 m-auto">
  <form>
    <img class="mb-4" src="https://getbootstrap.com/docs/5.3/assets/brand/bootstrap-logo.svg" alt="" width="72" height="57">
    <h1 class="h3 mb-3 fw-normal">Please sign in</h1>

    <div class="form-floating">
      <input type="email" class="form-control" id="floatingInput" placeholder="name@example.com">
      <label for="floatingInput">Email address</label>
    </div>
    <div class="form-floating">
      <input type="password" class="form-control" id="floatingPassword" placeholder="Password">
      <label for="floatingPassword">Password</label>
    </div>

    <div class="form-check text-start my-3">
      <input class="form-check-input" type="checkbox" value="remember-me" id="flexCheckDefault">
      <label class="form-check-label" for="flexCheckDefault">
        Remember me
      </label>
    </div>
    <button class="btn btn-primary w-100 py-2" type="submit">Sign in</button>
    <p class="mt-5 mb-3 text-body-secondary">&copy; 2017–2023</p>
  </form>
</main>

@endsection
```

incluimos o @csrf para nao ter forjamento no formulario

```php

gera um token para o formulario
@csrf

```

vamos agora incluir o method e action no formulario

```php

<form method="POST" action="{{route('login.index')}}">
```

incluimos o name na nossa rota

```php
Route::get('/login',[AuthController::class ,'login'])->name('login.index');
```

Criamos a rota de post

```php

Route::post('/login',[AuthController::class ,'store'])->name('login.store');

```

Criamos a funcao no controller

```php

  public function store(Request $request){

    }
```

incluindo a validacao nao funcao store

```php
 public function store(Request $request){

  $request->validate([
            'email' => 'required|email',
            'password' => 'required|string'
        ]);
    }

```

## exibindo os erros na tela

criamos a validacao de erro no html para exibir os erros com foreach

```php

   @if ($errors->any())
            @foreach ($errors->all() as  $error)
                {{$error}}
            @endforeach
        @endif
```
## exibindo erros por campo

```php

    @error('email')
        {{$message}}
    @enderror
```

passando class do bootstrap uando der erro

```php

 class="form-control @error('password') is-invalid @enderror"
```

## recuperando o valor antigo

```php
  {{ old('email') }}

```

## traduzindo os erros

config/app.php

temos o locale mudamos para pt-BR

na pasta lang precisamos criar uma pasta pt

pt/validation.php



Instalação
Scaffold do diretório lang
```ssh
php artisan lang:publish
```
Instale o pacote
```ssh
composer require lucascudo/laravel-pt-br-localization --dev
```
Publique as traduções
```ssh
php artisan vendor:publish --tag=laravel-pt-br-localization
```
Configure o Framework para utilizar 'pt_BR' como linguagem padrão
// Altere Linha 85 do arquivo config/app.php para:
```php

'locale' => 'pt_BR'
```

## criando a validacao personalizada

php artisan make:request LoginRequest


autorizamos e incluimos a regra

```php
 public function authorize(): bool
    {
        return true;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array<string, \Illuminate\Contracts\Validation\ValidationRule|array|string>
     */
    public function rules(): array
    {
        return [
            'email' => 'required|email',
            'password' => 'required|string'
        ];
    }

```

incluiamos o LoginRequest no controller

```php
use App\Http\Requests\LoginRequest;
 public function store(LoginRequest $request){


        dd($request->validated());
    }

```

## Criando Http execoes

404,401,500 etc

```php
abort();
abort_if(boolean,code,message);
abort_unless();

```

## Criando a pagina 404

```php
abort('404','pagina nao encontrada')

```
mostrando a mensagem acima
```php
{{$exception->getMessage()}}

```

## Criando as paginas de errors da laravel automatico

php artisan vendor:publish --tag=laravel-errors

resource/view/404.blade.php

## Criando nossa propia exception

php artisan make:exception InvalidOrderException


usando a nossa classe InvalidOrderException

```php
Na routes incluimos a classe InvalidOrderException
Route::get('/login',function(){
    throw new InvalidOrderException();
})->name('login.index');

```

Repostando o erro captado
Exception/Handler.php

```php
public function register(): void
    {
        $this->reportable(function (Throwable $e) {
            //cai aqui o erro InvalidOrderException
            dd('x')
        });
    }

```

## reportando a nossa excecao

```php
 $this->reportable(function(InvalidOrderException $e){
        dd($e);
 })

```

## Ignorando alguma execao
Handler.php

```php
  protected $dontReport = [
        InvalidOrderException::class
    ];

```


## Renderizando as execoes

```php

  $this->renderable(function(InvalidOrderException $e ){
        return response()->view('errors.invalid-order');
        });

```

podemos renderizar tbm com json

```php
$this->renderable(function(InvalidOrderException $e ){
        return response()->json('errors.invalid-order');
        });
```

## isolando a camada de execao

podemos usar a classe criada InvalidOrderException e isolar nossos
metodos nela , ao inves de uar na Handler todas classes

```php
class InvalidOrderException extends Exception
{
    public function report(){
        dd('report');
    }

    public function render(){
        return response()->view('passamos a nossa view de errors ');
    }
}


```

## introducao query builder

vamos no database/seeders/DatabaseSeeder

vamos descomentar User::factory

vamos fazer o migrate no ssh

php artisan migrate --seed

## Query Builder

incluimos DB na nossa rota
routes/web.php

```php

use Illuminate\Support\Facades\DB;
DB

selecionamos a table com
 DB::table('users')->get();// get pega tudo
```

pegamos os dados users e colocamos na view

```php
Route::get('/', function () {
   $users =  DB::table('users')->get();

    return view('welcome',compact('users'));
});

```

Na view welcome.blade.php usamos o @foreach

```php
 @foreach ($users as $user)
    {{$user->id}} = {{$user->name}} - ({{$user->email}})<br>
@endforeach

```
## Pegando somente um registro no banco de dados

```php

Route::get('/', function () {
   $users =  DB::table('users')->find(id: 5);
  dd($users);
    return view('welcome');
});
```

Podemos usar o first

```php
Route::get('/', function () {
   $users =  DB::table('users')->first();
  dd($users);
    return view('welcome');
});

```
Podemos passar com where

```php

Route::get('/', function () {
   $users =  DB::table('users')->where('email', 'anjali.luettgen@example.org')->first();
  dd($users->email);
    return view('welcome');
});
```

## Query Builder checando se um registro existe

```php

por ao contrario podemos usar o doesntExist()

 $user = DB::table('users')
    ->where('email','anjali.luettgen@example.org')
    ->doesntExist();
```

## Selecionando uma coluna especifica
```php

$user = DB::table('users')
    ->select('name')
    ->get();
```

Alterando nome da coluna

```php
$user = DB::table('users' as 'user_name')
    ->select('name')
    ->get();

```

Podemos chamar outras colunas passando mais

```php

Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->select('name  as user_name')
    ->get();
    dd($user);
     return view('welcome');
 });
```
## Ordenando resultados

```php

Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->orderBy('name','desc')
    ->get();
    dd($user);
     return view('welcome');
 });
```
Podemos usar de uma forma mais curta

```php

Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->orderByDesc('name')
    ->get();
    dd($user);
     return view('welcome');
 });
```
Pegando de uma forma randomica

```php

Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->inRandomOrder('name')
    ->first(); //pegando somente 1 resultado randomico
    dd($user);
     return view('welcome');
 });
```

Ordenando pelo mais novo latest
ordenando pelo mais antigo oldest

```php
Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->latest()
    ->get();
    dd($user);
     return view('welcome');
 });
 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->oldest()
    ->get();
    dd($user);
     return view('welcome');
 });

 pegando o ultimo e novo pelo name
 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->latest('name')
    ->get();
    dd($user);
     return view('welcome');
 });
```
## Tranalhando com whereIn

```php

 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->whereIn('id',[1,2,3]) // Verifica se essa coluna existe o valor que vamos passar
    ->get();
    dd($user);
     return view('welcome');
 });

 Resultado dessa bisca acima seria do usuario 1,2,3

Podemos tambem fazer o inverso whereNotIn()

 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->whereNotIn('id',[1,2,3]) // Verifica se essa coluna existe o valor que vamos passar
    ->get();
    dd($user);
     return view('welcome');
 });

 So pega outros menos o 1 e 5

 Tambem podemos usar o orWhereIn()
 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->whereIn('id',[1,5])
    // ->whereNotIn('id',[1,5])
    ->orWhereIn('id',[8,9]) Verifica se essa coluna existe o valor que vamos passar
    // ->orWhereNotIn('id',[8,9])
    ->get();
    dd($user);
     return view('welcome');
 });
```

## Limitando resultados

```php

 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->limit(2) limita para 2
    ->get();
    dd($user);
     return view('welcome');
 });

 usamos o take() tambem como limit()

  Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->take(2) limita para 2
    ->get();
    dd($user);
     return view('welcome');
 });

Pegando os ultimos novos 2

Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->latest()
    ->limit(2) limita para 2
    ->get();
    dd($user);
     return view('welcome');
 });

 Pegando os ultimos

 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->oldest()
    ->limit(2) limita para 2
    ->get();
    dd($user);
     return view('welcome');
 });

 Ordenando pelo orderByDesc('name')

  Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->orderByDesc('name')
    ->limit(2) limita para 2
    ->get();
    dd($user);
     return view('welcome');
 });

 Usamos o skip() para pular o numero colocado

   Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->skip(1)// ou offset() mesma coisa
    ->limit(2) limita para 2
    ->get();
    dd($user);
     return view('welcome');
 });
```

## trabalhando com where

```php
 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->where('coluna','operador', 'valor')
    ->get();
    dd($user);
     return view('welcome');
 });

  Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->where('name','=', 'Adella Corwin')// like = != <> diferente
    ->first();
    dd($user);
     return view('welcome');
 });

 Usamos o like

 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->where('name','like', '%Dr.%')// like = != <> diferente
    ->first();
    dd($user);
     return view('welcome');
 });

Podemos usar o where varias vezes

 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->where('name','like', '%Dr.%')// like = != <> diferente
    ->where(column:'id', operator: '=',value:  1)
    ->first();
    dd($user);
     return view('welcome');
 });

 Caso nao usarmos o operator ele vai considerar como =

 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->where(column:'id', value:  1)
    ->first();
    dd($user);
     return view('welcome');
 });

 Passamos um [] para a condicao do where nao ficar verboso

 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->where([
        ['id', 1],
        ['name' ,'=','Allin Corin'],
        ['like','Dr.%'],
        ['id' > 2],
        ['id' => 2],
        ['id' < 3]
        ['id' <= 3]
    ])
    ->first();
    dd($user);
     return view('welcome');
 });


```

## trabalhando com orWhere

```php
 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->where(column:'name', value:  'bruno hamawaki')
    ->orWhere(column: 'name' , value:'natalia hamawaki')
    ->first();
    dd($user);
     return view('welcome');
 });

orWhere() e um ou outro seria um  WHERE name OR name no banco de dados


podemos tambem usar o clousure ('name' '=' 'z')

Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->where(column:'name', value:  'bruno hamawaki')
    ->orWhere(function($query){
        $query->where('name', 'natalia hamawaki');
        $query->where('id', 1);

    })
    ->get();
    dd($user);
     return view('welcome');
 });
```
## trabalhando com whereBetween()

```php

Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->whereBetween(column:'id', [1,4])// entre 1 a 4
    ->get();
    dd($user);
     return view('welcome');
 });

 Inverso seria o whereNotBetween()


Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->whereNotBetween(column:'id', [1,4])// entre 1 a 4
    ->get();
    dd($user);
     return view('welcome');
 });

 usando a datas

 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->whereBetween(column:'created_at', ['2021-01-01', '2022-02-02'])// entre 1 a 4
    ->whereNotBetween(column:'created_at', ['2021-01-01', '2022-02-02'])// entre 1 a 4
    ->get();
    dd($user);
     return view('welcome');
 });
```
## trabalhando com valores nulos

```php

Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->where(column:'created_at', null)
    ->whereNull(column:'created_at')// e a mesma coisa de cima
    ->whereNotNull(column:'created_at')
    ->orWhereNull(column:'updated_at')// ou null
    ->get();
    dd($user);
     return view('welcome');
 });

```
## trabalhando com whereDate()

```php

Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->whereDate(column:'created_at', operator: '=', value: '2021-02-01')
    // podemos usar os operados > < => <= <> = like
    // se omitir o operator fica =
    ->whereDay(column:'created_at',value: '03')
    ->whereMonth(column:'created_at',value: '11')
    ->whereYear(column:'created_at',value: '2021')
    ->whereTime(column:'created_at',value: '10:00:30')
    ->get();
    dd($user);
     return view('welcome');
 });
```
## trabalhando com whereColumn()

```php
 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->whereColumn(first:'created_at', operator: '=',second: 'updated_at')
    ->orWhereColumn(first:'created_at', operator: '=',second: 'updated_at')
    // compara as colunas e se for igual traz o resultado
    ->get();
    dd($user);
     return view('welcome');
 });

```

## usando o inner join

php artisan migrate:fresh --seed
criando as tabelas e populando elas com seed

```php
Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->join(table: 'posts', first: 'users.id', operator: '=', second: 'posts.user_id')//
    ->select('users.*', 'posts.title')
    ->get();
    dd($user);
     return view('welcome');
 });

```

## left e right join

```php

Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->leftJoin(table: 'posts', first: 'users.id', operator: '=', second: 'posts.user_id')//
    ->select('users.*', 'posts.title')
    ->get();
    dd($user);
     return view('welcome');
 });

 right

 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->rightJoin(table: 'posts', first: 'users.id', operator: '=', second: 'posts.user_id')//
    ->select('users.*', 'posts.title')
    ->get();
    dd($user);
     return view('welcome');
 });


```

## Consulta avancada com join

```php
Podemos usar varios joins juntos

Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->join(table: 'posts', first: 'users.id', operator: '=', second: 'posts.user_id')//
    ->join(table: 'products', first: 'users.id', operator: '=', second: 'products.user_id')//
    ->select('users.*', 'posts.title')
    ->get();
    dd($user);
     return view('welcome');
 });

 Podemos tambem usa function()
 e misturar com where

 Route::get('/', function () {
    $user = DB::table('users', 'email')
    ->join(table: 'posts',function($join){
        $join->on('users.id', '=', 'posts.user_id')->where('posts.user_id' ,'>','8');
    })
    ->select('users.*', 'posts.title')
    ->get();
    dd($user);
     return view('welcome');
 });
```

## montando query condicionalmente

```php
Pegando resultado com querystring da url o name
 usando o Request class

 Route::get('/', function (Request $request) {
    $user = DB::table('users', 'email')
    ->where('name', 'LIKE', '%'.$request->name.'%')
    ->get();
    dd($user);
     return view('welcome');
 });


QUando alguma coisa for positiva usamos o when()

 Route::get('/', function (Request $request) {
    $name = $request->name;
    $user = DB::table('users', 'email')

    ->when(!empty($name), function($query) use ($name){

        $query->where('name', 'LIKE', '%'. $name.'%')
        })

    ->get();
    dd($user);
     return view('welcome');
 });

```

## Registrando  registros

```php
 Route::get('/', function () {

    $user = DB::table('users')
    ->insert([
        'name' => 'Jon Snow',
        'email' => 'jon@email.com'
        'password' => '1234'
    ]);

dd($user);

     return view('welcome');
 });

Route::get('/', function(){
   $user =  DB::table('users')->get();
   dd($user);
});

Inserindo varios valores de uma vez so

Route::get('/', function(){

    $user = DB::table('users')->insert([
        [
            'name' => 'Jon Snow2',
            'email' => 'jon2@email.com',
            'password' => '1234'
        ],
        [
            'name' => 'Jon Snow3',
            'email' => 'jon3@email.com',
            'password' => '1234'
        ],
        [
            'name' => 'Jon Snow4',
            'email' => 'jon4@email.com',
            'password' => '1234'
        ],

    ]);
});


Usamos uma tatica para a laravel nao da erro de duplicagem

usamos o insertOrIgnore()

Route::get('/', function(){

    $user = DB::table('users')->insertOrIgnore([
        [
            'name' => 'Jon Snow2',
            'email' => 'jon2@email.com',
            'password' => '1234'
        ],
        [
            'name' => 'Jon Snow3',
            'email' => 'jon3@email.com',
            'password' => '1234'
        ],
        [
            'name' => 'Jon Snow4',
            'email' => 'jon4@email.com',
            'password' => '1234'
        ],

    ]);
});


```
## inserindo registro e retornando o ID

```php
Pegando o id do ultimo resgistro inserido
com insertGetId()

Route::get('/', function(){
    $user = DB::table('users')->insertGetId([
        'name' => 'jon Snow5',
        'email' => 'jon5@email.com',
        'password' => '1234'
    ]);

    dd($users);

    return view('welcome');
});

```
## Atualiando registros

```php

Cuidado sempre temo que passar o where , se nao vai modificar todos os dados


 Route::get('/', function(){

    $user = DB::table('users')
    ->where('user_id' => $id)
    ->update([
        'name' => 'Bruno'
    ]);

    dd($user);

    return view('welcome');
 });

```

## inserindo e atualizando registros
usamos o upsert()
se ja existe ele vai atualizar

uniqueBy seria o unico campo

```php

Route::get('/', function(){
    $user = DB::table('users')
    ->upsert([
        dados que a gente quer inserir
        'name' => 'bruno',
        'email' => 'bruno@gmail.com'
        'password' => '1234'

    ], uniqueBy: [
        'name', 'email'
        se ja exsiteir no banco de dados atualiza, se noa existir ja insira
    ], update: [
        caso for atualizar passar aonde e o valor
        'remember_token'
    ]);
});

```

## Atualizando ou inserindo registros
 Vamos atualizar o registro , se ele nao existir ele inseri no banco de dados

```php

 Route::get('/', function(){
    $user = DB::table('users')->updateOrInsert([
        campos qie a gente quer pesquisar no BD
        'name' => 'Jon Snow'
    ],[
        Segundo array seria o que a gente quer atualizar no banco de dados
        'remember_token' => 'zxy',
        'password' => '1234'
    ]);

    dd($user);

    return view('welcome');
 })

```

## Incrementando e detectando valores

```php
Podemos incrementar com increment()

 Route::get('/', function(){
     $user = DB::table('users')
     ->where('name','Bruno')
     ->increment('rate', amount: 1);

     dd($user);

     return view('welcome');
 });

Ou podemos tambem decrementar ou seja diminuir  com decrement()
 Route::get('/', function(){
     $user = DB::table('users')
     ->where('name','Bruno')
     ->decrement('rate', amount: 2);

     dd($user);

     return view('welcome');
 });

```
## deletando registro no BD

```php

 Route::get('/',function(){
    $user = DB::table('users')
    ->where('user_id', 1)
    ->delete()
 });

```
## Criando uma paginacao

```php

Route::get('/', function(){
    $user = DB::table('users')
    ->paginate(perPage: 2)
});
```
Na url passamos ?page=2 iremos para pagina 2 da paginacao

## Paginacao simplificada

```php

Route::get('/', function(){
    $user = DB::table('users')
    ->simplePaginate(perPage: 2)
});
```

## Mostrando paginacao na view

```php
Route::get('/', function(){
    $user = DB::table('users')
    ->paginate(perPage: 2)

    return view('users',[
        'users' => $users
    ])
});

```

para usar o bootstrap temos que ir no


app/Providers/Appservices

```php
 public function boot(){
    Paginator::useBootstrap();
 }

```Podemos configurar os links da paginacao
```
```php
se for 1 ele vai colocar sempre so 1 na frente 1 atraz

{{$users->onEachSide(1)->links()}}

```

## Customizando o layout da paginacao


```php
{{$users->onEachSide(1)->links('paginator.defautl')}}

```

criamos na views paginator/default.blade.php

assim seria muito complicado refazer a paginacao

no caso temos uma forma mais facil de fazer a paginacao

php artisan vendor:publish --tag=laravel-pagination


## Criando a nossa primeira Migration

php artisan make:migration create_posts_table

```php

Podemos criar a nossa propia colunas

 public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('body');
            $table->timestamps();
        });
    }
Como ja rodamos a migration nao atualiza nada temos que usar a palavra refresh

php artisan migrate
```

## Desfazendo a migration

php artisan migrate:rollback

ele vai deletar a tabela

dando rollback com mais steps atraz

Desfazendo 2 vezes
php artisan migrate:rollback --step=2


## Resetando a migration

php artisan migrate:reset

ele vai resetar todas a tabelas(deletar)


ao inves de fazer reset e migrate faz refresh

refresh ele ja deleta e cria novamente

php artisan migrate:refresh

refresh nao deleta a pocisao da tabela , ou seja o ID continua contabilizando apartir de onde parou

agora caso usamos o fresh ele vai deletar e resetar tudo mesmo desde o zero

php artisan migrate:fresh


## Tipos de colunas da migration
[https://laravel.com/docs/10.x/migrations#generating-migrations]
bigIncrements
bigInteger
binary
boolean
char
dateTimeTz
dateTime
date
decimal
double
enum
float
foreignId
foreignIdFor
foreignUlid
foreignUuid
geometryCollection
geometry
id
increments
integer
ipAddress
json
jsonb
lineString
longText
macAddress
mediumIncrements
mediumInteger
mediumText
morphs
multiLineString
multiPoint
multiPolygon
nullableMorphs
nullableTimestamps
nullableUlidMorphs
nullableUuidMorphs
point
polygon
rememberToken
set
smallIncrements
smallInteger
softDeletesTz
softDeletes
string
text
timeTz
time
timestampTz
timestamp
timestampsTz
timestamps
tinyIncrements
tinyInteger
tinyText
unsignedBigInteger
unsignedDecimal
unsignedInteger
unsignedMediumInteger
unsignedSmallInteger
unsignedTinyInteger
ulidMorphs
uuidMorphs
ulid
uuid
year

```php

 public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id()->unique();
            $table->string('title');
            $table->text('body')->nullable();
            $table->boolean('is_active');
            $table->decimal('amount');
            $table->float('amount_2')->nullable();
            $table->double('amount_3');
            $table->integer('amount_4');
            $table->date('published_at');
            $table->timestamp('published_at_timestamp');
            $table->foreignId('user_id')
                ->constrained()
                ->onDelete('CASCADE')// deleta junto
                ->onUpdate('CASCADE')// atualiza junto
            $table->timestamps();
        });
    }

```

## Atualizando tabelas

php artisan make:migration update_posts_table --table=posts

```php

 public function up(){


    Schema::table('posts', function(Blueprint $table){
        $table=>string('adicionando_mais_colunas')->nullable()->after('adicionando depois do title');


    })
 }

```

## modificando a coluna existente

php artisan make:migration mdify_posts_table --table=posts

para modificar a coluna temos que instalar o

## composer require doctrine/dbal

```php



 public function up(){


    Schema::table('posts', function(Blueprint $table){


        $table->renameColumn('modificando nome title', 'modificando para post_title');


    })
 }

```
