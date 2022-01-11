## Dénombrements 

#### Calculer le nombre d'employés qui sont "Représentant(e)"

```
SELECT COUNT(*) 
    FROM Employe
    WHERE Fonction = "Représentant(e)";
```

#### Calculer le nombre de produits de moins de 50 euro

```
SELECT COUNT(*) 
    FROM Produit
    WHERE PrixUnit < 50;
```

#### Calculer le nombre de produits de catégorie 2 et avec plus de 10 unités en stocks

```
SELECT COUNT(*) 
    FROM Produit
    WHERE CodeCateg = 2
    AND UnitesStock >= 10;
```

#### Calculer le nombre de produits de catégorie 1, des fournisseurs 1 et 18

```
SELECT COUNT(*) 
    FROM Produit
    WHERE CodeCateg = 1
    AND NoFour IN (1, 18);
```

#### Calculer le nombre de pays différents de livraison

```
SELECT COUNT(DISTINCT PaysLiv) 
    FROM Commande;
```

## Calculs statistiques simples

#### Calculer le coût moyen du port pour les commandes du client "QUICK"

```
SELECT ROUND(AVG(Port), 2) as PortMoyen
    FROM Commande
    WHERE CodeCli = "QUICK";
```

#### Calculer le coût du port minimum et maximum des commandes

```
SELECT MIN(CAST(Port AS FLOAT)) AS PortMinimum,
        MAX(CAST(Port AS FLOAT)) AS PortMaximum
    FROM Commande;
```
Ici, nous devons transformer (grâce à la fonction `CAST()`) le port en numérique, car celui-ci est stocké en chaîne de caractères dans la base.

#### Pour chaque messager (par leur numéro : 1, 2 et 3), donner le montant total des frais de port leur correspondant

```
SELECT SUM(Port)
    FROM Commande
    WHERE NoMess = 1;
```

```
SELECT SUM(Port)
    FROM Commande
    WHERE NoMess = 2;
```

```
SELECT SUM(Port)
    FROM Commande
    WHERE NoMess = 3;
```

## Agrégats selon attribut(s)

#### Donner le nombre d'employés par fonction

```
SELECT Fonction, COUNT(*) AS NbEmployes
    FROM Employe
    GROUP BY Fonction;
```

#### Donner le montant moyen du port par messager

```
SELECT NoMess, 
        ROUND(AVG(Port), 2) AS PortMoyen
    FROM Commande
    GROUP BY NoMess;
```

#### Donner le nombre de catégories de produits fournis par chaque fournisseur

```
SELECT NoFour,
        COUNT(*) AS NbProduitsFournis  ,
        COUNT(DISTINCT CodeCateg) AS NbCategories
    FROM Produit
    GROUP BY NoFour;
```

#### Donner le prix moyen des produits pour chaque fournisseur et chaque catégorie de produits fournis par celui-ci

```
SELECT NoFour, CodeCateg, 
        AVG(PrixUnit) AS PrixMoyen
    FROM Produit
    GROUP By NoFour, CodeCateg;
```

## Restriction sur agrégats

#### Lister les fournisseurs ne fournissant qu'un seul produit

```
SELECT NoFour
    FROM Produit
    GROUP BY NoFour
    HAVING COUNT(*) = 1;
```

#### Lister les catégorie dont les prix sont en moyenne supérieurs strictement à 150 euro

```
SELECT CodeCateg
    FROM Produit
    GROUP BY CodeCateg
    HAVING AVG(PrixUnit) > 150;
```

#### Lister les fournisseurs ne fournissant qu'une seule catégorie de produits

```
SELECT NoFour
    FROM Produit
    GROUP BY NoFour
    HAVING COUNT(DISTINCT CodeCateg) = 1;
```

#### Lister le produit le plus cher pour chaque fournisseur, pour les produits de plus de 500 euro

```
SELECT NoFour, RefProd, NomProd, PrixUnit
    FROM Produit
    WHERE PrixUnit >= 500
    GROUP BY NoFour
    HAVING PrixUnit = MAX(PrixUnit);
```

## Exercices complémentaires

#### Donner la quantité totale commandée par les clients, pour chaque produit

```
SELECT RefProd, SUM(Qte) as "Quantité totale commandée"
    FROM DetailCommande
    GROUP BY RefProd;
```

#### Donner les cinq clients avec le plus de commandes, triés par ordre décroissant

```
SELECT CodeCli, COUNT(*) AS "Nb Commandes"
    FROM Commande
    GROUP BY CodeCli
    ORDER BY 2 DESC
    LIMIT 5;
```

#### Calculer le montant total des lignes d'achats de chaque commande, sans et avec remise sur les produits

```
SELECT NoCom,
        SUM(PrixUnit * Qte) as "Montant sans remise",
        SUM(PrixUnit * Qte * (1 - Remise)) as "Montant avec remise"
    FROM DetailCommande
    GROUP BY NoCom;
```

#### Pour chaque catégorie avec au moins 10 produits, calculer le montant moyen des prix

```
SELECT CodeCateg, COUNT(*) AS "Nb Produits",
        ROUND(AVG(PrixUnit), 2) AS "Prix moyen"
    FROM Produit
    GROUP BY CodeCateg
    HAVING COUNT(*) >= 10;
```

#### Donner le numéro de l'employé ayant fait le moins de commandes

```
SELECT NoEmp
    FROM Commande
    GROUP By NoEmp
    ORDER BY COUNT(*)
    LIMIT 1;
```
