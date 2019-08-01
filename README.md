# Iptables

iptables é o nome da ferramenta da interface do usuário que permite a criação de regras de firewall e NATs.  
Apesar de, tecnicamente, o iptables ser apenas uma ferramenta que controla o módulo netfilter, o nome "iptables" é frequentemente utilizado como referência ao conjunto completo de funcionalidades do netfilter.  
O iptables é parte de todas as distribuições modernas do Linux.

## Compartilhamento Básico

- Habilita o compartilhamento das placas de rede

`echo "1" > /proc/sys/net/ipv4/ip_forward`

- Chama os módulos do kernel

```
modprobe ip_tables
modprobe iptable_nat
```

- Limpando regras pré-existentes

```
iptables -F INPUT
iptables -F OUTPUT
iptables -F -t nat
iptables -F -t mangle
```

- Habilita o encaminhamento de pacotes

```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

## Redirecionamento de Portas

- Redirecionamento das portas 80 e 443

```
iptables -t nat -A PREROUTING -i eth1 -p tcp -m --dport 80 -j REDIRECT --to-ports 8080
ou iptables -t nat -A PREROUTING -i eth1 -p tcp --dport 80 -j REDIRECT --to-ports 8080
iptables -t nat -A PREROUTING -i eth1 -p tcp -m --dport 443 -j REDIRECT --to-ports 8080
ou iptables -t nat -A PREROUTING -i eth1 -p tcp --dport 443 -j REDIRECT --to-ports 8080
```

**Obs**: O redirecionamento da porta 443 pode não deixar sites ssl funcionarem corretamente.

## Bloqueio de Portas

 ```
iptables -A INPUT -p tcp --destination-port 80 -j DROP
iptables -A FORWARD -p tcp --destination-port 80 -j DROP
iptables -A INPUT -p tcp --destination-port 443 -j DROP
iptables -A FORWARD -p tcp --destination-port 443 -j DROP
```

## Gerar um log para a conexão em uma porta
`iptables -t nat -A PREROUTING -i eth0 -p tcp -m limit --limit 1/min --limit-burst 2 -m tcp --dport 443 -j LOG --log-prefix "log_https "`

ou

`iptables -A INPUT -i eth0 -p tcp -m limit --limit 1/min --limit-burst 2 -m tcp --dport 443 -j LOG --log-prefix "log_https "`

Obs: Neste caso é para porta 443 (https)