# Cht'ti JUG Quarkus: Supersonic, Subatomique Java

> Talk donné par Emmanuel Bernard le Mardi 01 Octobre 2019

## Introduction

Quarkus est un framework java pour écrire des applications back end (Cloud native, microservices, serverless), il est possible de démarrer un projet facilement avec le [générateur quarkus](https://code.quarkus.com).


On peut demarrer Quarkus en mode developpement à l'aide de la commande ```mvn compile quarkus:dev```.

Quarkus compile automatiquement à chaque changement de code, et les datasources configurées avec Quarkus sont reconnues par Hibernate.

## Pourquoi Quarkus

On passe de plus en plus vers une logique containers en mode microservices, et avec les microservices, on a des contraintes qui s'ajoutent :
* Communication inter-services (Kafka)
* Mémoire
* Temps pour écrire les applications réduit

Il est important de noter que ce qui nous a amené à écrire des microservices, c'est l'automatisation des déploiements. Et le point clef c'est **d'isoler** les microservices les uns des autres.

> Et Java dans tout ça ?

Java, a la base a été fait pour être competitif en nombre de requêtes / secondes sur un core CPU donné, c'est un language interprété avec compilation JIT. C'est donc relativement lent.

De plus, la JVM a besoin de RAM pour gérer ses ressources internes : le **metaspace**.

A l'époque des monolithes, ce sacrifie CPU / RAM à la JVM était acceptable car ce surcoût était unique de part la nature du monolithe. Cependant, dans une logique microservices, lorsqu'on **scale up** l'application, on augmente le nombre de containers, on parle de scalabilité horizontale. Ceci à pour effet de multiplier par n ce surcoût fixe lié au **metaspace**, et finit par devenir une perte **importante** de RAM et de CPU.


Dans une logique microservices et container, la donnée importante n'est plus le nombre de requêtes / secondes, mais plutôt le nombre de requêtes / s / Mb.


## Quarkus c'est quoi ?

> Son ojectif : Rendre la vie plus simple.

Quarkus reduit le temps de developpement, compile rapidemment selon les changements du code, et indépendant de l'IDE.

Il nous permet également d'avoir de la **compilation native**, afin d'avoir un binaire à la fin à l'aide d'une compilation AOT (Ahead of Time). Le binaire produit est plus rapide au *runtime* puisqu'on a pas besoin d'attendre que le code compile en JIT.

Cette compilation AOT réalisée à l'aide de [GraalVM](https://www.graalvm.org/) est optimisée pour réduire au maximum le **metaspace**. Pour se faire, GraalVM réduit au maximum le nombre de classes utilisées, il n'a pas de notion d'ajout dynamique, tout le code qui n'est pas utilisé est **éliminé**, c'est le principe de *dead code elimination*.


Cependant... tous les framework qui ont des **annotations** et donc font de la **reflexion**, sont difficiles à intégrer à GraalVM, car on a du *dead code elimination*. Il en résulte que si on veut faire de la reflexion sur du code, il faut donner ce code à GraalVM au moment de la compilation.

Quarkus intervient pour déplacer le travail des framework qui se déroulait au **runtime** vers le **buildtime**, Quarkus identifie les classes qui ont besoin de reflexion pour tel ou tel framework. En somme il extrait la sémantique des framework pour la donner à GraalVM.

## Conclusion

*In fine*, on profite de tous les avantages de GraalVM tout en pouvant utilser nos frameworks favoris qui font de la reflexion. On dispose qu'une compilation AOT qui nous produit un binaire natif plus rapide, et sans trop de **metaspace**. Ainsi, on est bon pour faire du microservices avec Java !
