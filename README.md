# Service client e-commerce : l'IA répond au suivi de commande, avec n8n

**L'IA lit, des règles décident, vos modèles répondent.** Ce système répond seul aux « Où est ma commande ? » reçus par e-mail ou par le chat de votre site, avec les vraies informations de la commande et du colis. Tout le reste (un retour, une réclamation, un doute) vous arrive avec la raison du transfert, la commande concernée et un brouillon de réponse prêt.

C'est le système présenté dans la vidéo YouTube de la chaîne [EMDIGITAL](https://www.youtube.com/@emdigitalfr) : une réponse exacte 25 secondes après l'e-mail du client, 4 secondes dans le chat, et 16 demandes sur 35 traitées sans personne sur le jeu d'essai de la démo.

Ce dépôt contient :

| Fichier ou dossier | Contenu |
|---|---|
| [`workflows/`](workflows) | Les 4 workflows n8n à importer |
| [`regles-de-decision.md`](regles-de-decision.md) | Les 10 règles qui décident : répondre, demander une précision ou vous passer la main |
| [`modeles-de-reponses.md`](modeles-de-reponses.md) | Les modèles de réponses, d'accusés de réception et de brouillons |
| [`tableur/`](tableur) | Les colonnes du tableur Google Sheets |

## Comment ça marche

Deux portes d'entrée, un seul cerveau :

- **01 · Réception des e-mails** : chaque minute, les e-mails non lus de l'adresse du service client.
- **02 · Chat du site** : la bulle de chat de votre site envoie le message, le numéro de commande et l'e-mail saisis par le client, et affiche la réponse tout de suite.
- **03 · Traiter une demande** : le cerveau, en 5 étapes.
  1. **La réception.** Un message automatique (réponse d'absence, notification) est ignoré, sans même passer par l'IA.
  2. **La lecture par l'IA.** Mistral (une IA française, hébergée en Europe) n'en retient que deux choses : de quoi parle le client (suivi, retour, autre chose, plusieurs sujets), avec la phrase du client qui le justifie, et le numéro de commande s'il l'a écrit. Sans phrase à citer, rien n'est retenu.
  3. **La commande et le colis.** La commande est retrouvée dans WooCommerce, avec une clé en **lecture seule**. Si l'e-mail de facturation n'est pas celui de la personne qui demande, aucune information n'est donnée. Le suivi du transporteur est optionnel.
  4. **Les règles.** Écrites à l'avance, lues dans l'ordre : la première qui s'applique décide ([`regles-de-decision.md`](regles-de-decision.md)). Une valeur manquante mène toujours vers vous, jamais vers une réponse devinée.
  5. **La réponse.** Un de vos modèles, rempli avec les données de la commande. **L'IA n'écrit aucune phrase.** Pour une demande transférée : un accusé de réception au client et un brouillon pour vous, dans le tableur.
- **04 · Envoyer votre réponse** : vous écrivez votre réponse dans le tableur, elle part au client dans la minute. Une seule fois : une deuxième réponse à la même demande est refusée.

Chaque demande devient une ligne de l'onglet **Demandes** (statut, raison, commande, brouillon) et chaque message une ligne de l'onglet **Échanges**.

## Ce qu'il vous faut

- **n8n**, auto-hébergé ou n8n Cloud, dans une version récente (les workflows ont été construits en novembre 2026).
- **Une boutique WooCommerce** et une clé d'API REST en **lecture seule** (WooCommerce, Réglages, Avancé, API REST).
- **Une clé d'API Mistral**, créée sur [La Plateforme de Mistral](https://console.mistral.ai).
- **Un compte Google** pour Gmail (réception) et Google Sheets (le tableur).
- **Une adresse d'envoi en SMTP** (celle de votre boutique).
- Pour le chat : une bulle de chat sur votre site capable d'appeler une adresse web (votre développeur ou votre extension de chat).

## Installation

1. **Créez le tableur.** Un Google Sheet avec deux onglets, **Demandes** et **Échanges**, et en ligne 1 les en-têtes de [`tableur/demandes.csv`](tableur/demandes.csv) et [`tableur/echanges.csv`](tableur/echanges.csv). Notez son identifiant : la partie de l'adresse entre `/d/` et `/edit`.
2. **Importez les workflows.** Dans n8n : *Créer un workflow*, puis le menu *…*, puis *Import from File*, pour chacun des 4 fichiers de [`workflows/`](workflows). Commencez par le 03.
3. **Branchez vos accès.** Ouvrez chaque nœud marqué en rouge et créez ou choisissez l'identifiant demandé : Gmail, Google Sheets, Google Sheets Trigger, Mistral Cloud, WooCommerce, SMTP et, pour le chat, Header Auth. Aucun identifiant n'est fourni dans les fichiers.
4. **Reliez les portes au cerveau.** Dans les workflows 01 et 02, nœud **Traiter la demande (03)** : choisissez le workflow 03.
5. **Remplissez « Vos réglages »** (workflow 03, et l'expéditeur dans le workflow 04) :

   | Réglage | À mettre |
   |---|---|
   | `id_tableur` | L'identifiant de votre Google Sheet |
   | `nom_boutique`, `url_boutique` | Le nom et l'adresse de votre boutique |
   | `expediteur`, `repondre_a` | L'adresse d'envoi des réponses, et celle où le client répond |
   | `signature` | La signature ajoutée à chaque e-mail |
   | `lien_suivi` | L'adresse de suivi de votre transporteur, avec `{numero}` à la place du numéro de suivi |
   | `api_transporteur` | Optionnel : l'adresse de l'API de suivi de votre transporteur, avec `{numero}` (voir plus bas) |
   | `champ_numero_suivi`, `champ_transporteur`, `champ_date_expedition` | Optionnel : les champs personnalisés de vos commandes, si vous n'utilisez pas l'extension *Advanced Shipment Tracking* |
   | `delai_retour_jours` | Votre délai de retour, cité dans le brouillon de retour |
   | `jours_en_cours` | Combien de jours une commande expédiée reste « en cours » |
   | `seuil_confiance` | Laissez 0,5 |

6. **Réglez la réception.** Dans le workflow 01, remplacez `service-client@votre-boutique.fr` dans le filtre Gmail par votre adresse de service client. Une adresse dédiée et un filtre strict : l'agent ne lit que ce qui lui est destiné.
7. **Adaptez vos modèles** ([`modeles-de-reponses.md`](modeles-de-reponses.md)) dans le nœud **Décider** du workflow 03 : votre ton, vos délais, votre politique de retour.
8. **Testez avant d'activer.** Passez votre boutique sur une commande de test à votre nom, écrivez depuis une boîte que vous contrôlez (les réponses partent vers l'expéditeur), lancez les workflows à la main avec *Execute workflow*, et vérifiez le tableur et les e-mails. Testez aussi le piège : demandez une commande qui n'est pas à votre adresse. Activez ensuite les 4 workflows.

### Le suivi transporteur (optionnel)

Sans `api_transporteur`, la situation vient de WooCommerce seul : « en préparation » ou « expédiée », avec le numéro et le lien de suivi. C'est déjà la majorité des questions.

Pour aller plus loin (en transit, en retard, en point relais, livré), mettez dans `api_transporteur` l'adresse de l'API de suivi de votre transporteur, ajoutez son authentification dans le nœud **Suivi transporteur**, puis adaptez le nœud **Adapter la réponse du transporteur** : il transforme la réponse de votre transporteur dans le format décrit en tête du nœud. Un colis livré, un incident ou un retour à l'expéditeur vous est toujours transféré.

### Le chat du site

Votre site appelle l'adresse du webhook du workflow 02 (méthode POST), avec l'en-tête secret choisi dans l'identifiant Header Auth et ce contenu :

```json
{ "message": "Bonjour, où en est mon colis ?", "numero_commande": "1187", "email": "client@exemple.fr" }
```

La réponse arrive dans la même requête, à afficher dans la bulle : `{ "reponse": "…", "statut": "répondue (auto)" }`. Le secret ne doit jamais être visible dans le code de la page : c'est votre serveur qui appelle n8n.

## Combien ça coûte

- **L'IA** : seulement la lecture du message, quelques dixièmes de centime par message. Pour tous les messages des essais de la démo, la facture Mistral a été de 0,15 €.
- **n8n** : gratuit en auto-hébergement (comptez un petit serveur, autour de 10 à 30 € par mois selon ce qui tourne dessus), ou l'abonnement n8n Cloud.
- **Le temps** : environ 4 secondes de traitement par message. Par e-mail, comptez jusqu'à une minute de plus, le temps du relevé de la boîte.

## Les données de vos clients

- **L'IA ne reçoit que le message** (texte, expéditeur, objet). Jamais votre fichier clients, jamais les commandes.
- **WooCommerce reste en lecture seule** : le système ne peut rien modifier dans votre boutique.
- **Aucune commande n'est décrite à quelqu'un d'autre que son client** : l'e-mail de la personne qui demande doit être celui de la commande.
- **Les réponses ne contiennent jamais d'adresse complète ni de téléphone**, seulement la ville de livraison quand le colis arrive.
- **À faire de votre côté** : fixer une durée de conservation des lignes du tableur, la mentionner dans votre politique de confidentialité, et supprimer les lignes au-delà.

> Je ne suis pas avocate. En cas de doute sur votre situation, faites valider votre dispositif par un juriste ou votre DPO.

## Limites connues

- **WooCommerce et Gmail uniquement** dans cette version. Pour une autre boutique (Shopify, PrestaShop) ou une autre boîte, il faut remplacer les nœuds correspondants.
- **Le suivi transporteur est à brancher vous-même** : chaque transporteur a son API.
- **Dans le chat, la demande de précision n'a pas de fil** : le client renvoie un message avec le bon numéro.
- **Ce n'est pas l'IA qui rend les réponses fiables, ce sont vos règles et vos modèles.** Relisez-les avant d'activer, et regardez l'onglet Demandes les premiers jours.

## Aller plus loin

Pour mes clients, je vais un cran plus loin : une interface à leur nom, avec la file « À traiter », l'historique et l'envoi de la réponse en un clic, et le branchement à leur boutique et à leur transporteur.

Vous avez une boutique en ligne et vous voulez savoir ce qui pourrait partir seul chez vous ? Je propose un **diagnostic gratuit de 30 minutes** : [prendre rendez-vous](https://tidycal.com/emelineroblot/30-minute-meeting?utm_source=github&utm_medium=readme&utm_campaign=ia-service-client-suivi-commande).

Emeline Roblot · [EMDIGITAL](https://emdigital.fr/?utm_source=github&utm_medium=readme&utm_campaign=ia-service-client-suivi-commande)

## Licence

[MIT](LICENSE) : vous pouvez utiliser, modifier et partager ces workflows librement.
