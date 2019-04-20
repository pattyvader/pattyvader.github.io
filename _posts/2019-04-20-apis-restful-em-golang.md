---
layout: post
title:  "APIs RESTful em Go"
subtitle: "Minha primeira API Go"
date:   2019-04-20
background: '/img/gopher.jpg'
thumbnail: '/img/gopher.jpg'
image_footer: '/img/gopherwars.jpg'
---
Minha aventura pelo mundo Go resultou nessa primeira implementação de API. *Resumindo*: é algo despretencioso e totalmente iniciante!

Esse post descreve em linhas gerais um pouco sobre Go e APIs RESTful, e logo após é explicado sobre o que foi codificado. O código da API está disponível [aqui](https://github.com/pattyvader/users_service).

## A linguagem Go

O Go é uma linguagem de programação relativamente nova, e foi desenvolvida por Engenheiros do Google, em 2007. Já em 2009, o Go tornou-se Open Source, permitindo a todos contribuir na melhoria e evolução da linguagem. [Contribua!](https://github.com/golang/go)

Para facilitar o aprendizado e um melhor entendimento do básico da linguagem, foi desenvolvido o [Go tour](https://go-tour-br.appspot.com/list). Ele é um ambiente online e interativo, onde se pode dar os primeiros passos com Go.

##### Principais características

1. Possui uma sintaxe bem simples.
2. É uma linguagem compilada.
3. A curva de aprendizado é baixa.

## APIs RESTful

O termo REST (*Representational State Transfer*) foi criado por Roy Fielding, em 2000. É um estilo de arquitetura para ajudar na criação e organização de sistemas distribuídos. Podemos usar esse padrão arquitetural para criar uma API (*Application Programming Interface*).

Podemos definir API com um sistema onde a interação cliente/servidor é realizada através do protocolo HTTP. Nessa interação o cliente realiza requisições para o servidor solicitando dados de determinado recurso. Normalmente a resposta com esses dados estão no formato `.JSON`.

```json
[
    {
        "ID": 1,
        "Name": "Patty",
        "Email": "pattyvaderbr@gmail.com",
        "Password": "123456",
        "Admin": true
    },
]
```

**Resumindo**: *API RESTful* é uma plataforma que expõe dados como recursos, permitindo assim, interagir com eles.  

##### 1. Recurso, Coleção e Representação

###### 1.1 Recurso

Recurso é a representação de algo, ou um objeto, que pode ser manipulado. Pode-se dizer que `user` é um recurso.

###### 1.2 Coleção

Coleção é um conjunto de recursos, como por exemplo, `/users`.

###### 1.3 Representação

É a maneira pela qual um recurso pode ser representado. Essa representação pode ser feita em JSON, XML ou HTML. 

```json
[
    {
        "ID": 1,
        "Name": "Patty",
        "Email": "pattyvaderbr@gmail.com",
        "Password": "123456",
        "Admin": true
    },
]
```

##### 2. HTTP Verbs

* **GET** : obtém a representação de um recurso.
* **HEAD**: obtém os headers.
* **POST**: cria um novo recurso baseado na sua representação.
* **PUT** : muda o estado do recurso ou cria um novo se você conhece a sua URL.
* **DELETE**: remove um recurso.
* **PATCH**: modifica parte do estado de um recurso.

##### 3. Status code

* **1xx - Informativo**: solicitação recebida e dando continuidade ao processo.
* **2xx - Sucesso**: a solicitação foi recebida, entendida e aceita pelo servidor.
* **3xx - Redirecionamento**: o cliente deve executar uma ação adicional para completar a sua requisição.
* **4xx - Erro no cliente**: a solicitação enviada pelo cliente possui erros.
* **5xx - Erro no servidor**: erros ocorridos no servidor.

## Um pouco de código

##### Tecnologias utilizadas

* [Gorilla](https://github.com/gorilla/mux) - implementa rotas URL
* [PostreSQL](https://www.postgresql.org/)
* [TOML](https://github.com/BurntSushi/toml) - lê arquivos do tipo TOML

###### Arquivos do tipo TOML

É um formato de arquivo de configuração mais fácil de ler e escrever, criado por Tom Preston-Werner que é o co-fundador do GitHub. Consiste em pares de chave/valor, nomes de seções e comentários. Abaixo segue um exemplo desse tipo de arquivo.


```toml
#dados para conexão no banco de dados
[database]
server = "192.168.1.1"
ports = [ 8001, 8001, 8002 ]
connection_max = 5000
enabled = true
```

Trata-se de um acrônimo para "Tom's Obvious, Minimal Language".

##### Implementação

O projeto `users_service` está estruturado da seguinte maneira:

```go
  users_service/
     dao/
        connectionDAO.go
        userDAO.go
     handlers/
        userHandler.go
     models/
        user.go
     .gitignore
     .travis.yml
     LICENSE
     README.md
     config.toml
     database.sql
     main.go
```
##### 1. Especificação da API

A API possui as seguintes URLs:

**`GET`** `lista todos os usuários`

```go
/v1/users/
```

**`GET`** `lista somente 1 usuário`

```go
/v1/users/{id}
```

**`POST`** `cadastra um novo usuário`

```go
/v1/users/
```

**`PUT`** `atualiza somente 1 usuário`

```go
/v1/users/{id}
```

**`DELETE`** `remove somente 1 usuário`

```go
/v1/users/{id}
```

##### 2. DAO

```
 DAO: Data Access Object (Objeto de acesso a dados)
 Consiste em separar as regras de negócio do acesso à base de dados.
```

O diretório `dao` possui os arquivos `connectionDAO.go` e `userDAO.go`

###### 2.1 **connectionDAO.go**

Esse arquivo possui 2 funções:

```go
func InitDB() {
   var conf configFile
   var err error

   if _, err = toml.DecodeFile("config.toml", &conf); err != nil {
     log.Println(err)
     return
   }

   dbInfo := fmt.Sprintf("host=%s port=%d user=%s password=%s dbname=%s sslmode=%s",
     conf.DB.Host, conf.DB.Port, conf.DB.User, conf.DB.Password, conf.DB.Name, conf.DB.SSLmode)
   db, err = sql.Open("postgres", dbInfo)
   if err != nil {
     panic(err)
   }

   err = db.Ping()
   if err != nil {
     panic(err)
   }
   log.Printf("host: %s, port: %d, user: %s, name: %s",
     conf.DB.Host, conf.DB.Port, conf.DB.User, conf.DB.Name)
}
```

```go
func CloseDB() {
   db.Close()
}
```

A primeira é responsável por ler o arquivo do tipo TOML e efetuar a conexão com o banco de dados. E a segunda fecha essa conexão.

###### 2.2 **userDAO.go**

Nesse arquivo constam as funções responsáveis por lidar diretamente com o banco de dados. São elas:

```go
func GetAllUsers()
func GetUserByID()
func CreateUser()
func UpdateUser()
func RemoveUser()
```

Para exemplificar a estrutura que possuem essas funções, abaixo é mostrada a `RemoveUser` que é responsável por remover um usuário da base de dados.

```go
func RemoveUser(userID int) (int, error) {
  result, err := db.Exec(`DELETE FROM users where id = $1`, userID)
  if err != nil {
     return 0, err
  }

  rowsDeleted, err := result.RowsAffected()
  if err != nil {
     return 0, err
  }

  return int(rowsDeleted), err
}
```

##### 3. Handlers

Os handlers são responsáveis por "escutar" as requisições do cliente e retornar uma resposta. No arquivo `userHandler.go` encontram-se as seguintes funções:

```go
func GetAllUsersHandler()
func GetUserByIDHandler()
func CreateUserHandler()
func UpdateUserHandler()
func DeleteUserHandler()
```

A função `GetAllUsersHandler`, por exemplo, é responsável por retornar todos os usuários da base de dados. Retorna o erro **500**, caso a requisição não seja bem sucedida.

```go
func GetAllUsersHandler(w http.ResponseWriter, r *http.Request) {
  users, err := dao.GetAllUsers()
  if err != nil {
    http.Error(w, "Internal error", http.StatusInternalServerError)
    return
  }
  FormatResponseToJSON(w, http.StatusOK, users)
}
```

Foi adicionado a esse arquivo uma função que formata a resposta para o cliente em formato JSON.

```go
func FormatResponseToJSON(w http.ResponseWriter, statusCode int, response interface{}) {
  json, err := json.Marshal(response)
  if err != nil {
    http.Error(w, err.Error(), http.StatusInternalServerError)
  }

  w.Header().Set("Content-Type", "application/json")
  w.WriteHeader(statusCode)
  w.Write(json)
}
```

##### 4. Models

O modelo é uma struct do tipo `User`. É bem simples e representa os dados de usuário que serão necessários para cadastrá-lo no banco de dados. Essa struct está declarada em `user.go`.

```go
type User struct {
  ID       int
  Name     string
  Email    string
  Password string
  Admin    bool
}
```

##### 5. Config

O arquivo `config.go` possui os dados de conexão com o banco de dados. É um arquivo do tipo `TOML`.

```toml
[database]
user="users_service"
host="127.0.0.1"
port=5434
password="users_service"
name="users_service"
sslmode="disable"
```

##### 6. Main

O arquivo `main.go` é o mais importante do projeto. Nele estão contidas as declarações das URLS que constam na API.

```go
func main() {
   dao.InitDB()
   defer dao.CloseDB()

   r := mux.NewRouter()
   r.HandleFunc("/v1/users/", handlers.GetAllUsersHandler).Methods("GET")
   r.HandleFunc("/v1/users/{id}", handlers.GetUserByIDHandler).Methods("GET")
   r.HandleFunc("/v1/users/", handlers.CreateUserHandler).Methods("POST")
   r.HandleFunc("/v1/users/{id}", handlers.UpdateUserHandler).Methods("PUT")
   r.HandleFunc("/v1/users/{id}", handlers.DeleteUserHandler).Methods("DELETE")
   if err := http.ListenAndServe(":8001", r); err != nil {
     log.Fatal(err)
   }
}
```

## Futuras melhorias

* Adicionar testes.

* Permitir a integração de novos banco de dados.

* Transformar a API em um microserviço.

* Adicionar autenticação\autorização de usuários.


## Links interessantes

[Um pouco da história do Go](https://golang.org/doc/faq#history)

[Instalando o Go](https://golang.org/doc/install)

[A tour of Go](https://go-tour-br.appspot.com/welcome/1)

[Effective Go](https://golang.org/doc/effective_go.html)

[The Go Blog](https://blog.golang.org/)

[Go at Google: Language Design in the Service of Software Engineering](https://talks.golang.org/2012/splash.article)

[TOML](https://github.com/toml-lang/toml)

[Status code HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)

[Roy Fielding’s REST dissertation](https://oleb.net/2018/rest/)

[Irresistible APIs](https://www.manning.com/books/irresistible-apis)
