---
title: 'Redis'
subtitle: 'Modul 346, BBZW'
author: 'Patrick Bucher'
---

# Redis

![Redis: **Re**mote **Di**ctionary **S**ervice](logo.png)

# Arten von Datenspeichern

- **Strukturierte** Daten: Relationale Datenbanken
    - [PostgreSQL](https://www.postgresql.org/)
    - [MySQL](https://www.mysql.com/)
    - [Microsoft SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-2019)
    - [Oracle](https://www.oracle.com/database/)
    - [sqlite](https://www.sqlite.org/index.html)
- **Unstrukturierte** Daten: Dateisystem, BLOB-Storage
    - [Amazon S3](https://aws.amazon.com/s3/)
        - [Minio](https://min.io/)
    - [Azure Blob Storage](https://azure.microsoft.com/en-us/products/storage/blobs/)
- **Halbstrukturierte** Daten: NoSQL-Datenbanken
    - [MongoDB](https://www.mongodb.com/)
    - [CouchDB](https://couchdb.apache.org/)
    - [InfluxDB](https://www.influxdata.com/)
    - [**Redis**](https://redis.io/)

# Redis speichert Datenstrukturen

Von **Go** kennen Sie (siehe [Datentypen](https://go.dev/ref/spec#Types)):

- Primitive Datentypen
- Slices/Arrays
- Maps
- Strukturen

**Redis** unterstützt u.a. (siehe [Datentypen](https://redis.io/docs/data-types/)):

- Strings
- Listen
- Hashes
- Sets

Mit Redis kann man **Datenstrukturen** abspeichern. Redis **ist** eine Map
(Dictionary).

# Art der Datenspeicherung

Redis kann Daten auf verschiedene Arten speichern:

- **RDB** (Redis Database): Datenbank als **Zustand** in einer Datei
    - Vorteile: kompakt, schnell, ideal für Backups
    - Nachteile: sichert Daten nur periodisch
- **AOF** (Append Only File): Datenbank als **Transaktionslog** in einer Datei
    - Vorteile: nachvollziehbar, sicher
    - Nachteile: grösser, langsamer
- **AOF** & **RDB**: Datenbank wird doppelt abgespeichert
    - Vorteile: sehr sicher
    - Nachteile: Performance schlechter, benötigt mehr Speicherplatz
- gar nicht (In-Memory-Datenbank): Datenbank wird nur im Memory gehalten
    - Voreteile: extrem schnell
    - Nachteile: Datenverlust bei Serviceunterbruch

# Installation

## Ubuntu

    $ sudo apt install redis
    $ sudo systemctl enable --now redis.service

Installiert und startet den Redis-Server mit Hilfswerkzeugen (z.B. `redis-cli`).

## Kostenloses [Cloud-Angebot](https://redis.com/try-free/)

- Registrierung mit E-Mail-Adresse, Google- oder GitHub-Account
- lokales `redis-cli` wird weiterhin benötigt

## Online-Demo von [Railway.app](https://railway.app/new)

- grafisches Interface

# Verwendung

    $ redis-cli
    127.0.0.1:6379> PING
    PONG
    127.0.0.1:6379> SET name John
    OK
    127.0.0.1:6379> KEYS *
    1) "name"
    127.0.0.1:6379> GET name
    "John"
    127.0.0.1:6379> DEL name
    (integer) 1
    127.0.0.1:6379> EXISTS name
    (integer) 0

# Befehle

Redis kennt über 400 [Befehle](https://redis.io/commands/) (Stand: 27.11.2022).

- Das Präfix richtet sich nach der Datenstruktur, auf welcher der Befehl operiert:
    - List: `L` bzw. `R` für Operationen am linken bzw. rechten Listenende
    - Sets: `S`
    - Hashes: `H`
    - Sorted Sets: `Z`
- Die Befehle haben keine, einen oder mehrere (teils optionale) Parameter:
    - `FLUSHALL`: keine Parameter
    - `GET key`: ein Parameter
    - `SET key value`: zwei Parameter

# Befehle: Grundlegende Verwendung

- [`PING`](https://redis.io/commands/ping/): Verbindung testen (gibt `PONG` aus,
  wenn Verbindung steht)
- `HELP`: Hilfe ausgeben, z.B. zu einem Befehl
    - `HELP PING`
- [`AUTH`](https://redis.io/commands/auth/): Interaktive Authentifizierung mit Passwort
- [`FLUSHALL`](https://redis.io/commands/flushall/): Löscht **alle** Einträge
- [`KEYS`](https://redis.io/commands/flushall/): Schlüssel gemäss Muster anzeigen
    - `KEYS *`: listet alle Schlüssel auf
- [`EXISTS`](https://redis.io/commands/exists/): Prüft, ob ein Schlüssel existiert
- [`TYPE`](https://redis.io/commands/exists/): Gibt den Datentyp des Werts von
  einem Schlüssel aus
- [`SAVE`](https://redis.io/commands/save/): Persistente Speicherung forcieren

# Befehle: Einfache Werte

- [`SET`](https://redis.io/commands/set/): Ein Schlüssel/Wert-Paar definieren
    - [`MSET`](https://redis.io/commands/mset/): Mehrere Schlüssel/Wert-Paare
      gleichzeitig definieren
- [`GET`](https://redis.io/commands/get/): Wert anhand eines Schlüssels auslesen
- [`DEL`](https://redis.io/commands/del/): Eintrag entfernen
- [`RENAME`](https://redis.io/commands/rename/): Schlüssel umbenennen

# Strukturierte Schlüsselnamen

Schlüsselnamen können gemäss einer Konvention strukturiert werden:

    SET lucerne.name Luzern
    SET lucerne.population 81592

    SET employee:1234:name Dilbert
    SET employee:1234:position Engineer

# Strukturierte Schlüsselnamen: `meow`-Endpoints

    SET endpoint.frickelbude.identifier frickelbude
    SET endpoint.frickelbude.url http://code.frickelbude.ch/api/v1/version
    SET endpoint.frickelbude.method GET
    SET endpoint.frickelbude.status 200
    SET endpoint.frickelbude.frequency 5m
    SET endpoint.frickelbude.failafter 3

Schlüssel zum gleichen "Feld" auslesen:

    KEYS endpoint.*.url

Schlüssel zu gleichen "Datensatz" auslesen:

    KEYS endpoint.frickelbude.*

# Befehle: Hashes

Ein **Hash** speichert Schlüssel/Wert-Paare ab und ist mit einer `map` oder
`struct` in Go vergleichbar, erlaubt aber keine Verschachtelung.

- [`HSET`](https://redis.io/commands/hset/): Definiert einen Hash mit
  Schlüssel/Wert-Paaren
- [`HGET`](https://redis.io/commands/hget/): Gibt ein Feld zu einem Hash
  aus
- [`HGETALL`](https://redis.io/commands/HGETALL/): Gibt alle Feldnamen und
  -Werte zu einem Hash aus
- [`HKEYS`](https://redis.io/commands/hkeys/): Gibt die Feldnamen zu einem
  Hash aus
- [`HVALS`](https://redis.io/commands/hvals/): Gibt die Werte zu einem Hash
  aus
- [`HDEL`](https://redis.io/commands/hdel/): Löscht ein Feld von einem Hash
  (aber nicht den Hash selber)
- [`HSETNX`](https://redis.io/commands/hsetnx/): Setzt ein Feld von einem Hash,
  sofern es noch nicht definiert ist

Die Struktur von einem zusammengesetzten Objekt muss nicht über den Namen
codiert werden.

# Hashes: `meow`-Endpoints

    HSET endpoint.canary
        identifier canary
        url http://localhost:8000/canary
        method GET
        status 200
        frequency 5s
        failafter 4

    HGET endpoint.canary url
    "http://localhost:8000/canary"

    KEYS endpoint.*
    1) "endpoint.canary"

# Bonus: Ausgabe von CSV und JSON

    $ redis-cli --csv HGETALL endpoint.canary
    "identifier","canary","url","http://localhost:8000/canary","method","GET",
    "status","200","frequency","5s","failafter","4"

    $ redis-cli --json HGETALL endpoint.canary

```json
{
  "identifier": "canary",
  "url": "http://localhost:8000/canary",
  "method": "GET",
  "status": "200",
  "frequency": "5s",
  "failafter": "4"
}
```

# Links

- [Redis.io](https://redis.io/): OpenSource-Software
    - [Documentation](https://redis.io/docs/): Offizielle Dokumentation
    - [Commands](https://redis.io/commands/): Befehlsübersicht 
    - [Redis Data Types](https://redis.io/docs/data-types/): Datentypen
    - [Redis CLI](https://redis.io/docs/manual/cli/): Kommandozeileninterface
    - [Clients](https://redis.io/resources/clients/): Sprachanbindungen
        - [Go Redis](https://github.com/go-redis/redis): Client-Library für Go
- [Redis.com](https://redis.com/): kommerzielles Angebot
    - [Redis Enterprise Cloud](https://redis.com/redis-enterprise-cloud/overview/): Cloud-Angebot
    - [Try Free](https://redis.com/try-free/): Kostenloses Cloud-Angebot zum Einstieg
