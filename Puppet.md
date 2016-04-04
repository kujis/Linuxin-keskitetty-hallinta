#Hello Puppet -kotitehtävä


Asensin puppet agentin komennoilla: 
sudo apt-get update
sudo apt-get install puppet

Tämän jälkeen loin tiedoston nimeltä "helloPuppet kansioon tmp ja kirjoitin sisällöksi "Hello Henry" seuraavalla komennolla:
puppet apply -e 'file { "/tmp/helloPuppet": content => "Hello Henry!\n" }'

Tarkistin, että tiedosto on luotu komennolla: ls /tmp/helloPuppet ja sen jälkeen avasin sen nano tmp/helloPuppet komennolla tarkistaakseni että lisäämäni teksti oli siellä.

Loin kansion puppet komennolla: mkdir puppet, tämän jälkeen loin tiedosto polun mkdir -p modules/hello/manifests/ ja sinne tiedoston init.pp komennolla: nano modules/hello/manifests/init.pp.
Lisäsin init.pp -tiedostoon alla olevan koodiin pätkän:

class hello {
        file { '/tmp/helloModule':
            content => "See you at TeroKarvinen.com!\n"
        }
}

Tämän jälkeen ajoin luomani moduulin komennolla: puppet apply --modulepath modules/ -e 'class {"hello":}'

Ensimmäisellä kerralla en onnistunut moduulin ajossa, sillä olin tehnyt kirjoitus virheen ja luonut manifests kansion sijaan kansion 'manifest'. Sain seuraavan virheilmoituksen: 
"Error: Puppet::Parser::AST::Resource failed with error ArgumentError: Could not find declared class hello at line 1 on node xubuntu.tielab.haaga-helia.fi
Wrapped exception:
Could not find declared class hello
Error: Puppet::Parser::AST::Resource failed with error ArgumentError: Could not find declared class hello at line 1 on node xubuntu.tielab.haaga-helia.fi"

Kun korjasin tekemäni kirjoitusvirheen sain komennon ajon jälkeen ilmoituksen: 
Notice: Compiled catalog for xubuntu.tielab.haaga-helia.fi in environment production in 0.04 seconds
Notice: /Stage[main]/Hello/File[/tmp/helloModule]/ensure: defined content as '{md5}e2a0fbdfba4d5df59a94fa5611929aad'
Notice: Finished catalog run in 0.02 seconds

Lopulta tarkastin vielä luomani tiedoston komennoilla: ls /tmp/helloModule ja cat /tmp/helloModule
