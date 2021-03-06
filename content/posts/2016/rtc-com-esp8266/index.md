---
type: wordpress
title: Relógio RTC com o ESP8266
date: 2016-02-05 12:38:14
authors:
  - alexandrevicenzi
slug: rtc-com-esp8266
images:
  - /images/wp-content/uploads/2016/02/rtc.jpg
categories:
  - embarcados
  - tutoriais
tags:
  - esp-12
  - esp8266
  - iot
  - lua
  - nodemcu
  - rtc
---

No artigo de hoje veremos como utilizar um RTC (<em>Real Time Clock</em>) com o dispositivo ESP8266. Este módulo incorpora funções Wi-Fi e foi pensado para projetos de baixo custo para mobilidade, eletrônicos vestíveis, Internet das Coisas (IoT), e o que sua criatividade permitir.

Este artigo pode ser um tanto quanto simples, mas utilizar um RTC (<em>Real Time Clock</em>) em um projeto é de grande importância as vezes. Principalmente quando desejamos monitorar algo em determinados horários. Vamos pegar um exemplo básico, supondo que você esteja monitorando a temperatura do ambiente, é interessante saber a hora da medição para calcular a variação de temperatura durante um determinado período. Sim, este é um exemplo simples, mas exemplifica claramente o uso de um RTC.

<!--more-->

Antes de começar vamos a lista de materias necessários.

<strong>Material</strong>
<ul>
	<li><a href="http://www.filipeflop.com/pd-2c1464-modulo-wifi-esp8266-esp-07.html?utm_medium=Post&amp;utm_campaign=ButecoOpenSource" target="_blank">ESP-07</a> ou <a href="http://www.filipeflop.com/pd-2c1441-modulo-wifi-esp8266-esp-12e.html?utm_medium=Post&amp;utm_campaign=ButecoOpenSource" target="_blank">ESP-12</a></li>
	<li><a href="http://www.filipeflop.com/pd-1c7dbf-real-time-clock-rtc-ds3231.html?utm_medium=Post&amp;utm_campaign=ButecoOpenSource" target="_blank">RTC DS3231</a></li>
</ul>
<strong>Posso utilizar outro ESP?</strong>

Sim e não, o ESP8266 possui interface I2C, porém não são todos os modelos de ESP que disponibilizam estes pinos. No caso do ESP-01 estes pinos não estão disponíveis no módulo, apesar de estarem no componente pricipal. Já o ESP-07 é basicamente o ESP-12 com antena externa, sendo assim é possível utilizá-lo.

<strong>DS3231</strong>

O <a href="http://datasheets.maximintegrated.com/en/ds/DS3231.pdf" target="_blank">DS3231</a> é um relógio de tempo real (<em>Real Time Clock</em>) de alta precisão e baixo consumo de energia com interface de comunicação I2C.

<strong>Código</strong>

O código abaixo esta escrito na linguagem de programação Lua, e para tal, você deve utilizar o firmware <a href="http://nodemcu.readthedocs.org/en/dev/" target="_blank">NodeMCU</a> no seu ESP. Se você tem dúvidas de como alterar o firmware do seu ESP, eu recomento a leitura do <a href="/nodemcu-lua-para-o-esp8266">NodeMCU: Lua para o ESP8266</a>.

<script src="//gistfy-app.herokuapp.com/github/ButecoOpenSource/exemplos/nodemcu/rtc.lua?branch=master" type="text/javascript"></script>

Na tabela abaixo são explicadas as constantes necessárias:
<table>
<tbody>
<tr>
<td>I2C_ADDRESS</td>
<td>Endereço I2C do DS3231.</td>
</tr>
<tr>
<td>I2C_REG</td>
<td>Endereço iniciar de leitura</td>
</tr>
<tr>
<td>I2C_ID</td>
<td>ID do I2C</td>
</tr>
<tr>
<td>SDA_PIN</td>
<td>Pino onde esta o SDA (1-12)</td>
</tr>
<tr>
<td>SCL_PIN</td>
<td>Pino onde esta o SCL (1-12)</td>
</tr>
</tbody>
</table>
Como o DS3231 utiliza utiliza o sistema de numeração <a href="https://pt.wikipedia.org/wiki/Codifica%C3%A7%C3%A3o_bin%C3%A1ria_decimal" target="_blank">BCD (Binary-coded decimal)</a>, necessitamos das funções <code>to_decimal</code> e <code>to_bcd</code> para converter um número inteiro para BCD.

O método <code>get_time</code> é responsável por obter a data/hora atual. Inicialmente enviamos para o I2C qual endereço e posição vamos ler. Depois, solicitamos 7 bytes, pois sabemos quantos bytes ler e o que cada posição contém. Como cada parte da data/hora consome 1 byte e sabendo que o endereço inicial é 00h, a última porção da data/hora estará no endereço 06h. Conforme <a href="http://datasheets.maximintegrated.com/en/ds/DS3231.pdf" target="_blank">datasheet do DS3231</a>, segue abaixo os endereços de cada parte:
<table>
<tbody>
<tr>
<td>00h</td>
<td>Segundos (00-59)</td>
</tr>
<tr>
<td>01h</td>
<td>Minutos (00-59)</td>
</tr>
<tr>
<td>02h</td>
<td>Horas (00-23)</td>
</tr>
<tr>
<td>03h</td>
<td>Dia da Semana (1-7)</td>
</tr>
<tr>
<td>04h</td>
<td>Dia do Mês (01-31)</td>
</tr>
<tr>
<td>05h</td>
<td>Mês (01-12)</td>
</tr>
<tr>
<td>06h</td>
<td>Ano (00-99)</td>
</tr>
</tbody>
</table>
O método <code>get_time_iso_8601</code> retorna a data/hora no formato <a href="https://pt.wikipedia.org/wiki/ISO_8601" target="_blank">ISO 8601</a>, que é uma representação internacional de data e hora.

Por fim, o método <code>set_time</code> é similar ao <code>get_time</code>. A ordem de gravação é a mesma ordem de leitura, assim como na leitura é necessário converter de inteiro para BCD.

<strong>Exemplo de uso</strong>

No exemplo abaixo, inicialmente alteramos a hora com a função <code>set_time</code>, para ajustar o relógio. Em seguida solicitamos a hora atual com a função <code>get_time</code>, esta função retorna uma tabela de inteiros. Por fim, solicitamos a hora atual no formato ISO 8601 com a função <code>get_time_iso_8601</code>, que retornará uma <em>string</em>.

<samp>&gt; =set_time(0, 11, 0, 3, 3, 2, 16)
&gt; =get_time()
06 11 00 03 03 02 16
&gt; =get_time_iso_8601()
2016-02-03-T00:11:37</samp>

Para ajudar um pouco a quem está começando, a imagem abaixo demonstra como utilizar o <a href="http://esp8266.ru/esplorer/">ESPlorer</a> para fazer o upload do arquivo e a execução dos comandos.

<a href="/images/wp-content/uploads/2016/02/esp_rtc_description.png">
<img class="aligncenter" src="/images/wp-content/uploads/2016/02/esp_rtc_description.png" alt="ESPlorer" width="50%" height="50%" />
</a>
<p style="text-align: center;"><small>Clique na imagem para ampliá-la</small></p>
<strong>Consideração final</strong>

Este é o primeiro projeto com o ESP de muitos que ainda estão por vir. Como o NodeMCU utiliza Lua, esta é uma  linguagem de programação que iremos aprender ao longo dos artigos. Mesmo que você não conheça Lua, não precisa se preocupar, eu também não conhecia antes de utilizar o NodeMCU.

Gostaria de agradecer a <a href="http://www.filipeflop.com/?utm_source=Blog&amp;utm_medium=Post&amp;utm_campaign=ButecoOpenSource" target="_blank">FILIPEFLOP</a> pelo material fornecido para a realização deste artigo.

Se ficou alguma dúvida, com o ESP, NodeMCU ou o ESPlorer, deixe um comentário que iremos ajudá-lo. :)
