**LPG ****semi-trailers ****&**** bottles monito****ring**

| **Gestion documentaire** |
| --- |
| ***Présentation du document *** |
| **Nom du document :** MÉMOIRE TECHNIQUE |
| **Objet :** Système de Traçabilité du Gaz Hors Réseau - CSPH |
| **Version** | 0.0 |
| **Auteur** |  |
| ***Historique*** |
| **Version** | **Date** | **Commentaire** | **Par** | **Validé Par** |
| 0.0 | 28.01.2026 | 1 ère version |  |  |
| ***Documents associés*** |
| **Titre du document** | **Auteur** | **Version** |
|  |  |  |
|  |  |  |
| ***Validation internes du document*** |
| **Nom** | **Titre** | **Version** |
|  |  |  |
|  |  |  |
| ***Diffusion*** |
| **Niveau de diffusion** |
| Sous contrôle (voir liste de diffusion) | **✓**** ** |
| **Liste de diffusion** |
| **Noms ****&**** Prénoms** | **Titre** | **Société** |
| Equipe projet | Comité de suivi |  |

		1	RÉSUMÉ EXÉCUTIF	4

		1.1	Contexte et enjeux	4

		1.2	Objectifs du système	5

		1.3	Synthèse de la solution proposée	5

		1.4	Indicateurs Clés du Projet	6

		2	ÉTAT DES LIEUX ET ANALYSE DU CIRCUITE DE DISTRIBUTION	6

		2.1	Présentation du contexte règlementaire et sectoriel	6

		2.2	Acteurs du circuit de distribution	6

		2.3	Flux de distribution du gaz vrac (camions-citernes)	7

		2.4	Flux de distribution du gaz conditionné (Bouteilles 50 kg)	7

		2.5	Points de défaillance et Risques identifiés	8

		2.6	Besoins Fonctionnelles Issus de l’Analyse	9

		2.6.1	Identification unique des actifs	9

		2.6.2	Mesure automatique des volumes distribués	9

		2.6.3	Géolocalisation des véhicules	9

		2.6.4	Enregistrement électronique des livraisons de bouteilles	9

		2.6.5	Transmission sécurisée et centralisée des données	9

		2.6.6	Détection automatique des anomalies	9

		2.6.7	Tableau de bord de supervision	9

		2.7	Synthèse et justification de la solution proposée	10

		3	Prototype IoT du camion-citerne	10

		3.1	Présentation du prototype embarqué	10

		3.2	Vue d'ensemble fonctionnelle	10

		3.3	Diagramme de l'unité embarquée	10

		3.4	Contraintes d'installation sur semi-remorque	10

		3.4.1	Contraintes mécaniques et vibratoires	11

		3.4.2	Contraintes thermiques	11

		3.4.3	Contraintes liées au transport de matières dangereuses	11

		3.4.4	Contraintes d’alimentation électrique	12

		3.4.5	Contraintes d’accès et de maintenance	12

		3.5	Composants matériels	12

		3.5.1	Contrôleur Principal	13

*https://www.eurolpg.eu/wp-content/uploads/2021/03/50m3-4.jpg*

RÉSUMÉ EXÉCUTIF

Contexte et enjeux

La Caisse de Stabilisation des Prix des Hydrocarbures **(CSPH)** est l’organe étatique chargé de réguler et de sécuriser la distribution de produits pétroliers au Cameroun. Dans le cadre de ses missions régaliennes, la CSPH a identifié une problématique structurelle majeure : L’absence de mécanismes fiables de traçabilité sur le segment du gaz domestique distribué hors réseau[^1].

Ce déficit de contrôle engendre plusieurs conséquences préjudiciables pour l’Etat : 

- L’impossibilité de disposer de statistiques fiables sur les volumes de gaz réellement distribués par les opérateurs.

- Les déclarations de ventes potentiellement sous-estimées, entraînant des pertes significatives sur les recettes de péréquation.

- Une application indifférenciée de la subvention d’État sur le gaz domestique, y compris pour les usages industriels ou professionnels qui ne devraient pas en bénéficier.

Face à ces enjeux, la CSPH a lancé un appel à projets visant à expérimenter, sur un échantillon représentatif, une solution technologique de traçabilité combinant des dispositifs IoT[^2] embarqués et une infrastructure numérique cloud, avant d’envisager une généralisation à l’ensemble du secteur pétrolier aval.

Objectifs du système 

Le système proposé dans le présent mémoire technique répond à trois objectifs stratégiques définis par la CSPH :

- Recueillir des statistiques fiables sur les ventes du gaz hors réseau, en capturant automatiquement les volumes distribués à chaque point de livraison.

- Contrôler les déclarations de ventes de gaz domestique en distinguant, grâce à la traçabilité, les livraisons à usage résidentiel des livraisons à usage professionnel ou industriel, permettant ainsi l’application d’un tarif non subventionné sur le gaz hors réseau.

- Réduire les coûts liés à la subvention sur le gaz domestique en distinguant, grâce à la traçabilité, les livraisons à usage résidentiel des livraisons à usage professionnel ou industriel, permettant ainsi l’application d’un tarif non subventionné sur le gaz hors réseau.

Synthèse de la solution proposée

La solution architecturée dans ce document repose sur deux dispositifs complémentaires, adaptés aux spécificités de chaque flux de distribution :

**Flux 1 Gaz Vrac**** : Prototype IoT embarqué sur camion-citerne**

Un boitier électronique industriel, alimenté par un panneau solaire couplé à une batterie LiFePO4, est installé sur chaque semi-remorque citerne, Ce prototype intègre un capteur de volume, un module GPS et un module de communication 4G/LTE. Il collecte en temps réel les données de volume de gaz restant dans la citerne et la position géographique du véhicule, et les transmet de manière sécurisée vers la plateforme cloud de la CSPH.

**Flux 2 Bouteilles de 50 kg**** : Traçabilité RFID / PDA**

Chaque bouteille de 50 kg est équipée d’un tag RFID passif UHF (norme EPC Gen2), portant un identifiant unique. Lors de chaque opération de livraison, le technicien du marketer scanne les bouteilles à l’aide d’un terminal PDA 4G. L’application embarquée enregistre l’identité des bouteilles livrées, le destinataire, la date et l’heure, et transmet immédiatement les données vers le serveur central via le réseau 4G/LTE.

Indicateurs Clés du Projet

| Paramètre | Valeur / Cible |
| --- | --- |
| Périmètre pilote (camions) |  |
| Périmètre pilote (bouteilles 50 kg) |  |
| Précision du capteur de volume | ± 1 % du volume nominal |
| Précision GPS | < 5 mètres (CEP50) |
| Autonomie batterie (sans soleil) | 72 heures minimum |
| Latence de transmission des données | < 30 secondes |
| Disponibilité de la plateforme cloud | > 99,5% (SLA cible) |
| Durée de la phase pilote | - |

ÉTAT DES LIEUX ET ANALYSE DU CIRCUITE DE DISTRIBUTION

Présentation du contexte règlementaire et sectoriel

Le secteur du gaz domestique au Cameroun est soumis à un régime de péréquation géré par la CSPH. Ce mécanisme garantit un prix uniforme du gaz sur l’ensemble du territoire national, indépendamment des coûts réels de transport et de distribution supportés par les opérateurs. En contrepartie, les marketers[^3] agréés reçoivent des compensations financières de l’État, calculées sur la base des volumes déclarés de gaz distribué. 

Ce dispositif, conçu pour protéger le pouvoir d’achat des ménages camerounais, présente une vulnérabilité intrinsèque : il repose sur des déclarations transmises par les marketers eux-mêmes, sans mécanisme de vérification indépendant et automatisés des volumes réels distribués. Le gaz hors réseau, en raison de la dispersion géographique des livraisons et de l’absence d’instrumentation standardisée sur les véhicules de transport.

Acteurs du circuit de distribution

Le circuit de distribution du gaz hors réseau implique plusieurs catégories d’acteurs ont les interactions définissent le périmètre fonctionnel du système de traçabilité à mettre en place :

| Acteur | Rôle | Point de contact avec solution |
| --- | --- | --- |
| CSPH | Organe régulateur, récepteur des statistiques et contrôleur des déclarations | Tableau de bord, alertes |
| Marketers agréés | Opérateurs de distribution | Transmission des données via prototype IoT / PDA |
| Dépôts | Site de chargement et de stockage du gaz vrac | Point de départ des trajets tracés |
| Chauffeurs / livreurs | Opérateurs terrain, conducteurs des camions citernes | Porteurs du prototype embarqué |
| Clients (entreprises) | Destinataires finaux du gaz 50 kg ou vrac | Point de livraison enregistré dans le système |
| DTI Solutions and Services | Intégrateur de la solution IoT | Administrateur de la plateforme |

Flux de distribution du gaz vrac (camions-citernes)

Le gaz vrac est transporté depuis les dépôts pétroliers agréés vers sites industriels ou commerciaux clients, exclusivement par camions-citernes semi-remorques. L’analyse du circuit actuel révèle les étapes suivantes :

- **Chargement au dépôt :** Le camion-citerne est rempli à un dépôt pétrolier. Un bon de chargement papier est émis, mentionnant le volume chargé.

- **Transport :** Le chauffeur achemine le gaz vers un ou plusieurs clients/ Le trajet n’est actuellement soumis à aucun suivi GPS systématique.

- **Livraison chez le client :** le volume livré est mesuré empiriquement, sans instrumentation électronique standardisée sur les camions. Le bon de livraison est signé manuellement.

- **Déclaration au marketer :** les bons de livraison sont agrégés par le marketer et consolidés dans une déclaration mensuelle transmise à la CSPH.

- **Vérification par la CSPH :** faute de données indépendantes, la CSPH ne dispose pas à ce stade de moyens fiables de contrôle croisé des volumes déclarés.

Ce processus, entièrement semi automatisés et reposant sur des documents papier, il est particulièrement exposé aux risques de manipulation des volumes déclarés, de détournements de gaz subventionné et d’erreurs de compréhension.

Flux de distribution du gaz conditionné (Bouteilles 50 kg)

Les bouteilles de gaz de 50 kg sont destinées à des clients professionnels (restaurants, hôtels, industries légères) Leur circuit de distribution est le suivant :

- **Remplissage en usine de conditionnement :** les bouteilles sont remplies et scellées. Aucun identifiant électronique n’est actuellement apposé sur les bouteilles.

- **Stockage dans les dépôts des marketers :** les bouteilles sont stockées en vrac, sans traçabilité individuelle.

- **Livraison chez le client :** un livreur transporte les bouteilles et récupère les bouteilles vides. La transaction est enregistrée sur un bon de papier.

- **Retour des bouteilles vides :** Les consignes sont retournées aux dépôts. Le suivi de rotation de bouteilles est partiel et non centralisé.

L’absence d’identifiant unique sur chaque bouteille empêche tout suivi de leur cycle de vie, rendant impossible la vérification du nombre réel de livraisons effectuées par un marketer « X »[^4] sur une période donnée.

Points de défaillance et Risques identifiés

L’analyse critique du circuit de distribution a permis d’identifier les points de faillance suivants, justifiant la mise en place d’une solution technologique de traçabilité :

| Risque identifier | Flux concerné | Impact | Gravité |
| --- | --- | --- | --- |
| Sous-déclaration des volumes de gaz distribués par les marketers | Vrac & bouteilles | Perte sur les recettes de péréquation de l’État | Élevée |
| Absence de suivi GPS des trajets des camions-citernes | Gaz Vrac | Incapacité de détecter les livraisons non déclarées | Élevée |
| Détournement de gaz subventionné vers des usages non éligibles | Vrac & Bouteilles | Distorsion de la subvention, perte financière pour l’État | Élevée |
| Absence d’identifiant unique sur les bouteilles 50 kg | Bouteilles 50 kg | Impossibilité de tracer les livraisons individuelles | Élevée |
| Bons de livraison papier non vérifiables | Vrac & Bouteilles | Fragilité des déclarations, risque de falsification | Moyenne |
| Aucun croisement automatique des déclarations des marketers / volumes réels | Vrac & Bouteilles | Absence de détection proactive des anomalies | Élevée |
| Dépendance à la bonne foi des chauffeurs et livreurs | Vrac & Bouteilles | Risque opérationnel sur la fiabilité des données terrain | Moyenne |

Besoins Fonctionnelles Issus de l’Analyse

L’analyse du circuit de distribution conduit à formuler les besoins fonctionnels prioritaires que le système de traçabilité devra satisfaire.

Identification unique des actifs

Chaque camion-citerne et chaque bouteille de 50 kg doit être doté d’un identifiant électronique unique, infalsifiable et lisible automatiquement par les dispositifs du système.

Mesure automatique des volumes distribués

Le volume de gaz chargé et livré par chaque camion-citerne doit être mesuré automatiquement par un capteur électronique embarqué, sans intervention manuelle du chauffeur.

Géolocalisation des véhicules

La position géographique de chaque camion-citerne doit être enregistrée en continu tout au long du projet.

Enregistrement électronique des livraisons de bouteilles

Chaque opération de livraison de bouteilles 50 kg doit être enregistrée électroniquement par scan RFID, en capturant l’identifiant de la bouteille, l’identité du destinataire, la date, l’heure et la localisation GPS du PDA.

Transmission sécurisée et centralisée des données

L’ensemble des données collectées sur le terrain doit être transmis en temps réel, de manière chiffrée, vers une plateforme cloud centralisée accessible par la CSPH.

Détection automatique des anomalies

La plateforme doit être en mesure de comparer automatiquement les volumes déclarés par les marketers avec les volumes mesurés par le système, et de générer des alertes en cas d’écart significatif.

Tableau de bord de supervision

La CSPH doit disposer d’une interface de supervision en temps réel, permettant de visualiser les données de distribution, de consulter les historiques de livraison et d’exporter des rapports statistiques pour les besoins de la péréquation.

Synthèse et justification de la solution proposée

L’analyse du circuit de distribution met en évidence que les failles actuelles sont de nature structurelle : elles ne peuvent pas être corrigés par de simples mesures organisationnelles ou administratives. La mise en place d’une solution technologique indépendante, automatisée et infalsifiable est la seule réponse adéquate aux risques identifiés.

La solution retenue, décrite en détail dans les parties suivantes du mémoire, combine deux dispositifs complémentaire, un prototype IoT embarqué sur les camions-citernes et une solution de traçabilité RFID/PDA pour les bouteilles interconnectées à une plateforme cloud centralisée. Cette architecture répond intégralement aux sept besoins fonctionnels identifiés ci-dessus, tout en étant dimensionné pour une montée en charge progressive vers une généralisation à l’ensemble du parc national.

Prototype IoT du camion-citerne

Présentation du prototype embarqué

   Vue d'ensemble fonctionnelle

   Diagramme de l'unité embarquée

Le dispositif embarqué intègre plusieurs composants dont un capteur de niveau Rochester, un module GPS intégré et pour la transmission réseau, un module 4G/LTE. 

   Contraintes d'installation sur semi-remorque

L’installation du prototype IoT sur un camion-citerne semi-remorque destiné au transport de gaz de pétrole liquéfié (GPL) impose des exigences techniques strictes. L’environnement d’un véhicule industriel en service actif combine des facteurs mécaniques, thermiques, électriques et réglementaires qui doivent tous être pris en compte lors de la conception du dispositif embarqué.

Contraintes mécaniques et vibratoires

Les semi-remorques en service sur les routes camerounaises sont soumis à des vibrations continues et à des chocs ponctuels, en particulier sur les axes de desserte non bitumés ou dégradés. La norme IEC 60068-2-6 définit les seuils de résistance aux vibrations sinusoïdales applicables aux équipements industriels embarqués. Le boîtier du prototype devra satisfaire à minima aux exigences suivantes :

- Plage de fréquence des vibrations : 5 à 500 Hz

- Accélération admissible : jusqu'à 2G en vibrations continues, 5G en chocs répétés

- Fixation mécanique par supports antivibratoires (silent blocs) sur le châssis de la remorque, à l'abri des zones de forte contrainte mécanique (suspension, essieux)

- Le câblage interne au boîtier devra être protégé par des serre-câbles et des gaines tressées, et les connecteurs seront verrouillés mécaniquement (type push-lock ou à baïonnette)

Contraintes thermiques

Le Cameroun présente des conditions climatiques exigeantes avec des températures ambiantes pouvant atteindre 38 à 42 °C dans les zones sahéliennes et semi-arides traversées par les routes de livraison. À ces températures ambiantes s’ajoutent les effets de l’ensoleillement direct sur le toit de la citerne, pouvant porter la température de surface à 60 – 70 °C. Les contraintes thermiques imposent :

- Une plage de fonctionnement garantie de -10 °C à +70 °C pour tous les composants actifs

- Un boitier en aluminium anodisé ou en acier inoxydable pour dissiper la chaleur par conduction

- Un positionnement du boitier en zone ombragée du châssis, protégé du rayonnement solaire direct

- L’utilisation d’une batterie LiFePO4 dont la tolérance thermique (-20 °C à +60 °C) est supérieure aux technologies Li-Ion classiques

Contraintes liées au transport de matières dangereuses

Les camions-citernes GPL sont soumis à la règlementation ADR (Accord Européen relatif au transport international des marchandises Dangereuses par Route), adoptée par le Cameroun en mars 1999 via le règlement CEMAC[^5].

- Aucun équipement électrique non certifié ATEX[^6] (ou équivalent) ne doit être installé dans les zones de classe 1 ou 2 (à proximité immédiate des vannes, évents et raccords de la citerne)

- Le boîtier IoT sera positionné dans la zone dite « zone sûre » du châssis, généralement à l'avant ou sur les flancs du châssis porteur, à distance réglementaire des équipements sous pression

- Les câbles reliant le capteur à effet Hall à la gateway traverseront la séparation entre zone dangereuse et zone sûre via des presse-étoupes certifiés ATEX

- La tension d'alimentation maximale admissible dans la zone à risque est de 12 V DC, conformément aux spécifications des barrières de sécurité intrinsèque

Contraintes d’alimentation électrique

Le réseau de bord des semi-remorques camerounais est généralement en 24 V DC (camions lourds). Toutefois, certains tracteurs disposent d'un réseau 12 V DC. Le prototype sera alimenté prioritairement par le panneau solaire et la batterie LiFePO4 embarquée, avec couplage à la batterie du véhicule en secours. Les contraintes suivantes s'appliquent :

- Isolation galvanique obligatoire entre le réseau de bord du véhicule et l'électronique du prototype (convertisseur DC-DC isolé)

- Protection contre les inversions de polarité et les surtensions transitoires (transients) générés par le démarrage du moteur

- Fusible de protection calibré sur chaque ligne d'alimentation

Contraintes d’accès et de maintenance

- Le boîtier devra être accessible sans démontage de pièces du véhicule, pour permettre les interventions de maintenance (remplacement batterie, vérification connexions, mise à jour firmware[^7])

- Une étiquette d'identification inviolable (tamper-evident) sera apposée sur le boîtier pour détecter toute tentative d'ouverture non autorisée, les données de toute ouverture seront remontées au serveur

- La fixation du boîtier se fera par boulonnage sur le châssis (quatre points minimum), avec utilisation de vis inviolables (type Torx[^8] ou pentalobe[^9])

Composants matériels

La présente section détaille l'ensemble des composants matériels retenus pour la constitution du prototype IoT embarqué sur les semi-remorques. Le choix de chaque composant a été guidé par trois critères prioritaires : la robustesse en environnement industriel mobile, la compatibilité avec les interfaces de la gateway retenue (Advantech ADAM-6717), et la disponibilité commerciale sur le marché africain ou en importation directe.

Contrôleur Principal

Présentation et justification du choix

La gateway retenue pour ce prototype est l'Advantech ADAM-6717-A, un module IoT industriel de la série ADAM-6700. Ce dispositif intègre en un seul boîtier les fonctions d'acquisition de données analogiques et numériques, de traitement embarqué sous Linux, et de passerelle vers le cloud. Ce choix est justifié par plusieurs facteurs déterminants pour le projet :

- Architecture tout-en-un : le module combine acquisition I/O, calcul embarqué et connectivité réseau

- Programmation Node-RED native : l'environnement de programmation graphique Node-RED permet de développer rapidement les flux de traitement des données (lecture capteur → formatage JSON → publication MQTT) sans développement bas-niveau

- Support des protocoles industriels standards : Modbus TCP et Modbus RTU sur RS-485, garantissant la compatibilité avec le capteur à effet Hall et le module GPS

- Plage de température étendue (-40 °C à +70 °C) adaptée aux conditions climatiques camerounaises

- Stockage sur carte micro-SD pour le buffer offline en cas de perte de connectivité 4G

Caractéristiques techniques détaillées

| Paramètres | Spécification |
| --- | --- |
| Processeur |  |
| Mémoire RAM |  |
| Stockage Flash |  |
| Système exploitation |  |
| Environnement de programmation |  |
| Entrées analogiques |  |
| Entrées numériques |  |
| Sorties numériques |  |
| Interfaces série |  |
| Interfaces réseau |  |
| Interface USB |  |
| Protocoles supportés |  |
| Alimentation |  |
| Température de fonctionnement |  |
| Humidité |  |
| Montage |  |
| Dimension (L*H*P) |  |
| Certification |  |
| Prix indicatifs |  |

Alternatives de gateways IoT industrielles comparables

Dans un souci d'indépendance vis-à-vis d'un fournisseur unique et de résilience de la chaîne d'approvisionnement, le tableau ci-après présente quatre gateways industrielles alternatives à l'ADAM-6717, présentant des caractéristiques fonctionnelles comparables et pouvant être retenues en remplacement ou en diversification pour la phase de généralisation.

| Référence | Fabricant | Atouts principaux | Connectivité | Temp. | Prix indicatif |
| --- | --- | --- | --- | --- | --- |
| TRB246 | Teltonika Networks | Gateway 4G LTE  intégrée, I/O RS-485, dual SIM, aluminium | 4G LTe Cat 4, RS-485, RS-232, Ethernet, I/O | -40/+75 °C | ~250-320 USD |
| Moxa MC-1100 | Moxa | Gateway ARM cortex-A8, Linux, Modbus RTU/TCP, cetifié EN  (transport) | 2×RS-485, 2×Ethernet, USB, mini PCIe 4G | -40/+70 °C | ~380–420 USD |
| Robustel R3000 | Robustel | Routeur IoT industriel 4G, double SIM, RS-232/485, DIN Rail, certifié IEC 62443 | 4G LTE, 2×RS-232/485, Ethernet, Wi-Fi | -40/+70 °C | ~290–350 USD |
| AirGate 4G (NOVUS) | NOVUS Automation | Gateway IoT 4G avec I/O analogiques et numériques, MQTT natif, interface Web simple | 4G LTE, RS-485, 4×AI (4–20 mA), 4×DI/DO | -20/+60 °C | ~320–380 USD |

Le SoC (system on a Chip) de notre prototype repose sur ADAM 6717 qui est un dispositif utilisé dans l’automatisation industrielle couplé à une passerelle réseau Teltonika RUT241, le premier dispose de 8 canaux pour l’entrée et la sortie, avec 5 canaux dédiés à l’entrée digitale. Ce module est conçu pour l’acquisition des données et peut être programmé grâce à des outils comme Node-RED.

|  |  |
| --- | --- |
| ADAM 6017 | RUT 241 |

Capteur de niveau de gaz

Technologie retenue

La mesure du niveau de gaz dans la citerne constitue la donnée métier centrale du prototype. Après analyse des différentes technologies disponibles (ultrasonique, magnétostrictive, à pression différentielle, capacitive et à effet Hall), la technologie à effet Hall a été retenue pour ce projet. Ce choix repose sur les considérations suivantes :

| Technologie | Principe | Avantages | Inconvénients | Pertinence |
| --- | --- | --- | --- | --- |
| Effet Hall | Détection du champ magnétique d’un flotteur | Sans contact, aucune pièce mobile interne, résistant aux vibrations signal (4-20mA) direct | Précision limitée à quelques centimètres | Excellent aucune pénétration dans la citerne |
| Ultrasonique | Mesure du temps de vol d’une impulsion sonore entre la surface du liquide et le capteur | Bonne précision ((±1–2 cm) sans contact avec le fluide | Sensibles aux vapeurs, mousses et turbulences, très gourmand en énergie | Risque d’interférence avec les vapeurs GPL |
| Magnétostrictive | Mesure de position d’un flotteur par impulsion magnétique sur guide d’ondes | Très haute précision | Pénétration dans la citerne obligatoire, coût élevé | Pénétration dans la citerne : Certification ADR complexe |
| Pression différentielle | Mesure de la précision hydrostatique en fond de citerne | Simple, robuste, faible cout | Sensible à densité du fluide et à la température | Le GPL est un fluide à pression variable, biais possible |

Le capteur à effet Hall retenu fonctionne selon le principe suivant : un flotteur magnétique externe coulisse le long d'un tube guide fixé sur la paroi extérieure de la citerne. Une rangée de capteurs à effet Hall, disposés à intervalles réguliers sur le tube, détecte la position du champ magnétique du flotteur et génère un signal analogique continu (4–20 mA) proportionnel au niveau de remplissage. Ce signal est directement compatible avec les entrées analogiques de l'ADAM-6717 (plage 4–20 mA).

L'intégration du capteur à effet Hall sur la citerne GPL ne nécessite aucune pénétration de la paroi de la citerne, ce qui est un avantage décisif pour la conformité réglementaire ADR. La procédure d'intégration se déroule comme suit :

- Montage du tube guide externe : le tube guide (acier inoxydable 316L, diamètre 25 mm) est fixé verticalement sur la paroi extérieure de la citerne par des colliers de serrage en acier inoxydable. La longueur du tube est égale à la hauteur utile de la citerne.

- Positionnement du flotteur magnétique : le flotteur, de densité calibrée pour le GPL, est inséré dans le tube guide et suit librement le niveau de remplissage de la citerne par corrélation magnétique à travers la paroi du tube.

- Connexion au boîtier ADAM-6717 : le signal 4–20 mA en sortie du capteur est acheminé vers l'entrée analogique AI0 de l'ADAM-6717 via un câble blindé deux conducteurs, passant par un presse-étoupe certifié ATEX à la traversée de la zone de sécurité.

- Étalonnage sur site : après installation, un étalonnage en deux points (citerne vide = 4 mA, citerne pleine = 20 mA) est réalisé pour calibrer la correspondance signal/volume. Les paramètres d'étalonnage sont enregistrés dans la configuration Node-RED de l'ADAM-6717.

 Intégration sur la citerne

 3.3 Module GPS

       Caractéristiques & précision

       3.3.2 Cas de perte de signal

   3.4 Module de communication 4G/LTE

       3.4.1 Référence du module retenu

       3.4.2 Gestion de la carte SIM

   3.5 Unité de stockage local (buffer offline)

   3.6 Boîtier & intégration mécanique (IP67, vibrations, chaleur)

Source : https://www.fergusonhome.com/generac-7009/s1996365?uid=4725050

Generac 7009 Propane Tank Level

Source : https://www.youtube.com/watch?v=2TyI7xdhN6o 

=====================================================

~500 camions GPL actifs, réseau national avec plusieurs dépôts. Chaque camion embarque un terminal intelligent capable de communiquer par satellite et d’agréger les données capteurs localement.

**Fonctionnalités principales**

- Suivi **GPS/satellite** en temps réel de chaque camion.

- Lecture et transmission des données capteurs embarqués (niveau GPL, pression, température).

- Statuts de livraison mis à jour par le chauffeur via le terminal.

- Planification et affectation des tournées par les dispatchers.

- Alertes automatiques : déviation de route, arrêt non planifié, seuils capteurs dépassés.

- Gestion des profils camions, chauffeurs et dépôts.

- **Monitoring :** santé système, logs, métriques de performance.

**Connectivité - mode hybride**

- GPRS/4G en zone urbaine et semi-urbaine (prioritaire, faible latence).

- Satellite (ex. Iridium, Orbcomm, Starlink) en zone blanche — couverture totale mais latence plus élevée (~600ms–1s) et coût par message.

- Le terminal bascule automatiquement selon la disponibilité réseau (*fallback** **logic*).

- En mode satellite, fréquence réduite à 1 update/5 min pour limiter les coûts.

*Hypothèse : ~15–20% du temps de trajet en zone satellite selon les routes nationales.*

**Capteurs IoT embarqués**

Le terminal communique en local avec les capteurs via bus filaire (CAN bus, RS-485 ou Modbus). Il agrège les lectures et les inclut dans chaque trame de position envoyée.

*Sonde capacitive (niveau GPL)**,** Capteur de pression**,** Température citerne**,** Détecteur de fuite*

- Fréquence de lecture capteurs : toutes les 10 secondes en local sur le terminal.

- Transmission vers le serveur : agrégée dans la trame GPS (toutes les 30s en 4G, 5 min en satellite).

- Alertes critiques capteurs (fuite détectée, surpression) transmises immédiatement, même en satellite.

- Données capteurs stockées localement sur le terminal en cas de perte de connexion (*store *&* **forward*).

**Hypothèses de dimensionnement**

- Trame 4G (GPS + capteurs) : ~500 octets toutes les 30s.

- Trame satellite : ~200 octets toutes les 5 min (coût optimisé).

- Volume ingéré en pic (4G) : ~2,5 Mo/min pour toute la flotte.

- Stockage local terminal : 72h de données capteurs hors connexion.

- Rétention serveur : 12 mois (réglementaire).

- Latence d'affichage carte : < 2s (4G) / < 10s acceptable (satellite).

*Ex. : une alerte surpression déclenche un message satellite prioritaire en **<** 30s, quel que soit le mode actif.*

**Sécurité ****&**** réglementation**

- Données de trajet et capteurs entièrement auditables (matière dangereuse).

- Authentification multi-rôles : chauffeur, dispatcher, responsable, admin.

- Chiffrement de bout en bout — y compris sur le lien satellite.

- Intégrité des données capteurs vérifiable (signature des trames côté terminal).

**Dimensionnement**

Cette partie a pour objectif de présenter une conception complète et chiffrée du capacity planning adaptée aux spécifications fournies. Nous avons utilisé les hypothèses données (500 camions, trames 500 octets / 30 s en 4G, 200 octets / 5 min en satellite, 15–20 % du temps en satellite, rétention 12 mois, etc.) et nous avons appliqué un facteur conservateur (24 h/jour d’activité) pour dimensionner le système de manière sécurisée. Tous les calculs sont transparents et peuvent être ajustés.

Selon les déclarations de la SNH (Energies media), le 23 juin 2025, le dépôt de Bipaga a enregistré 10 000 camions citernes chargés de mi-2019 à juin 2025, et représentant en 2024 une contribution annuelle de 14% à l’approvisionnement du réseau national. La SCDP de son côté également renforce ses capacités avec l’acquisition de 3 nouveaux camions d’une capacité de 25 tonnes chacun dédiées à l’acheminement du Gaz de Pétrole Liquéfié (GPL) le 24 mars 2026.

**Couche terrain – Le Camion et sa connectivité**

Le but ici est de montrer comment les informations sont transmises dans le réseau GPRS / 4G, le terminal chargé d’agréger les trames de données provenant des capteurs sera la partie principale de notre système. Module principal de la flotte il permettra à notre interface de communiquer directement avec les modules embarqués 

Couche Cloud – réception, traitement et exploitation des données

| **Méthode** | **Route** | **Description ****&**** paramètres** | **Rôle requis** |
| --- | --- | --- | --- |
| **Ingestion et réception des trames terrain** |
| POST | /v1/ingest/frame | Reçoit une trame unique du terminal embarqué. Body : truck_id, lat, lng, speed, canal, sensors[],ts Response : 202 Accepted – Traitement asynchrone | Terminal |
| POST | /v1/ingest/batch | Reçoit le lot de trame accumulées (store & forward). Corps : Frames[] – tableau de trames ordonnées par ts. Limite 500 trames / requêtes . | Terminal |
| **Tracking – Positions et flux en temps réel** |
| GET | /v1/tracking/live | Retourne la dernière position connue de tous les camions Query : ? status = moving│idle│offline ? warehouse_id = ’ ‘ | dispatcher admin |
| GET | /v1/tracking/ :truck_id | Historique des positions du camion. Query : | dispatcher admin |
| WS | /v1/tracking/stream | Canal WebSocket. Auth par header Authorization : Bearer au handshake. Events : position_update, alert_triggered, truck_offline. Filtre optionnel : {“filter” : [“truck_id”]). Ping/pong 30 s. | dispatcher admin |
| **Flotte – Gestion des camions** |
| GET | /v1/trucks | Listes des camions. Query : ?warehouse_id)=, ?status=active│inactive│maintenance,?page-&limit=. | dispatcher admin |
| GET | /v1/trucks/ :id | Détails d’un camion : métadonnées. Capacité, dépôt de base, chauffeur affecté, dernière position, dernières lectures des capteurs. | dispatcher admin |
| PATCH | /v1/trucks/ :id | Mise à jour partielle: statut, warehouse_id. Immatriculation non modifiable après création. | admin |
| GET | /v1/trucks/ :id/sensors | Historique de lecture des capteurs du camion | dispatcher admin |
| **Alertes – Détection et acquittement** |
| GET | /v1/alerts | Liste des alertes. Query : ?criticity= ,?type=, ?acquitted=false,?truck_id=,?from= & to=. | dispatcher admin |
| GET | /v1/alerts/ :id | Détail d’une alerte. Type de sévérité, contexte capteurs au moment du déclanchement (payload), position GPS associée, historique d’acquittement. | dispatcher admin |
| **POST** | /v1/alerts/ :id/ack | Acquitte une alerte. {‘’note’’ : ‘’string’’} – commentaire obligatoire pour les alertes critiques (fuite). Crée une entrée immuable dans le log audit. | dispatcher admin |
| **Tournées – Planification et replay** |
| GET | /v1/trips | Liste des tournées. Query : ? Status=scheduled│in progress│finished,?truck_id=,?driver_id=,?id= | dispatcher admin |
| POST | /v1/trips | Créer une tournée | dispatcher |
| GET | /v1/trips/ :id | Détails d’une tournée | tous |
| GET | /v1/trips/ :id/replay | Rejoue la trace GPS complète d’une tournée terminée. | dispatcher admin |
| **ADMIN ****&**** Dashboard** |
| GET | /v1/health |  | system |
| GET | /v1/metrics |  | admin |
| GET | /v1/warehouse |  | admin |
| GET | /v1/warehouse/ :id/trucks |  | dispatcher admin |

[^1]: Représente le gaz constitué du vrac et des bouteilles de gaz de 50 kg livrées aux entreprises
[^2]: Internet of Things, fait référence à des objets connectés
[^3]: Acteurs responsables des activités marketing et commerciales et de suivi client liés aux produits GPL, aux bouteilles ou aux services de distribution.
[^4]: Utilisé en mathématique pour désigner une variable (valeur) inconnu ou un paramètre.
[^5]: Communauté Économique et Monétaire de l’Afrique Centrale
[^6]: ATEX (Atmosphères Explosibles) est un système de certification qui garantit la sécurité des équipements utilisés dans des environnements où des atmosphères explosives peuvent se former, comme dans les industries pétrolières, chimiques ou pharmaceutiques.
[^7]: Logiciel de bas niveau intégré directement dans la mémoire non volatile d'un appareil électronique
[^8]: Vis à six branches arrondies
[^9]: Vis possédant une tête en forme d'étoile à cinq pétales ou lobes