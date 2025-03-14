# Ubuntu(WSL) com Docker + GLPI + Grafana + MySQL

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

**OBS:** Apos criar as pastas acesse o Windows Explorer e copia os arquivos para usas respectivas pastas

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