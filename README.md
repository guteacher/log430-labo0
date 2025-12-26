# Labo 00 – Infrastructure (Git, Docker, CI/CD)
<img src="https://upload.wikimedia.org/wikipedia/commons/2/2a/Ets_quebec_logo.png" width="250">    
ÉTS - LOG430 - Architecture logicielle - Chargé de laboratoire: Gabriel C. Ullmann, Automne 2025.    

## 🎯 Objectifs d’apprentissage
- Apprendre à créer un projet **Python** conteneurisé avec **Docker** à partir de zéro.
- Apprendre à écrire et exécuter des tests automatisés avec **pytest**.
- Mettre en place un pipeline **CI/CD** avec les ressources à notre disposition.

---

## ⚙️ Setup

Dans ce laboratoire, vous travaillerez sur une application calculatrice. Cette calculatrice est volontairement très simple afin que nous puissions nous concentrer sur la configuration et la structure du projet, ainsi que sur la création d'un pipeline CI/CD. 

Vous allez créer la structure du projet vous-même à partir de zéro, en créant le `requirements.txt`, `Dockerfile`, `docker-compose.yml`, `.env` et `config.py`. Chaque activité vous guidera dans une étape de setup, puis l'implémentation. Il est très important de réaliser ce laboratoire car:
- Les concepts que vous apprendrez ici vous aideront dans **TOUS** les laboratoires suivants.
- Les concepts architecturaux et les pratiques de développement que vous apprenez ici peuvent être appliqués au projet, dans n'importe quel langage de programmation ou framework.

Dans les prochains laboratoires, nous verrons des architectures plus complexes et nous travaillerons avec une variété d'outils logiciels et de concepts architecturaux.

> ⚠️ ATTENTION : Si vous n'avez dèjá, nous vous recommendons d'installer **VS Code**, **Python 3+** et **Docker Desktop** avant de commencer.

> ⚠️ IMPORTANT : Avant de commencer le setup et les activités, veuillez lire la documentation architecturale dans le répertoire `/docs/arc42/docs.pdf` pour comprendre que type d'application nous seront en train de développer.

### 1. Clonez le dépôt GitHub

```bash
git clone https://github.com/[votrenom]/log430-labo0
cd log430-a25-labo0
```

### 2. Créez votre fichier requirements.txt
Le fichier `requirements.txt` contien la liste de dépendances Python que vous avez besoin pour rouler votre projet et qui seront installés via [pip](https://www.w3schools.com/python/python_pip.asp) dans votre environnement. Vous aurez besoin d'un seule dépendance pour ce projet, `pytest` (pour éxecuter les tests unitaires). Alors, créez un fichier `requirements.txt` dans le repertorie racine de votre projet :

```sh
pytest>=7.0
```

### 3. Créez votre Dockerfile
Un fichier `Dockerfile` est une recette permettant de créer une image de conteneur Docker. Un conteneur est une machine virtuelle simplifiée qui s'exécutera dans votre environnement de développement local, mais qui peut également s'exécuter dans un environnement de production si vous le souhaitez. Créez un fichier `Dockerfile` dans le repertorie racine de votre projet :

```sh
FROM python:3.11-slim
WORKDIR /app
COPY src/ ./src/
```

### 4. Créez votre docker-compose.yml
Un fichier `docker-compose.yml` décrit quelles conteneurs (également appelées services) seront crées en utilisant votre image de conteneur Docker comme base. Dans notre cas, nous voulons uniquement exécuter notre calculatrice. Créez un fichier `docker-compose.yml` dans le repertorie racine de votre projet :

```yml
services:
  calculator:
    build: .
    volumes:
      - .:/app
```

### 4. Créez votre .env
Un fichier `.env` est utilisé pour garder variables d'une application qui sont distinctes pour chaque instance et que nous ne voulons pas écrire dans les code par raisons de securité et flexibilité. Par example, une application de gestion de magasin aura une base de données different pour chaque magasin, avec un nom d'utilisateur et mot de passe également distinctes et qui ne doivent pas êtres partagés dans le code. Ici, dans ce très simple cas, nous garderons simplement le nom de l'utilisateur de la calculatrice. Créez un fichier `.env` dans le repertorie racine de votre projet :
```sh
CALCULATOR_USERNAME="Your Name"
```

Une fois le fichier `.env` est crée et la variable existe, l'application sur `/src/calculator.py` est dèjá preparé pour lire le `.env`, extraire la variable `CALCULATOR_USERNAME` et l'utiliser. Si vous faites votre propre application du zéro, vous devriez écrire vous même le code pour lire le `.env`, ou utiliser une librarie tel que [dotenv](https://www.geeksforgeeks.org/python/using-python-environment-variables-with-python-dotenv/) pour vous aider.

### 5. Demarrez le contenuer
Dans le terminal, éxécutez :


---

## 🧪 Activités

### 1. Écrivez les tests

Dans le fichier `test_calculator.py`, écrivez des tests pour les fonctions définies dans `calculator.py`.

```python
def test_addition():
    assert addition(2, 3) == 5
```
Pour lancer les tests localement:

```bash
pytest
```

Si cela ne marche pas dans votre environnement, vous pouvez essayer:
```bash
python3 -m pytest
```

> 💡 **Question 1** : Si l’un des tests échoue à cause d’un bug, comment pytest signale-t-il l’erreur et aide-t-il à la localiser ? Rédigez un test qui provoque volontairement une erreur, puis montrez la sortie du terminal obtenue.

### 2. Ajoutez une étape à la pipeline CI (intégration continue)

Ajoutez une étape (step) dans `.github/workflows/.github-ci.yml` pour que GitHub exécute les tests automatiquement à chaque push. 

### 3. Versionnez votre code

Si tous les tests passent :

```bash
git add .
git commit -m "Tests pour calculator.py"
git push
```

GitHub éxecutera les tests dans son serveur, et ils devront passer également si ils sont corrects.

> 💡 **Question 2** :  Que fait GitHub pendant les étapes de « setup » et « checkout » ? Veuillez inclure la sortie du terminal GitHub CI dans votre réponse.

### 4. Automatiser déploiement continu (CD)
Après l’exécution des tests, déployez l’application dans un serveur ou machine virtuelle via SSH manuellement:

```bash
ssh $my_username@$my_hostname
git clone https://github.com/guteacher/log430-a25-labo0
cd log430-a25-labo0
```

>  📝 **NOTE** : N'oubliez pas d'installer Python, Docker et toutes les dépendances nécessaires sur le serveur de déploiement.

Procédez ensuite à la mise en place de l’automatisation du déploiement continu (CD) dans la machine virtuelle à l’aide de GitHub et de scripts Bash. Je vous recommande d'utiliser un GitHub Runner auto-hébergé (self-hosted) pour effectuer le déploiement automatique dans votre VM. Voici les instructions pour créer un [Runner](https://docs.github.com/en/actions/how-tos/manage-runners/self-hosted-runners/add-runners).

Il existe d'autres méthodes de déploiement continu, mais vous avez besoin d'un serveur avec un nom DNS ou une adresse IP ouvert à Internet pour pouvoir l'utiliser avec GitHub. Par exemple :
- **SSH**: https://www.cyberciti.biz/faq/noninteractive-shell-script-ssh-password-provider/ 
- **Webhook**: https://docs.github.com/en/webhooks

> ⚠️ IMPORTANT : Il n'est pas recommandé d'écrire les noms d'utilisateur et les mots de passe en « plain text » dans un script CI/CD. Veuillez utiliser les [secrets](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets#creating-secrets-for-an-environment) dans votre [environnement](https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments#creating-an-environment) GitHub.

> 💡 **Question 3** : Quelle approche et quelles commandes avez-vous exécutées pour automatiser le déploiement continu de l'application dans la machine virtuelle ? Veuillez inclure les sorties du terminal et les scripts Bash dans votre réponse.

Quelques commandes utiles pour vérifier l’état des ressources :
```bash
free -h   # Vérifier l’utilisation de la RAM
top       # Vérifier l’utilisation du CPU et les processus en cours
df -h     # Vérifier l’espace disque disponible
```

> 💡 **Question 4** : Quel type d'informations pouvez-vous obtenir via la commande « top » ? Veuillez inclure la sortie du terminal dans votre réponse.
---

## 📦 Livrables

- Code compressé en `.zip` contenant **l'ensemble du code source** du projet Labo 00.
- Rapport `.pdf` répondant aux 4 questions presentées dans ce fichier. Il est **obligatoire** d'ajouter du code ou des sorties de terminal pour illustrer chacune de vos réponses.
