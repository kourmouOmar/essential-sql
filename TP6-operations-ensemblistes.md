## Union

En utilisant la clause `UNION` 

### Lister les employés (nom et prénom) étant "Représentant(e)" ou étant basé au "Royaume-Uni"

```
SELECT Nom, Prenom
    FROM Employe
    WHERE Fonction = "Représentant(e)"
UNION  
SELECT Nom, Prenom
    FROM Employe
    WHERE Pays = "Royaume-Uni";
```

### Lister les clients (société et pays) ayant commandés via un employé situé à Londres ("London" pour rappel) ou ayant été livré par "Speedy Express"

```
SELECT Societe, Cl.Pays
    FROM Client Cl, Commande Co, Employe E
    WHERE Cl.CodeCli = Co.CodeCli
    AND Co.NoEmp = E.NoEmp
    AND E.Ville = "London"
UNION
SELECT Societe, Cl.Pays
    FROM Client Cl, Commande Co, Messager M
    WHERE Cl.CodeCli = Co.CodeCli
    AND Co.NoMess = M.NoMess
    AND NomMess = "Speedy Express";
```

## Intersection

Avec l'opérateur `INTERSECT` :

### Lister les employés (nom et prénom) étant "Représentant(e)" et étant basé au "Royaume-Uni"

```
SELECT Nom, Prenom
    FROM Employe
    WHERE Fonction = "Représentant(e)"
INTERSECT
SELECT Nom, Prenom
    FROM Employe
    WHERE Pays = "Royaume-Uni";
```

### Lister les clients (société et pays) ayant commandés via un employé basé à "Seattle" et ayant commandé des "Desserts"

```
SELECT Societe, Cl.Pays
    FROM Client Cl, Commande Co, Employe E
    WHERE Cl.CodeCli = Co.CodeCli
    AND Co.NoEmp = E.NoEmp
    AND E.Ville = "Seattle"
INTERSECT
SELECT Societe, Cl.Pays
    FROM Client Cl, Commande Co, DetailCommande DC,
         Produit P, Categorie Ca
    WHERE Cl.CodeCli = Co.CodeCli
    AND Co.NoCom = DC.NoCom
    AND DC.RefProd = P.RefProd
    AND P.CodeCateg = Ca.CodeCateg
    AND NomCateg = "Desserts";
```


## Différence

Avec l'opérateur `EXCEPT` :

### Lister les employés (nom et prénom) étant "Représentant(e)" mais n'étant basé au "Royaume-Uni"

```
SELECT Nom, Prenom
    FROM Employe
    WHERE Fonction = "Représentant(e)"
EXCEPT
SELECT Nom, Prenom
    FROM Employe
    WHERE Pays = "Royaume-Uni";
```

### Lister les clients (société et pays) ayant commandés via un employé situé à Londres ("London" pour rappel) et n'ayant jamais été livré par "United Package"

```
SELECT Societe, Cl.Pays
    FROM Client Cl, Commande Co, Employe E
    WHERE Cl.CodeCli = Co.CodeCli
    AND Co.NoEmp = E.NoEmp
    AND E.Ville = "London"
EXCEPT
SELECT Societe, Cl.Pays
    FROM Client Cl, Commande Co, Messager M
    WHERE Cl.CodeCli = Co.CodeCli
    AND Co.NoMess = M.NoMess
    AND NomMess = "United Package";
```

## Exercices complémentaires

### Lister les employés ayant déjà pris des commandes de "Boissons" ou ayant envoyés une commande via "Federal Shipping"

```
SELECT Nom, Prenom
    FROM Employe E, Commande Co, DetailCommande DC,
         Produit P, Categorie Ca
    WHERE E.NoEmp = Co.NoEmp
    AND Co.NoCom = DC.NoCom
    AND DC.RefProd = P.RefProd
    AND P.CodeCateg = Ca.CodeCateg
    AND NomCateg = "Boissons"
UNION
SELECT Nom, Prenom 
    FROM Employe E, Commande Co, Messager M
    WHERE E.NoEmp = Co.NoEmp
    AND Co.NoMess = M.NoMess
    AND NomMess = "Federal Shipping";
```

### Lister les produits de fournisseurs canadiens et ayant été commandé par des clients du "Canada"

```
SELECT Nomprod
    FROM Produit P, Fournisseur F
    WHERE P.NoFour = F.NoFour
    AND Pays = "Canada"
INTERSECT
SELECT NomProd
    FROM Produit P, DetailCommande DC, Commande Co, Client Cl
    WHERE P.RefProd = DC.RefProd
    AND DC.NoCom = Co.NoCom
    AND Co.CodeCli = Cl.CodeCli
    AND Pays = "Canada";
```

### Lister les clients (Société) qui ont commandé du "Konbu" mais pas du "Tofu"

```
SELECT Societe
    FROM Client Cl, Commande Co, DetailCommande DC, Produit P
    WHERE Cl.CodeCli = Co.CodeCli
    AND Co.NoCom = DC.NoCom
    AND DC.RefProd = P.RefProd
    AND NomProd = "Konbu"
EXCEPT
SELECT Societe
    FROM Client Cl, Commande Co, DetailCommande DC, Produit P
    WHERE Cl.CodeCli = Co.CodeCli
    AND Co.NoCom = DC.NoCom
    AND DC.RefProd = P.RefProd
    AND NomProd = "Tofu";
```

