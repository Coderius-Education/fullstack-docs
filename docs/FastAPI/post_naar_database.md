---
sidebar_position: 11
---

# POST naar database

Nu je weet hoe POST requests werken én hoe sqlitedict werkt, gaan we ze combineren! We maken een formulier dat data permanent opslaat in een database.

## Een simpel voorbeeld: Naam opslaan

We maken een formulier waar iemand zijn naam kan invullen. Die naam slaan we op in een database.

### Stap 1: HTML formulier

Maak `static/pages/naam_opslaan.html`:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Naam Opslaan</title>
    </head>
    <body>
        <h1>Bewaar je naam</h1>
        <form method="post" action="/opslaan">
            <input type="text" name="naam" required>
            <button type="submit">Opslaan</button>
        </form>
    </body>
</html>
```

### Stap 2: FastAPI endpoints

In `main.py`:

```python
from fastapi import FastAPI, Form
from fastapi.responses import FileResponse
from sqlitedict import SqliteDict

app = FastAPI()

@app.get("/naam")
async def naam_formulier():
    return FileResponse("static/pages/naam_opslaan.html")

@app.post("/opslaan")
async def naam_opslaan(naam: str = Form(...)):
    # Open database en sla naam op
    with SqliteDict("namen.db") as db:
        db["laatste_naam"] = naam
        db.commit()

    return {"bericht": f"Naam '{naam}' opgeslagen!"}
```

### Wat gebeurt hier?

1. Gebruiker gaat naar `/naam` → ziet formulier
2. Gebruiker vult "Jan" in → klikt Opslaan
3. POST request naar `/opslaan` met `naam="Jan"`
4. `SqliteDict("namen.db")` opent de database
5. `db["laatste_naam"] = naam` slaat de naam op
6. `db.commit()` maakt het permanent
7. Browser toont: `{"bericht": "Naam 'Jan' opgeslagen!"}`

**Belangrijk:** Ook als je de server herstart, de naam blijft bewaard!

## Test het

1. Start je server: `fastapi dev main.py`
2. Ga naar: `http://127.0.0.1:8000/naam`
3. Vul een naam in en klik Opslaan
4. Stop de server (Ctrl+C)
5. Start de server opnieuw
6. De naam staat nog steeds in `namen.db`!

## Data terug lezen

Voeg een endpoint toe om de opgeslagen naam te zien:

```python
@app.get("/toon_naam")
async def toon_naam():
    with SqliteDict("namen.db") as db:
        naam = db.get("laatste_naam", "Nog geen naam opgeslagen")

    return {"laatste_naam": naam}
```

Ga naar `http://127.0.0.1:8000/toon_naam` om de opgeslagen naam te zien!

## Meerdere namen opslaan

In plaats van steeds overschrijven, kunnen we elke naam met een unieke key opslaan:

```python
import time

@app.post("/opslaan")
async def naam_opslaan(naam: str = Form(...)):
    # Gebruik timestamp als unieke key
    key = f"naam_{int(time.time())}"

    with SqliteDict("namen.db") as db:
        db[key] = naam
        db.commit()

    return {"bericht": f"Naam '{naam}' opgeslagen met key '{key}'"}
```

Alle namen bekijken:

```python
@app.get("/alle_namen")
async def alle_namen():
    with SqliteDict("namen.db") as db:
        namen = list(db.values())

    return {"namen": namen}
```

## Opdrachten

### Opdracht 1: Predict - Wat zie je?

Je runt deze code twee keer:

```python
# Eerste keer
@app.post("/sla_op")
async def sla_op(woord: str = Form(...)):
    with SqliteDict("data.db") as db:
        db["woord"] = woord
        db.commit()
    return {"status": "opgeslagen"}
```

Eerste keer vul je "Hallo" in, tweede keer "Dag".

**Vraag:** Wat staat er in de database na de tweede keer?

<details>
<summary>Klik hier voor het antwoord</summary>

In de database staat: `{"woord": "Dag"}`

De nieuwe waarde overschrijft de oude omdat we dezelfde key ("woord") gebruiken.

</details>

### Opdracht 2: Run - Score opslaan

Maak een formulier waar iemand zijn game score kan invoeren en opslaan:

1. Maak `static/pages/score_form.html` met een number input
2. Maak een POST endpoint die de score opslaat in `scores.db`
3. Maak een GET endpoint om de score te bekijken

Test het!

### Opdracht 3: Investigate - Zonder commit

**Vraag:** Wat gebeurt er als je `db.commit()` vergeet in de POST endpoint?

Probeer het uit door commit weg te halen en het formulier in te vullen. Stop dan de server en start opnieuw. Is de data er nog?

<details>
<summary>Klik hier voor het antwoord</summary>

Nee! Zonder `db.commit()` wordt de data alleen in het geheugen opgeslagen, maar niet naar het bestand geschreven. Als je de server herstart is de data weg.

**Altijd commit() gebruiken!**

</details>

### Opdracht 4: Modify - Met template response

Pas Opdracht 2 aan zodat na het opslaan een mooie HTML pagina wordt getoond in plaats van JSON.

Maak een template `templates/score_opgeslagen.html` en gebruik `TemplateResponse`.

<details>
<summary>Klik hier voor een hint</summary>

```python
from fastapi import Request
from fastapi.templating import Jinja2Templates

templates = Jinja2Templates(directory="templates")

@app.post("/score")
async def score_opslaan(request: Request, score: int = Form(...)):
    with SqliteDict("scores.db") as db:
        db["score"] = score
        db.commit()

    return templates.TemplateResponse(
        "score_opgeslagen.html",
        {"request": request, "score": score}
    )
```

</details>

### Opdracht 5: Make - Gastenboek

Maak een gastenboek waar mensen een bericht kunnen achterlaten:

1. Formulier met naam en bericht
2. Sla elk bericht op met een unieke key (gebruik timestamp)
3. Maak een pagina die alle berichten toont

<details>
<summary>Klik hier voor een hint</summary>

**HTML formulier:**
```html
<form method="post" action="/gastenboek">
    <input type="text" name="naam" required>
    <textarea name="bericht" required></textarea>
    <button type="submit">Verstuur</button>
</form>
```

**Opslaan:**
```python
import time

@app.post("/gastenboek")
async def gastenboek_post(
    naam: str = Form(...),
    bericht: str = Form(...)
):
    key = f"bericht_{int(time.time())}"

    with SqliteDict("gastenboek.db") as db:
        db[key] = {
            "naam": naam,
            "bericht": bericht
        }
        db.commit()

    return {"status": "Bericht opgeslagen!"}
```

**Tonen:**
```python
@app.get("/berichten")
async def toon_berichten():
    with SqliteDict("gastenboek.db") as db:
        berichten = list(db.values())

    return {"berichten": berichten}
```

</details>

## Veelgemaakte fouten

### 1. Database in POST maar niet commit

```python
# ❌ FOUT
@app.post("/opslaan")
async def opslaan(data: str = Form(...)):
    with SqliteDict("data.db") as db:
        db["key"] = data
        # Vergeten: db.commit()
    return {"status": "ok"}
```

Data verdwijnt na server herstart!

### 2. Zelfde key overschrijven

```python
# ❌ Probleem (overschrijft steeds)
db["naam"] = nieuwe_naam

# ✅ Beter (unieke keys)
db[f"naam_{timestamp}"] = nieuwe_naam
```

### 3. Database vergeten te importeren

```python
# ❌ FOUT
from sqlitedict import SqliteDict  # Vergeten!

@app.post("/opslaan")
async def opslaan(naam: str = Form(...)):
    with SqliteDict("data.db") as db:  # NameError!
```

Vergeet de import niet!

## Samenvatting

Je hebt nu geleerd:
- ✅ POST request data opslaan in sqlitedict
- ✅ Database openen in een endpoint
- ✅ `db.commit()` gebruiken om permanent op te slaan
- ✅ Data permanent bewaren tussen server restarts
- ✅ Unieke keys gebruiken om meerdere items op te slaan

**Belangrijkste punten:**
1. Open database met `SqliteDict("bestand.db")`
2. Sla data op met `db[key] = waarde`
3. Vergeet `db.commit()` niet!
4. Gebruik `with ... as db:` om automatisch te sluiten

Nu kun je gebruikersinput permanent opslaan! 🎉
