## INSTITUTO FEDERAL DE ALAGOAS - CAMPUS ARAPIRACA
### Curso Técnico Integrado em Informática - Turma 914 - Ano letivo de 2021
### Disciplina de Insfraestrutura e Serviços de Redes
### Professor Alaelson Jatobá
### Aluna: Jeycy Karollaynny Silva Almeida


# ROTEIRO de INSTALAÇÂO SAMBA & DNS
> Neste repositório encontra-se o roteiro de instalação, configuração e testes do serviço de compartilhamento do Linux (o SAMBA) e do sistema de nomes de domínio (DNS)


# Sumário

- [X] Introdução;
- [X] Nome do domínio;
- [X] Tabela de nomes FQDN dos hosts;
- [X] Tabela de definições de IPs;
- [X] Implementação do SAMBA;
- [ ] Implementação do DNS;
- [ ] Configuração das interfaces de rede;
- [ ] Sessão de testes:
   - [ ] Testes dig, nslookup e ping; 
   - [X] Gravando um arquivo no SAMBA;
   - [ ] Configuração da interface do host local para usar o DNS;
- [ ] Referências;


# Introdução:

O Samba refere-se à implementação do protocolo SMB (Server Message Block) em servidores Linux, é uma suíte que permite a integração de ambientes distintos através da comunicação entre sistemas Linux e Windows. O DNS (Domain Name System), por sua vez, é o sistema de domínio da internet, é um serviço de nomes, é ele quem vincula o endereço do IP ao nome de seu domínio, por exemplo: o IP do google é 8.8.8.8, o domínio é google.com; o DNS vincula o endereço IP ao nome de domínio para melhor compreensão dos usuários na rede.

Sabendo disso, iremos realizar no decorrer deste documento, a demonstração da instalação, configuração e testes destes serviços. 


# Nome de domínio:
> Neste trabalho foi requisitado o nome de domínio com o formato "nome.labredes.ifalarapiraca.local"

 ```jeycy.labredes.ifalarapiraca.local```
 
 
# Tabela de nomes FQDN dos hosts:

> FQDN (Fully Qualified Domain Name - Nome de Domínio Completamente Qualificado), podemos dizer para melhor compreensão que é o "nome completo do domínio". É um nome de domínio que especifica sua localização exata na árvore hierárquica do DNS. Especifica todos os níveis de domínio.

| Nome do serviço | Nome FQDN |
| --- | --- |
| Gateway (gw) | gw.jeycy.labredes.ifalarapiraca.local. |
| NameServer1 (ns1) | ns1.jeycy.labredes.ifalarapiraca.local. |
| Samba-SRV | samba.jeycy.labredes.ifalarapiraca.local. |


# Tabela de definições de IPs:

- Tabela com as definições da rede externa (ens160):

| Descrição | IP|
| --- | --- |
| Rede | 10.9.14.0 |
| Máscara | 255.255.255.0 |
| Gateway (gw) | 10.9.14.1 |
| Samba-SRV | 10.9.14.114 |
| Broadcast | 10.9.14.255 |
| NameServer1 (ns1) | 10.9.14.114 |

- Tabela com as definições da rede interna (ens192):

| Descrição | IP |
| --- | --- |
| Rede | 192.168.0.105 |
| Máscara | 255.255.255.248 |
| Broadcast | 192.168.0.111 |

Para visualizar estes IPs basta que no seu terminal digite o comando ```ifconfig```, esse comando mostrará as informações das interfaces configuradas do arquivo *.yaml*

