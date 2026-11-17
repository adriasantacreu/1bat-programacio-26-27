# Lliga de robots · Missió 1: la jornada 1 ha arribat en brut

---

**De:** Comitè Tècnic · Lliga de Robots del Maresme  
**Per a:** Equip d'analistes de dades  
**Assumpte:** Resultats de la jornada 1 — processament i normalització de dades  

Bon dia, equip,

El sistema de cronometratge de la pista ha exportat els resultats de la **jornada 1** (18/09) en un fitxer de text pla: `lliga_robots_jornada1.csv`. Hi ha 48 curses de tres categories (*Seguidor de línia*, *Laberint* i *Velocitat*), dues rondes i tres jutges.

Actualment el conjunt de dades no és operable: els temps estan expressats en segons bruts, les hores de registre no indiquen la durada de cada prova i no és possible identificar de manera immediata els abandonaments (DNF), les desqualificacions (DSQ) ni els millors registres per categoria.

Cal lliurar un full de càlcul **normalitzat, estructurat i clarament interpretable** abans de publicar la classificació oficial. Disposeu del fitxer adjunt per començar el tractament.

Atentament,

*Comitè Tècnic · Lliga de Robots del Maresme*

---

## Fases de treball

Realitzeu les tasques seguint l'ordre establert. Els apartats marcats amb ⭐ corresponen a requeriments avançats.

### M0 · Inspecció de l'arxiu en origen (abans d'obrir Sheets)
Obriu el fitxer `.csv` amb un **editor de text pla** (com ara el Bloc de notes o l'editor de text del sistema), sense utilitzar cap programari de fulls de càlcul.
- Quin caràcter s'utilitza com a delimitador de columnes?
- Per quin motiu alguns camps de text estan delimitats **entre cometes** i d'altres no?
- Quantes files de registres conté el fitxer (sense comptar la capçalera)?

### M1 · Importació i configuració regional
1. Creeu un full nou: **Fitxer › Importa › Puja**.
2. Tipus de separador: selecció automàtica o personalitzada segons correspongui. Activeu l'opció *Converteix el text en números, dates i fórmules*.
3. **Fitxer › Configuració › Configuració regional: Estats Units**. Justifiqueu per quin motiu aquesta configuració és necessària *abans* de realitzar la importació del fitxer.

### M2 · Estructura i navegació pel conjunt de dades
- **Immobilitzeu** la fila 1 (*Visualitza › Congela › 1 fila*) per mantenir visible la capçalera durant el desplaçament.
- Ajusteu l'amplada de totes les columnes de manera automàtica segons el contingut (seleccioneu-les totes i feu doble clic al límit de qualsevol columna).
- Apliqueu format a la capçalera: text en negreta, color de fons corporatiu suau i alineació centrada.
- **Navegació per teclat (eficiència operativa):**
  - Seleccioneu tot el conjunt de dades mitjançant **`Ctrl + A`** per aplicar una tipografia homogènia.
  - Desplaceu-vos ràpidament al darrer registre amb **`Ctrl + ↓`** i torneu a l'inici amb **`Ctrl + ↑`** (o `Ctrl + Inici`), evitant l'ús del ratolí per a grans volums de dades.
  - Per modificar el contingut d'una cel·la sense sobreescriure'l íntegrament, utilitzeu la tecla **`F2`**.

### M3 · Tipificació i format de dades
Apliqueu el format corresponent a cadascuna de les columnes següents per garantir-ne la llegibilitat:

| Columna | Format requerit | Exemple de visualització |
|---|---|---|
| `data` | Data completa | `Friday, September 18, 2026` |
| `hora_inici`, `hora_fi` | Hora estàndard | `09:02:32` |
| `temps_volta_s` | Numèric (2 decimals, alineat a la dreta) | `23.04` |
| `voltatge_V` | Numèric personalitzat amb unitats | `8.33 V` |
| `pes_g` | Numèric personalitzat amb unitats | `633 g` |
| `cost_eur` | Moneda en euros (format personalitzat) | `18.58 €` |

*Indicació:* les cadenes de text i unitats fixes en formats numèrics personalitzats han d'anar delimitades entre cometes dobles (per exemple: `0.00" V"`).

### M4 · Càlcul de durades i representació temporal interna
Afegiu dues noves columnes al final de la taula (columnes P i Q):
1. **Columna P · `durada_cursa`**: càlcul del temps transcorregut entre `hora_fi` i `hora_inici`. Format de cel·la: **Durada**.
2. **Columna Q · `temps_volta`**: conversió del valor de `temps_volta_s` al format `mm:ss.00`.  
   *Fonament teòric:* els fulls de càlcul emmagatzemen les unitats de temps com a fracció decimal d'un dia sencer (24 hores = 86.400 segons). Apliqueu la conversió corresponent.
3. Al final de la taula, calculeu el temps total acumulat de la jornada sumant els valors de la columna P.
4. **Anàlisi de format:** en una cel·la independent, comproveu l'operació `=TIME(23,0,0) + TIME(6,0,0)`. Quina diferència s'observa si s'aplica el format *Hora* respecte al format *Durada*? Quina és la causa d'aquest comportament?
5. **Comprovació del valor numèric subjacent:** introduïu el valor `12:00:00` en una cel·la i elimineu el format aplicat mitjançant **`Ctrl + \`** (o apliqueu format *Número*). Quin valor decimal s'obté i quin significat físic té?

⭐ **Requeriment avançat:** implementeu el càlcul de la columna Q mitjançant una única funció matricial **`ARRAYFORMULA`** ubicada a la cel·la Q2 (recordeu que l'interval inferior ha d'estar completament buit).

### M5 · Format condicional mitjançant criteris predefinits
- Columna `temps_volta_s`: apliqueu una **escala de colors** (gradació de verd per als valors menors a vermell per als majors).  
  → *Qüestió d'anàlisi:* És coherent comparar directament els temps de categories tècnicament diferents com *Laberint* i *Velocitat*? Com es podria millorar aquesta segmentació?
- Columna `pes_g`: ressalteu en color taronja els registres amb valor **superior a 1200 g** (infracció del límit tècnic de la categoria).
- Columna `voltatge_V`: ressalteu en color vermell els valors **inferiors a 7 V** (nivell crític de bateria).  
*Optimització:* utilitzeu l'opció **"Afegeix una altra regla"** per agilitzar la creació de condicions múltiples sobre els mateixos intervals.

### M6 · Format condicional basat en fórmules (ressaltat de files completes)
Seleccioneu tot el rang de dades (de la fila 2 a la 49, columnes A a Q) i configureu les regles següents mitjançant l'opció *La fórmula personalitzada és*:
1. Fila completa en **fons vermell suau** si l'estat és abandonament (`DNF`). Fórmula: `=$N2="DNF"`
2. Fila completa en **text gris i ratllat** si l'estat és desqualificació (`DSQ`). Fórmula: `=$N2="DSQ"`
3. ⭐ Fila completa en **fons verd clar i text en negreta** si correspon al **millor temps de la seva categoria**.  
   *Pista de recerca:* consulteu la documentació oficial sobre la funció `MINIFS`.

*Important:* justifiqueu la necessitat d'utilitzar la referència mixta (`$N2`), amb bloqueig exclusiu de la columna.

### ⭐ M7 · Integració de dades auxiliars en TSV generades amb IA
Per completar el model de dades, cal incorporar la taula de categories de competició que no estava inclosa en l'exportació inicial:
1. Sol·liciteu a un model d'intel·ligència artificial (com ara Gemini o ChatGPT) el següent requeriment:  
   > *"Genera una taula de 4 columnes (Categoria, Pes màxim en g, Voltatge límit en V, Descripció tècnica) per a les categories Seguidor de línia, Laberint i Velocitat en format TSV (Tab-Separated Values) dins d'un bloc de codi per a Google Sheets."*
2. Creeu una pestanya nova al document amb el nom **`Categories`**.
3. Situeu el cursor a la cel·la **`A1`** i enganxeu el contingut directament amb **`Ctrl + V`**.
4. Observeu com el delimitador de tabulació distribueix automàticament els camps en columnes independents sense necessitat d'un procés d'importació de fitxers.

### M8 · Optimització visual i dreceres de treball
- **Control de visualització:** desactiveu l'opció **Línies de quadrícula** a través del menú **Visualitza › Mostra** per avaluar la presentació del document amb els estils aplicats.
- Premeu la combinació **`Ctrl + /`** per accedir a la guia de tecles de drecera del full de càlcul. Identifiqueu i anoteu **dues combinacions noves** rellevants per a l'edició o navegació eficient.

---

## Criteris de lliurament

A la tasca corresponent de Google Classroom, cal aportar:
1. L'**enllaç d'accés** al document de Google Sheets (verificant que disposi de permisos de lectura per al professor).
2. Resposta a la qüestió d'anàlisi: **quina ha estat la dificultat tècnica o comportament inesperat més rellevant durant el processament i com s'ha resolt?**
3. **Format del text:** la resposta s'ha de presentar estructurada en sintaxi **Markdown** (emprant com a mínim un encapçalament de nivell `#` i termes destacats en **negreta**).
