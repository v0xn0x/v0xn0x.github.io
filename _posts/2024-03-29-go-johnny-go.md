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

Eu usaria com cuidado essa tipo de declaração, pois não fica muito claro qual é o seu tipo

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

- **Callbacks:** Passar uma função como argumento para outra função. Isso é útil em padrões de projeto como observadores ou ao trabalhar com [goroutines](https://go.dev/tour/concurrency/1) e canais para processamento assíncrono.

- **Encapsulamento:** Criar closures que podem capturar e manter estado local. Uma função anônima pode acessar variáveis do escopo em que foi definida, permitindo técnicas de encapsulamento e preservação de estado.

- **Operações de coleção:** Aplicar operações em elementos de coleções, como mapas e slices, por exemplo, filtragem, redução e mapeamento, sem a necessidade de criar funções nomeadas explícitas para cada operação.

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

Aqui, a função anônima `filtrar` é usada para determinar se um número em um slice é par. A função anônima permite encapsular a lógica de filtragem diretamente no contexto onde ela é necessária, sem a necessidade de declarar uma função separada.

As funções anônimas são uma ferramenta versátil em Go, permitindo escrever código mais conciso, modular e expressivo, especialmente em cenários que envolvem callbacks, closures, e operações sobre coleções.

Já as funções variádicas em Go são aquelas que podem receber um número variável de argumentos do mesmo tipo. Elas são particularmente úteis quando você precisa de uma função que pode ser chamada com diferentes números de argumentos, oferecendo flexibilidade na forma como as funções são invocadas. Isso é conseguido usando `...` antes do tipo de parâmetro na definição da função, indicando que a função pode aceitar qualquer número de argumentos desse tipo.

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

**Exemplos:**

- **Exemplo 1:** Retorno de resultado e erro

Uma função que tenta abrir um arquivo pode retornar um ponteiro para o arquivo e um valor de erro que indica se a abertura foi bem-sucedida:

```go
package main

import (
    "fmt"
    "os"
)

func abrirArquivo(nome string) (*os.File, error) {
    f, err := os.Open(nome)
    if err != nil {
        return nil, err
    }
    return f, nil
}

func main() {
    arquivo, err := abrirArquivo("exemplo.txt")
    if err != nil {
        fmt.Println("Erro ao abrir o arquivo:", err)
        return
    }
    defer arquivo.Close()
    // Operações com o arquivo...
}
```

- **Exemplo 2:** Retorno de múltiplos valores relacionados

Uma função que divide dois números e retorna o quociente e o resto:

```go
package main

import "fmt"

func dividir(dividendo, divisor int) (quociente, resto int) {
    quociente = dividendo / divisor
    resto = dividendo % divisor
    return
}

func main() {
    q, r := dividir(10, 3)
    fmt.Printf("Quociente: %d, Resto: %d\n", q, r)
}
```

### Exercícios de Fixação

1. Declare uma variável chamada idade do tipo int e inicialize-a com um valor inteiro que represente sua idade real ou uma idade fictícia de sua escolha.

2. Desenvolva uma função que receba duas strings como argumentos. A função deve concatenar essas duas strings, combinando-as em uma única string, e então retornar o resultado dessa combinação.

3. Declare um Map que mapeia nomes de frutas a suas cores e acesse um valor.

4. Crie um struct Carro com os campos Marca e Ano, instancie-o e printe seus valores.

5. Escreva uma função variádica que calcule a média de um slice de float64 e retorne dois valores: a média e um booleano indicando sucesso.

## Aula 3: Rock 'N' Roll

### Operadores Aritméticos

Em Go, os operadores aritméticos são utilizados para realizar operações matemáticas básicas. Eles incluem

- Adição (+): Adiciona dois operandos
- Subtração (-): Subtrai o segundo operando do primeiro
- Multiplicação (\*): Multiplica ambos operandos
- Divisão (/): Divide o numerador pelo denominador
- Módulo (%): Retorna o resto da divisão
- Incremento (++): Aumenta o valor do operando em 1
- Decremento (--): Diminui o valor do operando em 1

**Exemplo prático:** Calcular o total de uma fatura, aplicando descontos e adicionando impostos, é uma operação comum em sistemas financeiros.

```go
package main

import "fmt"

func calculateInvoice(subtotal, discount, taxRate float64) float64 {
	discountAmount := subtotal * (discount / 100)              // Calcula o valor do desconto
	taxAmount := (subtotal - discountAmount) * (taxRate / 100) // Calcula o valor do imposto
	total := subtotal - discountAmount + taxAmount             // Calcula o total final
	return total
}

func main() {
	fmt.Println(calculateInvoice(1000, 10, 1))
}
```

### Operadores Relacionais

Os operadores relacionais são usados para comparar dois valores, retornando um valor booleano (true ou false). Os operadores incluem:

- == (igual a)
- != (diferente de)
- < (menor que)
- \> (maior que)
- <= (menor ou igual a)
- \>= (maior ou igual a)

**Exemplo prático:** Verificar se uma data de pagamento está atrasada em comparação com a data atual.

```go
package main

import (
	"fmt"
	"time"
)

func isPaymentOverdue(dueDate, currentDate time.Time) bool {
	return currentDate.After(dueDate) // Retorna true se a data atual for depois da data de vencimento
}

func main() {
	dueDate := time.Date(2024, time.April, 10, 0, 0, 0, 0, time.UTC)
	currentDate := time.Now()

	if isPaymentOverdue(dueDate, currentDate) {
		fmt.Println("Vencido")
	} else {
		fmt.Println("A Vencer")
	}
}
```

### Operadores de Atribuição

Os operadores de atribuição em Go são usados para definir ou modificar o valor de uma variável. Eles incluem:

- = (atribuição simples)
- +=, -=, \*=, /=, %= (atribuição com operação)

**Exemplo prático:** Atualizar o saldo de uma conta após um pagamento.

```go
package main

import "fmt"

func updateBalance(balance *float64, payment float64) {
	*balance -= payment // Subtrai o pagamento do saldo
}

func main() {
	var balance float64 = 10000
	var payment float64 = 5000

	fmt.Printf("Balaço atual: R$ %.2f\r\n", balance)
	fmt.Printf("Pagamento: R$ %.2f\r\n", payment)

	updateBalance(&balance, payment)

	fmt.Printf("Balaço atualizado: R$ %.2f\r\n", balance)
}
```

### Operadores Lógicos

Os operadores lógicos permitem a combinação de valores booleanos. São eles:

- && (E lógico)
- \|\| (OU lógico)
- ! (NÃO lógico)

**Exemplo prático:** Determinar se uma transação deve ser processada, com base no status da conta e no saldo disponível.

```go
package main

import "fmt"

func shouldProcessTransaction(isAccountActive bool, hasSufficientFunds bool) bool {
	return isAccountActive && hasSufficientFunds // Processa apenas se a conta está ativa e tem fundos suficientes
}

func main() {
	var isAccountActive bool = false
	var hasSufficientFunds bool = true

	if shouldProcessTransaction(isAccountActive, hasSufficientFunds) {
		fmt.Println("Operação processada")
	} else if !isAccountActive {
		fmt.Println("A conta está inativa")
	} else if !hasSufficientFunds {
		fmt.Println("Fundos insuficientes")
	}
}
```

### Operadores Unários

Operadores unários em Go operam em um único operando. Eles incluem:

- +, - (operadores de sinal)
- !, ^ (negativo lógico e bitwise NOT)
- \*, & (operadores de ponteiros para dereferência e endereço)

**Exemplo prático:** Negar o valor de uma quantia para registrar um estorno.

```go
package main

import "fmt"

func negateAmount(amount float64) float64 {
	return -amount // Muda o sinal do valor para indicar um estorno
}

func main() {
	var amount float64 = 120

	fmt.Printf("Sinal invertido: %.2f", negateAmount(amount))
}
```

### Structs

#### Declarando structs com inferência de tipos

Structs são tipos de dados compostos em Go que agrupam variáveis relacionadas. Ao usar :=, o tipo da struct é inferido pelo compilador com base nos valores atribuídos.

**Exemplo prático:**

```go
package main

import "fmt"

type Transaction struct {
	Amount   float64
	Category string
}

func main() {
	transaction := Transaction{100.0, "Revenue"}
	fmt.Println(transaction) // Mostra a transação com amount 100 e category Revenue
}
```

#### Declarando structs sem ter todos os valores

Em Go, você pode criar uma instância de uma struct sem fornecer valores para todos os campos. Isso é útil quando você deseja inicializar apenas certos campos com valores.

**Exemplo prático:**

```go
package main

import (
	"fmt"
	"time"
)

type Payment struct {
	Amount   float64
	Date     time.Time
	Receiver string
}

func main() {
	payment := Payment{Amount: 150.0} // Só o Amount é inicializado
	fmt.Println(payment)              // Mostra a estrutura de Payment com apenas o Amount definido
}
```

### Structs dentro de structs

Structs podem ser aninhadas dentro de outras para criar estruturas de dados mais complexas.

**Exemplo prático:**

```go
package main

import "fmt"

type Invoice struct {
	ID           string
	Transactions []Transaction // Lista de transações na fatura
}

type Transaction struct {
	Amount   float64
	Category string
}

func main() {
	t1 := Transaction{100.00, "Revenue"}
	t2 := Transaction{200.00, "Subscription"}

	transactions := []Transaction{t1, t2}

	invoice := Invoice{"123456789", transactions}

	fmt.Println(invoice)
}
```

### Herança

Embora Go não suporte herança direta, você pode usar composição para alcançar uma funcionalidade similar, incorporando uma struct em outra.

**Exemplo prático:**

```go
package main

import "fmt"

type BaseAccount struct {
	AccountNumber string
	Balance       float64
}

type CheckingAccount struct {
	BaseAccount    // Composição: BaseAccount é incorporada em CheckingAccount
	OverdraftLimit float64
}

func main() {
	baseAccount := BaseAccount{"AXZ", 123.45}
	checkingAccount := CheckingAccount{baseAccount, 456.78}

	fmt.Println(checkingAccount)
}
```

### Ponteiros

Ponteiros em Go são usados para referenciar diretamente a memória onde um valor está armazenado. Eles são essenciais para operações de baixo nível e para manipular grandes estruturas de dados sem copiar valores.

**Exemplo prático:** Alterar o saldo de uma conta diretamente usando ponteiros.

```go
package main

import "fmt"

func updateBalance(balance *float64, payment float64) {
	*balance -= payment // Subtrai o pagamento do saldo
}

func main() {
	balance := 2000.0
	updateBalance(&balance, 150.0) // Passa o endereço de balance para a função
	fmt.Println(balance)           // Mostra o saldo atualizado
}
```

### Exercícios de Fixação

## Aula 4: Marty McFly

### Arrays em Go

Arrays em Go são coleções de elementos do mesmo tipo com tamanho fixo. No contexto de um módulo financeiro de um ERP, vamos usar um array para armazenar os códigos de transações financeiras.

**Inserindo dados em um array:**

```go
package main

import "fmt"

func main() {
    var transactionCodes [5]int
    transactionCodes[0] = 101
    transactionCodes[1] = 102
    transactionCodes[2] = 103
    transactionCodes[3] = 104
    transactionCodes[4] = 105

    fmt.Println(transactionCodes)
}
```

**Consultando dados em um array:**

```go
package main

import "fmt"

func main() {
    transactionCodes := [5]int{101, 102, 103, 104, 105}

    // Acessando o terceiro código de transação
    fmt.Println("Terceiro código de transação:", transactionCodes[2])
}
```

**Excluindo dados em um array:**

Arrays em Go têm um tamanho fixo, então não é possível "excluir" um elemento diretamente. Podemos, no entanto, zerar um elemento ou reorganizar o array.

```go
package main

import "fmt"

func main() {
    transactionCodes := [5]int{101, 102, 103, 104, 105}

    // Zerando o terceiro código de transação
    transactionCodes[2] = 0

    fmt.Println(transactionCodes)
}
```

### Slices

Slices são abstrações dinâmicas de arrays que podem mudar de tamanho. Slices são ideais para listas de elementos que variam durante a execução do programa, como uma lista de transações pendentes em um módulo financeiro.

**Inserindo dados em um slice:**

```go
package main

import "fmt"

func main() {
    transactions := []string{}

    // Adicionando transações
    transactions = append(transactions, "Transação 1")
    transactions = append(transactions, "Transação 2")

    fmt.Println(transactions)
}
```

**Consultando dados em um slice:**

```go
package main

import "fmt"

func main() {
    transactions := []string{"Transação 1", "Transação 2", "Transação 3"}

    // Acessando a segunda transação
    fmt.Println("Segunda transação:", transactions[1])
}
```

**Excluindo dados em um slice:**

```go
package main

import "fmt"

func main() {
    transactions := []string{"Transação 1", "Transação 2", "Transação 3"}

    // Removendo a segunda transação
    transactions = append(transactions[:1], transactions[2:]...)

    fmt.Println(transactions)
}
```

### Maps

Maps são estruturas que armazenam dados em pares chave-valor. Em um módulo financeiro, um map pode ser usado para associar o ID de uma transação ao seu status.

**Inserindo dados em um map:**

```go
package main

import "fmt"

func main() {
	transactionStatus := make(map[int]string)

	// Adicionando status de transações
	transactionStatus[1] = "Pendente"
	transactionStatus[2] = "Concluído"
	transactionStatus[3] = "Falhou"

	fmt.Println(transactionStatus)
}
```

**Consultando dados em um map:**

```go
package main

import "fmt"

func main() {
    transactionStatus := map[int]string{
        1: "Pendente",
        2: "Concluído",
        3: "Falhou",
    }

    // Verificando o status da transação 2
    fmt.Println("Status da transação 2:", transactionStatus[2])
}
```

**Excluindo dados em um map:**

```go
package main

import "fmt"

func main() {
    transactionStatus := map[int]string{
        1: "Pendente",
        2: "Concluído",
        3: "Falhou",
    }

    // Removendo uma transação
    delete(transactionStatus, 2)

    fmt.Println(transactionStatus)
}
```

### Estruturas de Controle

Estruturas de controle permitem direcionar o fluxo de execução do código. Aqui estão alguns exemplos básicos usando condições e seleção:

Uso de if, else if, else, e if com inicialização:

```go
package main

import "fmt"

func main() {
    balance := 5000
    withdrawAmount := 6000

    if balance < withdrawAmount {
        fmt.Println("Saldo insuficiente para saque")
    } else if balance == withdrawAmount {
        fmt.Println("Saque integral do saldo")
    } else {
        fmt.Println("Saque realizado com sucesso")
    }

    // Exemplo com inicialização
    if newTransaction := 700; newTransaction > 500 {
        fmt.Println("Transação importante")
    }
}
```

Uso de switch:

```go
package main

import "fmt"

func main() {
    statusCode := 2

    switch statusCode {
    case 1:
        fmt.Println("Transação pendente")
    case 2:
        fmt.Println("Transação concluída")
    case 3:
        fmt.Println("Transação falhou")
    default:
        fmt.Println("Código de status desconhecido")
    }
}
```

## Aula 5:

### Loops

Laço for semelhante a while

Em Go, o laço for pode ser usado como um laço while de outras linguagens, omitindo-se a inicialização e o incremento:

```go
package main

import "fmt"

func main() {
    balance := 1000
    goal := 5000

    for balance < goal {
        balance += 500
        fmt.Println("Saldo atualizado:", balance)
    }
}
```

Este exemplo simula a acumulação de saldo até atingir um valor objetivo, muito comum em módulos financeiros para verificar metas de economia ou investimento.

**Laço for com cláusula range**
A cláusula range permite iterar sobre slices, arrays e maps. Vamos usar range para processar transações financeiras:

## Referências

- [H41stur: gogogo](https://github.com/h41stur/gogogo)
- [Golang: Documentation](https://tip.golang.org/doc/)

  {% include embed/youtube.html id='6-49l--smnc' %}
