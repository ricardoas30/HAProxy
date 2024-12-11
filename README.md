
![Logo](https://upload.wikimedia.org/wikipedia/commons/a/ab/Haproxy-logo.png)


# Balanceamento de Carga - WEB

O HAProxy é um software de código aberto e gratuito que fornece um balanceador de carga e servidor proxy de alta disponibilidade 
para aplicativos baseados em TCP/IP e HTTP que espalha solicitações por vários servidores. O HAProxy tem uma reputação 
de ser rápido e eficiente (em termos de processador e uso de memória).



## Apêndice

Para instalar e configurar um balanceador de carga com HAProxy, siga as instruções detalhadas abaixo. Este guia abrange a instalação no sistema operacional CentOS 8, embora os passos sejam geralmente similares para outras distribuições com algumas variações nos comandos de instalação.

Pré-requisitos
Acesso root ou um usuário com privilégios de superusuário.
O HAProxy não deve ser instalado no mesmo servidor que os aplicativos a serem balanceados, para evitar conflitos de porta

## Passo 1: Preparação do Ambiente

Atualizando o sistema

```bash
  yum update -y
```

Adicionar entradas no arquivo /etc/hosts (ou conforme necessário para a sua rede):

```bash
  192.168.1.10    haproxy  
  192.168.1.11    web-node01  
  192.168.1.12    web-node02
```    

## Passo 2: Instalar o HAProxy

Instalar o pacote HAProxy

```bash
  yum install haproxy -y
```    

## Passo 3: Configuração do HAProxy

Fazer um backup do arquivo de configuração original:

```bash
  cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg-org
```

Editar o arquivo de configuração,
abra o arquivo de configuração do HAProxy:

```bash
  vi /etc/haproxy/haproxy.cfg
```

Adicione os seguintes blocos de configuração no arquivo:

```bash
  frontend web-frontend
    bind 192.168.122.123:8080
    mode http
    option http-server-close
    option forwardfor
    stats uri /haproxy?stats
    default_backend web-backend

  backend web-backend
    balance roundrobin
    server web-node01 192.168.122.209:80 check port 80
    server web-node02 192.168.122.70:80  check port 80
```    

Configuração de logs:
Para armazenar as estatísticas do HAProxy, edite o arquivo de configuração do rsyslog:

```bash
  vi /etc/rsyslog.d/haproxy.conf
```  

Adicione as seguintes linhas:

```bash
 local2.=info     /var/log/haproxy-access.log  
 local2.notice    /var/log/haproxy-info.log
```  
