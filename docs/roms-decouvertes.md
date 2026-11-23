# ROM SMT-Goupil — fonctions cachées et comparaison des BIOS

Relevés faits sur toutes les ROM PC du dépôt (G4, G40, G5, G50, G6, G60, Golf, Strator,
G286E, RAZ-PWR6 : 55 images, paires paire/impaire entrelacées) et sur la notice et les schémas
de la carte mère du G5 (non inclus dans le dépôt).

## INT 15h : réglage de la vitesse du processeur

Fonction non documentée, présente dans tous les BIOS ISA SMT (G6 V1R1.4 `937E`, ISA32-A R2
`940B`, ISA32-B R2 `9430`, ISA32-B R3.4 `889F`), absente de l'ATON.

```
9430: cmp ah,0E0h ; jb ...      INT 15h AH=E0h : régler la vitesse
9435: cmp ah,0E1h ; ja erreur   INT 15h AH=E1h : lire la vitesse
```

- `AH=E0h`, `AL=0` haute, `1` basse, `2` auto. `AL>2` → `AH=86h`, CF=1.
- `AH=E1h` → `AL=0` haute, `1` basse, `2` auto. Retour CF=0, AH=0.

Registre système Goupil, port `84h` : bit 0 accès débogueur, **bit 3 vitesse basse**,
**bit 4 mode auto**.

Test sous DEBUG : `A 100` / `MOV AH,E1` / `INT 15` / `INT 3` / `G=100` → AL.

## Vitesse « Auto »

Dans l'interruption horloge (INT 08h, ISA32-B R2 `9EC0`-`9EF5`) : si le mode auto est actif
(port `84h` bit 4) et qu'un moteur de disquette tourne (`40:3F` bits 0-3), le BIOS passe en
vitesse basse (bit 3 à 1). Quand le délai d'arrêt moteur coupe le lecteur (écriture en
`3F2h`), il repasse en vitesse haute. L'aide du setup ATON, qui reprend la fonction, indique :
« AUTO is used with MS/DOS to support floppy based software protection keys ».

## Souris : InPort et PS/2

- **G5** (schéma folio 22/29) : puce **MSMC** en 17L, contrôleur de souris Microsoft InPort
  (entrées XA/XB/YA/YB/SW1-3), sortie souris quadrature sur **P10**, interruption IT3 (IRQ3,
  cavalier E1), décodage d'adresse par PAL16L8A 13L avec cavaliers E27/E28 « ACCESS ».
- Aucun BIOS n'accède aux ports InPort (`23Ch`-`23Fh`).
- Code et messages souris PS/2 (`<Mouse clock line error>`, `Souris standard reconnue`)
  présents dans G6 V1R1.4, G60 V1R3.1, ISA16-B R3.4, ISA32-B R2/R2.2/R3.4 et ATON ; absents
  de tous les G5, G50, Golf XT/286/SX et de **ISA32-A R2**.
- ISA32-A R2 et ISA32-B R2 : même date, ROM vidéo identique, 92 % de code commun. B R2
  contient en plus environ 900 octets de gestion PS/2 (`98D7`-`9C5D`).

## G5 : connecteur de lecteur de carte à mémoire

Notice (p. 45, 50) : **P16 « Connexion d'un lecteur de carte à mémoire »**, bord avant de
la carte mère entre P6 (pile) et P13 (diode de façade) ; **E21** présent = lecteur non
installé (standard), **E22** présent = installé.

Schéma folio 23/29 (port série) : P16 (10 broches, 1 et 2 à la masse) est câblé sur le
**même UART NS16450 13A que COM1**. Deux récepteurs RS-232 75154 : 14A pour le connecteur
arrière P3, 16A pour P16. Les cavaliers **E21 à E24** choisissent lequel des deux alimente les
entrées de l'UART (lignes RxD/CTS/DSR/DCD). Les sorties passent par les 75150 15A/15B.

Aucun des 13 BIOS G5 ne contient de code ni de message pour ce lecteur.

Autres cavaliers du G5 : E10 lecteur de disquette bi-vitesse / mono-vitesse ; **E7, E8, E11
cavaliers de test en usine** ; E18, E19, E20, E30, E31 obligatoirement présents.

## Lecteur de bande

Aucune des 55 images ne contient de code ni de texte de lecteur de bande (`tape`, `streamer`,
`QIC`, `bande`).

## Dates de compilation de la ROM vidéo

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
différents), un emplacement de copyright en `E004` et une aide du débogueur remaniée. Le
tableau SMT le marque « PROTO ».

## Étiquette en `FFE8`

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

## BIOS proches (comparaison octet à octet)

| Paire | Écart | Ce qui diffère |
|---|---|---|
| G5 S86 V2 `G5MON-86 V2R3` ↔ Golf XT `GLMON-86 V1R3` (= Strator) | **6 octets** | nom, version, sommes, et une constante d'initialisation disquette (`8A79` : `mov al,2` sur G5, `mov al,4` sur Golf) |
| G5 286 `V3R7` ↔ G5 286 `V4R7` | **2 octets** | le chiffre de version et la somme |
| G5 286 `V4R7` ↔ Golf 286 `GLMON-286 V1R7` | **4 octets** | nom, version, somme, et le délai d'attente de l'interruption disquette (`A204` : `mov al,0Ah` sur G5, `12h` sur Golf) |
| G6 SX / SX AC / DX / 486 | identiques | une seule image V1R1.4 |
| Golf SX `GLMON-SX V1R6` ↔ RAZ-PWR6 | 88 % identiques | |
| Golf SXC « DGI » ↔ G5 SX | — | le BIOS du Golf SXC s'annonce `G5MON-SXC V1R6B`, celui du G5 SX `G5MON-SXC V1R7` |
| G5 SX V1R7 ↔ Golf SX V1R7 | 85 % identiques | même date 12/04/90 |
| G5 386DX V1R7 ↔ G50 V2R7 | 81 % identiques | même date 15/06/90 |
| G5 286 V3R6 ↔ G5 386 V2R6 | 85 % identiques | même date 07/11/89 |
| ISA32-B R3.4 ↔ EISA-A R3.5 (G60) | 66 % identiques | |

Entre un Golf et le G5 de même génération, un seul octet fonctionnel diffère, dans la gestion
du lecteur de disquette.

## BIOS de même date

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
printemps 1990). À partir de 1991, les BIOS portent le nom d'un bus (**ISA16**, **ISA32**,
**EISA**) et non plus d'une machine.

## G6, ISA32 et ATON

- ISA32-B R2 ↔ G6 V1R1.4 : 69 % identiques ; ISA32-B R2 ↔ R3.4 : 77 %.
- ATON et ISA32-B R3.4 : même emplacement de copyright en `F000:E004`, même terminateur `@`
  (`ATON-SYSTEMES (C)1993@`).
- ROM vidéo ATON : 1265 octets différents de la ROM vidéo SMT, pointeurs décalés par blocs.
- ISA32-B R3.4 contient une entrée 80286 dans sa table des processeurs.
- La table des processeurs de l'ATON ajoute i386DX/SX/SL et i486DX/SX/DX2/DX4.
- Numérotation des ASIC SMT `L5Axxxx`, du G5 (`CPU286-MEM` `L5A0478`/`L5A0680`) au Golf 486
  (`L5A1110`, `L5A1111`, `L5A6027`, `L5A6373`).

## Chronologie

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

## Recoupements avec la documentation SMT

Sources : Goupil Revue n° 1 à 7 (mars 1982 – 1984), NIG n° 5, 13, 14, 29, 30, BIT n° 19-27,
35-38, 106, 200-212, notes « Informations techniques diverses » (G40, G3/G4/G40), notes
diverses, notice mémoire G5 (05/1988), manuel du moniteur couleur multimode (1986), notes
GoupilNet DOS 3.1 (1985). Compilations retronik.silicium.org (Bjorn Darkaad, 2024).

### Vérifié dans les ROM

| Fait documenté | Vérification dans le dépôt |
|---|---|
| BIT 210 (28/08/1990) : avec les BIOS **VxR6 et VxR6B**, le débogueur intégré validé fait perdre 1 Ko de mémoire (CHKDSK) ; corrigé en VxR7 | `sub word [40:13h],1` puis segment du Ko réservé rangé en `40:06h`, présent dans **toutes** les R6/R6B (G5 286 V3R6, G5 386 V2R6, G50 V2R6 et V2R6B, Golf 286 V1R6B, Golf SX V1R6, Golf SXC V1R6B, RAZ-PWR6) et dans **aucune** R5 ni R7. La routine suivante teste le port `84h` bit 0 (accès débogueur). |
| Goupil Revue n° 2 (1982) : la version du moniteur G2 se lit en `FD04h` (`13` = 16×64, `14` = 24×80) | MON2 (`F800h`-`FFFFh`) : en `FD03h`, `BITA #$14` — instruction neutre qui sert à loger l'octet de version à adresse fixe. Bannière `GOUPIL 1.4 8/81` en `F980h` : moniteur 24×80 V1.4 d'août 1981. |
| Note G40 : table des disques durs retenus pour le G40 | Types 1 à 14 du BIOS `G4MON-286 V1R3` conformes (306/4 NEC 5124, 615/4 ST225-NEC 5126, 977/5 ST4051, 615/8 NEC 5146, 733/5 ST4038, 823/10 NEC 5452, 1024/9 ST4096…). `V2R5` remplace les types 8, 9, 14 et ajoute un type 16 : 9, 14 et 16 sont à 34-35 secteurs/piste (RLL/ESDI). |
| BIT 23 (09/1984) : moniteurs G3 — 6809 `UC09.2 3D-B` V2R2, 8088 `RAPR.1 6B-C/5B-C` V1R2, Z80 `Z80.2 1B-C` V2.3, UNIBUG V2.3 | 8088 V1R2 = les deux RAPR du dépôt (exact). 6809 du dépôt = V2R0 indice A, antérieur à V2R2. Z80 du dépôt = V2.8, postérieur à V2.3. Le moniteur 8088 est sur la carte RAM/PROM, pas sur la carte UC (straps S13 à ajouter lors de la mise à niveau). |
| Goupil Revue n° 7 : fonction moniteur 6809 `ACCMEM` = `SWI` + `FCB $0A` (A = page physique haute, B = page 6809) | Cohérent avec le gestionnaire de pages 4 Ko et l'exécutif à `SWI n` lus dans `G3MON-6809 V2R0`. Entrées-sorties en `FF000h`-`FF7FFh` ; une fois mappées en `F000h`, ACIA 1 en `F000h`-`F001h` (comme dans la ROM). |

### Appris des documents

- **Golf / G5 et contrôleur disquette** : NIG 5 — cavalier E30 du Golf 286 selon le
  contrôleur WD37C65 (sans suffixe ou A/B).
- **Disques Conner CP3022 / CP342** (IDE à contrôleur intégré) : cavalier dédié sur Golf 286,
  G5 286 V4 et G5 386 SX (BIT 201-203, 03/1990), avec une 3e position « contrôleur externe ».
- **Vidéo PVGA intégrée et SVGA 800×600** : G5 286 V4, Golf 286 et G5 386 SX ont un
  oscillateur 36 MHz (+ 25,175 et 28,322 MHz) et un cavalier qui le valide « pour le mode SVGA ».
  Sur le G5-286 VGA, les interrupteurs EGA de la façade (près de la prise souris) ont disparu :
  tout passe par `MODE`.
- **Lecteur de carte à mémoire** : cavaliers E21-E24 aussi sur G5 286 rév. D et G5 386 rév. B,
  « les quatre doivent avoir la même position ».
- **128 Ko cachés du G5 286 rév. D** : avec SW8 (façade) sur OFF et E16 retiré, 768 Ko
  conventionnels ; les 128 Ko au-delà de 640 Ko sont en `D0000h`-`EFFFFh`, **non reconnus par
  le BIOS**, à initialiser par logiciel avant lecture (sinon erreur de parité).
- **G5 S86** : ses cartes mémoire de 1 Mo sont en mémoire **EMS** (pilote `EMS.SYS`), alors que
  G5 286/386 sont en mémoire étendue.
- **G40** : coprocesseur 80287 → PAL `C40.3FB` à la place de `C40.3FA`, test `T287` (BIT 106).
- **G4 + carte EGA** : V1R4 retirer E23 ; V2R2 retirer E21 et SW1 contacts 5 et 6 sur ON.
- **G50 rév. B** : cavalier E3 EPROM 27512/27256, E28 zone `C0000h`-`CFFFFh` cachable.
- **OS/2 1.0 et 1.1 adaptés par SMT** (NIG 14, 06/1989) : FDISK maison, 4 partitions, jusqu'à
  128 Mo, démarrage sur n'importe quelle partition ; PROM EGA reconnue à toute adresse.
- **3614 code SMTT** : NIG et BIT consultables sur Minitel.
- **Goupil 3** (Revue n° 4, 7 et notes) : sortie le 1er janvier 1983 ; cartes 6809, Z80 et
  8088 cohabitant à deux, sélectionnées par commutateur ; la carte 8088 n'a pas de RAM (carte
  256 Ko obligatoire) ; bus compatible **G64** ; espace d'adresses 1 Mo par pages de 4 Ko ;
  MS-DOS **2.06** (puis 2.11), jusqu'à 6 lecteurs A-F ; la configuration de base peut utiliser
  un **Minitel** comme clavier-écran ; carte synthèse vocale Alcatel, IEEE-488, Vidéotex.
- **Goupil 2** : 16, 48 ou 64 Ko (DRAM 4116) ; PROM moniteur rouges (16×64), vertes (24×80) ou
  bleues (disque dur) ; G2FLEX2 remonté de 8 Ko pour préparer le passage au 6809 (FLEX9) ;
  Winchester 5" de 5 Mo sur WD1000.
- **GoupilNet (1985-1987)** : Microsoft Networks (MS-NET) adapté par SMT pour MS-DOS 3.1
  (« Microsoft Networks SERVER Version 1.0 », © Microsoft - SMT Goupil 1985) : REDIR, XPORT,
  SESSION, FSERVER. Cartes « transporteur » pour Goupil 3-PC et G4, adresse 00h-3Fh par
  micro-interrupteurs, recopiée dans le fichier `NETADDR` (adresse sur 20 octets, l'octet suivant
  réservé à une passerelle télécommunications), bus deux fils avec résistance de fin de ligne.
  Sans rapport avec la carte GoupilNet 2 du dépôt (X.25, SNA, NetBIOS). La couche physique n'est
  décrite dans aucun des documents.
- **Moniteur couleur multimode** (1986) : 13", tube 370HYB22, 15,5-35 kHz automatique, CGA/EGA/PGA
  et analogique, pour G4 et G40. Fabricant non indiqué.
