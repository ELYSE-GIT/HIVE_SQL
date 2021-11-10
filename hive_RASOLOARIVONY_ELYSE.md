
NOM : RASOLOARIVONY Elysé
Id: 20211003
TP : HIVE




##### HIVE Beeline Client ######

# connect to HADOOP cluster using SSH
Password : XXXXXXX

# Connecter sur client Hive 
$ beeline
[elyse.rasoloarivony@hadoop-edge01 ~]$ beeline

....
ng a script to a file
Usage: script <filename>
0: jdbc:hive2://hadoop-master01.efrei.online:> !set                Set a beeline variable
Usage: set <key> <value>
0: jdbc:hive2://hadoop-master01.efrei.online:> !sh                 Execute a shell command
Exception raised from Shell command java.lang.Exception: Failed to execute Execute a shell
. . . . . . . . . . . . . . . . . . . . . . .> !verbose            Set verbose mode on
. . . . . . . . . . . . . . . . . . . . . . .> ;
Error: Error while compiling statement: FAILED: ParseException line 1:16 cannot recognize input near 'Execute' 'a' 'SQL' (state=42000,code=40000)
0: jdbc:hive2://hadoop-master01.efrei.online:>


# la liste des commmande de beeline
$ !help
....0: jdbc:hive2://hadoop-master01.efrei.online:> help
!addlocaldriverjar  Add driver jar file in the beeline client side.
!addlocaldrivername Add driver name that needs to be supported in the beeline
                    client side.
!all                Execute the specified SQL against all the current connections
!autocommit         Set autocommit mode on or off
!batch              Start or execute a batch of statements
!brief              Set verbose mode off
!call               Execute a callable statement
.........
!dbinfo             Give metadata information about the database
!delimiter          Sets the query delimiter, defaults to ;
!describe           Describe a table
!dropall            Drop all tables in the current database
!exportedkeys       List all the exported keys for the specified table
!go                 Select the current connection
!help               Print a summary of command usage
........
!sh                 Execute a shell command
!sql                Execute a SQL command
!tables             List all the tables in the database
!typeinfo           Display the type map for the current connection
!verbose            Set verbose mode on

Comments, bug reports, and patches go to ???


# voir qui est connecté en ce moment: (jdbc?)
$ !list
0: jdbc:hive2://hadoop-master01.efrei.online:> !list
1 active connection:
 #0  open     jdbc:hive2://hadoop-master01.efrei.online:2181,hadoop-master02.efrei.online:2181,hadoop-master03.efrei.online:2181/default;httpPath=cliservice;principal=hive/_HOST@EFREI.ONLINE;serviceDiscoveryMode=zooKeeper;ssl=true;transportMode=http;zooKeeperNamespace=hiveserver2

# Listez les bases de données
$ show databases;

+-------------------------------+
|         database_name         |
+-------------------------------+
| a_aney                        |
| a_chevron                     |
| a_leblanc                     |
| a_ngau                        |
| a_poloubinski                 |
| a_tonlop                      |
| abdelhadi_hirchi              |
| adrien_tarcy                  |
| adrouineau_areino             |
.....
| cmauvezin                     |
| cvenet                        |
| database_ccarayon             |
| db_ccarayon                   |
| default                       |
| droguet                       |
| e_bernard                     |
| e_diez                        |
| e_guedj                       |
| e_joliman                     |
| e_yimene_kaze                 |
| ebitton                       |
| elyse_rasoloarivony           |
...
| wzouitene                     |
| y_jendoubi                    |
| yang_chen                     |
| yingda                        |
| ymaassouli                    |
| ymeli                         |
+-------------------------------+
213 rows selected (0.224 seconds)


# Créer une base de donnée :
$ create database elyse_rasoloarivony;

# entrer dans ma base de donnée
$ use elyse_rasoloarivony;

Je suis maintenant dans ma base de donnée personnelle


# Créer une table, nommons là "temp" :
$ create table temp (col string);

....
425); Time taken: 0.024 seconds
INFO  : OK
INFO  : Concurrency mode is disabled, not creating a lock manager
+-----------+------------+----------+
| col_name  | data_type  | comment  |
+-----------+------------+----------+
| col       | string     |          |
+-----------+------------+----------+

# lister les tables dans ma base de donnée
$ show tables;
...
+-----------------+
|    tab_name     |
+-----------------+
| temp            |
+-----------------+
1 row selected (0.21 seconds)

### quelques commandes de visualisation d'une table ######



* regardez les colonnes de ma table :
$ show columns from temp

* voir les propriétés ma table "temp" :
$ show tblproperties temp;

* Information sur la table (localisation, ...):
$ show table extended  like temp;

+----------------------------------------------------+
|                      tab_name                      |
+----------------------------------------------------+
| tableName:temp                         |
| owner:elyse.rasoloarivony                          |
| location:hdfs://efrei/warehouse/tablespace/external/hive/elyse_rasoloariv                                                                                                                                     ony.db/temp |
| inputformat:org.apache.hadoop.mapred.TextInputFormat |
| outputformat:org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat |
| columns:struct columns { string col}               |
| partitioned:false                                  |
| partitionColumns:                                  |
| totalNumberFiles:1                                 |
| totalFileSize:16778                                |
| maxFileSize:16778                                  |
| minFileSize:16778                                  |
| lastAccessTime:1636040207450                       |
| lastUpdateTime:1636040473695                       |
|                                                    |
+----------------------------------------------------+

* Description sur ma table :
$ describe extended temp;

+-----------------------------+----------------------------------------------------+----------+
|          col_name           |                     data_type                      | comment  |
+-----------------------------+----------------------------------------------------+----------+
| col                         | string                                             |          |
|                             | NULL                                               | NULL     |
| Detailed Table Information  | Table(tableName:temp, dbName:istepanian, owner:elyse.rasoloarivony, createTim......................





# supprimer la table
$ drop table temp;

# vérifier si c'est bien supprimé
$ show tables;

+-----------------+
|    tab_name     |
+-----------------+
|                 |
+-----------------+
0 rows selected (0.115 seconds)

# Quitter beeline avec
$ !quit



#### CREATE TABLES #######

# Reconnecter à beeline comme au début et reconnecter à ma base de donnée



# créer une table externe nommée trees_external:

* créer un dossier dans hdfs via beeline :
$ sh! hdfs dfs -mkdir trees_table


$ CREATE EXTERNAL TABLE trees_external(GEOPOINT string, ARRONDISSEMENT int, GENRE string, ESPECE string, FAMILLE string, ANNEEPLANTATION int, HAUTEUR float, CIRCONFERENCE float, ADRESSE string, NOMCOMMUN string, VARIETE string, OBJECTID int, NOM_EV string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ';' LINES TERMINATED BY '\n' STORED AS TEXTFILE LOCATION 'hdfs://efrei/user/elyse.rasoloarivony/trees_table' TBLPROPERTIES ("skip.header.line.count"="1");

J'ai ajouté TBLPROPERTIES ("skip.header.line.count"="1") à la requête CREATE TABLE pour garantir que l'en-tête  csv (les noms de colonnes) sera ignoré lors du changement des données. Nous pouvons maintenant charger les données dans notre table externe en utilisant LOAD DATA


ou

$  CREATE EXTERNAL TABLE trees_external (GEOPOINT STRING, ARRONDISSEMENT INT, GENRE STRING, ESPECE STRING, FAMILLE STRING, ANNEE_PLANTATION DATE, HAUTEUR FLOAT, CIRCONFERENCE FLOAT, ADRESSE STRING, NOM_COMMUN STRING, VARIETE STRING, OBJECTID INT, NOM_EV STRING) ROW FORMAT DELIMITED FIELDS TERMINATED BY ';' LINES TERMINATED BY '\n' LOCATION '/user/elyse.rasoloarivony/trees_table';




# Vérifions bien si la table est bien créee

$ show tables;
...
INFO  : Concurrency mode is disabled, not creating a lock manager
+-----------------+
|    tab_name     |
+-----------------+
| trees_external  |
+-----------------+





$ describe extended trees_table;

+-----------------------------+----------------------------------------------------+----------+
|          col_name           |                     data_type                      | comment  |
+-----------------------------+----------------------------------------------------+----------+
| geopoint                    | string                                             |          |
| arrondissement              | int                                                |          |
| genre                       | string                                             |          |
| espece                      | string                                             |          |
| famille                     | string                                             |          |
| anneeplantation             | int                                                |          |
| hauteur                     | float                                              |          |
| circonference               | float                                              |          |
| adresse                     | string                                             |          |
| nomcommun                   | string                                             |          |
| variete                     | string                                             |          |
| objectid                    | int                                                |          |
| nom_ev                      | string                                             |          |
|                             | NULL                                               | NULL     |







# Créer une table interne:

$ CREATE TABLE trees_internal (GEOPOINT STRING, ARRONDISSEMENT INT, GENRE STRING, ESPECE STRING, FAMILLE STRING, ANNEE_PLANTATION DATE, HAUTEUR FLOAT, CIRCONFERENCE FLOAT, ADRESSE STRING, NOM_COMMUN STRING, VARIETE STRING, OBJECTID INT, NOM_EV STRING);

INFO  : Starting task [Stage-0:DDL] in serial mode
INFO  : Completed executing command(queryId=hive_20211110125835_4d1df3c3-3f6b-4da0-b962-67cd891bf6bc); Time taken: 0.089 seconds
INFO  : OK
INFO  : Concurrency mode is disabled, not creating a lock manager
No rows affected (0.135 seconds)


0: jdbc:hive2://hadoop-master01.efrei.online:> show tables;
...
INFO  : Executing command(queryId=hive_20211110125841_8c52755e-69cd-4869-b4a9-4a65320d8342): show tables
INFO  : Starting task [Stage-0:DDL] in serial mode
INFO  : Completed executing command(queryId=hive_20211110125841_8c52755e-69cd-4869-b4a9-4a65320d8342); Time taken: 0.017 seconds
INFO  : OK
INFO  : Concurrency mode is disabled, not creating a lock manager
+-----------------+
|    tab_name     |
+-----------------+
| trees_external  |
| trees_internal  |
+-----------------+
2 rows selected (0.128 seconds)



* Importation des données dans la table interne en utilisant celle d'externe

$ INSERT OVERWRITE TABLE trees_internal SELECT * FROM trees_external;



* Vérification si chaque table a  les mêmes lignes
$ SELECT * FROM trees_external;
$ SELECT * FROM trees_internal;


Avec ces commandes on peut vérifier si les 2 tables contiennent bient le même nombre de ligne

....
INFO  : Completed compiling command(queryId=hive_20211104145021_0c648086-c3fb-4c5c-8aab-77a72c15ff81); Time taken: 0.402 seconds
INFO  : Concurrency mode is disabled, not creating a lock manager
INFO  : Executing command(queryId=hive_20211104145021_0c648086-c3fb-4c5c-8aab-77a72c15ff81): select count(*) from trees_internal
INFO  : Completed executing command(queryId=hive_20211104145021_0c648086-c3fb-4c5c-8aab-77a72c15ff81); Time taken: 0.003 seconds
INFO  : OK
INFO  : Concurrency mode is disabled, not creating a lock manager
+------+
| _c0  |
+------+
| 97   |
+------+



#### CREATION DES REQUETES #####

Comme dans MapReduce:

* La liste des différents district :
$ SELECT DISTINCT ARRONDISSEMENT FROM trees_internal;


+-----------------+
| arrondissement  |
+-----------------+
| 3               |
| 4               |
| 5               |
| 6               |
| 7               |
| 8               |
| 9               |
| 11              |
| 12              |
| 13              |
| 14              |
| 15              |
| 16              |
| 17              |
| 18              |
| 19              |
| 20              |
+-----------------+
17 rows selected (28.472 seconds)





* la liste des différentes espèces

$ SELECT ESPECE FROM trees_internal GROUP BY ESPECE;

+-----------------+
|     espece      |
+-----------------+
| araucana        |
| atlantica       |
| australis       |
| baccata         |
| bignonioides    |
| biloba          |
| bungeana        |
| cappadocicum    |
| carpinifolia    |
| colurna         |
| coulteri        |
| decurrens       |
| dioicus         |
| distichum       |
| excelsior       |
| fraxinifolia    |
| giganteum       |
| giraldii        |
| glutinosa       |
| grandiflora     |
| hippocastanum   |
| ilex            |
| involucrata     |
| japonicum       |
| kaki            |
| libanii         |
| monspessulanum  |
| nigra           |
| nigra laricio   |
| opalus          |
| orientalis      |
| papyrifera      |
| petraea         |
| pomifera        |
| pseudoacacia    |
| sempervirens    |
| serrata         |
| stenoptera      |
| suber           |
| sylvatica       |
| tomentosa       |
| tulipifera      |
| ulmoides        |
| virginiana      |
| x acerifolia    |
+-----------------+
45 rows selected (27.241 seconds)



* La liste de nombre d'arbre par genre:

$ SELECT GENRE, COUNT(*) AS NB_TREES FROM trees_internal GROUP BY GENRE;

+-----------------+-----------+
|      genre      | nb_trees  |
+-----------------+-----------+
| Acer            | 3         |
| Aesculus        | 3         |
| Ailanthus       | 1         |
| Alnus           | 1         |
| Araucaria       | 1         |
| Broussonetia    | 1         |
| Calocedrus      | 1         |
| Catalpa         | 1         |
| Cedrus          | 4         |
| Celtis          | 1         |
| Corylus         | 3         |
| Davidia         | 1         |
| Diospyros       | 4         |
| Eucommia        | 1         |
| Fagus           | 8         |
| Fraxinus        | 1         |
| Ginkgo          | 5         |
| Gymnocladus     | 1         |
| Juglans         | 1         |
| Liriodendron    | 2         |
| Maclura         | 1         |
| Magnolia        | 1         |
| Paulownia       | 1         |
| Pinus           | 5         |
| Platanus        | 19        |
| Pterocarya      | 3         |
| Quercus         | 4         |
| Robinia         | 1         |
| Sequoia         | 1         |
| Sequoiadendron  | 5         |
| Styphnolobium   | 1         |
| Taxodium        | 3         |
| Taxus           | 2         |
| Tilia           | 1         |
| Ulmus           | 1         |
| Zelkova         | 4         |
+-----------------+-----------+
36 rows selected (22.497 seconds)




* La liste des hauteurs des plus grandes arbres par genre

$ SELECT GENRE, MAX(HAUTEUR) AS HEIGHT FROM trees_internal GROUP BY GENRE;

+-----------------+---------+
|      genre      | height  |
+-----------------+---------+
| Acer            | 16.0    |
| Aesculus        | 30.0    |
| Ailanthus       | 35.0    |
| Alnus           | 16.0    |
| Araucaria       | 9.0     |
| Broussonetia    | 12.0    |
| Calocedrus      | 20.0    |
| Catalpa         | 15.0    |
| Cedrus          | 30.0    |
| Celtis          | 16.0    |
| Corylus         | 20.0    |
| Davidia         | 12.0    |
| Diospyros       | 14.0    |
| Eucommia        | 12.0    |
| Fagus           | 30.0    |
| Fraxinus        | 30.0    |
| Ginkgo          | 33.0    |
| Gymnocladus     | 10.0    |
| Juglans         | 28.0    |
| Liriodendron    | 35.0    |
| Maclura         | 13.0    |
| Magnolia        | 12.0    |
| Paulownia       | 20.0    |
| Pinus           | 30.0    |
| Platanus        | 45.0    |
| Pterocarya      | 30.0    |
| Quercus         | 31.0    |
| Robinia         | 11.0    |
| Sequoia         | 30.0    |
| Sequoiadendron  | 35.0    |
| Styphnolobium   | 10.0    |
| Taxodium        | 35.0    |
| Taxus           | 13.0    |
| Tilia           | 20.0    |
| Ulmus           | 15.0    |
| Zelkova         | 30.0    |
+-----------------+---------+
36 rows selected (25.075 seconds)



* Ordonner les arbres de la plus petite vers la plus grande en circonférence:

SELECT ARRONDISSEMENT,FAMILLE,NOM_COMMUNE,CIRCONFERENCE FROM trees_internal ORDER BY CIRCONFERENCE ASC;

+-----------------+----------------+----------------------------+----------------+
| arrondissement  |    famille     |        nom_commune         | circonference  |
+-----------------+----------------+----------------------------+----------------+
| 12              | Ebenaceae      | Plaqueminier de Virginie   | NULL           |
| 16              | Taxodiaceae    | Séquoia géant              | NULL           |
| 16              | Magnoliaceae   | Magnolia à grandes fleurs  | NULL           |
| 7               | Moraceae       | Oranger des Osages         | NULL           |
| 12              | Fagaceae       | Chêne vert                 | NULL           |
| 12              | Pinaceae       | Pin Napoléon               | 50.0           |
| 5               | Fagaceae       | Faux de Verzy              | 72.0           |
| 16              | Cornaceae      | Arbre aux pochettes        | 120.0          |
| 12              | Taxaceae       | If commun                  | 140.0          |
| 16              | Araucariaceae  | Désespoir du singe         | 140.0          |
| 16              | Ebenaceae      | Kaki                       | 145.0          |
| 12              | Sapindaceae    | Erable d'Italie            | 160.0          |
| 12              | Ebenaceae      | Kaki                       | 160.0          |
| 16              | Fabaceae       | Chicot du Canada           | 162.0          |
| 12              | Ebenaceae      | Plaqueminier de Virginie   | 180.0          |
| 16              | Fagaceae       | Chêne liège                | 180.0          |
| 4               | Ulmaceae       | Orme champêtre             | 180.0          |
| 7               | Eucomiaceae    | Arbre à gutta-percha       | 190.0          |
| 16              | Moraceae       | Murier à papier            | 190.0          |
| 16              | Pinaceae       | Cèdre bleu de l'Atlas ple  | 195.0          |
| 8               | Cupressaceae   | Cèdre à encens             | 195.0          |
| 16              | Fagaceae       | Hêtre pleureur             | 200.0          |
| 12              | Magnoliaceae   | Tulipier de Virginie       | 205.0          |
| 19              | Malvaceae      | Tilleul argenté            | 205.0          |
| 3               | Betulaceae     | Noisetier de Byzance       | 210.0          |
| 16              | Ginkgoaceae    | Arbre aux quarante écus    | 215.0          |
| 15              | Betulaceae     | Aulne glutineux            | 220.0          |
| 20              | Sapindacaees   | Erable de Montpellier      | 225.0          |
| 12              | Pinaceae       | Pin aux grands cônes       | 225.0          |
| 19              | Ginkgoaceae    | Arbre aux quarante écus    | 230.0          |
| 16              | Fagaceae       | Hêtre pleureur             | 230.0          |
| 16              | Taxaceae       | If commun                  | 235.0          |
| 16              | Pinaceae       | Pin de Corse               | 240.0          |
| 12              | Ulmaceae       | Zelkova du Japon           | 240.0          |
| 11              | Betulaceae     | Noisetier de Byzance       | 245.0          |
| 12              | Ulmaceae       | Faux orme de Sibérie       | 245.0          |
| 12              | Pinaceae       | Pin noir                   | 248.0          |
| 16              | Pinaceae       | Pin noir                   | 250.0          |
| 12              | Ginkgoaceae    | Arbre aux quarante écus    | 255.0          |
| 16              | Ulmaceae       | Faux orme de Sibérie       | 260.0          |
| 6               | Bignoniaceae   | Catalpa commun             | 260.0          |
| 16              | Betulaceae     | Noisetier de Byzance       | 260.0          |
| 12              | Taxodiaceae    | Cyprés chauve              | 270.0          |
| 12              | Sapindaceae    | Erable de Cappadoce        | 280.0          |
| 8               | Ginkgoaceae    | Arbre aux quarante écus    | 283.0          |
| 16              | Taxodiaceae    | Cyprés chauve              | 290.0          |
| 12              | Cannabaceae    | Micocoulier de Provence    | 295.0          |
| 16              | Fagaceae       | Hêtre pourpre              | 300.0          |
| 12              | Magnoliaceae   | Tulipier de Virginie       | 305.0          |
| 8               | Taxodiaceae    | Séquoia géant              | 320.0          |
| 9               | Juglandaceae   | Pérocarya du Caucase       | 330.0          |
| 19              | Fabaceae       | Sophora du Japon           | 335.0          |
| 14              | Taxodiaceae    | Séquoia sempervirent       | 335.0          |
| 16              | Platanaceae    | Platane d'Orient           | 340.0          |
| 20              | Sapindaceae    | Marronnier d'Inde          | 345.0          |
| 16              | Taxodiaceae    | Cyprés chauve              | 350.0          |
| 13              | Pinaceae       | Cèdre bleu de l'Atlas      | 350.0          |
| 13              | Sapindaceae    | Marronnier d'Inde          | 355.0          |
| 19              | Oleaceae       | Frêne commun               | 365.0          |
| 14              | Fagaceae       | Hêtre pourpre              | 370.0          |
| 12              | Fagaceae       | Hêtre pourpre              | 370.0          |
| 16              | Platanaceae    | Platane d'Orient           | 375.0          |
| 5               | Fabaceae       | Robinier faux-acacia       | 385.0          |
| 16              | Ginkgoaceae    | Arbre aux quarante écus    | 395.0          |
| 16              | Ulmaceae       | Faux orme de Sibérie       | 395.0          |
| 20              | Platanaceae    | Platane commun             | 395.0          |
| 16              | Juglandaceae   | Pérocarya du Caucase       | 400.0          |
| 12              | Platanaceae    | Platane commun             | 405.0          |
| 12              | Platanaceae    | Platane commun             | 405.0          |
| 16              | Paulowniaceae  | Paulownia                  | 420.0          |
| 12              | Fagaceae       | Chêne rouvre               | 430.0          |
| 12              | Pinaceae       | Cèdre du Liban             | 440.0          |
| 12              | Fagaceae       | Chêne rouve                | 450.0          |
| 16              | Pinaceae       | Cèdre du Liban             | 460.0          |
| 16              | Simaroubaceae  | Ailanthe                   | 460.0          |
| 18              | Platanaceae    | Platane d'Orient           | 470.0          |
| 19              | Taxodiaceae    | Séquoia géant              | 470.0          |
| 12              | Platanaceae    | Platane d'Orient           | 475.0          |
| 16              | Platanaceae    | Platane commun             | 480.0          |
| 8               | Platanaceae    | Platane d'Orient           | 480.0          |
| 16              | Taxodiaceae    | Séquoia géant              | 490.0          |
| 12              | Platanaceae    | Platane commun             | 490.0          |
| 16              | Sapindaceae    | Marronnier d'Inde          | 505.0          |
| 12              | Platanaceae    | Platane commun             | 510.0          |
| 16              | Platanaceae    | Platane commun             | 520.0          |
| 16              | Platanaceae    | Platane commun             | 525.0          |
| 16              | Juglandaceae   | Ptérocarya de Chine        | 530.0          |
| 12              | Fagaceae       | Hêtre pleureur             | 530.0          |
| 16              | Fagaceae       | Hêtre pourpre              | 558.0          |
| 12              | Juglandaceae   | Noyer noir                 | 570.0          |
| 12              | Platanaceae    | Platane commun             | 570.0          |
| 14              | Platanaceae    | Platane commun             | 580.0          |
| 17              | Platanaceae    | Platane commun             | 595.0          |
| 16              | Taxodiaceae    | Séquoia géant              | 655.0          |
| 19              | Platanaceae    | Platane d'Orient           | 670.0          |
| 8               | Platanaceae    | Platane d'Orient           | 700.0          |
| 7               | Platanaceae    | Platane d'Orient           | 700.0          |
+-----------------+----------------+----------------------------+----------------+
97 rows selected (29.837 seconds)


* District qui contient le plus d'arbre
SELECT ARRONDISSEMENT,COUNT(*) AS NB FROM trees_internal GROUP BY ARRONDISSEMENT ORDER BY COUNT(*) DESC LIMIT 1;

+-----------------+-----+
| arrondissement  | nb  |
+-----------------+-----+
| 16              | 36  |
+-----------------+-----+
1 row selected (60.581 seconds)
