# Agent de recherche documentaire

> Agent IA capable de répondre en langage naturel à des questions sur un corpus d'articles scientifiques, en citant ses sources (architecture RAG).



## 1. Contexte

Les chercheurs, étudiants et ingénieurs doivent consulter un grand nombre de documents (articles, PDF, rapports) pour trouver une information précise. Cette recherche manuelle est longue et fastidieuse.

Les grands modèles de langage (LLM) savent répondre à des questions en langage naturel, mais ils ne connaissent pas les documents propres à l'utilisateur et peuvent **inventer des réponses** (hallucinations). L'approche **RAG** (*Retrieval-Augmented Generation*) répond à ce problème : l'agent va d'abord chercher les passages pertinents dans les documents, puis le LLM rédige sa réponse **à partir de ces passages uniquement**.

## 2. Problématique

> Comment permettre à un utilisateur d'interroger en langage naturel un ensemble de documents scientifiques et d'obtenir des réponses **fiables, précises et sourcées**, tout en limitant les hallucinations du LLM ?

Questions techniques sous-jacentes :
- Comment découper les documents pour que les passages retrouvés soient pertinents ?
- Quel modèle d'embeddings choisir pour représenter le texte ?
- Comment vérifier que les citations affichées correspondent bien à la réponse ?

## 3. Dataset

- **Nom :** Natural Language Processing Research Papers
- **Source :** Kaggle (auteur : subhamjain)
- **Contenu :** articles scientifiques du domaine du traitement automatique du langage (NLP)

| Colonne | Description |
|---|---|
| `article` | Texte complet de l'article |
| `abstract` | Résumé de l'article |
| `section_names` | Noms des sections de l'article |

**Utilisation dans le projet :**
- `article` : source de connaissances de l'agent (découpage en passages puis vectorisation) ;
- `section_names` : enrichissement des citations (indiquer de quelle section provient une réponse) ;
- `abstract` : référence pour vérifier la qualité des réponses.

> Le dataset complet n'est pas versionné dans Git, car il dépasse la limite de taille de fichier de GitHub. Téléchargez-le depuis Kaggle et placez `arxiv-metadata-oai-snapshot.json` dans `data/` avant de lancer le projet. Ce fichier est ignoré par Git.

## 4. Objectifs

**Objectif général :** concevoir un agent de recherche documentaire fonctionnel basé sur une architecture RAG.

**Objectifs spécifiques :**
1. Ingérer et nettoyer le corpus d'articles.
2. Découper les textes en passages et les convertir en vecteurs (embeddings).
3. Stocker les vecteurs dans une base vectorielle.
4. Retrouver les passages les plus proches d'une question.
5. Générer une réponse appuyée sur ces passages, avec **références aux sources**.
6. Évaluer la pertinence des réponses et des citations.
7. Proposer une interface simple pour la démonstration.

## 5. Fonctionnalités prévues

- Recherche en langage naturel dans le corpus d'articles
- Réponses rédigées par un LLM à partir des extraits retrouvés
- Citation des sources (titre ou identifiant de l'article, section, passage utilisé)
- Résumé des documents trouvés
- Indication claire quand l'information n'est pas présente dans le corpus
- Interface web simple pour poser des questions (Streamlit ou Gradio)

**Évolutions possibles (si le temps le permet) :**
- Historique de conversation (questions de suivi)
- Ajout de nouveaux documents PDF par l'utilisateur
- Filtrage par thème ou par section

## 6. Architecture

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

## 7. Technologies

| Besoin | Outil envisagé |
|---|---|
| Langage | Python |
| Orchestration RAG | LangChain ou LlamaIndex |
| Embeddings | sentence-transformers (ou API d'embeddings) |
| Base vectorielle | ChromaDB ou FAISS |
| LLM | Claude (API) ou autre LLM |
| Manipulation des données | pandas |
| Interface | Streamlit ou Gradio |

## 8. Évaluation

- **Pertinence de la recherche :** les passages retrouvés contiennent-ils la réponse ?
- **Fidélité de la réponse :** la réponse est-elle bien appuyée sur les passages cités ?
- **Qualité des citations :** la source affichée correspond-elle au contenu utilisé ?
- **Comparaison avec une référence :** utilisation des résumés (`abstract`) comme base de vérification.
- **Réglages testés :** taille des passages, nombre de passages retrouvés (top-k), choix du modèle d'embeddings.



## 10. Structure du projet

```
Agent-recherche-documentaire/
├── data/               # Dataset (ou extrait)
├── notebooks/          # Exploration et tests
├── src/                # Code source de l'agent
├── app.py              # Interface (Streamlit/Gradio)
├── requirements.txt    # Dépendances Python
└── README.md
```

## 11. Installation et utilisation

```bash
# Cloner le dépôt
git clone https://github.com/Salma-Gouihya/Agent-de-recherche-documentaire.git
cd Agent-de-recherche-documentaire

# Installer les dépendances
pip install -r requirements.txt

# Lancer l'application
streamlit run app.py
```

_Ces commandes seront ajustées au fur et à mesure de l'avancement du projet._


## 13. Limites et points d'attention

- La qualité des réponses dépend directement de la qualité du découpage et des embeddings.
- L'agent ne peut répondre qu'à partir des documents du corpus.
- Une validation humaine reste nécessaire pour un usage professionnel.

## 14. Références

- Dataset : *Natural Language Processing Research Papers*, Kaggle
- Principe RAG : Lewis et al., *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks* (2020)
