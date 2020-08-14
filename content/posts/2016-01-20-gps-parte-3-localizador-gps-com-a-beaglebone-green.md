---
type: wordpress
title: "GPS Parte 3: Localizador GPS com a BeagleBone Green"
date: 2016-01-20 21:11:20
authors:
  - alexandrevicenzi
slug: /gps-parte-3-localizador-gps-com-a-beaglebone-green/
images:
  - /images/wp-content/uploads/2015/12/map_pin.png
categories:
  - Tutorial
tags:
  - beaglebone green
  - bottle
  - flask
  - gps
  - nmea
  - python
  - raspberrypi
---

Na <a href="/gps-parte-1-entendendo-o-seu-funcionamento" target="_blank">primeira parte</a> sobre GPS, vimos como este sistema de rastreamento por satélite funciona. Já na <a href="/gps-parte-2-decodificando-uma-sentenca-nmea" target="_blank">segunda parte</a>, vimos uma sentença NMEA funciona e como fazemos para decodificá-la.

Neste artigo, iremos colocar tudo o que vimos em prática. Sendo assim, vamos a lista de material necessária:

<ul>
	<li>Uma <a href="http://www.filipeflop.com/pd-1436b0-beaglebone-black-rev-c.html?utm_source=Blog&amp;utm_medium=Banner&amp;utm_campaign=ButecoOpenSource" target="_blank">BeagleBone Black </a> ou <a href="http://www.filipeflop.com/pd-24c7f0-beaglebone-green.html?utm_source=Blog&amp;utm_medium=Banner&amp;utm_campaign=ButecoOpenSource" target="_blank">Green</a> ou um <a href="http://www.filipeflop.com/pd-1d13cc-raspberry-pi-2-model-b.html?utm_source=Blog&amp;utm_medium=Banner&amp;utm_campaign=ButecoOpenSource" target="_blank">Raspberry Pi</a></li>
	<li>Um módulo GPS</li>
</ul>

No meu caso, irei utilizar uma <a href="http://www.filipeflop.com/pd-24c7f0-beaglebone-green.html?utm_source=Blog&amp;utm_medium=Banner&amp;utm_campaign=ButecoOpenSource" target="_blank">BeagleBone Green</a>, que foi fornecida pela <a href="http://www.filipeflop.com?utm_source=Blog&amp;utm_medium=Banner&amp;utm_campaign=ButecoOpenSource" target="_blank">FILIPEFLOP</a> e um modulo GPS ublox NEO-6m.

Antes de mais nada, vou explicar o que vamos fazer. Basicamente, iremos utilizar a BBG para controlar o GPS e servir como servidor de página para exibir um mapa com a localização atual do GPS. Na imagem abaixo você pode observar o resultado.

<a href="/images/wp-content/uploads/2016/01/gps-bbg.png"><img class="aligncenter" title="" src="/images/wp-content/uploads/2016/01/gps-bbg.png" alt="" width="40%" height="40%" /></a>

<strong>Código</strong>

Para criar a parte de templates e rotas foi utilizado o micro framework <a href="http://bottlepy.org/docs/dev/index.html" target="_blank">Bottle</a>. O Bottle é um WSGI para Python, muito similar ao <a href="http://flask.pocoo.org/" target="_blank">Flask</a>. Optei por ele pelo fato de ser distribuido em um único arquivo, facilitando a vida de quem usa sistemas embarcados.

Outra dependência é o <em>parser</em> NMEA implementado na <a href="/gps-parte-2-decodificando-uma-sentenca-nmea" target="_blank">segunda parte</a> deste tutorial.

No arquivo abaixo temos o template da página do mapa.

```html
<html>
<head>
    <title>GPS Position</title>
    <meta name="viewport" content="initial-scale=1.0">
    <meta charset="utf-8">
    <style>
      html, body {
        height: 100%;
        margin: 0;
        padding: 0;
      }
      #map {
        height: 100%;
      }
    </style>
</head>
<body>
    <div id="map"></div>
    <script type="text/javascript">
        function initMap() {
            var map = new google.maps.Map(document.getElementById('map'), {
                zoom: 15,
                center: {
                    lat: {{ latitude }},
                    lng: {{ longitude }}
                }
            });

            var marker = new google.maps.Marker({
                position: {
                    lat: {{ latitude }},
                    lng: {{ longitude }}
                },
                animation: google.maps.Animation.DROP,
                map: map,
                title: 'Current Position'
            });
        }
    </script>
    <script src="https://maps.googleapis.com/maps/api/js?key={{ api_key }}&callback=initMap" async defer></script>
</body>
</html>
```

Como podemos observar o código é bem simples, e toda a parte de inicialização do mapa é feito nas linhas 20 a 38. O mapa faz parte da <a href="https://developers.google.com/maps/documentation/javascript/?hl=pt-br" target="_blank">Google Maps Javascript API</a>, o que facilita muito o desenvolvimento.

No arquivo abaixo temos o módulo responsável por conversar com o GPS.

```py
# -*- coding: utf-8 -*-

import Adafruit_BBIO.UART as UART
import serial
import thread
import Queue
import nmea

from collections import namedtuple

Pos = namedtuple('Pos', ['latitude', 'longitude'])

q = Queue.Queue()
parser = nmea.NMEAParser()


def listen(q):
    UART.setup("UART2")

    with serial.Serial(port="/dev/ttyO2", baudrate=9600) as ser:
        print('Connected to GPS: %s' % ser.name)
        while True:
            sentence = ser.readline()
            # remove \r\n
            sentence = sentence[:-2]

            if sentence[0] == '$':
                try:
                    gps_data = parser.parse(sentence)

                    if gps_data.is_valid and gps_data.sentence_name != 'VTG':
                        q.put(gps_data)
                except:
                    pass


def start():
    thread.start_new_thread(listen, (q,))


def get_current_position(wait=False):
    try:
        gps_data = q.get(wait)
        return Pos(latitude=gps_data.latitude_degree, longitude=gps_data.longitude_degree)
    except:
        return None


if __name__ == '__main__':
    start()

    while True:
        print(get_current_position(True))
```

Na linha 3 é importado o módulo UART do Adafruit, se você estiver usando um Raspberry Pi lembre-se de alterar o import. O método <code>listen</code> é quem faz praticamente todo o trabalho. Ele configura o UART, e fica escutando o GPS na porta serial. Ao receber os dados do GPS, é feita a decodificação da sentença, e se for uma sentença válida adicionamos na fila. A sentença <code>VTG</code> é ignorada pois ela não possui latitude e longitude.

O método <code>start</code> cria uma <code>Thread</code> para não dar <em>lock</em> na aplicação inteira enquanto a porta serial estiver em uso. Isto é necessário para iniciar o servidor WSGI, pois ele é que fica em <em>lock</em> na aplicação. Por fim, o método <code>get_current_position</code> retorna a posição atual do GPS se disponível.

No arquivo abaixo temos o <em>setup</em> do nosso WSGI.

```py
# -*- coding: utf-8 -*-

from bottle import route, run, template

import gps


@route('/')
def index():
    pos = gps.get_current_position()

    if pos:
        return template('index', api_key='<GOOGLE MAPS KEY>', latitude=pos.latitude, longitude=pos.longitude)
    else:
        return template('error')


if __name__ == '__main__':
    gps.start()
    run(host='0.0.0.0', port=8081)
```

Esta parte não tem muitos mistérios, mas é necessário resaltar que na linha 13, é necessário colocar a sua <a href="https://console.developers.google.com/flows/enableapi?apiid=maps_backend&amp;keyType=CLIENT_SIDE&amp;reusekey=true&amp;hl=pt-br" target="_blank">chave da Google Api</a> para que o mapa seja carregado no browser, isto porque o JavaScript do mapa faz acesso as APIs do Google. Não se preocupe, este serviço é gratuito. :)

Na linha 19, podemos observar que é necessário iniciar a <code>Thread</code> do GPS, e na linha 20, o próprio WSGI.

O projeto em si é relativamente simples, sendo a parte do GPS a com complexidade maior. O intuito desta série de artigos, era mostrar como é simples interpretar os dados recebidos do GPS. Apesar de presente em nosso dia a dia, muitos nem fazemos ideia de como esta tecnologia funciona.

Você pode conferir o código fonte na íntegra no <a href="https://github.com/ButecoOpenSource/exemplos/tree/master/exemplos_python/gps_bbg" target="_blank">repositório de exemplos</a> do Buteco.
