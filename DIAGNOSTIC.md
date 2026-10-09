# Diagnostic

Une section par test en échec : renseignez ses quatre champs.

## testAddingALineToAnUnknownOrderIsNotFound

**Symptôme** :

**Cause** :

**Règle du module en jeu** :

**Correctif** :

## testAddingALineToAPaidOrderIsAConflict

**Symptôme** : Lorsque l'on ajoute une ligne à une commande déjà payée, au lieu de lever un code d'erreur 409, on obtient un code 200 montrant que l'ajout de la ligne a fonctionné alors que ce n'est pas sensé se produire

**Cause** : Dans la méthode ```addLine(Order $order, OrderAddLineInput $input)``` de OrderService.php, on ne vérifie en fait jamais si la commande en question a déjà étée règlée ou pas, ce qui ne lève donc pas de code 409 dans le cas ou celle-ci l'est

**Règle du module en jeu** : Gestion des exceptions

**Correctif** : Au début de la méthode ```addLine(Order $order, OrderAddLineInput $input)```, il faut rajouter la vérification suivante:
```php
if (OrderStatus::Paid === $order->getStatus()) {
     throw new OrderAlreadyPaidException();
}
```

## testAddingALineToMyOrder

**Symptôme** :

**Cause** :

**Règle du module en jeu** :

**Correctif** :

## testAddingALineWithAZeroQuantityIsUnprocessable

**Symptôme** : Si on ajoute une ligne à notre commande en mettant comme quantité 0, la requête renvoie un 200 alors qu'elle est sensée renvoyer un 422

**Cause** : Dans la Dto OrderAddLineInput.php, une des conditions de l'attribut ```$quantity``` était ```#[Assert\PositiveOrNull]```, ce qui implique qu'ajouter une ligne avec une quantité nulle est autorisé par l'Api

**Règle du module en jeu** : Validations de surface dans les Dto d'entrée

**Correctif** : Dans OrderAddLineInput.php, on remplace l'assert ```#[Assert\PositiveOrNull]``` par ```#[Assert\Positive]```, ce qui renvoie comme prévu un code 422 lorsque l'on ajoute une ligne avec une quantité nulle

## testListingKitchenTicketsReturnsMine

**Symptôme** : Appeler GET /kitchen-tickets ne renvoie pas les bons de cuisine de l'utilisateur connecté, mais aussi ceux des autres

**Cause** : Lors de la récupération des bons, il n'est pas spécifié dans aucun ```WHERE``` que l'on souhaite juste récupérer les bons de l'utilisateur connecté

**Règle du module en jeu** : Validations de profondeur

**Correctif** : Dans KitchenTicketRepository.php, dans ```findFor(User $user)```, on rajoute cette ligne au ```createQueryBuilder```: ```->where('k.createdBy = :user')``` afin de ne récupérer que les bons de l'utilisateur connecté


## testListingKitchenTicketsWithoutTokenIsUnauthorized

**Symptôme** : Un code 200 est renvoyé alors qu'un 401 devrait être renvoyé (non autorisé)

**Cause** : Lors de la définition de l'endpoint dans l'entité kitchen, il n'était pas spécifié que ce dernier nécessitait d'être connecté

**Règle du module en jeu** : La sécurisation des endpoints

**Correctif** : Dans le ```GetCollection()``` de KitchenTicket.php, on rajoute la ligne ```security: "is_granted('ROLE_USER')",```

## testOpeningAnOrderIgnoresAnAbandonedOne

**Symptôme** : Si l'on a une commande dite abandonnée, donc qui doit être supprimée, et qu'on en ouvre une nouvelle, l'abandonnée est renvoyée au lieu d'en ouvrir une nouvelle

**Cause** : Dans OrderRepository.php, lors de la recherche d'une commande encore active pour un utilisateur, on ne vérifie pas si la commande doit être supprimée, ce qui fait qu'une commande abandonnée est renvoyée au lieu d'en créer une nouvelle

**Règle du module en jeu** : Validations de profondeur

**Correctif** : Dans la méthode ```findActiveFor(User $user)``` de OrderRepository.php, dans le createQueryBuilder, il faut rajouter la ligne vérifiant que la commande n'ait pas été supprimée, c'est à dire la ligne ```->andWhere('o.deletedAt IS NULL')```

## testPayingMyOrderMarksItPaid

**Symptôme** :

**Cause** :

**Règle du module en jeu** :

**Correctif** :

## testRefreshingTwiceWithTheSameTokenIsUnauthorized

**Symptôme** :

**Cause** :

**Règle du module en jeu** :

**Correctif** :

## testRemovingALineFromSomeoneElsesOrderIsForbidden

**Symptôme** : Il est possible en étant connecté, de supprimer une ligne dans la commande de quelqu'un d'autre, alors que cette opération est sensée remonter une erreur 403 forbidden

**Cause** : Dans la définition de l'endpoint DELETE pour la route ```/orders/{id}/lines/{lineId}```, la ligne sécurité (```"is_granted('ROLE_USER') or object.getCreatedBy() == user"```) définit qu'il faut être connecté OU être le propriétaire de la commande pour en supprimer une ligne

**Règle du module en jeu** : Sécurisation des endpoints

**Correctif** : Il faut remplacer dans la dite ligne le 'or' par un 'and', afin que la condition pour supprimer une ligne d'une commande soit d'être connecté mais aussi d'être le propiétaire de la commande. La ligne devient donc ```"is_granted('ROLE_USER') and object.getCreatedBy() == user"```
