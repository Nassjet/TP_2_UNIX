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

1. Voici ce que donne la commande `ps`:

![image](https://github.com/user-attachments/assets/cca8f707-321a-4770-b45c-af3f80881cd5)

La commande ps affiche les processus en cours avec des informations supplémentaires dont TIME qui indique le temps cumulé du processeur. 
Le processus lancé après le démarrage est systemmd, parce que c'est le premier qu'on retrouve en tapant la commande `ps -e`: 

![image](https://github.com/user-attachments/assets/c9008e24-85b9-4715-ac8d-1afcabe17e1d)

Voici la commande permettant de savoir depuis quand mon serveur tourne 


Le processus qui est le plus utilisé par le processeur est la commande en elle-même parce qu'on quand on regarde dans la colonne %CPU soit le pourcentage que chaque processus utilise dans le CPU, tout les processus sont à 0% sauf la commande elle-même. 

Voici le nombre de processus: 

![image](https://github.com/user-attachments/assets/b92d0829-d46b-4a42-baf6-402f5f73ba2e)

Bon finalement je viens d'apprendre que ps, c'est les processus en cours, donc pas tout ceux depuis le démarrage, finalement lorsqu'on fait ps, c'est bien le dernier PID affiché qui compte, ainsi il y a 1188 processus depuis le début.

![image](https://github.com/user-attachments/assets/5dc00d61-5da6-453b-bd7d-ab053dab21f8)


2. grâce à ce lien :
    [Lien](https://www.funix.org/fr/unix/process.htm)
On trouve la commande `ps -ef` qui affiche le ppid de chaque pid. 

4. On essaie d'installer le pstree avec `apt search pstree`, il me répond que c'est pas le bon dossier : 

![image](https://github.com/user-attachments/assets/52fc173f-e477-4d08-a224-25100d15178d)

Donc, il faut faire `apt search psmisc` puis `apt install psmisc`

Ensuite pour trier le top en fonction du pourcentage d'utilisation de la mémoire c'est `top -o %MEM` [Lien](https://www.malekal.com/top-lister-processus-linux/)

Le processus le plus gourmand est systemmd aussi bien en terme de cpu que mémoire.  

![image](https://github.com/user-attachments/assets/f4697f6f-130c-4981-8c74-e147227df788)

Pour changer la couleur dans top il faut aller dans top et taper la touche "z" : 
[Lien](https://www.malekal.com/top-lister-processus-linux/#Comment_changer_les_couleurs)

Htop c'est plus beau déjà, ensuite il y a moins de lignes, ensuite il y a un menu qui peut aider en bas :

![image](https://github.com/user-attachments/assets/64234467-8a2a-49db-b3f9-789d7af98ed4)


### Exercice 2 : Arrêt d'un processus

Ici on rajoute les programmes grâce à la commande `nano date-sh` puis on les exécute avec juste `./` devant le nom du programme, ils s'executent mais il faut les mettre en arrière-plan avec ctrl-Z sinon on ne peut plus rien faire. 
Ensuite on regarde les scripts en cours avec la commande job, comme on a mit les scripts en arrière plan, on les ramene au premier plan avec `fg %1` fg pour "foreground" et le %1 pour le numéro du processus qu'on veut arreter, et là il faut juste faire Ctrl+C . 

Ensuite on essaie de faire la même chose avec `ps` et `kill`, ça donne `ps aux | grep date` pour récupérer le PID et ensuite on execute `kill *PID*` en mettant le PID voulu pour arrêter un script. 

Selon le man de sh, ./bin/sh permet d'indiquer au script qu'il doit être executé avec le script sh. Le `while` crée une boucle infinie, qui va dans un premier temps faire une pause de 1 seconde avec `sleep 1`. 
Puis va afficher avec `echo` et enfin affiche la date actuelle, tout ça à l'infini, tant qu'on arrête pas le programme. 

### Exercice 3 : les tubes 

`ls | cat`: liste les scripts
`ls -l | cat > liste`: redirige tout les fichiers afficher par ls dans un fichier liste. 
`ls -l  | tee liste`: affiche les droits des d'écriture de lecture et de suppresion. 
`ls -l | tee liste | wc -l`: compte le nombre de scripts qui existent dans le fichier liste. 

### Exercie 4: 

Encore une fois, on cherche le package rsyslog puis on l'installe avec les commandes `apt`, ensuite on lance le service avec `systemctl restart rsyslog` puis on vérifie qu'il est bien lancé avec `systemctl status rsyslog`. 

On trouve grâce au statut la ligne du PID démon : 

![image](https://github.com/user-attachments/assets/54ecf769-2264-4616-b095-96f5c3e080ef)

Les messages standards sont écrits dans le fichier `/var/log/syslog`. On vérifie et il y a beaucoup de messages. 

Le service "cron" permet d'exécuter des tâches planifiées à des intervalles réguliers. Les tâches sont définies dans des fichiers appelés crontabs, où on peut spécifier des commandes à exécuter à des horaires précis.

La commande `tail -f` permet de suivre un fichier en temps réel, affichant les nouvelles lignes au fur et à mesure qu'elles sont ajoutées. 

Le fichier `/etc/logrotate.conf` est un fichier de configuration pour le service logrotate, qui gère la rotation des fichiers de journalisation (logs) sur un système Linux.
