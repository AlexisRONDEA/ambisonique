#	  Projet : Ambisonique
  
Collaborateurs : RONDEAU Alexis, SOULE Zeïck, BODET Amaury, VAN DE MOOSDIJK Martina, MILLASSEAU Adrien, LATHUILLERE Colin

###	I / Introduction
	
Ce projet, initialement conçu par des troisièmes années, permet de connecter huit enceintes pour délivrer un son provenant de différentes directions.
Nos objectifs concernent l’encombrement et la qualité sonore de l’amplificateur audio. En effet, les enceintes sont pour l’instant alimentées par des alimentations de laboratoire, ce qui est peu pratique et prend de la place. De plus, le signal audio est transmis par un seul fil. Cette liaison asymétrique est la source de parasite sur le signal et donne lieu à une mauvaise qualité sonore. 
On se propose donc de remplacer la transmission du signal par une liaison symétrique différentielle, pour supprimer le plus de parasites possible. On change l’amplificateur actuel par un amplificateur de classe D pour améliorer la qualité sonore du signal en sortie. Enfin, on fait passer l’alimentation des enceintes par le câble transmettant le signal audio, pour alimenter les enceintes avec le réseau.
Pour cela on sépare ce projet en quatre modules et en deux PCB. Les deux premiers appartiendront au premier et le séparateur avec l’ampli audio seront sur le deuxième PCB.

Objectifs : 
- Créer un module modifiant le courant d'entrée du réseau 230V AC en 24V DC
- Créer un module qui additionne la partie continue de 24V au signal
- Créer un module qui sépare le signal et la puissance pour alimenter les enceintes
- Créer l’amplificateur de classe D
 
Le [cahier des charges](https://github.com/AlexisRONDEA/ambisonique/blob/main/livrables/Cahier%20des%20Charges%20-%20Ambisonique%20%5B2%5D.pdf), le [diagramme d’architecture](https://github.com/AlexisRONDEA/ambisonique/blob/main/livrables/Diagramme%20Architecture%20-%20Ambisonique%20%5B2%5D.pdf), la [présentation PDF](https://github.com/AlexisRONDEA/ambisonique/blob/main/livrables/Diagramme%20Architecture%20-%20Ambisonique%20%5B2%5D.pdf) du projet ainsi que les [commandes des composants](https://github.com/AlexisRONDEA/ambisonique/tree/main/livrables) se trouvent dans le dossier « [livrables](https://github.com/AlexisRONDEA/ambisonique/tree/main/livrables) ».

###	II / Alimentation du circuit

Pour ce module on utilise les composants suivants :
-	Un connecteur MPT 0.5/2-2.54
-	Un transformateur PCB, 230V, 2 * 24V
-	Un pont redresseur monophasé
-	Un contrôleur DC/DC, synchrone, Buck, output 24V

Le transformateur permet de modifier la tension d’entrée du réseau de 230V alternatif en 24V alternatif. Le courant est ensuite envoyé dans le pont de diode redresseur qui renvoie une tension de 33V continu en sortie. Enfin, le contrôleur Buck abaisse la tension à 24V continu pour l’envoyer dans le module additionneur.

Dans un premier temps, pour créer ce module on voulait utiliser un montage avec un régulateur de tension. Cependant, ce circuit était assez vieux et donc pas assez performant par rapport à d’autres montages envisageables. Les pertes étaient trop importantes et c’est pour cela que nous avons décidé d’opter pour ce circuit actuel. 
De plus, notre circuit actuel devait utiliser les deux branches de sortie du transformateur qui fournit du 2x24V, car une fois 24V n’aurait pas était suffisant. Cependant la tension de sortie correspond à un courant alternatif, qui est donc redresser par le pont de diode ensuite à 33V continu. C’est pourquoi on n’utilise qu’une seule branche de sortie du transformateur.  

 Ce module fait partie du premier PCB, les fichiers utiles au PCB sont donc ambisoniqueAlim.sch et ambisoniqueAlim.brd. Les empreintes du contrôleur Buck et du transformateur ont été créé pour Eagle tandis que l’empreinte du pont redresseur a été téléchargée sur internet. 


###	III / Additionneur

Pour ce module, on utilise les composants suivants : 

-	Deux transistors de puissance NPN TIP31A 
-	Deux connecteurs Jack 

On réalise un montage à collecteur commun en utilisant le TIP31A. Il permet d'obtenir en sortie, au niveau de l'émetteur et dans le même fil, le signal audio ainsi que les 24 V DC obtenus dans le module précédent. On assure la transmission par un seul câble XLR. On souhaite également avoir un courant de 1.5 A en sortie du transistor, d'où le choix d'un transistor de puissance pour réaliser ce montage. Enfin, on réalise ce montage deux fois, avec un signal audio positif puis négatif afin de pouvoir les soustraire et travailler en différentiel. L'objectif du différentiel est d'éviter les perturbations électromagnétiques, qui se répercutent sur les deux signaux. En les soustrayant, on efface alors les parasites. On souhaite également éviter d'impacter la valeur continue, en choisissant judicieusement la valeur des résistances. 

Nous avons pensé à réaliser ce module en utilisant un schéma additionneur avec un amplificateur opérationnel, néanmoins cela aurait été compliqué car il nous fallait avoir un courant conséquent en sortie, ce qui n'est pas possible avec un AOP. 

Ce module fait partie du premier PCB, les fichiers utiles au PCB sont donc "ambisoniqueAlim.sch" et "ambisoniqueAlim.brd".


 ###	IV / Séparateur 
 
Pour ce module on utilise les composants suivants :
-	Deux contrôleurs DC/DC, synchrone, Buck, output 12V

Pour ce module, on utilise des condensateurs. On utilise leur caractéristique de filtre pour séparer la composante continue et le signal. On souhaite séparer la composante continue afin de l'utiliser en tant qu'alimentation pour les enceintes, et d'utiliser la composante alternative pour l'amplifier dans le module suivant. Les deux câbles de puissance rentrent alors dans les contrôleurs Buck qui renvoient une tension de 12V. On fait se rejoindre les deux câbles, puis on les sépare pour alimenter le microprocesseur et les MOSFET. En effet, les MOSFET sont directement alimentés en 12V, tandis que la tension d’entrée du microprocesseur passe par un régulateur avant d’alimenter le microprocesseur en 3,3V. 

Pour ce module, on a dû recommander les contrôleurs Buck. En effet, ceux commandés ne pouvait que donner du 5V en sortie contrairement aux 12V voulus.

Ce module fait partie du second PCB, les fichiers utiles au PCB sont donc "ambisoniqueAmpli.sch" et "ambisoniqueAmpli.sch".

###	V / Amplificateur audio

Pour ce module on utilise les composants suivants :
-	Un microcontrôleur STM32L412KBT6
-	Un MOSFET de puissance, Canal N, 20 V, 3.9 A, 0.025 Ohm
-	Un MOSFET de puissance, Canal P, 20 V, 3.2 A, 0.08 Ohm

L’objectif de cette partie du projet est de remplacer l’amplificateur audio déjà existant en le substituant par un microprocesseur. Ce dernier permet de créer une PWM qui rentre ensuite dans un amplificateur de classe D composé des deux MOSFET de puissance. Pour ce faire, on alimente le microprocesseur avec du 3,3V venant du module séparateur et les MOSFET en 12V. Le microprocesseur récupère le signal différentiel en entrée et le traite, pour retourner un signal simple avec le moins de parasites possible. Ensuite, le signal est comparé à un signal triangle, modélisé par un compteur TIMER sur le microprocesseur, pour générer la PWM. 

Nous voulions d’abord utiliser d’autres circuits, avec d’autres types d’amplificateur. Cependant, cet amplificateur de classe D est le plus performant. 
De plus, la programmation du microprocesseur n’est pas terminée.

Ce module fait partie du second PCB, les fichiers utiles au PCB sont donc "ambisoniqueAmpli.sch" et "ambisoniqueAmpli.sch". Les empreintes des MOSFET viennent d’internet, et celle du microprocesseur nous a été fournie en amont.
