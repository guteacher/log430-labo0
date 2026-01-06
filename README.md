# Labo 00 – Infrastructure (Git, Docker, CI/CD)

<img src="https://upload.wikimedia.org/wikipedia/commons/2/2a/Ets_quebec_logo.png" width="250">    
ÉTS - LOG430 - Architecture logicielle - Chargé de laboratoire: Gabriel C. Ullmann.

## 🎯 Objectifs d'apprentissage

- Apprendre à créer un projet **Python** conteneurisé avec **Docker** à partir de zéro.
- Apprendre à écrire et exécuter des tests automatisés avec **pytest**.
- Mettre en place un pipeline **CI/CD** avec les ressources à notre disposition.

---

## ⚙️ Setup

Dans ce laboratoire, vous travaillerez sur une application calculatrice. Cette calculatrice est volontairement très simple afin que nous puissions nous concentrer sur la configuration et la structure du projet, ainsi que sur la création d'un pipeline CI/CD.

Vous allez créer la structure du projet vous-même à partir de zéro, en créant le `requirements.txt`, `Dockerfile`, `docker-compose.yml` et `.env`. Chaque activité vous guidera dans une étape de setup, puis l'implémentation. Il est très important de réaliser ce laboratoire car:

- Les concepts que vous apprendrez ici (ex. le setup Python et Docker, les approches de test et déploiement, etc.) vous aideront à mieux comprendre **TOUS** les laboratoires suivants.
- Les concepts architecturaux et les pratiques de développement que vous apprenez ici peuvent être appliqués au projet, **dans n'importe quel langage de programmation ou framework**.

Dans les prochains laboratoires, nous verrons des architectures plus complexes et nous travaillerons avec une variété d'outils logiciels et de concepts architecturaux.

> ⚠️ **ATTENTION** : Si vous ne l'avez pas déjà fait, nous vous recommandons d'installer **VS Code**, **Python 3+**, **Docker Desktop** et **MySQL Workbench** avant de commencer.

> ⚠️ **IMPORTANT** : Avant de commencer le setup et les activités, veuillez lire la documentation architecturale dans le répertoire `/docs/arc42/docs.pdf` pour comprendre quel type d'application nous serons en train de développer.

### 1. Clonez le dépôt

```bash
git clone https://github.com/[votrenom]/log430-labo0
cd log430-labo0
```

### 2. Créez votre fichier requirements.txt

Le fichier `requirements.txt` contient la liste des dépendances Python que vous avez besoin pour exécuter votre projet et qui seront installées via [pip](https://www.w3schools.com/python/python_pip.asp) dans votre environnement. Vous aurez besoin d'une seule dépendance pour ce projet : `pytest` (pour exécuter les tests unitaires). Créez un fichier `requirements.txt` dans le répertoire racine de votre projet :

```sh
pytest>=7.0
```

### 3. Créez votre Dockerfile

Un fichier `Dockerfile` est une recette permettant de créer une image de conteneur Docker. Un conteneur est une machine virtuelle simplifiée qui s'exécutera dans votre environnement de développement local, mais qui peut également s'exécuter dans un environnement de production si vous le souhaitez. Créez un fichier `Dockerfile` dans le répertoire racine de votre projet :

```sh
FROM python:3.11-slim
WORKDIR /app
COPY src/ ./src/
COPY requirements.txt ./
ENV PYTHONPATH=/app/src
RUN pip install --no-cache-dir -r requirements.txt
```

### 4. Créez votre docker-compose.yml

Un fichier `docker-compose.yml` décrit quels conteneurs (également appelés services) seront créés en utilisant votre image de conteneur Docker comme base. Dans notre cas, nous voulons uniquement exécuter notre calculatrice. Créez un fichier `docker-compose.yml` dans le répertoire racine de votre projet :

```yml
services:
  calculator:
    build: .
    volumes:
      - .:/app
    stdin_open: true
    tty: true
```

### 5. Créez votre .env

Un fichier `.env` est utilisé pour garder les variables d'une application qui sont distinctes pour chaque instance et que nous ne voulons pas écrire dans le code pour des raisons de sécurité et de flexibilité. Par exemple, une application de gestion de magasin aura une base de données différente pour chaque magasin, avec un nom d'utilisateur et mot de passe également distincts et qui ne doivent pas être partagés dans le code. Ici, dans ce très simple cas, nous garderons simplement le nom de l'utilisateur de la calculatrice. Créez un fichier `.env` dans le répertoire racine de votre projet avec une seule ligne :

```sh
CALCULATOR_USERNAME=YourName
```

Une fois le fichier `.env` créé et la variable définie, l'application dans `/src/calculator.py` est déjà préparée pour lire le `.env`, extraire la variable `CALCULATOR_USERNAME` et l'utiliser. Si vous faites votre propre application à partir de zéro, vous devriez écrire vous-même le code pour lire le `.env`, ou utiliser une librairie telle que [dotenv](https://www.geeksforgeeks.org/python/using-python-environment-variables-with-python-dotenv/) pour vous aider.

### 6. Démarrez le conteneur

Dans le terminal, exécutez :

```sh
docker compose build
docker compose up -d
```

Ensuite, cliquez sur votre conteneur dans la liste dans Docker Desktop, sélectionnez l'onglet `Exec` et exécutez:

```sh
python src/calculator.py
```

> 📝 **NOTE** : l'autocomplétion en appuyant sur Tab et les flèches du clavier ne marchent pas dans Docker Desktop, parce qu'il utilise une interface Bash simplifiée. Si vous n'aimez pas cela, vous pouvez également exécuter les commandes via [docker exec](https://docs.docker.com/reference/cli/docker/container/exec/) à partir de votre machine hôte (hors Docker).

---

## 🧪 Activités

### 1. Écrivez les tests

Dans le fichier `test_calculator.py`, écrivez des tests pour les fonctions définies dans `calculator.py`.

```python
def test_addition():
    assert addition(2, 3) == 5
```

Pour lancer les tests:

```bash
pytest
```

> 💡 **Question 1** : Si l'un des tests échoue à cause d'un bug, comment pytest signale-t-il l'erreur et aide-t-il à la localiser ? Rédigez un test qui provoque volontairement une erreur, puis montrez la sortie du terminal obtenue.

### 2. Ajoutez une étape à la pipeline CI (Intégration Continue)

Ajoutez une étape (step) dans `.github/workflows/.github-ci.yml` pour que GitHub exécute les tests automatiquement à chaque push.

> ⚠️ **IMPORTANT** : Il n'est pas recommandé d'écrire les noms d'utilisateur et les mots de passe en « plain text » dans un fichier tel que `.github/workflows/.github-ci.yml`. Veuillez utiliser les [secrets](https://docs.github.com/fr/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets) dans un [environnement GitHub](https://docs.github.com/fr/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments#creating-an-environment) **si vous avez besoin** de gérer des informations d'authentification.

### 3. Versionnez votre code

Si tous les tests passent :

```bash
git add .
git commit -m "Tests pour calculator.py"
git push
```

GitHub exécutera les tests sur son serveur, et ils devront passer également s'ils sont corrects.

> 💡 **Question 2** : Que fait GitHub pendant les étapes de « setup » et « checkout » ? Veuillez inclure la sortie du terminal GitHub CI dans votre réponse.

### 4. Réfléchissez à comment déployer votre code en production

Déployer en production signifie copier votre dépôt et faire le setup de votre application en dehors de votre environnement de développement, dans un autre serveur. Ce serveur pourrait être, par exemple :

- Un serveur physique dans une entreprise/école
- Un serveur en nuage (ex. Azure, AWS, etc.)
- Une machine virtuelle (VM) dans un serveur
- Une machine virtuelle (VM) dans un gestionnaire de machines virtuelles qui pourrait lui-même être dans une machine virtuelle (ex. Proxmox)

Dans le cours LOG430, nous utiliserons [Proxmox](https://proxmox.com/en/), une plateforme de virtualisation.

#### 4.1 Qu'est-ce que Proxmox?

Proxmox est un logiciel qui nous permet de créer et gérer plusieurs machines virtuelles ou conteneurs. Proxmox peut être installé directement sur un ordinateur ou dans une machine virtuelle.

#### 4.2 N'est-ce pas un peu trop compliqué? Pourquoi ne pas simplement déployer sur un serveur fourni par l'école ou sur notre propre ordinateur?

Parce qu'en utilisant Proxmox, nous pouvons simuler l'expérience de travailler avec une plateforme en nuage (ex. Azure, AWS, etc.) sans avoir besoin d'un compte sur ces plateformes ou les limitations d'utilisation de ce compte. Nous pouvons simplement créer une instance Proxmox sur notre ordinateur ou sur un serveur externe (ex. avec une infrastructure fournie par l'école) et avoir quelque chose de similaire à un nuage privé où nous pouvons créer des machines virtuelles en utilisant une interface graphique ou une ligne de commande.

#### 4.3 Pourquoi mettons-nous l'accent sur l'utilisation du nuage dans ce cours?

Parce que la majorité des grandes applications dans l'industrie sont déployées en nuage, nous devons nous familiariser avec ce type d'environnement si nous voulons être bien préparés à les utiliser de manière professionnelle ou pour des projets personnels.

Dans ce labo, nous ferons le setup d'une instance Proxmox dans notre environnement de développement (c'est-à-dire, sur notre propre ordinateur). Dans les prochains labos, nous utiliserons une instance fournie par l'ÉTS.

### 5. Installez Proxmox

Tout d'abord, téléchargez l'image ISO de Proxmox. Cette image est conçue pour être installée directement sur un ordinateur, mais, dans notre cas, nous l'installerons dans une VM en utilisant une application telle que [Oracle VirtualBox](https://www.virtualbox.org/) ou [UTM](https://mac.getutm.app/).

- **Windows, Mac (Intel) ou Linux**: [Tutoriel avec VirtualBox](https://www.youtube.com/watch?v=j7hgAMuH7c8)
- **Mac (ARM)**: [Tutoriel avec UTM](https://www.youtube.com/watch?v=5HrWEB6Mz00)

### 6. Déployez votre application manuellement

Créez une nouvelle VM sur Proxmox, puis déployez l'application sur un serveur ou une machine virtuelle via SSH manuellement:

```bash
ssh $my_username@$my_hostname
git clone https://github.com/guteacher/log430-labo0
cd log430-labo0
```

> 📝 **NOTE 1** : N'oubliez pas d'installer Python, Docker et toutes les dépendances nécessaires sur le serveur de déploiement.

Quelques commandes utiles pour vérifier l'état des ressources :

```sh
free -h   # Vérifier l'utilisation de la RAM
top       # Vérifier l'utilisation du CPU et les processus en cours
df -h     # Vérifier l'espace disque disponible
```

> 📝 **NOTE 2** : si vous avez peu de RAM ou d'espace disque sur votre ordinateur, l'éxécution de Proxmox peut devenir très lente. Si vous avez des problèmes de performance, faites-moi signe.

> 💡 **Question 3** : Quel type d'informations pouvez-vous obtenir via la commande « top » ? Veuillez inclure la sortie du terminal dans votre réponse.

### 7. Automatisez le déploiement continu (CD)

Plusieurs alternatives existent pour le CD : déploiement déclenché par webhooks, accès SSH ou via un outil CI/CD (ex. ArgoCD). Cependant, dans ce labo, nous vous recommandons d'utiliser un [GitHub Runner auto-hébergé (self-hosted)](https://docs.github.com/fr/actions/how-tos/manage-runners/self-hosted-runners/add-runners).

Nous vous recommandons cette approche parce que c'est la plus simple et moins dépendante d'une configuration spécifique de réseau (ex. il n'est pas nécessaire d'ouvrir des ports spécifiques dans le pare-feu, ou d'utiliser une approche événementielle).

---

## 📦 Livrables

- Code compressé en `.zip` contenant **l'ensemble du code source** du projet Labo 00.
- Rapport `.pdf` répondant aux 3 questions présentées dans ce fichier. Il est **obligatoire** d'ajouter du code ou des sorties de terminal pour illustrer chacune de vos réponses
