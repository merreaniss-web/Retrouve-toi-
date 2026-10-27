# TEMPO — Atlas cartographique

Application web mobile de navigation routière, avec interface Atlas (sauge, forêt, ivoire), carte en priorité, commandes compactes et thèmes clair/sombre.

## Installation sur GitHub Pages

1. Décompresse `tempo-atlas-final.zip`.
2. Copie **les fichiers de déploiement situés à la racine** (`index.html`, `icon.svg`, `manifest.webmanifest`, `sw.js`, `.nojekyll`, ce README) à la racine du dépôt GitHub utilisé par Pages. Ne place pas `index.html` dans un sous-dossier.
3. Commit/push les changements, puis vérifie dans **Settings → Pages** que la source de publication correspond au dépôt et à la branche souhaités.
4. Ouvre l'URL HTTPS GitHub Pages, accepte l'autorisation de localisation et recharge une fois après le déploiement.
5. Sur iPhone, ouvre l'URL dans Safari, puis **Partager → Sur l'écran d'accueil** pour l'utiliser comme application web.

Le service worker est passé en version `v4` pour invalider l'ancien cache applicatif. Si une ancienne interface reste affichée, ferme l'application web, recharge le site dans Safari puis rouvre-la. Au besoin, supprime l'ancienne icône de l'écran d'accueil et ajoute-la à nouveau.

## Fonctions incluses

- Carte MapLibre avec style clair/sombre indépendamment du thème de l'interface.
- Thème de l'application en mode automatique (système), clair ou sombre, avec préférence mémorisée.
- Recherche d'adresse et choix du résultat géographique proposé par le géocodeur.
- Choix d'un départ par adresse, GPS ou point choisi sur la carte ; choix précis de l'arrivée sur la carte.
- Calcul et affichage de routes alternatives, instructions de manœuvre, estimation d'arrivée et durée/distance.
- Guidage vocal en français par la synthèse vocale disponible dans le navigateur.
- Suivi GPS pendant le guidage, vitesse estimée quand la précision GPS le permet, progression, tentative de recalcul si l'on s'écarte de la route et maintien d'écran lorsque le navigateur accepte Wake Lock.
- Modes de classement Rapide, Facile (pondération de la complexité des manœuvres) et Économique (classement des routes disponibles selon distance et estimation carburant).
- Estimation carburant configurable selon consommation et prix saisis.
- Limitation de vitesse recherchée dans OpenStreetMap près de la position ; la valeur n'apparaît que si une donnée cartographique correspondante est trouvée.
- Points de vigilance issus d'un jeu de données public mis en cache, avec tentative de repli OpenStreetMap si la source principale est indisponible.
- Couche de trafic TomTom facultative, activable avec une clé fournie par l'utilisateur dans les réglages.
- Personnalisation de la position (flèche, voiture ou point, couleur et taille).
- Historique local des destinations et signalements locaux (bouchon, accident, obstacle, danger, fermeture, autre), avec position GPS ou point choisi sur la carte.
- Interface de guidage à panneau inférieur compact ; informations détaillées cachées jusqu'à l'ouverture explicite de « Détails ».

## Dépendances réseau et limites importantes

TEMPO est une application web statique. **La navigation et la carte ne sont pas entièrement hors ligne.** L'application requiert Internet pour les ressources et services ci-dessous :

| Service | Usage | Limite à connaître |
|---|---|---|
| MapLibre GL JS + CSS (CDN) | Moteur cartographique | Si les deux CDN sont inaccessibles, la carte ne peut pas démarrer. |
| OpenFreeMap (`bright` / `liberty`) | Styles, fonds et tuiles de carte | Les tuiles sont chargées en réseau ; elles ne sont pas intégralement incluses dans le ZIP. |
| Photon / OpenStreetMap | Recherche d'adresses | Service public : disponibilité, quota et qualité des résultats non garantis. Sélectionne toujours le bon résultat. |
| OSRM public demo | Calcul de routes et étapes routières | Service de démonstration public, sans garantie de disponibilité ni de niveau de service. Les alternatives dépendront de ce qu'il renvoie. |
| Overpass / OpenStreetMap | Recherche de limitations de vitesse et repli de vigilance | Une donnée peut manquer, être ancienne ou être incorrecte. La signalisation réelle prévaut toujours. |
| data.gouv.fr | Source nationale de vigilance / radars mise en cache | Le jeu de données référencé est publié au 30 décembre 2025 dans cette version. Il peut être dépassé. |
| TomTom Traffic API (facultatif) | Couche de circulation et flux sur le segment proche | Nécessite une clé API personnelle et un accès/quotas autorisés. Le calcul OSRM de durée n'est **pas** corrigé par le trafic TomTom. Restreins la clé au domaine autorisé. |

Les signalements personnels et l'historique sont enregistrés dans le stockage local du navigateur. **Les signalements ne sont pas envoyés ni partagés entre utilisateurs** : cela nécessite un vrai backend/une base de données partagée, qui n'est pas fourni par GitHub Pages seul.

La géolocalisation nécessite HTTPS et l'autorisation explicite du navigateur. iOS peut limiter le GPS ou l'audio lorsque Safari est mis en arrière-plan ou que le téléphone verrouille l'application ; garde TEMPO ouverte pendant le guidage. La voix dépend de la prise en charge Web Speech du navigateur et de ses voix installées.

## Pourquoi le projet ne fait pas 70 Mo

Le ZIP est volontairement léger. Le gonfler avec des octets sans données ne rendrait aucune fonction plus complète. Pour une vraie version hors ligne de 70 Mo ou davantage, il faut incorporer de véritables tuiles de carte et des données de routage téléchargeables pour une région définie, plus un moteur de routage hors ligne. Cette version utilise les services réseau listés ci-dessus et ne prétend donc pas fournir une navigation hors ligne complète.

## Sécurité

- Ne place aucune clé privée ou clé TomTom non restreinte dans le code source public.
- La clé TomTom saisie dans l'application est conservée dans le `localStorage` de ce navigateur ; elle est visible par toute personne ayant accès à ce navigateur. Utilise une clé restreinte, ou un backend si tu dois protéger un secret.
- Les valeurs de vitesse, limitations et points de vigilance sont indicatives. Respecte toujours les panneaux et le Code de la route.

## Vérifications effectuées

Le JavaScript a été passé à `node --check`. Un test automatisé de l'interface avec services réseau **simulés** a vérifié le démarrage de la carte (moteur simulé), les suggestions de recherche, le calcul d'alternatives, l'état compact/étendu du guidage, le changement de thème, les écrans Signalements/Réglages, la sauvegarde locale d'un signalement et l'absence d'erreur JavaScript dans ce scénario. Les identifiants HTML et les références JavaScript ont aussi été contrôlés.

Le poste de construction ne disposait pas d'accès réseau externe fonctionnel : je n'ai donc pas pu confirmer l'état réel des API, tester une position GPS physique, valider une clé TomTom ou effectuer un essai routier réel. Avant de s'en servir sur route, teste chaque service sur l'URL publiée, à l'arrêt et dans un environnement sûr.
