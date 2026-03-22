# Camada Aplicação com Transporte TCP e UDP

**Objetivo:** 
* Dominar o ambiente do Packet Tracer;
* Compreender o funcionamento dos protocolos da camada de transporte (TCP e UDP), analisar portas, números de sequência, confirmações (ACK) e o processo de multiplexação;
* Explorar os serviços da camada de Aplicação;
  
---

## 0. Preparação do Ambiente (Topologia)
Antes de iniciar a simulação, certifique-se de que a rede está montada:
1.  Vamos aproveitar a topologia da aula passada e adicionar os itens a seguir: 
2.  Vá em **End Devices** e adicione 4 **Clientes:** computadores (PCs) no switch esquerdo e nomeie-os como:
    * `HTTP-Client`
    * `FTP-Client`
    * `DNS-Client`
    * `Email-Client`
3.  Vá en **End Devices** e adicione 2 **Servidores:** servidor no switch direito;
4.  Nomeie os servidores como:
    * `Multi-Server`. Pra isso, vá na aba `Config`, depois **`GLOBAL`** e depois `Settings`, digite `Multi-Server` no campo `Display Name`;
    * `DNS-Server`. Faça o mesmo para este outro servidor.
5.  **Configuração IP e Máscara:** Configure o IP e a máscara de todos os hosts que estão na mesma sub-rede. Pegue suas anotações do mapeamento de rede da última aula. Deve ser igual à tabela abaixo. Então, adicione manualmente o IP em cada PC e servidor conforme a topologia: quem está no switch esquerdo use a faixa de IPs da esquerda. Quem está no switch direito, use a faixa de IPs da direita. **Qual IP escolher para cada host?** A boa prática é a seguinte: servidores e impressoras devem ter os primeiros IPs da faixa. PCs veem na sequência.


| Sub-rede | Localização/Uso             | Endereço de Rede | 1º Útil        | Último Útil     | Broadcast     |   Máscara        | Gateway (Configurado) |
|----------|-----------------------------|------------------|----------------|-----------------|---------------|------------------|-----------------------|
| Sub 1    | LAN Alunos (Esquerda)       | 192.168.0.0      | 192.168.0.1    | 192.168.0.62    | 192.168.0.63  | 255.255.255.192  | 192.168.0.62          |
| Sub 2    | Link WAN (Roteadores)       | 192.168.0.64     | 192.168.0.65   | 192.168.0.126   | 192.168.0.127 | 255.255.255.192  | N/A (Ponto-a-Ponto)   |
| Sub 3    | LAN Profs (Direita)         | 192.168.0.128    | 192.168.0.129  | 192.168.0.190   | 192.168.0.191 | 255.255.255.192  | 192.168.0.190         |
| Sub 4    | Disponível (Reserva)        | 192.168.0.192    | 192.168.0.193  | 192.168.0.254   | 192.168.0.255 | 255.255.255.19   | a definir             |

6.  Configure o **Gateway** em todos os hots: PCs e Sevidores, seguindo a tabela acima;
7.  E certfique-se que os serviços (HTTP, EMAIL e FTP) estão ativos no `Multi-Server`. Clique no Servidor e vá na aba `Service` e depois:
    * HTTP: verifique que está no `On`;
    * EMAIL: verifique que está no `On`;
    * FTP: verifique que está no `On`.

8. **Configuração do DNS-Server**: Você deve montar uma tabela no DNS-Server que tenha o nome e o IP de cada host da sua rede. Para isso, clique no hots **DNS-Server**, vá na aba `Service`, no menu vertical `DNS` você preenche os seguintes campos:
    * **Name:** você digita exatamente a sequência de caracteres que você nomeou um dado host;
    * **Addess:** você digita o IP que você atribiu para ele;
    * Clique em **Add**. Deve ficar uma tabela como a figura a seguir:

<img src="https://github.com/agodoi/m09ec-semana08a/blob/main/assets/tabelaDNS-Server.png" width="600">

Para que essa tabela vai servir? Para você pingar os hosts usando o nome de deles e não o endereço de IP. Mas isso faremos nas etapas seguintes desta aula.

* O DNS do DNS-Server deve ser ele mesmo: 192.168.0.130. Este é o endereço que adotei para a minha rede, já que o 192.168.0.129 ficou para o Multi-Server. Lembrando que o final `.129` é o 1º IP útil da rede `.128` e o combinado é que os servidores tenham os primeiros IPs úteis da respectiva rede. Logo, o final `.129`ficou para o `Multi-Server`e o final `.130` ficou pro `DNS-Server`;
* Desligue todos os serviços do DNS-Server exceto o próprio DNS.

---

## 1. Parte 1: Gerando Tráfego de Rede
Mude para o **Modo de Simulação** e siga os passos abaixo para cada cliente:

<img src="https://github.com/agodoi/m09ec-semana08a/blob/main/assets/simulation.png" width="300">


### A. HTTP-Client
* Abra o `HTTP-Client` > aba **Desktop** > **Web Browser**.
* Digite o IP do `Multi-Server` e clique em **Go**. Feche a janela no X.

### B. FTP-Client
* Abra o `FTP-Client` > aba **Desktop** > **Command Prompt**.
* Digite o comando: `ftp [IP do Multi-Server]`. Feche a janela no X.

### C. DNS-Client
* Abra o `DNS-Client` > aba **Desktop** > **Command Prompt**.
* Digite o comando: `nslookup multiserver.pt.ptu`. Feche a janela no X.

### D. E-mail-Client
* Abra o `Email-Client` > aba **Desktop** > **Email**.
* Clique em **Compose** (Compor) e envie um e-mail para: `user@multiserver.pt.ptu`.
* Assunto e corpo do texto: Qualquer mensagem (ex: "Teste TCP"). Clique em **Send**.

---

## 2. Parte 2: Análise de Protocolos (Exploração de PDUs)
Agora, use o botão **Capture / Forward** (Capturar/Encaminhar) para ver os pacotes (PDUs) se movendo.

### A. Observando a Multiplexação
* Clique em avançar várias vezes;
* Observe como diferentes pacotes de cores variadas (cada um representando um protocolo) viajam pelo mesmo cabo. Isso demonstra a **multiplexação**, onde diversos fluxos de dados compartilham o meio físico.

### B. Analisando o TCP (Exemplo HTTP)
1.  No painel de simulação, clique em **Edit Filters** e selecione apenas **HTTP** e **TCP**;
2.  Clique na PDU (envelope) que sai do `HTTP Client`;
3.  Vá em **Inbound PDU Details** (ou Outbound) e role até a seção **TCP**:
    * **Source Port:** Verifique a porta de origem (geralmente uma porta alta, como 1025).
    * **Dest Port:** Verifique a porta de destino (80 para HTTP).
    * **Sequence Number e ACK:** Observe como esses números garantem que os dados cheguem na ordem correta.

### C. Analisando o UDP (Exemplo DNS)
1.  Altere os filtros para mostrar apenas **DNS** e **UDP**;
2.  Clique na PDU que sai do `DNS Client`;
3.  Observe que, ao contrário do TCP, **não existem números de sequência ou de confirmação (ACK)**;
4.  O UDP é mais rápido, porém "não confiável", pois não confirma o recebimento.

---

## 3. Parte 3: Verificação com o Comando `netstat`
Para ver as conexões ativas "em tempo real", volte para o **Realtime Mode**:

1.  No `MultiServer`, abra o **Command Prompt** e digite: `netstat`;
2.  **O que observar:**
    * Você verá as conexões estabelecidas (**ESTABLISHED**);
    * Identifique as portas: **21** (FTP), **80** (HTTP) e **25** (SMTP/Email);
    * Observe que algumas conexões fecham rapidamente, enquanto o FTP permanece aberto aguardando a senha do usuário;
---


## 4. Parte 4: Verificando o funcionamento do DNS-Server
Vá em pelo menos 3 hosts quaisquer da sua topologia, abra o `Command Prompt` da aba `Desktop` e digite:
* ping `nome do pc` e aguarde o resultado. Note que você não colocou o IP explicitamente e sim, o nome do computador;
* Esse artifício só é possível porque existe o servidor DNS com o nome de cada PC e o seu respectivo IP.

## 5. Parte 5: Verificando o funcionamento do FTP-Server
Essa parte é muito louca. Você vai salvar um arquivo `.txt` (a partir de qualquer host) no Multi-Server já que você o configurou para suportar o FTP.

### A. Criando um arquivo texto qualquer:
* Vá no host FTP-Client, mas você poderia ir em qualquer host da sua topologia e fazer o mesmo; 
* Vá na aba **Desktop**;
* Clique em **Text Editor**;
* Digite um texto de 250 palavras. Pede o GPTO criar um texto doido com 250 palavras para você. Copie e cole no editor de texto, clique em **File** e salve com o nome `meutexto.txt`;

### B. Configurando o Multi-Server como FTP:
* Clique no host **Multi-Server**;
* Vá na aba **Services**;
* Vá no menu vertical **FTP**;
* No campo **User Name** crie um SUPER usuário e uma senha em **Password**. Anote isso aí no canto para não esquecer. Porque SUPER usuário? Porque ele vai ter acesso à tudo no FTP.
* Marque as 5 caixinhas: **Write**, **Read**, **Delete**, **Rename** e **List**;
* Clique em **Add**;
* Agora crie um MINI usuário e com outra senha em **Password**. Novamente, anota isso aí para não esquecer. Este **MINI** vai ter restrições de acesso ao FTP.
* Marque as caixinhas: **Read** e **List**;
* Clique em **Add**.

<img src="https://github.com/agodoi/m09ec-semana08a/blob/main/assets/FTP.png" width="600">

## Responda no Google Forms:
* **Cores das PDUs:** Por que têm pacotes de cores diferentes?
* **Segurança:** Por que o FTP ou HTTP são considerados "não seguros" em comparação com versões criptografadas (HTTPS/SFTP)? Notou que os dados (incluindo senhas) viajam em texto claro no TCP básico?
* **Portas Bem Conhecidas:** Atente-se à memorização das portas:
    * DNS: 53 (UDP)
    * HTTP: 80 (TCP)
    * FTP: 21 (TCP)
    * SMTP: 25 (TCP)
