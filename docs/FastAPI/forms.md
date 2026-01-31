---
sidebar_position: 8
---

# Eigen POST request

Tot nu toe hebben we alleen data *getoond* aan gebruikers. Maar hoe ontvangen we data *van* gebruikers? Dat doen we met HTML formulieren en POST requests!

## Een simpel formulier

Laten we een simpel formulier maken waar iemand zijn naam kan invullen.

### Stap 1: HTML formulier maken

Maak een bestand `static/pages/naam_form.html`:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Naam Formulier</title>
    </head>
    <body>
        <h1>Wat is je naam?</h1>
        <form method="post" action="/verstuur">
            <label for="naam">Naam:</label>
            <input type="text" id="naam" name="naam" required>
            <button type="submit">Verstuur</button>
        </form>
    </body>
</html>
```

### Belangrijke onderdelen

- `<form method="post" action="/verstuur">` - Stuurt data via POST naar `/verstuur`
- `<input name="naam">` - Invoerveld met naam "naam"
- `name="naam"` - Deze naam gebruiken we straks in Python!
- `required` - Veld is verplicht
- `<button type="submit">` - Knop om te versturen

### Stap 2: GET endpoint (formulier tonen)

In `main.py`:

```python
from fastapi import FastAPI
from fastapi.responses import FileResponse

app = FastAPI()

@app.get("/naam")
async def toon_formulier():
    return FileResponse("static/pages/naam_form.html")
```

Dit toont het lege formulier.

### Stap 3: POST endpoint (data ontvangen)

Voeg deze imports toe:

```python
from fastapi import FastAPI, Form
from fastapi.responses import FileResponse
```

Voeg een POST endpoint toe:

```python
@app.post("/verstuur")
async def ontvang_naam(naam: str = Form(...)):
    print(f"Naam ontvangen: {naam}")
    return {"bericht": f"Hallo {naam}!"}
```

### Wat doet deze code?

- `@app.post("/verstuur")` - Luistert naar POST requests op `/verstuur`
- `naam: str = Form(...)` - Haalt het veld `naam` uit het formulier
- `Form(...)` - Vertelt FastAPI dat dit een formulier veld is
- De `...` betekent dat het veld verplicht is

## Testen

1. Start je server: `fastapi dev main.py`
2. Ga naar: `http://127.0.0.1:8000/naam`
3. Vul je naam in en klik op Verstuur
4. Je ziet een JSON bericht: `{"bericht": "Hallo Jan!"}`
5. In de terminal zie je: `Naam ontvangen: Jan`

## Meerdere velden

Je kunt meerdere velden toevoegen:

```html
<form method="post" action="/registreer">
    <label for="naam">Naam:</label>
    <input type="text" name="naam" required>

    <label for="leeftijd">Leeftijd:</label>
    <input type="number" name="leeftijd" required>

    <button type="submit">Verstuur</button>
</form>
```

En in Python:

```python
@app.post("/registreer")
async def registreer(
    naam: str = Form(...),
    leeftijd: int = Form(...)
):
    print(f"{naam} is {leeftijd} jaar oud")
    return {"naam": naam, "leeftijd": leeftijd}
```

**Let op:** De `name` in HTML moet matchen met de parameter naam in Python!

## Verschillende input types

```html
<!-- Tekst -->
<input type="text" name="naam">

<!-- Email -->
<input type="email" name="email">

<!-- Nummer -->
<input type="number" name="leeftijd">

<!-- Wachtwoord (sterretjes) -->
<input type="password" name="wachtwoord">

<!-- Textarea (meerdere regels) -->
<textarea name="bericht"></textarea>
```

## Opdrachten

### Opdracht 1: Predict - Wat zie je?

Gegeven dit formulier:

```html
<form method="post" action="/groet">
    <input type="text" name="naam">
    <button type="submit">Verstuur</button>
</form>
```

En deze Python code:

```python
@app.post("/groet")
async def groet(naam: str = Form(...)):
    return {"groet": f"Hallo {naam}!"}
```

**Vraag:** Als je "Sarah" invult, wat zie je dan in de browser?

<details>
<summary>Klik hier voor het antwoord</summary>

Je ziet: `{"groet": "Hallo Sarah!"}`

De browser toont een JSON object met je naam erin verwerkt.

</details>

### Opdracht 2: Run - Maak je eerste formulier

Maak een formulier waar iemand zijn favoriete kleur kan invullen:

1. Maak `static/pages/kleur_form.html` met een formulier
2. Voeg een GET endpoint `/kleur` toe om het formulier te tonen
3. Voeg een POST endpoint `/kleur_verstuur` toe om de data te ontvangen
4. Print de kleur in de terminal

Test het!

### Opdracht 3: Investigate - Verplicht vs Optioneel

Bekijk dit verschil:

```python
# Verplicht veld
naam: str = Form(...)

# Optioneel veld
bijnaam: str = Form(None)
```

**Vraag:** Wat gebeurt er als je een verplicht veld leeg laat?

<details>
<summary>Klik hier voor het antwoord</summary>

FastAPI geeft een 422 error omdat het veld verplicht is. Het formulier wordt niet verwerkt.

Bij een optioneel veld (`Form(None)`) krijgt de variabele de waarde `None` als je het veld leeg laat.

</details>

### Opdracht 4: Modify - Twee velden

Pas je formulier uit Opdracht 2 aan en voeg een tweede veld toe voor "leeftijd".

Update ook de POST endpoint om beide velden te ontvangen en te printen.

### Opdracht 5: Make - Contact formulier

Maak een contact formulier met:
- Naam (text, verplicht)
- Email (email, verplicht)
- Bericht (textarea, verplicht)

Print alle gegevens in de terminal als het formulier wordt verstuurd.

<details>
<summary>Klik hier voor een hint</summary>

HTML:
```html
<form method="post" action="/contact">
    <input type="text" name="naam" required>
    <input type="email" name="email" required>
    <textarea name="bericht" required></textarea>
    <button type="submit">Verstuur</button>
</form>
```

Python:
```python
@app.post("/contact")
async def contact(
    naam: str = Form(...),
    email: str = Form(...),
    bericht: str = Form(...)
):
    print(f"Van: {naam} ({email})")
    print(f"Bericht: {bericht}")
    return {"status": "ontvangen"}
```

</details>

## Troubleshooting

### "Field required" error

Zorg dat:
- Het `name` attribuut in HTML overeenkomt met de Python parameter
- Je `Form(...)` gebruikt in Python
- Het veld is ingevuld (of gebruik `Form(None)` voor optionele velden)

### Data komt niet aan

Controleer:
- Is `method="post"` ingesteld in de `<form>` tag?
- Staat de juiste `action="/endpoint"` in de form?
- Heb je `from fastapi import Form` geïmporteerd?

## Samenvatting

Je hebt nu geleerd:
- ✅ HTML formulieren maken met `<form method="post">`
- ✅ Input velden toevoegen met `<input name="...">`
- ✅ Form data ontvangen met `Form(...)` in FastAPI
- ✅ Het verschil tussen verplichte (`Form(...)`) en optionele (`Form(None)`) velden
- ✅ Verschillende input types gebruiken

In de volgende les gaan we kijken naar het opslaan van form data in een database!
