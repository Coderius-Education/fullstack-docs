---
sidebar_position: 5
---

# HTML in apart bestand

Tot nu toe hebben we HTML als strings in onze Python code geschreven. Dit werkt, maar wordt al snel onoverzichtelijk. In deze les leren we hoe we **HTML bestanden** kunnen serveren vanuit een aparte folder.

## Waarom HTML in aparte bestanden?

Voordelen:
- ✅ Overzichtelijker - HTML en Python code gescheiden
- ✅ Makkelijker te onderhouden - wijzigingen in één bestand
- ✅ Betere syntax highlighting in je editor
- ✅ Makkelijker samenwerken - frontend en backend apart

## Mappenstructuur aanmaken

Maak een `static` folder met een `pages` subfolder in je project:

```
je-project/
├── main.py
└── static/
    ├── pages/
    │   ├── home.html
    │   └── about.html
    └── css/
        └── style.css
```

## Static files configureren

Zorg dat je dit in je `main.py` hebt staan (als je dit nog niet hebt):

```python
from fastapi import FastAPI
from fastapi.responses import FileResponse
from fastapi.staticfiles import StaticFiles

app = FastAPI()

# Mount de static folder
app.mount("/static", StaticFiles(directory="static"), name="static")
```

## Je eerste HTML bestand serveren

### Stap 1: HTML bestand maken

Maak een bestand `static/pages/home.html`:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Home</title>
        <link rel="stylesheet" href="/static/css/style.css">
    </head>
    <body>
        <div class="container">
            <h1>Welkom op mijn website!</h1>
            <p>Dit is een HTML pagina die wordt geserveerd vanuit een bestand.</p>
            <p>Veel overzichtelijker dan alles in Python strings, toch?</p>
        </div>
    </body>
</html>
```

### Stap 2: CSS bestand maken

Maak een bestand `static/css/style.css`:

```css
body {
    font-family: Arial;
    padding: 20px;
}

h1 {
    color: blue;
}
```

### Stap 3: Endpoint toevoegen

Voeg deze endpoint toe aan `main.py`:

```python
@app.get("/")
async def home():
    return FileResponse("static/pages/home.html")
```

### Wat doet deze code?

- `FileResponse()` - Stuurt een bestand terug naar de browser
- `"static/pages/home.html"` - Het pad naar het HTML bestand

### Testen

Start je server met `fastapi dev main.py` en ga naar [http://127.0.0.1:8000/](http://127.0.0.1:8000/).

Je zou nu je HTML pagina moeten zien met de CSS styling!

## Meerdere pagina's

Maak een tweede pagina `static/pages/about.html`:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Over Mij</title>
        <link rel="stylesheet" href="/static/css/style.css">
    </head>
    <body>
        <div class="container">
            <h1>Over Mij</h1>
            <p>Ik ben een student die bezig is met het leren van webdevelopment.</p>
            <p>FastAPI maakt het serveren van HTML pagina's heel eenvoudig!</p>

            <nav>
                <a href="/">Terug naar Home</a>
            </nav>
        </div>
    </body>
</html>
```

Voeg een endpoint toe:

```python
@app.get("/about")
async def about():
    return FileResponse("static/pages/about.html")
```

## Links tussen pagina's toevoegen

Update je `home.html` om een link naar de about pagina toe te voegen:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Home</title>
        <link rel="stylesheet" href="/static/css/style.css">
    </head>
    <body>
        <div class="container">
            <h1>Welkom op mijn website!</h1>
            <p>Dit is een HTML pagina die wordt geserveerd vanuit een bestand.</p>
            <p>Veel overzichtelijker dan alles in Python strings, toch?</p>

            <nav>
                <a href="/about">Over Mij</a>
            </nav>
        </div>
    </body>
</html>
```

## Opdrachten

### Opdracht 1: Predict - Wat gebeurt er?

Bekijk deze code:

```python
@app.get("/contact")
async def contact():
    return FileResponse("static/pages/contact.html")
```

**Vragen:**
1. Wat gebeurt er als je naar `/contact` gaat maar `contact.html` bestaat niet?
2. Waar moet je het `contact.html` bestand plaatsen?

<details>
<summary>Klik hier voor de antwoorden</summary>

1. Je krijgt een foutmelding (500 Internal Server Error) omdat het bestand niet gevonden kan worden.
2. Je moet het bestand plaatsen in `static/pages/contact.html` (het pad dat in de FileResponse staat).

</details>

### Opdracht 2: Run - Maak een contact pagina

1. Maak een nieuw bestand `static/pages/contact.html`
2. Voeg HTML toe met:
   - Een titel "Contact"
   - Een paragraaf met contactinformatie (bijvoorbeeld je email of social media)
   - Links terug naar home en about
3. Voeg de endpoint toe aan `main.py`
4. Test het in je browser

**Bonus:** Style de links mooi met CSS!

### Opdracht 3: Investigate - CSS delen

**Vraag:** Waarom werkt de CSS (uit `style.css`) op alle pagina's?

<details>
<summary>Klik hier voor het antwoord</summary>

De CSS werkt op alle pagina's omdat elke HTML pagina deze regel bevat:
```html
<link rel="stylesheet" href="/static/css/style.css">
```

Dit betekent dat alle pagina's hetzelfde CSS bestand laden. Als je de CSS aanpast, veranderen alle pagina's tegelijk! Dit is heel handig voor een consistente stijl.

</details>

### Opdracht 4: Modify - Navigatie menu

Maak een navigatie menu dat op elke pagina hetzelfde is.

Voeg deze CSS toe aan `style.css`:

```css
nav a {
    margin-right: 10px;
}
```

Voeg dit menu toe aan **alle** je HTML pagina's (home, about, contact):

```html
<nav>
    <a href="/">Home</a>
    <a href="/about">Over Mij</a>
    <a href="/contact">Contact</a>
</nav>
```

Test het en kijk hoe je nu makkelijk tussen pagina's kunt navigeren!

### Opdracht 5: Make - Portfolio website

Maak een complete portfolio website met minimaal 4 pagina's:
1. **Home** (`/`) - Verwelkoming en introductie
2. **Projecten** (`/projecten`) - Lijst met projecten die je hebt gemaakt
3. **Vaardigheden** (`/vaardigheden`) - Lijst met je programmeertalen en tools
4. **Contact** (`/contact`) - Hoe mensen je kunnen bereiken

**Requirements:**
- Elk pagina heeft hetzelfde navigatie menu
- Elke pagina gebruikt dezelfde CSS
- Voeg basis CSS styling toe (kleuren, marges)
- Gebruik afbeeldingen waar relevant (bijvoorbeeld project screenshots)

<details>
<summary>Klik hier voor tips</summary>

**Mappenstructuur:**
```
static/
├── pages/
│   ├── home.html
│   ├── projecten.html
│   ├── vaardigheden.html
│   └── contact.html
├── css/
│   └── style.css
└── images/
    └── project1.png
```

**Tip voor projecten pagina:**
```html
<div class="project">
    <h3>Calculator App</h3>
    <img src="/static/images/calculator.png" alt="Calculator">
    <p>Een eenvoudige calculator gebouwd met Python.</p>
</div>
```

**Tip voor vaardigheden pagina:**
```html
<ul class="skills">
    <li>Python</li>
    <li>HTML & CSS</li>
    <li>FastAPI</li>
    <li>Git</li>
</ul>
```

</details>

## Voordelen van deze aanpak

✅ **Overzichtelijk** - HTML, CSS en Python gescheiden
✅ **Eenvoudig** - Geen extra libraries nodig
✅ **Snel** - Wijzigingen direct zichtbaar (als FastAPI in dev mode draait)
✅ **Herbruikbaar** - CSS wordt door alle pagina's gedeeld

## Nadelen

❌ **Statisch** - Geen dynamische content (geen gebruikersnaam, geen database data)
❌ **Herhaling** - Navigatie menu moet op elke pagina apart
❌ **Geen variabelen** - Alles is hardcoded in HTML

**Later leer je over Jinja2 templates om deze nadelen op te lossen!**

## Samenvatting

Je hebt nu geleerd om:
- ✅ HTML bestanden te serveren met `FileResponse`
- ✅ Aparte CSS bestanden te gebruiken
- ✅ Meerdere pagina's te maken met navigatie
- ✅ Een complete website structuur op te zetten

In de volgende les gaan we kijken naar afbeeldingen en hoe we die kunnen tonen op onze pagina's!
