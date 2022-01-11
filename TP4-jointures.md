## Jointures naturelles

### Récupérer les informations des fournisseurs pour chaque produit

```sql
SELECT *
    FROM Produit NATURAL JOIN Fournisseur;
```

### Afficher les informations des commandes du client "Lazy K Kountry Store"

```sql
SELECT *
    FROM Client NATURAL JOIN Commande
    WHERE Societe = "Lazy K Kountry Store";
```

### Afficher le nombre de commande pour chaque messager (en indiquant son nom)

```sql
SELECT NomMess, COUNT(*) as "Nb Commandes"
    FROM Messager NATURAL JOIN Commande
    GROUP BY NomMess;
```

## Jointures internes

### Récupérer les informations des fournisseurs pour chaque produit

```sql
SELECT *
    FROM Produit INNER JOIN Fournisseur
        ON Produit.NoFour = Fournisseur.NoFour;
```

### Afficher les informations des commandes du client "Lazy K Kountry Store"

```sql
SELECT *
    FROM Commande INNER JOIN Client
        ON Commande.CodeCli = Client.Codecli
    WHERE Societe = "Lazy K Kountry Store";
```

### Afficher le nombre de commande pour chaque messager (en indiquant son nom)

```sql
SELECT NomMess, COUNT(*)
    FROM Commande INNER JOIN Messager
        ON Commande.NoMess = Messager.NoMess
    GROUP BY NomMess;
```

## Jointures externes

### Compter pour chaque produit, le nombre de commandes où il apparaît, même pour ceux dans aucune commande

```sql
SELECT NomProd, COUNT(DISTINCT NoCom)
    FROM Produit LEFT OUTER JOIN DetailCommande
        ON Produit.RefProd = DetailCommande.RefProd
    GROUP BY NomProd;
```

### Lister les produits n'apparaissant dans aucune commande

```sql
SELECT NomProd
    FROM Produit LEFT OUTER JOIN DetailCommande
        ON Produit.RefProd = DetailCommande.RefProd
    GROUP BY NomProd
    HAVING COUNT(DISTINCT NoCom) = 0;
```

### Existe-t'il un employé n'ayant enregistré aucune commande ?

```sql
SELECT Nom, Prenom
    FROM Employe LEFT OUTER JOIN Commande
        ON Employe.NoEmp = Commande.NoEmp
    GROUP BY Nom, Prenom
    HAVING COUNT(DISTINCT NoCom) = 0;
```


## Jointures *à la main*

### Récupérer les informations des fournisseurs pour chaque produit, avec jointure à la main

```sql
SELECT *
    FROM Produit, Fournisseur
    WHERE Produit.NoFour = Fournisseur.NoFour;
```

### Afficher les informations des commandes du client "Lazy K Kountry Store", avec jointure à la main

```sql
SELECT *
    FROM Commande, Client
    WHERE Commande.CodeCli = Client.Codecli
    AND Societe = "Lazy K Kountry Store";
```

### Afficher le nombre de commande pour chaque messager (en indiquant son nom), avec jointure à la main

```sql
SELECT NomMess, COUNT(*)
    FROM Commande, Messager
    WHERE Commande.NoMess = Messager.NoMess
    GROUP BY NomMess;
```


## Exercices complémentaires

### Compter le nombre de produits par fournisseur

```sql
SELECT Societe, COUNT(*) AS "Nb produits"
    FROM Fournisseur NATURAL JOIN Produit
    GROUP BY Societe
    ORDER BY 2 DESC;
```

### Compter le nombre de produits par pays d'origine des fournisseurs

```sql
SELECT Pays, COUNT(*) AS "Nb produits"
    FROM Fournisseur NATURAL JOIN Produit
    GROUP BY Pays
    ORDER BY 2 DESC;
```

### Compter pour chaque employé le nombre de commandes gérées, même pour ceux n'en ayant fait aucune

```sql
SELECT Nom, Prenom, COUNT(NoCom) AS "Nb Commandes"
    FROM Employe LEFT OUTER JOIN Commande
        ON Employe.NoEmp = Commande.NoEmp
    GROUP BY Nom, Prenom
    ORDER BY 3 DESC;
```

### Afficher le nombre de pays différents des clients pour chaque employe (en indiquant son nom et son prénom)

```sql
SELECT Nom, Prenom, COUNT(DISTINCT Cl.Pays) AS "Nb pays différents"
    FROM Employe E, Commande Co, Client Cl
    WHERE E.NoEmp = Co.NoEmp
    AND Co.CodeCli = Cl.CodeCli
    GROUP BY Nom, Prenom
    ORDER BY 3 DESC;
```

### Compter le nombre de produits commandés pour chaque client pour chaque catégorie

```sql
SELECT Cl.Societe, NomCateg, COUNT(DISTINCT P.RefProd) AS "Nb Produits commandés"
    FROM Client Cl, Commande Co, DetailCommande DC, Produit P, Categorie Ca
    WHERE Cl.CodeCli = Co.CodeCli
    AND Co.NoCom = DC.NoCom
    AND DC.RefProd = P.RefProd
    AND P.CodeCateg = Ca.CodeCateg
    GROUP BY Cl.Societe, NomCateg
    ORDER BY 1, 3 DESC;
```

