---
sidebar_position: 3
---

# HTML & CSS

Tot nu toe hebben we alleen JSON teruggestuurd vanuit onze API. Maar wat als we een échte webpagina willen tonen met HTML en CSS? Daar gaan we nu mee aan de slag!

## HTMLResponse importeren

Voeg bovenaan je `main.py` de volgende import toe:

```python
from fastapi import FastAPI
from fastapi.responses import HTMLResponse
```

## Een eenvoudige HTML pagina

Voeg nu deze nieuwe endpoint toe aan je `main.py`:

```python
@app.get("/html", response_class=HTMLResponse)
async def get_html():
    return """
    <!DOCTYPE html>
    <html>
        <head>
            <title>Mijn eerste pagina</title>
        </head>
        <body>
            <h1>Hello World!</h1>
            <p>Dit is mijn eerste HTML pagina via FastAPI.</p>
        </body>
    </html>
    """
```

### Wat doet deze code?

- `@app.get("/html", response_class=HTMLResponse)` - Geeft aan dat deze endpoint HTML teruggeeft in plaats van JSON
- De functie `get_html()` returnt een string met HTML code
- We gebruiken een multi-line string (`"""`) om de HTML overzichtelijk te houden

### Testen

Start je server met `fastapi dev main.py` en ga naar [http://127.0.0.1:8000/html](http://127.0.0.1:8000/html).

Je zou nu een webpagina moeten zien met een heading en een paragraaf!

## HTML met CSS

Laten we de pagina wat mooier maken met CSS:

```python
@app.get("/styled", response_class=HTMLResponse)
async def get_styled_html():
    return """
    <!DOCTYPE html>
    <html>
        <head>
            <title>Gestylede pagina</title>
            <style>
                body {
                    font-family: Arial, sans-serif;
                    background-color: #f0f0f0;
                    margin: 0;
                    padding: 20px;
                }
                h1 {
                    color: #333;
                    text-align: center;
                }
                .container {
                    max-width: 800px;
                    margin: 0 auto;
                    background-color: white;
                    padding: 30px;
                    border-radius: 10px;
                    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
                }
                p {
                    line-height: 1.6;
                    color: #666;
                }
            </style>
        </head>
        <body>
            <div class="container">
                <h1>Welkom!</h1>
                <p>Dit is een mooie gestylede pagina.</p>
                <p>CSS maakt webpaginas veel aantrekkelijker!</p>
            </div>
        </body>
    </html>
    """
```

Ga naar [http://127.0.0.1:8000/styled](http://127.0.0.1:8000/styled) om het resultaat te zien.

## Opdrachten

### Opdracht 1: Make - Maak je eigen pagina

Maak een nieuwe endpoint `/portfolio` die een persoonlijke portfolio pagina toont met:

- Een titel met jouw naam
- Een subtitel "Mijn Portfolio"
- Minimaal 3 paragrafen over jezelf (hobby's, vaardigheden, favoriete vakken)
- CSS styling met:
  - geef de paragrafen een andere tekstkleur

**Tip:** Gebruik de eerdere voorbeelden als basis en pas ze aan naar jouw stijl!

<details>
<summary>Klik hier voor een voorbeeldoplossing</summary>

```python
@app.get("/portfolio", response_class=HTMLResponse)
async def portfolio():
    return """
    <!DOCTYPE html>
    <html>
        <head>
            <title>Mijn Portfolio</title>
            <style>
                p {
                    color: #770d0dff;
                }
            </style>
        </head>
        <body>
            <div class="container">
                <h1>Jouw Naam</h1>
                <h2>Mijn Portfolio</h2>

                <p>Hallo! Ik ben een enthousiaste leerling die graag programmeert.
                Ik vind het geweldig om nieuwe technologieën te leren en creatieve
                oplossingen te bedenken voor problemen.</p>

                <p><strong>Vaardigheden:</strong> Ik heb ervaring met Python, HTML, CSS,
                en ik ben nu bezig met het leren van FastAPI om webapplicaties te bouwen.
                Ik vind backend development heel interessant!</p>

                <p><strong>Favoriete vakken:</strong> Informatica staat natuurlijk bovenaan,
                maar ik vind ook wiskunde en Engels leuk. Elk vak heeft zijn eigen uitdagingen
                en dat maakt het interessant.</p>
            </div>
        </body>
    </html>
    """
```

**Uitdaging:** Kun je een gradient achtergrond toevoegen zoals in dit voorbeeld? Of een highlight box met een gekleurde rand?

</details>

### Opdracht 6: Make - Interactieve elementen

Maak een nieuwe endpoint `/button` met een knop die van kleur verandert als je eroverheen beweegt met je muis.

**Tip:** Gebruik de CSS `:hover` pseudo-class om styling toe te passen wanneer je muis over een element beweegt.

<details>
<summary>Klik hier voor de oplossing</summary>

```python
@app.get("/button", response_class=HTMLResponse)
async def button_page():
    return """
    <!DOCTYPE html>
    <html>
        <head>
            <title>Interactieve Knop</title>
            <style>
                body {
                    display: flex;
                    justify-content: center;
                    align-items: center;
                    min-height: 100vh;
                    margin: 0;
                    background-color: #f5f5f5;
                    font-family: Arial, sans-serif;
                }
                .button {
                    padding: 20px 40px;
                    font-size: 24px;
                    background-color: #4CAF50;
                    color: white;
                    border: none;
                    border-radius: 8px;
                    cursor: pointer;
                    transition: all 0.3s ease;
                    box-shadow: 0 4px 6px rgba(0,0,0,0.1);
                }
                .button:hover {
                    background-color: #45a049;
                    transform: translateY(-2px);
                    box-shadow: 0 6px 12px rgba(0,0,0,0.2);
                }
            </style>
        </head>
        <body>
            <button class="button">Hover over mij!</button>
        </body>
    </html>
    """
```

</details>

## Extra uitdaging: Meerdere pagina's

Kun je een homepage maken (`/`) met links naar al je andere pagina's? Gebruik HTML `<a>` tags om links te maken:

```html
<a href="/styled">Ga naar de gestylede pagina</a>
```

<details>
<summary>Klik hier voor een voorbeeldoplossing</summary>

```python
@app.get("/", response_class=HTMLResponse)
async def homepage():
    return """
    <!DOCTYPE html>
    <html>
        <head>
            <title>Homepage</title>
        </head>
        <body>
            <h1>Welkom bij mijn FastAPI Website!</h1>
            <p>Kies een pagina om te bezoeken:</p>
            <ul class="nav">
                <li><a href="/html">Simpele HTML pagina</a></li>
                <li><a href="/styled">Gestylede pagina</a></li>
                <li><a href="/kaart">Profiel kaart</a></li>
                <li><a href="/portfolio">Mijn portfolio</a></li>
                <li><a href="/button">Interactieve knop</a></li>
                <li><a href="/docs">API Documentatie</a></li>
            </ul>
        </body>
    </html>
    """
```

</details>

## Samenvatting

Je hebt nu geleerd om:
- ✅ HTML terug te geven vanuit FastAPI met `HTMLResponse`
- ✅ CSS te gebruiken om je pagina's te stylen
- ✅ Multi-line strings te gebruiken voor overzichtelijke HTML
- ✅ Interactieve elementen te maken met `:hover`
- ✅ Meerdere pagina's te linken met `<a>` tags

In de volgende les gaan we kijken naar hoe we externe HTML en CSS bestanden kunnen gebruiken in plaats van alles in één string te zetten!
