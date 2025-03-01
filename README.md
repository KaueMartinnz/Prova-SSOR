# Projeto SSOR - Configuração de Proxy Squid em Empresas de TI

## Universidade Federal do Ceará  
**Curso Tecnológico em Segurança da Informação**  
**Segurança em Sistemas Operacionais e Redes de Computadores**  

### Alunos: Antônio Kauê e Natércia Sousa

---

## Objetivo

Este projeto tem como objetivo a implementação de um servidor proxy utilizando o Squid para controlar o acesso a sites. O foco da configuração será:

- Bloquear acesso a sites específicos (como redes sociais);
- Restringir acesso fora dos horários permitidos.

---

---

## Requisitos

### Sistemas Operacionais Utilizados
- **Máquina Virtual:** Linux Ubuntu 22.04.5 LTS
- **Máquina Virtual:** Linux Ubuntu 24.10
- **Máquina Real:** Windows 11 Pro 23H2

### Ambiente de Virtualização
- VirtualBox Versão 7.1.4.165100

### Servidor Proxy Utilizado
- Squid Versão 5.9

---

## Etapas do Projeto

### 1. Configuração da Rede no VirtualBox
Antes de iniciar a configuração do Squid, é necessário configurar a rede da máquina virtual:

1. No VirtualBox, selecione a máquina onde o Squid será instalado.
2. Acesse as **Configurações de Rede**.
3. Configure a conexão para **Placa em Modo Bridge**.
4. No campo **Modo Promíscuo**, selecione **Permitir tudo**.

---

### 2. Configuração Inicial do Squid no Ubuntu

#### Atualização do Sistema
```bash
sudo apt update  
sudo apt upgrade -y
```

#### Instalação do Squid
```bash
sudo apt install squid
squid -v  # Verificação da versão
sudo systemctl status squid
```

### 3. Configuração do Squid

#### Criar Backup do Arquivo de Configuração
```bash
sudo cp /etc/squid/squid.conf /etc/squid/squid.conf.bkp
```

#### Definição de ACLs para Bloqueio de Sites
Edite o arquivo de configuração do Squid:
```bash
sudo nano /etc/squid/squid.conf
```
No final do arquivo adicione os sites a serem bloqueados:
```bash
acl sites_bloqueados dstdomain .facebook.com .instagram.com .tiktok.com
http_access deny sites_bloqueados
```

#### Definição de ACLs para Controle de Horários
```bash
acl horario_permitido time MTWHF 09:30-10:00
acl horario_permitido time MTWHF 12:00-13:00
acl horario_permitido time MTWHF 15:30-16:00
```
Permitir acesso aos sites bloqueados apenas nos horários definidos:
```bash
http_access allow sites_bloqueados horario_permitido
http_access deny sites_bloqueados
```

#### Passo 6: Configurar o IP no Squid
Pesquise a linha `http_port 3128` no arquivo de configuração:
```bash
sudo nano /etc/squid/squid.conf
```
Use `Ctrl + W` para buscar a linha:
```bash
http_port 3128
```
Edite essa linha para incluir o IP da sua rede local, por exemplo:
```bash
http_port 192.168.0.105:3128
```

#### Passo 7: Remover Restrição de Acesso Global
Pesquise a linha `http_access deny all` no arquivo de configuração:
```bash
sudo nano /etc/squid/squid.conf
```
Use `Ctrl + W` para localizar:
```bash
http_access deny all
```
Apague essa linha para permitir o funcionamento correto do proxy.

#### Aplicar as Mudanças e Reiniciar o Squid
```bash
sudo systemctl restart squid
sudo systemctl status squid
```

---

## Configuração do Proxy no Sistema Operacional

### Linux Ubuntu
1. Acesse **Configurações** > **Network Proxy**;
2. Configure o IP da máquina em que o Squid foi instalado;
3. Salve as configurações.

### Firefox (Ubuntu)
1. Acesse **Settings** > **Network Settings**;
2. Insira o IP do servidor Squid e porta **3128**;
3. Marque a opção "Also use this proxy for HTTPS";
4. Salve as configurações.

### Windows 11
1. Acesse **Configurações** > **Rede e Internet** > **Usar um Servidor Proxy**;
2. Insira o IP do servidor e a porta **3128**;
3. Salve as configurações.

---

## Resultados

Os testes foram realizados em máquinas virtuais e físicas. 

- Ao acessar sites bloqueados, o navegador retorna a mensagem: **"O servidor proxy está recusando conexões"**.
- Ao acessar sites não bloqueados, a navegação ocorre normalmente.
- O controle de acesso por horário funcionou corretamente, liberando o acesso nos intervalos definidos.

---

## Conclusão

Este projeto demonstrou a implementação de um servidor proxy Squid para controle de acesso à internet. As regras de bloqueio e liberação foram eficazes, permitindo um gerenciamento eficiente dos sites acessados dentro da rede.

O passo a passo apresentado pode ser utilizado para configurar servidores Squid em diferentes ambientes, garantindo segurança e controle de navegação em redes empresariais.

**Autores:** Antônio Kauê e Natércia Sousa

