# Limitations pratiques de \<shacl-form> pour RML

## Fonctionnalités SHACL non (ou partiellement) supportées par \<shacl-form>

- `sh:alternativePath`
  - Propriété de chemin permettant d'exprimer une union de prédicats (ex. `rml:subjectMap | rml:subject`).
  - Svt utilisé dans RML-Core.
  - Non documentée comme supportée par \<shacl-form> &rarr; comportements inattendus dans la génération de formulaire: tous les chemins apparaissant et ce n'est pas ce qu'on veut au final.
  - Décision : ne pas utiliser `sh:alternativePath` dans le rml-shape. Les remplacer par des (`sh:or` / `sh:xone`) lorsque possible, sinon, prendre une décision (par exemple, ici, uniquement rml:subjectMap possible).

- `sh:targetSubjectsOf` / `sh:targetObjectsOf`
  - \<shacl-form> ne les exploite pas pour la structure du formulaire.
  - Pour la génération de formulaires avec \<shacl-form>, `sh:targetClass` suffit pour indiquer quelle shape s'applique à une classe d'instances
  - Décision : 
    - utiliser principalement `sh:targetClass` , mais 
    - conserver `sh:targetObjectsOf` dans les shapes comme méta-informations utiles pour la validation et pour d'autres outils tq:
    - Sera peut-être utile pour le futur via setClassInstanceProvider (aller chercher des données preexistantes et les mettre dans le formulaire)


## Diviser les shapes en :
1. rml-shapes, inspiré de RML-Core
2. form-shapes, shapes supplémentaires hor namespace rml: pour piloter la génération de formulaire (nœud racine, création de noeuds isolés, possibilité de créer plusieurs noeuds.)

## rml-shapes à simplifier par rapport à RML-Core
- Les shapes officielles RML-Core sont exhaustives et font largement usage de :
  - `sh:and`, `sh:xone`, shapes héritées (`TermMap`, `ExpressionMap`, etc.) ;
  - chemins complexes, cibles avancées.
- Ces shapes sont adaptées à la **validation**, mais produisent des formulaires difficiles à utiliser et partiellement interprétés par \<shacl-form>.
- Décision :
  - Repartir de mon profil de shapes simplifié
  - Utiliser les shapes RML-Core comme référence conceptuelle et pour les règles de réécriture (shortcuts, description, etc).

## Gestion des shortcuts RML (ex. `rml:subject`)

- Spécification RML-Core / R2RML :
  - Un triple `?tm rml:subject ?y` est équivalent à `?tm rml:subjectMap [ rml:constant ?y ]`.
- Stratégie adoptée :
  - Le formulaire n'expose que la forme longue (`rml:subjectMap` avec `rml:constant` ou `rml:template`).
  - Avantage :
    - shapes plus simples, meilleure compatibilité avec \<shacl-form>,
    - conformité avec la sémantique officielle de RML.


# Décisions de conception

## 1. Multi-TriplesMap via un même formulaire

Choix: Nœud racine pour la génération de formulaire
`
An RML mapping is represented as an RDF graph. In other words, RDF is used not just as the target data model of the mapping, but also as a formalism for representing the RML mapping itself.

An RDF graph that represents an RML mapping is called an RML mapping graph.

The RML vocabulary is the set of IRIs defined in this specification that start with the rml: as prefix and uses namespace IRI: http://w3id.org/rml/
`
Conformément à la spécification RML‑Core, un mapping graph peut contenir des triples additionnels en dehors du vocabulaire RML ; ces triples seront apparemment ignorés par les processeurs RML. Ainsi, j'exploite cette liberté pour introduire un nœud racine (ex:Mapping ou autre) et des shapes SHACL orientés UI, dans un namespace non‑RML (ex:), afin de piloter la génération de formulaires avec \<shacl-form> sans compromettre la validité du mapping RML final.

## 2. Réutilisation de ressources

- Pour permettre la sélection de ressources existantes (ex. réutiliser un LogicalSource), il est utile de typer les propriétés avec `sh:class` ou `sh:node` (ex. `sh:class rml:LogicalSource` sur la propriété `rml:logicalSource`). 
- Ces informations pourront être exploitées par des mécanismes comme `setClassInstanceProvider` pour proposer des listes d'instances existantes. 
- Décision : typer les propriétés de liens (logicalSource, subjectMap, objectMap, etc.) pour préparer la réutilisation d'instances.
- TODO: à voir encore concretement cmt faire avec des données dirèctement crées àpd formulaire.

## 3. Rôle de `sh:nodeKind` et `sh:datatype` dans le formulaire

- `sh:datatype` restreint les littéraux à un type donné (string, integer, boolean, etc.) et est utilisé par des frameworks comme DASH pour choisir le type de widget (champ texte, case à cocher, etc.). 
- `sh:nodeKind` restreint le genre de nœud (IRI, Literal, BlankNode, combinaisons) et permet de distinguer les propriétés "ressource" des propriétés "littéral". 
- Dans le contexte RML :
  - les constantes de SubjectMap et PredicateMap sont des IRIs (`sh:nodeKind sh:IRI`),
  - les constantes d'ObjectMap peuvent être IRIs ou littéraux. 
- Décision : utiliser `sh:nodeKind` et `sh:datatype` de manière ciblée pour guider l'UI et réduire les erreurs, sans implémenter toutes les extensions DASH (editors, viewers) dans un premier temps. 
- TODO: voir cmt les erreurs sont handle.


