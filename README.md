
![Logo](https://github.com/ricardoas30/HAProxy/blob/main/logo-haproxy.png)



# Balanceamento de Carga - WEB

O HAProxy é um software de código aberto e gratuito que fornece um balanceador de carga e servidor proxy de alta disponibilidade 
para aplicativos baseados em TCP/IP e HTTP que espalha solicitações por vários servidores. O HAProxy tem uma reputação 
de ser rápido e eficiente (em termos de processador e uso de memória).



## Apêndice

Para instalar e configurar um balanceador de carga com HAProxy, siga as instruções detalhadas abaixo. Este guia abrange a instalação no sistema operacional Fedora 41, embora os passos sejam geralmente similares para outras distribuições com algumas variações nos comandos de instalação.

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

1.Fazer um backup do arquivo de configuração original:

```bash
  cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg-org
```

2.Editar o arquivo de configuração,
abra o arquivo de configuração do HAProxy:

```bash
  vi /etc/haproxy/haproxy.cfg
```

3.Adicione os seguintes blocos de configuração no arquivo:

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

4.Configuração de logs:
Para armazenar as estatísticas do HAProxy, edite o arquivo de configuração do rsyslog:

```bash
  vi /etc/rsyslog.d/haproxy.conf
```  

5.Adicione as seguintes linhas:

```bash
 local2.=info     /var/log/haproxy-access.log  
 local2.notice    /var/log/haproxy-info.log
```  

## Passo 4: Configuração do Firewall

Permita a porta 80 (HTTP):

```bash
  firewall-cmd --permanent --add-port=8080/tcp  
  firewall-cmd --reload
```  

## Passo 5: Iniciar HAProxy

1.Reiniciar o serviço HAProxy:

```bash
 systemctl restart haproxy
```  

2.Habilitar o HAProxy para iniciar automaticamente:

```bash
 systemctl enable haproxy
```  

## Passo 6: Verificação

Para testar se o HAProxy está funcionando corretamente, você pode usar o comando curl para fazer várias requisições e verificar se o tráfego é distribuído entre os servidores:

```bash
  curl 192.168.122.123:8080
```

Além disso, você pode verificar as estatísticas acessando:

```bash
  http://192.168.122.123:8080/haproxy?stats
```


## Demonstração

Acesse o servidor HAProxy que ele irá balancear e distribuir para os servidores Web.

```bash
  http://192.168.122.123:8080/
```  

## Considerações Finais

Utilizando o HAProxy, você obtém um balanceador de carga de alta disponibilidade, conhecido por sua eficácia na distribuição de tráfego, suporte a várias plataformas e flexibilidade de configuração.
## Licença

[haproxy - Documentação](https://www.haproxy.org/)

