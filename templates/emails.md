# Templates Emails — Process GPA Résidence ANTOINETTE

Tous les emails utilisés dans les workflows n8n GPA. Les variables entre `{{ }}` sont les expressions n8n injectées automatiquement.

---

## 1. Accusé de Réception — Client (Réserve Recevable)

**Déclencheur** : Nouveau signalement reçu, analyse IA → recevable = true
**Expéditeur** : sav.antoinette@fayatimmobilier.com
**Destinataire** : Email de l'occupant

**Sujet** :
```
Réception de votre demande GPA — Lot {{ $json.lot }} — Résidence ANTOINETTE
```

**Corps** :
```
Bonjour {{ $json.nom_client }},

Nous accusons bonne réception de votre signalement concernant le logement
{{ $json.lot }} ({{ $json.piece }}) :
« {{ $json.description }} »

Votre dossier a été enregistré sous le numéro de réserve GPA n°{{ $json.id_reserve }}.
Nous avons transmis votre demande à notre Maître d'Œuvre d'Exécution qui va prendre
contact avec vous pour organiser l'intervention dans les meilleurs délais.

Cordialement,
Fayat Immobilier — Service GPA Résidence ANTOINETTE
sav.antoinette@fayatimmobilier.com
```

---

## 2. Refus — Client (Réserve Non Recevable)

**Déclencheur** : Nouveau signalement reçu, analyse IA → recevable = false
**Expéditeur** : sav.antoinette@fayatimmobilier.com
**Destinataire** : Email de l'occupant

**Sujet** :
```
Votre demande GPA — Lot {{ $json.lot }} — Résidence ANTOINETTE
```

**Corps** :
```
Bonjour {{ $json.nom_client }},

Nous avons bien reçu votre message concernant :
« {{ $json.description }} »

Après analyse, ce désordre ne relève pas de la Garantie de Parfait Achèvement
({{ $json.motif_non_recevable }}).

Il n'est donc pas possible de donner suite à cette demande dans le cadre de
la garantie légale d'un an. Si vous pensez que ce refus est une erreur,
n'hésitez pas à nous recontacter avec des informations complémentaires.

Cordialement,
Fayat Immobilier — Service GPA Résidence ANTOINETTE
sav.antoinette@fayatimmobilier.com
```

---

## 3. Transmission au MOEX — Nouvelle Fiche GPA

**Déclencheur** : Réserve recevable, fiche PDF générée
**Expéditeur** : sav.antoinette@fayatimmobilier.com
**Destinataire** : souys2.3@connexionbatiment.fr (Clémence LIQUARD, CONNEXION BÂTIMENT)
**Pièces jointes** : Fiche GPA n°{id}.pdf

**Sujet** :
```
Réserve GPA n°{{ $json.id_reserve }} — Résidence ANTOINETTE — Lot {{ $json.lot }} — {{ $json.piece }}
```

**Corps (cas standard)** :
```
Bonjour Clémence,

Veuillez trouver ci-joint la fiche d'intervention GPA n°{{ $json.id_reserve }}
concernant le logement {{ $json.lot }} ({{ $json.piece }}).

Merci de bien vouloir mandater l'entreprise compétente ({{ $json.entreprise }})
et nous retourner la fiche complétée et signée avant le {{ $json.date_limite }}.

Cordialement,
Fayat Immobilier — Service GPA
```

**Corps (cas URGENCE SÉCURITÉ)** :
```
Bonjour Clémence,

⚠️ URGENCE SÉCURITÉ — Intervention à planifier sous 48h.

Veuillez trouver ci-joint la fiche d'intervention GPA n°{{ $json.id_reserve }}
concernant le logement {{ $json.lot }} ({{ $json.piece }}).

Ce désordre présente un risque immédiat pour la sécurité des occupants.
Merci de mandater l'entreprise ({{ $json.entreprise }}) en urgence absolue.

Date limite d'intervention : {{ $json.date_limite }}

Cordialement,
Fayat Immobilier — Service GPA
```

---

## 4. Relance 1 au MOEX (J+7, ton poli)

**Déclencheur** : Workflow Relances — ancienneté ≥ 7 jours, aucune relance envoyée
**Expéditeur** : sav.antoinette@fayatimmobilier.com
**Destinataire** : souys2.3@connexionbatiment.fr

**Sujet** :
```
[RELANCE 1] Réserve GPA n°{{ $json.id_reserve }} — Résidence ANTOINETTE — Lot {{ $json.lot }}
```

**Corps** :
```
Bonjour Clémence,

Sauf erreur de notre part, nous n'avons pas reçu de retour concernant la
réserve GPA n°{{ $json.id_reserve }} (lot {{ $json.lot }} — {{ $json.piece }}),
transmise le {{ $json.date_diffusion }}.

Cette réserve est en attente depuis {{ $json.nb_jours }} jours.

Pouvez-vous nous confirmer l'avancement de l'intervention et nous retourner
la fiche complétée et signée dans les meilleurs délais ?

Cordialement,
Fayat Immobilier — Service GPA Résidence ANTOINETTE
```

---

## 5. Relance 2 au MOEX (J+14, ton ferme)

**Déclencheur** : Workflow Relances — ancienneté ≥ 14 jours, relance 2 non envoyée
**Expéditeur** : sav.antoinette@fayatimmobilier.com
**Destinataire** : souys2.3@connexionbatiment.fr

**Sujet** :
```
[URGENT — RELANCE 2] Réserve GPA n°{{ $json.id_reserve }} — Résidence ANTOINETTE — Lot {{ $json.lot }}
```

**Corps** :
```
Bonjour,

La réserve GPA n°{{ $json.id_reserve }} (lot {{ $json.lot }} — {{ $json.piece }})
est sans retour depuis {{ $json.nb_jours }} jours (transmise le {{ $json.date_diffusion }}).

Merci de traiter ce dossier en urgence et de nous retourner la fiche complétée
et signée dans les meilleurs délais.

Sans retour de votre part sous 48h, nous serons contraints d'escalader ce dossier.

Fayat Immobilier — Service GPA Résidence ANTOINETTE
```

---

## 6. Alerte Interne Fayat (J+14, vers responsable interne)

**Déclencheur** : Même déclencheur que Relance 2
**Expéditeur** : sav.antoinette@fayatimmobilier.com
**Destinataire** : Responsable interne Fayat (à configurer)

**Sujet** :
```
[ALERTE GPA] Réserve n°{{ $json.id_reserve }} sans retour depuis {{ $json.nb_jours }} jours
```

**Corps** :
```
Bonjour,

ATTENTION : La réserve GPA n°{{ $json.id_reserve }} n'a toujours pas de retour
du MOEX après {{ $json.nb_jours }} jours.

• Lot : {{ $json.lot }}
• Localisation : {{ $json.piece }}
• Description : {{ $json.description }}
• Date de transmission initiale : {{ $json.date_diffusion }}
• Entreprise concernée : {{ $json.entreprise }}
[SI URGENCE] • ⚠️ Cette réserve a été marquée comme URGENCE SÉCURITÉ.

Relance automatique envoyée à CONNEXION BÂTIMENT.
Merci de relancer Clémence LIQUARD (souys2.3@connexionbatiment.fr) directement
si vous n'avez pas de retour sous 24h.

Service GPA Automatisé — Résidence ANTOINETTE
```

---

## 7. Notification Interne Clôture (Quitus Reçu)

**Déclencheur** : Email du MOEX avec PDF signé détecté → réserve clôturée
**Expéditeur** : sav.antoinette@fayatimmobilier.com
**Destinataire** : Responsable interne Fayat

**Sujet** :
```
[GPA CLÔTURÉE] Réserve n°{{ $json.id_reserve }} — Lot {{ $json.lot }}
```

**Corps** :
```
Bonjour,

La réserve GPA n°{{ $json.id_reserve }} (lot {{ $json.lot }} — {{ $json.piece }})
a été clôturée automatiquement.

Le quitus signé a été archivé dans Google Drive (dossier Quitus Signés).

• Date de clôture : {{ $now.format('dd/MM/yyyy') }}
• Durée totale de traitement : calculée automatiquement dans Excel

Le tableau de suivi Excel a été mis à jour.

Service GPA Automatisé — Résidence ANTOINETTE
```

---

## Notes de Configuration

### Variables à remplacer dans n8n
- `REMPLACER_PAR_EMAIL_FAYAT` → `sav.antoinette@fayatimmobilier.com`
- `REMPLACER_PAR_EMAIL_RESPONSABLE_INTERNE` → email du responsable GPA chez Fayat (ex: r.secretant@fayatimmobilier.com)
- `REMPLACER_PAR_ID_CREDENTIALS_SMTP` → ID des credentials SMTP dans n8n

### Configuration SMTP recommandée
- **Host** : mail.fayatimmobilier.com (ou provider email Fayat)
- **Port** : 587 (STARTTLS) ou 465 (SSL)
- **From** : sav.antoinette@fayatimmobilier.com
- **Authentification** : user/password

### Configuration IMAP
- **Host** : mail.fayatimmobilier.com
- **Port** : 993 (IMAP SSL)
- **Mailbox** : INBOX
- **Post-process** : Marquer comme lu (évite double traitement)
