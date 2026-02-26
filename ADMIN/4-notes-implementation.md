<style>
r { color: Red }
o { color: Orange }
g { color: Green }
</style>

# Méthode
J'ai commencé par m'inspirer de mappings pour voir les formulations couramment utilisées, celles plus nécessaires, celles plus rares.
Puis, j'ai fait une version simple de shapes (rml-shape-simple.ttl).
Je l'ai testée sur des mappings simples (rml-data.ttl), comme données d'entrée du formulaire.
## Critique
De cette méthode, j'utilise vraiment la propriété de base de Shacl qui est de **vérifier** du RDF.

Or peut etre qu'une meilleure approche serait de ne pas nécessairement me base sur une formulation "courante" de RML mais plutot de me baser sur les besoins réels des utilisateurs (i.e avoir un mappage).
Au final, c'est le formulaire qui généra le RML. Cette formulation de RML peut être différente de ce qui est "courant", tant que le mappage est celui attendu par l'utilisateur! <g>*Est-ce correct?*</g>

# rml-shape-simple.ttl
Basé sur le CheatSheet Map de Aleksei Ivanov (https://medium.com/@amivanoff/the-new-rml-cheat-sheet-map-everything-to-the-rdf-with-ease-ba6a767edfaf)

# rml-shape.ttl
Plus complexe. Basé sur les shapes de la référence (https://kg-construct.github.io/rml-resources/portal/)
J'ai choisi de prendre seulement qques élements pour l'instant.
Changer les **sh:alternativePath** en sh:xone ou sh:or car pas bien gérés dans le formulaire. Ou bien changer dans l'interpretation de alternative path.
**&rarr; C'est dans src/property-template.ts que sont gérés les diff sh:**

## <o>question</o>
>A triples map ...:
It MUST have zero or one logical source (rml:logicalSource) property <g>whose value MUST be an instance of a sub-class of an abstract logical source (rml:AbstractLogicalSource)</g>. 

avec rml:AbstractLogicalSource $\subset$ rml:Iterable qui a 
+ zero or one rml:referenceFormulation property
+ zero or one rml:iterator property
  
<o>Où est rml:LogicalSource dans tout ça ?</o> 
J'imagine que rml:LogicalSource $\subset$ rml:AbstractLogicalSource.
Du coup doit-on ajouter AbstractLogicalSourceShape ou plutot LogicalSourceShape? t.q.
+ one rml:source property
+ one rml:referenceFormulation property
+ zero or one rml:iterator property

## Reponse
Voir dans rml-io

# Suite ?
Poursuivre la version basées sur la spec en veillant à ne pas trop *alourdir* le formulaire pour l'utilisateur.
&rarr; Prioriser les propriétés les plus utilisées pour les listes déroulantes.

(Voir plus tard)
Ajouter la possibilité de ne pas avoir que le TriplesMap comme root node.

* formulaire sympa qui puisse supporter nptk type de shapes.
* avoir la possibilté de gerer tout mapping
* autoriser les blank node ? cmt les gerer? Peut etre présenter le contenu ou son nom. (Utilisier Blanknode identifier? ou plutot des IRI avec un champ IRI)
**&rarr; C'est dans dist/constants.d.ts que sont gérés les noms (/ou affichage des noms?) de blank nodes**
* Modifier un peu le design du formulaire pour le rendre plus user friendly. -> Ok, ca a été fait.

--------------------------------------------

# RML shapes solutions
* essayer de simplifier la version des shapes officielles car elle mutliplie les imbrications (ex: LogicalSource -> AbstractLogicalSource -> Iterable), et ainsi rend le formulaire plus complexe. Et rendu visuel moins clair. &rarr; on perd le coté "convivial" d'un formulaire.
* Le composant ULB‑Darmstadt gère bien sh:or/sh:xone via des listes déroulantes, mais ne gère pas certains cas complexes de combinaisons logiques (ex. sh:alternativePath).
* A la question "Faut-il gérer tous les cas de RML shapes?" ma réponse est non, je pense. Car distinction entre deux type le shapes:
  - profil « forme » : SHACL simplifié, orienté création de formulaires
  - profil « validation » : SHACL plus complet, orienté validation de données RDF