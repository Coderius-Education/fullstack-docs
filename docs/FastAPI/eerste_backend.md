---
sidebar_position: 2
---

# Eerste back-end

Maak een nieuw bestand aan met de naam `main.py` en plak hierin de volgende code:

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "Hello World"}
```

## Wat doet deze code?

- `from fastapi import FastAPI` - Importeert de FastAPI library
- `app = FastAPI()` - Maakt een nieuwe FastAPI applicatie aan
- `@app.get("/")` - Een decorator die aangeeft dat deze functie uitgevoerd wordt bij een GET request naar de root URL (`/`)
- `async def root():` - Definieert een asynchrone functie genaamd `root`
- `return {"message": "Hello World"}` - Stuurt een JSON response terug naar de browser

## De server starten

Draai nu in de terminal het volgende commando:

```bash
fastapi dev main.py
```

Er verschijnt veel tekst in je terminal, maar één regel is voor nu heel belangrijk:

```
1. server   Server started at http://127.0.0.1:8000
```

Je website draait op deze URL. Ga er maar eens heen in je browser.

## Wat moet je zien?

Als je naar [http://127.0.0.1:8000](http://127.0.0.1:8000) gaat, zie je:

```json
{"message":"Hello World"}
```

## Automatische API documentatie

FastAPI genereert automatisch interactieve API documentatie. Ga naar:

- [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs) - Swagger UI (interactief)
- [http://127.0.0.1:8000/redoc](http://127.0.0.1:8000/redoc) - ReDoc (alternatieve weergave)

Hier kun je je API testen zonder code te schrijven!

## De server stoppen

Druk op `Ctrl+C` in de terminal om de server te stoppen.

## Troubleshooting

**Port al in gebruik?**
Als je een foutmelding krijgt dat poort 8000 al in gebruik is, stop dan eerst andere draaiende FastAPI servers of gebruik een andere poort:

```bash
fastapi dev main.py --port 8001
```

## Opdrachten

### Opdracht 1: Modify - Verander de boodschap

Pas de code aan zodat de API een andere boodschap teruggeeft. 

Bijvoorbeeld: `{"message": "Welkom bij mijn API"}` of `{"bericht": "Hallo wereld"}`.

<details>
<summary>Klik hier voor de oplossing</summary>

Je kunt de return statement aanpassen:

```python
@app.get("/")
async def root():
    return {"message": "Welkom bij mijn API"}
```

Of je kunt zelfs de key veranderen:

```python
@app.get("/")
async def root():
    return {"bericht": "Hallo wereld", "status": "online"}
```

Vergeet niet de server opnieuw te laden (dit gebeurt automatisch met `fastapi dev`). Check het resultaat in je browser!

</details>

### Opdracht 2: Make - Maak een nieuwe endpoint

Maak een nieuw endpoint `/info` die informatie over jezelf teruggeeft, zoals je naam, leeftijd en favoriete programmeertaal.

**Tip:** Je hebt een nieuwe functie nodig met een `@app.get()` decorator, net zoals bij de root endpoint.

<details>
<summary>Klik hier voor de oplossing</summary>

Voeg deze code toe onder de bestaande `root()` functie:

```python
@app.get("/info")
async def info():
    return {
        "naam": "Jouw Naam",
        "leeftijd": 16,
        "favoriete_taal": "Python"
    }
```

Je volledige `main.py` ziet er nu zo uit:

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "Hello World"}


@app.get("/info")
async def info():
    return {
        "naam": "Jouw Naam",
        "leeftijd": 16,
        "favoriete_taal": "Python"
    }
```

Test het door naar [http://127.0.0.1:8000/info](http://127.0.0.1:8000/info) te gaan. Je kunt de nieuwe endpoint ook zien in de automatische documentatie op `/docs`!

</details>


