# Choix et essais — Clustering des trajets 3.2A

## Objectif et unité d’analyse
- Identifier des profils de trajets selon leurs propriétés et leurs usages.
- Une ligne = un trajet ; aucune identification de personne ni de motif déclaré.
- Deux lectures retenues : durée–déplacement et contexte historique des stations.

## 1. Préparation commune
- Jointures gauches : trips → cities ; trips → bikes → bike_types ; stations au départ et à l’arrivée.
- Vérifier les clés, les correspondances et la conservation du nombre de trajets.
- Identifiants utilisés pour les jointures et l’interprétation, jamais comme valeurs numériques de clustering.
- Durée en minutes ; déplacement à vol d’oiseau en kilomètres, différent de la longueur réelle du parcours.
- Le taux déplacement/durée reste descriptif : il est redondant et ne mesure pas la vitesse réelle.
- Heure locale par système, changements d’heure inclus ; weekend binaire et heure sinus/cosinus disponibles.
- Coordonnées brutes hors des modèles retenus ; coordonnées de stations absentes non imputées.
- Places inconnues : une place par défaut, original conservé ; descripteur uniquement.
- Propulsion et forme : descripteurs uniquement ; les 2 346 trajets sans ces informations sont conservés.
- L’exclusion initialement envisagée aurait notamment pénalisé Komorniki et Luboń.
- Batterie, autonomie, capacité de batterie, rack_locks et stations_status hors des analyses retenues.
- Valeurs extrêmes conservées ; leur influence est vérifiée séparément.
- Par système : premiers 60 % pour apprendre, 20 % pour comparer, derniers 20 % pour décrire.

## 2. Essais exploratoires et décisions
| Essai | Conclusion et décision |
|---|---|
| B — Durée/taux + horaires, ACP et K-means | Quatre descriptions lisibles, mais continuum et sensibilité aux extrêmes ; secondaire. |
| C — Centralité relative + weekend | Rapprochement/éloignement utiles ; centres approximatifs et groupe weekend influencé par l’encodage. |
| D — Fusion temps–espace | Groupes surtout temporels ; pas de gain spatial convaincant. |
| G — Géométrie enrichie | Décrit notamment les liaisons transversales ; dépend du centre, pas de preuve de cinq classes naturelles. |
| H — Durée relative à une même liaison et propulsion | Confirme les familles physiques ; conservé comme contrôle, sans nouvelle classe revendiquée. |
| I — Rythmes des liaisons heure × semaine/weekend | Silhouette ≈ 0,133 ; pas de sous-profils convaincants. |
| J — HDBSCAN | Surtout semaine/weekend dans la représentation testée ; pas d’apport retenu. |

## 3. Analyse physique retenue (E)
- Variables actives : log(durée) et log(déplacement), standardisées sur 80 000 trajets d’apprentissage.
- Comparer les GMM à 1–8 composantes par ICL approximé, pour limiter complexité et recouvrement.
- Deux familles retenues : déplacement en peu de temps (77,6 %) et durée élevée pour le déplacement (22,4 %).
- Environ 9,8 % d’appartenances ambiguës au seuil 0,8 ; tous les trajets restent dans les tableaux.
- Graphes de groupes limités aux appartenances ≥ 0,8, avec le fichier entier comme référence.

## 4. Analyse fonctionnelle retenue (F)
- Pour chaque station : huit tranches de trois heures, en semaine, sur l’historique des premiers 60 %.
- Signature = part des départs − part des arrivées ; normalisations séparées, pas un bilan absolu de vélos.
- Arrivées datées avec la durée réelle ; lissage +3 par tranche ; au moins 100 départs et 100 arrivées.
- Chaque trajet reçoit les deux signatures : 16 variables, sans standardisation séparée des tranches.
- Couverture : 692 835 trajets (59,7 %) ; les signatures absentes excluent uniquement de cette analyse.
- ACP à au moins 90 % de variance : sept axes, 92 % ; K-means utilise ces sept axes, pas seulement deux.
- K = 3 choisi par silhouette parmi 2–6 : vers les pôles récepteurs du matin, peu polarisées, sens inverse.
- Sur la période finale : environ 14 %, 72 %, 14 % ; heures et weekend décrivent ensuite ces profils.
- Même liaison orientée = même contexte : ce modèle ne distingue pas tous les usages individuels d’une liaison.

## 5. Contrôles et croisement
- Réapprentissages : échantillons, systèmes exclus, extrêmes/arrondis physiques, axes/lissage fonctionnels.
- ARI minimum : physique 0,971 ; fonctionnel 0,941 ; signatures réestimées plus tard 0,636.
- Diagnostics GPS, marges d’affectation, concentration et bootstrap conservés et recalculés.
- Croisement sur les mêmes trajets : durées élevées ≈ 24 % dans le peu polarisé, contre 13–15 % ailleurs.
- Contrôle dans 59 systèmes : écart médian +6 points, positif dans 83 % ; association non universelle.
- Le croisement ne constitue pas six nouvelles classes et ne démontre aucune causalité.

## 6. Présentation, reproductibilité et limites
- Un seul notebook autonome ; préparation commune au départ et tableau historique A–J à la fin.
- Simplification limitée à l’écriture du code ; raisonnements, diagnostics et contrôles conservés.
- A, E et F sont reproduits ; H est repris comme contrôle ; les autres essais sont des résumés historiques.
- Exécuter dans l’ordre ; graines fixées et versions affichées, sans garantie d’identité entre environnements.
- Validation interne : les périodes ont déjà été explorées ; stabilité ne signifie pas classes naturelles.
- Centres approximatifs, couverture partielle et sélection initiale inconnue limitent la généralisation.
- Aucun profil ne prouve travail, balade, sport, arrêts ou trajet familial ; le vélo peut refléter l’offre locale.
