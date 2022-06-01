# Mini-Projet microcontrôleur STM32 : Fighting Game & Interaction entre deux modules BT 2.0

Ce mini projet est composé de deux parties. La première consiste en un "fighting game" contre la carte microcontrôleur(GAME_mini_Projet_vs_carte). 
La seconde consiste au même "fighting game" entre deux cartes microcontrôleurs en liaison série (UART7) ou en intéraction entre 2 modules Bleutooth 2.0 pour jouer à 2.

Le dépôt git comporte donc 3 projets STM32, le premier "GAME_mini_Projet_vs_carte" est à téléverser sur une carte microcontrôleur STM32 pour jouer contre la carte. Les deux autres projets STM32 sont à téléverser sur deux cartes microcontrôleurs dans la perspective de jouer à 2.

L'objectif de ce mini-projet est de prendre en main le système d'exploitation temps réel FreeRTOS sur la carte microcontrôleur STM32 pour réaliser une application complexe mettant en jeu les différentes notions explorées au cours des séances cours/TP ainsi qu'une fonctionnalité ou un périphérique non étudié de la carte STM32. Il s'agit ici de l'intéraction entre deux cartes microcontrôleur par liaison série UART7 puis par l'intermédiaire de deux modules Bluetooth 2.0.

Le principe du jeu développé dans ce mini-projet consiste en deux joeurs qui se déplacent horizontalement sur l'écran de la carte microcontrôleur STM32 à l'aide du Joystick et jettent des missiles dirigeables pour attaquer chacun son rival. Le premier joueur qui arrive à frapper l'autre est déclarer gagnant de la partie ainsi jouée. La version finale du jeu permet à deux joueurs de se confronte à distance grâce à l'exploitation de deux modules Bluetooth RN42 sur la liaison UART7 de la carte STM32, la distance entre les deux cartespeu atteindre 10m ce qui assure au joueur suffisement de confort pour pouvoir se concentrer sur la partie en cours.

Nous détaillerons dans la suite le principe du jeu implimenté (tâches, messageries, Mutexes, ...), la mise en ouevre du périphérique UART7, ainsi que la configuration des modules RN42 pour réaliser la communication Bluetooth entre les deux cartes microcontrôleur dans la partie à deux joueurs.

## Principe du jeu "FIghting Game" :

## Fonctionnement des programmes : 

## Présentation et utilisation de liaison série UART7 : 

## Présentation et utilisation des modules bluetooth RN42 :

 
