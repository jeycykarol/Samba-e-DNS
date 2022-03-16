## INSTITUTO FEDERAL DE ALAGOAS - CAMPUS ARAPIRACA
### Curso Técnico Integrado em Informática - Turma 914 - Ano letivo de 2021
### Disciplina de Insfraestrutura e Serviços de Redes
### Professor Alaelson Jatobá
### Aluna: Jeycy Karollaynny Silva Almeida


# ROTEIRO de INSTALAÇÂO SAMBA & DNS
> Neste repositório encontra-se o roteiro de instalação, configuração e testes do serviço de compartilhamento do Linux (o SAMBA) e do sistema de nomes de domínio (DNS)


# Sumário

- [X] 1. Introdução;
- [X] 2. Nome do domínio;
- [X] 3. Tabela de nomes FQDN dos hosts;
- [X] 4. Tabela de definições de IPs;
- [X] 5. Implementação do SAMBA;
- [ ] 6. Implementação do DNS;
- [ ] 7. Configuração das interfaces de rede;
- [ ] 8. Sessão de testes:
   - [ ] 8.1. Testes dig, nslookup e ping; 
   - [X] 8.2. Gravando um arquivo no SAMBA;
   - [ ] 8.3. Configuração da interface do host local para usar o DNS;
- [ ] 9. Referências;


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
  
  Perceba o antes e o depois da execuçao do comando, note que o arquivo de babckup foi criado!
  
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

  > Deve aparecer um saída como na imagem abaixo, que mostra a pasta na RAIZ da VM:

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
   
   Para verficar os grupos que nossa VM possui é só usar o comando ```getent group```, com esse comando você verifica os grupos e os usuários deles, use esse comando agoa e você verá que não existe nenhum usuário no grupo *sambshare*.
   
- **Lembra do arquivo de configuração do samba (o *smb.conf*)? Lembra da área [public]? Agora deveremos modificá-la, para tornar válidos somente os usuários pertencentes ao grupo *sambashare*. Para isso entre no arquivo de configuração usando o comando ```sudo nano /etc/samba/smb.conf```.**
   
   Lembre que para sair do arquivo de configuração basta apertar **ctrl+x** e **y** (para salvar as mudanças realizadas)
   
   Faça as modificações de acordo com a imagem abaixo:
   
   ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img23.jpg)
   
   Depois das configurações lembre-se de reiniar o serviço (```sudo systemctl restart smbd```) e verficar seu funcionamento (```sudo systemctl status smbd```)!

- Pronto! Agora os usuários válidos são apenas os do grupo *sambashare*. Contudo, lembremos que ainda não há usuários neste grupo (```getent group```) - use o comando para verificar. 

  **Sendo assim, devemos criar um usuário, para isso use o comando ```sudo adduser aluno``` (aluno é o nome do usuário, pode colocar qualquer nome).**

  > DICA: para deixar os campos em branco aperte **enter**. E anote em um papel as informações de login e senha, para que você não esqueça!!!!!

- **Depois de criar o usuário do S.O (Sistema Operacional), agora deveremos vinculá-lo ao samba, para isso use o comando ```sudo smbpasswd -a aluno```, esse comando irá pedir uma senha, essa deverá ser a senha de acesso para a pasta public, então anote!!!!**
   
- **Com o comando anterior apenas vinculamos o usuário ao serviço samba, agora deveremos adicionar este usuário ao grupo *sambashare*, para isso use o comando ```sudo usermod -aG sambashare aluno```**
   
   > PARA SABER MAIS: O usuário que foi criado é um usuário comum como qualquer outro, para que ele deixe de ser "normal" e tenha acesso e vínculo ao samba é que devemos executar os dois comandos anteriores.

  ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img25.jpg)
  
  Depois de adicionar o usuário ao grupo deveremos verificar se isso realmente aconteceu, para isso use o comando ```getent group``` - já vimos esse comando, compare para ver a mudança :)
  
  ![ifconfig](https://github.com/jeycykarol/Samba-e-DNS/blob/main/samba/samba/img26.jpg)
  
  PRONTO!!!!
  
  Serviço de Compartilhamento SAMBA instalado e configurado com SUCESSO!!!!! :)
  
  *Para ver o funcionamento do serviço navegue até a sessão 8.2. deste documento!*

# 6. Implementação do DNS:

# 7. Configuração das interfaces de rede:

# 8. Sessão de testes:

## 8.1. Testes dig, nslookup e ping:

## 8.2. Gravando um arquivo no SAMBA:

## 8.3. Configuração da interface do host local para usar o DNS:

# 9. Referências:
