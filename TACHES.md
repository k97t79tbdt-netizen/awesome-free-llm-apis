# Tableau des tâches

Statuts : `À FAIRE` · `EN COURS` · `EN REVUE` · `BLOQUÉ` · `FAIT`
Règles : voir `AGENTS.md`. Mettre à jour ce fichier à chaque changement de statut.

| ID | Tâche | Priorité | Fichiers touchés | Assigné à | Relecteur | Statut | PR |
|---|---|---|---|---|---|---|---|
| T01 | Vérifier l'incident sécurité : le workflow malveillant a-t-il tourné ? Révoquer les clés exposées, protéger `main` | 🔴 Haute | (paramètres GitHub) | **Humain** | Claude | À FAIRE | — |
| T02 | Ajouter une CI sur les PR : JSON valide + README régénéré identique | 🔴 Haute | `.github/workflows/check.yml` (validation humaine requise) | | | À FAIRE | — |
| T03 | Resynchroniser le skill `free-llm-apis/` avec `data.json` (retirer Cerebras, GitHub Models, Kluster ; ajouter les fournisseurs manquants) | 🟠 Moyenne | `free-llm-apis/**` | | | À FAIRE | — |
| T04 | Nettoyer `verify-providers.js` : retirer Cerebras/SambaNova/GitHub, mapping clé ↔ fournisseur explicite | 🟠 Moyenne | `scripts/verify-providers.js` | | | À FAIRE | — |
| T05 | Validation de schéma de `data.json` (champs requis, notation `128K`, `RPM/RPD`) | 🟠 Moyenne | `scripts/validate-data.js` | | | À FAIRE | — |
| T06 | Audit des free tiers – Provider APIs (Aion, Cohere, Gemini, Mistral, Z AI) : vérifier dans les docs officielles | 🟠 Moyenne | `data.json` (ces 5 fournisseurs) | | | À FAIRE | — |
| T07 | Audit des free tiers – Inference providers lot A (Cloudflare, Groq, Hugging Face, Kilo Code, LLM7.io, ModelScope) | 🟠 Moyenne | `data.json` (ces 6 fournisseurs) | | | À FAIRE | — |
| T08 | Audit des free tiers – Inference providers lot B (NVIDIA NIM, Ollama Cloud, OpenRouter, OVHcloud, SiliconFlow) | 🟠 Moyenne | `data.json` (ces 5 fournisseurs) | | | À FAIRE | — |
| T09 | Sondage réel des modèles avec les clés (nécessite réseau + `.env.verify`) et mise à jour de `.verify/` | 🟡 Basse | `.verify/**` | Humain ou agent local | | À FAIRE | — |
| T10 | Recherche de nouveaux fournisseurs éligibles (free permanent, sans CB, API REST) | 🟡 Basse | `data.json` (nouvelles entrées seulement) | | | À FAIRE | — |
| T11 | Synchroniser le fork avec l'amont `mnfst/awesome-free-llm-apis` | 🟡 Basse | tout | Humain | | À FAIRE | — |

## Dépendances

- T02 et T05 avant les gros audits (T06–T08) : la CI attrapera les erreurs de format.
- T03 après T06–T08 (le skill doit refléter les données vérifiées).
- T06, T07, T08 peuvent tourner **en parallèle** (fournisseurs disjoints).
