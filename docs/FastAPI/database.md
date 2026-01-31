---
sidebar_position: 10
---

# Gegevens opslaan

Tot nu toe werken we met data die verdwijnt zodra je programma stopt. In deze les leer je hoe je data **permanent** kunt opslaan met een database!

## Wat is sqlitedict?

`sqlitedict` is een Python library waarmee je een database kunt gebruiken alsof het een gewone dictionary is. De data wordt opgeslagen in een bestand en blijft bewaard, zelfs als je computer uit staat!

**Vergelijk:**
- Normale dictionary: `{}` - Data verdwijnt als programma stopt
- sqlitedict: `SqliteDict()` - Data blijft bewaard in een bestand

## Installatie

```bash
pip install sqlitedict
```

## Basis gebruik

### Data opslaan

Maak een bestand `test_db.py`:

```python
from sqlitedict import SqliteDict

# Open de database (maakt bestand als het niet bestaat)
with SqliteDict("mijn_data.db") as db:
    # Sla data op, net als een dictionary
    db["naam"] = "Jan"
    db["leeftijd"] = 16
    db["hobby"] = "Programmeren"

    # BELANGRIJK: Sla de wijzigingen op!
    db.commit()

print("Data opgeslagen!")
```

Run het: `python test_db.py`

Er wordt nu een bestand `mijn_data.db` aangemaakt met je data!

### Data uitlezen

```python
from sqlitedict import SqliteDict

# Open de database (leest het bestaande bestand)
with SqliteDict("mijn_data.db") as db:
    naam = db["naam"]
    leeftijd = db["leeftijd"]

    print(f"Naam: {naam}")
    print(f"Leeftijd: {leeftijd}")
```

Output:
```
Naam: Jan
Leeftijd: 16
```

De data is er nog, zelfs na het herstarten van je programma!

## Belangrijke commando's

### Opslaan (Create/Update)

```python
with SqliteDict("data.db") as db:
    db["key"] = "waarde"
    db.commit()  # NIET vergeten!
```

### Uitlezen (Read)

```python
with SqliteDict("data.db") as db:
    waarde = db["key"]
    print(waarde)
```

### Verwijderen (Delete)

```python
with SqliteDict("data.db") as db:
    del db["key"]
    db.commit()  # NIET vergeten!
```

### Alles bekijken

```python
with SqliteDict("data.db") as db:
    for key, value in db.items():
        print(f"{key}: {value}")
```

### Checken of iets bestaat

```python
with SqliteDict("data.db") as db:
    if "naam" in db:
        print("Naam bestaat!")
    else:
        print("Naam bestaat niet.")
```

## Complexe data opslaan

Je kunt ook dictionaries en lijsten opslaan:

```python
from sqlitedict import SqliteDict

with SqliteDict("gebruikers.db") as db:
    # Dictionary opslaan
    db["jan"] = {
        "email": "jan@mail.com",
        "leeftijd": 16,
        "hobbies": ["gamen", "programmeren"]
    }

    db["sarah"] = {
        "email": "sarah@mail.com",
        "leeftijd": 15,
        "hobbies": ["muziek", "tekenen"]
    }

    db.commit()

print("Gebruikers opgeslagen!")
```

Uitlezen:

```python
from sqlitedict import SqliteDict

with SqliteDict("gebruikers.db") as db:
    jan = db["jan"]
    print(f"Email: {jan['email']}")
    print(f"Hobbies: {jan['hobbies']}")
```

## Opdrachten

### Opdracht 1: Predict - Wat gebeurt er?

Gegeven deze code:

```python
from sqlitedict import SqliteDict

# Eerste keer runnen
with SqliteDict("test.db") as db:
    db["score"] = 100
    db.commit()

# Tweede keer runnen (nieuw programma)
with SqliteDict("test.db") as db:
    print(db["score"])
```

**Vraag:** Wat zie je als je dit programma twee keer achter elkaar runt?

<details>
<summary>Klik hier voor het antwoord</summary>

Beide keren zie je: `100`

De data blijft bewaard in het bestand `test.db`, zelfs tussen programma runs!

</details>

### Opdracht 2: Run - Jouw eerste database

Maak een programma `contact.py` dat:
1. Een contact opslaat met naam en telefoonnummer
2. Het contact weer uitleest en print

Test het twee keer om te zien dat de data blijft bestaan!

### Opdracht 3: Investigate - Commit vergeten

**Vraag:** Wat gebeurt er als je `db.commit()` vergeet?

Probeer het uit:
```python
from sqlitedict import SqliteDict

with SqliteDict("test_commit.db") as db:
    db["test"] = "waarde"
    # db.commit()  # VERGETEN!

# Nu proberen uit te lezen
with SqliteDict("test_commit.db") as db:
    print(db.get("test", "Niet gevonden"))
```

<details>
<summary>Klik hier voor het antwoord</summary>

Je ziet: `Niet gevonden`

Zonder `db.commit()` worden de wijzigingen **niet opgeslagen**! De data blijft alleen in het geheugen en verdwijnt zodra het programma stopt.

**Altijd commit() gebruiken na wijzigingen!**

</details>

### Opdracht 4: Modify - Update data

Maak een programma dat:
1. Een score opslaat (bijv. 50)
2. De score uitleest
3. De score verhoogt met 10
4. De nieuwe score opslaat

Print de score elke keer.

<details>
<summary>Klik hier voor een hint</summary>

```python
from sqlitedict import SqliteDict

# Stap 1: Opslaan
with SqliteDict("game.db") as db:
    db["score"] = 50
    db.commit()
    print(f"Score opgeslagen: {db['score']}")

# Stap 2 & 3: Uitlezen en verhogen
with SqliteDict("game.db") as db:
    oude_score = db["score"]
    nieuwe_score = oude_score + 10
    db["score"] = nieuwe_score
    db.commit()
    print(f"Score verhoogd van {oude_score} naar {nieuwe_score}")
```

</details>

### Opdracht 5: Make - Contact lijst

Maak een programma `contacten.py` dat een contactenlijst beheert:

**Functie 1: Contact toevoegen**
```python
naam = input("Naam: ")
telefoon = input("Telefoon: ")
# Sla op in database
```

**Functie 2: Alle contacten tonen**
```python
# Loop door alle contacten en print ze
```

**Functie 3: Contact zoeken**
```python
zoek_naam = input("Zoek naam: ")
# Zoek en print het contact
```

<details>
<summary>Klik hier voor een voorbeeldoplossing</summary>

```python
from sqlitedict import SqliteDict

def voeg_contact_toe():
    naam = input("Naam: ")
    telefoon = input("Telefoon: ")

    with SqliteDict("contacten.db") as db:
        db[naam] = telefoon
        db.commit()

    print(f"✅ {naam} toegevoegd!")

def toon_alle_contacten():
    with SqliteDict("contacten.db") as db:
        if len(db) == 0:
            print("Geen contacten gevonden.")
            return

        print("\n--- Alle Contacten ---")
        for naam, telefoon in db.items():
            print(f"{naam}: {telefoon}")

def zoek_contact():
    zoek_naam = input("Zoek naam: ")

    with SqliteDict("contacten.db") as db:
        if zoek_naam in db:
            print(f"{zoek_naam}: {db[zoek_naam]}")
        else:
            print("Contact niet gevonden.")

# Menu
while True:
    print("\n1. Contact toevoegen")
    print("2. Alle contacten tonen")
    print("3. Contact zoeken")
    print("4. Stop")

    keuze = input("Kies: ")

    if keuze == "1":
        voeg_contact_toe()
    elif keuze == "2":
        toon_alle_contacten()
    elif keuze == "3":
        zoek_contact()
    elif keuze == "4":
        break
```

</details>

## Veelgemaakte fouten

### 1. Commit vergeten

```python
# ❌ FOUT
with SqliteDict("data.db") as db:
    db["key"] = "waarde"
    # Vergeten: db.commit()

# ✅ GOED
with SqliteDict("data.db") as db:
    db["key"] = "waarde"
    db.commit()  # Altijd!
```

### 2. Database niet sluiten

```python
# ❌ FOUT (kan problemen geven)
db = SqliteDict("data.db")
db["key"] = "waarde"
db.commit()
# Vergeten: db.close()

# ✅ GOED (with zorgt voor automatisch sluiten)
with SqliteDict("data.db") as db:
    db["key"] = "waarde"
    db.commit()
```

### 3. Key bestaat niet

```python
# ❌ FOUT (geeft error als key niet bestaat)
with SqliteDict("data.db") as db:
    print(db["naam"])  # KeyError!

# ✅ GOED (gebruik get met default waarde)
with SqliteDict("data.db") as db:
    print(db.get("naam", "Geen naam"))
```

## Samenvatting

Je hebt nu geleerd:
- ✅ sqlitedict installeren en importeren
- ✅ Data opslaan met `db[key] = value`
- ✅ Data uitlezen met `db[key]`
- ✅ Wijzigingen opslaan met `db.commit()`
- ✅ Data verwijderen met `del db[key]`
- ✅ Alle data bekijken met `db.items()`
- ✅ Checken of iets bestaat met `key in db`

**Belangrijkste regel: Vergeet nooit `db.commit()` na wijzigingen!**

In de volgende les leer je hoe je sqlitedict kunt combineren met FastAPI om formulierdata permanent op te slaan!
