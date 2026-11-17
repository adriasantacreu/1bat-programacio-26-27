---
title: "Lliga de robots · Missió 2: la classificació"
subtitle: "1r de batxillerat · Programació i robòtica · Institut Escola Sant Pol de Mar"
geometry: margin=2cm
mainfont: TeX Gyre Heros
monofont: DejaVu Sans Mono
fontsize: 10.5pt
---

**De:** Comitè Tècnic · Lliga de Robots del Maresme\
**Per a:** Equip d'analistes de dades\
**Assumpte:** Classificació oficial de la jornada 1

Bon dia, equip,

Gràcies per la feina de la Missió 1. Les dades ja són llegibles, però encara no sabem **qui ha guanyat**. Necessitem la classificació oficial de la jornada 1: punts per cursa, resums per categoria i per equip, i un cercador perquè els jutges puguin consultar qualsevol cursa.

A més, ens han arribat les **normes oficials de pes**. Alguns robots les podrien haver incomplert. Ho heu de comprovar.

I una cosa més. Aquesta vegada no us donarem les fórmules. **Les haureu de treure de la documentació oficial de Google.** Un bon analista no memoritza funcions: les busca, les llegeix i les prova.

*Comitè Tècnic · Lliga de Robots del Maresme*

---

Treballeu **al mateix full de la Missió 1**. Els apartats amb ⭐ són extres.

## Part 0 · Fitxes de funció (al vostre GitHub)

Al vostre repositori de GitHub, creeu el fitxer **`funcions.md`** (copieu-hi la plantilla `plantilla_funcions.md`). Feu-hi una fitxa per a cada funció:

| Obligatòries | ⭐ Extres |
|---|---|
| `COUNTIFS`, `SUMIFS`, `IFS`, `XLOOKUP` | `AVERAGEIFS`, `MAXIFS`, `UNIQUE`, `SORT` |

Cada fitxa té: l'enllaç a la documentació oficial, què fa (amb les vostres paraules), la sintaxi, una taula amb els arguments (quins són obligatoris i quins opcionals), l'exemple de la documentació, **un exemple vostre amb les dades de la lliga** (amb el resultat que us dona) i un **«Compte!»** tret de l'apartat *Notes* de la pàgina.

Documentació (llista de totes les funcions): <https://support.google.com/docs/table/25273?hl=ca>

| Funció | Pàgina |
|---|---|
| COUNTIFS | <https://support.google.com/docs/answer/3256550?hl=ca> |
| SUMIFS | <https://support.google.com/docs/answer/3238496?hl=ca> |
| IFS | <https://support.google.com/docs/answer/7014145?hl=ca> |
| XLOOKUP | <https://support.google.com/docs/answer/12405947?hl=ca> |
| AVERAGEIFS ⭐ | <https://support.google.com/docs/answer/3256534?hl=ca> |
| MAXIFS ⭐ | <https://support.google.com/docs/answer/7013817?hl=ca> |
| UNIQUE ⭐ | <https://support.google.com/docs/answer/10522653?hl=ca> |
| SORT ⭐ | <https://support.google.com/docs/answer/3093150?hl=ca> |

Al final del fitxer, responeu aquestes preguntes. **Les respostes són a la documentació**:

1. Què passa amb `SUMIFS` si l'interval de suma i l'interval del criteri **no tenen la mateixa mida**?
2. Què torna `IFS` si **cap** de les condicions és certa? Com ho podeu evitar?
3. Per a què serveix el **quart argument** de `XLOOKUP`? Què passa si no el poseu i no es troba res?
4. A `SUMIF` l'interval de suma va al final. I a `SUMIFS`? Escriviu les dues sintaxis una sota l'altra.

*Atenció:* la documentació en català escriu `;` entre arguments. El nostre full està configurat amb **EUA** i fa servir `,`.

## M1 · Preparació

1. Canvieu el nom de la pestanya de dades a **`Jornada1`**.
2. **Netegeu** *Format › Format condicional*: esborreu les regles repetides o de prova. Ha de quedar net.
3. Esborreu el contingut de la pestanya `Categories` (la taula que us va fer la IA) i enganxeu-hi la **taula oficial** (`categories_oficials.tsv`, a Classroom) a la cel·la `A1`:

| Categoria | Pes màxim (g) | Voltatge mínim (V) |
|---|---|---|
| Seguidor de línia | 1000 | 7.0 |
| Laberint | 1200 | 7.0 |
| Velocitat | 900 | 7.4 |

Si teníeu alguna cosa a les columnes R, S o T de `Jornada1`, moveu-la a una altra banda.

## M2 · Punts i control de pes (pestanya `Jornada1`)

**Columna R · `punts`.** Cada cursa dona punts segons aquest reglament. S'aplica **la primera norma que es compleix**:

| Norma | Punts |
|---|---|
| L'estat és `DSQ` | 0 |
| L'estat és `DNF` | 1 |
| És el millor temps de la seva categoria | 10 |
| El temps és igual o més petit que la mitjana de la seva categoria (curses OK) | 6 |
| Qualsevol altra cursa acabada | 3 |

Una sola fórmula a `R2` que es pugui arrossegar fins a `R49`. *Pista:* `IFS`, `MINIFS` i `AVERAGEIFS`. La categoria **no** es pot escriure a mà.

*Comprovació:* la cursa `J1-029` ha de donar 10 punts. En total, a la columna R hi ha d'haver **tres** curses de 10 punts.

**Columna S · `pes_max`.** El pes màxim de la categoria de cada cursa, buscat a la pestanya `Categories` amb `XLOOKUP`.

**Columna T · `control_pes`.** Escriu `EXCÉS` si el pes del robot supera el màxim i `OK` si no. Amb `IF`. Afegiu una regla de format condicional que pinti de taronja les cel·les `EXCÉS`.

## M3 · La classificació (pestanya nova `Classificació`)

**Taula 1 · Per categoria.** Una fila per categoria i aquestes columnes:

| Categoria | Curses | Acabades (OK) | DNF | % abandonament | Millor temps | Pitjor temps | Temps mitjà | Cost total |
|---|---|---|---|---|---|---|---|---|

Els temps, només de curses OK. Escriviu les fórmules de la primera fila referenciant la cel·la de la categoria (no el text) i arrossegueu-les.

*Comprovació:* a la fila de Laberint us ha de sortir 16 curses, 11 acabades i 25 % d'abandonament.

**Taula 2 · Per equip.** Una fila per equip (n'hi ha 10) i aquestes columnes:

| Equip | Curses | Acabades (OK) | Punts | Cost total |
|---|---|---|---|---|

1. La llista d'equips es pot escriure a mà. ⭐ O es pot generar sola amb `=SORT(UNIQUE(...))`.
2. Ordeneu la taula de més punts a menys (*Dades › Ordena l'interval*).
3. Format condicional: la fila de l'equip amb **més punts** en verd i negreta (la fórmula ha de tornar TRUE o FALSE).

## M4 · El cercador dels jutges

A la pestanya `Classificació`, en un lloc visible, feu un cercador: a una cel·la s'escriu l'id d'una cursa (per exemple `J1-023`) i a sota surten l'**equip**, el **robot**, la **categoria**, el **temps**, l'**estat** i els **punts**. Amb `XLOOKUP`. Si l'id no existeix, ha de sortir `No existeix` (no un error).

⭐ Feu que l'id es triï d'un **desplegable** (*Dades › Validació de dades*, amb l'interval dels id).

## ⭐ M5 · El Comitè sanciona

El Comitè decideix que **una cursa amb excés de pes val 0 punts**.

1. Columna U · `punts_finals` a `Jornada1`.
2. Una tercera taula amb la classificació per equips amb els punts finals.
3. Responeu a l'apartat de lliurament: **ha canviat el guanyador?** Si hi ha empat, proposeu un criteri de desempat raonat (i calculeu-lo).

## Lliurament

A la tasca de Classroom:

1. L'**enllaç al full** (amb permís de lectura per al professor).
2. L'**enllaç al vostre `funcions.md`** a GitHub.
3. Una resposta curta en **Markdown** (un `#` com a mínim i alguna **negreta**): **quina funció us ha costat més d'entendre llegint la documentació i què vau provar al full per entendre-la?**

**Data límit: dilluns 19 d'octubre, 23:59.**

Abans de lliurar, reviseu-ho:

- [ ] Les fórmules s'han arrossegat i no hi ha cap categoria escrita a mà.
- [ ] Cap cel·la amb error (`#N/A`, `#VALUE!`…).
- [ ] *Format condicional* net, sense regles de prova.
- [ ] El `funcions.md` és un fitxer `.md` de veritat al vostre GitHub (no un Google Doc).
