---
title: "Format condicional a Google Sheets: de 0 a expert"
subtitle: "Apunts · 1r de batxillerat · Programació i robòtica · Institut Escola Sant Pol de Mar"
geometry: margin=2cm
mainfont: TeX Gyre Heros
monofont: DejaVu Sans Mono
fontsize: 10.5pt
---

Tots els exemples fan servir el full de la **lliga de robots** (pestanya `Jornada1`, files 2 a 49). Cada regla d'aquests apunts està aplicada en una pestanya del **full d'exemples** (feu-ne una còpia per tocar-lo): <https://docs.google.com/spreadsheets/d/12Z0XZrBNTvUwtACbMC19umO8Q5CfQjQRVpNyXWAAJJQ/edit>

| Columna | Dada | Columna | Dada |
|---|---|---|---|
| A | `id` | L | `pes_g` |
| D | `jutge` | M | `cost_eur` |
| E | `equip` | N | `estat` (OK, DNF, DSQ) |
| G | `categoria` | O | `observacions` |
| J | `temps_volta_s` | S | `pes_max` (Missió 2) |
| K | `voltatge_V` | | |

# Nivell 0 · Què és i on és

Una regla de format condicional canvia l'aspecte d'una cel·la (color de fons, color del text, negreta…) **només si es compleix una condició**. Si les dades canvien, el color canvia sol.

**On:** selecciona les cel·les i obre *Format › Format condicional*. S'obre un panell a la dreta.

Cada regla té tres parts:

| Part | Al panell | Exemple |
|---|---|---|
| **On** s'aplica | *Aplica a l'interval* | `N2:N49` |
| **Quan** s'aplica | *Regles de format* (*Formata les cel·les si…*) | el text és exactament `DNF` |
| **Com** queda | *Estil de format* | fons vermell clar |

Per veure totes les regles del full: clica en una cel·la qualsevol sense res seleccionat i obre el panell. Per esborrar-ne una: passa-hi el ratolí per sobre i clica la paperera.

# Nivell 1 · Regles fetes

El desplegable *Formata les cel·les si…* ja porta les condicions més habituals:

| Condició | Exemple amb la lliga | Interval | Valor |
|---|---|---|---|
| El text és exactament | les curses DNF | `N2:N49` | `DNF` |
| El text conté | observacions que parlen de la bateria | `O2:O49` | `Bateria` |
| Més gran que | robots de més de 1000 g | `L2:L49` | `1000` |
| Està entre | pes entre 900 i 1000 g | `L2:L49` | `900` i `1000` |
| La cel·la està buida | observacions que falten | `O2:O49` | (res) |

Resultats que t'han de sortir: 9 DNF, 8 observacions amb «bateria», 8 robots de més de 1000 g, 8 robots entre 900 i 1000 g, 12 observacions buides.

**Diverses regles al mateix interval.** Fes una regla per a `DNF` (vermell) i una altra per a `DSQ` (gris) al mateix interval `N2:N49`. Al panell, *Afegeix una altra regla*.

**L'ordre importa.** Si dues regles es compleixen a la mateixa cel·la, mana **la de més amunt** de la llista. Les regles es poden arrossegar per canviar-ne l'ordre.

# Nivell 2 · Escala de colors

A la pestanya *Escala de colors* del panell (en lloc de *Color únic*), cada cel·la agafa un color segons el seu valor: el més petit d'un color, el més gran d'un altre, i els del mig fan la transició.

Exemple: interval `M2:M49` (cost), mínim en blanc i màxim en vermell. D'un cop d'ull veus quines curses han sortit cares.

**Compte:** l'escala compara **tot l'interval**. Si ho fas amb `J2:J49` (temps de volta), els temps de Laberint (60–117 s) es menjaran els de Velocitat (6–12 s) i tot Velocitat sortirà igual. Una escala només té sentit si els valors són comparables.

# Nivell 3 · Fórmula personalitzada

Les regles fetes només miren **la mateixa cel·la**. Quan la condició depèn d'una altra columna o d'un càlcul, cal *La fórmula personalitzada és*.

**Les tres normes:**

1. La fórmula comença amb `=` i ha de tornar **TRUE o FALSE**.
2. S'escriu pensant en la **primera cel·la** de l'interval (la de dalt a l'esquerra). Sheets la copia a la resta com si l'arrossegués.
3. Els `$` decideixen què es mou i què no quan la fórmula es copia.

## Els dòlars

| S'escriu | Quan baixa una fila | Quan va una columna a la dreta | Ús típic |
|---|---|---|---|
| `N2` | `N3` | `O2` | gairebé mai en format condicional |
| `$N2` | `$N3` | `$N2` | **mirar sempre la mateixa columna**, cada fila la seva |
| `N$2` | `N$2` | `O$2` | mirar sempre la mateixa fila |
| `$N$2` | `$N$2` | `$N$2` | un valor fix (una cel·la de control, un rang de comparació) |

## Pintar la fila sencera

Interval: `A2:T49` (tota la taula, sense la capçalera).
Fórmula: `=$N2="DNF"`

A la fila 2, totes les cel·les miren `$N2`. A la fila 3, totes miren `$N3`. Com que la `N` porta `$`, la columna no es mou i tota la fila es pinta igual.

Si t'oblides el `$` (`=N2="DNF"`), la cel·la `B2` mirarà `O2`, la `C2` mirarà `P2`… i el resultat surt desplaçat.

## Comparar amb una altra columna

Excés de pes (amb la columna `S` de la Missió 2). Interval `L2:L49`:

```
=$L2>$S2
```

Han de sortir 11 cel·les pintades.

## Diverses condicions: AND i OR

Curses de Velocitat acabades en menys de 8 s (interval `A2:T49`):

```
=AND($G2="Velocitat", $N2="OK", $J2<8)
```

Curses que no han acabat, siguin DNF o DSQ:

```
=OR($N2="DNF", $N2="DSQ")
```

Han de sortir 2 files (J1-017 i J1-045) a la primera i 11 a la segona.

# Nivell 4 · Fórmules amb funcions

Aquí és on entren les funcions de la sessió 9. L'esquema és sempre el mateix: **el valor de la fila** comparat amb **un càlcul sobre tota la columna**.

```
=$J2 = MINIFS($J$2:$J$49, ...)
```

A l'esquerra, **el valor de la fila** (`$J2`). A la dreta, **un càlcul sobre tota la columna** (rangs fixos, amb `$` a tot arreu).

**El millor temps de cada categoria** · interval `A2:T49`

```
=$J2=MINIFS($J$2:$J$49,$G$2:$G$49,$G2,$N$2:$N$49,"OK")
```

**Els temps per sobre de la mitjana de la seva categoria** · interval `J2:J49`

```
=AND($N2="OK",
     $J2>AVERAGEIFS($J$2:$J$49,$G$2:$G$49,$G2,$N$2:$N$49,"OK"))
```

**Les 3 curses més cares** · interval `M2:M49`

```
=$M2>=LARGE($M$2:$M$49,3)
```

**Els id repetits** · interval `A2:A49`

```
=COUNTIF($A$2:$A$49,$A2)>1
```

**Una fila sí i una no** · interval `A2:T49`

```
=ISEVEN(ROW())
```

**La cursa que s'ha escrit al cercador** (`Classificació!B1`) · interval `A2:T49`

```
=$A2=INDIRECT("Classificació!B1")
```

Comprovacions: tres files de millor temps (J1-029, J1-035 i J1-017, totes de l'equip Cable Suelto); 18 temps per sobre de la mitjana; cap id repetit.

Fixa't en el `$G2` de dins del `MINIFS`: **sense `$` a la fila**, perquè cada fila ha de buscar el mínim de **la seva** categoria. La resta de rangs porten `$` a tot arreu.

## Referències a una altra pestanya

En una fórmula de format condicional, **no** es pot escriure directament `Categories!B2`. Cal fer-ho amb `INDIRECT` i el nom entre cometes:

```
=$L2>XLOOKUP($G2, INDIRECT("Categories!A2:A4"), INDIRECT("Categories!B2:B4"))
```

Fa el mateix que `=$L2>$S2`, però sense necessitar la columna `S`.

# Nivell 5 · Expert: depurar i mantenir

**1. Prova la fórmula en una columna auxiliar.** Abans de posar-la en una regla, escriu-la a una columna buida (per exemple `V2`) i arrossega-la. Si veus `TRUE` i `FALSE` on toca, copia-la a la regla. Després esborra la columna.

**2. Si es pinta tot o no es pinta res**, revisa en aquest ordre:

| Símptoma | Causa probable |
|---|---|
| Es pinta tot | la fórmula torna un número i no TRUE/FALSE (`=MINIFS(...)` sol) |
| Es pinta tot o res | la fórmula està escrita per a la fila 1 però l'interval comença a la 2 (o al revés) |
| Surt desplaçat | falta el `$` davant de la columna |
| Només funciona a la primera fila | hi ha `$` davant del número de fila (`$N$2`) |
| No surt res | text mal escrit: `"Seguidor de línea"` no és `"Seguidor de línia"`. Millor `$G2` que escriure el text |
| Error en desar la regla | referència a una altra pestanya sense `INDIRECT` |

**3. Una regla, un interval.** Si copies i enganxes cel·les, les regles es copien amb elles i es multipliquen. Revisa el panell de tant en tant i fusiona o esborra les repetides.

**4. Colors amb sentit.** Pocs colors i sempre amb el mateix significat: vermell = problema, verd = bé, gris = no compta. Si tot està pintat, no destaca res.

**5. La regla no canvia les dades.** El color no es pot filtrar ni comptar amb fórmules. Si necessites comptar el que has pintat, fes servir la mateixa condició amb `COUNTIFS`.

# Practica

1. Pinta en blau les curses que ha jutjat la Núria (fila sencera).
2. Pinta en taronja el voltatge dels robots per sota de 7 V.
3. Pinta de gris les files DSQ i de vermell les DNF, i fes que el text de les DSQ surti ratllat.
4. Pinta en verd la fila de la cursa més barata de cada categoria.
5. A la taula d'equips de la Missió 2 (equips a `A2:A11`, punts a `D2:D11`), pinta en verd i negreta la fila de l'equip amb més punts.
6. Pinta les curses que han fet els robots de l'equip que s'ha escrit a la cel·la `Classificació!B3`.

\newpage

**Solucions**

1. Interval `A2:T49` · `=$D2="Núria"` → 15 files.
2. Interval `K2:K49` · regla feta *Més petit que* `7` (o `=$K2<7`) → 1 cel·la (J1-010).
3. Interval `A2:T49` · dues regles: `=$N2="DSQ"` (gris, ratllat) i `=$N2="DNF"` (vermell) → 2 i 9 files.
4. Interval `A2:T49` · `=$M2=MINIFS($M$2:$M$49,$G$2:$G$49,$G2)` → 3 files.
5. Interval `A2:E11` · `=$D2=MAX($D$2:$D$11)`.
6. Interval `A2:T49` · `=$E2=INDIRECT("Classificació!B3")`.
