---
sidebar_position: 7
---

# GET vs POST

Voordat we met formulieren gaan werken, is het belangrijk om te begrijpen wat het verschil is tussen **GET** en **POST** requests. Dit zijn de twee meest gebruikte HTTP methoden.

## Wat zijn HTTP methoden?

Elke keer dat je browser met een server communiceert, gebruikt het een HTTP methode om aan te geven *wat* het wil doen. De twee belangrijkste zijn:

- **GET** - Vraag data op van de server
- **POST** - Stuur data naar de server

## GET requests

### Wanneer gebruik je GET?

- Een pagina laden
- Zoeken op een website
- Data ophalen zonder iets te veranderen

### Kenmerken van GET

- ✅ **Data in de URL** - Parameters zijn zichtbaar in de adresbalk
- ✅ **Kan gebookmarked** - Je kunt de URL opslaan
- ✅ **Kan gecached** - Browser kan het resultaat onthouden
- ❌ **Niet voor gevoelige data** - Wachtwoorden nooit via GET!

### Voorbeeld GET request

Kijk naar deze URL:
```
https://www.google.com/search?q=fastapi
```

- `search` is de pagina
- `?q=fastapi` is de data (zoekterm)
- Alles staat in de URL!

### GET in FastAPI

```python
@app.get("/zoek")
async def zoek(term: str):
    return {"je_zocht_naar": term}
```

Je kunt dit testen op: `http://127.0.0.1:8000/zoek?term=python`

De `term` komt uit de URL!

## POST requests

### Wanneer gebruik je POST?

- Formulieren versturen
- Data opslaan in een database
- Inloggen
- Iets aanmaken of wijzigen

### Kenmerken van POST

- ✅ **Data in de body** - Niet zichtbaar in de URL
- ✅ **Veilig voor gevoelige data** - Zoals wachtwoorden
- ✅ **Kan grote hoeveelheden data versturen** - Geen limiet zoals bij GET
- ❌ **Kan niet gebookmarked** - De data staat niet in de URL

### POST in FastAPI

```python
from fastapi import Form

@app.post("/login")
async def login(
    gebruiker: str = Form(...),
    wachtwoord: str = Form(...)
):
    return {"ingelogd_als": gebruiker}
```

De data komt uit het formulier, niet uit de URL!

## Het verschil in de praktijk

### Scenario: Zoeken op een website

**GET** is perfect:
```
/zoek?term=fastapi&categorie=programmeren
```

- Je kunt de zoekresultaten delen (URL kopieren)
- Je kunt terug naar deze zoekopdracht
- De data is niet gevoelig

### Scenario: Inlogformulier

**POST** is nodig:
```python
@app.post("/login")
async def login(
    gebruiker: str = Form(...),
    wachtwoord: str = Form(...)
):
    # Wachtwoord staat NIET in de URL!
    return {"status": "ingelogd"}
```

- Wachtwoord is niet zichtbaar in de URL
- Veiliger
- Geen bookmark mogelijk (dat wil je ook niet bij inloggen)

## Beide methoden op dezelfde URL

Je kunt zowel GET als POST op dezelfde URL hebben:

```python
@app.get("/contact")
async def contact_pagina():
    # Toon het formulier
    return FileResponse("static/pages/contact.html")

@app.post("/contact")
async def contact_verstuur(
    naam: str = Form(...),
    bericht: str = Form(...)
):
    # Verwerk het formulier
    print(f"Bericht van {naam}: {bericht}")
    return {"status": "ontvangen"}
```

- **GET /contact** - Toont het lege formulier
- **POST /contact** - Verwerkt de ingevulde data

## Opdrachten

### Opdracht 1: Predict - GET of POST?

Voor elk scenario, bepaal of je GET of POST zou gebruiken:

1. Een lijst met alle blogposts ophalen
2. Een nieuw blogpost aanmaken
3. Een specifieke blogpost lezen
4. Een wachtwoord wijzigen
5. Een pagina met producten filteren op prijs

<details>
<summary>Klik hier voor de antwoorden</summary>

1. **GET** - Je haalt alleen data op, verandert niets
2. **POST** - Je stuurt data om iets nieuws aan te maken
3. **GET** - Je vraagt om data, verandert niets
4. **POST** - Wachtwoorden horen NOOIT in een URL
5. **GET** - Je filtert alleen, en je wilt de URL kunnen delen

</details>

### Opdracht 2: Run - Test GET met parameters

Maak een endpoint die een naam uit de URL haalt:

```python
@app.get("/groet")
async def groet(naam: str = "Bezoeker"):
    return {"bericht": f"Hallo {naam}!"}
```

Test het:
- `http://127.0.0.1:8000/groet` - Wat zie je?
- `http://127.0.0.1:8000/groet?naam=Jan` - Wat zie je nu?

### Opdracht 3: Investigate - Data in URL vs Body

Bekijk deze twee endpoints:

```python
# GET - data in URL
@app.get("/bereken")
async def bereken_get(getal1: int, getal2: int):
    return {"som": getal1 + getal2}

# POST - data in body
@app.post("/bereken")
async def bereken_post(
    getal1: int = Form(...),
    getal2: int = Form(...)
):
    return {"som": getal1 + getal2}
```

**Vragen:**
1. Hoe roep je de GET versie aan?
2. Wat heb je nodig voor de POST versie?
3. Welke zou je gebruiken voor een calculator op een website?

<details>
<summary>Klik hier voor de antwoorden</summary>

1. Via de URL: `http://127.0.0.1:8000/bereken?getal1=5&getal2=3`
2. Een HTML formulier met POST method en twee input velden
3. **GET** is beter voor een calculator omdat:
   - Gebruikers kunnen berekeningen delen via URL
   - Geen gevoelige data
   - Simpeler om te gebruiken (geen formulier nodig)

</details>

### Opdracht 4: Make - Beide methoden

Maak een simpele pagina op `/rekenmachine`:

**GET versie** - Toont een HTML pagina met een formulier:
```python
@app.get("/rekenmachine", response_class=HTMLResponse)
async def rekenmachine():
    return """
    <!DOCTYPE html>
    <html>
        <body>
            <h1>Rekenmachine</h1>
            <form method="post" action="/rekenmachine">
                <input type="number" name="a" required>
                +
                <input type="number" name="b" required>
                <button type="submit">Bereken</button>
            </form>
        </body>
    </html>
    """
```

**POST versie** - Verwerkt de berekening:
```python
@app.post("/rekenmachine")
async def rekenmachine_bereken(
    a: int = Form(...),
    b: int = Form(...)
):
    return {"resultaat": a + b}
```

Test het!

## Vergelijkingstabel

| Aspect | GET | POST |
|--------|-----|------|
| **Data locatie** | In de URL (query parameters) | In de request body |
| **Zichtbaarheid** | Zichtbaar in adresbalk | Niet zichtbaar |
| **Bookmark** | Kan gebookmarked | Kan niet gebookmarked |
| **Terug knop** | Werkt normaal | Browser waarschuwt |
| **Gebruik** | Data ophalen | Data versturen/opslaan |
| **Veiligheid** | Niet voor gevoelige data | Veiliger voor gevoelige data |
| **Data limiet** | ~2000 karakters (URL limiet) | Geen praktische limiet |
| **Caching** | Wordt gecached | Wordt niet gecached |

## Samenvatting

Je hebt nu geleerd:
- ✅ Wat GET en POST requests zijn
- ✅ Wanneer je GET gebruikt (data ophalen)
- ✅ Wanneer je POST gebruikt (data versturen)
- ✅ Dat je beide op dezelfde URL kunt gebruiken
- ✅ Waarom wachtwoorden nooit via GET mogen

**Vuistregel:**
- **GET** = "Geef me data"
- **POST** = "Hier is data"

In de volgende les gaan we POST gebruiken met formulieren om data van gebruikers te ontvangen!
