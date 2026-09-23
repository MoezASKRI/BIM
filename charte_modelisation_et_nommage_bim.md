# Charte Standard de Modélisation et de Governance Data BIM
## Standard Industriel pour Maquettes Numériques, Familles et Exploitation

---

## 1. Objet et Domaine d'Application

Le présent document établit la **Charte Générale de Modélisation et de Nommage BIM** applicable à l'ensemble des projets de construction, de réhabilitation et de gestion de patrimoine d'infrastructures industrielles. 

Il définit les exigences strictes en matière de :
* Structuration et nommage des maquettes numériques (`.rvt`).
* Codification des familles Revit (`.rfa`) et paramètres associés.
* Système d'identification unique des équipements pour l'intégration GMAO/SAP (`TAG_Equipement`).
* Découpage des sous-projets internes (*Worksets*).
* Processus de synchronisation des données via les plugins d'échange (MLabs Export Import, DiRoots, BIMLink).

---

## 2. Organisation Multi-Lots & Disciplines

Le découpage du projet s'appuie sur une codification numérique à deux chiffres définissant les disciplines principales :

| Code Discipline | Intitulé de la Discipline | Champ d'Application |
| :---: | :--- | :--- |
| **`02`** | **Architecture** | Aménagements intérieurs, cloisons, faux-plafonds, portes, pièces (*Rooms*). |
| **`03`** | **VRD / Civil** | Voiries, réseaux divers, terrassement, aménagements extérieurs. |
| **`04`** | **Structure** | Béton armé, charpente métallique, fondations, éléments porteurs post-curage. |
| **`05`** | **CVC / HVAC** | Traitement d'air, ventilation, chauffage, climatisation, désenfumage. |
| **`06`** | **Électricité** | Courants forts (CFO), courants faibles (CFA), SSI, éclairage, GTB. |
| **`08`** | **Plomberie & Fluides** | Sanitaires, évacuations, réseaux fluides industriels et utilités. |

---

## 3. Règles de Nommage des Familles Revit (`.rfa`)

Afin de garantir la clarté des modèles et la compatibilité avec les bases de données d'exploitation, toutes les familles Revit chargées dans le projet doivent respecter **5 règles fondamentales** :

### 3.1 Exigences Stricte
1. **Intitulé en Français** : Le nom de la famille doit être rédigé en texte libre, clair et intelligible décrivant exactement le composant.
2. **Plafond Strict de 25 Caractères** : La longueur totale du nom de la famille ne doit **jamais dépasser 25 caractères** (espaces, tirets et caractères spéciaux inclus).
3. **Interdiction Absolue des Préfixes Disciplinaires** : Il est formellement interdit d'ajouter des préfixes de lot (`A_`, `S_`, `E_`, `ELEC_`, `CVC_`, `CFO_`, `CFA_`) au début du nom de famille. 
   * *Exemple incorrect* : `CFO_Pave_LED_600x600_Std` (23 car. mais contient le préfixe parasite `CFO_`).
   * *Exemple conforme* : `Pave_LED_600x600_Std` (20 car. $\le 25$).
4. **Catégorisation Native Appropriée** : Tout composant doit être classé dans sa catégorie native Revit (ex. *Équipement électrique*, *Luminaires*, *Terminaux aérauliques*). L'utilisation de la catégorie *Modèle générique* est interdite pour les éléments métiers.
5. **Traduction Anglaise Obligatoire (`NAME_ENG`)** : Le paramètre partagé `NAME_ENG` doit comporter la traduction exacte de l'élément en anglais, rédigée en style *Title Case* (ex. `LED Panel Light 600x600`).

---

## 4. Codification de l'Identifiant Équipement (`TAG_Equipement`)

Le paramètre `TAG_Equipement` (ou `NN_Tag` / `TAG_Exploitation`) constitue la clé primaire d'interfaçage entre la maquette numérique BIM et le système de gestion de maintenance (GMAO / SAP).

### 4.1 Formule Canonique
$$\mathbf{\text{[Site]-[Bâtiment]-[Discipline]-[Niveau]-[Trigramme][Chrono]}}$$

* **`Site`** : Code trigramme du site industriel (ex. `ST`).
* **`Bâtiment`** : Identifiant du bâtiment (ex. `B01`).
* **`Discipline`** : Code à 2 chiffres de la discipline (`02`, `04`, `05`, `06`, `08`).
* **`Niveau`** : Niveau d'implantation (`L00`, `L01`, `EXT`...).
* **`Trigramme`** : Code matériel de 2 à 4 lettres (ex. `LUM`, `BAES`, `CTA`, `DET`, `VNN`, `TGBT`).
* **`Chrono`** : Numéro séquentiel à 2 ou 3 chiffres (`01`, `02`, `001`...).

*Exemple Conforme* : **`ST-B01-06-L00-LUM01`**

### 4.2 Plafond Stricte GMAO / SAP
Le `TAG_Equipement` reconstitué ne doit **jamais dépasser 30 caractères** au total.

### 4.3 Matrice d'Assujettissement au Tagging

* **Éléments Soumis au Tagging (`TAG_OBLIGATOIRE`)** :
  * Tous les équipements actifs et terminaux d'exploitation : armoires électriques, TGBT, coffrets, luminaires, BAES, prises, interrupteurs, détecteurs DAI, caméras, CTA, ventilo-convecteurs, grilles, registres, pompes, vannes pilotées.
* **Éléments Exclus du Tagging (`NO_TAG`)** :
  * Tous les réseaux de distribution passifs et contenants : chemins de câbles, conduits, dalles de câbles, gaines aérauliques, tuyauteries, dalles structurales, murs, pièces (*Rooms*).

---

## 5. Codification des Fichiers Maquettes (`.rvt`)

Chaque fichier de maquette numérique déposé sur la plateforme collaborative (CDE) doit suivre la structure de nommage officielle :

$$\mathbf{\text{[Site]\_[Bâtiment]\_[Discipline]\_[Phase]\_BIM\_[CodeLMNOPQ]\_[F-Number]\_[Révision].rvt}}$$

* **Exemple Conforme** : `ST_B01_06_DD_BIM_911901_F-XXXXXXXX_A.rvt`
* **Masque F-Number** : Identifiant GED avec préfixe `F-` suivi de 8 majuscules (`F-XXXXXXXX`).
* **Gestion des Révisions** :
  * **Lettre (`A`, `B`, `C`...)** : Réservée aux phases d'études et de diffusions de travail / WIP.
  * **Nombre à 3 chiffres (`001`, `002`...)** : Strictement réservé aux livraisons officielles As-Built (DEX/DOE) archivées dans la GED finale.

---

## 6. Structure des Sous-Projets Revit (*Worksets*)

Chaque maquette partagée doit être découpée en sous-projets internes selon la codification :
$$\mathbf{\text{WS[Discipline]\_00\_[Description]}}$$

### 6.1 Sous-Projets Communs Obligatoires
* `Shared Levels and Grids` (Quadrillages et niveaux).
* `WS00_00_OLD` (Éléments conservés mais masqués des vues de synthèse).
* `#LINK-XX_RVT_[Nom]` (Sous-projets dédiés au chargement des maquettes liées).

### 6.2 Découpage Métier par Discipline (Exemples)
* **Électricité (`06`)** :
  * `WS06_01_CFO_PUISSANCE` (TGBT, armoires, prises fort courant)
  * `WS06_02_ECLAIRAGE_BAES` (Luminaires, spots, blocs de secours)
  * `WS06_03_SSI_SECURITE` (Détection incendie, contrôle d'accès)
  * `WS06_04_CFA_VDI` (Baies, prises RJ45, caméras)
  * `WS06_05_CHEMINEMENTS` (Chemins de câbles, conduits)
  * `WS06_06_GTB_AUTOMATISME` (Automates, bus de communication)
* **Structure (`04`)** :
  * `WS04_01_BETON` (Poteaux, voiles, dalles portouses)
  * `WS04_02_CHARPENTE_METAL` (Poutres, fermes, contreventements)

---

## 7. Workflow d'Échange de Données Revit $\leftrightarrow$ Excel

Pour garantir la qualité des données sans altérer la maquette native, le traitement des nomenclatures s'effectue via un flux bidirectionnel.

### 7.1 Plugins d'Échange Compatibles
* **MLabs Export Import** (Plugin privilégié pour l'import/export fluide Excel).
* **DiRoots** (SheetOne / ParaManager).
* **Ideate BIMLink**.

### 7.2 Règle d'Or de Traçabilité
Tout tableau d'échange Excel/CSV doit **impérativement conserver la colonne de l'identifiant unique** (`GUID`, `UniqueId` ou `Element ID`) générée par Revit. Sans cette clé, aucune réinjection automatique ne sera possible.

### 7.3 Structure Canonique du Tableau de Nomenclature (17 Colonnes)

1. `ID_Element` / `UniqueId` *(Garde-fou réinjection)*
2. `Categorie` *(Classification native)*
3. `Famille_et_Type` *(Nom source)*
4. `Niveau` *(Étage hôte)*
5. `ROOM_NUMBER` *(Numéro de local)*
6. `ROOM_NAME_ENG` *(Nom anglais de pièce)*
7. `Nom_Famille_FR` *(Nom nettoyé $\le 25$ car.)*
8. `Nb_Car_Famille` *(Calcul `NBCAR`)*
9. `Ctrl_Famille_25` *(Voyant d'alerte $\le 25$ car.)*
10. `NAME_ENG` *(Traduction anglaise Title Case)*
11. `Statut_Tag` (`TAG_OBLIGATOIRE` vs `NO_TAG`)
12. `Code_Site` (ex. `ST`)
13. `Code_Batiment` (ex. `B01`)
14. `Code_Discipline` (`02`, `04`, `05`, `06`, `08`)
15. `Trigramme_Chrono` (ex. `LUM01`)
16. `TAG_Equipement_Calcule` *(Reconstitution `TEXTE.JOIN`)*
17. `Conformite_SAP_30` *(Voyant d'alerte $\le 30$ car.)*
