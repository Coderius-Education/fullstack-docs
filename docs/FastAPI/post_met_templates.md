---
sidebar_position: 9
---

# POST met templates

In de vorige les heb je geleerd hoe je formulier data ontvangt met POST requests. Nu gaan we kijken hoe je die data mooi kunt tonen met **Jinja2 templates**.

## Waarom templates?

Tot nu toe hebben we JSON teruggegeven: `{"naam": "Jan"}`. Dat is handig voor API's, maar niet mooi voor gebruikers. Met templates kunnen we de data in een mooie HTML pagina tonen!

## Jinja2 installeren en configureren

### Stap 1: Installeren

Jinja2 is al geïnstalleerd als je `fastapi[standard]` hebt. Zo niet:

```bash
pip install jinja2
```

### Stap 2: Templates folder aanmaken

Maak een `templates` folder in je project:

```
je-project/
├── main.py
├── static/
└── templates/
    └── resultaat.html
```

### Stap 3: Configureren in main.py

```python
from fastapi import FastAPI, Form, Request
from fastapi.responses import FileResponse
from fastapi.templating import Jinja2Templates

app = FastAPI()

# Templates configureren
templates = Jinja2Templates(directory="templates")
```

## Een simpel voorbeeld

### Het formulier (in static folder)

Maak `static/pages/groet_form.html`:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Groet Formulier</title>
    </head>
    <body>
        <h1>Hoe heet je?</h1>
        <form method="post" action="/groet">
            <input type="text" name="naam" required>
            <button type="submit">Verstuur</button>
        </form>
    </body>
</html>
```

### De resultaat template

Maak `templates/groet_resultaat.html`:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Resultaat</title>
    </head>
    <body>
        <h1>Hallo {{ naam }}!</h1>
        <p>Leuk je te ontmoeten.</p>
        <a href="/groet">Terug naar formulier</a>
    </body>
</html>
```

**Let op:** `{{ naam }}` is een **template variabele**. Deze wordt vervangen door de waarde die we vanuit Python sturen!

### De endpoints in main.py

```python
@app.get("/groet")
async def groet_form():
    return FileResponse("static/pages/groet_form.html")

@app.post("/groet")
async def groet_verwerk(request: Request, naam: str = Form(...)):
    return templates.TemplateResponse(
        "groet_resultaat.html",
        {
            "request": request,
            "naam": naam
        }
    )
```

### Wat gebeurt hier?

1. **GET /groet** - Toont het lege formulier
2. Gebruiker vult "Jan" in en klikt Verstuur
3. **POST /groet** - Ontvangt de naam
4. `templates.TemplateResponse()` - Rendert de template
5. `{"request": request, "naam": naam}` - Stuurt data naar de template
6. Template toont: "Hallo Jan!"

**Belangrijk:** `"request": request` moet je altijd meesturen!

## Meerdere variabelen

Je kunt meerdere variabelen naar een template sturen:

### HTML formulier

```html
<form method="post" action="/profiel">
    <input type="text" name="naam" required>
    <input type="number" name="leeftijd" required>
    <button type="submit">Verstuur</button>
</form>
```

### Template

Maak `templates/profiel.html`:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Profiel</title>
    </head>
    <body>
        <h1>Profiel van {{ naam }}</h1>
        <p>Je bent {{ leeftijd }} jaar oud.</p>
    </body>
</html>
```

### Python

```python
@app.post("/profiel")
async def profiel(
    request: Request,
    naam: str = Form(...),
    leeftijd: int = Form(...)
):
    return templates.TemplateResponse(
        "profiel.html",
        {
            "request": request,
            "naam": naam,
            "leeftijd": leeftijd
        }
    )
```

## Opdrachten

### Opdracht 1: Predict - Wat zie je?

Gegeven deze template `begroeting.html`:

```html
<h1>Welkom {{ voornaam }} {{ achternaam }}!</h1>
<p>Je email is: {{ email }}</p>
```

En deze Python code:

```python
@app.post("/welkom")
async def welkom(
    request: Request,
    voornaam: str = Form(...),
    achternaam: str = Form(...),
    email: str = Form(...)
):
    return templates.TemplateResponse(
        "begroeting.html",
        {
            "request": request,
            "voornaam": voornaam,
            "achternaam": achternaam,
            "email": email
        }
    )
```

**Vraag:** Wat zie je als je "Jan", "Jansen", en "jan@email.com" invult?

<details>
<summary>Klik hier voor het antwoord</summary>

Je ziet:
```
Welkom Jan Jansen!
Je email is: jan@email.com
```

De variabelen `{{ voornaam }}`, `{{ achternaam }}` en `{{ email }}` worden vervangen door de waarden uit het dictionary.

</details>

### Opdracht 2: Run - Maak je eerste template

Maak een formulier waar iemand zijn favoriete eten kan invullen:

1. Maak een HTML formulier in `static/pages/`
2. Maak een template in `templates/` die toont: "Jouw favoriete eten is: [eten]"
3. Voeg de GET en POST endpoints toe
4. Test het!

### Opdracht 3: Investigate - Request parameter

**Vraag:** Waarom moet je altijd `"request": request` meesturen in het dictionary?

<details>
<summary>Klik hier voor het antwoord</summary>

FastAPI's template systeem heeft het request object nodig voor interne functionaliteit zoals:
- URL generatie
- Context informatie
- Foutafhandeling

Zonder `"request": request` krijg je een error. Het is een vereiste van FastAPI's Jinja2 integratie.

</details>

### Opdracht 4: Modify - Drie velden

Pas Opdracht 2 aan en voeg twee extra velden toe:
- Favoriete drank
- Favoriete dessert

Toon alle drie de waarden in de template.

### Opdracht 5: Make - Contact resultaat pagina

Maak een contact formulier met:
- Naam (text)
- Email (email)
- Bericht (textarea)

Maak een template die alle ingevoerde gegevens netjes toont met:
- Een titel "Bericht ontvangen"
- Alle velden overzichtelijk weergegeven
- Een link terug naar het formulier

<details>
<summary>Klik hier voor een hint</summary>

**Formulier:**
```html
<form method="post" action="/contact">
    <input type="text" name="naam" required>
    <input type="email" name="email" required>
    <textarea name="bericht" required></textarea>
    <button type="submit">Verstuur</button>
</form>
```

**Template (templates/contact_resultaat.html):**
```html
<!DOCTYPE html>
<html>
    <head>
        <title>Bericht Ontvangen</title>
    </head>
    <body>
        <h1>Bericht Ontvangen</h1>
        <p><strong>Naam:</strong> {{ naam }}</p>
        <p><strong>Email:</strong> {{ email }}</p>
        <p><strong>Bericht:</strong></p>
        <p>{{ bericht }}</p>
        <a href="/contact">Terug naar formulier</a>
    </body>
</html>
```

**Python:**
```python
@app.post("/contact")
async def contact_verwerk(
    request: Request,
    naam: str = Form(...),
    email: str = Form(...),
    bericht: str = Form(...)
):
    return templates.TemplateResponse(
        "contact_resultaat.html",
        {
            "request": request,
            "naam": naam,
            "email": email,
            "bericht": bericht
        }
    )
```

</details>

## Verschil met JSON response

### JSON (vorige les):
```python
@app.post("/naam")
async def naam_post(naam: str = Form(...)):
    return {"naam": naam}
```
→ Toont: `{"naam": "Jan"}` (voor API's)

### Template (deze les):
```python
@app.post("/naam")
async def naam_post(request: Request, naam: str = Form(...)):
    return templates.TemplateResponse(
        "naam_resultaat.html",
        {"request": request, "naam": naam}
    )
```
→ Toont: Mooie HTML pagina met de naam (voor gebruikers)

## Samenvatting

Je hebt nu geleerd:
- ✅ Jinja2 templates configureren in FastAPI
- ✅ `{{ variabele }}` syntax gebruiken in templates
- ✅ Data van POST requests naar templates sturen
- ✅ `TemplateResponse` gebruiken met `request` en je variabelen
- ✅ Het verschil tussen JSON en template responses

**Onthoud:**
- Templates in `templates/` folder
- Altijd `"request": request` meesturen
- `{{ variabele }}` wordt vervangen door de waarde

In de volgende les gaan we kijken naar het opslaan van form data in een database!
