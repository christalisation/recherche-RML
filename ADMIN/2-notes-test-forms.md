# ULB-Darmstadt-Notes-Test-Forms
- Semble déjà mettre en place une solution pour les noeuds imbriqués:
example.ttl utilise un classInstanceProvider (callback) pour récupérer des instances de classes. Ces instances servent à remplir le dropdown du champ (p.ex. rôles provenant d’une taxonomie externe).

>Si la shape pour prov:agent contient davantage d’informations (p.ex. sh:node pointant vers une NodeShape, ou sh:class), shacl-form adaptera l’éditeur:
sh:node <SomeShape> → générera un sous‑formulaire imbriqué (un groupe de champs décrits par SomeShape).
sh:class <SomeClass> → cherchera (dans les graphs disponibles) des instances de cette classe et peut proposer un sélecteur « link existing » (ou une liste d’options) si des instances sont trouvées.
sh:in (...) → générera un dropdown avec ces valeurs littérales.
sh:or / sh:xone → génèrera un choix entre différentes options/structures (p.ex. Person vs Organisation), exactement comme sur la page example. 
– Source: Copilot (GPT-5 mini)

- Par contre, je n'a pas réussi à faire fonctionner cela pour des objets créés directement à partir du formulaire (pas des liens vers des objets existants), or, c'est justement ce qu'on reecherche à faire.


- J'ai fini par ajouter un noeud racine afin que le formulaire prenne en compte tous mes objects. Je n'ai pas trouver de meilleure solution.
Je me demande s'il s'agit d'une propriété figée de shacl-form. Non juste pour la selection dans le formulaire. A voir cmt faire pour la suite...

# DanielBeecke-Notes-Test-Forms
- Il semble que toutes les shapes aient un ex:RootShape. Probablement obligatoire aussi ici d'avoir un noeud racine.
&rarr; J'adapte alors mon schacl university en conséquence.
- A fonctionné correctement après avoir ajouté un noeud racine. Pas eu besoin de beguggage. Code bien structuré, simple à modifier.
- [ ] Pour les listes déroulantes, comment faire?  Proposition: utiliser sh:in avec des littéraux.
- [x] Ajout descrition de chaque champ avec sh:description.
- [ ] Jeté un oeil vite-fait un peu aux possibilite de mise en forme.
- Rem: Que faire le bouton SAVE.
# Shaperone
_script donne une page blanche... I gave up._

# Other existing forms I chose not to use
- [x] Chercher aussi d'autres repo shacl-forms:
    - [ ] Schimatos shacl-form: https://github.com/schimatos/schimatos.org.git
        - utilise des données qui sont déjà stockées
        - [ ] A voir
    - [ ] Zazuko shacl-forms: https://github.com/zazuko/shacl-forms.git
        - > ⚠️ This library is very experimental and you should not expect any sort of stability or reliability at this point. 
        - (Pas de démo dispo)
        - _script de start not running. I gave up._
        - [ ] A voir
- [ ] Rechercher d'autres implémentations de forms autre que SHACL.
    - ... _mais pas trop eu de temps à y consacrer._


# Idées pour la suite
- Refaire un peu du RML pour voir ce dont j'aurais besoin exactement pour mon shape.
- Se focus sur faire le bon choix quant aux form à utiliser. Justifier par rappot au but du projet. (Ici pour faire un formulaire de RML)

--------

- Partir de Daniel Beecke car 
    - lui et ses collaborateurs avaient une vision similaire de base 
    - ont déjà fait un gros travail de structuration.
    - code propre et bien documenté (grande aide pour la modification)
    - avantageux vu qu'ils ont travaillé en groupe: 
        - possibilité de demander de l'aide en cas de besoin.
      - leur discussions GitHub sont disponibles et peuvent donner des idées.
- Terminer de lire les issues ouvertes dans _The scope of SHACL-UI_.
- Essayer d'implémenter les listes déroulantes avec des objets existants créés ailleurs dans le graphe
    - S'inspirer de ULB-Darmstadt avec leur callback de classInstanceProvider.