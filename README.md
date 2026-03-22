# Camada Aplicação com Transporte TCP e UDP

**Objetivo:** 
* Dominar o ambiente do Packet Tracer;
* Compreender o funcionamento dos protocolos da camada de transporte (TCP e UDP), analisar portas, números de sequência, confirmações (ACK) e o processo de multiplexação;
* Explorar os serviços da camada de Aplicação;
  
---

## 0. Preparação do Ambiente (Topologia)
Antes de iniciar a simulação, certifique-se de que a rede está montada:
1.  **Servidor:** Adicione um servidor e nomeie-o como `MultiServer`.
2.  **Clientes:** Adicione 4 computadores (PCs) e nomeie-os como:
    * `HTTP Client`
    * `FTP Client`
    * `DNS Client`
    * `Email Client`
3.  **Conectividade:** Conecte todos os dispositivos a um **Switch** genérico.
4.  **Configuração IP:** Certifique-se de que todos os dispositivos estão na mesma sub-rede (ex: 192.168.1.x) e que os serviços (HTTP, FTP, DNS, EMAIL) estão ativos no `MultiServer`.

---

## 1. Parte 1: Gerando Tráfego de Rede
Mude para o **Modo de Simulação** e siga os passos abaixo para cada cliente:

<img src="https://github.com/agodoi/m09ec-semana08a/blob/main/assets/simulation.png" width="300">


### A. Cliente HTTP
* Abra o `HTTP Client` > aba **Desktop** > **Web Browser**.
* Digite o IP do `MultiServer` e clique em **Go**. Feche a janela no X.

### B. Cliente FTP
* Abra o `FTP Client` > aba **Desktop** > **Command Prompt**.
* Digite o comando: `ftp [IP_do_MultiServer]`. Feche a janela no X.

### C. Cliente DNS
* Abra o `DNS Client` > aba **Desktop** > **Command Prompt**.
* Digite o comando: `nslookup multiserver.pt.ptu`. Feche a janela no X.

### D. Cliente de E-mail
* Abra o `Email Client` > aba **Desktop** > **Email**.
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

## Dicas para o Professor:
* **Cores das PDUs:** Porque têm pacotes de cores diferentes?
* **Segurança:** Por que o FTP ou HTTP são considerados "não seguros" em comparação com versões criptografadas (HTTPS/SFTP)? Notou que os dados (incluindo senhas) viajam em texto claro no TCP básico?
* **Portas Bem Conhecidas:** Atente-se à memorização das portas:
    * DNS: 53 (UDP)
    * HTTP: 80 (TCP)
    * FTP: 21 (TCP)
    * SMTP: 25 (TCP)
