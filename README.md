# base-de-donne 


 Liste des clients (toutes les informations) dont le nom commence par "D"

INSERT INTO clients (noCli, nom, prenom, adresse, cpo, ville) VALUES
    (3, 'Dupond', 'Camille', 'Rue Crébillon', '44000', 'Nantes'),
    (4, 'Desmoulin', 'Daniel', 'Rue descendante', '21000', 'Dijon'),
    (9, 'Dupond', 'Jean', 'Rue des mimosas', '75018', 'Paris');
    
2️⃣ Nom et prénom de tous les clients

INSERT INTO clients (noCli, nom, prenom, adresse, cpo, ville) VALUES
    (1, 'Albert', 'Anatole', 'Rue des accacias', '61000', 'Amiens'),
    (2, 'Bernard', 'Barnabé', 'Rue du bar', '1000', 'Bourg en Bresse'),
    (3, 'Dupond', 'Camille', 'Rue Crébillon', '44000', 'Nantes'),
    (4, 'Desmoulin', 'Daniel', 'Rue descendante', '21000', 'Dijon'),
    (5, 'Ernest', 'Etienne', 'Rue de l’échaffaud', '42000', 'Saint Étienne'),
    (6, 'Ferdinand', 'François', 'Rue de la convention', '44100', 'Nantes'),
    (9, 'Dupond', 'Jean', 'Rue des mimosas', '75018', 'Paris'),
    (14, 'Boutaud', 'Sabine', 'Rue des platanes', '75002', 'Paris');
3️⃣ Liste des fiches (n°, état) pour les clients (nom, prénom) qui habitent en Loire-Atlantique (44)

INSERT INTO fiches (noFic, noCli, dateCrea, datePaiement, etat) VALUES
    (1004, 6, DATE_SUB(NOW(), INTERVAL 11 DAY), NULL, 'EC'),
    (1005, 3, DATE_SUB(NOW(), INTERVAL 10 DAY), NULL, 'EC');
4️⃣ Détail de la fiche n°1002

INSERT INTO fiches (noFic, noCli, dateCrea, datePaiement, etat) VALUES
    (1002, 4, DATE_SUB(NOW(), INTERVAL 13 DAY), NULL, 'EC');

INSERT INTO lignesFic (noFic, noLig, refart, depart, retour) VALUES
    (1002, 1, 'A03', '2024-11-18', '2024-11-22'),
    (1002, 2, 'A04', '2024-11-19', '2024-11-24'),
    (1002, 3, 'S03', '2024-11-23', NULL);
5️⃣ Prix journalier moyen de location par gamme

SELECT
    gammes.libelle AS 'Gamme',
    AVG(tarifs.prixJour) AS 'tarif journalier moyen'
FROM
    grilleTarifs
JOIN
    gammes ON grilleTarifs.codeGam = gammes.codeGam
JOIN
    tarifs ON grilleTarifs.codeTarif = tarifs.codeTarif
GROUP BY
    gammes.libelle;
6️⃣ Détail de la fiche n°1002 avec le total

INSERT INTO fiches (noFic, noCli, dateCrea, datePaiement, etat) VALUES
    (1002, 4, DATE_SUB(NOW(), INTERVAL 13 DAY), NULL, 'EC');

INSERT INTO lignesFic (noFic, noLig, refart, depart, retour) VALUES
    (1002, 1, 'A03', '2024-11-18', '2024-11-22'),
    (1002, 2, 'A04', '2024-11-19', '2024-11-24'),
    (1002, 3, 'S03', '2024-11-23', NULL);
7️⃣ Grille des tarifs

INSERT INTO grilleTarifs (codeGam, codeCate, codeTarif) VALUES
    ('EG', 'FOA', 'T1'),
    ('EG', 'FOP', 'T2'),
    ('EG', 'MONO', 'T1'),
    ('EG', 'PA', 'T1'),
    ('EG', 'SA', 'T1'),
    ('EG', 'SURF', 'T1'),
    ('HG', 'FOA', 'T4'),
    ('HG', 'FOP', 'T4'),
    ('HG', 'SA', 'T4'),
    ('HG', 'SURF', 'T3'),
    ('MG', 'FOA', 'T2'),
    ('MG', 'FOP', 'T3'),
    ('MG', 'MONO', 'T2'),
    ('MG', 'PA', 'T2'),
    ('MG', 'SA', 'T2'),
    ('MG', 'SURF', 'T2'),
    ('PR', 'FOA', 'T6'),
    ('PR', 'FOP', 'T6'),
    ('PR', 'SA', 'T6'),
    ('PR', 'SURF', 'T5');
8️⃣ Liste des emplacements de la catégorie SURF

INSERT INTO articles (refart, designation, codeGam, codeCate) VALUES
    ('S01', 'Décathlon Apparition', 'EG', 'SURF'),
    ('S02', 'Décathlon Apparition', 'EG', 'SURF'),
    ('S03', 'Décathlon Apparition', 'EG', 'SURF');
9️⃣ Calcul du nombre moyen d'articles loués par fiche de location

SELECT
    AVG(ligneCount) AS 'nb_lignes_moyen_par_fiche'
FROM (
    SELECT
        noFic,
        COUNT(*) AS ligneCount
    FROM
        lignesFic
    GROUP BY
        noFic
) AS ligneCounts;
10️⃣ Calcul du nombre de fiches de localisation établies pour les catégories de localisation Ski alpin, Surf et Patinette

SELECT
    categories.libelle AS 'catégorie',
    COUNT(fiches.noFic) AS 'numéro de localisation'
FROM
    fiches
JOIN
    lignesFic ON fiches.noFic = lignesFic.noFic
JOIN
    articles ON lignesFic.refart = articles.refart
JOIN
    categories ON articles.codeCate = categories.codeCate
WHERE
    categories.libelle IN ('Ski alpin', 'Surf', 'Patinette')
GROUP BY
    categories.libelle;
11️⃣ Calcul du montant moyen des fiches de localisation

SELECT
    AVG(total) AS 'montant moyen d\'une fiche de location'
FROM (
    SELECT
        fiches.noFic,
        SUM((DATEDIFF(lignesFic.retour, lignesFic.depart) + 1) * tarifs.prixJour) AS total
    FROM
        fiches
    JOIN
        lignesFic ON fiches.noFic = lignesFic.noFic
    JOIN
        articles ON lignesFic.refart = articles.refart
    JOIN
        grilleTarifs ON articles.codeGam = grilleTarifs.codeGam AND articles.codeCate = grilleTarifs.codeCate
    JOIN
        tarifs ON grilleTarifs.codeTarif = tarifs.codeTarif
    GROUP BY
        fiches.noFic
) AS totals;
