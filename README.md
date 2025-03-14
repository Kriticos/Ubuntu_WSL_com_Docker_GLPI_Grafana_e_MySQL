# Ubuntu WSL com Docker: GLPI, Grafana e MySQL

## 1. Ubuntu

### Instalar o Ubuntu

Abra o Powershell e digite o codigo abaixo e presione enter novamente

```Powershell
wsl --install ubuntu 
```

Abrindo o ubuntu.

```Powershell
wsl -d Ubuntu
```

**OBS:** na primeira vez que for executado sera solicitado a criação de um usuario e senha

Caso tenha algum problema com a questão de data e hora

```bash
sudo rm /etc/resolv.conf
sudo bash -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'
sudo bash -c 'echo "[network]" > /etc/wsl.conf'
sudo bash -c 'echo "generateResolvConf = false" >> /etc/wsl.conf'
sudo chattr +i /etc/resolv.conf
```

## 2. Docker

### Instalando o Docker

Copie e cole cada um dos comandos abaixo, pressionando Enter após cada um:

```bash
sudo apt-get update

sudo apt-get install ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

sudo sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 3. Rede Docker

Crie a rede `network-docker` com o comando:

```bash
sudo docker network create --driver bridge --subnet 172.18.0.0/16 network-docker
```

## 4. Estrutura de Pastas

### Criando as pastas

```bash
sudo mkdir -p /bskp/{grafana,mysql,databases,glpi}
```

**OBS:** Apos criar as pastas acesse o Windows Explorer e copia os arquivos para usas respectivas pastas.
**Lembre-se de alterar os arquivos .env**

### Permissões das Pastas

```bash
sudo chown -R 472:472 /bskp/grafana
sudo sudo chmod -R 755 /bskp/grafana
sudo chown -R 999:999 /bskp/databases
sudo chmod -R 755 /bskp/databases
sudo chmod 644 /bskp/mysql/docker-file/init.sql
sudo chown -R 1000:1000 /bskp/glpi
sudo chmod -R 755 /bskp/glpi
```

## 5. Subindo os containers

### SRV-MySQL

```bash
sudo docker compose -f /bskp/mysql/mysql.yml up -d
```

### SRV-GLPI

```bash
sudo docker compose -f /bskp/glpi/glpi.yml up -d
```

### SRV-GRAFANA

```bash
sudo docker compose -f /bskp/grafana/grafana.yml up -d
```

## Acesando as ferramentas

### Grafana

```html
http://localhost:6080/
```

### GLPI

```html
http://localhost:7080/
```

# Configurações Adicionais

Esta seção permite que o Ubuntu seja executado automaticamente sempre que você fizer logon no Windows, além de configurar o ambiente Ubuntu/WSL para iniciar o serviço Docker e ajustar a alocação de recursos.

---

## A. Configurando o Script wsl_ubuntu.ps1

1. **Criar o Script**  
   - Abra o Bloco de Notas e cole o texto abaixo:

   ```bash
       wsl -d ubuntu -u root
   ```

2. **Salvar o Script**  
   - Salve o arquivo na pasta **"C:\Scripts"** com o nome **.ps1**.

---

## B. Criando uma Tarefa no Agendador do Windows

Siga os passos abaixo para configurar uma tarefa que executa o wsl_ubuntu automaticamente:

1. **Abrir o Agendador de Tarefas**  
   - Clique com o botão direito do mouse em **"Biblioteca do Agendador de Tarefas"**.  
   - Selecione **"Criar Tarefa Básica"**.

2. **Definir Nome e Gatilho**  
   - Informe um nome para a tarefa e clique em **"Avançar"**.  
   - Selecione **"Ao fazer logon"** como gatilho e clique em **"Avançar"**.

3. **Configurar a Ação**  
   - Selecione **"Iniciar um programa"** e clique em **"Avançar"**.  
   - No campo **"Programa/Script"**, insira o seguinte caminho:

   ```txt
       C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
   ```

   - No campo **"Adicione argumentos"** (opcional), insira:

   ```txt
       -File "C:\Scripts\wsl_ubuntu.ps1"
   ```

4. **Finalizar Configuração**  
   - Clique em **"Avançar"** e, em seguida, em **"Concluir"** para salvar a tarefa.

> Agora, sua tarefa será executada automaticamente ao fazer logon no sistema.

---

## C. Configurando o Ubuntu e o Docker no WSL

Após configurar a tarefa, siga os passos abaixo para ajustar o ambiente Ubuntu/WSL:

1. **Abrir o Ubuntu via PowerShell**  
   - Abra o PowerShell e digite:

   ```bash
       wsl -d ubuntu -u root
   ```

2. **Editar o Arquivo wsl.conf**  
   - No terminal, execute:

   ```bash
       nano /etc/wsl.conf
   ```

3. **Adicionar Configuração para Iniciar o Docker**  
   - Ao final do arquivo, adicione a linha abaixo. Em seguida, pressione **Ctrl + X**, depois **Y** para salvar e **Enter** para sair:

   ```bash
       command = "service docker start"
   ```

4. **Sair do Terminal**  
   - Digite:

   ```bash
       exit
   ```

---

## D. Adicional 02: Ajuste de Memória e Processadores para o WSL

1. **Criar o Arquivo de Configuração**  
   - Abra o Bloco de Notas e digite o seguinte:

   ```notepad
       [wsl2]
       memory=4GB
       processors=4
   ```

2. **Salvar o Arquivo**  
   - Salve o arquivo na pasta do seu usuário do Windows (por exemplo, **C:\Usuarios\NOME_DO_SEU_USUARIO\\**) com o nome **.wslconfig**.

> **OBS:** Altere os valores de memória e processadores conforme necessário para otimizar o desempenho do WSL.
