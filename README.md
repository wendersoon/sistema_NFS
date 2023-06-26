
![nfs2](https://github.com/wendersoon/sistema_NFS/assets/70353348/d3c6fa31-5389-4cbc-bbda-9ecb846986ad)

# Introdução

Essa wiki documenta todas as etapas de implementação de um sistema de arquivos NFS proposto pelo professor [Paulo Henrique Sousa Barbosa](https://github.com/agenteph) na disciplina de Sistemas Operacionais do curso de Ciência da Computação do Instituto Federal de Educação, Ciência e Tecnologia do Maranhão - Campus Imperatriz (IFMA). Projeto implementado pelos os discentes do 6º período [Elvis Rodrigues Almeida](https://github.com/Elvis-Almeida) e [Wenderson Oliveira Melo](https://github.com/dswendersonmelo).

### Mas o que é um Sistema de Arquivos NFS?

NFS (Network File System) é um sistema de arquivos distribuídos com um protocolo baseado em RPC, com um relacionamento cliente/servidor entre a máquina que possui um sistema de arquivo a ser distribuído e a máquina que aguarda o acesso a estes sistemas de arquivos.
 
O NFS é útil para compartilhar diretórios de arquivos entre múltiplos usuários da mesma rede. Por exemplo: um grupo de usuários trabalhando no mesmo projeto podem ter acesso aos arquivos deste projeto usando um diretório compartilhado do sistema de arquivo NFS (comumente conhecido como partilha do NFS) montado no diretório /myproject. Para accessar os arquivos compartilhados, o usuário vai ao diretório /myproject. Não há senhas ou comandos especiais para lembrar. Os usuários trabalham como se o diretório estivesse em suas máquinas locais assim tendo um trabalho mais eficiente e rápido. 


## Especificações dos computadores usados

### Elvis

Processador: Intel® Core™ i5-9300H CPU @ 2.40GHz × 8<br>
Memória ram: 8GB<br>
Placa de vídeo: GTX 1650<br>
Sistema operacioal: Linux Pop!_OS 22.04 LTS<br>
Disco: SSD NVMe m.2 512GB<br>
Placa de rede: Gigabit

### Wenderson

Processador: Intel i3-1005G1 CPU @ 1.20GHz × 4<br>
Memória ram: 4GB<br>
Sistema operacioal: Ubuntu 22.04.1 LTS<br>
Disco: SSD NVMe 256GB<br>
Placa de rede: Gigabit


# Instalação e Configuração

## No Servidor

### Instalação

1. Baixe e instale o servidor NFS pelo seguinte comando: 

`sudo apt-get install nfs-kernel-server`

![Captura de tela de 2022-11-25 18-08-26](https://user-images.githubusercontent.com/104470835/204924876-fcd680b2-59bb-4678-b4a7-d1514c1411fe.jpg)

### Configuração

2. Após a finalização da instalação, vamos criar um diretório na pasta raiz do sistema operacional. No nosso caso o diretório irá se chamar `/fast`. Podemos ver que nosso diretório foi criado com o comando ``ls:

![204157749-d4e5d326-e874-46b4-94b5-50745765ff52](https://user-images.githubusercontent.com/104470835/204926036-68d4b331-f7b1-4dd1-843d-00a055a61658.jpg)

![204157772-d8ab8599-d9c5-4da9-adbb-882e6d17456a](https://user-images.githubusercontent.com/104470835/204926156-820e60cc-fbe3-42b5-aecb-bce38ad3bfd4.jpg)

3. Nosso próximo passo é abrir o arquivo `/etc/exports` com o comando `sudo nano /etc/exports` para podermos adicionar as permissões ao nosso diretório recém criado. Nessa janela vamos adicionar as seguintes permissões de `rw` -leitura e escrita, `sync`- atualização automática, `no_subtree_check` - não vericação de subárvore e `no_root_squash` - permite que root remotos possam ser capazes de mudar quaisquer arquivos no sistema de arquivos compartilhados:

![204157979-6271a2dd-195b-4b0b-98a0-7cd1b779299f](https://user-images.githubusercontent.com/104470835/204927862-4cd883e1-ceda-450f-9368-5a584f46fc8b.jpg)

![204158296-a0d836b0-6dc8-431a-8863-83967e919575](https://user-images.githubusercontent.com/104470835/204927986-2a5d5de6-1f9c-421d-a910-b33089da8a02.jpg)



4. Agora vamos negar todas as permissões de host, para isso vamos abrir o arquivo `/etc/hosts.deny` com o seguinte comando `sudo nano /etc/hosts.deny` e iremos adicionar as seguintes linhas:

```
portmap: ALL
lockd: ALL
mountd: ALL
rquotad: ALL
```
![204159880-4e70fa7e-9d12-4886-acf9-db23ae8a3389](https://user-images.githubusercontent.com/104470835/204929015-8fb93215-17c4-4e92-b6b8-b0add8ea8429.jpg)

![204160013-87467cec-8a3b-4541-862c-f394fcf959b1](https://user-images.githubusercontent.com/104470835/204929110-4b6d3022-eb7b-4347-8a6b-5a250a09f17e.jpg)


5. Nesse passo vamos aprovar todas as permissões de host, para isso precisamos abrir o arquivo `/etc/hosts.allow` com o comando `sudo nano /etc/hosts.allow` e com isso adicionar as seguintes permissões para hosts na rede `192.168.0.0`:

```
portmap: 192.168.0.0/24
lockd: 192.168.0.0/24
rquotad 192.168.0.0/24
mountd: 192.168.0.0/24
statd: 192.168.0.0/24
```
![204160203-4616b02c-d2f8-48ac-8a3e-5f263bb21198](https://user-images.githubusercontent.com/104470835/204929675-9d1aa3b2-a875-4ed6-8ef0-cbfaa682ac29.jpg)

![204160320-9be84ca2-74fb-449c-93f5-a5754c463edd](https://user-images.githubusercontent.com/104470835/204929772-aba1b00f-0acb-41ba-a3d2-9dbe46f0b12e.jpg)


6. E antes de finalizarmos nossa configuração iremos reinicar nosso servidor, usando o seguinte comando `sudo /etc/init.d/nfs-kernel-server restart`:

![204160381-40f45ec8-1554-4acb-9c8f-8e2bb5741571](https://user-images.githubusercontent.com/104470835/204930346-0755bf95-1739-43f4-a64f-c98cef9bc20f.jpg)


Após reiniciar aparecerá deve apareceer essa mensagem:
![204160549-68beaeb9-e380-4dd6-8ccc-faf786c62903](https://user-images.githubusercontent.com/104470835/204930573-646a736f-1a70-41c8-8864-c28679b7ff88.jpg)


7. E como utilizaremos uma conexão via cabo, configurei com `ifconfig` a placa de rede com o `IP 192.168.0.1`:

![Captura de tela de 2022-11-25 18-35-53](https://user-images.githubusercontent.com/104470835/204931554-9d306ac4-3dde-447c-a543-5c892d038811.jpg)

Dessa forma, o servidor já está pronto para uso!!

## No Cliente

### Instalação

1. Baixe e instale o cliente NFS pelo seguinte comando:

`sudo apt-get install nfs-common`

![install-nfs1](https://user-images.githubusercontent.com/104470835/204919672-d7efce7b-6435-4146-a15d-036a2464ba13.png)
![install-nfs2](https://user-images.githubusercontent.com/104470835/204919702-65e49d8b-c326-403e-ad9f-c06dab8af70c.png)


### Configuração

2. Agora crie um diretório que será montado para o acesso. No meu caso chamei-o de /fast (é interessante ser o mesmo nome usado no servidor para facilitar a nomeação e lembrança dos diretórios), o diretório foi criado como mostra o comando `ls`:

![criaçãode dir](https://user-images.githubusercontent.com/104470835/204920722-fa22a1cd-a74e-417a-aa6e-56a8f568620b.jpg)

3. Abra o arquivo `/etc/fstab` e adicione o link do servidor que no caso é o `IP 192.168.0.1` como mostra a imagem abaixo:
 *  Abrir arquivo
![comando_pasta](https://user-images.githubusercontent.com/104470835/204921181-e40b8302-ba32-4d41-bba5-e84c1b818382.jpg)
 *  Adicionar link do servidor
![link-servidor](https://user-images.githubusercontent.com/104470835/204921842-6c5fe6ba-8059-4f37-9efd-6d74d59bf1bd.jpg)

Perceba que na imagem após o caminho da pasta que criei para ser montada há `nfs rw 0 0`. Os significados são os seguintes: `nfs` - Network System File, `rw` - sistema de arquivos para leitura e escrita, o primeiro `0`- Este campo define se o dump do utilitário de backup fará backup do sistema de arquivos. Se definido como o sistema de arquivos "0" ignorado, o sistema de arquivos "1" será copiado e o segundo `0` - o Fsck order informa ao fsck qual ordem verificar os sistemas de arquivos, se definido como "0" o sistema de arquivos será ignorado. Para saber mais veja [aqui](https://help.ubuntu.com/community/Fstab).

4. Antes de montar definitivamente o diretório `/fast`, configurei o IP da minha placa como `192.168.0.2` para ser da mesma classe de IP do servidor como mostra a imagem abaixo utilizando do comando `ifconfig`:

![modificando a  rede 02](https://user-images.githubusercontent.com/104470835/204922618-9f291ab3-e04e-413b-a386-88f0a6c47f7c.jpg)

5. Agora é montar o diretório com o seguinte comando:

![mount](https://user-images.githubusercontent.com/104470835/204923362-d2546e8a-8d6d-4511-ab42-e822a6fe74bb.jpg)


Pronto, finalmente temos a pasta montada e podemos iniciar os testes!!


# Testes

## Como será testado?

Testaremos nosso sistema NFS usando um cabo *cat5e*, serão transferidos arquivos de aproximadamente 15GB, 5GB, 1GB, 100MB, 50MB, 10MB e 1MB entre o cliente(Wenderson) e o servidor(Elvis). Mediremos o tempo decorrido com o seguinte comando `time rsync -avh --info=progress2 --stats pastaorigem pastadestino`.

Abaixo os prints dos testes:

* 1MB
![teste1MB](https://user-images.githubusercontent.com/104470835/204933125-93bfe026-3618-4ed2-9d33-9c639dfa1286.jpg)
* 10MB
![teste10MB](https://user-images.githubusercontent.com/104470835/204933302-2c38b289-53b5-473c-add3-c2ad0f09fc2c.jpg)
* 50MB
![teste50MB](https://user-images.githubusercontent.com/104470835/204933471-7da6d0b6-f16d-46ab-a6b7-2d325d8925fb.jpg)
* 100MB
![teste100MB](https://user-images.githubusercontent.com/104470835/204933577-c02e43b4-ad74-4843-974f-56565abb8a84.jpg)
* 1GB
![teste1GB](https://user-images.githubusercontent.com/104470835/204933685-a5bf7649-93c8-4c51-989f-718f2765dd9d.jpg)
* 5GB
![teste5GB](https://user-images.githubusercontent.com/104470835/204933851-f145fe74-023f-42ff-9018-ec3d21c8eebd.jpg)
* 15GB
![teste15gb](https://user-images.githubusercontent.com/104470835/204933997-8f175ac5-886c-4354-9713-69269c295b49.jpg)

E utilizando o comando `sudo iptraf` no servidor podemos ver a taxa de transferência em Mbps, mostrando que a transferência é em Gigabit:

![Captura de tela de 2022-11-29 14-11-36](https://user-images.githubusercontent.com/104470835/204934552-5f5cf5a8-2d28-4d33-aec8-08b355d08356.jpg)

# Conclusão
A tabela seguinte mostra os resultados, de maneira resumida, que obtivemos:

| Tamanho do Arquivo | Tempo de Transferência(em segundos) | Taxa Média de Transferência(em MB/s) |
|--- |--- |--- |
| 1MB | 00,12 | 39,93 |
| 10MB| 00,23 | 75,49 |
| 50MB| 00,81 | 89,27 |
| 100MB| 01,18 | 89,48 |
| 1GB| 13,66 | 131,56 |
| 5GB| 47,90 | 110,31 |
| 15GB| 145,72 | 110,06 |

Concluímos que todo o nosso sistema NFS obteve uma ótima performance, grande parte por causa dos dispositivos usados e o cabo cat5e.






