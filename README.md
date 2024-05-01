Ce rapport vous invite à découvrir les technologies utilisées dans le développement web moderne, en utilisant Docker, Kubernetes et les services cloud. L'objectif est de comprendre comment ces outils peuvent être utilisés pour créer des applications web efficaces et évolutives.
Dans ce projet, nous allons suivre différentes étapes, de la création d'une petite application avec html pour frontend et python Flask pour backend à son déploiement dans un environnement Kubernetes. Nous utiliserons Docker pour isoler notre application dans des conteneurs, ce qui facilitera son déploiement et sa gestion.

1) Nous allons commencer par coder notre application
2) Créer une image Docker => faire un Dockerfile
Une image Docker est un ensemble de couches contenant tout ce dont une application a besoin pour s'exécuter. Cela inclut le système d'exploitation, les bibliothèques et le code de l'application. Pour créer notre image Docker, nous spécifions les dépendances de celui-ci vu que nous utilisons Flask nous ajoutons dans un fichier appelé requirements les dépendances pour exécuter notre application.
Un Dockerfile est un fichier texte contenant des instructions pour construire une image Docker. Ces instructions décrivent les étapes nécessaires pour assembler l'image à partir d'une image de base, installer les dépendances, copier les fichiers de l'application et configurer l'environnement d'exécution. En résumé, le Dockerfile est un script de construction automatisant le processus de création d'une image Docker.
Pour construire notre image nous allons commencer par télécharger docker  Desktop sur notre ordinateur : https://www.docker.com/get-started 
Ensuite créer notre fichier dockerfile et ajouter les instructions pour construire notre image docker.
Une fois que notre Dockerfile a été créé, nous pouvons construire notre  image Docker et exécuter un conteneur Docker à partir de cette image  en utilisant les commandes suivantes dans le répertoire de notre application : 
docker build -t myrile-flask .

docker images : Pour vérifier l’image qu’on vient de créer.

docker run -p 5000:5000 myrile-flask


Cela démarrera notre conteneur Docker à partir de notre image et exposera le port 5000 de notre conteneur sur le port 5000 de notre machine locale. Nous pouvons accéder ensuite à notre application Flask en allant à http://localhost:5000

3) Publier l’image Docker sur le Docker Hub

Pour publier ton image Docker sur Docker Hub, nous devons :
1) Créer un compte sur Docker Hub 
2) Connecter Docker à notre compte Docker Hub : docker login
3) Tagger notre image Docker : docker tag flask amide/app-flask
4) Publier ton image sur Docker Hub : docker push utilisateur/app-flask



Une fois que la commande docker push est terminée, nous pouvons vérifier sur Docker Hub que notre image a été publiée avec succès en accédant à la page de notre compte Docker Hub et en cherchant le dépôt correspondant à notre image.


4) Créer un déploiement Kubernetes
Un déploiement Kubernetes est un outil qui permet de gérer et de contrôler le déploiement d'une application conteneurisée sur un cluster Kubernetes.
Pour créer un déploiement Kubernetes pour notre application, nous allons suivre ces étapes :
Installer Minikube via https://minikube.sigs.k8s.io/docs/start/ 
Créer un fichier de configuration YAML pour notre déploiement, Ce fichier contiendra des informations sur l'image Docker à utiliser, le nombre de répliques de notre application à déployer, et d'autres configurations telles que les ressources nécessaires (CPU, mémoire), les ports à exposer, etc.
Déployer notre application avec kubectl : Une fois que tu as créé ton fichier de configuration YAML, nous pouvons déployer notre application en utilisant l'outil en ligne de commande kubectl de Kubernetes kubectl apply -f ton_fichier.yaml pour créer le déploiement.


Vérifier que le déploiement est réussi : kubectl get deployments pour afficher la liste des déploiements Kubernetes.

 
Une fois que notre déploiement est réussi, nous pouvons récupérer l'adresse IP et accéder à notre application en utilisant le service Kubernetes associé.

5) Créer un service Kubernetes
Un service Kubernetes est une ressource qui permet d'exposer une application déployée sur un cluster Kubernetes de manière à ce qu'elle puisse être accessible depuis d'autres parties du cluster ou depuis l'extérieur.
En d'autres termes,un service Kubernetes fournit une adresse IP stable pour accéder à une application déployée sur un cluster Kubernetes.
Pour créer un service Kubernetes pour notre application, nous allons suivre ces étapes :
Créer un fichier de configuration YAML pour notre service, ce fichier contient des informations sur le service lui-même, telles que son nom, son type (ClusterIP, NodePort, LoadBalancer, etc.), les ports sur lesquels il écoute, et éventuellement des étiquettes pour la sélection des pods à exposer.

Déployer notre application avec kubectl : Une fois que nous avons créé notre fichier de configuration YAML, nous pouvons déployer notre service en utilisant l'outil en ligne de commande kubectl de Kubernetes kubectl apply -f ton_fichier.yaml pour créer le service.
Vérifier que le service est réussi : kubectl get services pour afficher la liste des services Kubernetes dans le cluster. Cela inclut des informations telles que le nom du service, le type de service, le cluster IP, les ports exposés, et l'état actuel du service.


Une particularité qu’il y a avec cette partie est qu’il y a plusieurs méthode qu’on peut utiliser pour créer le déploiement et le service, on peut utiliser directement des lignes de commande au lieu de créer de fichier YAML.
Autre chose pour les services, pour certaines parties de votre application (par exemple, les frontaux), vous souhaiterez peut-être exposer un service sur une adresse IP externe, située en dehors de votre cluster.
Les ServiceTypes Kubernetes vous permettent de spécifier le type de service que vous souhaitez. La valeur par défaut est ClusterIP.
Les valeurs de type et leurs comportements sont :
ClusterIP : expose le service sur une adresse IP interne au cluster. Le choix de cette valeur rend le service accessible uniquement depuis l'intérieur du cluster. Il s'agit du ServiceType par défaut.
NodePort : expose le service sur l'adresse IP de chaque nœud sur un port statique (le NodePort). Un service ClusterIP, vers lequel le service NodePort est acheminé, est automatiquement créé. Vous pourrez contacter le service NodePort, depuis l'extérieur du cluster, en demandant NodeIP:NodePort.
LoadBalancer : expose le service en externe à l'aide de l'équilibreur de charge d'un fournisseur de cloud. Les services NodePort et ClusterIP, vers lesquels l'équilibreur de charge externe est acheminé, sont automatiquement créés.
ExternalName : mappe le service au contenu du champ externalName (par exemple foo.bar.example.com), en renvoyant un enregistrement CNAME
Pour exposer les routes HTTP et HTTPS à l'aide de NodePort nous pouvons utiliser la commande : kubectl expose deployment myservice --type=NodePort --port=5000
Pour récupérer l'adresse du service : minikube service myservice --url


Si on veut utiliser LoadBalancer on remplace type=LoadBalancer.


Partie 2 : Ajouter une gateway en local
Ingress est un contrôleur de ressources Kubernetes conçu pour exposer des services HTTP et HTTPS à l'extérieur du cluster. Il agit comme une passerelle pour le trafic entrant vers les services Kubernetes, en permettant le routage basé sur les règles définies dans les objets Ingress.
Configurer Ingress sur Minikube avec le contrôleur d'entrée NGINX
Nous avons activer  le contrôleur NGINX Ingress : minikube addons enable ingress
Vérifiez que le contrôleur NGINX Ingress est en cours d'exécution 


Comme vous pouvez le remarquer dans les captures, nous avons pu accéder à notre page web grâce à un nom de domaine.
Partie 3 et 4 
Ajouter un deuxième service en local 
Pour ajouter un deuxième service en local, nous avons suivi une approche similaire à celle que nous avons utilisée pour le service Flask. Nous avons créer un fichier.yml pour déployer le deuxième déployment comme un serveur MySQL :


Et un deuxième fichier de service appelé service 2.yml 




Pour lier les Services nous allons effectuer un changement dans le fichier de déploiement en ajoutant le nom du service Mysql pour la connexion et lui attribuer un port.




Nous n’avons pas exposé notre serveur mysql à l'extérieur
Partie 4 Ajouter une base de données en local
Vu que nous avons déjà installé un serveur mysql en déployant le deuxième service, nous allons utiliser ce même serveur pour créer notre base de données.





Ce rapport explore les étapes du développement et du déploiement d'une application web moderne en utilisant Docker, Kubernetes et les services cloud. Voici un résumé succinct :
Codage de l'application : Nous avons développé une application web avec HTML pour le frontend et Python Flask pour le backend.
Création de l'image Docker : Nous avons encapsulé notre application dans une image Docker à l'aide d'un Dockerfile, puis exécuté cette image dans un conteneur Docker.
Publication de l'image Docker : L'image Docker a été téléchargée sur Docker Hub après avoir créé un compte et exécuté quelques commandes.
Déploiement Kubernetes : Nous avons déployé notre application sur un cluster Kubernetes en utilisant un déploiement et un service Kubernetes.
Utilisation d'Ingress : Nous avons configuré Ingress pour gérer le routage HTTP/HTTPS et exposer nos services à l'extérieur du cluster.
En résumé, ce rapport offre un aperçu des étapes clés pour développer et déployer une application web moderne avec Docker, Kubernetes et les services cloud.













