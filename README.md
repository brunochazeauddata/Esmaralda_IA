# Esmaralda_IA

# 🌟 Projet Esméralda IA - Assistant Thérapeutique Intelligent

Ce dépôt contient l'interface web frontend et la documentation de l'architecture pour **Esméralda IA**, une assistante virtuelle personnalisée conçue pour l'accompagnement thérapeutique. 

Le système repose sur un couplage entre une interface web hébergée sur GitHub, un moteur de workflow (n8n), une base de données vectorielle et relationnelle (Supabase), et un modèle de langage (Google Gemini).

---

## 🛠️ Stack Technique et Architecture

*   **Frontend :** HTML5, CSS3 (Glassmorphism design), Vanilla JavaScript.
*   **Moteur d'automatisation :** n8n (Agent IA, Mémoire, Outils RAG).
*   **LLM & Embeddings :** Google Gemini (`gemini-1.5-pro` ou équivalent pour le chat, `gemini-embedding-001` pour la vectorisation).
*   **Base de Données & Mémoire :** Supabase (PostgreSQL pour l'historique des conversations, pgvector pour la recherche documentaire).
*   **Librairies externes :** 
    *   [Lucide Icons](https://lucide.dev/) (Icônes UI)
    *   [Marked.js](https://marked.js.org/) (Rendu du Markdown)

---

## 🚀 Fonctionnalités Principales

1.  **Chat en temps réel :** Dialogue fluide avec l'agent IA Esméralda via un webhook n8n.
2.  **Mémoire de session :** Conservation du contexte de la conversation grâce à un `sessionId` unique généré côté client et stocké dans le `localStorage`.
3.  **RAG (Retrieval-Augmented Generation) :** Capacité de l'IA à interroger une base de connaissances métier (documents stockés et vectorisés sur Supabase) avant de formuler une réponse.
4.  **Historique des conversations :** Récupération et affichage des anciennes sessions de chat directement depuis la base de données Supabase via son API REST.
5.  **Rendu Markdown natif :** Prise en charge de la mise en forme avancée (gras, italique, listes, sauts de ligne) dans les réponses de l'IA et dans l'historique.

---

## 📝 Historique des Implémentations et Correctifs (Changelog)

Au cours du développement, plusieurs défis techniques ont été résolus pour stabiliser le système :

### 1. Résolution de "l'amnésie" de l'IA (Perte de contexte)
*   **Problème :** L'IA oubliait le contexte après la première question. Le nœud de mémoire n8n créait une nouvelle session à chaque requête.
*   **Solution Frontend :** Ajout de la clé `action: "sendMessage"` dans le payload JSON envoyé au webhook. Cela permet au nœud *Chat Trigger* de n8n de reconnaître la requête comme provenant d'un widget de chat standard et d'extraire automatiquement le `sessionId`.
*   **Solution n8n :** Configuration du nœud *Postgres Chat Memory*. Le paramètre *Session ID* a été défini sur **"Connected chat trigger node"**, permettant une liaison dynamique et automatique de la mémoire.

### 2. Amélioration de l'UI et intégration du Markdown
*   **Problème :** Les réponses de l'IA (générées en Markdown) et l'historique s'affichaient sous forme de blocs de texte brut, sans formatage.
*   **Solution :**
    *   Importation de la bibliothèque **Marked.js** via CDN.
    *   Refonte CSS pour styliser les éléments Markdown (`.md-content p`, `strong`, `em`, `ul`, `li`).
    *   Mise à jour des fonctions JavaScript (`appendMessage` et `loadHistory`) pour utiliser `marked.parse(text)`, transformant ainsi la syntaxe IA en HTML propre et lisible.

### 3. Refonte de l'onglet Historique
*   **Amélioration :** Remplacement des anciens blocs "vert/bleu" par un design épuré, inspiré des interfaces conversationnelles modernes (type ChatGPT).
*   **Implémentation :** Ajout d'en-têtes de messages (`.msg-header`) avec des icônes distinctes pour l'utilisateur (👤) et l'IA (✨), et intégration du rendu Markdown pour la relecture des anciennes sessions.

### 4. Flux RAG (Recherche Documentaire)
*   Mise en place d'un sous-workflow n8n dédié :
    *   Réception de la question de l'utilisateur.
    *   Transformation de la requête en vecteur de dimension 384 via l'API Google Gemini Embeddings.
    *   Requête RPC (Remote Procedure Call) vers Supabase pour trouver les segments de texte les plus pertinents.
    *   Formatage et renvoi des données à l'Agent IA principal pour formuler une réponse sourcée et contextualisée.

---

## 📁 Structure du code Frontend

Le frontend tient dans un fichier unique `index.html` pour faciliter l'hébergement (ex: GitHub Pages). Il se divise en trois parties :
1.  **CSS (Style) :** Design responsive (Glassmorphism), adaptation mobile et gestion des "Safe Areas" pour iOS/Android.
2.  **HTML (Structure) :** Conteneur principal, onglets (Assistant / Historique), et zone de saisie.
3.  **JavaScript (Logique) :** 
    *   Génération/Récupération de l'UUID de session.
    *   Appels API vers le Webhook n8n (`sendMessage`).
    *   Appels API REST vers Supabase (`loadHistory`).
    *   Gestion du DOM, des animations et du parsing Markdown.

---

## 🔒 Sécurité et Variables
*   L'authentification à Supabase pour la lecture de l'historique se fait via une `anon_key` (clé publique) restreinte par les politiques RLS (Row Level Security) configurées côté base de données.
*   Les requêtes vers l'IA passent par n8n, gardant les clés d'API (Gemini, Supabase Service Role) sécurisées côté serveur.

---
*Projet développé avec passion pour l'accompagnement et l'évolution personnelle.* ✨
