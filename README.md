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

On crée une copie de la clé avec `ssh-copy-id root@10.20.0.137`: ![image](https://github.com/user-attachments/assets/ecc5dfef-4b47-454a-98d8-6eb4e6d270ad) voici une explication en anglais de comment fonctionne la clé. 


ensuite on reconfigure le fichier sshd_config et on remet `PermitRootLogin no` au lieu de yes. 

## Processus

### Etude des processus UNIX 

Voici ce que donne la commande `ps`:

![image](https://github.com/user-attachments/assets/cca8f707-321a-4770-b45c-af3f80881cd5)

La commande ps affiche les processus en cours avec des informations supplémentaires dont TIME qui indique le temps cumulé du processeur. 
Le processus lancé après le démarrage est systemmd, parce que c'est le premier qu'on retrouve en tapant la commande `ps -e`: 

![image](https://github.com/user-attachments/assets/c9008e24-85b9-4715-ac8d-1afcabe17e1d)

Voici la commande permettant de savoir depuis quand mon serveur tourne 


Le processus qui est le plus utilisé par le processeur est la commande en elle-même parce qu'on quand on regarde dans la colonne %CPU soit le pourcentage que chaque processus utilise dans le CPU, tout les processus sont à 0% sauf la commande elle-même. 




