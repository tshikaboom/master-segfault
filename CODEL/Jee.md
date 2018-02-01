
# Servlet / JSP

Hibernate: Framework DAO (database access object), interface avec la BD, 
permet une indépendance vis à vis du type de BD.

Spring: Framework Service, interface avec DAO, permet une indépendance
vis à vis de l'implémentation du DAO.

JSF: Framework MVC: Validation de formulaire, routing des pages

---

Les servlets ne sont pas thread safe.

Communication entre servlet:
- dans l'objet Request (setAttribute) mais seule servlet receptrice du
forward pourra y accéder
- dans la Session (setAttribute) mais accessible seulement durant la
durée de la session
- dans le ServletContext (setAttribute) accessible durant toute la
durée de vie de l'application

La session est le cache de premier niveau de Hibernate car tout
objet est mis en mémoire dans la session.

Un objet dans l'état Transient n'a pas de représentation dans la BD.

Un object dans l'état Detached a une représentation dans la BD mais
les modifs ne sont pas propagées.

---

session.save(obj): retourne l'id de l'objet sauvegardé

session.persist(obj): retourne void

session.update(obj): erreur si l'objet n'est plus dans la BD.

session.saveOrUpdate(obj): lorsqu'on ne sait pas si l'objet est toujours
dans la BD.

session.merge: ne met à jour que les champs de l'objet qui ont changés
(peut être coûteux car il faut charger en mémoire l'objet de la BD pour
faire les comparaisons)

session.get(Class, id): cherche dans la session puis la BD et renvoie
null si non trouvé

session.load(Class, id): cherche dans la sessions, renvoie un proxy qui 
ne contient que l'id s'il n'est pas dans la session (Exception si un 
attribut est accédé).

---

Dans hibernate.cfg.xml: plusieurs noeuds <session-factory> avec des
attributs name différents permet de lier Hibernate à plusieurs types
de BD.

Possibilité de modifier le fichier hibernate.cfg.xml de manière
programmatique.

---

Attribut xml lazy="true": les associations des entités ne sont récupérées
que lorsqu'elles sont accédées. L'attribut xml lazy est aussi applicable
aux attributs d'une classe qui ne sont pas des associations.

Types d'héritage en BD:
- 1 table fusionnant tous les types/sous-types
- par jointure
- par union (mauvais)

Méthodes pour forcer le lazy-loading:
- lazy="true" -> passer à "false"
- faire un session.get sur l'attribut lazy
- requête HQL: "left join fecth person.job job"
- méthode statique: initialize(PersonJob) 

FLushMode.NEVER: utile dans le cas de conversations longues
(formulaire sur plusieurs pages)

---

Représenter des clés composées avec Hibernate:
1. CompositeId
2. Créer une classe ContactKey qui contient les attributs de la clé,
avec getters et setters et implémentant Serializable.
3. Attribut de type ContactKey dans Contact.

---

Automatic versioning: lorsqu'on veut modifier une version périmée ->
Exception et mise à jour vers la dernière version.

Implémentations possibles du versioning:
- Avec timestamp
- Avec numéro de version (mieux car moins de collision)

Pour une association: optimistic-lock="false" permet de ne pas
créer de nouvelle version lorsqu'un attribut de l'association est
modifié.

Cache de 2ème niveau: n'y mettre que les objets qui ont un ratio de
read beaucoup plus élevé que d'écriture.


# Spring

Avantage Spring: rendre l'application maintenable et évolutive.

Spring = conteneur léger car Spring ne nécessite pas de serveur
d'application (Tomcat) pour fonctionner, une JVM suffit.

Il est possible de modifier programmatiquement le fichier 
applicationContext.xml

#### Programmation par aspect (AOP)

ProceedingJoinPoint (argument pour les méthodes type around):
objet représentatnt la méthode qui aurait du être exécutée 
(référence sur le contexte avant le joinpoint).

---

Avantages de coupler Spring à Hibernate:
- permet d'avoir une indépendance vers le gestionnaire de transaction
(on peut injecter n'importe quel gestionnaire de transaction dans 
applicationContext.xml)
- plus simple à développer

Inconvénients:
- difficile de traiter les erreurs
- dépendance forte vers Spring

# JSF (Java Server Faces)

Faclet <-> MBean <-> Servlet <-> Services

Utilités d'une page JSF:
1. Controller et navigation de page (faces-config.xml)
2. Validation des formulaires
3. Composants graphiques

Dans JSF la FacesServlet joue le rôle de controller en vérifiant la
navigabilité définie dans faces-config.xml
