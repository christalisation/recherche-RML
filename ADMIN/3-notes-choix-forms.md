Choix du formulaire SHACL à utiliser
============================================

Pour le choix du formulaire, j'ai:
- listé propriétés et des fonctionnalités clés, avec leur rôle indispensable pour votre projet TFE;
- les ai testés avec des shapes simples de test generés via Gemini, sur la liste de fomrs réutilisables trouvés sur GitHub;
- évalué leur compatibilité avec les besoins spécifiques de votre projet (prise en charge ou non, display pertinent);

Propriétés et fonctionnalités clés
---------------------------------
- ´sh:xone´: Un nœud doit se conformer à exactement une des shapes listées. 
Utile pour les termMap qui peuvent etre  rml:constant, rml:reference ou rml:template.
````
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix ex: <http://example.com/ns#> .

ex:ProblematicShape
  a sh:NodeShape ;
  sh:targetClass ex:SomeClass ;
  sh:property [
    sh:path ex:code ;
    sh:name "Code" ;
    sh:description "Un code qui doit être (une chaîne de caractères) OU BIEN (avoir une longueur de 5+), mais pas les deux." ;
    sh:xone (
      [ sh:datatype xsd:string ; ]
      [ sh:minLength 5 ; ]
    )
  ] .
````
```@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix ex: <http://example.com/ns#> .

ex:ContactShape
  a sh:NodeShape ;
  sh:targetClass ex:Person ;
  sh:property [
    sh:path foaf:name ;
    sh:name "Nom complet" ;
    sh:description "Veuillez entrer votre nom complet." ;
    sh:datatype xsd:string ;
    sh:minCount 1 ;
    sh:maxCount 1 ;
  ] ;
  sh:xone (
    [
      sh:description "Une personne avec une adresse e-mail mais sans numéro de téléphone." ;
      sh:property [
        sh:path foaf:mbox ;
        sh:name "Adresse e-mail" ;
        sh:minCount 1 ;
        sh:maxCount 1 ;
        sh:pattern "^[\\w-\\.]+@([\\w-]+\\.)+[\\w-]{2,4}$" ;
        sh:message "Veuillez fournir une adresse e-mail valide." ;
      ] ;
      sh:property [
        sh:path foaf:phone ;
        sh:maxCount 0 ;
      ] ;
    ]
    [
      sh:description "Une personne avec un numéro de téléphone mais sans adresse e-mail." ;
      sh:property [
        sh:path foaf:phone ;
        sh:name "Numéro de téléphone" ;
        sh:minCount 1 ;
        sh:maxCount 1 ;
        sh:datatype xsd:string ;
      ] ;
      sh:property [
        sh:path foaf:mbox ;
        sh:maxCount 0 ;
      ] ;
    ]
  ) .
```

- sh:or: Un nœud doit se conformer à au moins une des shapes listées.
````
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix ex: <http://example.com/ns#> .

ex:ProductShape
  a sh:NodeShape ;
  sh:targetClass ex:Product ;
  sh:property [
    sh:path ex:productID ;
    sh:name "Identifiant du produit" ;
    sh:description "L'identifiant unique du produit." ;
    sh:minCount 1 ;
    sh:maxCount 1 ;
    sh:or (
      [
        sh:description "L'identifiant peut être un code-barres (13 chiffres)." ;
        sh:datatype xsd:string ;
        sh:pattern "^[0-9]{13}$" ;
        sh:message "Le code-barres doit contenir 13 chiffres." ;
      ]
      [
        sh:description "L'identifiant peut être une URI." ;
        sh:nodeKind sh:IRI ;
        sh:message "L'identifiant doit être une URI valide." ;
      ]
    )
  ] .
````
- sh:group: Principalement pour le display.
````
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix ex: <http://example.com/ns#> .

# 1. Define the Property Groups
# These are like templates for the groups you will use.

ex:ContactGroup
  a sh:PropertyGroup ;
  sh:label "Contact Information" ;
  sh:description "How to get in touch with the person." ;
  sh:order 0 . # This group should appear first.

ex:AddressGroup
  a sh:PropertyGroup ;
  sh:label "Mailing Address" ;
  sh:description "The physical mailing address." ;
  sh:order 1 . # This group should appear second.


# 2. Define the Node Shape that uses the groups

ex:UserProfileShape
  a sh:NodeShape ;
  sh:targetClass ex:User ;

  # This property is not in any group
  sh:property [
    sh:path ex:displayName ;
    sh:name "Display Name" ;
    sh:order 0 ; # This property appears first overall
  ] ;

  # Assign properties to the "Contact Information" group
  sh:property [
    sh:path ex:email ;
    sh:name "Email" ;
    sh:group ex:ContactGroup ;
    sh:order 1 ; # First item within the contact group
  ] ;
  sh:property [
    sh:path ex:phone ;
    sh:name "Phone" ;
    sh:group ex:ContactGroup ;
    sh:order 2 ; # Second item within the contact group
  ] ;

  # Assign properties to the "Mailing Address" group
  sh:property [
    sh:path ex:streetAddress ;
    sh:name "Street" ;
    sh:group ex:AddressGroup ;
    sh:order 1 ; # First item within the address group
  ] ;
  sh:property [
    sh:path ex:city ;
    sh:name "City" ;
    sh:group ex:AddressGroup ;
    sh:order 2 ; # Second item within the address group
  ] ;
  sh:property [
    sh:path ex:postalCode ;
    sh:name "Postal Code" ;
    sh:group ex:AddressGroup ;
    sh:order 3 ; # Third item within the address group
  ] .
````
- 

Notes:
- ULB-Darmstadt: 
    - chercher cmt creer plusieurs instances de noeuds. 
    - cmt ne pas juste prendre le premier noeud?