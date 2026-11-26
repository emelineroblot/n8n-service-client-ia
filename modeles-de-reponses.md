# Modèles de réponses

L'IA n'écrit aucune phrase : chaque message part d'un de ces modèles, rempli avec les données de la commande. Les `{{variables}}` sont remplacées par leur valeur ; un message dont une variable est vide n'est jamais envoyé (la demande vous est transférée). Votre signature (réglage `signature`) est ajoutée à la fin de chaque e-mail.

Pour les modifier : le nœud **Décider** du workflow 03 (constante `M`, au début du code des modèles).

## Réponses automatiques

Une par situation du colis. Elles partent seules, seulement si toutes les règles sont passées.

### Commande en préparation

**Objet** : Votre commande n° {{numero}}

**E-mail**

```
{{bonjour}},

Merci pour votre message. Votre commande n° {{numero}} du {{date_commande}} ({{articles}}) est en cours de préparation.

Elle sera remise au transporteur très prochainement : vous recevrez alors un lien pour suivre votre colis.
```

**Chat**

```
Votre commande n° {{numero}} du {{date_commande}} est en cours de préparation. Elle sera remise au transporteur très prochainement.
```

### Commande expédiée

**Objet** : Votre commande n° {{numero}} est expédiée

**E-mail**

```
{{bonjour}},

Bonne nouvelle : votre commande n° {{numero}} a été remise à {{transporteur}} {{date_expedition}}. Livraison estimée : {{date_estimee}}.

Numéro de suivi : {{numero_suivi}}
[Suivre mon colis]({{lien_suivi}})
```

**Chat**

```
Votre commande n° {{numero}} a été remise à {{transporteur}} {{date_expedition}}. Livraison estimée : {{date_estimee}}. Suivi : {{lien_suivi}}
```

### Colis en transit

**Objet** : Votre commande n° {{numero}} est en route

**E-mail**

```
{{bonjour}},

Votre commande n° {{numero}} est en cours d'acheminement chez {{transporteur}}.

Dernière étape : {{derniere_etape}} ({{date_etape}}).
Livraison estimée : {{date_estimee}}.

[Suivre mon colis]({{lien_suivi}})
```

**Chat**

```
Votre commande n° {{numero}} est en route chez {{transporteur}} (dernière étape : {{derniere_etape}}, {{date_etape}}). Livraison estimée : {{date_estimee}}. Suivi : {{lien_suivi}}
```

### Colis en cours de livraison

**Objet** : Votre commande n° {{numero}} arrive aujourd'hui

**E-mail**

```
{{bonjour}},

Votre commande n° {{numero}} est en cours de livraison aujourd'hui par {{transporteur}}, à {{ville}}.

[Suivre mon colis]({{lien_suivi}})
```

**Chat**

```
Votre commande n° {{numero}} est en cours de livraison aujourd'hui par {{transporteur}}. Suivi : {{lien_suivi}}
```

### Colis en retard

**Objet** : Votre commande n° {{numero}} : nouvelle date de livraison

**E-mail**

```
{{bonjour}},

Votre commande n° {{numero}} a pris du retard chez {{transporteur}} : la livraison prévue {{date_estimee_initiale}} est désormais estimée {{date_estimee}}. Toutes nos excuses pour ce contretemps.

[Suivre mon colis]({{lien_suivi}})
```

**Chat**

```
Votre commande n° {{numero}} a pris du retard chez {{transporteur}} : livraison désormais estimée {{date_estimee}}. Toutes nos excuses. Suivi : {{lien_suivi}}
```

### Colis en point relais

**Objet** : Votre commande n° {{numero}} vous attend

**E-mail**

```
{{bonjour}},

Votre commande n° {{numero}} vous attend : {{derniere_etape}} ({{date_etape}}).

Pensez à vous munir d'une pièce d'identité pour la retirer.

[Suivre mon colis]({{lien_suivi}})
```

**Chat**

```
Votre commande n° {{numero}} vous attend : {{derniere_etape}} ({{date_etape}}). Pensez à votre pièce d'identité. Suivi : {{lien_suivi}}
```

## Demande de précision

Quand le client a plusieurs commandes en cours et ne donne pas de numéro.

### Plusieurs commandes en cours : laquelle ?

**Objet** : Quelle commande ?

**E-mail**

```
{{bonjour}},

Merci pour votre message. Vous avez plusieurs commandes en cours :

{{liste}}

Pouvez-vous simplement répondre à cet e-mail avec le numéro de la commande qui vous intéresse ?
```

**Chat**

```
Vous avez plusieurs commandes en cours ({{liste_courte}}) : laquelle vous intéresse ?
```

## Accusés de réception

Envoyés tout de suite quand la demande vous est transférée : le client sait qu'on s'occupe de lui.

### Accusé de réception (cas général)

**Objet** : Nous avons bien reçu votre message

**E-mail**

```
{{bonjour}},

Nous avons bien reçu votre message et nous vous répondons personnellement au plus vite (sous 24 h ouvrées).
```

**Chat**

```
Merci, votre demande est transmise à notre service client : vous recevrez une réponse personnelle par e-mail sous 24 h ouvrées.
```

### Accusé de réception d'une demande de retour

**Objet** : Votre demande de retour

**E-mail**

```
{{bonjour}},

Nous avons bien reçu votre demande de retour. Nous vous envoyons personnellement la marche à suivre (sous 24 h ouvrées). Inutile de renvoyer le colis d'ici là.
```

**Chat**

```
Merci, votre demande de retour est transmise à notre service client : vous recevrez la marche à suivre par e-mail sous 24 h ouvrées.
```

### Accusé neutre (commande introuvable ou d'un autre client)

**Objet** : Nous avons bien reçu votre message

**E-mail**

```
{{bonjour}},

Nous avons bien reçu votre message. Pour protéger les données de nos clients, une personne de notre équipe vérifie votre demande et vous répond personnellement sous 24 h ouvrées.
```

**Chat**

```
Pour protéger les données de nos clients, une personne de notre équipe vérifie votre demande et vous répond par e-mail sous 24 h ouvrées.
```

## Brouillons pour vous

Jamais envoyés seuls : ils vous attendent dans la colonne « brouillon » du tableur. Les [crochets] sont à compléter.

### Brouillon : demande de retour

**Objet** : Votre demande de retour

**E-mail**

```
{{bonjour}},

Vous pouvez nous retourner {{articles_ou_article}} dans les {{delai_retour}} jours suivant la réception. Voici la marche à suivre :

1. Emballez soigneusement l'article dans son emballage d'origine.
2. [Préciser le mode de retour]

Le remboursement intervient sous 5 jours après réception du colis.
```

### Brouillon : colis indiqué livré

**Objet** : Votre commande n° {{numero}}

**E-mail**

```
{{bonjour}},

Le transporteur indique que votre commande n° {{numero}} a été livrée {{date_etape}}. Nous ouvrons une enquête auprès de {{transporteur}} et revenons vers vous sous 48 h.

[Vérifier : boîte aux lettres, voisins, gardien]
```

### Brouillon : incident de livraison

**Objet** : Votre commande n° {{numero}}

**E-mail**

```
{{bonjour}},

Votre colis (commande n° {{numero}}) a rencontré un incident chez {{transporteur}} : {{derniere_etape}}. Nous avons ouvert une réclamation et [proposer un renvoi ou un remboursement].
```

### Brouillon : toute autre demande

**Objet** : {{objet_libre}}

**E-mail**

```
{{bonjour}},

Merci pour votre message.

[Votre réponse]
```

## Variables disponibles

`bonjour` (« Bonjour » + prénom), `numero`, `date_commande`, `articles`, `articles_ou_article`, `ville`, `transporteur`, `numero_suivi`, `lien_suivi`, `date_expedition`, `date_estimee`, `date_estimee_initiale`, `derniere_etape`, `date_etape`, `liste` et `liste_courte` (commandes en cours), `delai_retour`, `objet_libre`.
