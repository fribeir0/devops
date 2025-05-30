# Instalação do Prometheus

> Passo a passo para instalar e configurar o Prometheus em sistemas Linux.

---

## 1. Pré-requisitos

- Acesso root ou usuário com permissão `sudo`

---

## 2. Criar usuários e diretórios

1. Crie o grupo e usuário **prometheus** (sem login):
   ```bash
   sudo groupadd --system prometheus
   sudo useradd --system --no-create-home \
       --shell /sbin/nologin \
       --gid prometheus \
       prometheus
   ```

2. Crie diretórios para configurações e dados:
   ```bash
   sudo mkdir -p /etc/prometheus
   sudo mkdir -p /var/lib/prometheus
   ```

---

## 3. Baixar e instalar

1. Acesse a página de downloads para pegar a **URL** da versão desejada:
   ```bash
   # Exemplo de URL (substitua pela versão mais recente)
   PROM_VERSION="2.45.0"
   wget https://github.com/prometheus/prometheus/releases/download/v${PROM_VERSION}/prometheus-${PROM_VERSION}.linux-amd64.tar.gz
   ```

2. Descompacte o pacote:
   ```bash
   tar -xvf prometheus-${PROM_VERSION}.linux-amd64.tar.gz
   cd prometheus-${PROM_VERSION}.linux-amd64
   ```

3. Instale os binários:
   ```bash
   sudo mv prometheus promtool /usr/local/bin/
   sudo chown prometheus:prometheus /usr/local/bin/prometheus
   sudo chown prometheus:prometheus /usr/local/bin/promtool
   ```

---

## 4. Configurar arquivos

1. Movendo consoles e configurações:
   ```bash
   sudo mv consoles/ console_libraries/ prometheus.yml /etc/prometheus/
   ```

2. Ajuste permissões:
   ```bash
   sudo chown -R prometheus:prometheus /etc/prometheus
   sudo chown -R prometheus:prometheus /var/lib/prometheus
   ```

---

## 5. Criar serviço systemd

Crie o arquivo `/etc/systemd/system/prometheus.service` com o conteúdo:

```ini
[Unit]
Description=Prometheus Monitoring
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

---

## 6. Habilitar e iniciar serviço

```bash
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

### Comandos úteis

- **Reiniciar Prometheus**  
  ```bash
  sudo systemctl restart prometheus
  ```

- **Verificar status**  
  ```bash
  sudo systemctl status prometheus
  ```

---

## 7. Acessando a interface

Abra no navegador:

```
http://<IP_DO_SERVIDOR>:9090
```

---

### Dicas

- Substitua `PROM_VERSION` pela última versão disponível, ou desejada.
- Verifique as opções de configuração em [`prometheus.yml`](https://prometheus.io/docs/prometheus/latest/configuration/configuration/).

---

**Pronto!** 🎉  
Seu Prometheus está instalado e em execução.
