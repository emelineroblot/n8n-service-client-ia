# Les règles de décision

Ce n'est pas l'IA qui décide. Elle a seulement lu deux choses dans le message : de quoi parle le client (avec la phrase qui le justifie) et le numéro de commande. Ensuite, ce sont ces règles, écrites à l'avance, qui décident. Elles sont lues **dans l'ordre** : la première qui s'applique gagne. Une réponse automatique ne part que si **aucune** ne s'est appliquée avant.

| # | Si… | Alors | Ce que reçoit le client | Ce que vous recevez |
|---|---|---|---|---|
| 1 | C'est un message automatique (réponse d'absence, notification, adresse « noreply ») | **Ignorer**, sans passer par l'IA | Rien | Une ligne « ignorée » |
| 2 | Le message est illisible, ou l'IA ne cite aucune phrase du client pour justifier ce qu'elle a compris | **Transférer** | Un accusé de réception | Un brouillon à compléter |
| 3 | Le client veut un retour, un échange ou un remboursement | **Transférer** | L'accusé « demande de retour » | Le brouillon de retour |
| 4 | Le client parle d'autre chose, ou de plusieurs sujets à la fois | **Transférer** | Un accusé de réception | Un brouillon à compléter |
| 5 | Le numéro de commande n'existe pas | **Transférer** | Un accusé **neutre** | Un brouillon à compléter |
| 6 | La commande existe, mais à l'e-mail d'un autre client | **Transférer** | Un accusé **neutre** : aucune information sur la commande | Un brouillon à compléter |
| 7 | Le client répond à une demande de précision sans désigner une de ses commandes | **Transférer** | Un accusé de réception | Un brouillon à compléter |
| 8 | Aucune commande en cours à cette adresse | **Transférer** | Un accusé de réception | Un brouillon à compléter |
| 9 | Plusieurs commandes en cours, et pas de numéro | **Demander laquelle** | La liste de ses commandes en cours (numéro, date, montant) | Rien : sa réponse relance le traitement |
| 10 | Le colis est livré, a un incident, la commande est annulée, remboursée ou en attente de paiement | **Transférer** | Un accusé de réception | Le brouillon adapté (colis livré, incident) |
| | **Sinon** : une seule commande, la sienne, en préparation, expédiée, en transit, en livraison, en retard ou en point relais | **Répondre** | La réponse automatique de sa situation | Une ligne « répondue (auto) » |

Et un dernier filet : si un modèle a une variable vide (par exemple un lien de suivi absent), le message ne part pas, et la demande vous est transférée (raison `envoi_bloque`).

## Pourquoi la règle 6 est la plus importante

Quelqu'un écrit « Bonjour, où en est la commande 1187 ? » depuis une adresse qui n'est pas celle de la commande. Un chatbot naïf répondrait « Elle arrive demain à Lyon ». Ici, la règle est simple : **l'adresse e-mail de celui qui demande doit être celle de la commande**. Sinon, aucune information. Dans le chat, le client saisit son numéro de commande **et** son e-mail : sans correspondance, même réponse neutre.

## Les modifier

Les règles sont dans le nœud **Décider** du workflow 03 (fonction `decider`), une ligne par règle, dans l'ordre de ce tableau. Les situations qui peuvent recevoir une réponse automatique sont dans `SITUATIONS_AUTO` : retirez-en une pour qu'elle vous soit toujours transférée.
