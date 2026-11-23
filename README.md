# SMT-Goupil — bibliothèque des ROM

Images des EPROM des ordinateurs **SMT-Goupil** (BIOS, moniteurs, micrologiciels de cartes
et d'accessoires), avec la description de chacune et les écrans qu'elles affichent.

Le classement suit la nomenclature PROM d'origine de SMT-Goupil (tableau « Système /
Version PROM / Désignation / Type d'EPROM / C.R.C / Code article »), reproduite dans
[docs/nomenclature-proms-1.png](docs/nomenclature-proms-1.png) et
[docs/nomenclature-proms-2.png](docs/nomenclature-proms-2.png).

Les écrans de démarrage (POST) et de configuration (setup) de chaque ROM sont réunis dans
[captures/](captures/README.md).

Ce qui relie les BIOS entre eux (dates de livraison, BIOS quasi identiques, dates des ROM
vidéo, étiquettes `FFE8`, chronologie, sources SMT consultées) est réuni
[avant le catalogue](#ce-qui-relie-les-bios-entre-eux) ; le reste est rangé avec chaque
machine.

## Lire ce catalogue

- **C.R.C** : somme sur 16 bits de tous les octets du fichier, la méthode de la
  colonne C.R.C du tableau SMT. SMT ajustait un octet de chaque PROM pour tomber sur une
  valeur ronde (`xx00`). Cette valeur sert à identifier un dump sans ambiguïté.
- **✓** : la somme du fichier correspond à la ligne du tableau SMT. **≠** : la somme
  diffère, voir la remarque. Pas de marque : la PROM n'apparaît pas dans le tableau.
- **Paires P/N, EV/OD, H/L, IC9/IC10, U14/U15** : les cartes 16 et 32 bits utilisent
  deux EPROM, une pour les octets pairs et une pour les octets impairs. Les deux
  fichiers d'une paire vont ensemble. Pour lire l'image complète, entrelacez-les octet
  par octet.
- **Doublons** : un même contenu fourni sous plusieurs noms n'est stocké qu'une fois
  par dossier. Les autres noms sont donnés dans la remarque. Une image partagée par deux
  machines figure dans les deux dossiers (git ne la stocke qu'une fois).

```
bios/
├── Club/                  Goupil Club
├── G2/                    Goupil 2
├── G3/                    Goupil 3 (Z80, 6809, 8088)
├── G4/                    Goupil 4 (80186) + sources assembleur du BIOS V1R4
├── G40/                   Goupil 40 (80286)
├── G5/                    Goupil 5 : S86, 286, 386, 386DX, SX, G286E (Normerel)
├── G50/                   Goupil 50 (80386)
├── G6/                    Goupil 6 : SX, SX AC, DX, 486, carte ISA32-B, BIOS ATON ISA-B
├── G60/                   Goupil 60 EISA + disquette de configuration ECU
├── G100/                  Goupil 100 + terminal GT220
├── Golf/                  Golf XT, Golf 286, Golf SX, Golf 486 (BIOS modifié)
├── Strator/               terminal point de vente Strator
├── SFENA-5090/            SFENA 5090
├── Accessoires/           claviers, cartes vidéo, contrôleur disque, carte réseau
└── Utilitaires/           PROM d'effacement du mot de passe, utilitaire DOS du Club
captures/                  écrans de démarrage et de setup, polices de caractères
docs/                      nomenclature PROM de SMT-Goupil, carte de test G16
```

---

## Ce qui relie les BIOS entre eux

Relevés faits sur les 55 images de BIOS PC du dépôt (paires paire/impaire entrelacées) et
recoupés avec la documentation SMT listée [à la fin de cette section](#sources-documentaires).

### BIOS de même date

| Date | BIOS |
|---|---|
| 21/09/88 | G40 V2R5, G5 286 V1R5 et V2R5, G50 V1R5 |
| 26/06/89 | G5 S86 V1R3 et V2R3, Golf XT V1R3, Strator |
| 07/11/89 | G5 286 V3R6, G5 386 V2R6, G50 V2R6, Golf SX V1R6 |
| 11/01/90 | Golf 286 V1R6B, G50 V2R6B, PROM RAZ-PWR6 |
| 12/04/90 | G5 SX V1R7, Golf SX V1R7, G50 V3R7 |
| 15/06/90 | G5 286 V3R7 et V4R7, G5 386DX V1R7, G50 V2R7, Golf 286 V1R7 |
| 04-06/03/91 | ISA16-B R3.4, ISA32-A R2, ISA32-B R2, ISA32-B R3.4 |

Le chiffre R est le même pour tous les BIOS d'une même date (R5 en 1988, R6 fin 1989, R7 au
printemps 1990). À partir de 1991, les BIOS portent le nom d'un
bus (**ISA16** pour le 286, **ISA32** pour le 386/486, **EISA**) et non plus d'une machine.

### BIOS quasi identiques (comparaison octet à octet)

| Paire | Écart | Ce qui diffère |
|---|---|---|
| G5 S86 V2 `G5MON-86 V2R3` ↔ Golf XT `GLMON-86 V1R3` (= Strator) | **6 octets** | nom, version, sommes, et une constante d'initialisation disquette (`8A79` : `mov al,2` sur G5, `mov al,4` sur Golf) |
| G5 286 `V3R7` ↔ G5 286 `V4R7` | **2 octets** | le chiffre de version et la somme |
| G5 286 `V4R7` ↔ Golf 286 `GLMON-286 V1R7` | **4 octets** | nom, version, somme, et le délai d'attente de l'interruption disquette (`A204` : `mov al,0Ah` sur G5, `12h` sur Golf) |
| G6 SX / SX AC / DX / 486 | identiques | une seule image V1R1.4 |
| Golf SX `GLMON-SX V1R6` ↔ RAZ-PWR6 | 88 % identiques |  |
| Golf SXC « DGI » ↔ G5 SX | — | le BIOS du Golf SXC s'annonce `G5MON-SXC V1R6B`, celui du G5 SX `G5MON-SXC V1R7` |
| G5 SX V1R7 ↔ Golf SX V1R7 | 85 % identiques | même date 12/04/90 |
| G5 386DX V1R7 ↔ G50 V2R7 | 81 % identiques | même date 15/06/90 |
| G5 286 V3R6 ↔ G5 386 V2R6 | 85 % identiques | même date 07/11/89 |
| ISA32-B R3.4 ↔ EISA-A R3.5 (G60) | 66 % identiques |  |

Entre un Golf et le G5 de même génération, un seul octet fonctionnel diffère, dans la gestion
du lecteur de disquette. Le cavalier E30 du Golf
286 dépend du contrôleur de disquette WD37C65 monté (sans suffixe ou A/B, NIG 5).

### Dates des ROM vidéo

Chaque ROM vidéo porte sa propre date en `5FF5`, au même format MM/JJ/AA que celle du BIOS
système en `FFF5`, à partir de `PVGA V3` (fin 1989).

| BIOS | Date vidéo `5FF5` | Date système `FFF5` |
|---|---|---|
| G6 V1R1.4 | 8 août 1990 | 10 août 1990 |
| G60 V1R3.1 | 5 nov. 1990 | 7 déc. 1990 |
| ISA32-A R2 / B R2 / B R2.2 | 22 janv. 1991 | 5 mars / 5 mars / 1er févr. 1991 |
| ISA32-B R3.4 | 7 févr. 1991 | 6 mars 1991 |
| ISA16-B R3.4 | 15 févr. 1991 | 4 mars 1991 |
| EISA-A R3.5 | 13 mars 1991 | 30 avril 1991 |
| ATON EISA-A R3.6 / R3.9 | 26 / 27 déc. 1991 | 26 déc. 1991 / 13 avril 1992 |
| ATON ISA-B R5.4 | 27 déc. 1991 | 23 avril 1996 |

Par rapport à ISA32-B R2, ISA32-B R3.4 a une ROM vidéo plus récente (20 351 octets
différents), un emplacement de copyright en `E004` et une aide du débogueur remaniée.

### Étiquette en `FFE8`

| BIOS | `FFE8` |
|---|---|
| G6 V1R1.4, G60 V1R3.1 | aucune |
| ISA16-B R3.4 | `ABGOUPIL` |
| ISA32-A R2 | `BAGOUPIL` |
| ISA32-B R2, R3.4 | `BBGOUPIL` |
| EISA-A R3.5, ATON EISA-A R3.6 | `CAGOUPIL` |
| ATON EISA-A R3.9 | `CAATON  ` |
| ATON ISA-B R5.4 | `BBATON  ` |

La première lettre correspond au bus (A = ISA16, B = ISA32, C = EISA), la deuxième à la
variante (A/B), la suite à l'éditeur. Aucun BIOS ne lit ces octets.

### Débogueur intégré : 1 Ko perdu en R6 et R6B

BIT 210 (28/08/1990) : avec les BIOS **VxR6 et VxR6B**, le débogueur intégré validé fait
perdre 1 Ko de mémoire (visible avec CHKDSK) ; corrigé en VxR7. Dans les ROM : `sub word
[40:13h],1` puis segment du Ko réservé rangé en `40:06h`, présent dans **toutes** les R6/R6B
(G5 286 V3R6, G5 386 V2R6, G50 V2R6 et V2R6B, Golf 286 V1R6B, Golf SX V1R6, Golf SXC
V1R6B, RAZ-PWR6) et dans **aucune** R5 ni R7. La routine suivante teste le port `84h` bit 0
(accès débogueur).

### Ce que les BIOS ne gèrent pas

- **Lecteur de bande** : aucune des 55 images ne contient de code ni de texte de lecteur de
  bande (`tape`, `streamer`, `QIC`, `bande`).
- **Souris InPort** : aucun BIOS n'accède aux ports InPort (`23Ch`-`23Fh`). Voir le [G5](#carte-mère-du-g5--notice-schémas-et-notes-smt).

### Chronologie

| Date | Événement | Source |
|---|---|---|
| janv. 1988 | Golf 286 (test PCW) | nosher.net |
| début 1990 | rachat de Normerel, devenu branche OEM | Tech Monitor 05/07/1990 |
| avril 1990 | Golf 386SX annoncé | nosher.net |
| juillet 1990 | gamme G6 annoncée (286, deux 386, 486), livraisons dès septembre | Tech Monitor |
| 8-10 août 1990 | G6 V1R1.4 compilé | ROM |
| déc. 1990 (sem. 49) | carte Golf 486 fabriquée | sérigraphie |
| janv.-avril 1991 | ISA16 / ISA32 / EISA R2-R3.5 | ROM |
| juin 1991 | dépôt de bilan de SMT-Goupil | Wikipédia |
| 26-27 déc. 1991 | ROM vidéo et EISA R3.6 datées | ROM |
| avril 1992 | ATON EISA-A R3.9 (G60) | ROM |
| avril 1996 | ATON ISA-B R5.4 | ROM |

### Sources documentaires

Goupil Revue n° 1 à 7 (mars 1982 – 1984), NIG n° 5, 13, 14, 29, 30, BIT n° 19-27, 35-38,
106, 200-212, notes « Informations techniques diverses » (G40, G3/G4/G40), notes diverses,
notice mémoire G5 (05/1988), notice et schémas de la carte mère du G5, manuel du moniteur
couleur multimode (1986), notes GoupilNet DOS 3.1 (1985). Compilations retronik.silicium.org
(Bjorn Darkaad, 2024). Ces documents ne sont pas dans le dépôt.

Autres informations tirées de ces documents :

- **OS/2 1.0 et 1.1 adaptés par SMT** (NIG 14, 06/1989) : FDISK maison, 4 partitions, jusqu'à
  128 Mo, démarrage sur n'importe quelle partition ; PROM EGA reconnue à toute adresse.
- **3614 code SMTT** : NIG et BIT consultables sur Minitel.

---

## Club — `bios/Club/`

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `CLUB_U45A.BIN` | 27C256 | 4200 | Moniteur du Goupil Club (8088, position U45), `G4MON-88 V2R1` et `CMON-6301` : la version qu'affiche un Club réel (« CMON-6301 V1R2 -- G4MON-88 V2R1 »), plus récente que la V1R1 de `CLUB1_U45B.BIN`. Dump validé sur une machine réelle : la ROM fonctionne sur un Club. PROM absente du tableau SMT (qui ne donne que l'U45B du Club V1), d'où la somme sans marque. Elle porte l'indice A, la V1R1 l'indice B. Différences avec la V1R1 [ci-dessous](#g4mon-88-v2r1--ce-qui-change-par-rapport-à-la-v1r1). |
| `CLUB1_U45B.BIN` | 27C256 | 5500 ✓ | Moniteur du Goupil Club V1 (8088, position U45). Contient `G4MON-88 V1R1` et `CMON-6301` : BIOS 8088 dérivé du G4, plus le dialogue avec le microcontrôleur Hitachi 6301, qui sert d'horloge temps réel et porte sa propre version (`V1R2` sur un Club réel ; liaison série bit à bit sur le 8255). |
| `CLUB_U97A.BIN` | 27C64 | DDC0 ✓ | Générateur de caractères (polices vidéo) du Club, position U97. Presque identique à la police de la carte vidéo G4GA (9 octets d'écart). |
| [`../Utilitaires/P10AHFF/P10AHFF.COM`](bios/Utilitaires/P10AHFF/P10AHFF.COM) | — | 15CA | Utilitaire DOS du Club, rangé avec les [utilitaires](#utilitaires--biosutilitaires) : il empêche Norton Commander de faire perdre l'écran LCD interne, voir [ci-dessous](#g4mon-88-v2r1--ce-qui-change-par-rapport-à-la-v1r1). |

### G4MON-88 V2R1 : ce qui change par rapport à la V1R1

Comparaison des deux ROM désassemblées. Le code de la V2R1 est plus long de 22h octets :
les routines sont décalées de 21h à 2Ch octets, et la partie haute (`F000:E000h`-`FFFFh` :
points d'entrée fixes compatibles IBM, polices) est identique, à part les adresses des
routines déplacées. La bannière ne change pas de forme : `CMON-6301  V1R2 -- G4MON-88  V2R1`.
Les vraies modifications :

- **Copyright** : la chaîne ` (C) Copyright SMT-Goupil 1986 ` est ajoutée après les noms de
  version, en tête de ROM. Aucun code ne l'affiche. Elle date la V2R1 de 1986 au plus tôt.
- **POST, 6301** : il y a une tâche de plus, juste après l'initialisation du 6301 (deux
  écritures de `00h` dans son registre `1Fh`). Elle écrit `00h` dans le registre `27h`,
  que la V1R1 n'utilise jamais. Si le 6301 ne répond pas, elle donne le même code d'erreur
  (03) que les autres échanges avec le 6301. Le rôle de ce registre n'est pas connu :
  il faudrait le code du 6301 (`CMON-6301`).
- **Disquette (INT 13h)** : quand le contrôleur 765 est réinitialisé, le BIOS lui envoie la
  commande Specify (`03h`) avec le premier octet de la table des paramètres de disquette
  (vecteur `INT 1Eh` : vitesse de pas SRT et temps de déchargement HUT). La V1R1
  limitait la vitesse de pas : un SRT de `Bh` à `Fh` (5 ms à 1 ms au débit de 500 kbit/s)
  était ramené à `Ah` (6 ms). La V2R1 envoie l'octet tel quel. La vitesse de pas inscrite
  dans la table est donc respectée, y compris quand DOS installe la sienne.
- **Moniteur** (invite `+`) : l'interpréteur appelle chaque commande entre `PUSH DS` et
  `POP DS`. En V1R1, une commande qui changeait DS dérangeait l'interpréteur pour la suite.
- **Vidéo du Club** (`INT 10h` fonction `FFh`, propre au Club) : cette fonction programme
  le contrôleur vidéo de la carte (ports `3D0h`-`3DFh`) avec l'un de deux jeux de réglages.
  Le choix se fait sur le bit 0 du registre `17h` du 6301 (lu par l'`INT 1Ah` fonction
  `FDh`) ; le POST appelle la fonction une seule fois, avec AL égal à ce bit. AL non nul
  donne le jeu de l'**écran externe** (prise DB9), avec une palette de 16 couleurs (3 bits
  par composante rouge, vert, bleu) : il ne change pas. AL = 0 donne le jeu de l'**écran
  LCD interne**, qui change :
  - en V1R1, ses 16 entrées mêlaient vert et bleu sans ordre régulier. La couleur 0 (le fond)
    y était la plus claire et la couleur 15 était noire : des pixels sombres sur fond clair ;
  - en V2R1, la couleur n donne le niveau n modulo 8 sur la seule composante verte, de
    0 (noir) à 7 ;
  - deux registres de ce jeu (index `66h`-`67h`) passent aussi de `28h`, `14h` à `24h`, `33h`.

  Sur un Club réel, l'écran LCD ne montre pas de différence visible entre les deux versions.

  N'importe quel programme peut appeler cette fonction. Norton Commander et ses visionneuses
  (`NC.EXE`, `123VIEW.EXE`, `DBVIEW.EXE`) appellent l'`INT 10h` fonction `FFh` (sur un PC, la
  mise à jour de l'écran de TopView/DESQview), avec un AL quelconque. Sur un Club réel, le
  lancement de Norton fait ainsi perdre l'affichage sur le LCD au profit de l'écran externe.
  L'utilitaire [`P10AHFF.COM`](#utilitaires--biosutilitaires) (47 octets, 30/01/1987, conservé
  dans `bios/Utilitaires/P10AHFF/`), chargé dans l'`AUTOEXEC.BAT` avant Norton, l'évite : un
  programme résident qui intercepte
  l'`INT 10h` et rend la main sans rien faire quand AH = `FFh`. L'écran choisi au démarrage
  est ainsi conservé. Le mécanisme est le même avec la V1R1, qui a la même fonction.

En dehors de ces points, le code est identique instruction par instruction : tests du POST,
interrupteurs, mesure de la mémoire, commandes du moniteur, dialogue avec le 6301.

## Goupil 2 — `bios/G2/`

Machine 6800 de 1979. EPROM 2516 et 2716 de 2 Ko.

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `MON1_G2_MN2D_24X80_8_pouce.BIN` | 2516 | D785 ✓ | Moniteur MON1 (MN2D), affichage 24×80, amorçage sur disquette 8 pouces. Le dump contient des restes de source 6800 (`BOOT85`, `LDAB COMR91 GET WD STATUS`…). Aussi fourni sous le nom `MON1_24X80_8pouce_MN2D_G2.BIN`. |
| `MON2_G2_MN3D_24X80_8_pouce.BIN` | 2516 | AD9B ✓ | Moniteur MON2 (MN3D), même configuration, partie haute (`F800h`-`FFFFh`, vecteurs du 6800 à la fin). Bannière `GOUPIL 1.4 8/81` en `F980h` : moniteur 24×80 V1.4 d'août 1981. Aussi fourni sous le nom `MON2_24X80_8pouce_MN3D_G2.BIN`. |
| `VIDEO_G2_SMT.BIN` | 2716 | B38A ≠ B3A4 | Générateur de caractères « SMT-VIDEO ». |
| `VIDEO_SMT_G2.BIN` | 2716 | B39C ≠ B3A4 | Deuxième dump de la même PROM, avec 2 octets d'écart (`0067h`, `0325h`). Aucun des deux ne donne la somme du tableau : au moins l'un des deux est altéré. Il faudrait refaire le dump. |

**Octet de version.** Goupil Revue n° 2 (1982) indique que la version du moniteur se lit sous
moniteur en `FD04h` (`+M FD04`) : `13` pour l'écran 16×64 et les disques 5" simple densité,
`14` pour l'écran 24×80 et les disques 5", 8" ou durs. Dans MON2, `FD03h` contient
`BITA #$14` : une instruction sans effet qui sert à loger l'octet de version à adresse fixe.
Les PROM moniteur étaient de couleur différente selon la configuration : rouges (16×64),
vertes (24×80), bleues (disque dur).

**Matériel** (Goupil Revue) : 16, 48 ou 64 Ko de mémoire (DRAM 4116) ; G2FLEX2 remonté de
8 Ko pour préparer le passage au 6809 (FLEX9) ; disque Winchester 5" de 5 Mo sur WD1000.

## Goupil 3 — `bios/G3/`

Le G3 accepte plusieurs cartes processeur : Z80, 6809 et 8088.

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `G3_Z80.2.8.BIN` | 2732 | E5A3 ✓ | Moniteur de la carte Z80, bannière « Moniteur Z80 - Goupil 3 - V 2.8 » : postérieur à la V2.3 du BIT n° 23 (09/1984). |
| `UC09.2.1D-A_G3_6809.BIN` | 2764 | B26C ✓ | Moniteur `G3MON-6809 V2R0` de la carte 6809, partie basse (`4000h`-`5FFFh`). |
| `UC09.2.3D-A_G3_6809.BIN` | 2764 | AD33 ✓ | Moniteur `G3MON-6809 V2R0`, partie haute (`6000h`-`7FFFh`, vecteurs du 6809 à la fin). Ce n'est pas une PROM à part : une instruction est à cheval sur les deux (`CMPA #'-'` en `5FFFh`). |
| `UC88.1.6B-C.BIN` | 2716 | DCED ✓ | PROM de la carte 8088 (UC88-1/6B-C). |
| `VISU.1.1D-B.BIN` | 2716 | 7EC0 ✓ | Générateur de caractères de la carte de visualisation VISU (VISU-1/1D-B). Le tableau la range avec le G3 8088 ; elle était fournie dans le dossier G2. |
| `RAPR1.5B-C_G3MON_8088_V1R2.BIN` | 2764 | 29FE | Moniteur `G3MON-8088 V1R2`, partie haute (RAPR1.5B-C, 8 Ko : bannière, aide). |
| `RAPR1.6B-C_G3_MON.BIN` | 27128 | F7C9 | Moniteur `G3MON-8088 V1R2`, partie basse (RAPR1.6B-C, 16 Ko : point d'entrée, messages « Erreur parite memoire! », « Piege interruption! »). Ce n'est pas un moniteur à part : les deux PROM forment un seul moniteur de 24 Ko, en `C0000h`. |
| `G3_8088_Test_PC.BIN` | 2764 | A054 | PROM de test d'usine : « Test cartes CPU, RAM/PROM 8088 et 640X400 », version du 19-8-84. |

Écrans des moniteurs 8088 et 6809 : voir les [captures](captures/README.md#g3).

**Versions d'après SMT.** Le bulletin d'information technique n° 23 (13/09/1984) liste les
moniteurs alors en vigueur : 6809 `UC09.2 3D-B` V2R2, 8088 `RAPR.1 6B-C` + `RAPR.1 5B-C` V1R2,
Z80 `Z80.2 1B-C` V2.3, UNIBUG `30-C` V2.3. Le moniteur 8088 du dépôt est exactement celui-là ;
il est sur la carte RAM/PROM et sa mise à niveau demande d'ajouter le strap S13. Le 6809 du
dépôt (V2R0, indice A) est antérieur : la V2R2 n'a pas été dumpée. Pour les disques Winchester,
le moniteur 6809 doit être au moins en V1R3 (BIT n° 24). Le G3PC utilise d'autres PROM 8088,
`RAPC 1.5C` et `RAPC 1.6C` (BIT n° 35, 1985).

**Carte 6809.** Le moniteur `G3MON-6809 V2R0` (16 Ko en deux PROM) est le frère du
G3MON-8088 : mêmes tables du 6845 et du clavier (accents compris), mêmes registres de la
carte graphique (`F320h` et `F358h` ici, `D320h` et `D358h` côté 8088). Il n'a aucun code
commun avec le moniteur 6800 du G2. Ce qu'on lit dans le code :

- un gestionnaire de mémoire à pages de 4 Ko : une écriture en `FFF0h`+*n* choisit la
  page physique (adresses de 20 bits) vue dans la page logique *n* ;
- un petit exécutif : services appelés par `SWI n`, vecteurs d'interruption renvoyés en
  RAM (`EFF4h`-`EFFFh`), modules d'extension repérés par l'octet `A5h` ;
- la visu : texte 80 × 25 en `0000h` (page physique `FEh`, la même adresse `FE000h` que
  sur la carte 8088), RAM du générateur de caractères en `0800h`, 6845 en `F380h` ;
- deux ACIA 6850 : ligne série en `F000h`, clavier et console en `F004h` (sans visu, la
  console sort sur cette ACIA) ;
- disquette : contrôleur de type WD179x en `F12Ch` et DMA (probablement un 6844) en
  `F100h` ; la touche BOOT lit le secteur 1 en `C100h` et l'exécute ;
- les commandes B, G, I, M, O, P, R, S et T (T affiche la table des pages), sans
  commande d'aide ; erreur « ? », invite `+`. Touche COM (`D8h`) : mode terminal ;
  Ctrl + BOOT (`CAh`) : mode local.

Goupil Revue n° 7 (1984) documente le service `SWI` + `FCB $0A` (`ACCMEM`) : A = octet de
poids fort de l'adresse physique, B = page 6809 où la placer. Les entrées-sorties sont en
`FF000h`-`FF7FFh` ; une fois mappées en `F000h`, l'ACIA 1 est en `F000h`-`F001h`, comme dans
la ROM.

**Machine** (Goupil Revue n° 4, 7 et notes) : sortie le 1er janvier 1983 ; deux cartes
processeur parmi 6809, Z80 et 8088 peuvent cohabiter, choisies par commutateur ; la carte
8088 n'a pas de RAM (carte 256 Ko obligatoire) ; bus compatible **G64** ; espace d'adresses
de 1 Mo par pages de 4 Ko ; MS-DOS **2.06** (puis 2.11), jusqu'à 6 lecteurs A-F ; la
configuration de base peut utiliser un **Minitel** comme clavier-écran ; cartes synthèse
vocale Alcatel, IEEE-488, Vidéotex.

## Goupil 4 — `bios/G4/`

PC compatible à 80186. PROM système en paire 11J/12J (V1) puis 7Q/7S (V2).

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `CPU.4.1.11J-D.BIN` + `CPU.4.1.12J-D.BIN` | 2764 ×2 | 0700 ✓ / E600 ✓ | G4 V1, BIOS `G4MON-186 V1R4`. |
| `CPU.4.2.7Q-D_G4_V2.BIN` + `CPU.4.2.7S-D_G4_V2.BIN` | 27128 ×2 | 6E00 / 2B00 | G4 V2, BIOS `G4MON-186 V2R4` (indice D, plus ancien que celui du tableau). |
| `CPU.4.2.7Q-E.BIN` + `CPU.4.2.7S-E.BIN` | 27128 ×2 | 7F00 ✓ / FB00 ✓ | G4 V2, BIOS `G4MON-186 V3R1`, indice E, la version du tableau. |
| `G4V2R2_ARL2_7S-B.BIN` | 27128 | 32CE | G4 V2, BIOS `G4MON-186 V2R2`, PROM 7S seule (indice B). La PROM 7Q associée manque. |

**Carte EGA** (notes SMT) : en V1R4 retirer E23 ; en V2R2 retirer E21 et mettre les contacts
5 et 6 de SW1 sur ON.

**Moniteur couleur multimode** (manuel de 1986) : 13", tube 370HYB22, 15,5 à 35 kHz
automatique, CGA/EGA/PGA et analogique, pour G4 et G40. Fabricant non indiqué.

### Sources — `bios/G4/sources-V1R4/`

| Fichier | Description |
|---|---|
| `G4MON.L1`, `G4MON.L2` | Listings d'assemblage MASM 1.27 du BIOS V1R4, datés du 08-02-85 (« GOUPIL4 PC compatible Rom I/O System ») : diagnostics de mise sous tension (POD), pilote disquette, etc. Ce sont des fichiers texte, pas des images de PROM, mais ils documentent le code des deux PROM V1. |

## Goupil 40 — `bios/G40/`

PC AT à 80286, BIOS `G4MON-286`.

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `CPU40.1.2K-C_G40_V1.BIN` + `CPU40.1.5J-C_G40_V1.BIN` | 27128 ×2 | 4C8B / 7375 | G40 V1, BIOS `G4MON-286 V1R3`. |
| `PGM-C40.2.2K-J_G40_V1_V2.BIN` + `PGM-C40.2.5J-J_G40_V1_V2.BIN` | 27128 ×2 | 3000 ✓ / 7D00 ✓ | G40 V1/V2, BIOS `G4MON-286 V2R5` (C40.2-2K/J et 5J/J du tableau). |

**Table des disques durs.** Les types 1 à 14 de `V1R3` sont ceux de la note SMT « Informations
techniques diverses » du G40 : 306/4 (NEC 5124, 10 Mo), 615/4 (ST225, NEC 5126), 977/5
(ST4051), 615/8 (NEC 5146), 925/5 (Wren II), 733/5 (ST4038), 823/10 (NEC 5452), 1024/9
(ST4096, Wren II 80 Mo). `V2R5` remplace les types 8, 9 et 14 et ajoute un type 16 ; les types
9, 14 et 16 sont à 34-35 secteurs par piste (disques RLL ou ESDI).

**Coprocesseur 80287** (BIT 106) : la PAL `C40.3FB` remplace `C40.3FA` ; test `T287`.

La carte EGA Paradise et la carte vidéo V40GA du G40 sont rangées dans
[Accessoires](#accessoires--biosaccessoires).

## Goupil 5 — `bios/G5/`

### G5 S86 (8086) — `bios/G5/S86/`

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `MSG5.7F-C.BIN` + `MSG5.9F-C.BIN` | 27C256 ×2 | 9700 ✓ / 3000 ✓ | G5 S86 V1 EGA, BIOS `G5MON-86 V1R3`. |
| `MSG5.2.5E-B.BIN` + `MSG5.2.6E-B.BIN` | 27C256 ×2 | 2300 ✓ / 8100 ✓ | G5 S86 V2 VGA, BIOS `G5MON-86 V2R3`, avec le BIOS VGA Paradise `PVGA V2` intégré. |

### G5 286 — `bios/G5/286/`

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `M2G5.1.3L-B.BIN` + `M2G5.1.4L-B.BIN` | 27C256 ×2 | CD00 / 1500 | G5 286 V1 EGA, BIOS `G5MON-286 V1R2` (indice B, 1987). |
| `G5286EV_V1R2.4.BIN` + `G5286OD_V1R2.4.BIN` | 27C256 ×2 | 1300 / 3C00 | G5 286 V1, BIOS `G5MON-286 V1R4.2` (EV = octets pairs, OD = impairs). |
| `M2G5.1.3L-D.BIN` + `M2G5.1.4L-D.BIN` | 27C256 ×2 | 6C00 ✓ / AD00 ✓ | G5 286 V1 (6C) EGA, BIOS `G5MON-286 V1R5`, 8 et 10 MHz. |
| `M2G5.2.3B-A.BIN` + `M2G5.2.4B-A.BIN` | 27C256 ×2 | B900 / A100 | G5 286 V2 (4C) EGA, BIOS `G5MON-286 V2R4` (indice A). |
| `M2G5.2.3B-B.BIN` + `M2G5.2.4B-B.BIN` | 27C256 ×2 | 0400 ✓ / 4900 ✓ | G5 286 V2 (4C) EGA, BIOS `G5MON-286 V2R5`, 8 à 12,5 MHz. |
| `M2G5.2.3B-1.BIN` + `M2G5.2.4B-1.BIN` | 27C256 ×2 | 3700 ✓ / E000 ✓ | G5 286 V3 VGA (GVGA16-V2), BIOS `G5MON-286 V3R5` + `PVGA V2`. |
| `M2G5.3.3B-A.BIN` + `M2G5.3.4B-A.BIN` | 27C256 ×2 | 0600 / CE00 | G5 286 V3 VGA, BIOS `G5MON-286 V3R6` du 07/11/89 + `PVGA V3`. Aussi fourni sous les noms `G5286EV_V3R6-4A.BIN` / `G5286OD_V3R6-5A.BIN` et `M2G5_3_3 B_A V3R6.BIN` / `M2G5_3_4 B_A V3R6.BIN`. |
| `M2G5.3.3B-C.BIN` + `M2G5.3.4B-C.BIN` | 27C256 ×2 | 3B00 ✓ / 8100 ✓ | G5 286 V3 VGA, BIOS `G5MON-286 V3R7` du 15/06/90 + `PVGA V4`. |
| `M2G5.4.4A-C.BIN` + `M2G5.4.5A-C.BIN` | 27C256 ×2 | 3B00 ✓ / 8100 ✓ | G5 286 V4 VGA, BIOS `G5MON-286 V4R7` du 15/06/90. La PROM paire est identique à `M2G5.3.3B-C` et à `MG.1.9K-D` (Golf 286). |

### G5 386 — `bios/G5/386/`

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `M3G5.1.3M-C.BIN` + `M3G5.1.5M-C.BIN` | 27C256 ×2 | E300 / DB00 | G5 386 V1 EGA, BIOS `G5MON-386 V1R4`. |
| `M3G5.1.3M-E.BIN` + `M3G5.1.5M-E.BIN` | 27C256 ×2 | DF00 ✓ / A800 ✓ | G5 386 V1 EGA, BIOS `G5MON-386 V1R7` du 22/01/91, 16 à 20 MHz. |
| `M3G5.2.3M-E.BIN` + `M3G5.2.5M-E.BIN` | 27C256 ×2 | 3D00 ✓ / 1000 ✓ | G5 386 V2 VGA, BIOS `G5MON-386 V2R6` du 07/11/89 + `PVGA V3`. Les fichiers `M3G5.2.3M-B` / `M3G5.2.5M-B` fournis sont identiques. |

### G5 386 DX — `bios/G5/386DX/`

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `M3DX.1A-C.BIN` + `M3DX.3A-C.BIN` | 27C256 ×2 | E800 ✓ / 8200 ✓ | G5 386 DX V1, BIOS V1R7 du 15/06/90 + `PVGA V4`, 20 à 25 MHz. |

### G5 SX — `bios/G5/SX/`

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `MG5SX.1.2G-C.BIN` | 27C512 | 5500 ✓ | G5 SX V1, BIOS V1R7 du 12/04/90 en une seule PROM 64 Ko, avec le BIOS VGA `PVGA V4` et la gestion du mot de passe. |

Le BIOS **ISA32-A R2** commun au G5 et au Golf SX est rangé dans [Golf SX](#golf-sx--biosgolfsx).

### G286E Normerel — `bios/G5/G286E-Normerel/`

Le « Goupil blanc » : un 286 fabriqué par **Normerel** et vendu par SMT-Goupil.

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `G286E_IC9-A.BIN` + `G286E_IC10-A.BIN` | 27C256 ×2 | 08A4 / 335C | BIOS Normerel « ROMBIOS 80286 1985,1990 » avec BIOS VGA du 28/06/1990 (indice A). Dump incomplet, même cause que le V3.05. |
| `G286E_V3.05.IC9-F.BIN` + `G286E_V3.05.IC10-F.BIN` | 27C256 ×2 (27C512 au tableau) | D76B ≠ EE66 / 8495 ≠ 6C9A | BIOS Normerel V3.05, BIOS VGA du 30/01/1991 (indice F). Les sommes ne correspondent pas au tableau parce que le dump est **incomplet** : ces EPROM sont des 27C512 lues comme des 27C256, seule la moitié haute de chaque puce a été lue. Le BIOS saute dans la moitié manquante (`E000:4D20`) et ne peut pas démarrer. Puces d'origine plus disponibles : moitié basse perdue. Pour valider un dump complet s'il réapparaît (puces lues en 27C512, 64 Ko chacune) : IC9 `EE66` (moitié basse seule `16FB`), IC10 `6C9A` (moitié basse seule `E805`) ; les 32 derniers Ko doivent être identiques aux fichiers actuels. |
| `ISA_16B_R3.4.IC9.BIN` + `ISA_16B_R3.4.IC10.BIN` | 27C256 ×2 | E500 / 8400 | BIOS SMT-Goupil `ISA16-B R3.4` (1985-1991) avec `PVGA MV1`, monté sur la même carte Normerel. |

### Carte mère du G5 : notice, schémas et notes SMT

- **Souris InPort** (schéma folio 22/29) : puce **MSMC** en 17L, le contrôleur de souris
  Microsoft InPort (entrées XA/XB/YA/YB/SW1-3), sortie souris quadrature sur **P10**,
  interruption IT3 (IRQ3, cavalier E1), décodage d'adresse par PAL16L8A 13L avec cavaliers
  E27/E28 « ACCESS ». Aucun BIOS G5 ne gère la souris : ni InPort ni PS/2.
- **Lecteur de carte à mémoire** (notice p. 45 et 50) : connecteur **P16** « Connexion d'un
  lecteur de carte à mémoire », bord avant de la carte entre P6 (pile) et P13 (diode de
  façade) ; **E21** présent = lecteur non installé (standard), **E22** présent = installé.
  Schéma folio 23/29 : P16 (10 broches, 1 et 2 à la masse) est câblé sur le **même UART
  NS16450 13A que COM1**. Deux récepteurs RS-232 75154 : 14A pour le connecteur arrière P3,
  16A pour P16 ; les cavaliers **E21 à E24** choisissent lequel des deux alimente les
  entrées de l'UART (RxD/CTS/DSR/DCD), et « les quatre doivent avoir la même position »
  (aussi sur G5 286 rév. D et G5 386 rév. B). Les sorties passent par les 75150 15A/15B.
  Aucun des 13 BIOS G5 ne contient de code ni de message pour ce lecteur.
- **Autres cavaliers** : E10 lecteur de disquette bi-vitesse / mono-vitesse ; **E7, E8, E11
  cavaliers de test en usine** ; E18, E19, E20, E30, E31 obligatoirement présents.
- **Disques Conner CP3022 / CP342** (IDE à contrôleur intégré, BIT 201-203, 03/1990) :
  cavalier dédié sur Golf 286, G5 286 V4 et G5 386 SX, avec une 3e position « contrôleur
  externe ».
- **Vidéo PVGA intégrée et SVGA 800×600** : G5 286 V4, Golf 286 et G5 386 SX ont un
  oscillateur 36 MHz (en plus de 25,175 et 28,322 MHz) et un cavalier qui le valide « pour le
  mode SVGA ». Sur le G5 286 VGA, les interrupteurs EGA de la façade (près de la prise souris)
  ont disparu : tout passe par `MODE`.
- **128 Ko cachés du G5 286 rév. D** : avec SW8 (façade) sur OFF et E16 retiré, 768 Ko
  conventionnels ; les 128 Ko au-delà de 640 Ko sont en `D0000h`-`EFFFFh`, **non reconnus par
  le BIOS**, à initialiser par logiciel avant lecture (sinon erreur de parité).
- **Mémoire du G5 S86** : ses cartes mémoire de 1 Mo sont en mémoire **EMS** (pilote
  `EMS.SYS`), alors que les G5 286/386 sont en mémoire étendue.

## Goupil 50 — `bios/G50/`

386 avec BIOS `G50MON-386`. Sur cette machine, P désigne la PROM de droite et N celle de
gauche.

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `M5.1.12P-B.BIN` + `M5.1.12N-B.BIN` | 27C256 ×2 | 4100 / E900 | G50 V1 EGA, BIOS `G50MON-386 V1R5`. La PROM N est identique à celle de l'indice C. |
| `M5.1.12P-C_G50_V1R5B.BIN` + `M5.1.12N-C_G50_V1R5B.BIN` | 27C256 ×2 | 3D00 ✓ / E900 ✓ | G50 V1 EGA, BIOS `G50MON-386 V1R5B`, 20 à 25 MHz. |
| `G50_V2R5_M5.2.12P-A.BIN` + `G50_V2R5_M5.2.12N-A.BIN` | 27C256 ×2 | 8200 / 7D00 | G50 V2 DX VGA, BIOS `G50MON-386 V2R5` + `PVGA V2`. |
| `G50_V2R6_M5.1.12P-D.BIN` + `G50_V2R6_M5.1.12N-D.BIN` | 27C256 ×2 | 7E00 / F700 | BIOS `G50MON-386 V2R6` du 07/11/89 + `PVGA V3`. |
| `G50_V2R6B_M5.2.12P-C.BIN` + `G50_V2R6B_M5.2.12N-C.BIN` | 27C256 ×2 | 9D00 / D500 | BIOS `G50MON-386 V2R6B` du 11/01/90 + `PVGA V3`. |
| `M5.2.12P-E_G50_V2R7.BIN` + `M5.2.12N-E_G50_V2R7.BIN` | 27C256 ×2 | 4A00 ✓ / 2700 ✓ | G50 V2 DX VGA, BIOS `G50MON-386 V2R7` du 15/06/90 + `PVGA V4`. |
| `G50_12P_V2R7_DD500Mo.BIN` + `G50_12N_V2R7_DD500Mo.BIN` | 27C256 ×2 | 4A00 / 2700 | V2R7 modifié pour un disque de 500 Mo : 3 octets changés sur la P, 6 sur la N. Les sommes ont été rééquilibrées. |
| `MG50.33.U14-A.BIN` + `MG50.33.U15-A.BIN` | 27C256 ×2 | 5100 ✓ / 7200 ✓ | G50 V3 DX VGA 33 MHz, BIOS `G50MON-386 V3R7` du 12/04/90. |

**G50 rév. B** : cavalier E3 EPROM 27512/27256, E28 zone `C0000h`-`CFFFFh` cachable.

## Goupil 6 — `bios/G6/`

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `M3G6SX_V1R1.4.11H-2.BIN` | 27C512 | BC00 ✓ | G6 SX 16 MHz, BIOS `GOUPIL G6 BIOS V1R1.4` du 08/08/90 + `PVGA V4`. |
| `M3G6SX-AC.17J-2.BIN` | 27C512 | BC00 ✓ | G6 SX AC 20 MHz. Contenu identique au précédent. |
| `M3G6DX.17L-2.BIN` | 27C512 | BC00 ✓ | G6 DX 25 à 33 MHz. Contenu identique. |
| `M4G6-486.18K-2.BIN` | 27C512 | BC00 ✓ | G6 486 25 à 33 MHz. Contenu identique. Les quatre étiquettes portent la même image V1R1.4 ; elle est aussi fournie sous les noms `G6_SX_V1R1_4`, `G6_SX_AC_V1R1_4` et `G6_DX_V1R1_4`. |
| `ISA32B-R2.BIN` | 27C512 | 3000 ✓ | Carte ISA32-B, BIOS `ISA32-B R2` du 05/03/91, avec `SVGA V5`. C'est aussi le BIOS du **Golf 486** (carte mère propre au Golf, qui partage ce BIOS avec les cartes ISA32-B). Aussi fourni sous les noms `Goupil_ISA32BR2.BIN` et `ISA32_B_R2.BIN`. |
| `ISA32BR2.2.BIN` | 27C512 | 1D00 ✓ | BIOS `ISA32-B R2.2` du 01/02/91. Aussi fourni sous le nom `G6_ISA32B_R2_2.BIN`. |
| `ISA32B-R3.4.BIN` | 27C512 | 2500 ✓ | BIOS `ISA32-B R3.4` du 06/03/91, marqué **PROTO** dans le tableau. Aussi fourni sous le nom `G6_SX_ISA32B_R3_4.BIN`. |
| `ISA32B_R2_G6_SX_MODIF_TYPE36.BIN` | 27C512 | 3000 | ISA32-B R2 modifié : 12 octets changés vers `E481h`-`E57Eh`, dans la table des disques durs, pour définir un type 36. La somme a été rééquilibrée. |
| `G6ATON_ISA-B_R5.4.BIN` | 27C010 (128 Ko) | 1A00 | BIOS **ATON Systèmes `ISA-B R5.4`** du 23/04/96, dernière évolution de la lignée ISA-B, pour G6 486. Image de 128 Ko : BIOS vidéo `SVGA V5 ATON` en `0x00000` (24 Ko), vide de `0x08000` à `0x0FFFF`, BIOS système 64 Ko en `0x10000`. Ajoute la reconnaissance des i486DX2/DX4, l'option « Auto-LBA » pour les disques IDE de plus de 504 Mo et la copie du BIOS vidéo en mémoire shadow. Menus en anglais uniquement. Il faut une 27C010 et une carte qui présente la vidéo en `C0000h`. |

### Fonctions cachées des BIOS ISA SMT

**Vitesse du processeur par `INT 15h`.** Fonction non documentée, présente dans tous les BIOS
ISA SMT (G6 V1R1.4 `937E`, ISA32-A R2 `940B`, ISA32-B R2 `9430`, ISA32-B R3.4 `889F`),
absente de l'ATON.

```
9430: cmp ah,0E0h ; jb ...      INT 15h AH=E0h : régler la vitesse
9435: cmp ah,0E1h ; ja erreur   INT 15h AH=E1h : lire la vitesse
```

- `AH=E0h`, `AL=0` haute, `1` basse, `2` auto. `AL>2` → `AH=86h`, CF=1.
- `AH=E1h` → `AL=0` haute, `1` basse, `2` auto. Retour CF=0, AH=0.

Registre système Goupil, port `84h` : bit 0 accès débogueur, **bit 3 vitesse basse**,
**bit 4 mode auto**. Test sous DEBUG : `A 100` / `MOV AH,E1` / `INT 15` / `INT 3` / `G=100` → AL.

**Vitesse « Auto ».** Dans l'interruption horloge (INT 08h, ISA32-B R2 `9EC0`-`9EF5`) : si le
mode auto est actif (port `84h` bit 4) et qu'un moteur de disquette tourne (`40:3F` bits
0-3), le BIOS passe en vitesse basse (bit 3 à 1). Quand le délai d'arrêt moteur coupe le
lecteur (écriture en `3F2h`), il repasse en vitesse haute. L'aide du setup ATON, qui reprend
la fonction, indique : « AUTO is used with MS/DOS to support floppy based software protection
keys ».

### Souris PS/2 et InPort

Code et messages souris PS/2 (`<Mouse clock line error>`, `Souris standard reconnue`)
présents dans G6 V1R1.4, G60 V1R3.1, ISA16-B R3.4, ISA32-B R2/R2.2/R3.4 et ATON ; absents de
tous les G5, G50, Golf XT/286/SX et de **ISA32-A R2**. ISA32-A R2 et ISA32-B R2 : même date,
ROM vidéo identique, 92 % de code commun ; B R2 contient en plus environ 900 octets de gestion
PS/2 (`98D7`-`9C5D`).

### Du G6 à l'ATON

- ISA32-B R2 ↔ G6 V1R1.4 : 69 % identiques ; ISA32-B R2 ↔ R3.4 : 77 %.
- ATON et ISA32-B R3.4 : même emplacement de copyright en `F000:E004`, même terminateur `@`
  (`ATON-SYSTEMES (C)1993@`).
- ROM vidéo ATON : 1265 octets différents de la ROM vidéo SMT, pointeurs décalés par
  blocs.
- ISA32-B R3.4 contient une entrée 80286 dans sa table des processeurs.
- La table des processeurs de l'ATON ajoute i386DX/SX/SL et i486DX/SX/DX2/DX4.
- Numérotation des ASIC SMT `L5Axxxx`, du G5 (`CPU286-MEM` `L5A0478`/`L5A0680`) au Golf 486
  (`L5A1110`, `L5A1111`, `L5A6027`, `L5A6373`).

## Goupil 60 — `bios/G60/`

386/486 sur bus EISA.

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `G60.V1R3.1_SMT.BIN` | 27C512 | CC00 ✓ | BIOS `GOUPIL G60 BIOS EISA V1R3.1` du 07/12/90, `SVGA V4`, version SMT. |
| `G60-R3_5.BIN` | 27C512 | 9F00 | BIOS `EISA-A R3.5` du 30/04/91, `SVGA V5 SMT-GOUPIL`. |
| `G60.EISA-AR3.6_ATON.BIN` | 27C512 | A200 ✓ | BIOS `EISA-A R3.6` du 26/12/91, version **ATON Systèmes**, repreneur de la marque. Aussi fourni sous le nom `G60_EISA_AR3_6.BIN`. |
| `G60.EISA-AR3.9_ATON.BIN` | 27C512 | A700 ✓ | BIOS `EISA-A R3.9` du 13/04/92, version ATON. Aussi fourni sous le nom `G60_EISA_AR3_9.BIN`. |

### Disquette de configuration — `bios/G60/disquette-ECU/`

Contenu de la disquette **ECU** (EISA Configuration Utility) du G60 : `ECU.EXE`,
`ECU.HLP`, les fichiers de configuration `.CFG` des cartes EISA (SMT, Adaptec, AST, DPT,
Mylex, Novell…), les configurations système `.SCI` et l'index `!!CFG.NDX`. Ce ne sont pas
des images de PROM.

## Goupil 100 — `bios/G100/`

Machine multipostes à base de 80386, sans rien de commun avec un PC hormis le processeur.
Elle n'a pas d'écran : chaque poste est un terminal série GT220, et la console est
branchée sur un SCC Z8530. Le reste passe par des cartes intelligentes :

- un processeur de contrôle (horloge, LED, bouton MAINT), qui répond en EBCDIC ;
- un processeur de stockage de masse, pour les disques ESDI et les disquettes ; il exécute
  des blocs de commande posés en mémoire ;
- des processeurs série (4, 16 ou 32 voies) et des cartes réseau Ethernet ou Starlan.

Les PROM vont par paires H/L ; elles contiennent le moniteur
**G100MON-386** (16 Ko, programme C 16 bits en mode réel, recopié en RAM en `10000h`). Il fait l'autotest,
l'inventaire des cartes, lit l'heure, puis charge l'IPL de la partition amorçable du disque
ESDI 0 (ou de la disquette, selon le bouton MAINT) en `80000h`. Il vérifie la signature
`IPLG100`, puis lui passe la main.

Écrans de la console des trois versions : voir les [captures](captures/README.md#g100).

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `G100_M311-J8-A.BIN` + `G100_M311-J10-A.BIN` | 2764 ×2 | BFE5 ✓ / D4C0 ✓ | G100 V1R1 25 MHz, `G100MON-386 V1R1`. `J10` porte les octets pairs, `J8` les impairs. |
| `G100_V1R2_H.H5.BIN` + `G100_V1R2_L.H7.BIN` | 2764 ×2 | 9933 ✓ / 832B ✓ | G100 V1R2 33 MHz, `G100MON-386 V1R2`. `L` porte les octets pairs. |
| `G100_V1R2.1_H.BIN` + `G100_V1R2.1_L.BIN` | 2764 ×2 | 9ECA / 89D8 | G100 V1R2.1, révision suivante, `G100MON-386 V1R2.1`. |

### Terminal GT220 — `bios/G100/Terminal-GT220/`

Terminal texte des postes du G100 : il affiche « GOUPIL GT220 » au démarrage. C'est un
terminal multi-émulations de classe VT220 : VT200 (7 ou 8 bits), VT100, VT52, ANSI.SYS,
PCTerm, Wyse 50/925, TeleVideo 910+/925/950, ADDS Regent 25 et Viewpoint, ADM 3A/5,
Hazeltine 1500. Il a deux sessions (Setup A et B, « Dual Host »), 80 ou 132 colonnes,
16 nationalités et 11 menus de configuration. Réglages d'usine : session A en VT200
7 bits et 80 colonnes, session B en TVI 925 et 132 colonnes, 9600 bauds 8N1.

Le constructeur n'est pas nommé. La ROM contient cependant `ESI "Easy" Tools` et une
émulation `6310-Reg25/VP` (le terminal ESP 6310 d'Esprit), et le Hazeltine 1500 ouvre la
liste des émulations : c'est très probablement un terminal **Esprit Systems** (repreneur
des terminaux Hazeltine), vendu sous la marque Goupil. Le modèle d'origine n'est pas
identifié. Le mot « SMT » n'apparaît dans aucune version : il vient des étiquettes.

Processeur de la famille 6502. Les PROM ne sont pas entrelacées : U39 occupe
`8000h`-`FFFFh`, dont la moitié basse est fixe. La fenêtre `C000h`-`FFFFh` est commutée par
les bits 3 et 4 du port `70h` entre la moitié haute d'U39 et les deux moitiés d'U33 (set-up
et police ; autotest et écrans). Chaque banque se termine par la version (`FFF0h`), un
octet qui arrondit la somme, puis les vecteurs (reset `9000h`). Matériel déduit du code :
CRTC de type 6845 en `00h`, VIA 6522 en `10h` (son timer déclenche un NMI par rangée de
caractères), DUART 2681 en `20h` (sessions A et B), ACIA 6850 du clavier en `40h`,
générateur de caractères en RAM, dans lequel le micrologiciel charge sa police (444
caractères 8×13 compressés, jeu VT220 et jeu PC) ; la double largeur est faite en
doublant chaque caractère en mémoire.

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `GT220_S1_SR_U33.BIN` + `GT220_S1_SR_U39.BIN` | 27C256 ×2 | 3300 / 9300 | Version `S1.5q` (le set-up affiche « Version S1.5r »). « SR » est vraisemblablement le « 5R » de l'étiquette mal lu. Clavier de type DEC (Set Up, Next Scrn). |
| `SMT_VS1_5N_U33_GT220.BIN` + `SMT_VS1_5N_U39_GT220.BIN` | 27C256 ×2 | A700 / E200 | Version `S1.5n`, révision antérieure de la même branche : mêmes textes, mêmes menus et même police, code réorganisé. |
| `SMT_VT1_5B_U33_GT220.BIN` + `SMT_VT1_5B_U39_GT220.BIN` | 27C256 ×2 | 0600 / 0700 | Version `T1.5b`, pour un clavier de type PC (SysReq, Pg Up/Pg Dn, PF1-PF4, « See Users Manual for actual keys ») ; 5 caractères de plus dans la police. |

Écran d'accueil, set-up, menus et police : voir les [captures](captures/README.md#terminal-gt220).

## Golf — `bios/Golf/`

### Golf XT — `bios/Golf/XT/`

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `PGM-GL.10G-A.BIN` + `PGM-GL.10J-A.BIN` | 27C256 ×2 | 8100 ✓ / 2300 ✓ | Golf XT V1 10 MHz, BIOS `GLMON-86 V1R3` + `PVGA V2`. Aussi fourni sous les noms `GLMON_86_10G_A.BIN` / `GLMON_86_10J_A.BIN`. Les PROM du Strator sont identiques. |

### Golf 286 — `bios/Golf/286/`

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `M2GL.9K-1.BIN` + `M2GL.9M-1.BIN` | 27C256 ×2 | 2600 / 2B00 | BIOS `GLMON-286 V1R5.3` + `PVGA V0`, la plus ancienne révision du dossier. |
| `MG.1.9K-C_V1R6B.BIN` + `MG.1.9M-C_V1R6B.BIN` | 27C256 ×2 | 2000 / B000 | BIOS `GLMON-286 V1R6B` du 11/01/90 + `PVGA V3`. |
| `MG.1.9K-D.BIN` + `MG.1.9M-D.BIN` | 27C256 ×2 | 3B00 ✓ / 8100 ✓ | Golf 286 10 à 12,5 MHz, BIOS `GLMON-286 V1R7` du 15/06/90 + `PVGA V4`. |

### Golf SX — `bios/Golf/SX/`

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `MGSX.1.11M-C.BIN` | 27C512 | 6500 | Golf SX, BIOS `GLMON-SX V1R6` du 07/11/89 + `PVGA V3`. PROM **MGSX.1.11M/C** (indice C), TMS 27C512 du support 11M : **dump relu sur un Golf SX réel**, identique. Indice antérieur à celui du tableau SMT, d'où l'absence de ✓. Aussi fourni sous le nom `GOLF_SX_V1R6_11M.BIN`. |
| `GOLF_SXC_V1R6_DGI.BIN` | 27C512 | 5500 | Golf SX**C**, BIOS V1R6 du 06/04/90, variante « DGI » (vraisemblablement une série client). |
| `MGSX.1.11M-E.BIN` | 27C512 | 5100 ✓ | Golf SX 16 à 20 MHz, BIOS `GLMON-SX V1R7` du 12/04/90 + `PVGA V4`. PROM **MGSX.1.11M/E** (indice E), celle du tableau SMT : **dump relu sur un Golf SX réel**, identique. Aussi fourni sous le nom `GOLF_SX_V1R7.BIN`. |
| `ISA32A-R2.BIN` | 27C512 | 0E00 ✓ | Carte ISA32-A pour **G5 et Golf SX**, BIOS `ISA32-A R2` du 05/03/91, `SVGA V5`, code article 40618055A. Aussi fourni sous les noms `ISA32_A_R2.BIN` et `Goupil_ISA32AR2.BIN`. Le dossier G5 en contenait aussi une copie. |
| `ISA32_A_R2_modif_disque170m.BIN` | 27C512 | 0E00 | ISA32-A R2 modifié pour un disque de 170 Mo : 8 octets changés dans la table des disques (`E4EBh`-`E57Fh`). La somme est conservée. |

Notation des PROM : celle de la colonne « Désignation / indice PROM » du tableau SMT. Pour `MGSX.1.11M/E`, « MGSX.1 » est la désignation de la PROM du Golf SX, « 11M » son support sur la carte et la lettre après la barre l'**indice**, qui avance à chaque révision du contenu. Le tableau ne donne que le dernier indice de chaque PROM : V1R7 = E pour le Golf SX (C = V1R6 ; l'indice D n'est pas retrouvé), V1R7 = D pour le Golf 286 (C = V1R6B). Dans les noms de fichiers, la barre devient un tiret.

L'ISA32-A R2 n'a pas de gestion de souris PS/2, contrairement à l'ISA32-B R2 : voir
[Souris PS/2 et InPort](#souris-ps2-et-inport).

### Golf 486

Le Golf 486 a sa propre carte mère, spécifique au Golf : ce n'est pas une carte ISA32-B de G6. Elle partage en revanche le même BIOS que les cartes mères ISA32-B : son BIOS d'origine est `bios/G6/ISA32B-R2.BIN`. Un dump fait sur la machine (`DUMP_GOLF486.BIN`) est identique octet pour octet à ce fichier, il n'est donc pas stocké une seconde fois.

Le dossier `bios/Golf/486/` contient une version **modifiée** de ce BIOS, faite pour un Golf 486
dont la pile de l'horloge (DS1287) est épuisée. Ce n'est pas une ROM SMT-Goupil d'origine.

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `ISA32-B_R2-batHS-autoconf-logo.BIN` | 27C512 | A400 | `ISA32-B R2` modifié, MD5 `c6c10472858473b0afd88c76c02f352d`. **Pile HS** : le BIOS n'efface plus la configuration quand la pile est morte. **Auto-configuration** : à chaque démarrage à froid, l'horloge et la configuration (A: 1,44 Mo, disque type 39, 640 + 15 360 Ko, français) sont réécrites, et la machine démarre seule. **Logo Goupil** en haut à droite pendant le démarrage ([capture](captures/Golf/486/ISA32-B_R2-batHS-autoconf-logo_demarrage.png)), au choix dans une nouvelle ligne du setup « Logo au démarrage » ; le logo par défaut revient après chaque coupure de courant. Le BIOS vidéo n'est pas modifié. Validée sur un Golf 486 réel le 17 novembre 2026. |

## Strator — `bios/Strator/`

Ordinateur point de vente Strator, construit sur la base du Golf XT.

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `GLMON-86.10G-A_STRATOR.BIN` + `GLMON-86.10J-A_STRATOR.BIN` | 27C256 ×2 | 8100 / 2300 | BIOS `GLMON-86 V1R3`, identique octet pour octet aux PROM du Golf XT (`PGM-GL.10G-A` / `10J-A`). |

## SFENA 5090 — `bios/SFENA-5090/`

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `SFENA_5090_U27_REV_B5D.BIN` + `SFENA_5090_U28_REV_B5D.BIN` | 27C256 ×2 | 7476 / A534 | Micrologiciel Z80 du **terminal vidéo SFENA 5090**, version `5090 Rev.B5--K`, « © SFENA 1987 ». Rangé avec les machines Goupil dans les fichiers d'origine, mais aucune mention de SMT ni de Goupil. Absent du tableau SMT. |

C'est un terminal compatible VT220 : il répond `ESC[?62;1;2;6;7;8;9c` à la demande
d'attributs, et propose aussi les émulations VT52, VT100, VT101, VT102, VT131 et Qume
QVT-203 Plus. Il a 80 ou 132 colonnes, une ligne d'état avec horloge (`01/01/00 01:00` par
défaut), un SET-UP de dix pages en anglais, français ou allemand, 14 claviers nationaux,
des touches programmables et une calculatrice. Rien ne le relie au GT220 du G100.

- U28 occupe `0000h`-`7FFFh` ; U27 est coupée en deux banques de 16 Ko qui apparaissent
  dans la fenêtre `8000h`-`BFFFh` (`OUT 53h` ou `OUT 5Bh`). RAM système en `C000h`-`DFFFh`
  (la configuration du SET-UP, avec sa somme, est en `DE8Fh`-`DFFFh`), mémoire d'écran en
  `E000h`-`FFFFh`.
- Contrôleur vidéo SCN2674 en `00h`-`07h`, SIO Z80 en `10h`-`13h` (voie A : hôte ;
  voie B : imprimante), attributs en `30h` (inverse, gras, souligné, clignotant, jeu
  graphique DEC). Le clavier est une liaison série lue bit à bit sur la broche SYNC de la
  SIO.
- La police n'est pas dans ces PROM : elle est dans un générateur de caractères séparé,
  absent du dépôt.

Autotest et SET-UP : voir les [captures](captures/README.md#sfena-5090).

## Accessoires — `bios/Accessoires/`

| Dossier / fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `Clavier-G4-G40/CLAVIER_G4_G40.BIN` | 2716 | 669A | Micrologiciel du clavier G4 et G40, pour un 8049 (MCS-48, 128 octets de RAM). Clavier XT de 84 touches (codes 01h à 54h du jeu 1, pas de F11/F12), trame XT de 9 bits, voyants gérés par le clavier, autotest qui envoie `AAh` (`CEh` en cas d'échec), somme de la ROM vérifiée au démarrage. Les fichiers `CLAVIER_G4.BIN` et `CLAVIER_G40.BIN` fournis sont identiques. Le BIOS du G4 lit bien cette trame XT ; celui du G40 programme un 8042 en mode AT, qui ne la lirait pas : quel clavier équipait le G40 reste à vérifier. |
| `Clavier-G5/CLAVIER_G5.BIN` | 2764 | E021 | Micrologiciel du clavier G5, pour un 8031 (MCS-51 ; 4 Ko utilisés sur 8). Clavier étendu (101 touches US plus les deux touches ISO), protocole XT ou AT choisi par cavalier : un seul clavier pour le G5 S86 (XT) et les G5 286/386 (AT). Commandes du clavier IBM étendu (identification `AB 83`), jeux de codes 1, 2 et 3. Pas de chaîne ni de numéro de version. |
| `Video-G4GA-V40GA/VGA_G4_4L-A.BIN` | 27128 | BDC0 | Carte vidéo graphique G4GA du G4, aussi utilisée sur le G40 sous la référence V40GA.1F-A (fichier identique). Les 8 premiers Ko sont la police de caractères. |
| `Video-G4GA-V40GA/VGA_G4_4L-A_VIDEO_G4GA.BIN` | 2764 | DDC0 | Première moitié de la PROM précédente, en dump séparé (police seule). |
| `EGA-Paradise-G40/G40_EGA.BIN` | 27128 | 7200 | BIOS de la carte EGA Paradise du G40 (`PEGA BIOS`, Paradise Systems 1986, réf. 28290-A1101). |
| `Disque-Xebec-G4/XEBEC_G4_10413BG.BIN` | 2732 | 8A32 | Micrologiciel du Z80 d'un contrôleur de disque dur Xebec (étiquette 10413BG), et non une ROM d'extension PC. Il présente au PC l'interface AT du WD1003 (registres en `1F0h`-`1F7h` : restore, lecture, écriture, vérification, formatage, seek, diagnostic, paramètres), celle qu'emploient les BIOS du G40 ; les BIOS du G4 attendent un contrôleur XT en `320h`. L'étiquette « G4 » est donc à vérifier. Pas de table de disques (elle est dans le BIOS système) ; 17 secteurs par piste (MFM). |
| `GoupilNet/goupilnet2-3JD.BIN` | 27C256 | 9700 | Micrologiciel de la carte réseau **GoupilNet 2** (3JD), version 2.00 : noyau temps réel (`SYSTEME`, `DRIVERS`, `cmd_task`) et piles X.25, SNA/QLLC, TNS et NetBIOS. Fourni deux fois (seul et dans un zip), même contenu. |
| `Carte-test-G16/G16TEST.BIN` | 27C256 | 2200 | ROM de la **carte de test G16** de SMT-Goupil : `G16TEST #1.0` du 03/09/90. C'est une ROM d'extension de 32 Ko (en-tête `55 AA`), lancée par le BIOS pendant le POST. Elle contient une suite de tests de production en anglais avec trois modes : *Go/Nogo*, *Assembly* et *Burning* (déverminage). Elle se configure selon le nombre de disquettes et de disques durs, propose des chaînes de tests programmables et tient des compteurs de tests et d'erreurs. Carte TEST80-V2 qui la porte, lecture des 8 LED du port 80h, codes du POST et bips des BIOS reconnus par G16 TEST : [docs/carte-test-g16.md](docs/carte-test-g16.md). |

**GoupilNet (1985-1987)**, d'après les notes GoupilNet DOS 3.1 : Microsoft Networks (MS-NET)
adapté par SMT pour MS-DOS 3.1 (« Microsoft Networks SERVER Version 1.0 », © Microsoft - SMT
Goupil 1985) : REDIR, XPORT, SESSION, FSERVER. Cartes « transporteur » pour Goupil 3-PC et
G4, adresse 00h-3Fh par micro-interrupteurs, recopiée dans le fichier `NETADDR` (adresse sur
20 octets, l'octet suivant réservé à une passerelle télécommunications), bus deux fils avec
résistance de fin de ligne. Sans rapport avec la carte GoupilNet 2 ci-dessus (X.25, SNA,
NetBIOS). La couche physique n'est décrite dans aucun des documents.

## Utilitaires — `bios/Utilitaires/`

| Fichier | EPROM | C.R.C | Description |
|---|---|---|---|
| `RAZ-PWR6/RAZ-PWR6.BIN` | 27C512 | A61A ✓ | **PROM de réinitialisation du mot de passe** pour Goupil G5, G6 et Golf SX (« PROM REINIT PASSWORD »). C'est un BIOS SX complet (`PVGA V3`) qui efface le mot de passe stocké en CMOS : on l'installe le temps d'un démarrage, puis on remet la PROM d'origine. Aussi fourni sous le nom `EPROM_REINIT_PWR6 GOLF_SX_GOUPIL.BIN`. |
| `P10AHFF/P10AHFF.COM` | — (programme DOS, 47 octets) | 15CA | Utilitaire du **Goupil Club**, du 30/01/1987, à charger dans l'`AUTOEXEC.BAT` avant Norton Commander. Programme résident qui intercepte l'`INT 10h` et rend la main sans rien faire quand AH = `FFh`. Sur le Club, cette fonction du BIOS bascule l'affichage entre l'écran LCD interne et l'écran externe (prise DB9). Norton Commander l'appelle et fait perdre l'affichage interne ; `P10AHFF` l'en empêche. Voir le [Club](#g4mon-88-v2r1--ce-qui-change-par-rapport-à-la-v1r1). |

---

## Licence et contenu tiers

Les images de PROM contiennent des micrologiciels de SMT-Goupil, ATON Systèmes,
Normerel, Paradise Systems, Xebec et SFENA, dont les droits ne sont pas détenus ici. Elles
sont conservées à des fins de préservation et de réparation de machines possédées. Les
descriptions et les captures d'écran de ce dépôt sont originales.
