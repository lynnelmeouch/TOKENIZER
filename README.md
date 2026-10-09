# Tokenizer — Conception OO d'automates reconnaisseurs

Un **Tokenizer** écrit en Java orienté objet, qui utilise des **automates d'états finis (AEF)** pour découper un texte en *tokens* (identificateurs, mots-clés, types, chaînes, commentaires…) et les afficher **en couleur**, comme le fait un éditeur de code.

## Principe

1. Chaque catégorie de token est décrite par un **automate** au format `.dot` (Graphviz).
2. Chaque automate est implémenté par une classe Java `Rec*` (un *reconnaisseur*).
3. Le `Tokenizer` lit le texte et essaie les reconnaisseurs sur l'entrée.
4. Quand un reconnaisseur identifie un token, il émet une **notification** via un `Listener`.
5. Le `PrettyPrinter` reçoit ces notifications et affiche chaque token dans la couleur correspondant à sa catégorie.

```
Texte ──► Tokenizer ──► Reconnaisseurs (AEF) ──► Listener ──► PrettyPrinter ──► affichage coloré
```

Cette architecture (pattern *observateur*) sépare bien la **reconnaissance** des tokens de leur **affichage** : on peut ajouter un nouveau reconnaisseur sans toucher au PrettyPrinter, et inversement.

## Reconnaisseurs

| Classe | Automate | Reconnaît | Notification |
|---|---|---|---|
| `RecAny` | `any.dot` | tout mot de longueur 1 | `any(String)` |
| `RecABC` | `abc.dot` | le langage `a.(b.b*\|c)` | `abc(String)` |
| `RecCommand` | `Command.dot` | les commandes entre `` ` `` (sans saut de ligne) | `command(String)` / `error(...)` |
| `RecIdent` | `Ident.dot` | les identificateurs (lettres, `_`, chiffres, ne commençant pas par un chiffre) | `ident(String)`, `keyword(String)`, `type(String)` |
| `RecString` | `String.dot` | les chaînes entre `"` avec caractères échappés (`\"`, `\\`, `\n`), sans saut de ligne | `string(String)` |
| `RecComment` | `Comment.dot` | les commentaires C/Java (`// ...` et `/* ... */`) | `comment(String)` |

Un automate supplémentaire, `_aba.dot`, décrit le langage `(a|b)*.aba`.

### Détails

- **Identificateurs** : un identificateur commençant par une majuscule est classé comme **type** (`Object`, `Recognizer`), et ceux qui appartiennent à la liste des mots-clés (`if`, `else`, `return`…) comme **keyword**. `1_x` est rejeté.
- **Chaînes** : gère les séquences d'échappement et signale une erreur si la chaîne contient un saut de ligne.
- **Commentaires** : gère les commentaires sur une ou plusieurs lignes, y compris les cas piégeux comme `/* ... "*/" ... */`.

## Ma contribution

Le squelette du projet (le `Tokenizer`, le `Listener`, le `PrettyPrinter` et les reconnaisseurs d'exemple `RecAny`, `RecABC` et `RecCommand`) était fourni. J'ai réalisé les exercices demandés dans le TP :

1. **Automate `(a|b)*.aba`** : conception de l'AEF dans `_aba.dot`.
2. **Identificateurs** : conception de l'automate `Ident.dot` et implémentation de `RecIdent.java`, avec la distinction entre identificateurs, **mots-clés** et **types**.
3. **Chaînes de caractères** : conception de l'automate `String.dot` et implémentation de `RecString.java`, avec gestion des caractères échappés et détection des chaînes contenant un saut de ligne.
4. **Commentaires** : conception de l'automate `Comment.dot` et implémentation de `RecComment.java`, pour les commentaires `//` et `/* ... */` sur plusieurs lignes.

Pour chacun, j'ai intégré le nouveau reconnaisseur dans `Tokenizer.java` et vérifié son fonctionnement avec `make test` (cas valides) et `make error` (cas d'erreur).

## Utilisation

Prérequis : Java, `make`, et les outils `dot` (Graphviz), `sed` et `tar`.

| Commande | Effet |
|---|---|
| `make` | affiche la configuration et vérifie les outils |
| `make jpg` | génère les images des automates à partir des fichiers `.dot` |
| `make exec` | compile les `.java` en `.class` (dans `bin/`) |
| `make demo` | lance le Tokenizer sur ce README |
| `make test` | lance le Tokenizer sur les fichiers de `TEST/OK/` |
| `make error` | lance le Tokenizer sur les fichiers de `TEST/ERROR/` pour vérifier la détection d'erreurs |
| `make clean` | supprime les fichiers générés |

Exécution directe :

```bash
java -cp bin/ Recognizer.Tokenizer <fichier>
```

## Notions abordées

- Description d'automates d'états finis au format `.dot`
- Implémentation d'AEF dans un langage orienté objet
- Pattern observateur (`Listener` / `PrettyPrinter`)
- Application à la coloration syntaxique dans un éditeur de code

---

*Projet réalisé dans le cadre d'un TP universitaire.*