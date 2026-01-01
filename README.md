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
- Les concepts que vous apprendrez ici (ex. le setup Python et Docker, les approches de test et déploiement, etc) vous aideront à mieux comprendre **TOUS** les laboratoires suivants.
- Les concepts architecturaux et les pratiques de développement que vous apprenez ici peuvent être appliqués au projet, **dans n'importe quel langage de programmation ou framework**.

Dans les prochains laboratoires, nous verrons des architectures plus complexes et nous travaillerons avec une variété d'outils logiciels et de concepts architecturaux.

> ⚠️ **ATTENTION** : Si vous ne l'avez pas déjà fait, nous vous recommandons d'installer **VS Code**, **Python 3+**, **Docker Desktop** et **MySQL Workbench** avant de commencer.

> ⚠️ **IMPORTANT** : Avant de commencer le setup et les activités, veuillez lire la documentation architecturale dans le répertoire `/docs/arc42/docs.pdf` pour comprendre quel type d'application nous serons en train de développer.

### 1. Clonez le dépôt GitHub

```bash
git clone https://github.com/[votrenom]/log430-labo0
cd log430-labo0
```

### 2. Créez votre fichier requirements.txt
Le fichier `requirements.txt` contient la liste de dépendances Python que vous avez besoin pour rouler votre projet et qui seront installées via [pip](https://www.w3schools.com/python/python_pip.asp) dans votre environnement. Vous aurez besoin d'une seule dépendance pour ce projet, `pytest` (pour exécuter les tests unitaires). Alors, créez un fichier `requirements.txt` dans le répertoire racine de votre projet :

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

Une fois le fichier `.env` créé et la variable existe, l'application sur `/src/calculator.py` est déjà préparée pour lire le `.env`, extraire la variable `CALCULATOR_USERNAME` et l'utiliser. Si vous faites votre propre application à partir de zéro, vous devriez écrire vous-même le code pour lire le `.env`, ou utiliser une librairie telle que [dotenv](https://www.geeksforgeeks.org/python/using-python-environment-variables-with-python-dotenv/) pour vous aider.

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

> 📝 **NOTE** : l'autocomplétion en appuyant sur Tab et les flèches du clavier ne marchent pas dans Docker Desktop, parce qu'il utilise une interface Bash simplifiée. Si vous n'aimez pas ça, vous pouvez également exécuter les commandes via [docker exec](https://docs.docker.com/reference/cli/docker/container/exec/) à partir de votre machine hôte (hors Docker).

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

### 2. Ajoutez une étape à la pipeline CI (intégration continue)

Ajoutez une étape (step) dans `.github/workflows/.github-ci.yml` pour que GitHub exécute les tests automatiquement à chaque push. 

### 3. Versionnez votre code

Si tous les tests passent :

```bash
git add .
git commit -m "Tests pour calculator.py"
git push
```

GitHub exécutera les tests dans son serveur, et ils devront passer également s'ils sont corrects.

> 💡 **Question 2** :  Que fait GitHub pendant les étapes de « setup » et « checkout » ? Veuillez inclure la sortie du terminal GitHub CI dans votre réponse.

### 4. Automatiser le déploiement continu (CD)
TODO: en construction

---

## 📦 Livrables

- Code compressé en `.zip` contenant **l'ensemble du code source** du projet Labo 00.
- Rapport `.pdf` répondant aux 4 questions présentées dans ce fichier. Il est **obligatoire** d'ajouter du code ou des sorties de terminal pour illustrer chacune de vos réponses