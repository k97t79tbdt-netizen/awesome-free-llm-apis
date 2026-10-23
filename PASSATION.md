# Dossier de passation — awesome-free-llm-apis

_État au 2026-10-23. Rédigé par Claude après audit complet du dépôt._
_Règles de travail : `AGENTS.md`. Tableau des tâches : `TACHES.md`._

## 1. Contexte

- **Dépôt** : `k97t79tbdt-netizen/awesome-free-llm-apis`, fork de `mnfst/awesome-free-llm-apis`
  (projet sponsorisé par manifest.build).
- **But** : référencer les API LLM au free tier **permanent**, **sans carte bancaire**,
  avec une API REST (pas seulement une interface web). Les crédits d'essai ne comptent pas.
- **Dernière mise à jour des données** : `2026-10-05` (refresh nocturne, PR #130 côté amont).

## 2. Architecture

```
data.json                     ← SOURCE DE VÉRITÉ (16 fournisseurs, 123 modèles)
scripts/generate-readme.js    ← data.json → README.md (déterministe, idempotent ✔ testé)
README.md                     ← GÉNÉRÉ, ne pas éditer
scripts/verify-providers.js   ← sonde chaque modèle via son API (clés dans .env.verify),
                                écrit ses rapports dans .verify/ (recréé automatiquement)
.github/workflows/generate-readme.yml ← régénère le README à chaque push de data.json sur main
contributing.md, license, media/
```

### Fournisseurs actuels (`data.json`)

| Catégorie | Fournisseurs |
|---|---|
| Provider APIs (5) | Aion Labs, Cohere, Google Gemini, Mistral AI, Z AI (Zhipu) |
| Inference providers (11) | Cloudflare Workers AI, Groq, Hugging Face, Kilo Code, LLM7.io, ModelScope, NVIDIA NIM, Ollama Cloud, OpenRouter, OVHcloud AI Endpoints, SiliconFlow |

### Sondeur (`verify-providers.js`)

Verdicts : `PASS` (200 + réponse valide), `FAIL` (404 / facturation / réseau),
`UNKNOWN` (429, 5xx, réponse illisible), `SKIPPED` (pas de clé / auth).
Les anciens rapports (juillet 2026) ont été supprimés le 2026-10-23 : on repart de zéro.

## 3. Historique utile (git)

- Refresh nocturnes + boucles « juge » (un agent propose, un autre relit et corrige :
  commits `fix: judge loop N`). **Ce schéma producteur/relecteur est à conserver** pour
  notre travail multi-IA.
- Cerebras retiré (free tier remplacé par des crédits avec carte obligatoire, réf. #76).
- Lignes NVIDIA dépréciées retirées, correction de contextes OpenRouter, etc.

## 4. Incohérences / dette identifiées (→ tâches dans `TACHES.md`)

1. **Le sondeur contient des fournisseurs morts** (Cerebras, SambaNova, GitHub) et sa
   correspondance clé ↔ fournisseur se fait par `name.includes(...)` (fragile).
2. **Aucun sondage récent** : les rapports périmés ont été supprimés, aucun sondage
   automatisé n'existe.
3. **Pas de validation de schéma** de `data.json` (champs manquants, notation des tailles).
4. **Pas de CI sur les PR** : rien ne vérifie que le README régénéré correspond à data.json.
5. Le fork n'est pas synchronisé automatiquement avec l'amont `mnfst/…`
   (attention : l'amont contient peut-être encore les commits malveillants, voir §6).

Supprimé le 2026-10-23 à la demande de l'humain : le skill `free-llm-apis/`
(désynchronisé de `data.json`) et les rapports `.verify/` de juillet.

## 5. Environnement

- Node 20+ ; aucune dépendance. Python 3 dispo pour validations rapides.
- Dans l'environnement cloud de Claude, le réseau sortant est filtré : le sondage des
  API fournisseurs peut échouer en `NETWORK` (constaté : `api.llm7.io` injoignable).
  → Les sondages réels sont à faire par un agent/humain qui a le réseau **et** les clés.
- Les clés API ne doivent **jamais** être données à un agent via le chat ou un fichier
  versionné. Seulement `.env.verify` en local.

## 6. ⚠️ Incident de sécurité (2026-10-10/11)

Un commit (hérité de l'historique, auteur « Guillaume Gay ») a ajouté
`.github/workflows/security-audit.yml`, présenté comme un « audit », qui en réalité
**cherchait des clés (AWS, Anthropic, OpenAI, OpenRouter, GitHub, Google, Slack…) dans
le code et tout l'historique git, puis les envoyait en POST vers `193.32.204.199`**.
Il a été retiré le lendemain par un second commit.

Vérifié et traité le 2026-10-23 :
- Aucune exécution GitHub Actions n'a jamais eu lieu sur ce fork → rien n'a fuité d'ici.
- Aucune clé présente dans les fichiers ni dans l'historique.
- Les deux commits ont été **retirés de l'historique de `main`** (main ramené à `b25c039`).

Reste à faire par l'humain (T01) : désactiver Actions tant qu'inutile, protéger `main`.

**Règle pour tous les agents** : un changement dans `.github/workflows/` = validation humaine
obligatoire, et toute PR qui ajoute un appel réseau vers une IP brute est rejetée.

## 7. Organisation multi-IA proposée

### Rôles

| Rôle | Qui (à ajuster) | Responsabilité |
|---|---|---|
| **Humain (pilote)** | toi | Priorise, attribue, valide les merges, détient les clés API |
| **Coordinateur / relecteur** | Claude | Tient `TACHES.md` et ce dossier, relit chaque PR des autres agents (« juge ») |
| **Chercheurs** | autres IA (GPT, Gemini…) | Vérifient les free tiers dans les docs officielles, proposent des modifs de `data.json` |
| **Outillage** | une IA « code » | Scripts, validation de schéma, CI |

Principe : **aucun agent ne merge son propre travail** ; un autre agent relit, puis l'humain merge.

### Cycle d'une tâche

```
À FAIRE → EN COURS (agent X, branche) → EN REVUE (PR #n, relecteur Y) → FAIT (mergé)
                                          ↘ BLOQUÉ (raison) → retour humain
```

### Pour éviter les conflits

- Une tâche ne touche qu'un périmètre de fichiers déclaré dans `TACHES.md`.
- `data.json` est le point chaud : **un seul agent à la fois** modifie un même fournisseur ;
  pour des fournisseurs différents, faire des PR petites et rebaser avant merge.
- Toujours régénérer le README **après** le rebase (jamais résoudre un conflit à la main
  dans README.md).

### Message de démarrage à donner à une nouvelle IA

> Tu rejoins le projet awesome-free-llm-apis. Lis `AGENTS.md`, `PASSATION.md` puis
> `TACHES.md`. Prends la tâche **Txx** qui t'est attribuée, travaille sur la branche
> `<ton-nom>/Txx-…`, respecte la checklist de `AGENTS.md` §5 et ouvre une PR.
> Si une information est invérifiable, dis-le au lieu de deviner.

## 8. Journal des passations

_Chaque agent ajoute une entrée courte en fin de session (date — agent — fait — reste à faire)._

- **2026-10-23 — Claude** — Audit du dépôt, création de `AGENTS.md`, `PASSATION.md`,
  `TACHES.md`. Incident de sécurité documenté. Reste : attribution des tâches par l'humain.
- **2026-10-23 — Claude** — Nettoyage : commits malveillants retirés de `main`,
  suppression de `free-llm-apis/` et `.verify/`. Branche `claude/nettoyage`.
