## Sous-requêtes

### Lister les employés n'ayant jamais effectué une commande, via une sous-requête

```
SELECT Nom, Prenom
    FROM Employe
    WHERE NoEmp NOT IN (SELECT NoEmp
                            FROM Commande);
```

### Nombre de produits proposés par la société fournisseur "Mayumi's", via une sous-requête

```
SELECT COUNT(*)
    FROM Produit
    WHERE NoFour = (SELECT NoFour
                        FROM Fournisseur
                        WHERE Societe = "Mayumis");
```

ou

```
SELECT COUNT(*)
    FROM Produit NATURAL JOIN
        (SELECT NoFour
            FROM Fournisseur
            WHERE Societe = "Mayumis");
```

### Nombre de commandes passées par des employés sous la responsabilité de "Patrick Emery"

```
SELECT COUNT(*)
    FROM Commande
    WHERE NoEmp IN (SELECT NoEmp
                        FROM Employe
                        WHERE RendCompteA = (SELECT NoEmp
                                                FROM Employe
                                                WHERE Nom = "Emery"
                                                AND Prenom = "Patrick"));
```

## Opérateur `EXISTS`

### Lister les produits n'ayant jamais été commandés, à l'aide de l'opérateur EXISTS

```
SELECT NomProd
    FROM Produit
    WHERE NOT EXISTS (SELECT *
                        FROM DetailCommande
                        WHERE RefProd = Produit.RefProd);
```

### Lister les fournisseurs dont au moins un produit a été livré en France

```
SELECT Societe
    FROM Fournisseur
    WHERE EXISTS (SELECT * 
                    FROM Produit, DetailCommande, Commande
                    WHERE Produit.RefProd = DetailCommande.RefProd
                    AND DetailCommande.NoCom = Commande.NoCom
                    AND PaysLiv = "France"
                    AND NoFour = Fournisseur.NoFour);
```

### Liste des fournisseurs qui ne proposent que des boissons

```
SELECT Societe
    FROM Fournisseur
    WHERE EXISTS (SELECT *
                    FROM Produit, Categorie
                    WHERE Produit.CodeCateg = Categorie.CodeCateg
                    AND NoFour = Fournisseur.NoFour
                    AND NomCateg = "Boissons")
    AND NOT EXISTS (SELECT *
                    FROM Produit, Categorie
                    WHERE Produit.CodeCateg = Categorie.CodeCateg
                    AND NoFour = Fournisseur.NoFour
                    AND NomCateg <> "Boissons");
```

## Exercices complémentaires

### Lister les clients qui ont commandé du "Camembert Pierrot" (sans aucune jointure)

```
SELECT Societe
    FROM Client
    WHERE CodeCli IN
        (SELECT CodeCli
            FROM Commande
            WHERE NoCom IN
                (SELECT NoCom
                    FROM DetailCommande
                    WHERE RefProd = (SELECT RefProd
                                        FROM Produit
                                        WHERE NomProd = "Camembert Pierrot")));
```

### Lister les fournisseurs dont aucun produit n'a été commandé par un client français

```
SELECT Societe
    FROM Fournisseur
    WHERE NOT EXISTS
        (SELECT *
            FROM Client, Commande, DetailCommande, Produit
            WHERE Client.CodeCli = Commande.CodeCli
            AND Commande.NoCom = DetailCommande.NoCom
            AND DetailCommande.RefProd = Produit.RefProd
            AND Produit.NoFour = Fournisseur.NoFour
            AND Client.Pays = "France");
```

### Lister les clients qui ont commandé tous les produits du fournisseur "Exotic liquids"

```
SELECT Client.Societe
    FROM Client, Commande, DetailCommande, Produit, Fournisseur
    WHERE Client.CodeCli = Commande.CodeCli
    AND Commande.NoCom = DetailCommande.NoCom
    AND DetailCommande.RefProd = Produit.RefProd
    AND Produit.NoFour = Fournisseur.NoFour
    AND Fournisseur.Societe = "Exotic Liquids"
    GROUP BY Client.Societe
    HAVING COUNT(DISTINCT Produit.RefProd) =
        (SELECT COUNT(*)
            FROM Produit NATURAL JOIN Fournisseur
            WHERE Societe = "Exotic Liquids");
```

### Quel est le nombre de fournisseurs n’ayant pas de commandes livrées au Canada ?

```
SELECT COUNT(*)
    FROM Fournisseur
    WHERE NOT EXISTS
        (SELECT * 
            FROM Commande, DetailCommande, Produit
            WHERE Commande.NoCom = DetailCommande.NoCom
            AND DetailCommande.RefProd = Produit.RefProd
            AND Produit.NoFour = Fournisseur.NoFour
            AND PaysLiv = "Canada");
```

### Lister les employés ayant une clientèle sur tous les pays

```
SELECT Nom, Prenom
    FROM Employe, Commande, Client
    WHERE Employe.NoEmp = Commande.NoEmp
    AND Commande.CodeCli = Client.CodeCli
    GROUP By Nom, Prenom
    HAVING COUNT(DISTINCT Client.Pays) = 
        (SELECT COUNT(DISTINCT PAys)
            FROM Client);
```

