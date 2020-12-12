---
type: wordpress
title: TorChat
summary: |
  Muitos de vocês já devem ter ouvido falar do TorBrowser um serviço bastante conhecimento para quem deseja navegar de forma anônima, o TorChat busca a mesma ideia, envio e recebimento de mensagens de forma segura.
  TorChat é software livre licenciado sob os termos da GNU General Public License(GPL). A primeira versão pública do TorChat foi lançado em novembro de 2007 por Bernd Kreuß e desde então tem sido constantemente desenvolvido, disponível para Linux, Windows e atualmente em Mac.
date: 2015-05-11 23:05:53
authors:
  - daniel-magevski
slug: torchat
categories:
  - seguranca
  - privacidade
tags:
  - anonimato
  - tor
---

<a href="/images/wp-content/uploads/2015/05/torchat1.jpg"><img class="alignnone  wp-image-2141" src="/images/wp-content/uploads/2015/05/torchat1.jpg" alt="torchat1" width="773" height="474" /></a>

Hoje falarei sobre Torchat, um serviço que utiliza a rede tor.

Muitos de vocês já devem ter ouvido falar do TorBrowser um serviço bastante conhecimento para quem deseja navegar de forma anônima, o TorChat busca a mesma ideia, envio e recebimento de mensagens de forma segura.

Não abordarei de forma avançada sobre o serviço, somente o básico para você instalar e utilizá-lo.

Veja sobre a rede <a href="//pt.wikipedia.org/wiki/Tor_%28rede_de_anonimato%29" target="_blank">Tor</a>

TorChat é software livre licenciado sob os termos da <a href="//pt.wikipedia.org/wiki/GNU_General_Public_License" target="_blank">GNU General Public License</a>(GPL). A primeira versão pública do TorChat foi lançado em novembro de 2007 por Bernd Kreuß e desde então tem sido constantemente desenvolvido, disponível para Linux, Windows e atualmente em Mac.

Em 5 de fevereiro de 2013, o desenvolvedor Prof7bit moveu o TorChat para o GitHub como um protesto contra a censura do Google sobre o acesso ao download do TorChat para certos países.

No Torchat cada usuário tem um ID alfanumérico único, composto por 16 caracteres. Este ID será criado aleatoriamente pelo Tor quando o cliente é iniciado pela primeira vez, é basicamente o .onion, lembrando, como é um serviço anônimo não existe login e senha para acessar.

Funciona assim, você instalar, ele gera seu ID, mas se você entra em outro computador irá gerar outro ID, então como faço para acessar minha conta em outro computador?

Simples, copie os arquivos de configuração dentro da pasta que geralmente é .torchat, são 3 arquivos.

<a href="/images/wp-content/uploads/2015/05/torchat2.jpg"><img class="alignnone size-full wp-image-2140" src="/images/wp-content/uploads/2015/05/torchat2.jpg" alt="torchat2" width="512" height="129" /></a>

No diretório Tor ficam os arquivos importantes, como privary key, e o seu hostname (nesse caso seu ID), em buddy-list.txt fica sua lista de amigos inclusive seu próprio ID, e por último fica o inicializador, o arquivo que e ele irá ler quando iniciar.

Se você quer acessar em outro computador precisa somente fechar o torchat e substituir os arquivos na pasta, é bom guardar os arquivos, lembrando que mesmo que você acesse em outro, as mensagens já lidas não ficam salvas.

Agora como baixar? Se você utiliza algum Software Manger é fácil, precisa somente atualizar e procurar.

Se for por  terminal digite <em>sudo apt-get update</em> para atualizar e <em>sudo apt-get install torchat</em> para instalar.

Aqui tem o código fonte e o arquivo .deb em um link na descrição <a href="//github.com/prof7bit/TorChat" target="_blank">download</a>

Para usuários Mac, porém nunca testei <a href="//www.sourcemac.com/?page=torchat" target="_blank">download</a>

Após instalado ele ficará parecido como na imagem.

<a href="/images/wp-content/uploads/2015/05/torchat3.png"><img class="alignnone size-full wp-image-2139" src="/images/wp-content/uploads/2015/05/torchat3.png" alt="torchat3" width="363" height="388" /></a>

Agora só passar o seu ID para seus amigos.

Wiki sobre <a href="//en.wikipedia.org/wiki/TorChat" target="_blank">Torchat </a>

O projetor Tor tem outros serviços veja-os <a href="//www.torproject.org/projects/projects.html.en" target="_blank">Tor Projetc</a>

&nbsp;
