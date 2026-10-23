# AGENTS.md — Règles pour toute IA qui travaille sur ce dépôt

> Point d'entrée unique pour Claude, GPT/Codex, Gemini, Copilot, Cursor, etc.
> Lis ce fichier **en entier**, puis `PASSATION.md` (contexte) et `TACHES.md` (qui fait quoi).

## 1. Le projet en une phrase

Liste « awesome » des API LLM avec un **free tier permanent, sans carte bancaire**.
La source de vérité est `data.json` ; `README.md` est **généré** à partir de lui.

## 2. Règles non négociables

1. **Ne jamais éditer `README.md` à la main.** Modifier `data.json`, puis lancer
   `node scripts/generate-readme.js` et committer les deux.
2. **Une tâche = une branche = une PR.** Nom de branche : `<agent>/<id-tache>-<slug>`
   (ex. `claude/T03-skill-sync`). Jamais de push direct sur `main`.
3. **Réclamer avant de travailler** : passer la tâche à `EN COURS` avec ton nom dans
   `TACHES.md` (commit dédié sur ta branche, ou demande à l'humain de le faire).
   Ne touche pas une tâche réclamée par un autre agent.
4. **Ne pas modifier les fichiers « zone d'un autre »** listés dans la tâche en cours
   d'un autre agent. En cas de doute, demander à l'humain.
5. **Toute affirmation sur un free tier doit avoir une source** (URL de la doc officielle)
   dans la description de la PR. Pas de source = pas de changement.
6. **Sécurité** (voir incident dans `PASSATION.md` §6) :
   - Aucun secret / clé API dans le dépôt, les logs, les PR ou les rapports `.verify/`.
     Les clés vont dans `.env.verify` (ignoré par git).
   - Ne jamais ajouter ni modifier de workflow GitHub Actions (`.github/workflows/`)
     sans validation explicite de l'humain.
   - Aucun `curl`/requête vers une IP ou un domaine non lié à un fournisseur listé.
7. **Ne rien inventer.** Si un chiffre (contexte, RPM, RPD…) n'est pas vérifiable,
   on le laisse tel quel et on le signale dans la PR.

## 3. Format des données (`data.json`)

```jsonc
{
  "lastUpdated": "YYYY-MM-DD",
  "providers": [{
    "name": "Groq", "category": "provider_api | inference_provider",
    "country": "…", "flag": "🇺🇸", "url": "<page de clé API>",
    "baseUrl": "https://…/v1", "description": "Free tier…, no credit card.",
    "footnoteRef": 1,
    "models": [{ "id": "…", "name": "…", "context": "128K", "maxOutput": "32K",
                 "modality": "Text", "rateLimit": "30 RPM, 14,400 RPD" }]
  }],
  "footnotes": [...], "glossary": [...]
}
```

Notation : tailles `128K`, `1M`, `~32K` ; limites `30 RPM, 14,400 RPD` ; conditions en note numérotée.

## 4. Commandes utiles

| Action | Commande |
|---|---|
| Régénérer le README | `node scripts/generate-readme.js` |
| Vérifier que le README est à jour | `node scripts/generate-readme.js && git diff --exit-code README.md` |
| Valider le JSON | `python3 -m json.tool data.json > /dev/null` |
| Sonder les fournisseurs | `node scripts/verify-providers.js --out .verify/report-YYYY-MM-DD.json [--provider "Groq"]` |
| Auto-test du sondeur | `node scripts/verify-providers.js --self-test` |

Node ≥ 20, aucune dépendance npm.

## 5. Checklist avant d'ouvrir une PR

- [ ] `data.json` valide et README régénéré (diff vide après régénération)
- [ ] `lastUpdated` mis à jour si les données changent
- [ ] Sources officielles citées dans la PR
- [ ] Aucun secret dans le diff
- [ ] `TACHES.md` mis à jour (statut + lien PR)
- [ ] Entrée ajoutée au **journal** en bas de `PASSATION.md` (2-3 lignes max)
