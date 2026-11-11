# Agent de recherche documentaire

> Agent IA capable de répondre en langage naturel à des questions portant sur un corpus d'articles scientifiques, en citant ses sources grâce à une architecture RAG (*Retrieval-Augmented Generation*).

| | |
|---|---|
| **Catégorie** | Connaissance et RAG |
| **Niveau** | Intermédiaire |
| **Membre A** | Zoubida ELACHARI |
| **Membre B** | Salma GOUIHYA |


## 1. Contexte

Chercheurs, étudiants et ingénieurs doivent consulter un très grand nombre de documents (articles scientifiques, PDF, rapports) pour retrouver une information précise. Cette recherche manuelle est longue, répétitive et source d'erreurs : il faut lire de nombreux textes, comparer les sources et repérer les passages utiles.

Les grands modèles de langage (LLM) permettent de poser des questions en langage naturel, mais ils présentent deux limites importantes :

- ils **ne connaissent pas** les documents propres à l'utilisateur ;
- ils peuvent **produire des réponses fausses mais convaincantes** (on parle d'hallucinations).

L'approche **RAG** répond à ces deux limites : avant de répondre, l'agent va chercher dans les documents les passages pertinents, puis le LLM rédige sa réponse en s'appuyant sur ces passages. Chaque réponse peut ainsi être vérifiée grâce aux sources citées.

## 2. Problématique

> Comment permettre à un utilisateur d'interroger en langage naturel un ensemble de documents scientifiques et d'obtenir des réponses **fiables, précises et sourcées**, tout en limitant les hallucinations du LLM ?

Cette problématique soulève plusieurs questions techniques :

- Comment **découper** les articles pour que les passages retrouvés soient pertinents et ni trop courts ni trop longs ?
- Quel **modèle d'embeddings** choisir pour représenter fidèlement le sens du texte ?
- Combien de passages faut-il fournir au LLM pour obtenir une bonne réponse ?
- Comment s'assurer que les **citations** affichées correspondent réellement au contenu de la réponse ?
- Comment faire en sorte que l'agent **reconnaisse** qu'il ne trouve pas la réponse dans le corpus ?

## 3. Présentation du projet

Le projet consiste à concevoir et développer un **agent de recherche documentaire**. L'utilisateur pose une question en langage naturel (par exemple : « Quelles méthodes sont utilisées pour la classification de textes ? »). L'agent :

1. cherche dans le corpus les passages qui se rapprochent le plus de la question ;
2. transmet ces passages au LLM ;
3. affiche une réponse rédigée, accompagnée des **références** des articles utilisés.

Le projet s'appuie sur un corpus d'articles scientifiques en NLP, ce qui permet de tester l'agent sur des textes longs, techniques et structurés en sections.

## 4. Dataset

- **Nom :** Natural Language Processing Research Papers
- **Source :** Kaggle (auteur : subhamjain)
- **Contenu :** articles scientifiques du domaine du traitement automatique du langage (NLP)

| Colonne | Description | Utilisation dans le projet |
|---|---|---|
| `article` | Texte complet de l'article | Base de connaissances de l'agent (découpage puis vectorisation) |
| `abstract` | Résumé de l'article | Référence pour vérifier la qualité des réponses |
| `section_names` | Noms des sections de l'article | Enrichissement des citations (section d'origine du passage) |

> Le fichier de données se trouve dans le dossier `data/`. S'il est trop volumineux pour GitHub (limite de 100 Mo), il est remplacé par un extrait et le lien de téléchargement Kaggle est indiqué.

## 5. Objectifs

**Objectif général :** concevoir un agent de recherche documentaire fonctionnel, basé sur une architecture RAG, capable de répondre avec des sources vérifiables.

**Objectifs spécifiques :**

1. Explorer, nettoyer et préparer le corpus d'articles.
2. Découper les textes en passages (*chunks*) adaptés à la recherche.
3. Convertir les passages en vecteurs (embeddings) et les stocker dans une base vectorielle.
4. Retrouver les passages les plus proches d'une question (*retrieval*).
5. Générer une réponse appuyée sur ces passages, avec citation des sources.
6. Évaluer la pertinence de la recherche, des réponses et des citations.
7. Proposer une interface simple pour utiliser et présenter l'agent.

## 6. Fonctionnement de l'agent

Le fonctionnement se déroule en deux phases.

### Phase 1 : préparation du corpus (réalisée une fois)

1. **Chargement** des articles depuis le dataset.
2. **Nettoyage** du texte (caractères parasites, doublons, textes vides).
3. **Découpage** de chaque article en passages, en conservant le titre ou l'identifiant de l'article et la section d'origine.
4. **Vectorisation** : chaque passage est transformé en vecteur par un modèle d'embeddings.
5. **Stockage** des vecteurs et de leurs métadonnées dans une base vectorielle.

### Phase 2 : réponse à une question (à chaque requête)

1. L'utilisateur saisit sa question.
2. La question est vectorisée avec le même modèle d'embeddings.
3. La base vectorielle renvoie les *k* passages les plus proches.
4. Ces passages et la question sont envoyés au LLM avec une consigne claire : répondre uniquement à partir des extraits fournis.
5. L'agent affiche la réponse accompagnée des sources utilisées.

```
Documents ──► Nettoyage ──► Découpage ──► Embeddings ──► Base vectorielle
                                                              │
Question ──► Embedding de la question ──► Recherche (top-k) ◄─┘
                                              │
                                  Passages pertinents + question
                                              │
                                              ▼
                                   LLM ──► Réponse + sources
```

## 7. Fonctionnalités prévues

**Fonctionnalités principales**

- Recherche en langage naturel dans le corpus d'articles.
- Réponses rédigées par un LLM à partir des extraits retrouvés.
- Citation des sources : article, section et passage utilisé.
- Résumé des documents pertinents.
- Message explicite lorsque l'information n'est pas présente dans le corpus.
- Interface web simple pour poser des questions et consulter les sources.

**Évolutions possibles (selon l'avancement)**

- Historique de conversation avec questions de suivi.
- Ajout de nouveaux documents PDF par l'utilisateur.
- Filtrage des recherches par thème ou par section.

## 8. Technologies utilisées

| Besoin | Outil envisagé |
|---|---|
| Langage | Python |
| Orchestration RAG | LangChain ou LlamaIndex |
| Embeddings | sentence-transformers (ou API d'embeddings) |
| Base vectorielle | ChromaDB ou FAISS |
| LLM | Claude (API) ou autre LLM |
| Manipulation des données | pandas |
| Interface | Streamlit ou Gradio |
| Versionnement | Git et GitHub |

## 9. Répartition des tâches

Le projet est réalisé en binôme. Le travail est divisé en deux grands blocs complémentaires : le **traitement des données** (Membre A) et l'**agent de questions-réponses** avec son interface (Membre B). Les deux blocs se rejoignent au niveau de la base vectorielle.

### Membre A : Zoubida ELACHARI — Données et recherche (retrieval)

**Responsabilité :** préparer le corpus et construire le moteur de recherche qui retrouve les bons passages.

- Explorer le dataset et analyser sa structure (colonnes, tailles, qualité des textes).
- Nettoyer et préparer les articles.
- Définir la stratégie de découpage en passages (taille, chevauchement) et l'implémenter.
- Choisir le modèle d'embeddings et générer les vecteurs.
- Mettre en place la base vectorielle (ChromaDB ou FAISS) avec les métadonnées (article, section).
- Développer la fonction de recherche qui renvoie les *k* passages les plus pertinents.
- Tester et régler la qualité de la recherche (taille des passages, valeur de *k*).
- Rédiger dans le rapport les parties : dataset, préparation des données, découpage, embeddings, base vectorielle.

**Livrable du Membre A :** un module de recherche prêt à l'emploi : on lui donne une question, il renvoie les meilleurs passages avec leurs sources.

### Membre B : Salma GOUIHYA — Agent, génération et interface

**Responsabilité :** construire l'agent qui rédige les réponses sourcées et l'interface pour l'utiliser.

- Intégrer le LLM et configurer l'appel via l'API.
- Concevoir le prompt : répondre uniquement à partir des extraits, citer les sources, signaler quand l'information est absente.
- Assembler la chaîne complète : question, recherche, génération, réponse avec références.
- Gérer la mise en forme des citations (article, section, passage).
- Développer l'interface (Streamlit ou Gradio) : saisie de la question, affichage de la réponse et des sources.
- Mettre en place l'évaluation : jeu de questions de test, vérification de la fidélité des réponses et des citations.
- Rédiger dans le rapport les parties : architecture de l'agent, prompt, interface, évaluation et résultats.

**Livrable du Membre B :** l'agent complet avec son interface et les résultats d'évaluation.

### Tâches communes

- Définir ensemble l'architecture générale et le format d'échange entre le module de recherche (A) et l'agent (B).
- Tester l'ensemble du système et corriger les problèmes d'intégration.
- Rédiger l'introduction, la conclusion et le README.
- Préparer la démonstration finale et la présentation.
- Gérer le dépôt GitHub : commits réguliers, une branche par fonctionnalité si besoin.

### Interface entre les deux parties

Le Membre A fournit au Membre B une fonction de recherche dont le résultat est une liste de passages, chacun avec : le **texte**, l'**identifiant de l'article**, la **section** et un **score de similarité**. Le Membre B utilise cette liste pour construire le prompt et afficher les sources.

## 10. Évaluation

L'agent est évalué selon plusieurs critères :

- **Pertinence de la recherche :** les passages retrouvés contiennent-ils bien l'information demandée ?
- **Fidélité de la réponse :** la réponse est-elle appuyée sur les passages fournis, sans information inventée ?
- **Qualité des citations :** la source affichée correspond-elle au contenu utilisé ?
- **Gestion des cas sans réponse :** l'agent indique-t-il qu'il ne sait pas lorsque le corpus ne contient pas l'information ?
- **Comparaison avec une référence :** utilisation des résumés (`abstract`) pour vérifier la cohérence des réponses.
- **Réglages testés :** taille des passages, nombre de passages retrouvés (*k*), choix du modèle d'embeddings.

## 11. Structure du projet

```
Agent-recherche-documentaire/
├── data/               # Dataset (ou extrait)
├── notebooks/          # Exploration et tests
├── src/
│   ├── preparation.py  # Nettoyage et découpage (Membre A)
│   ├── retrieval.py    # Embeddings et recherche (Membre A)
│   ├── agent.py        # Prompt, LLM et réponses sourcées (Membre B)
│   └── evaluation.py   # Évaluation (Membre B)
├── app.py              # Interface Streamlit/Gradio (Membre B)
├── requirements.txt    # Dépendances Python
└── README.md
```

## 12. Installation et utilisation

```bash
# Cloner le dépôt
git clone https://github.com/Salma-Gouihya/Agent-de-recherche-documentaire.git
cd Agent-de-recherche-documentaire

# Installer les dépendances
pip install -r requirements.txt

# Lancer l'application
streamlit run app.py
```

_Ces commandes seront ajustées au fur et à mesure de l'avancement du projet. Une clé d'API pour le LLM sera nécessaire (à stocker dans un fichier `.env`, jamais dans le dépôt)._



## 14. Limites et perspectives

**Limites**

- La qualité des réponses dépend directement de la qualité du découpage et des embeddings.
- L'agent ne peut répondre qu'à partir des documents du corpus.
- Un LLM peut encore commettre des erreurs : une vérification humaine reste nécessaire pour un usage professionnel.

**Perspectives**

- Ajouter d'autres types de documents (PDF personnels, pages web).
- Améliorer la recherche en combinant recherche par mots-clés et recherche vectorielle.
- Ajouter un mécanisme de reclassement des passages pour affiner la pertinence.

## 15. Références

- Dataset : *Natural Language Processing Research Papers*, Kaggle
- Principe RAG : Lewis et al., *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks* (2020)
