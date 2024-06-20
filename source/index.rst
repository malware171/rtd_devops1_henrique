.. rtd_devops_henrique documentation master file, created by
   sphinx-quickstart on Wed Jun 19 19:56:09 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Guia para subir aplicacao crud conternizada!
===============================================


Passo 1: Instalacao do multipass
================================

Atualizacao de dependencias 
----------------------------
Atualize as dependencias do linux para que nao haja qualquer outro erro ``sudo apt update`` tambem o comando ``sudo apt upgrade``


Download do multipass
---------------------

Segue o link do site oficial do multipass para a instalacao do mesmo

`Download Multipass`_

.. _Download Multipass: https://multipass.run/install

Apos a instalacao concluida siga para o passo 2

Passo 2: Criacao da VM
=======================

Maquina virtual de desenvolvimento
----------------------------------

Crie uma maquina virtual para testes antes de executar em seu ambiente de producao

Para criar sua maquina virtual utilize o comando:

.. code-block:: rst

   $ multipass launch -n servDev docker

Para a criacao estaremos utilizando a imagem ``docker`` para que ja venha configurado o docker, o comando ``-n`` serve para definir um nome para a maquina virtual, caso queira mudar apenas digite ``multipass launch -n nome-da-maquina-virtual docker``. 

Acesso a maquina virtual 
------------------------
Utilize o comando: 

.. code-block:: rst

   $ multipass shell servDev

O comando ``multipass shell`` e utilizado para acessar a maquina virtual.

Atualize os pacotes e dependencias da sua maquina virtual:


.. code-block:: rst

   $ sudo apt update && sudo apt upgrade
  
Passo 3: Download da aplicacao
==============================

Antes de fazer download verifique se voce esta dentro da maquina virtual, apos verificado digite o comando:

.. code-block:: rst

   $ git clone https://web:glpat-zyZUFmz1h5J_zyMg9Z2D@gitlab.com/henrique_corp/projetocrud

Verifique se a pasta foi baixada com o comando ``ls``.

Passo 4: Criacao dos containers
===============================

Container banco de dados
------------------------

Para executar o container utilize o seguinte comando: 

.. code-block:: rst

   $ docker container run -d -e MYSQL_ROOT_PASSWORD=suasenha --name bancoDeDados -v ./projetocrud/cruddb.sql:/docker-entrypoint-initdb.d/cruddb.sql mysql:8.0-debian

Lembre-se de substituir a senha em ``MYSQL_ROOT_PASSWORD=suasenha`` adicione uma senha forte.

Verifique se o container esta rodando com o comando ``docker container ls``. 

Agora preciso que voce execute esse comando: 

.. code-block:: rst

   $ docker container inspect bancoDeDados | egrep IPAddress

Com esse comando voce vai anotar o ``IPAddress``.

Agora precisamos mudar o arquivo ``function.php`` para isso digite:

.. code-block:: rst

   $ sudo nano projetocrud/functions.php

Dentro do arquivo ``functions.php`` altere os dados: 


Acesso ao container banco de dados
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Para acessar o container digite o seguinte comando:

.. code-block:: rst

   $ docker container exec -it bancoDeDados bash

Para sair do container digite o comando ``exit``

Container servidor web
----------------------







Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
