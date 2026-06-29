# [title:mr gitlab]travailler sur une branche isolée, puis demander sa fusion dans une branche principale après relecture.

Agent wf-Manager
phase 0 loop planner
Phase 1

Phase 0
Loop agent planner
Start
Agent planner
https://awesome-copilot.github.com/agents/#file=agents%2Fprd.agent.md

Create a well-described issue with clear acceptance criteria

1 Goal > 2 create user story >
Agent checker
Si la user story ne correspond pas au template voulu alors retry
End

Phase 1
Agent issue mr
ouvrir une merge request distante en draft avec la story> recuperer la branche de la mr en local >

Phase 2
Assigne l issue au Tdd loop agent

Loop agent tdd
Start
Creer les tdd red a partir de l issue
Laisser le loop pick en priorite le test a passer

verifier et charger si le fichier state si existant >
Creer une implementation pour que le tdd green pass en tant que maker > run green test some conditions en tant que checker > commit > sauvegarde l etat state de la loop.
Loop writes outcomes, timestamps, last actions
Continuous log

> iterate x5 (nb attempts) > eval
> Fin de loop

Tdd refactor phase

> sauvegarde checkpoint de session >

# # # agent reviewer autre que # # implementer

9 review > mesure >10 mark mr as ready >11 review finale >12 merge and close mr

Comment structurer cela dans la description de la MR ?

​Il ne s'agit pas de coller tout votre historique de chat (ce qui rendrait la MR illisible), mais de mettre une section "🤖 AI Implementation Context" compacte tout en bas de votre description de MR.

## 🤖 Contexte d'implémentation IA

Cette fonctionnalité a été développée en 0 collaboration avec [Copilot / Cursor / Agent personnel].

### 📌 Plan d'implémentation suivi

1. [x] Analyse de l'existant et extraction du schéma AST.
2. [x] Création du middleware Fastify pour l'injection du contexte.
3. [x] Isolation des logs dans le conteneur Docker (Port 3000).
4. [ ] _(Étape optionnelle / Future)_ : Mise en cache des sessions de contexte.

### 💡 Prompts clés & Hypothèses

- **Modèle utilisé :** GPT-4o / Claude 3.5 Sonnet via [Outil]
- **Contrainte imposée :** Approche delta-neutre sur la gestion des états, interdiction de modifier le core des modèles existants.
- **Raisonnement technique :** L'IA a proposé d'isoler la logique dans un hook custom plutôt qu'un middleware global pour éviter les ré-allocations mémoire à chaque requête (gain estimé: ~15%).

### 🔄 Checkpoint de session

Si vous devez reprendre ce travail avec un assistant IA, utilisez le fichier de contexte attaché ou injectez ce résumé :

> "L'architecture utilise Fastify + TypeScript containerisé. Le point bloquant sur le cast des UUID a été résolu à l'étape 3 via un helper natif."
