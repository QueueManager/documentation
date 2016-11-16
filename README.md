# API de Comunicação

Aqui esta descrito como será a comunicação entre todos os módulos do nos sistema.

Para tal iremos usar um **ESP8266** e um **PIC** em cada módulo. Cada ESP irá iniciar um servidor na porta _1000_.

## Atendente - Cliente

Ao receber o pedido por um botão o **módulo atendente** irá enviar _1-byte_ de mensagem para o **módulo cliente**: `<guiche>`. (**guiche** - Número do quichê esta ocioso e requisitou uma nova senha, ele dever ser apenas _1-byte_).

Por sua vez ao receber o byte da mensagem com o número do guichê  o **módulo cliente** irá enviar como retorno _3-bytes_ no seguinte formato: `<guiche><ticket>`.

> Exemplo de mensagem válida: `<0x03>`

Cada campo esta descrito abaixo:

* **guiche** - Número do guichê que esta ocioso, ele deve ser apenas _1-byte_. Inicialmente, temos dois guichês:
  * Caixa normal: `<0x01>`;
  * Gerente: `<0x02>`;
* **ticket** - Número que do próximo cliente, deve ser apenas _2-bytes_:
  * Comum: 0-999;
  * Preferencial: 1000-1999;

>  Exempo de mensagem válida: `<0x03><0x0042>`

## Atendente - Painel

Após receber a mensagem de retorno do  módulo cliente, o módulo atendente irá enviar a mensagem recebida diretamentet para o módulo painel.

# Sequência de comandos usada pelo módulo atendente

**Se conectar a rede:**

```
AT+CWMODE=1
AT+CWJAP_CUR="dlink",""
```

**Aguardar respostas:**

```
AT+CIPMUX=1 
AT+CIPSERVER=1,1000
```

**Se connectar com o cliente:**

```
AT+CIPSTART=”TCP”,”192.168.0.100”,1000
AT+CIPSEND=1
<guiche>
AT+CIPCLOSE
```

**Se connectar com o painel:**

```
AT+CIPSTART=”TCP”,”192.168.0.102”,1000
AT+CIPSEND=3
<guiche><ticket>
AT+CIPCLOSE
```
