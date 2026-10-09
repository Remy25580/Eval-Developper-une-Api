# Diagnostic

Une section par test en échec : renseignez ses quatre champs.

## testAddingALineToAnUnknownOrderIsNotFound

**Symptôme** :

**Cause** :

**Règle du module en jeu** :

**Correctif** :

## testAddingALineToAPaidOrderIsAConflict

**Symptôme** :

**Cause** :

**Règle du module en jeu** :

**Correctif** :

## testAddingALineToMyOrder

**Symptôme** :

**Cause** :

**Règle du module en jeu** :

**Correctif** :

## testAddingALineWithAZeroQuantityIsUnprocessable

**Symptôme** :

**Cause** :

**Règle du module en jeu** :

**Correctif** :

## testListingKitchenTicketsReturnsMine

**Symptôme** : Appeler GET /kitchen-tickets ne renvoie pas les bons de cuisine de l'utilisateur connecté, mais aussi ceux des autres

**Cause** : Lors de la récupération des bons, il n'est pas spécifié dans aucun ```WHERE``` que l'on souhaite juste récupérer les bons de l'utilisateur connecté

**Règle du module en jeu** :

**Correctif** : Dans KitchenTicketRepository.php, dans ```findFor(User $user)```, on rajoute cette ligne au ```createQueryBuilder```: ```->where('k.createdBy = :user')``` afin de ne récupérer que les bons de l'utilisateur connecté


## testListingKitchenTicketsWithoutTokenIsUnauthorized

**Symptôme** : Un code 200 est renvoyé alors qu'un 401 devrait être renvoyé (non autorisé)

**Cause** : Lors de la définition de l'endpoint dans l'entité kitchen, il n'était pas spécifié que ce dernier nécessitait d'être connecté

**Règle du module en jeu** : La sécurisation des endpoints

**Correctif** : Dans le ```GetCollection()``` de KitchenTicket.php, on rajoute la ligne ```security: "is_granted('ROLE_USER')",```

## testOpeningAnOrderIgnoresAnAbandonedOne

**Symptôme** :

**Cause** :

**Règle du module en jeu** :

**Correctif** :

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

**Symptôme** : 

**Cause** : 

**Règle du module en jeu** : 

**Correctif** : 
