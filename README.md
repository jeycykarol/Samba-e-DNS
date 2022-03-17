## INSTITUTO FEDERAL DE ALAGOAS - CAMPUS ARAPIRACA
### Curso Técnico Integrado em Informática - Turma 914 - Ano letivo de 2021
### Disciplina de Insfraestrutura e Serviços de Redes
### Professor Alaelson Jatobá
### Aluna: Jeycy Karollaynny Silva Almeida


# ROTEIRO de INSTALAÇÃO SAMBA & DNS
> Neste repositório encontra-se o roteiro de instalação, configuração e testes do serviço de compartilhamento do Linux (o SAMBA) e do sistema de nomes de domínio (DNS)

> Este documendo é dicado a todos os leigos no assunto! Espero ajudar com algumas dicas, observações e explicação!

DICA: não traduza a página para o português, algumas palavras aparecem erradas, deixe no inglês mesmo!!!


# Sumário

- [X] 1. Introdução;
- [X] 2. Nome do domínio;
- [X] 3. Tabela de nomes FQDN dos hosts;
- [X] 4. Tabela de definições de IPs;
- [X] 5. Implementação do SAMBA;
- [X] 6. Implementação do DNS;
   - [X] 6.1. Configuração da interface do host local para usar o DNS;
- [X] 7. Sessão de testes:
   - [X] 7.1. Testes dig, nslookup e ping; 
   - [X] 7.2. Gravando um arquivo no SAMBA;
- [X] 8. Referências;


# 1. Introdução:

O Samba refere-se à implementação do protocolo SMB (Server Message Block) em servidores Linux, é uma suíte que permite a integração de ambientes distintos através da comunicação entre sistemas Linux e Windows. O DNS (Domain Name System), por sua vez, é o sistema de domínio da internet, é um serviço de nomes, é ele quem vincula o endereço do IP ao nome de seu domínio, por exemplo: o IP do google é 8.8.8.8, o domínio é google.com; o DNS vincula o endereço IP ao nome de domínio para melhor compreensão dos usuários na rede.

Sabendo disso, iremos realizar no decorrer deste documento, a demonstração da instalação, configuração e testes destes serviços. 


# 2. Nome de domínio:
> Neste trabalho foi requisitado o nome de domínio com o formato "nome.labredes.ifalarapiraca.local"

 ```jeycy.labredes.ifalarapiraca.local```
 
 
# 3. Tabela de nomes FQDN dos hosts:

> FQDN (Fully Qualified Domain Name - Nome de Domínio Completamente Qualificado), podemos dizer para melhor compreensão que é o "nome completo do domínio". É um nome de domínio que especifica sua localização exata na árvore hierárquica do DNS. Especifica todos os níveis de domínio.

| Nome do serviço | Nome FQDN |
| --- | --- |
| Gateway (gw) | gw.jeycy.labredes.ifalarapiraca.local. |
| NameServer1 (ns1) | ns1.jeycy.labredes.ifalarapiraca.local. |
| Samba-SRV | samba.jeycy.labredes.ifalarapiraca.local. |


# 4. Tabela de definições de IPs:

- **Tabela com as definições da rede externa (ens160):**

| Descrição | IP|
| --- | --- |
| Rede | 10.9.14.0 |
| Máscara | 255.255.255.0 |
| Gateway (gw) | 10.9.14.1 |
| Samba-SRV | 10.9.14.114 |
| Broadcast | 10.9.14.255 |
| NameServer1 (ns1) | 10.9.14.114 |

- **Tabela com as definições da rede interna (ens192):**

| Descrição | IP |
| --- | --- |
| Rede | 192.168.0.105 |
| Máscara | 255.255.255.248 |
| Broadcast | 192.168.0.111 |

Para visualizar estes IPs basta que no seu terminal digite o comando ```ifconfig```, esse comando mostrará as informações das interfaces que foram configuradas no arquivo *.yaml*

![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img1.jpg)


# 5. Implementação do SAMBA:

- **Com o comando ```route -n``` verifique o funcionamento do gateway (isso é muitíssimo importante!)**

![routen](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img3.jpg)


- **Antes de começar a instalação de uma coisa nova é sempre bom atualizar o que já se tem, não é?
Então vamos atualizar a nossa máquina (```sudo update```) e fazer apload dos arquivos existentes (```sudo upgrade```) em suas novas versões:**

![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img4.jpg)


- **Para instalar o serviço de compartilhamento SAMBA, digite o comando:
```sudo apt install samba```**

![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img5.jpg)
![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img6.jpg)
![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img7.jpg)
![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img8.jpg)


- **Agora que você instalou veja com o comando ```whereis samba``` o caminho do serviço instalado para ter certeza da instalação.
Cuidado! Verique se é esse caminho da imagem abaixo:**

![whereis](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img9.jpg)


- **Veja se o serviço está ativado e rodando na máquina com o comando ```sudo systemctl status samba```**
> DICA: para conseguir voltar à linha de comando digite **q**;

![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img10.jpg)


- **Como sabemos o serviço de compartilhamento funciona nas portas 445 e 139 - se você não sabia, agora tá sabendo :) - sendo assim, como instalamos o samba elas devem estar funcionando! Para verificar o funcionamento das portas mencionadas digitamos o comando ```netstat -an | grep LISTEN``` na linha de comando.** 

  *Era para ter verificado antes da instalação para compararmos, mas esqueci :(*

![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img11.jpg)


- **Até agora a única coisa que fizemos foi a instalação do serviço, agora precisamos **configurá-lo**!!!!**
  **Para isso prescisamos do arquivo de configuração (que é o *smb.conf*), para fazer o backup do mesmo executamos o comando ```sudo cp/etc/samba/smb.conf{,.backup}```**
  
  Perceba o antes e o depois da execuçao do comando, note que o arquivo de backup foi criado!
  
  ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img12.jpg)
  
  Se você tiver a curiosidade de ver as configurações basta entrar na pasta */etc/samba/* e ver o arquivo *smb.conf*. Verás que existem muitas configurações padrão e que o arquivo tem MUITOS comentários, por esse motivo devemos usar o comando abaixo para deixar o arquivo somente com as configurações necessárias.
  
 - **Para retirarmos as linhas de comentário do arquivo *smb* devemos executar o comando ```sudo bash -c 'grep -v -E "^#|^;" /etc/samba/smb.conf.backup | grep . > 
/etc/samba/smb.conf'``` Ele retira todas as linhas de comentário.**

   Dentro da pasta */etc/samba* veja o arquivo com o comando ```cat smb.conf```
   
   ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img13.jpg)
   
   
- **Agora vamos editar este arquivo, para isso utilize o comando ```sudo nano /etc/samba/smb.conf``` Ele nos mostrará a página de configuração.**

  Na imagem a seguir marquei o que configuramos na parte [global], e as duas outras que foram adicionadas, a [home] e [public];
  
  > DICA: para sair do arquivo de configuração aperte **ctrl+x** e depois **y**
  
  ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img15.jpg)


- **Depois de configurado devemos reiniciar o serviço com o comando ```sudo systemctl restart smbd```. E depois veja se o serviço está funcionando com o comando ```sudo systemctl status smbd```**

> DICA: para previnir erros e situações ruins, nunca, NUNCA use TAB, principalmente em arquivos de configuração (muitas vezes não sabemos onde podemos ou não usar esse "meio rápido" de dar espaço). Outra coisa, é sempre bom despois de alguma mudança no serviço, utilizarmos os dois comandos anteriores!!!!

  ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img16.jpg)


- **Além disso, devemos verificar o funcionamento das interfaces que colocamos no arquivo de configuração (a *loopback, ens160 e ens192*), para isso usamos o comando ```netstat -an | grep LISTEN```**

  *Lembra que fizemos isso antes? Agora você pode comparar e ver as mudanças :)*
  
  ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img17.jpg)
  

> **IMPORTATÍSSIMO:** agora vocês verão uma configuração que vocês devem fazer na raiz da VM (***cd /***), eu acabei fazendo na área do *root* (***~$*** - como vocês verão nas imagens) e deu "tudo certo", todavia na "hora importante" do teste não deu. Para resolver eu só tive que mover a pasta para a raiz e deu tudo certo :) Sendo assim, siga o passo a passo a seguir normalmente, só lembre de fazê-lo na RAIZ da máquina (***cd /***)!!!!!!

- **No arquivo de configuração colocamos que a área [public] estava na pasta */samba/public*, se verificarmos (*ls -la*) ainda não possuímos esta pasta, portanto deveremos criá-la (na RAIZ da VM). Utilize o comando ```sudo mkdir samba```, depois verifique se a pasta foi criada usando o comando ```ls -la```:**

  > Deve aparecer uma saída como na imagem abaixo, que mostra a pasta na RAIZ da VM:

  ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img28.jpg)

- **Depois entre na pasta */samba* (```cd /samba```) para criar a pasta *public*.**
  
  Entrou na pasta? Agora use o comando ```sudo mkdir public```! Depois veja se a pasta foi criada (```ls -la```)
  
  ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img19.jpg)
  
 - **Agora vamos realizar as configurações de acesso, leitura e escrita da pasta *public***
   > Use os comandos na RAIZ da VM (***cd /***)!!!!!
   
   - **Para permitir o acesso para que qualquer usuário possa acessar a pasta *public* usamos o comando ```sudo chown -R nobody:nogroup /samba/public```**
   
   Verifique a imagem anterior e faça a comparação dela com a imagem a seguir, para que você entenda o que mudou!!!
   
   ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img20.jpg)
   
   - **As pasta está sem permissão de leitura e escrita, devemos mudar isso com o comando ```sudo chmod -R 0775 /samba/public```, para dar total acesso de leitura e escrita.**
   
   Verifique a imagem anterior e faça a comparação dela com a imagem a seguir, para que você entenda o que mudou!!!
   
   ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img21.jpg)
   
   - **Agora devemos afirmar que somente os usuários que pertencem ao grupo do samba é que podem ter acesso a pasta *public* (denominamos o grupo como *sambashare*).**
   
   Para realizar essa permissão e criar o grupo utilize o comando ```sudo chgrp sambashare /samba/public```
   
   *Ah, gente! Isso é que faz a "mágica" acontecer, para saber mais continue... :)*
   
   ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img22.jpg)
   
   Para verficar os grupos que nossa VM possui é só usar o comando ```getent group```, com esse comando você verifica os grupos e os usuários deles, use esse comando agora e você verá que não existe nenhum usuário no grupo *sambshare*.
   
- **Lembra do arquivo de configuração do samba (o *smb.conf*)? Lembra da área [public]? Agora deveremos modificá-la, para tornar válidos somente os usuários pertencentes ao grupo *sambashare*. Para isso entre no arquivo de configuração usando o comando ```sudo nano /etc/samba/smb.conf```.**
   
   Lembre que para sair do arquivo de configuração basta apertar **ctrl+x** e **y** (para salvar as mudanças realizadas)
   
   Faça as modificações de acordo com a imagem abaixo:
   
   ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img23.jpg)
   
   Depois das configurações lembre-se de reinciar o serviço (```sudo systemctl restart smbd```) e verficar seu funcionamento (```sudo systemctl status smbd```)!

- Pronto! Agora os usuários válidos são apenas os do grupo *sambashare*. Contudo, lembremos que ainda não há usuários neste grupo (```getent group```) - use o comando para verificar. 

  **Sendo assim, devemos criar um usuário, para isso use o comando ```sudo adduser aluno``` (aluno é o nome do usuário, pode colocar qualquer nome).**

  > DICA: para deixar os campos em branco aperte **enter**. E anote em um papel as informações de login e senha, para que você não esqueça!!!!!

- **Depois de criar o usuário do S.O (Sistema Operacional), agora deveremos vinculá-lo ao samba, para isso use o comando ```sudo smbpasswd -a aluno```, esse comando irá pedir uma senha, essa deverá ser a senha de acesso para a pasta public, então anote!!!!** A minha senha foi igual ao nome do meu usuário (aluno2).

Obs.: essa senha pode ser diferente da senha de usuário, mas recomendo que seja a mesma! 
   
- **Com o comando anterior apenas vinculamos o usuário ao serviço samba, agora deveremos adicionar este usuário ao grupo *sambashare*, para isso use o comando ```sudo usermod -aG sambashare aluno```**
   
   > PARA SABER MAIS: O usuário que foi criado é um usuário comum como qualquer outro, para que ele deixe de ser "normal" e tenha acesso e vínculo ao samba é que devemos executar os dois comandos anteriores.

  ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img25.jpg)
  
  Depois de adicionar o usuário ao grupo deveremos verificar se isso realmente aconteceu, para isso use o comando ```getent group``` - já vimos esse comando, compare para ver a mudança :)
  
  ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img26.jpg)
  
  PRONTO!!!!
  
  Serviço de Compartilhamento SAMBA instalado e configurado com SUCESSO!!!!! :)
  
  *Para ver o funcionamento do serviço navegue até a sessão 8.2. deste documento!*

# 6. Implementação do DNS:

Ante de começar eu mudei o nome da minha máquina para ***ns1*** (nameServer1) com o comando ```sudo hostnamectl set-hostname ns1```

![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img1.jpg)

- **Para implementarmos o DNS (Domain Name System), que é o sistema de domínio da internet, precisamos, primeiramente, fazer a atualização dos pacotes que já existem na máquina, para isso use o comando ```sudo apt update```.**

   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img2.jpg)
   
- **Depois disso podemos instalar o BIND9 (que é o software de servidor de nomes), use o comando a seguir para instalá-lo: ```sudo apt-get install bind9 dnsutils bind9-doc```**

   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img3.jpg)

- **Verifique o funcionamento do serviço com o comando ```sudo systemctl status bind9```**

   > Para voltar à linha de comando use a letra ***q***

   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img4.jpg)

   Fizemos apenas a instalação, agora precisamos configurá-lo para que ele funcione de fato! Então vamos lá:

- **Vamos entrar no diretório *bind* para isso use o comando ```ls -la /etc/bind```**

   O que aparece na tela são os arquivos do bind. Observe esses aquivos ***db***, as zonas são especificadas nesses arquivos. 

   > As zonas especificam o domínio e podem ser zonas de conversão direta (de nome para IP) ou zonas de conversão reversa (de IP para nome).

   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img5.jpg)

- **Devemos criar uma pasta *zones* dentro do diretório *bind*. Para isso utilize o comando ```sudo mkdir /etc/bind/zones```. Depois de executar o comando verifique se a pasta criada já está no diretório *bind* (use o comando ```ls -la /etc/bind``` para isso)**

   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img6.jpg)
   
   - Agora vamos copiar o db vazio com o nome da zona que queremos criar!

   > EXPLICANDO: Arquivos db são bancos de dados de resolução de nomes, por exemplo, sua VM conhece o nome de uma máquina, mas não sabe o IP, o arquivo db resolverá isso! Cada zona deve ter seu próprio arquivo db.

   Para fazer a cópia do db vazio execute o comando ```sudo cp /etc/bind/db.empty /etc/bind/zones/db.jeycy.labredes.ifalarapiraca.local```

   Observe que utilizei depois do *db.* o nome de domínio que especifiquei logo no começo do documento. Por isso é tão importante as definições da tabela no início, para não nos perdermos durante o processo!!!!

   > PARA SABER MAIS: O *.local*** significa que o DNS irá resolver apenas endereços internos, da rede local!

   Depois de criado o arquivo entre na pasta zones para verificar a criação do arquivo. Para entrar na pasta use ```cd /etc/bind/zones```, e para ver os arquivos da pasta use ```ls -la```
   
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img7.jpg)
   
   - Agora vamos configurar esse arquivo, para isso use o comando ```sudo nano db.jeycy.labredes.ifalarapiraca.local```

   *O arquivo já possui a estrutura que precisamos, ele é um diretório do tipo SOA, é um serviço autoridade.*

   O último ponto (.root.localhost **.** ) deve permanecer sempre, pois é o ponto do root da nossa VM
   
   Depois disso utilzei o comando ```cat db.jeycy.labredes.ifalarapiraca.local``` dentro da pasta de zonas para verificar a configuração que realizei no arquivo db.
   
   > OBSERVE: nossos nomes FQDN especificados nas tabelas foram criados aqui!!!!
   
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img8.jpg)
   
   ATENÇÃO: as configurações acima são para zonas diretas (conversão de nome de domínio para IP)
   
   - Apenas editamos as configurações, mas ainda não salvamos, para isso devemos "falar" para o arquivo *named.conf.local* (que é o arquivo de configuração do bind) que existe esse arquivo db. Use o comando ```sudo nano /etc/bind/named.conf.local``` Verifique as mudanças que realizei no arquivo:
   
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img9.jpg)
   
   Agora devemos verificar a sintaxe do arquivoe da zona: 

   - Use o comando ```sudo named-checkconf``` para verficar se a sintaxe do arquivo de configuração do bind está correta (se não retonar nada é porque não há erros). 

    ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img10.jpg)
   
   - Precisamos verificar também a sintaxe da zona, para isso você deve estar na pasta *zones* (```sudo /etc/bind/zones```), para verificar a sintaxe use o comando ```sudo named-checkzone jeycy.labredes.ifalarapiraca.local db.jeycy.labredes.ifalarapiraca.local```. Se não houver erros a saída é um "OK"

  - Apenas configuramos os arquivos, mas ainda não salvamos, para fazer isso reinicie o serviço com o comando ```sudo systemctl restart bind9```e depois veja se está funcionando corretamente como o comando  ```sudo systemctl status bind9```
   
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img11.jpg)

   Percebeu que existe várias saídas *network uncreachable resolving* após utilizar o último comando? É um aviso que informa que não foi possível resolver endereços IPv6. Precisamos usar o comando ```sudo nano /etc/default/named```, para resolver apenas IPv4, pois a nossa zona deve aparcer no log da saída do comando *sudo systemctl status bind9*. O comando ```sudo nano /etc/default/named``` nos levará a um arquivo de configuração, para resolver o problema basta colocar *-4* entre as aspas, para resolver apenas IPv4.
   
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img12.jpg)
   
   Agora reinicie o bind e veja o status dele:
   
   Observe na imagem a seguir que o nome da nossa zona aparece no log da saída do comando!!!!!
   
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img13.jpg)
   
   - Até agora configuramos apenas a zona direta, nosso DNS Server já resolve nomes, agora precisamos configurar a zona reversa!

   - Copie o arquivo de zona reversa usando o coomando ```sudo cp /etc/bind/db.127 /etc/bind/zones/db.10.9.14.rev``` 
   
     > Observe que o prefixo de rede (10.9.14.) já está sendo implementado na zona, no arquivo de configuração você perceberá que não precisaremos usar o IP completo da rede apenas os últimos numeros;
     
     ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img14.jpg)
     
    - Agora iremos configurar esse arquivo db da zona reversa, para isso use o comando ```sudo nano db 10.9.14.rev```. 

     Veja as minhas configurações na imagem a seguir, e se possível compare com as configurações da zona direta para verificar a diferença!
     
     ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img15.jpg)
     
    - Apenas editamos as configurações, mas ainda não salvamos, para isso devemos "falar" para o arquivo *named.conf.local* que existe esse arquivo db. Use o comando ```sudo nano /etc/bind/named.conf.local```

      [](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img16.jpg)
   
      Depois use o comando ```sudo named-checkconf``` para verficar se a sintaxe do arquivo de configuração do bind está correta (se não retonar nada é porque não há erros). 

      Apenas configuramos os arquivos, mas ainda não salvamos, para fazer isso reinicie o serviço com o comando ```sudo systemctl restart bind9```e depois veja se está funcionando corretamente como o comando  ```sudo systemctl status bind9```
      
      Depois disse usei o comando ```cat named.conf.local``` para verificar os arquivos db (direto e reverso).
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img17.jpg)    
      
      PRONTO!!!
      
      DNS Server configurado!!!!
      
      Para ver o teste navegue até a sessão 7.1. deste documento.
      
# 6.1. Configuração da interface do host local para usar o DNS:

Para configurara a interface do host local para que o servidor DNS consultado seja o que cofiguramos devemos entrar na pasta */etc/netplan* com o comando ```cd /etc/netplan```, lá existe o arquivo de configuração das interfaces, o *yaml*, iremos modificá-lo.

Para modificar o arquivo execute o comando ´´´sudo nano /etc/netpan/00-installer-config-yaml´´´. *Lembre-se que não pode usar TAB*

Para que possamos usar o BIND como nosso servidor DNS, deveremos alterar o endereço nameserver da interface ens160. CUIDADO! É o endereço nameserver, não o endereço da interface!!!! 

Altere o *addresses* colocando o IP do seu servidor DNS e na área search coloque nos cochetes [] o nome de domínio que você configurou! 

Para sair do arquico aperte *ctrl+x* e *y*.

Mas para salvar o arquivo de verdade você precisa executar o comando ```sudo netplan apply```. Verifique o antes e o depois do arquivo *yaml* na imagem

Pronto agora sua máquina tem o servidor DNS próprio!!! :)

![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/Img18.jpg)

Agora utilizemos o comando ```systemd-resolve --status ens160``` para que visualizemos a resolução da interface, perceba que com esse comando podemos visualizar o  IP do DNS Server utilizado pela máquina e seu nome de domínio. No nosso caso deverá aparecer 10.9.14.114 e jeycy.labredes.ifalarapiraca.local

![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img20.jpg)

Antes de configurar a interface ens160 para utilizar o DNS Server, executei esse comando para observarmos o antes e o depois, observe como estava antes da configuração do arquivo *yaml*:

![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/dns/img19.jpg)

# 7. Sessão de testes:

## 7.1. Testes dig, nslookup e ping:

Para realizar os testes referentes ao DNS é bem simples. Faremos os testes por vez, utilizando cada um dos comandos! Usaremos os comandos em cada um dos IP e hosts da tabela FQDN especificada para este trabalho.

- **7.1.1. DIG**
   
   > OBS: para fazer a pesquisa através de IP via dig, utilize **-x** antes do endereço IP!!!!!

   - ***Para zona reversa:***
  
      - Usando o IP 8.8.8.8 (do google): 
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_reversa/dig1_rev.jpg)
      
      - Usando o IP 10.9.14.1 (de gateway):
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_reversa/dig2_rev.jpg)
      
      - Usando o IP 10.9.14.114 (da ns1 e do samba):
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_reversa/dig3_rev.jpg)
      
      
   - ***Para zona direta:***
   
      - Para o Google:
  
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_direta/dig1_dirt.jpg)
      
      - Usando o gateway:
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_direta/dig2_dirt.jpg)
      
      - Usando o nameServer1:
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_direta/dig3_dirt.jpg)
      
      - Usando o samba:
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_direta/dig4_dirt.jpg)
      
      
- **7.1.2. NSLOOKUP**

   - ***Para zona reversa:***
  
      - Usando o IP 8.8.8.8 (do google): 
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_reversa/nsk1_rev.jpg)
      
      - Usando o IP 10.9.14.1 (de gateway):
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_reversa/nsk2_rev.jpg)
      
      - Usando o IP 10.9.14.114 (da ns1 e do samba):
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_reversa/nsk3_rev.jpg)
      
      
   - ***Para zona direta:***
      
      - Usando o gateway:
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_direta/nsk1_dirt.jpg)
      
      - Usando o nameServer1:
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_direta/nsk2_dirt.jpg)
      
      - Usando o samba:
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_direta/nsk3_dirt.jpg)
      
- **7.1.3. PING**

   - ***Para zona reversa:***
  
      - Usando o IP 8.8.8.8 (do google): 
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_reversa/ping1_rev.jpg)
      
      - Usando o IP 10.9.14.1 (de gateway):
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_reversa/ping2_rev.jpg)
      
      - Usando o IP 10.9.14.114 (da ns1 e do samba):
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_reversa/ping3_rev.jpg)
      
      
   - ***Para zona direta:***
   
      - Para o Google:
  
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_direta/ping1_dirt.jpg)
      
      - Usando o gateway:
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_direta/ping2_dirt.jpg)
      
      - Usando o nameServer1:
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_direta/ping3_dirt.jpg)
      
      - Usando o samba:
      
      ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/testes_dns/z_direta/ping4_dirt.jpg)

GRAVAÇÃO do [TESTE AO VIVO](https://drive.google.com/file/d/1nSEn1r42uo2jaCnOQ8PJrXCDuQBkMzAk/view?usp=drivesdk)!!!!!

## 7.2. Gravando um arquivo no SAMBA:

O nosso serviço de compartilhamneto (SAMBA), já foi instalado e configurado, agora deveremos testá-lo. Para isso utilizei os aplicativos [OpenVPN](https://openvpn.net/client-connect-vpn-for-windows/) e o [PuTTy](https://www.putty.org/).

**DESCRIÇÃO:** Esse teste será realizado em uma máquina Windows. Iremos salvar uma pasta nos samba através do host local com acesso via VPN ao laboratório da disciplina (labredes). Veja o passo a passo do teste:

1. **Ligue sua VPN e conecte-se a sua VM via terminal ssh (usei o PuTTy)**
2. **Vá até o Windows Explorer e na barra de pesquisa digite o IP da sua VM, nesse formato: *\\\10.9.14.114***
   
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/testando_o_samba/testando2.jpg)
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/testando_o_samba/testando3.jpg)
   
   Verifique se você consegue ver as pastas. Se sim, você conseguiu se comunicar com sua VM, porém, ainda NÃO testou o SAMBA - mas já é uma vitoriazinha :)
   
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/testando_o_samba/testando4.jpg)
   
3. **Agora iremos testar o SAMBA!!! Para isso clique na pasta *public***
   
   - Quando você clicar deverá aparecer uma tela de login como na imagem a seguir:
   
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/testando_o_samba/testando5.jpg)
   
   - Agora é o grande momento do teste!!! Lembra que eu pedi para você anotar as informações de login? Vamos utilizá-las agora! Use o nome do usuário criado e a senha que você criou ao utilizar o comando ```sudo smbpasswd -a aluno```
   
   Se a sua configuração do samba estiver correta você conseguirá acessar a pasta, mas se não conseguir acessar é porque fez alguma coisa errada na configuração, pode ser um errinho muito besta, mas faz toda diferença, se isso acontecer tenha calma e verifique o roteiro novamente!
   
   Se você conseguir acessar, você poderá criar arquivos e diretórios na pasta *public*. 
   
   *OBS: a minha (public) já tinha uma pasta (como você pode ver na imagem a seguir), porque o professor criou na hora da aula. SIM, ele testou ao vivo, já merecia um 10, não? :) Mas irei criar uma pasta também para que vocês vejam a mudança :)*
   
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/testando_o_samba/testando6.jpg)
   
   - Para criar a pasta é muito simples, é como criar qualquer outra pasta normalmente!
   
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/testando_o_samba/testando7.jpg)
   
5. **Verificando pasta criada no terminal**

   Criamos a pasta! Quando a criamos a mudança aconteceu automaticamente no nosso diretório */samba/public*, para verificar a criação da pasta vi aterminal ssh, entre na RAIZ da VM (**cd /**) e utilize o comando ```cd /samba/public``` para entrar na pasta *public*, depois use o comando ```ls -la``` para visualizar o conteúdo da pasta.
   
   Observe a imagem, tanto a pasta criada por mim, quanto a criada pelo professor, foram criadas e podem ser visualizadas via terminal!
   
   ![](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img29.jpg)
   
VIVA!!! O SAMBA funcionou!!!! 

TESTE REALIZADO COM SUCESSO!!!

Para verificar o teste feito ao vivo, [clique aqui](https://drive.google.com/file/d/1kxrd80ZgLLE4xC9BoC6RIpldTPrElry5/view?usp=drivesdk) (é um vídeo de 2 min)!

# 8. Referências:

- **APLICATIVOS:**

   Para fazer o trabalho precisei de dois aplicativos ***no computador***:
   
   - **OpenVPN Connect:** OpenVPN é um software para criar redes privadas através de túneis criptogrfados entre computadores. [Clique aqui para ser levado ao site para baixar o app](https://openvpn.net/client-connect-vpn-for-windows/)

   - **PuTTy:** O PuTTY é um software de emulação de terminal, destinado a suportar o acesso remoto de servidores via shell! (Ele é feinho, mas é muito simples de mexer!). [Clique aqui para ser levado ao site para baixar o app](https://www.putty.org/)

   ***No celular*** também utilizei a OpenVPN Connect, mas como terminal ssh utilizei o TERMUX. Para baixá-los perquise no Play Store!

- **MATERIAL de APOIO:**

   - Aulas dirigas pelo Prof. Alaelson Jatobá;

     Aula 18 (04/10/2021) - Aula de instalação , configuração e testes do SAMBA;
     
     Aula 21 (25/10/2021) e aula 22 (01/11/2021) - Aulas de instalação, configuração e testes do DNS;
     
     Obs: não redirecionei ao link do drive, porque não sei se tenho permissão para isso! As aulas mencionadas ficam como referência para utilização do prof. 
  
   - [Roteiros do GitHub do Prof. Alaelson Jatobá](https://github.com/alaelson/labredes2021)

- **DOCUMENTO do GITHUB**

Hahaha...não poderia deixar de indicar o site em que aprendi as configurações de markdown, para fazer este documento.

[Como fazer um readme.md bonitão!](https://raullesteves.medium.com/github-como-fazer-um-readme-md-bonit%C3%A3o-c85c8f154f8)
