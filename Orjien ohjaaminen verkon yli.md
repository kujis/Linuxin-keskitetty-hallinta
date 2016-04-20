#Orjien ohjaaminen verkon yli
13.4.2016

Ympäristö toteutettiin VirtualBoxissa. Käytössäni on Ubuntu Server 14.04 ja kaksi Xubuntu -tietokonetta.

###Syötin alla olevat komennot serverille:

1. wget https://apt.puppetlabs.com/puppetlabs-release-pc1-trusty.deb -ladatakseni tiedostot verkosta.
2. dpkg -i puppetlabs-release-pc1-trusty.deb -Suorittaakseni ladatun tiedoston
3. apt-get update
4. apt-get install puppetserver


###Syötin alla olevat komennot työasemille:
1. wget https://apt.puppetlabs.com/puppetlabs-release-pc1-trusty.deb
2. dpkg -i puppetlabs-release-pc1-trusty.deb
3. apt-get update
4. apt-get install puppet-agent

Palvelimen nimen lisääminen työasemiin konfiguroimalla tiedostoa: nano /etc/puppetlabs/puppet/puppet.conf
Lisätään tiedostoon: 
[main]
server = srv

Tämän jälkeen master puppet eli serveri täytyy käynnistää uudestaan komennolla:
/etc/init.d/puppetserver restart

Sain kuitenkin vastaukseksi ... failed.

Logien mukaan vaikutti siltä että sertifikaateissa oli ongelmia. Tyhjensin certificaatit ja generoin ne uudestaan seuraamalla ohjetta https://docs.puppet.com/puppet/latest/reference/ssl_regenerate_certificates.html.

Ajoin /etc/init.d/puppetserver restart -komennon uudestaan ja tällä kertaa vastaukseksi tuli done.

puppet agent -t - komennolla tarkistin onko palvelimella tapahtunut muutoksia.

###20.4.2016

#####Ws1 sertifikaatti piti hakea uudestaan seuraavilla toimenpiteillä.
Pysäytin Puppet -palvelun 
sudo puppet resource service puppet ensure=stopped

Poistin kansion SSL
rm -r /etc/puppetlabs/puppet/ssl

Käynnistin Puppet -palvelun uudestaan
puppet resource service puppet ensure=running

Ajoin komennon
Puppet agent -t

ja lopulta ajoin palvelimella komennon sudo puppet cert list, joka näytti sertifikaatit.

##Openssh-server -paketin asennus ws1 -työasemalle

Lisäsin ubuntu serveriltä löytyvään tiedostoon: /etc/puppetlabs/code/environments/production/manifests/site.pp alla olevat tiedot:

node ws1 {
	package ( 'openssh-server':
	ensure => 'installed':
	}
}

##Käyttäjän lisääminen ws2 -työasemalle

useradd ville
passwd ville
grep ville /etc/shadow
grep ville /etc/shadow >> /etc/puppetlabs/code/environments/production/manifests/site.pp

Lisäsin ubuntu serveriltä löytyvään tiedostoon: /etc/puppetlabs/code/environments/production/manifests/site.pp alla olevat tiedot:

node ws2 {
	user { 'ville':
	ensure => 'present',
	name => 'ville',
	home => '/home/ville',
	managehome => 'yes',
	password => grep ville /etc/shadow -komennolla luotu numerosarja
	}
}	


Ja lopulta ajoin molemmilla työasemilla puppet agent -t -kommenon. Ville -käyttäjätunnus tuli ws2 -tietokoneelle ja SSH asentui ws1 -tietokoneelle.

root@ws2:~# puppet agent -t
Info: Using configured environment 'production'
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for ws2.tielab.haaga-helia.fi
Info: Applying configuration version '1461138582'
Notice: /Stage[main]/Main/Node[ws2]/User[ville]/ensure: created
Notice: Applied catalog in 0.26 seconds

Tarkistin että käyttäjätunnus oli tullut tietokoneelle.

root@ws1:~# puppet agent -t
Info: Caching certificate for ws1.tielab.haaga-helia.fi
Info: Caching certificate_revocation_list for ca
Info: Caching certificate for ws1.tielab.haaga-helia.fi
Info: Using configured environment 'production'
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for ws1.tielab.haaga-helia.fi
Info: Applying configuration version '1461139188'
Notice: /Stage[main]/Main/Node[ws1]/Package[openssh-server]/ensure: created
Notice: Applied catalog in 11.18 seconds

Tarkistin, että SSH on asentunut komennolla ls /etc/ ja listasta löytyi ssh.

