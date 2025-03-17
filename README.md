# 🎓 Automatisation de l'émargement

Ce projet est un fork de [MTlyx/Emarge](https://github.com/MTlyx/Emarge), qui :

- Supporte les cours qui chevauchent des créneaux de cours classiques (par example un cours de 9h00 à 10h30, chevauche les plages 8h00-9h30 et 9h45-11h15).

- Supporte les changements d'emploi du temps durant la journée. Cela fonctionne en récupérant la liste des cours toutes les 30 minutes, et si un changement est détecté, les heures d'émargement sont recalculées (sauf pour le créneau actuel pour éviter des double émargements ou des omissions d'émargements).

- Supporte l'anglais, en spécifiant `LANG=EN` dans le `docker-compose.yml`.

- Sépare les variables d'environnement sensibles dans un fichier `secrets.env`.

- Augmente le délai entre le actions effectuées par Selenium, pour bien laisser le temps à la page de se charger.

- Vérifie si l'émargement a bien été effectué en recherchant un message spécifique sur la page d'émargement. Ajoute également un système de notifications permettant d’être informé en cas de succès ou d'échec (configurable avec `TOPIC` dans `secrets.env`).

- Possède une version plus à jour de `geckodriver`, et qui fixe la version de `selenium` pour garantir la compatibilité avec le driver.

## Configuration

### `docker-compose.yml`

| Variable    | Description                                    | Valeurs possibles                                              |
| ----------- | ---------------------------------------------- | -------------------------------------------------------------- |
| `FORMATION` | Formation de l'étudiant                        | `cyberdefense`, `cyberdata`, `cyberlog`                        |
| `ANNEE`     | Année d'étude                                  | `3`, `4`, `5`                                                  |
| `TP`        | Numéro du groupe de TP                         | `1` à `6`                                                      |
| `BLACKLIST` | Liste de mots-clés pour exclure certains cours | Ex. `Entrainement Le Robert, Activités HACK2G2, Activités GCC` |
| `LANG`      | Langue de Moodle                               | `EN` pour anglais, `FR` (par défaut)                           |

Exemple de configuration d'un cyberdefense en 3eme année dans le TP 1 :

```yaml
- FORMATION=cyberdefense
- ANNEE=3
- TP=1
- BLACKLIST=Entrainement Le Robert, Activités HACK2G2, Activités GCC
```

### `secrets.env`

| Variable   | Description                                           |
| ---------- | ----------------------------------------------------- |
| `USERNAME` | Identifiant UBS                                       |
| `PASSWORD` | Mot de passe UBS                                      |
| `TOPIC`    | Identifiant du canal de notification ntfy (optionnel) |

Exemple de configuration :
```yaml
USERNAME=e123456
PASSWORD=SuperSecurePassword
TOPIC=UnTrucRandom
```

## Notifications

Les notifications sont gérées avec [ntfy.sh](https://ntfy.sh/). C'est très simple d'utilisation :

1. Installez l'application [ntfy.sh](https://ntfy.sh/) (depuis les stores officiels, F-Droid ou [source](https://github.com/binwiederhier/ntfy))

   ![ntfy.sh](https://raw.githubusercontent.com/binwiederhier/ntfy/refs/heads/main/.github/images/screenshot-phone-main.jpg)

2. Appuyez sur le `+` en bas a droite et entrez un *topic* (nom unique pour votre canal de notification, l'application enverra les notifications à ce *topic*).

> [!NOTE]
> Les *topics*  sont partagés et hébergés sur un serveur public, entrez donc une valeur aléatoire pour éviter de recevoir des notifications indésirables envoyées par d'autres personnes.

3. Entrer le *topic* que vous avez utilisé dans le fichier `secrets.env`, et relancez le Docker.
