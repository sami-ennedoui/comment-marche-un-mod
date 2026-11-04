# Comment marche un mod

Je voulais jouer une carrière qui part de rien, dans un petit club nordique. FC 27 n'a pas ces divisions, alors j'ai regardé ce qu'il faudrait changer pour les ajouter. Cette question m'a surtout appris comment un jeu est construit.

Ce dépôt résume ce que j'ai compris, pour le stream Toulouse IA du 5 novembre 2026. Il ne contient ni le mod, ni fichier du jeu, ni code.

![Une tribune générée par un script à partir de la carte du quartier](images/tribune.jpg)

## Un jeu, c'est surtout des données

Le moteur fait tourner le jeu, mais presque tout ce qu'on voit à l'écran vient de fichiers de données. Un mod change ces données et touche rarement au code. On peut donc partir d'une question simple : dans quel fichier vit ce qui m'agace ?

| Ce qui m'agace | Où ça vit | Ce qu'un mod peut faire |
|---|---|---|
| Mon championnat n'existe pas | Des tables de clubs et de joueurs, des fichiers texte pour les compétitions | Ajouter des lignes et des règles |
| Tous les stades se ressemblent | Des modèles 3D et des textures | Remplacer des pièces |
| Le speaker ne connaît pas mon club | Des banques de sons et une table qui choisit la phrase | Ajouter ou remplacer des enregistrements |
| Les petits clubs ont de mauvais maillots | Des textures | Les remplacer |
| Je suis un inconnu et j'ai déjà des milliers de likes | Je n'ai pas cherché | Rien, tout ce qui agace ne mérite pas un mod |

## Le chemin d'un mod

1. **Ouvrir.** Les fichiers d'un jeu sont rangés dans de grosses archives. Des outils écrits par des passionnés, comme FIFA Editor Tool pour FC 27, savent les ouvrir et sortir leur contenu dans des formats courants.
2. **Lire.** Avant de changer quoi que ce soit, on regarde comment le jeu se sert de ces fichiers. C'est souvent là qu'on comprend pourquoi quelque chose nous agaçait.
3. **Prouver qu'on ne casse rien.** On relit un fichier, on le réécrit sans rien changer et on compare. Si les deux versions diffèrent, on ne comprend pas encore le format.
4. **Modifier.** On travaille ensuite avec des outils ordinaires : Blender pour la 3D, un script pour les tables, un modèle de synthèse vocale pour les voix.
5. **Réimporter.** L'outil produit un petit fichier qui contient seulement ce qui change. En général, un chargeur applique ce fichier au lancement et les fichiers d'origine restent en place.
6. **Tester dans le jeu.** L'éditeur peut accepter un fichier que le jeu refusera ensuite. Seul le jeu peut dire si ça marche.

## Le pipeline

Je ne vais pas saisir des milliers de joueurs à la main, donc un programme doit construire le mod à partir de données. On doit aussi pouvoir le relancer à tout moment. Voici le plan, et la dernière section dit ce qui existe vraiment.

1. **Décrire les ligues.** Un fichier de configuration décrit les divisions à ajouter et leurs clubs.
2. **Récupérer les joueurs.** Les joueurs viennent de la base d'un jeu de gestion de football, beaucoup plus complète sur les petites divisions. Ceux qui existent déjà dans FC 27 ne sont pas ajoutés une deuxième fois.
3. **Convertir les notes.** Les deux jeux ne notent pas les joueurs de la même façon. Un modèle apprend la correspondance sur les joueurs présents dans les deux, puis il l'applique aux autres. On mesure son erreur sur des joueurs qu'il n'a pas vus, et chaque division doit rester nettement en dessous de celle du dessus.
4. **Habiller les clubs.** Un script interroge l'API de Wikipedia. Il part de la page de la saison de chaque ligue, suit les liens vers les clubs, puis lit dans le code de chaque page les couleurs et les motifs du maillot. Il a déjà récupéré 66 clubs. Ces pages peuvent dater, et rien n'a encore été appliqué à un maillot du jeu.
5. **Écrire les compétitions.** Le programme génère les ligues et une petite coupe nordique. Il ne touche pas aux ligues existantes, sauf pour les relier aux nouvelles par les montées et les descentes.
6. **Assembler et tester.** Tout finit dans un seul mod, et on vérifie qu'une carrière tient sur plusieurs saisons.

## Les deux bonus

Ces deux idées ne bloquent rien, car le mod peut exister sans elles.

### Des stades qui ne se ressemblent plus

Les petits clubs jouent dans des décors génériques que beaucoup d'autres clubs partagent. L'idée est de générer un stade pour un club à partir de la carte réelle.

Un script interroge Overpass, l'API d'OpenStreetMap, et récupère les bâtiments, les routes, les arbres, les tribunes et les mâts autour du stade. Il convertit les coordonnées en mètres autour du rond central, dessine les textures, puis construit le tout dans Blender en moins d'une minute. Changer de stade revient à changer quelques identifiants.

![Le stade et son quartier, construits à partir de la carte](images/quartier.jpg)

La carte donne les positions. Le script estime les hauteurs, les gradins et les toits, donc le résultat est plausible et faux dans le détail. Cette méthode dépend aussi de la carte : sur six stades danois essayés, trois étaient assez bien dessinés. Ce modèle n'existe que dans Blender. Il n'a jamais été chargé dans le jeu, et ce passage reste à prouver.

### Des voix nordiques

Dans les petits stades, le speaker parle anglais et ne nomme pas le club. Seuls certains clubs ont des annonces à leur nom, et les autres reçoivent une phrase générique.

L'idée est de générer des annonces en danois, en suédois, en norvégien et en finnois avec une synthèse vocale open source, puis de les faire jouer à la place de la voix anglaise. Ces annonces pourraient aussi nommer le club. Personne ne les a encore entendues dans le jeu.

## Le rôle de l'IA

Claude Code a écrit presque tout le code et mené les essais décrits ici. Il va vite, et il annonce ses erreurs sur le même ton que ses réussites. Il m'a par exemple assuré que FIFA Editor Tool ne tournait que sous Windows, et c'était faux : l'outil tourne aussi sous Wine.

Mon travail consiste à choisir ce qui m'agace, à limiter le projet et à demander des preuves. Mon intervention la plus utile tient en trois mots : "are you sure??". Chaque étape écrit donc un fichier qu'on peut ouvrir et lire, et un résultat compte seulement s'il a tourné sur de vraies données.

## Où ça en est

Au 4 novembre 2026, rien de tout cela n'a tourné dans le jeu. Les premières vérifications sur les vrais fichiers passent, mais elles ne prouvent pas que le jeu acceptera une nouvelle ligue. Le test décisif reste à faire : une ligue factice dans une vraie carrière. La conversion des notes n'est pas écrite non plus, et les deux bonus restent des essais.

Le mod reste un projet personnel, pour une carrière hors ligne, et je ne le publie pas.

---

Données cartographiques © les contributeurs d'OpenStreetMap, licence ODbL : https://www.openstreetmap.org/copyright. Couleurs des clubs : Wikipedia, CC BY-SA. Les images sont des rendus Blender et ne contiennent aucun élément du jeu. Aucun club n'est associé à ce projet.
