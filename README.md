# levenshtein-udf
How to make levenshtein function a UDF

1. les sources
Elles se trouvent actuellement dans le reporteoie racine sur la machine : double_metaphone.c et levenshtein.c . Il s'agit de fonctions de calcul téléchargées puis transformées en librairies compatibles MySQL par mes soins. Les specs de la surcouche se trouvent ici :  https://dev.mysql.com/doc/refman/5.6/en/adding-udf.html 

Préconisation : sauvegarder les sources dans un svn, idéalement dans celui du projet virage qui les utilise.

2. les librairies
Les sources précédentes se compilent en librairies à l'aide de gcc. Il est très important de ne pas oublier le flag -shared lors de la compilation, sinon lors de la création de la fonction dans MySQL cela ne se passera pas très bien.

Préconisation : déposer les librairies (les fichiers double_metaphone.so et levenshtein.so dans notre cas) dans le répertoire /lib de la machine puis créer un lien symbolique du même nom depuis /usr/local/mysql/lib/plugin . Ainsi un programme qui aurait besoin de faire appel à ces mêmes fonctions n'aura pas besoin de passer par la base de données.

3. les fonctions
Pour finir il faut créer les fonctions dans MySQL afin de pouvoir les utiliser lors de requêtes. Il faut alors passer l'ordre suivant : CREATE FUNCTION <nom_de_la_fonction_dans_la_librairie> RETURNS <type_de_la_variable_de_retour_attendue> SONAME '<nom_de_la_librairie_avec_son_extension_mais_sans_le_path>'; 

Exemple : dans la librairire levenshtein.so, nous disposons de trois fonctions : levenshtein, levenshtein_k et levenshtein_ratio. Les deux premières retournent un entier, la dernière, qui nous intéresse un réel. Pour créer la fonction dans MySQL il faudra donc passer la commande suivante :  CREATE FUNCTION levenshtein_ratio RETURNS REAL SONAME 'levenshtein.so';
Nous pouvons désormais l'utiliser comme n'importe quelle autre fonction dans une requête.

Astuce :  La liste des UDF est trouvable grace a cette requête : select * from mysql.func . 
