#Orjien ohjaaminen verkon yli

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

puppet agent -t - komento tarkistaa onko palvelimella tapahtunut muutoksia.
