# Apprentissage supervisé d’une attaque d’un serveur Web

Par Florian Blanchet, Guillaume Michonneau, Changyi Song et Sili Zuo, élèves du Parcours Data Science de l’IMT Atlantique. 

Aujourd’hui, avec le développement du web et des nouvelles technologies telles que le cloud ou l’Internet des objets, la sécurité informatique est devenue un sujet majeur. Ceci d’autant plus que ces derniers mois ont vu apparaître dans la sphère publique de nouveaux types d’attaques telles que le malware Wannacry, des attaques par déni de service sur des plateformes reconnues (Amazon Web Service, Github…) ou les failles Meltdown et Spectre sur les processeurs Intel.

Depuis 2017 le département de Brest de Thales Services à mis en place un Datalab ayant pour objectif de valoriser les données clientes par le biais d’une démarche innovante basée sur les nouvelles technologies issues du Big Data et du Machine Learning. 

Notre projet a donc eu pour objectif de mêler le domaine du Machine Learning et de la cyber sécurité afin de prédire les tentative d’attaque sur un serveur web. 

Pour se faire nous avons déterminé les attaques les plus pertinentes à simuler pour générer des logs sur notre serveur. Ces logs ont été ensuite analysés et traités pour permettre d’entraîner différents types d’algorithmes supervisés de Machine Learning. Pour finir nous testons les algorithmes sur des échantillons de test afin d’évaluer leur performance de prédiction d’attaque.

### Notre victime : un site Web avec une page d’authentification 

Pour réaliser la détection d’attaque nous avions besoin d’une cible. Pour se faire nous avons utilisé un simple site Web avec une page d’authentification muni d’un formulaire login et mot de passe et d’une page “profil” accessible seulement si l’authentification a réussie.

Le serveur est codé en Node.js et le côté client en HTML/CSS. Pour se connecter le client fournit un login et un mot de passe dans un formulaire puis l’envoie à notre serveur. Notre serveur génère un log, applique la fonction de hash MD5 à la chaîne “login-mot_de_passe” et vérifie si le hash est présent dans la base de donnée MySQL. Si oui la personne est redirigée vers la page de profil et si non elle est redirigée vers la page d’accueil pour faire une nouvelle tentative. 
 
### Simulation de 3 types d’attaques de serveur Web

Nous avons choisi de simuler trois attaques web très répandues : l’attaque par force brute, l’injection SQL et la faille XSS.
L’attaque par force brute consiste à tester plusieurs combinaisons de login/mot de passe dites “courantes” [1]. Pour se faire nous avons récupérés sur [2] les 100 mots de passe les plus utilisés et avons testés une authentification avec.

L’injection SQL consiste à entrer une commande afin de modifier la requête à la base de donnée et soit afficher ou modifier les éléments présents dans les tables [3]. Pour simuler ce type d’attaque nous avons trouvé une liste de commandes SQL ayant une forte chance de marcher pour prendre le contrôle de la base de donnée et les avons mis en entrée. 

L’attaque XSS se base sur l’introduction de balise et code javascript dans le formulaire, ce qui révèle des variables mal protégées. Le but est d’afficher des données côté serveur non publiques, de modifier les permissions ou de modifier les fichiers exécutés [4]. De même que pour l’attaque par injection SQL nous l’avons simulé en testant des portions de code qui fonctionnent fréquemment. 

Toutes ces requêtes HTTP Post ont été générées en utilisant le langage python sur l’interface Jupyter Notebook.

### Faire parler les logs pour prédire si le serveur est attaqué

Les données que nous avons générées sont organisées en 452 attaques XSS, 128059 attaques par force brute, 235 attaques par l'injection SQL et 12000 lignes de logs normaux.

Dans le cadre de ce projet, 5 algorithmes d'apprentissage supervisé ont été utilisé : Arbre de Décision, Logistic Regression, Random Forest, Adaboost, XGboost. Suite à une comparaison entre ces algorithmes, l'algorithme Random Forest a produit une meilleur performance. Donc nous avons le choisi et obtenu une précision de 99.97% sur la classification en deux classes.

Afin de s'adapter aux données de client, nous avons fait une mise à jour des caractéristiques et puis repris le modèle de Random Forest. Enfin, nous avons eu une précision de 99.997%.

Pour aller plus loins, nous avons aussi essayé de faire une multi-classification pour bien préciser le type d’une attaque lorsqu’on l’a détecté. Basé sur les données simulées, nous avons obtenu un taux de précision de 99.98% sur la classification en quatre classes (3 attaques et sans attaque).

Sources 

[1] Wikipédia. Attaque par force brute. https://fr.wikipedia.org/wiki/Attaque_par_force_brute

[2] Liste de mots de passe fréquents. https://github.com/jeanphorn/wordlist

[3] Wikipédia. Injection SQL. https://fr.wikipedia.org/wiki/Injection_SQL

[4] Wikipédia. Cross-site scripting. https://fr.wikipedia.org/wiki/Cross-site_scripting 
