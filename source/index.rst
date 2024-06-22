.. rtd_devops_henrique documentation master file, created by
   sphinx-quickstart on Wed Jun 19 19:56:09 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Guia para subir aplicação CRUD contêinerizada!
===============================================


Passo 1: Instalação do Multipass
================================

Atualização de dependências 
----------------------------
Atualize as dependências do Linux para que não haja qualquer outro erro com ``sudo apt update`` e também execute o comando ``sudo apt upgrade``.


Download do Multipass
---------------------

Segue o link do site oficial do Multipass para a instalação do mesmo:

`Download Multipass`_

.. _Download Multipass: https://multipass.run/install

Após a instalação concluída, siga para o passo 2.

Passo 2: Criação da VM
=======================

Máquina virtual de desenvolvimento
----------------------------------

Crie uma máquina virtual para testes antes de executar em seu ambiente de produção.

Para criar sua máquina virtual, utilize o comando:

.. code-block:: rst

   $ multipass launch -n servDev docker

Para a criação, estaremos utilizando a imagem ``docker`` para que já venha configurado o Docker. O comando ``-n`` serve para definir um nome para a máquina virtual. Caso queira mudar, apenas digite ``multipass launch -n nome-da-maquina-virtual docker``. 

Acesso à máquina virtual 
------------------------
Utilize o comando: 

.. code-block:: rst

   $ multipass shell servDev

O comando ``multipass shell`` é utilizado para acessar a máquina virtual.

Atualize os pacotes e dependências da sua máquina virtual:


.. code-block:: rst

   $ sudo apt update && sudo apt upgrade
  
Passo 3: Download da aplicação
==============================

Antes de fazer o download, verifique se você está dentro da máquina virtual. Após verificar, digite o comando:

.. code-block:: rst

   $ git clone https://web:glpat-zyZUFmz1h5J_zyMg9Z2D@gitlab.com/henrique_corp/projetocrud

Verifique se a pasta foi baixada com o comando ``ls``.

Passo 4: Criação dos containers
===============================

Container banco de dados
------------------------

Para executar o container, utilize o seguinte comando: 

.. code-block:: rst

   $ docker container run -d -e MYSQL_ROOT_PASSWORD=suasenha --name bancoDeDados -v ./projetocrud/cruddb.sql:/docker-entrypoint-initdb.d/cruddb.sql mysql:8.0-debian

Lembre-se de substituir a senha em ``MYSQL_ROOT_PASSWORD=suasenha`` por uma senha forte.

Verifique se o container está rodando com o comando ``docker container ls``. 

Agora, execute este comando: 

.. code-block:: rst

   $ docker container inspect bancoDeDados | egrep IPAddress

Com este comando, você vai anotar o ``IPAddress``.

Agora precisamos mudar o arquivo ``functions.php``. Para isso, digite:

.. code-block:: rst

   $ sudo nano projetocrud/functions.php

Dentro do arquivo ``functions.php``, altere os dados: 


Acesso ao container banco de dados
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Para acessar o container quando necessário, digite o seguinte comando:

.. code-block:: rst

   $ docker container exec -it bancoDeDados bash

Para sair do container, digite o comando ``exit``

Container servidor web
----------------------

Para começar a configurar o container do servidor web, é necessário estar dentro da máquina virtual ``servDev``. Certifique-se de que o nome do terminal esteja ``ubuntu@servDev:~$``.

Rode o comando abaixo: 

.. code-block:: rst

   $ cp /etc/hosts /etc/host-modificated

Com este comando, você está fazendo uma cópia do seu ``/etc/hosts`` para a sua máquina virtual para fazer as modificações necessárias.

Agora vamos subir o container utilizando o comando: 

.. code-block:: rst

   $ docker run -d --name servidorWeb -p 80:80 -v /etc/host-modificated:/etc/hosts -v ./projetocrud:/var/www/techarper/ php:8.1-apache

Instalar e habilitar o MySQL PDO
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: rst

   $ docker container exec servidorWeb docker-php-ext-install pdo_mysql

Agora reinicie o seu container do servidor web:

.. code-block:: rst

   $ docker container restart servidorWeb

Configurando o domínio 
~~~~~~~~~~~~~~~~~~~~~~

Entre dentro do container do servidorWeb:

.. code-block:: rst

   $ docker container exec -it servidorWeb bash

Instale as dependências e o nano: 

.. code-block:: rst

   $ apt update && apt upgrade


.. code-block:: rst

   $ apt install nano

Agora vamos configurar o Apache:

.. code-block:: rst

   $ nano /etc/apache2/sites-available/000-default.conf

Agora edite o arquivo e o deixe com as seguintes informações: 

.. code-block:: rst

   ServerName www.techarper.com
   ServerAlias www.techarper.com techarper.com
   ServerAdmin henrique@techarper.com
   DocumentRoot /var/www/techarper

Agora salve com o comando ``Ctrl + O`` e saia do nano com ``Ctrl + X``. 

Após fazer as alterações, saia do container do servidorWeb com ``exit``.

Configurando o hosts
====================

Agora, na sua máquina virtual ``servDev``, edite o ``/etc/hosts-modificated`` para configurar o DNS.

Primeiro, obtenha o IP da sua máquina virtual com o seguinte comando: 

.. code-block:: rst

   $ ip -4 -br -c a

Vai aparecer algo semelhante a isso: 

.. code-block:: rst

   lo               UNKNOWN        127.0.0.1/8 
   ens3             UP             10.119.137.190/24 metric 100


Anote o IP de ``ens3`` para editar dentro do seu ``/etc/hosts-modificated``.

.. code-block:: rst

   $ nano /etc/hosts-modificated

Seu arquivo vai estar da seguinte forma: 

.. code-block:: rst

   127.0.1.1 servDev servDev
   127.0.0.1 localhost

   # The following lines are desirable for IPv6 capable hosts
   ::1 localhost ip6-localhost ip6-loopback
   ff02::1 ip6-allnodes
   ff02::2 ip6-allrouters

Adicione mais duas linhas com seu IP da máquina virtual e seu nome de domínio configurado dentro do Apache.


.. code-block:: rst

   127.0.1.1 servDev servDev
   127.0.0.1 localhost

   10.119.137.190   techarper.com   
   10.119.137.190   www.techarper.com

   # The following lines are desirable for IPv6 capable hosts
   ::1 localhost ip6-localhost ip6-loopback
   ff02::1 ip6-allnodes
   ff02::2 ip6-allrouters

Pronto, agora feche e salve. Em seguida, reinicie o container servidorWeb. 

.. code-block:: rst
   
   $ docker container restart servidorWeb

Após feito isso, tente acessar o domínio configurado.
