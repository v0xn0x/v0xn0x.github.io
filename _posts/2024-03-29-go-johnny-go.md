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

Este post tem como objetivo registrar minha jornada de aprendizado em GoLang (ou simplismente Go) através de um grupo de estudos que estou participandl na qual é liderado por [Leonardo Toledo](https://www.linkedin.com/in/leo-toledo/). Este grupo não só me oferece uma estrutura de estudo organizada, mas também um ambiente colaborativo para aprofundar meu conhecimento na linguagem. Todo o material, incluindo aulas e exercícios, está disponível no repositório do [Github](https://github.com/h41stur/gogogo).

Através deste registro, pretendo não apenas solidificar o conhecimento adquirido, mas também compartilhar insights e progressos com quem possa estar na mesma jornada ou considerando embarcar nela. Ao documentar cada aula e desenvolver exercícios propostos, busco uma compreensão mais profunda da linguagem, facilitando assim a absorção e aplicação do conhecimento adquirido.

## Aula 01: Básico do básico

### Como iniciar um projeto em Go

Tudo começa com a fundação. No mundo Go, isso significa executar um comando que, embora simples, é poderoso: `go mod init`. Este é o ponto de partida, o grito de guerra para qualquer projeto. Ao digitá-lo seguido pelo nome do nosso módulo, estamos não apenas criando um novo espaço para nosso código, mas também estamos nos preparando para uma gestão de dependências eficaz e moderna. O arquivo `go.mod` é criado, e com ele, a promessa de um projeto organizado e pronto para crescer.

- Os comandos a seguir demonstram a criação dos diretórios do nosso primeiro programa, bem com a criação do arquivo responsável pelo código fonte e a inicialização do projeto.

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

- Edite o arquivo `program.go` (recomendo o [Visual Studio Code](https://code.visualstudio.com/)) e adicione o código a seguir.

```go
// Adicione esse código ao arquivo program.go
package main

import "fmt"

func main() {
    fmt.Println("Go Go, Go Johnny Go")
}
```

Este snippet é o `Olá, Mundo!` no Go. É aqui que percebemos a sua simplicidade e poder. A função `fmt.Println` nos permite imprimir mensagens na tela, uma demonstração da utilização de pacotes para adicionar funcionalidades ao nosso código.

- Para executar o nosso primeiro programa, execute o comando `go run .` no terminal

```shell
cd ~/Documents/Go/aula-01
go run .
```

![img-description](/img/202403291345.png)
_go run ._

#### Utilização dos Pacotes: Ampliando Horizontes

Os pacotes em Go são ferramentas essenciais, bibliotecas que nos permitem estender a funcionalidade de nossos programas. Desde operações de entrada/saída básicas, manipulação de strings até a construção de aplicações web complexas, os pacotes têm um papel fundamental. Importá-los é simples, mas carrega consigo o peso da responsabilidade: cada pacote adicionado é uma nova dependência, um novo elo em nosso projeto.

Importar um pacote é tão fácil quanto adicionar uma linha import no topo do arquivo. A linguagem Go incentiva uma gestão cuidadosa dessas importações, garantindo que cada uma seja justificada e utilizada. Através do comando `go get`, podemos adicionar novas dependências ao nosso projeto, que são prontamente refletidas em nosso arquivo go.mod, mantendo tudo organizado e acessível.

#### Criando pacotes

Em Go, criar seu próprio pacote é um passo excitante para modularizar seu código, promovendo reutilização e clareza. Para criar um pacote, comece definindo um novo diretório dentro do seu projeto. O nome deste diretório será o nome do seu pacote. Dentro deste diretório, cada arquivo .go deve iniciar declarando este pacote através da palavra-chave package.

**Exemplos:**

Imagine que queremos criar um pacote chamado saudacoes para agrupar funções relacionadas a cumprimentos. Primeiro, criamos o diretório saudacoes no nosso projeto, e dentro dele, um arquivo chamado `saudacoes.go`:

- Comandos para a criação do diretório do pacote e o primeiro arquivo desse pacote.

```shell
cd ~/Documents/Go/aula-01

mkdir saudacoes
cd saudacoes

touch saudacoes.go
```

- Edite o arquivo `saudacoes/saudacoes.go` e adicione o código a seguir.

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
    "program.go/saudacoes"
)

func main() {
    saudacoes.Saudar("Chuck Berry")
}
```

![img-description](/img/202403291419.png)
_package saudacoes_

#### Utilizando pacotes externos

Go facilita a incorporação de pacotes externos, ampliando as possibilidades do que podemos construir. Para adicionar um pacote externo, usamos o comando `go get` seguido pelo caminho do pacote.

**Exemplos:**

Vamos usar o popular pacote [`gorilla/mux`](https://pkg.go.dev/github.com/gorilla/mux@v1.8.1), um pacote para roteamento de URL em aplicações web:

- Execute o comando no terminal dentro da pasta do projeto:

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

- Execute o comando no terminal dentro da pasta do projeto:

```shell
go mod tidy
```

Executar este comando regularmente ajuda a manter o projeto limpo e eficiente, garantindo que apenas as dependências necessárias sejam mantidas.x'

## Exercícios

Os exercícios propostos a seguir são baseados nos conceitos abordados durante nossa última aula, porém introduzem um nível adicional de desafio. O objetivo é aguçar sua curiosidade e fortalecer sua capacidade de solucionar problemas complexos, habilidades essenciais no cotidiano de um desenvolvedor.

As respostas não serão fornecidadas 😅😅😅, mas podemos debater no nosso grupo de estudos.

1. Crie um novo projeto em Go chamado `hello_world` e execute um programa que imprima `Hello, World!` no terminal.

2. Modifique o programa `hello_world` de tal forma que ao executá-lo ele solicite que um nome seja digitado e então ele exiba a mensagem `Hello, \<nome_digitado\>`, por exemplo, `Hello, João!`.

3. Utilize o pacote `strings` para converter o nome digitado para maiúsculo antes de cumprimentá-lo, por exemplo, `Olá, JÚLIA!`

## Aula 2: Mergulhando em Go

No dia 28-mar-2024 as 22:10 tivemos a nossa segunda aula de Go. Vou guiá-lo através de cada tópico com exemplos de código para que vocês (principalmente eu) possa seguir e fixar facilmente os conceitos.

### Variáveis em Go

Variáveis (em qualquer linguagem de programação) são espaços na memória onde você pode armazenar dados. Em Go, você pode declarar uma variável de várias maneiras:

**Declaração Simples**

Esta talvez seja a minha declaração favorita!

```go
var nome string = "Go"
```

**Declaração Múltipla**

```go
var x, y int = 5, 10
```

**Inicialização Durante a Declaração**

Eu usaria com cuidado essa tipo de declaração, pois não fica muito claro que é o seu tipo

```go
idade := 25 // Tipo inferido pelo compilador
```

**Auto Inferência e Constantes**

Go é inteligente o suficiente para inferir o tipo de dados baseado no valor atribuído, porém eu teria o mesmo cuidado da `Inicialização Durante a Declaração`

```go
const PI = 3.14
```

Aqui é importante destacar que foi declarada uma constante e que ao contrário de uma variável, uma vez que o seu valor é atribuido ele não mudará, por isso o nome `constante`.

### Tipos de Dados

Go é um linguagem fortemente tipada, ou seja, ela exige que o tipo de dados de uma variável seja definido explicitamente e seja rigorosamente mantido ao longo do código, sem permitir alterações implícitas ou automáticas de tipo. Isso significa que, se uma variável é declarada como sendo de um certo tipo de dados, qualquer operação que envolva essa variável deve ser compatível com seu tipo, e tentativas de usar essa variável com um tipo diferente de dados resultarão em erros em tempo de compilação ou execução.

Além disso, a linguagem Go suporta vários tipos de dados, incluindo inteiros (int, uint, int64, etc.), numéricos não inteiros (como float64 e complex128), strings, e muito mais.

**Strings**

```go
var saudacao string = "Olá, Go!"
```

**Array Slices e Map**

Slices são tipos de dados dinâmicos que podem crescer e encolher.

```go
slice := []int{1, 2, 3}
```

Map é uma coleção de pares chave-valor.

```go
mapa := map[string]int{"um": 1, "dois": 2}
```

**Structs e Pointers**

Structs permitem criar tipos de dados customizados.

```go
type Pessoa struct {
    Nome string
    Idade int
}
```

Pointers armazenam o endereço de memória de variáveis.

```go
var x int = 1
var p *int = &x
```

### Funções

Em Go, funções são cidadãs de primeira classe, permitindo que sejam passadas como argumentos e usadas como valores.

Declaração de Funções

```go
func soma(a int, b int) int {
    return a + b
}
```

**Funções Anônimas e Variadicas**

Funções anônimas, também conhecidas como funções lambda ou closures, são uma funcionalidade poderosa em muitas linguagens de programação, incluindo Go. Elas permitem que você declare uma função sem dar um nome a ela, possibilitando a definição de funções inline ou a passagem de funções como argumentos para outras funções. As funções anônimas são particularmente úteis para operações de curta duração que são utilizadas apenas em um contexto específico, simplificando o código ao evitar a necessidade de declarar uma função nomeada separada.

Para que servem:

- **Callbacks**: Passar uma função como argumento para outra função. Isso é útil em padrões de projeto como observadores ou ao trabalhar com goroutines e canais para processamento assíncrono.

- **Encapsulamento**: Criar closures que podem capturar e manter estado local. Uma função anônima pode acessar variáveis do escopo em que foi definida, permitindo técnicas de encapsulamento e preservação de estado.

- **Operações** de coleção: Aplicar operações em elementos de coleções, como mapas e slices, por exemplo, filtragem, redução e mapeamento, sem a necessidade de criar funções nomeadas explícitas para cada operação.

**Exemplos:**

- **Exemplo 1**: Usando uma função anônima para um callback

Imagine que você quer executar uma função que realiza uma tarefa e, em seguida, chama uma função de callback para sinalizar a conclusão da tarefa:

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	processarTarefa(func() {
		fmt.Println("Tarefa completada!")
	})
}

func processarTarefa(callback func()) {
	fmt.Println("Processando tarefa...")
	time.Sleep(2 * time.Second) // Simula uma tarefa sendo processada
	callback()                  // Chama a função de callback
}
```

Neste exemplo, a função processarTarefa recebe uma função anônima como argumento, que é chamada de callback após a simulação da tarefa.

- **Exemplo 2**: Usando funções anônimas para operações com slices

Você pode usar uma função anônima dentro de um loop ou como parte de operações em slices:

```go
package main

import "fmt"

func main() {
	numeros := []int{1, 2, 3, 4, 5}
	resultado := make([]int, 0)

	// Usando função anônima para filtrar números
	filtrar := func(n int) bool {
		return n%2 == 0 // Condição para filtrar números pares
	}

	for _, numero := range numeros {
		if filtrar(numero) {
			resultado = append(resultado, numero)
		}
	}

	fmt.Println("Números pares:", resultado)
}
```

Aqui, uma função anônima filtrar é usada para determinar se um número em um slice é par. A função anônima permite encapsular a lógica de filtragem diretamente no contexto onde ela é necessária, sem a necessidade de declarar uma função separada.

As funções anônimas são uma ferramenta versátil em Go, permitindo escrever código mais conciso, modular e expressivo, especialmente em cenários que envolvem callbacks, closures, e operações sobre coleções.

Já as funções variádicas em Go são aquelas que podem receber um número variável de argumentos do mesmo tipo. Elas são particularmente úteis quando você precisa de uma função que pode ser chamada com diferentes números de argumentos, oferecendo flexibilidade na forma como as funções são invocadas. Isso é conseguido usando ... antes do tipo de parâmetro na definição da função, indicando que a função pode aceitar qualquer número de argumentos desse tipo.

Para que servem:

- **Flexibilidade**: Permitem que você escreva funções que podem ser chamadas com diferentes quantidades de argumentos, tornando seu código mais flexível e reutilizável.

- **Conveniência**: São úteis para operações que podem ser aplicadas a um conjunto variável de elementos, como somar números, concatenar strings, ou aplicar uma função a uma lista de valores.

- **Simplificação de APIs**: Reduzem a necessidade de definir múltiplas funções para lidar com diferentes quantidades de argumentos, simplificando a API de uma biblioteca ou aplicação.

**Exemplos:**

- **Exemplo 1**: Uma função variádica para somar números.

```go
package main

import "fmt"

// soma aceita uma quantidade variável de argumentos int e retorna a soma deles
func soma(numeros ...int) int {
    total := 0
    for _, numero := range numeros {
        total += numero
    }
    return total
}

func main() {
    fmt.Println(soma(1, 2))          // 3
    fmt.Println(soma(1, 2, 3, 4, 5)) // 15
}
```

Este exemplo demonstra uma função variádica `soma`, que pode ser chamada com qualquer número de argumentos inteiros. A função itera sobre os argumentos e calcula a soma total.

- **Exemplo 2**: Aplicando uma função a uma lista de strings

```go
package main

import (
    "fmt"
    "strings"
)

// processarStrings aplica uma função de transformação a uma lista de strings
func processarStrings(transformar func(string) string, strings ...string) []string {
    resultados := make([]string, len(strings))
    for i, str := range strings {
        resultados[i] = transformar(str)
    }
    return resultados
}

func main() {
    // Chamando processarStrings com uma função anônima que converte strings para maiúsculas
    fmt.Println(processarStrings(strings.ToUpper, "primeiro", "segundo"))
    // Output: [PRIMEIRO SEGUNDO]

    // Chamando com uma função que repete as strings
    fmt.Println(processarStrings(func(s string) string {
        return s + s
    }, "eco", "test"))
    // Output: [ecoeco testtest]
}
```

Neste exemplo, processarStrings é uma função variádica que aplica uma função de transformação especificada a cada string em um slice de strings. A capacidade de passar qualquer número de strings como argumentos torna a função extremamente versátil.

Funções variádicas são uma ferramenta poderosa em Go, permitindo que você escreva códigos mais genéricos e reutilizáveis que podem lidar com um número variável de argumentos de forma elegante. Elas são especialmente úteis em funções que realizam operações de agregação ou aplicam uma operação a uma coleção de elementos.

### Funções com mais de um retorno

Funções com mais de um retorno em Go são uma característica poderosa da linguagem, permitindo que uma função retorne múltiplos valores simultaneamente. Esta capacidade é útil em uma variedade de situações e traz flexibilidade significativa ao design de funções e à gestão de dados retornados.

**Para que servem:**

- **Retorno de resultado e erro:** É um padrão comum em Go retornar o resultado de uma operação junto com um valor de erro que indica se a operação foi bem-sucedida. Isso permite um tratamento de erro robusto e claro.

- **Retorno de múltiplos valores relacionados:** Em algumas situações, pode fazer sentido que uma função calcule e retorne múltiplos valores que estão logicamente relacionados, como coordenadas (x, y) ou um valor dividido e seu resto.

- **Desempacotamento de estruturas de dados:** Uma função pode desempacotar elementos de uma estrutura de dados complexa e retorná-los como valores individuais, facilitando o acesso a esses elementos.

**Utilidade**

- **Clareza e segurança no tratamento de erros:** Ao retornar um valor de erro junto com o resultado de uma função, o código que chama essa função pode imediatamente verificar se ocorreu um erro e lidar com essa situação de forma adequada.

- **Simplificação do código:** Reduz a necessidade de usar estruturas de dados complexas ou ponteiros para retornar múltiplos valores, tornando o código mais legível e fácil de manter.

- **Melhoria na expressividade do código:** Facilita a escrita de funções que naturalmente produzem ou operam com múltiplos valores, tornando o código mais intuitivo e alinhado com a lógica do domínio do problema.

### Exercícios de Fixação

1. Crie uma variável idade do tipo int e atribua seu próprio valor.

2. Escreva uma função que aceite duas strings e retorne a combinação delas.

3. Declare um Map que mapeia nomes de frutas a suas cores e acesse um valor.

4. Crie um struct Carro com os campos Marca e Ano, instancie-o e printe seus valores.

5. Escreva uma função variádica que calcule a média de um slice de float64 e retorne dois valores: a média e um booleano indicando sucesso.

## Referências

- [H41stur: gogogo](https://github.com/h41stur/gogogo)
- [Golang: Documentation](https://tip.golang.org/doc/)
