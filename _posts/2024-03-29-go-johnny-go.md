---
title: Go, Johnny Go!
author: v0xn0x
date: 2024-03-29 11:45:00 -0300
categories: [ESTUDO, PROGRAMAÇÃO]
tags: [PROGRAMAÇÃO, GO]
pin: true
math: true
mermaid: true
image:
  path: /img/202403291300.png
  alt: "He was a lonely boy from nowhere’s land"
---

## Introdução

Este post tem como objetivo registrar minha jornada de aprendizado em GoLang, participando de um grupo de estudos dirigido e liderado por [Leonardo Toledo](https://www.linkedin.com/in/leo-toledo/). Este grupo não só me oferece uma estrutura de estudo organizada, mas também um ambiente colaborativo para aprofundar meu conhecimento na linguagem. Todo o material, incluindo aulas e exercícios, está disponível no repositório do [Github](https://github.com/h41stur/gogogo).

Através deste registro, pretendo não apenas solidificar o conhecimento adquirido, mas também compartilhar insights e progressos com quem possa estar na mesma jornada ou considerando embarcar nela. Ao documentar cada aula e desenvolver exercícios propostos, busco uma compreensão mais profunda da linguagem, facilitando assim a absorção e aplicação do conhecimento adquirido.

## Aula 01: Básico do básico

### Como iniciar um projeto em Go

Tudo começa com a fundação. No mundo Go, isso significa executar um comando que, embora simples, é poderoso: `go mod init`. Este é o ponto de partida, o grito de guerra para qualquer projeto. Ao digitá-lo seguido pelo nome do nosso módulo, estamos não apenas criando um novo espaço para nosso código, mas também estamos nos preparando para uma gestão de dependências eficaz e moderna. O arquivo **go.mod** é criado, e com ele, a promessa de um projeto organizado e pronto para crescer.

```shell
cd ~/Documents/

mkdir Go
cd Go

mkdir aula-01
cd aula-01

# Cria o arquivo que conterá o nosso primeiro código
touch program.go
go mod init program.go
```

### A Estrutura Básica do Código em Go

Com o terreno preparado, é hora de construir. A estrutura básica de um programa Go é um convite à clareza e à eficiência. Cada arquivo começa declarando seu pacote, e para um programa executável, `package main` é a porta de entrada. Dentro deste pacote, a função `main()` se destaca como o coração do nosso projeto, o ponto inicial de toda a execução.

```go
// Adicione esse código ao arquivo program.go
package main

import "fmt"

func main() {
    fmt.Println("Go Go, Go Johnny Go")
}
```

Este snippet é o "Olá, Mundo!" no Go. É aqui que percebemos a sua simplicidade e poder. A função `fmt.Println` nos permite imprimir mensagens na tela, uma demonstração da utilização de pacotes para adicionar funcionalidades ao nosso código.

Para executar o nosso primeiro programa, execute o comando `go run .` no terminal

```shell
cd ~/Documents/Go/aula-01
go run .
```

![img-description](/img/202403291345.png)
_go run ._

#### Utilização dos Pacotes: Ampliando Horizontes

Os pacotes em Go são ferramentas essenciais, bibliotecas que nos permitem estender a funcionalidade de nossos programas. Desde operações de entrada/saída básicas, manipulação de strings até a construção de aplicações web complexas, os pacotes têm um papel fundamental. Importá-los é simples, mas carrega consigo o peso da responsabilidade: cada pacote adicionado é uma nova dependência, um novo elo em nosso projeto.

Importar um pacote é tão fácil quanto adicionar uma linha import no topo do arquivo. A linguagem Go incentiva uma gestão cuidadosa dessas importações, garantindo que cada uma seja justificada e utilizada. Através do comando go get, podemos adicionar novas dependências ao nosso projeto, que são prontamente refletidas em nosso arquivo go.mod, mantendo tudo organizado e acessível.

#### Criando pacotes

Em Go, criar seu próprio pacote é um passo excitante para modularizar seu código, promovendo reutilização e clareza. Para criar um pacote, comece definindo um novo diretório dentro do seu projeto. O nome deste diretório será o nome do seu pacote. Dentro deste diretório, cada arquivo .go deve iniciar declarando este pacote através da palavra-chave package.

Exemplo:

Imagine que queremos criar um pacote chamado saudacoes para agrupar funções relacionadas a cumprimentos. Primeiro, criamos o diretório saudacoes no nosso projeto, e dentro dele, um arquivo chamado saudacoes.go:

```shell
cd ~/Documents/Go/aula-01

mkdir saudacoes
cd saudacoes

touch saudacoes.go
```

```go
// saudacoes/saudacoes.go
package saudacoes

import "fmt"

// Saudar com uma mensagem de "Olá, Mundo!"
func Saudar(nome string) {
    fmt.Printf("Olá, %s!\n", nome)
}
```

Para utilizar este pacote em nosso programa principal, importamos o caminho completo do pacote e chamamos sua função:

```go
package main

import (
    "meuprojeto/saudacoes"
)

func main() {
    saudacoes.Saudar("Mundo")
}
```

![img-description](/img/202403291419.png)
_package saudacoes_

#### Utilizando pacotes externos

Go facilita a incorporação de pacotes externos, ampliando as possibilidades do que podemos construir. Para adicionar um pacote externo, usamos o comando go get seguido pelo caminho do pacote.

Exemplo:

Vamos usar o popular pacote gorilla/mux, um pacote para roteamento de URL em aplicações web:

```shell
go get -u github.com/gorilla/mux
```

Agora, podemos usá-lo em nosso projeto:

```go
package main

import (
    "net/http"
    "github.com/gorilla/mux"
)

func main() {
    r := mux.NewRouter()
    r.HandleFunc("/", HomeHandler)
    http.ListenAndServe(":8080", r)
}

func HomeHandler(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("Olá, Mundo com Mux!"))
}
```

![img-description](/img/202403291426.png)
_Executando o programa_

![img-description](/img/202403291428.png)
_Mensagem exibida no nacegador_

#### Removendo dependências não utilizadas

Com o tempo, nosso projeto pode acumular dependências que não são mais usadas. Go torna fácil manter seu projeto limpo com o comando go mod tidy. Este comando verifica seu código para encontrar todas as dependências que são realmente utilizadas e remove qualquer coisa que não seja necessária do arquivo go.mod.

Comando:

```shell
go mod tidy
```

Executar este comando regularmente ajuda a manter o projeto limpo e eficiente, garantindo que apenas as dependências necessárias sejam mantidas.x'

## Referências

- [H41stur: gogogo](https://github.com/h41stur/gogogo)
- [Golang: Documentation](https://tip.golang.org/doc/)
