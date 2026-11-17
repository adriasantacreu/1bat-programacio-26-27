---
title: "Funcions amb condicions a Google Sheets"
subtitle: "Apunts · 1r de batxillerat · Programació i robòtica · Institut Escola Sant Pol de Mar"
geometry: margin=2cm
mainfont: TeX Gyre Heros
monofont: DejaVu Sans Mono
fontsize: 10.5pt
---

Aquests apunts acompanyen la presentació de la sessió 9. Tots els exemples fan servir el full de la **lliga de robots** (pestanya `Jornada1`, files 2 a 49):

| Columna | Dada | Columna | Dada |
|---|---|---|---|
| A | `id` | J | `temps_volta_s` |
| D | `jutge` | K | `voltatge_V` |
| E | `equip` | L | `pes_g` |
| F | `robot` | M | `cost_eur` |
| G | `categoria` | N | `estat` (OK, DNF, DSQ) |

# 1. Com es llegeix una pàgina de documentació

Totes les pàgines d'ajuda de Google Sheets tenen la mateixa estructura:

1. **Descripció**: una frase sobre què fa la funció.
2. **Exemple d'ús**: una fórmula d'exemple.
3. **Sintaxi**: el nom de la funció i els seus arguments, en ordre. És la part més important.
4. **Notes**: els detalls i els paranys. Aquí és on s'expliquen els errors.
5. **Exemples**: una petita taula amb fórmules i resultats.

Signes de la sintaxi:

| Signe | Vol dir | Exemple |
|---|---|---|
| `[ ]` | argument **opcional** | `IF(condició, si_cert, [si_fals])` |
| `...` | pots **repetir** l'últim grup | `[interval_criteri2, criteri2, ...]` |
| `;` a la documentació | separador de la configuració catalana | al nostre full (EUA) escrivim `,` |

**Tres consells:**

- Llegeix la sintaxi **argument per argument** i pregunta't: «a la meva taula, quina columna és aquesta?».
- Llegeix les **Notes**. Allà hi ha per què la fórmula et dona 0 o un error.
- **Prova-ho al full.** La documentació també s'equivoca (a la pàgina de `MINIFS` hi ha dos exemples amb la mateixa fórmula i resultats diferents, i la traducció pot ser automàtica). Si la pàgina en català és confusa, canvia `hl=ca` per `hl=en` a l'adreça.

Llista de totes les funcions: <https://support.google.com/docs/table/25273?hl=ca>

# 2. Una fitxa de model: MINIFS

**Documentació:** <https://support.google.com/docs/answer/7014063?hl=ca>

**Què fa:** troba el valor més petit d'una columna, però només entre les files que compleixen una o més condicions.

**Sintaxi:**

```
MINIFS(interval, interval_criteri1, criteri1, [interval_criteri2, criteri2, ...])
```

| Argument | Obligatori? | Què és |
|---|---|---|
| `interval` | Sí | Les cel·les d'on es treu el mínim |
| `interval_criteri1` | Sí | Les cel·les on es mira la primera condició |
| `criteri1` | Sí | La condició (un valor, una cel·la o un text com `">7"`) |
| `interval_criteri2, criteri2` | No | Més parelles de condició |

**El meu exemple amb la lliga:**

```
=MINIFS(J2:J49, G2:G49, "Laberint", N2:N49, "OK")
```

Resultat: `60.71`, el millor temps de volta de Laberint entre les curses acabades.

**Compte!**

- Si cap fila no compleix les condicions, torna **0** (no un error). Un 0 pot semblar un resultat correcte.
- Tots els intervals han de tenir **la mateixa mida**. Si no, `#VALUE!`.

# 3. Com s'escriuen els criteris

| Criteri | Vol dir |
|---|---|
| `"OK"` | igual a OK |
| `G2` | igual al que hi ha a la cel·la G2 |
| `"<>DSQ"` | diferent de DSQ |
| `">1000"` | més gran que 1000 |
| `"<="&B1` | més petit o igual que el valor de B1 |

Els operadors (`>`, `<`, `<>`, `>=`, `<=`) van **entre cometes**. Si el valor és en una cel·la, s'enganxa amb `&`.

# 4. La família IFS

Totes segueixen el mateix patró: **d'on surt el resultat** i després **parelles de condició**.

| Funció | Què fa | Sintaxi | Documentació |
|---|---|---|---|
| COUNTIFS | compta files | `COUNTIFS(interval_criteri1, criteri1, ...)` | [3256550](https://support.google.com/docs/answer/3256550?hl=ca) |
| SUMIFS | suma | `SUMIFS(interval_suma, interval_criteri1, criteri1, ...)` | [3238496](https://support.google.com/docs/answer/3238496?hl=ca) |
| AVERAGEIFS | mitjana | `AVERAGEIFS(interval_mitjana, interval_criteri1, criteri1, ...)` | [3256534](https://support.google.com/docs/answer/3256534?hl=ca) |
| MINIFS | mínim | `MINIFS(interval, interval_criteri1, criteri1, ...)` | [7014063](https://support.google.com/docs/answer/7014063?hl=ca) |
| MAXIFS | màxim | `MAXIFS(interval, interval_criteri1, criteri1, ...)` | [7013817](https://support.google.com/docs/answer/7013817?hl=ca) |

`COUNTIFS` és l'única que no té interval de resultat: només compta.

**Compte amb SUMIF i SUMIFS.** A `SUMIF(interval, criteri, [interval_suma])` la suma va **al final**. A `SUMIFS(interval_suma, ...)` va **al principi**.

Exemples amb la lliga:

| Pregunta | Fórmula | Resultat |
|---|---|---|
| Curses de Laberint acabades | `=COUNTIFS(G2:G49,"Laberint",N2:N49,"OK")` | 11 |
| DNF que ha posat la jutgessa Marta | `=COUNTIFS(D2:D49,"Marta",N2:N49,"DNF")` | 4 |
| Robots de més de 1000 g | `=COUNTIF(L2:L49,">1000")` | 8 |
| Cost total de Velocitat | `=SUMIFS(M2:M49,G2:G49,"Velocitat")` | 905.16 |
| Temps mitjà de Velocitat (OK) | `=AVERAGEIFS(J2:J49,G2:G49,"Velocitat",N2:N49,"OK")` | 9.69 |
| Pitjor temps de Laberint (OK) | `=MAXIFS(J2:J49,G2:G49,"Laberint",N2:N49,"OK")` | 117.54 |

# 5. Decidir: IF i IFS

**IF** ([documentació](https://support.google.com/docs/answer/3093364?hl=ca)): una condició i dos resultats.

```
IF(expressió_lògica, valor_si_és_cert, [valor_si_és_fals])
=IF(L2>1000, "PESAT", "OK")
```

**IFS** ([documentació](https://support.google.com/docs/answer/7014145?hl=ca)): moltes condicions. Torna el valor de la **primera** que és certa.

```
IFS(condició1, valor1, [condició2, valor2, ...])
=IFS(N2="DSQ", 0, N2="DNF", 1, TRUE, 3)
```

- Si **cap** condició és certa, torna `#N/A`. Per evitar-ho, l'última condició és `TRUE` («en qualsevol altre cas»).
- L'**ordre** importa: posa primer els casos especials.

# 6. Buscar: XLOOKUP

**XLOOKUP** ([documentació](https://support.google.com/docs/answer/12405947?hl=ca)): busca un valor en una columna i torna el que hi ha a la mateixa fila d'una altra columna.

```
XLOOKUP(clau_cerca, interval_cerca, interval_resultat, [valor_no_trobat], ...)
=XLOOKUP("J1-017", A2:A49, E2:E49, "No existeix")     → Cable Suelto
```

- `interval_cerca` i `interval_resultat` han de tenir la mateixa mida.
- Sense `valor_no_trobat`, si no es troba res surt `#N/A`.
- Per buscar en una altra pestanya: `Categories!A2:A4`.

# 7. Format condicional amb fórmula

La fórmula d'una regla ha de tornar **TRUE o FALSE**. Sheets l'aplica a la primera fila del rang i la va movent cap avall.

| Vull pintar… | Fórmula |
|---|---|
| les files DNF | `=$N2="DNF"` |
| el millor temps de cada categoria | `=$J2=MINIFS($J$2:$J$49,$G$2:$G$49,$G2)` |
| l'equip amb més punts (taula d'equips) | `=$D2=MAX($D$2:$D$11)` |

- `$N2`: la columna fixa (sempre mirem l'estat) i la fila mòbil (cada fila mira la seva).
- `$J$2:$J$49`: tot fix, perquè el rang de comparació és sempre el mateix.
- `=MINIFS(...)` sol **no funciona**: torna un número, i qualsevol número diferent de 0 compta com a TRUE. Es pinta tot.

# 8. Errors que vam veure a la Missió 1

| Error | Per què falla | Correcte |
|---|---|---|
| `=SUM(H2:I2)` per a la durada | suma dues hores | `=I2-H2` |
| `=J2` amb format `mm:ss` | 23.04 es llegeix com 23 dies | `=J2/86400` |
| `=MINIFS(...)` en una regla | no és TRUE/FALSE | `=$J2=MINIFS(...)` |
| `"Seguidor de línia"` escrit a mà | una regla per categoria, i un error de lletra la trenca | `$G2` |
| Markdown en un Google Doc | no és text pla | fitxer `.md` al GitHub |

# 9. Practica (les solucions són a sota)

1. Quantes curses ha acabat (OK) la segona ronda?
2. Quant ha costat en total el material de les curses que ha jutjat en Jordi?
3. Quin és el millor temps de volta de tota la jornada a la categoria Velocitat?
4. Quins robots tenen un voltatge per sota de 7 V? (`COUNTIF` per comptar-los i un filtre per veure'ls)
5. Amb `XLOOKUP`: quin robot ha fet la cursa `J1-017`?
6. Escriu una fórmula amb `IFS` que posi «Lent», «Normal» o «Ràpid» a una cursa de Velocitat segons si el temps és més gran que 11, entre 8 i 11, o més petit que 8.

\newpage

**Solucions**

1. `=COUNTIFS(C2:C49,2,N2:N49,"OK")` → **19**
2. `=SUMIFS(M2:M49,D2:D49,"Jordi")` → **693.89 €**
3. `=MINIFS(J2:J49,G2:G49,"Velocitat")` → **6.16** (J1-017)
4. `=COUNTIF(K2:K49,"<7")` → **1** (la cursa J1-010, 6.90 V)
5. `=XLOOKUP("J1-017",A2:A49,F2:F49)` → **Sprint-3**
6. `=IFS(J2>11,"Lent",J2>=8,"Normal",TRUE,"Ràpid")`. L'ordre fa la feina: si arriba a la segona condició, ja sabem que no és més gran que 11.
