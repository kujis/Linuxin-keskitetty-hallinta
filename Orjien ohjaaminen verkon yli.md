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

Ws1 sertifikaatti piti hakea uudestaan ajamalla seuraavilla toimenpiteillä.
Pysäytin Puppet -palvelun 
sudo puppet resource service puppet ensure=stopped

Poistin kansion SSL
rm -r /etc/puppetlabs/puppet/ssl

Käynnistin Puppet -palvelun uudestaan
puppet resource service puppet ensure=running

Ajoin komennon
Puppet agent -t

ja lopulta ajoin palvelimella komennon sudo puppet cert list, joka näytti sertifikaatit.
