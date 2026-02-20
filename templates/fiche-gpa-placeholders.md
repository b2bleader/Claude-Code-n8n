# Guide — Création du Template Google Doc Fiche GPA

## Objectif

Ce document explique comment créer le template Google Docs utilisé par le workflow n8n pour générer automatiquement les fiches d'intervention GPA.

---

## Étape 1 — Créer le document Google Docs

1. Aller sur [Google Drive](https://drive.google.com)
2. Créer un nouveau dossier : `GPA - Résidence ANTOINETTE`
   - Sous-dossier : `Fiches GPA` (pour les fiches générées)
   - Sous-dossier : `Quitus Signés` (pour l'archivage)
3. Dans le dossier racine, créer un nouveau Google Docs : **`TEMPLATE - Fiche GPA`**
4. **Récupérer l'ID du document** : visible dans l'URL `https://docs.google.com/document/d/**ID_ICI**/edit`
   → Reporter cet ID dans le workflow n8n (nœud "HTTP - Copier Template Google Doc")

---

## Étape 2 — Structure du document

Le document doit reproduire la structure de la fiche GPA en 4 blocs colorés. Utiliser exactement les placeholders ci-dessous (sensibles à la casse) :

---

### BLOC 1 — À remplir par le MOA (fond jaune)

```
FICHE D'INTERVENTION GPA
Résidence ANTOINETTE

N° de réserve : {{NUMERO_RESERVE}}
Date de la demande : {{DATE_DEMANDE}}
Opération : {{OPERATION}}

INFORMATIONS OCCUPANT
Nom : {{NOM_OCCUPANT}}
N° logement : {{LOT}} — Bâtiment {{BATIMENT}}
Email : {{EMAIL_OCCUPANT}}

DESCRIPTION DE LA RÉSERVE
Localisation : {{PIECE}}
Description : {{DESCRIPTION}}

Date limite d'intervention souhaitée : {{DATE_LIMITE}}
```

---

### BLOC 2 — À remplir par le MOEX (fond bleu)

```
SECTION MAÎTRE D'ŒUVRE D'EXÉCUTION (CONNEXION BÂTIMENT)

Entreprise mandatée : {{ENTREPRISE}}
Date limite d'intervention confirmée : ____________________
Nom du responsable MOEX : ____________________
Date : ____________________
Signature : ____________________
```

---

### BLOC 3 — À remplir par l'Entreprise (fond vert)

```
SECTION ENTREPRISE INTERVENANTE

Nom de l'intervenant : ____________________
Date d'intervention : ____________________
Description des travaux effectués : ____________________
____________________

Réserve levée : ☐ Oui  ☐ Non
Si non, motif : ____________________

Signature : ____________________
```

---

### BLOC 4 — À remplir par l'Occupant (fond orange)

```
SECTION OCCUPANT / QUITUS

Date : ____________________
Réserve levée à ma satisfaction : ☐ Oui  ☐ Non
Commentaires : ____________________
____________________

Signature : ____________________
```

---

## Tableau des Placeholders

| Placeholder | Rempli par | Exemple |
|-------------|------------|---------|
| `{{NUMERO_RESERVE}}` | n8n auto | `83` |
| `{{DATE_DEMANDE}}` | n8n auto | `12/11/2025` |
| `{{OPERATION}}` | n8n auto | `ANTOINETTE` |
| `{{NOM_OCCUPANT}}` | n8n (IA) | `M. AOFFER Salah` |
| `{{LOT}}` | n8n (IA) | `D205` |
| `{{BATIMENT}}` | n8n (IA) | `D` |
| `{{PIECE}}` | n8n (IA) | `Salle d'eau` |
| `{{DESCRIPTION}}` | n8n (IA) | `Paroi de douche à régler...` |
| `{{DATE_LIMITE}}` | n8n auto | `26/11/2025` |
| `{{ENTREPRISE}}` | n8n (IA) | `ATB` |
| `{{EMAIL_OCCUPANT}}` | n8n auto | `m.aoffer@email.fr` |

> **Important** : Écrire les placeholders EXACTEMENT comme indiqué, avec les doubles accolades `{{` et `}}`. La casse doit être respectée (MAJUSCULES).

---

## Étape 3 — Configuration dans n8n

Une fois le template créé, mettre à jour ces valeurs dans le workflow `gpa-reception-traitement.json` :

```
Nœud "HTTP - Copier Template Google Doc"
  → URL : remplacer REMPLACER_PAR_ID_TEMPLATE_GOOGLE_DOC par l'ID du template

Nœud "Google Drive - Archiver PDF Quitus"
  → driveId et folderId : remplacer par l'ID du dossier "Quitus Signés"

Nœud "HTTP - Copier Template Google Doc"
  → parents : remplacer REMPLACER_PAR_ID_DOSSIER_GDRIVE_FICHES par l'ID du dossier "Fiches GPA"
```

---

## Étape 4 — Credentials Google dans n8n

Créer un compte de service Google (ou OAuth2) avec les scopes :
- `https://www.googleapis.com/auth/drive`
- `https://www.googleapis.com/auth/documents`

Dans n8n → Credentials → Google Drive OAuth2 → noter l'ID de credential → mettre à jour `REMPLACER_PAR_ID_CREDENTIALS_GOOGLE` dans les deux workflows.

---

## Exemple de Fiche Générée

**Pour la réserve n°83 (M. AOFFER, lot D205) :**

```
FICHE D'INTERVENTION GPA
Résidence ANTOINETTE

N° de réserve : 83
Date de la demande : 12/11/2025
Opération : ANTOINETTE

INFORMATIONS OCCUPANT
Nom : M. AOFFER Salah
N° logement : D205 — Bâtiment D
Email : aoffer.salah@email.fr

DESCRIPTION DE LA RÉSERVE
Localisation : Salle d'eau
Description : Paroi de douche à régler, elle ne se ferme pas correctement au niveau du joint latéral

Date limite d'intervention souhaitée : 26/11/2025
```
