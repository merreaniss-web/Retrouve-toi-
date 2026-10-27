# TEMPO — Atlas cartographique

Refonte de l'application TEMPO à partir du fichier HTML existant. L'interface utilise la direction artistique **Atlas cartographique** : pierre claire, vert forêt, graphite et sable, avec thème d'interface automatique, clair ou sombre.

## Installation sur GitHub Pages

1. Télécharge et décompresse `tempo-atlas.zip`.
2. Sauvegarde le fichier `index.html` actuel de ton dépôt.
3. Copie à la racine du dépôt les quatre fichiers du projet : `index.html`, `manifest.webmanifest`, `icon.svg` et `sw.js`.
4. Valide les changements dans GitHub, puis ouvre l'adresse GitHub Pages en HTTPS.
5. Sur iPhone : ouvre le site dans Safari, touche **Partager**, puis **Sur l'écran d'accueil**. Sur Android/Chrome : menu du navigateur, puis **Installer l'application** si proposé.

## Fonctions présentes

- Recherche de destination avec suggestions d'adresses Photon, priorité à l'adresse complète quand elle est fournie.
- Départ soit depuis la position GPS, soit depuis une adresse choisie, soit depuis un point choisi sur la carte.
- Choix du point exact d'arrivée sur la carte (entrée, portail, parking).
- Calcul routier et alternatives via l'API publique OSRM ; modes Rapide, Facile (heuristique basée sur les manœuvres) et Économique (estimation carburant).
- Guidage vocal, suivi GPS, estimation d'arrivée, suivi de vitesse et recalcul si l'appareil s'éloigne de l'itinéraire.
- Personnalisation du curseur : flèche, voiture ou point, couleurs Atlas (forêt, sauge, sable, terre cuite, ivoire) et taille.
- Carte sombre ou claire manuelle, indépendante du thème de l'application, avec mémorisation du choix sur l'appareil. Vue du dessus / perspective routière.
- Historique local des destinations, réglages de carburant et thème auto/clair/sombre mémorisés sur l'appareil.
- Base publique nationale des radars chargée depuis data.gouv.fr et mise en cache localement ; si le téléchargement échoue, tentative de recours à OpenStreetMap autour de la position GPS. L'app présente uniquement des **zones générales de vigilance** et ne promet pas une couverture complète.
- Couche des flux de circulation TomTom et indicateur de trafic du segment routier le plus proche, activables avec une clé API personnelle.
- Cache de l'interface pour faciliter une réouverture ; carte, géocodage, GPS, itinéraires, données de vigilance et trafic demandent toujours une connexion active.

## Trafic en direct : configuration nécessaire

Pour activer la carte de circulation, crée une clé depuis le portail développeur TomTom, puis dans TEMPO ouvre le panneau de recherche, déroule **Circulation en direct et zones de ralentissement**, colle la clé et touche **Activer**. Le code utilise l'API Flow Segment Data pour la vitesse actuelle du tronçon le plus proche du GPS et les tuiles de trafic sur la carte.

La clé est conservée dans le `localStorage` du navigateur, pas dans le fichier GitHub. Comme la requête est envoyée depuis le navigateur, elle peut être vue par l'utilisateur et doit être limitée à ton domaine avec des quotas ; pour une application publique à grande échelle, il faut un backend/proxy.

## Contrôles réalisés sur cette livraison

- Syntaxe JavaScript vérifiée avec Node.js.
- Vérification structurelle : 81 identifiants HTML uniques et aucune cible manquante parmi les références JavaScript `$()` vérifiées.
- Test d’interface simulé : cycle des thèmes, changement du style de carte, panneau du pointeur, ouverture du panneau d’itinéraire, point de départ sur la carte et sélection du mode de trajet ; aucune erreur JavaScript détectée pendant ce test.
- Le test simulé ne valide pas la disponibilité réelle des services publics, la précision du GPS d’un téléphone ni une clé TomTom en production.

## Limites techniques connues

- Les services publics OSRM, Photon, OpenFreeMap/MapLibre, Overpass et data.gouv.fr ne garantissent pas une disponibilité permanente ni des quotas commerciaux. Vérifie leurs conditions avant un lancement public.
- OSRM donne une durée théorique sans trafic en direct ; TomTom fournit ici le flux du tronçon le plus proche et une couche visuelle, mais cette version **ne recalcule pas encore la durée totale du trajet** en agrégeant chaque route segment par segment.
- Les données de radars du jeu officiel data.gouv.fr utilisé ici sont publiées au 30 décembre 2025. Le chargement peut échouer selon la disponibilité ou les règles CORS du fournisseur ; le repli OpenStreetMap est collaboratif et non exhaustif.
- Les limitations de vitesse cartographiées peuvent manquer ou être incorrectes. Suis toujours les panneaux et les feux réels ; les alertes de vigilance sont générales et ne remplacent pas la conduite attentive.
- Un navigateur mobile peut suspendre la page en arrière-plan : TEMPO Web ne garantit pas le guidage continu d'une application native. Garde l'écran ouvert pendant la navigation.
- Le cache service-worker conserve seulement l'interface locale. Il ne rend pas la carte, le GPS, le routage ni les API disponibles hors ligne.

## Références techniques

- Base nationale des radars : https://www.data.gouv.fr/datasets/liste-des-radars-fixes-en-france
- OpenStreetMap / Overpass : https://wiki.openstreetmap.org/wiki/Overpass_API
- Photon : https://github.com/komoot/photon
- OSRM : https://project-osrm.org/
- TomTom Flow Segment Data : https://docs.tomtom.com/traffic-api/documentation/tomtom-maps/v1/traffic-flow/flow-segment-data
- Sécurité routière — zones de vigilance : https://www.securite-routiere.gouv.fr/
