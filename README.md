# Daniel Runner PWA

Aplicació web progressiva (PWA) de seguiment nutricional i d'entrenament per a corredors. Dissenyada per a ús personal, configurable per a qualsevol usuari.

---

## Instal·lació

1. Obre el fitxer `index.html` en un servidor local (p. ex. `python3 -m http.server 8743`)
2. Accedeix des del mòbil o escriptori via `http://localhost:8743`
3. En iPhone/iPad: Compartir → "Afegir a la pantalla d'inici" per instal·lar com a app nativa
4. En Android: el banner d'instal·lació apareix automàticament

Totes les dades es desen localment al navegador (`localStorage`). No requereix connexió a internet un cop instal·lada.

---

## Pestanyes i funcionalitats

### 🏠 Inici (Dashboard)

Resum diari complet:

- **Salutació i data** del dia actual
- **Gamificació**: nivell (Principiant → Campió), XP totals, barra de progrés i assoliments (badges)
- **Compte enrere** per a les properes curses
- **Tipus de dia**: normal, trampa, diumenge, càrrega CH o dia de cursa
- **Impacte de l'entrenament**: si hi ha entrenament al dia, mostra les kcal extra i els macros ajustats
- **Macros d'avui**: proteïnes, carbohidrats, greixos i kcal totals (amb barres visuals)
- **Entrenament d'avui**: llista de les sessions registrades per avui
- **Estat corporal**: últim pes, IMC, greix corporal, massa muscular

**D'on surten les dades:**
- Macros: calculats a partir del perfil (BMR/TDEE via fórmula Mifflin-St Jeor) i del tipus de dia
- Pes/composició: de l'últim registre a la secció Stats
- XP: sessions d'entrenament + registres + assoliments desblocats

---

### 🥗 Dieta

Pla nutricional setmanal complet:

- **Selector de dia**: dilluns a diumenge, amb indicador del tipus de dia
- **Macros del dia**: kcal totals, proteïnes, CH, greixos (escalen automàticament)
- **Pla d'àpats**: 5-6 àpats diaris amb hora, nom, descripció i kcal
- **Impacte entrenament**: si hi ha entrenament, les kcal s'ajusten sumant l'extra cremat

**D'on surten les dades:**
- El pla base (`MEAL_PLAN`) té 7 plantilles de menú (L-D) amb kcal de referència ~2200 kcal/dia
- `scaleFactor = kcal_objectiu_del_perfil / kcal_base_plantilla` → totes les kcal dels àpats s'escalen proporcionalment
- L'objectiu de kcal ve del perfil: `TDEE ± dèficit/superàvit` o manual
- Tipus de dia: normal (×1.0), diumenge (×0.87), trampa (×1.18), càrrega CH (×1.27), cursa (×1.45)

---

### 📅 Agenda (Calendari)

- Calendari mensual navegable (‹ ›)
- Codificació per colors: cursa (vermell), càrrega CH (morat), trampa (taronja), descans (blau), avui (verd)
- Llista de curses planificades (integrades i personalitzades)
- Historial d'entrenaments al calendari
- Botó **"Afegir nova cursa"**: permet registrar noves curses amb nom, data, km, lloc i color

**Curses preconfigrades:**
- Tast de la Mitja (10 km) — 31/10/2026, La Garriga
- Mitja Marató Granollers (21.1 km) — 24/01/2027

**Lògica de dies:**
- El dia de la cursa = `cursa`
- Els 3 dies anteriors = `carrega` (càrrega de carbohidrats)
- Dissabte = `trampa`
- Diumenge = `diumenge`
- La resta = `normal`

---

### 💪 Entrena

Registre setmanal d'entrenament:

- **Navegació setmanal** (‹ ›): veure setmanes anteriors conservant tot l'historial. La setmana comença el dilluns.
- **Afegir sessió**: peses, boxeo, córrer o caminar
  - **Peses**: durada + grups musculars treballats
  - **Boxeo**: sessió estàndard 1h (~600 kcal)
  - **Córrer**: km, kcal cremades, velocitat km/h
  - **Caminar**: temps (min) + distància (km) → kcal calculades automàticament per MET
- **Filtres**: tot, peses, boxeo, córrer, caminar
- **Gràfiques**: sessions per setmana, kcal cremades, km acumulats (córrer)

**Càlcul MET caminar:**
`kcal = MET × pes(kg) × temps(h)`
- < 3 km/h → MET 2.5
- 3-4 km/h → MET 3.0
- 4-5 km/h → MET 3.5
- 5-6 km/h → MET 4.5
- > 6 km/h → MET 5.0

**Dades desades a:** `dr_trainings` (localStorage)

---

### 📊 Stats

Seguiment de la composició corporal:

- **Nova mesura**: pes, IMC (calculat automàticament), % greix, % aigua, % proteïna, greix visceral, massa muscular, massa òssia, metabolisme basal, edat corporal, nota
- **Historial**: taula amb totes les mesures, amb opció d'eliminar
- **Gràfiques**: evolució de pes, % greix, greix visceral, composició corporal, IMC

**IMC** calculat com: `pes(kg) / (alçada(m))²`

**Dades desades a:** `dr_registres` (localStorage)

---

### 🍎 Aliments

Base de dades nutricional amb 70+ aliments:

- **Categories**: Proteïnes, Llegums, Lactis, Cereals, Verdures, Fruites, Greixos, Tubercles, Suplementació, Altres
- **Per cada aliment**: kcal/100g, proteïnes, carbohidrats, greixos
- **Indicador de salut**:
  - 🟢 **Sa**: verdures, fruites, proteïnes magres, llegums, cereals integrals, greixos saludables
  - 🟡 **Moderat**: lactis, ous, arròs blanc, pasta normal, fruits secs processats
  - 🔴 **Limitar**: pa blanc, sucre, mel, xocolata amb llet, alcohol
- **Preferits** (⭐): marca aliments que t'agraden. S'usen per personalitzar suggeriments futurs
- **Exclosos** (🚫): marca aliments que no vols menjar. S'exclouen dels plans de dieta suggerits
- **Cerca** per nom
- **Filtres** per categoria, preferits o exclosos

**Dades nutricionals:** Fonts de referència USDA FoodData Central, taules BDA i Nutri-Score europeu

**Dades desades a:** `dr_food_prefs` → `{ preferred: [...], excluded: [...] }` (localStorage)

---

### 🛒 Compra

Llista de la compra setmanal generada automàticament:

- **Articles organitzats per categories**: Proteïnes, Lactis, Cereals, Verdures, Fruites, Greixos, Tubercles, Suplementació, Altres
- **Quantitats setmanals**: cada article indica la quantitat necessària per cobrir els 7 dies de pla
- **Notes**: explica per a quins àpats s'utilitza cada ingredient
- **Checkbox**: marca el que ja tens o el que has comprat
- **Progrés**: barra que indica quants articles has completat
- **"Desmarcar tot"**: reinicia tota la llista (nova setmana de compra)

**D'on surten les quantitats:**
- Basades en les plantilles del `MEAL_PLAN` setmanal (7 dies)
- Les quantitats estan calculades per a 1 persona amb el pla nutricional estàndard de l'app

**Dades desades a:** `dr_shopping_checks` (localStorage)

---

### 👤 Perfil

Configuració personal completa:

#### Dades personals
- Nom, sexe, pes (kg), alçada (cm), edat
- Nivell d'activitat: sedentari, lleuger, moderat, actiu, molt actiu
- Objectiu: perdre pes, mantenir, guanyar massa

#### Kcal diàries
- **Mode Auto (BMR/TDEE)**: calcula automàticament
  - **BMR** (metabolisme basal): fórmula Mifflin-St Jeor
    - Home: `10×pes + 6.25×alçada − 5×edat + 5`
    - Dona: `10×pes + 6.25×alçada − 5×edat − 161`
  - **TDEE** = BMR × multiplicador d'activitat (1.2 sedentari → 1.9 molt actiu)
  - **Kcal objectiu** = TDEE − dèficit (perdre pes) / TDEE (mantenir) / TDEE + superàvit (guanyar)
  - Banner: mostra quantes kcal es retallen del TDEE i el % de dèficit
- **Mode Manual**: introdueixes directament les kcal diàries objectiu
- Pes objectiu (kg)

#### RESUM CALÒRIC (targeta automàtica)
- **BMR**: metabolisme basal calculat
- **TDEE**: kcal totals amb activitat
- **Dia normal / Càrrega CH / Dia cursa**: kcal per cada tipus de dia

#### Suplementació
14 suplements comuns amb descripció, dosi recomanada i millor moment de presa:

| Suplement | Beneficis principals | Dosi |
|-----------|---------------------|------|
| ☀️ Vitamina D3 | Ossos, immunitat | 1000–4000 UI/dia |
| 🍊 Vitamina C | Antioxidant, col·lagen | 500–1000 mg/dia |
| 🧠 Vitamina B12 | Energia, sistema nerviós | 500–1000 µg/dia |
| 💊 Complex B | Metabolisme energètic | 1 càpsula/dia |
| 🪨 Magnesi | Múscul, son, calambres | 300–400 mg/dia |
| 🐟 Omega-3 EPA/DHA | Inflamació, cor, cervell | 1–3 g/dia |
| 🦴 Col·lagen | Articulacions, tendons | 10–15 g/dia |
| 🔬 Zinc | Immunitat, testosterona | 15–30 mg/dia |
| 🩸 Ferro | Hemoglobina, energia | 14–18 mg/dia |
| 🦷 Calci | Ossos, contracció muscular | 1000–1200 mg/dia |
| ⚡ Creatina | Força, massa muscular | 3–5 g/dia |
| 🥛 Proteïna Whey | Recuperació muscular | 20–40 g/presa |
| 💪 BCAA | Anti-catabolisme | 5–10 g/dia |
| 🌙 Melatonina | Qualitat del son | 0.5–3 mg/dia |

Toca cada suplement per activar/desactivar. L'estat mostra els actius amb el primer benefici i el moment recomanat.

**Dades desades a:** `dr_profile`, `dr_supplements` (localStorage)

---

## Gamificació

### Nivells (per XP)
| XP | Nivell |
|----|--------|
| 0–199 | 🌱 Principiant |
| 200–499 | 🚶 Iniciació |
| 500–999 | 🏃 Runner |
| 1000–1999 | ⚡ Atleta |
| 2000+ | 🏆 Campió |

### Com guanyar XP
- +10 XP per cada mesura corporal registrada
- +10 XP per cada km corregut
- +60 XP per sessió de boxeo
- +40 XP per sessió de peses
- +30 XP per sessió de caminar
- +50 XP per cada assoliment desbloquejat

### Assoliments (badges)
`1r registre`, `1r entreno`, `5 entrenos`, `10 entrenos`, `25 entrenos`, `Boxejador`, `Forja el cos`, `1r running`, `5 km!`, `10 km!`, `Mitja marató`, `-1 kg`, `-5 kg`, `Objectiu 88kg`, `100 km!`

---

## Dades i privacitat

Totes les dades es desen **exclusivament al dispositiu** via `localStorage` del navegador. No es transmeten a cap servidor extern.

### Claus localStorage
| Clau | Contingut |
|------|-----------|
| `dr_profile` | Dades personals i configuració |
| `dr_registres` | Historial de mesures corporals |
| `dr_trainings` | Historial d'entrenaments |
| `dr_races` | Curses personalitzades |
| `dr_achievements` | Assoliments desblocats |
| `dr_food_prefs` | Aliments preferits i exclosos |
| `dr_supplements` | Suplements actius |
| `dr_shopping_checks` | Estat de la llista de la compra |

---

## Estructura tècnica

- **Tecnologia**: HTML5 + CSS3 + JavaScript vanilla (sense frameworks)
- **Un sol fitxer**: `index.html` (autocontingut)
- **PWA**: Service Worker integrat per funcionar offline
- **Charts**: Chart.js 4.4.1 via CDN
- **Service Worker cache**: `daniel-runner-v4`
- **Repositori**: `https://github.com/dmorenoar/daniel-runner.git`
- **Publicació automàtica**: cada canvi es puja a GitHub automàticament via hook de Claude Code

---

## Historial de versions

| Versió | Descripció |
|--------|-----------|
| v1 | App inicial: dashboard, dieta, calendari, entrenament, stats |
| v2 | Gamificació, assoliments, compte enrere curses |
| v3 | Sistema de perfil, càlcul BMR/TDEE, macros dinàmics, caminar MET, vista setmanal entrenament, dèficit calòric |
| v4 | Base de dades d'aliments (70+ aliments) amb indicadors de salut, preferits/exclosos, suplementació en perfil, llista de la compra setmanal, publicació automàtica GitHub |
