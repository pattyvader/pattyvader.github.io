---
layout: post
title:  "Meu primeiro pacote no PyPI"
subtitle: ""
date:   2017-12-07
background: '/img/logo-pypi.jpg'
image: '/img/logo-pypi.jpg'
---

Está rolando uma semana bem legal no Olist. O Bootcamp. Estão ocorrendo inúmeras atividades de programação,
entre elas o primeiro Hackthon do Olist. Pela primeira vez todos os devs estão reunidos fisicamente
em Curitiba, no office novinho.

Ou melhor, quase todos. :cry:

Estão participando remotamente eu que estou em Barcelona, e outro dev que está em Portugal.

Mas tudo bem. Como a galera é muito criativa por lá, conseguimos participar de tudo!!!  :dancer:

E para ajudar minha equipe fiquei responsável por publicar o package do nosso projeto no PyPI. Foi a primeira vez que submeti um pacote. Nesse post mostrei como realizar essa tarefa.  :smile:

## Mas o que é PyPI?

É um repositório de software open-source oficial do Python. Todo mundo pode enviar seus pacotes para lá.
E depois pode instalar na sua máquina os pacotes usando `pip` ou `easy_install`. Talvez você já tenha feito isso e nem sabia.  :hushed:

## Como publicar um *package*?

Antes de tudo: seu projeto deve estar em repositório público, pois conforme será mostrado nos passos seguintes o
PyPI precisará ter acesso ao projeto.

A PyPA(Python Packaging Authority) possui um [repositório](https://github.com/pypa/sampleproject) no GitHub onde você poderá verificar toda a estrutura de um projeto.


##### 1. Crie suas contas no PyPI

Primeiro criei as contas no PyPI. Existem 2 tipos - [PyPI](https://pypi.python.org/pypi?%3Aaction=register_form) e
[PyPI test](https://testpypi.python.org/pypi?%3Aaction=register_form).
Segui como base o artigo do [Peter Downs](http://peterdowns.com/posts/first-time-with-pypi.html).
E ele aconselha a criar as contas com os mesmos **user** e **password**. Facilita na hora de lembrar do login.

##### 2. Escreva o arquivo `.pypirc`

O `.pypirc` é o arquivo de configuração para fazer a autenticação no PyPI e contém os
links para fazer o upload do pacote. Ele deve ficar assim:

```
[distutils]
index-servers =
  pypi
  pypitest

[pypi]
repository: https://upload.pypi.org/legacy/
username=your_username
password=your_password

[pypitest]
repository: https://test.pypi.org/legacy/
username=your_username
password=your_password

```

>  Os links `https://pypi.python.org/pypi` e `https://testpypi.python.org/pypi` não estão mais ativos.

Devido a migração para o novo domínio [PyPI.org](https://packaging.python.org/glossary/#term-pypi-org) deve-se usar o `pypi.org/legacy`

Mais explicações [aqui](https://packaging.python.org/guides/migrating-to-pypi-org/)


##### 3. Prepare o arquivo de setup

Cada projeto no PyPI precisa de um arquivo `setup.py`, no diretório root. Ele contêm alguns dados sobre o seu projeto.
Ele deve ficar mais ou menos assim:

```
from distutils.core import setup
setup(
  name='sample',
  packages = ['mypackage'], # this must be the same as the name above
  version='1.2.0',
  description='A sample Python project',
  author='The Python Packaging Authority',
  author_email='pypa-dev@googlegroups.com',
  license='MIT',
  url='https://github.com/pypa/sampleproject', # The project's main homepage.
  keywords = ['testing', 'logging', 'example'], # arbitrary keywords
  classifiers = [],
)
```

[Exemplo de um arquivo de setup](https://github.com/pypa/sampleproject/blob/master/setup.py)

##### 4. Faça o upload

* **PyPI test**

    Para testar o upload e verificar se tudo está correto deve-se digitar:

   `python setup.py register -r pypitest`

    Esse comando faz o registro do pacote no PyPI test.

    Se tudo correu bem digite:

   `python setup.py sdist upload -r pypitest`

   Pronto! Se não apareceram erros pode-se ir para o próximo passo.

* **PyPI**

    Agora é a hora!  :alarm_clock:

    Para enviar o seu pacote para o PyPI somente digite:

    `python setup.py register -r pypi`

    E por fim:

    `python setup.py sdist upload -r pypi`


Seu pacote já se encontra nos servidores do PyPI!


*Simples assim!!!!*

---

[Para saber mais](https://pypi.python.org/pypi)

[Olisters developers](https://pypi.python.org/pypi/tapioca-statuspage)

---
