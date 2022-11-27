# Übungen zu Redis

1. **Erstellen Sie einen Fork von diesem Repository!**
2. **Klonen Sie Ihren Fork, nicht das Original-Repository!**
3. **Reichen Sie Ihre Lösungen per Pull Request ein!**

Dokumentieren Sie Ihre Befehle in Dateien nach dem Format `aufgabe-X.txt`, wobei
`X` für die Nummer der Aufgabe steht. Dokumentieren Sie auch die Ausgaben Ihrer
Befehle! Verwenden Sie das Präfix `> ` für Befehlszeilen, um diese von den
Ausgaben unterscheidbar zu machen.

## Aufgabe 0: Mit Redis verbinden

Starten Sie Ihre Ubuntu-VM. Öffnen Sie ein Terminal und überprüfen Sie, ob Redis
läuft:

    $ systemctl status redis

Falls Sie `Active: active (running)` lesen, läuft Redis.

Andernfalls können Sie Redis folgendermassen starten:

    $ sudo systemctl start redis.service

Verbinden Sie sich nun mittels `redis-cli` mit der Datenbank:

    $ redis-cli
    127.0.0.1:6379>

Es sollte nun die Eingabeaufforderung `127.0.0.1:6379>` erscheinen.

Setzen Sie den Befehl `PING` ab, der `PONG` zurückgeben sollte:

    127.0.0.1:6379> PING
    PONG

Hilfe zu einem Befehl bekommen Sie mit dem `HELP`-Befehl:

    > HELP PING

      PING [message]
      summary: Ping the server
      …

Mit `[Ctrl]-[D]` oder `[Ctrl]-[C]` können Sie die Sitzung beenden.

## Aufgabe 1: Steckbrief abspeichern

Verwenden Sie den [`SET`](https://redis.io/commands/set/)-Befehl, um Ihren
persönlichen Steckbrief abzuspeichern. Speichern Sie die folgenden Angaben ab:

- Vorname
- Nachname
- Alter (in ganzen Jahren)
- Wohnort
- Lieblingsessen

Verwenden Sie anschliessend den
[`MSET`](https://redis.io/commands/mset/)-Befehl, um den persönlichen Steckbrief
einer weiteren Person, die sie kennen, in einem einzigen Befehl einzugeben.

Erhöhen Sie anschliessend mit dem
[`INCR`](https://redis.io/commands/incr/)-Befehl Ihr Alter um ein Jahr.

Verwenden Sie den [`KEYS`](https://redis.io/commands/keys/)-Befehl, um alle
Schlüssel anzuzeigen. Geben Sie anschliessend alle Werte mithilfe des
[`GET`](https://redis.io/commands/get/)-Befehls aus.

## Aufgabe 2: TODO-Liste

Erstellen Sie eine TODO-Liste mithilfe des
[`LPUSH`](https://redis.io/commands/lpush/)-Befehls. Definieren Sie mindestens
drei Aufgaben, die Sie erledigen müssen. (Falls die Aufgaben Leerzeichen
enthalten, umschliessen Sie den Text mit Anführungs- und Schlusszeichen (`"…"`).

Geben Sie nun die komplette Liste mithilfe des
[`LRANGE`](https://redis.io/commands/lrange/)-Befehls aus. (Tipp: Der Index ist
0-basierend, und -1 bezeichnet das letzte Listenelement.)

Angenommen, Sie hätten nun eine dieser TODO-Aufgaben erledigt: Entfernen Sie das
Element mithilfe des [`LREM`](https://redis.io/commands/lrange/)-Befehls aus der
Liste. Erstellen Sie nun eine zweite Liste mit erledigten Aufgaben, und fügen
Sie die erledigte Aufgabe dieser neuen Liste hinzu.

Verschieben Sie eine weitere erledigte Aufgabe mit dem
[`RPOPLPUSH`](https://redis.io/commands/rpoplpush/)-Befehl von der einen in die
andere Liste.

## Aufgabe 3: Mengenoperationen

Erstellen Sie ein Set (d.h. eine Menge) mithilfe des
[`SADD`](https://redis.io/commands/sadd/)-Befehls mit dem Namen `mammals`
(Säugetiere) und den folgenden Einträgen:

- Mensch
- Fledermaus
- Pferd
- Gorilla
- Meerschweinchen
- Kuh

Erstellen Sie nun ein weiteres Set mit dem Namen `fourlegged` (Vierbeiner) und
den folgenden Einträgen:

- Pferd
- Krokodil
- Meerschweinchen
- Kuh
- Eidechse 
- Leguan

Verwenden Sie nun die Befehle
[`SINTER`](https://redis.io/commands/sinter/),
[`SUNION`](https://redis.io/commands/sunion/) und
[`SDIFF`](https://redis.io/commands/sdiff/), um die folgenden Mengen zu
erstellen:

1. Säugetiere, die nicht vier Beine haben
2. Vierbeiner, die keine Säugetiere sind
3. Vierbeinige Säugetiere
4. Tiere, die vier Beine haben und/oder Säugetiere sind

## Aufgabe 4: Endpunkte als Hashes abspeichern

Betrachten Sie die Datei `config.csv` von der letzten Woche. Erstellen Sie pro
Zeile einen Hash, der alle Angaben enthält. Verwenden Sie hierzu den
[`HSET`](https://redis.io/commands/hset/)-Befehl. Verwenden Sie Schlüssel der
Form `endpoint:[identifier]`, also z.B. `endpoint:frickelbude`.

Stoppen Sie nun die Redis-Sitzung mit `[Ctrl]-[D]`. Exportieren Sie nun alle
Ihre Endpoints als JSON-Dateien (`endpoint-[identifier].json`), indem Sie den
Redis-Client mit dem Parameter `--json` und dem Aufruf des [`HGETALL`](https://redis.io/commands/hgetall/) kombinieren:

    $ redis-cli --json HGETALL endpoint:… > endpoint-[identifier].json

Speichern Sie diese JSON-Dateien ebenfalls im Repository ab.

## Zusatzaufgabe A: Weitere Befehle kennenlernen

Recherchieren Sie selbständig zu den folgenden
[Redis-Befehlen](https://redis.io/commands/) und überlegen Sie sich, wozu diese
sinnvoll sein könnten. (Die Befehle sind in Gruppen aufgelistet, weil sie
zusammenarbeiten.) Machen Sie konkrete Anwendungsbeispiele und dokumentieren
Sie diese in der Datei `zusatzaufgabe-a.txt`.

1. `SELECT`, `MOVE`, `FLUSHDB`
2. `EXPIRE`, `EXPIREAT`, `SETEX`, `TTL`, `PERSIST`
3. `MULTI`, `EXEC`, `DISCARD`
4. `BRPOP`, `BLPOP`, `LPUSH`, `RPUSH`

## Zusatzaufgabe B: Passwort vergeben

[Finden Sie heraus](https://redis.io/docs/getting-started/#install-redis), wie
man in der Konfigurationsdatei `/etc/redis/redis.conf` ein Passwort definiert.
Setzen Sie ein Passwort, und starten Sie Redis neu:

    $ sudo systemctl restart redis.service

Anschliessend funktioniert Redis nicht mehr ohne Authentifizierung:

    $ redis-cli
    127.0.0.1:6379> PING
    (error) NOAUTH Authentication required.

Finden Sie drei Wege, sich zu authentifizieren:

1. mit einem Redis-Befehl
2. mit einem Kommandozeilenparameter
3. mit einer Umgebungsvariable

Testen Sie die Authentifizierung jeweils mit dem `PING`-Befehl.

Tipp: Verwenden Sie `redis-cli --help` für Hilfestellungen.

Dokumentieren Sie alle Schritte in der Datei `zusatzaufgabe-b.txt`.

## Zusatzaufgabe C: Redis in der Cloud

Folgen Sie der [Videoanleitung](https://www.youtube.com/watch?v=LSRxm72evE0), um
eine kostenlose Redis-Datenbank in der Cloud zu bekommen. Verbinden Sie sich per
`redis-cli` mit dieser Datenbank. (Sie brauchen hier nichts zu dokumentieren.)

