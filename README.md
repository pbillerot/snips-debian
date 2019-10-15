# snips-metro
Assistant Snips en Python3 pour Debian Buster

## Installation de Debian Buster dans une VM VirtualBox

- Installation minimum sans interface graphique
- en ssh 
```
    su -
    apt install sudo
    adduser <user> sudo
    apt install tree mc htop git virtualenv alsa-utils
    reboot
```

## Installation de Snips

### Dépendances
```bash
sudo apt-get update
sudo apt-get install -y dirmngr apt-transport-https
sudo bash -c  'echo "deb https://debian.snips.ai/stretch stable main" > /etc/apt/sources.list.d/snips.list'
sudo apt-key adv --keyserver pgp.mit.edu --recv-keys F727C778CCB0A455

sudo apt-get update
sudo apt-get install -y snips-platform-voice
sudo apt-get install -y snips-platform-demo
sudo apt-get install -y snips-watch
sudo apt-get install -y snips-template
sudo apt-get install -y snips-skill-server
```
A l'issue de cette installation, on retrouve les services suivants :

    sudo systemctl | grep snips
```
snips-asr.service               loaded activating auto-restart Snips ASR
snips-audio-server.service      loaded active     running      Snips Audio Server
snips-dialogue.service          loaded activating auto-restart Snips Dialogue
snips-hotword.service           loaded active     running      Snips Hotword
snips-injection.service         loaded activating auto-restart Snips Injection
snips-nlu.service               loaded activating auto-restart Snips NLU
snips-skill-server.service      loaded active     running      Snips Skill Server
snips-tts.service               loaded active     running      Snips TTS 
```

### Installation de l'assistant 
(le répertoire est accessible en rwd par $USER par défaut)

    /usr/share/snips/assistant/
        assistant.json
        ...

Le paquet ```snips-platform-demo``` a installé l'assistant de démo dans ce répertoire. Il faut le renommer en ```assistant_default``` par exemple pour le remplacer par le notre

Télécharger l'assistant à partir de la console Snips https://console.snips.ai/
et recopier le répertoire assitant sous ```/usr/share/snips/```

### Installation des skills
Pour ma part j'ai installé le projet Github sous ce répertoire en tant que propriétaire du répertoire

```sudo chown $USER /var/lib/snips/skills```

    /var/lib/snips/skills/<projet>/
        action-<nom action>.py
        ...

J'utilise **VSCodium** pour éditer le projet en sftp (sftp://user@ip)

__Création de l'environnement virtuel python__

Se positionner sous ```/var/lib/snips/skills/<projet>/```
    virtualenv -p python3 venv
    source venv/bin/activate
    pip install hermes-python requests configparser
    cp config.ini.default config.ini

Pour VSCodium
    pip install pylint rope

### Initialisation de l'assistant

    # Des problèmes pour faire fonctionner ces process en tant que service
    # Je les arrête
    sudo systemctl disable snips-audio-server.service
    sudo systemctl disable snips-skill-server.service
    sudo systemctl stop snips-audio-server.service
    sudo systemctl stop snips-skill-server.service

Vérifier que tous les autres services snips sont OK

    sudo systemctl status snips-*

### Test Audio

- Vérifier qu'une carte son a bien été trouvée

    ```cat /proc/asound/cards```

- Générer un son synthétique

    ```speaker-test -Dplug:default -c2```

- Enregistrer le microphone

    ```arecord -d 5 -f S16_LE -r 44100 test-mic.wav```

## Test
```
```
```
# Dans 3 terminaux différents
snips-watch --mqtt -vv
snips-skill-server -vvv
snips-audio-server
```
