# Harjoitus 5

Tämän harjoituksen tavoitteena oli opetella käyttämään Saltin muotteja, sekä luoda muutamia tiloja Saltin tavallisia toimintoja käyttäen.

Olen ennen harjoituksen tekoa asentanut uuden 64-bittisen Xubuntu orja-koneen **'e008'**, ja tähän openssh-serverin SSH-yhteyttä varten käyttäen [tekemääni skriptiä.](https://github.com/rootElmo/Agent-Setter)

#### Hello templates! Tee muotilla esimerkkitiedosto, jossa on muuttujien (esim grains) arvoja.

Aloitin luomalla uuden tilan, ja tälle kansion **hellojinja** sijaintiin ***/srv/salt/***. Loin tilalle tarvittavan _init.sls_-tiedoston komennolla

	master $ echo "" | sudo tee init.sls

Kirjoitin init.sls:än alustavasti seuraavaan muotoon:

	/tmp/hello.txt:
	  file.managed:
	    - source: salt://hellojinja/hello.txt

Loin seuraavaksi tarvittavan _hello.txt_-tiedoston komennolla

	echo "Hello world" | sudo tee hello.txt

ja katsoin, oliko tiedosto paikallaan _cat_ komennolla.

![scrshot1](../images/scrshot001.png)

Ajoin tämän jälkeen tilan aktiiviseksi:

	master $ sudo salt 'e008' state.apply hellojinja

![scrshot2](../images/scrshot002.png)

Testasin seuraavaksi Saltin avulla herra-koneelta, oliko tiedosto oikeasti paikallaan ja sisälsikö se sen, mitä olin sinne kirjoittanut

	master $ sudo salt 'e008' cmd.run 'cat /tmp/hello.txt'

![scrshot3](../images/scrshot003.png)

Homma pelittää tähän asti!

Seuraavaksi muutin _hello.txt_-tiedoston seuraavaan muotoon:

	Your agent-computer's CPU is {{ grains[cpu_model] }}

ja _init.sls_-tiedoston seuraavaan muotoon:

	/tmp/hello.txt:
	  file.managed:
	    - source: salt://hellojinja/hello.txt
	    - template: jinja

Ajoin tilan aktiiviseksi, mutta sain virheilmoituksen:

![scrshot4](../images/scrshot004.png)

Muuttujaa 'cpu_model' ei selvästikään oltu määritelty. En ihan ymmärtänyt tätä laajemmassa kontekstissam joten päätin lueskella internetistä ohjeita ja dokumentaatiota.

Pienin kikkailun ja kokeilun jälkeen kokeilun jälkeen sain kuin sainkin tiedoston toimimaan. Ongelmana olivat olemattomat hapsut 'cpu_model':in ympärillä. _hello.txt_ näytti näiden toimenpiteiden jälkeen tältä:

	Your agent-computer's CPU is {{ grains['cpu_model'] }}

Tila siis ajautui onnistuneesti ja tiedossa oli grainsista saatu oikea tieto orja-koneella:

![scrshot5](../images/scrshot005.png)

## Message of the Day

Tämän tehtävän tavoitteena oli muokata Ubuntun antamaa motd:ia, kun koneelle kirjaudutaan sisään, tai otetaan yhteys SSH:lla. Tällaisesta nivaskasta on siis kyse:

![scrshot6](../images/scrshot006.png)

Otin siis yhteyden orja-koneelle SSH:lla ja tämän onnistuttua sain tervehdykseksi kyseisen motd:in.

## Lähteet

Tero Karvinen: http://terokarvinen.com/2020/configuration-managment-systems-palvelinten-hallinta-ict4tn022-spring-2020/

SALTSTACK: https://docs.saltstack.com/en/master/topics/tutorials/states_pt3.html, https://docs.saltstack.com/en/getstarted/config/jinja.html
