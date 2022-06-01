# Mini-Projet microcontrôleur STM32 : Fighting Game & Interaction entre deux modules BT 2.0

Ce mini projet est composé de deux parties. La première consiste en un "fighting game" contre la carte microcontrôleur(GAME_mini_Projet_vs_carte). 
La seconde consiste au même "fighting game" entre deux cartes microcontrôleurs communiquant en liaison série (UART7) ou en intéraction entre 2 modules Bleutooth 2.0 pour jouer à deux.

Le dépôt git comporte donc 3 projets STM32, le premier "GAME_mini_Projet_vs_carte" est à téléverser sur une carte microcontrôleur STM32 pour jouer contre la carte. Les deux autres projets STM32 sont à téléverser sur deux cartes microcontrôleurs dans la perspective de jouer à 2.

L'objectif de ce mini-projet est de prendre en main le système d'exploitation temps réel FreeRTOS sur la carte microcontrôleur STM32 pour réaliser une application complexe mettant en jeu les différentes notions explorées au cours des séances cours/TP ainsi qu'une fonctionnalité ou un périphérique non étudié de la carte STM32. Il s'agit ici de l'intéraction entre deux cartes microcontrôleur par liaison série UART7 puis par l'intermédiaire de deux modules Bluetooth 2.0.

Le principe du jeu développé dans ce mini-projet consiste en deux joeurs qui se déplacent horizontalement sur l'écran de la carte microcontrôleur STM32 à l'aide du Joystick et jettent des missiles dirigeables pour attaquer chacun son rival. Le premier joueur qui arrive à frapper l'autre est déclarer gagnant de la partie ainsi jouée. La version finale du jeu permet à deux joueurs de se confronter à distance grâce à l'exploitation de deux modules Bluetooth RN42 sur la liaison UART7 de la carte STM32, la distance entre les deux cartes peut atteindre $10m$ ce qui assure aux joueurs suffisement de confort pour pouvoir se concentrer sur la partie en cours.

Nous détaillerons dans la suite le principe du jeu implimenté (tâches, messageries, Mutexes, ...), la mise en ouevre du périphérique UART7, ainsi que la configuration des modules RN42 pour réaliser la communication Bluetooth entre les deux cartes microcontrôleurs dans la partie à deux joueurs.

## Principe du jeu "FIghting Game" et fonctionnement des programmes :

Comme expliqué plus haut, le principe du jeu consiste en deux joueurs qui se battre par missiles et se déplace à l'aide du joystick pour éviter les missiles envoyés par l'adversaire. Pour tirer un missile il faut maintenir appuier le bouton BP1 de la carte microcontrôleur. Se missile se déplacera donc verticalement tant que le BP1 est maintenu appuié et prend comme position horizontale celle du joueur émetteur, il s'agit donc d'un missile orientable pour mieux attaquer le joueur adversaire.

Une vidéo de démonstration dans le cas de jeu contre carte microcontrôleur STM32 est disponible sur le lien suivant : https://www.youtube.com/shorts/jmvjBglkAN0

### Tâches :

Pour cette première version du jeu, le programme est composé de 4 tâches (+ defaultTASK) : 
- **Tâche diable** : Elle s'occupe du déplacement horizontale du diable en haut de l'acran et de l'envoie périodique de virus (ou bompe) pour attaquer le joueur.
- **Tâche Homme** : Elle permet au joueur de se déplacer en bas de l'écran à l'aide du Joystick pour éviter les virus envoyés par le diable et d'envoyer des missiles pour attaquer le diable en appuyant sur le bouton BP1.
- **Tâche virus** : Elle s'occupe des déplacements des missiles envoyés par le joueur ou par le diable sur l'écran et vérifie les collisions.  
- **Tâche Game_Over** : Détermine le vaiqueur de la partie jouée et détruit la tâche Homme (resp. diable) si le joueur perd (resp. gagne).

### Messagerie entre tâches :

Les tâches **diable** et **Homme** communiquent les positions des joeurs et des missiles avec la tâche **virus** en utilisant les 3 Queues *myQueueP2V*,*myQueueU2H* et *myQueueHommePos*. La tâche **virus** vérifie les collisions et communique avec la tâche **Game_Over** via les Queues *myQueue_Game_OverHandle* et *myQueue_I_WINHandle*.

### Partage de ressource : 

Les trois tâches **diable**, **Homme** et **Game_over** partagent la ressource *écran LCD*. Ainsi, un *Mutex* est utilisé pour passer des anomalies d'affichage. Lorsqu'une tâche s'accapare le mutex, elle ne peut pas être interrompue par une tâche utilisant le même mutex avant la libération de la ressource, ce qui garantie un affichage cohérent sur l'écran LCD.

### Fonction d'interruption :

Une interruption sur le *BP2* de la carte STM32 est utilisé pour modifier la complexité du jeu contre carte en augmentant la vitesse de déplacement du **diable** ainsi que celle des **virus** qu'il envoie. Elle communique avec les autres tâches par *varibale globale*.

## Présentation et utilisation de liaison série UART7 : 

La version à deux joueurs du **Fighting Game** utilise les mêmes tâches que dans la version à un joueur (contre carte). Néanmoins quelques modifications ont été nécessaires pour que le jeu fonctionne en interaction entre deux cartes microcontrôleurs. Deux projet STM32 ont été créés, *MAST1* et *MAST2*.

### Programme MAST1 :

Dans ce programme, 5 tâches ont été créées (+ **defaultTask**) : 

- **Tâche diable** : Elle reçoit la position courante du joueur *diable* par liaison série ou Bluetooth et affiche sur l'écran. La tâche correspondante dans le projet **MAST2** s'occupe du déplacement réel du diable à l'aide du Joystick.
- **Tâche Homme** : Elle permet au joueur de se déplacer en bas de l'écran à l'aide du Joystick pour éviter les virus envoyés par le diable et d'envoyer des missiles pour attaquer le diable en appuyant sur le bouton BP1.
- **Tache Task_Transmit_data** : Envoie les données par liaison série ou par Bluetooth.
- **Tâche missiles** : Elle s'occupe des déplacements des missiles envoyés par les deux joueurs sur l'écran et vérifie les collisions.  
- **Tâche Game_Over** : Détermine le vaiqueur de la partie jouée et détruit la tâche Homme (resp. diable) si le joueur perd (resp. gagne).

Dans ce projet on trouve en plus la fonction *HAL_UART_RxCpltCallback* dédiée à la réception de données envoyées par l'autre carte en liaison série ou en Bluetooth.

### Programme MAST2 : 

Dans ce programme, les mêmes tâches qu'en **MAST1** ont été définies. L'unique différence réside dans le fait que cette fois la **tâche Homme** reçoit la position du joueur *Homme* par liaison série ou par Bluetooth et ne fait qu'afficher l'*Homme* sur l'écran, alors que la **tâche diable** s'occupe du déplacement du joueur *diable* sur l'écran LCD à l'aide du Joystick et de l'emission de missiles de sa part.

Une vidéo de démonstration est disponible sur le lien suivant : https://www.youtube.com/shorts/ZzL9vxEpnPE

## Présentation et utilisation des modules bluetooth RN42 :

Dans cette version du jeu, notre objectif est de remplacer les fils utilisés pour réaliser la liaision série UART7 exploitée dans la version précédente par des modules Bluetooth RN42. Les programmes sont donc les mêmes que précédement.



**En cours**

 [https://www.youtube.com/shorts/jmvjBglkAN0](https://www.youtube.com/watch?v=-zqxK5wloWI)
 
 ## Conclusion :
 
