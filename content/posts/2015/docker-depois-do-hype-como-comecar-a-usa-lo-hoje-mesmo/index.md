---
title: Docker depois do hype, como começar a usá-lo hoje mesmo
date: 2015-10-18 19:53:49
authors:
  - germanofronza
slug: docker-depois-do-hype-como-comecar-a-usa-lo-hoje-mesmo
images:
  - /images/wp-content/uploads/2015/10/docker-small.png
categories:
  - ferramentas
  - tutoriais
tags:
  - docker
  - docker-compose
  - python
  - vagrant
---

{{<figure src="/images/wp-content/uploads/2015/10/docker-small.png" alt="Docker Logo">}}

Não é nenhuma novidade que Docker está na boca do povo. Qualquer conferência ou evento da nossa área tem ou terá uma palestra sobre o assunto, é o que vende no momento. Vários cases sendo apresentados, toneladas de ferramentas de gerenciamento e orquestração de *containers*, fazem do Docker uma ferramenta muito poderosa e que cresce a cada dia. Porém com tanta informação e soluções distintas faz com que muitos de nós sintam-se desmotivados a entrar nesse mundo, achando que no fim das contas será um grande *overkill* para nossos projetos atuais...
<!--more-->
Começando com uma pergunta, gerenciar ambientes de desenvolvimento é um problema para você ou seu time? Se a resposta for não, continue a ler o texto só por esporte. Mas se for sim, Docker será um grande aliado seu.

Se você trabalha em projetos distintos e principalmente que tenham dependência um com outro (ex: microservices applications), de alguma maneira você precisa isolar os ambientes para que pelo menos não hajam conflitos com versão de libs, de bancos de dados utilizados, etc. O que vem se utilizando a um bom tempo são máquinas virtuais provisionadas pelo Vagrant. Com ele é possível criar ambientes de desenvolvimento portáveis, reproduzíveis e que sejam o mais semelhante possível com os ambientes de staging e produção. O principal problema que surge com o uso dele é justamente o uso de máquinas virtuais e a degradação de performance da sua estação de trabalho. Máquinas virtuais possuem seu próprio Sistema Operacional, gerenciamento de memória, drivers de dispositivos, etc. Ter mais do que um ambiente de desenvolvimento "ligado" pode fazer com que seu trabalho fique insuportável.

*Containers* Docker por outro lado são muito mais leves, pois compartilham os recursos do Sistema Operacional hospedeiro. O diagrama abaixo ilustra a comparação entre máquinas virtuais e *containers*:

{{<figure src="/images/wp-content/uploads/2015/10/docker-containers-vms.png" alt="Docker Containers" caption="Fonte: Docker">}}

Com o Docker você pode ter diversos ambientes de desenvolvimento "ligados" ao mesmo tempo sem impactar a performance do seu computador. Não é utópico falar em ter todas as aplicações que compõem um sistema rodando ao mesmo tempo (por exemplo: WWWs, Nginx, workers,cache, databases, log shipping, etc. Ou ainda um conglomerado de micro serviços se este for o seu caso).

Os *containers* Docker são construídos a partir de imagens. As imagens por sua vez são construídas com as instruções contidas no arquivo Dockerfile, conforme o exemplo abaixo:

```python
FROM python:3
ADD . /code
WORKDIR /code
RUN pip install -r requirements.txt
```

* Linha 1) Indica que esta imagem irá herdar da imagem Python versão 3;
* Linha 2) Mapeia o diretório corrente (.) com um diretório nomeado /code;
* Linha 3) Indica que dessa linha em diante o diretório de trabalho (cd) é o /code;
* Linha 4) Executa o comando pip install para instalar as dependências do projeto.

Você deve estar se perguntando de onde vem essa imagem `python:3`. Ela vem do [Docker Hub](https://hub.docker.com), um local onde pode-se publicar imagem públicas ou privadas. Você também poderá publicar suas imagens para lá ou qualquer outro Docker Registry.

Outro aplicativo que vem junto com o Docker é o docker-compose. É com ele que você define os serviços que compõem a sua aplicação, no estilo Heroku. Para usá-lo, você deve criar um arquivo chamado `docker-compose.yml`, conforme o exemplo abaixo:

```yaml
web:
  build: .
  working_dir: /code
  command: python app.py
  links:
    - db
    - mongo
    - redis
  ports:
    - "8000:8000"
  db:
    images:
  - postgres
  mongo:
    images:
  - mongo:2.6
  redis:
    images:
  - redis:latest
```

Essa aplicação python de exemplo é composta pelos serviços web, db, mongo e redis. No serviço web estamos "linkando" com os demais, assim o Docker cria entradas no *host* para cada IP de serviço. Para "subir" a sua aplicação e seus serviços execute docker-compose up.

Acesse a [documentação do Docker](https://docs.docker.com/) que cobre desde a instalação (Mac, Linux e Windows) até os casos de uso mais complexos. Como sempre é um bom ponto de partida.

Espero que com esse pequeno empurrão você possa começar a usar Docker localmente e quem sabe no futuro até avançar com ele para a produção.
