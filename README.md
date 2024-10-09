# TP_2_UNIX

## Secure Shell: SSH
### Connection SSH root

Ici on se relance un serveur ssh sur la VM avec `systemctl restart ssh`et on vérifie `systemctl status ssh`. 
Ensuite on veut changer la configuration du serveur pour permettre les connections à distance de root (avec mot de passe évidemment). 

Pour se faire on va juste modifier dans le fichier /etc/ssh/sshd_config la ligne `PermitRootLogin` et y mettre tout de suite après `yes`. 

Il existe 4 options de configuration différentes de `PermitRootLogin`:
  -prohibited-password: désactive la connexion par mot de passe de root au serveur ssh
  -yes: autorise la connexion avec mot de passe
  -no: n'autorise pas la connexion 
  -forced-commands-only: les connexions de root avec une clé public sont autorisés (utiles si on veut récuperer des sauvegardes à distance si root n'est pas autorisé à la base). Toutes autres méthodes est désactivé.

### Authentification par clef / Génération de clefs

Pour générer une paire de clé public privé, il faut taper dans le terminal de la machine hôte la commande ` ssh-keygen -t rsa `, elles sont générées et on nous demande un passphrase, bien entendu dans un cas réel il serait plus judicieux d'en mettre une, parce que si quelqu'un réussit à avoir accès à la clé privée d'un serveur il peut avoir accès à absolument tout les machines qui sont liés. 

Ensuite on crée le fichier "authorized_keys" qui n'existe pas, le dossier ssh existe déjà mais on aurait utilisé `mkdir` pour le créer sinon, ensuite on va ajouter la clé dans le fichier grâce à la commande `cat>authorized_keys`.
Puis on donne uniquement les droits avec la commande `chmod 700 authorized_keys`. 

### Authentification depuis la machine hôte

On se connecte avec la commande `ssh -i id_rsa.pub root@10.20.0.137`spécifique à la machine. 

### Sécurisation 

On crée une copie de ma cmé avec `ssh-copy-id root@10.20.0.137`

ensuite on reconfigure le fichier sshd_config et on remet `PermitRootLogin no` au lieu de yes. 


