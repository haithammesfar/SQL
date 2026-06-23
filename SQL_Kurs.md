# SQL Zusammenfassung – Teil 1: DDL und DML

## 1. Überblick über SQL-Befehlsgruppen

SQL-Befehle werden oft in verschiedene Kategorien eingeteilt:

| Kategorie | Bedeutung                    | Beispiele                    |
| --------- | ---------------------------- | ---------------------------- |
| DDL       | Data Definition Language     | `CREATE`, `ALTER`, `DROP`    |
| DML       | Data Manipulation Language   | `INSERT`, `UPDATE`, `DELETE` |
| DQL       | Data Query Language          | `SELECT`                     |
| TCL       | Transaction Control Language | `COMMIT`, `ROLLBACK`         |
| DCL       | Data Control Language        | `GRANT`, `REVOKE`            |

In diesem Teil geht es um **DDL** und **DML**.

---

## 2. DDL – Data Definition Language

DDL steht für **Data Definition Language**.

DDL-Befehle werden verwendet, um die Struktur einer Datenbank zu erstellen, zu ändern oder zu löschen.

Wichtige DDL-Befehle:

* `CREATE`
* `ALTER`
* `DROP`
* zusätzlich häufig: `TRUNCATE`

---

## 2.1 CREATE

Mit `CREATE` erstellt man neue Datenbankobjekte, zum Beispiel:

* Datenbanken
* Tabellen
* Views
* Indexe

### Allgemeine Struktur

```sql
CREATE TABLE tabellenname (
    spalte1 datentyp constraint,
    spalte2 datentyp constraint,
    ...
);
```

### Beispiel

```sql
CREATE TABLE kunden (
    kunden_id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE,
    geburtsdatum DATE,
    erstellt_am TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Wichtige Bestandteile

| Bestandteil         | Bedeutung                   |
| ------------------- | --------------------------- |
| `CREATE TABLE`      | Erstellt eine neue Tabelle  |
| `kunden`            | Name der Tabelle            |
| `kunden_id INT`     | Spalte mit Datentyp         |
| `PRIMARY KEY`       | Eindeutiger Primärschlüssel |
| `NOT NULL`          | Wert darf nicht leer sein   |
| `UNIQUE`            | Wert muss eindeutig sein    |
| `DEFAULT`           | Standardwert                |
| `CURRENT_TIMESTAMP` | Aktuelles Datum und Uhrzeit |

---

## 2.2 Wichtige Constraints

Constraints sind Regeln für Spalten oder Tabellen.

| Constraint    | Bedeutung                                    |
| ------------- | -------------------------------------------- |
| `PRIMARY KEY` | Eindeutige Identifikation eines Datensatzes  |
| `FOREIGN KEY` | Verbindung zu einer anderen Tabelle          |
| `NOT NULL`    | Spalte darf nicht leer sein                  |
| `UNIQUE`      | Werte müssen eindeutig sein                  |
| `CHECK`       | Bedingung für Werte                          |
| `DEFAULT`     | Standardwert, falls kein Wert angegeben wird |

### Beispiel mit Foreign Key

```sql
CREATE TABLE bestellungen (
    bestellung_id INT PRIMARY KEY,
    kunden_id INT,
    bestelldatum DATE NOT NULL,
    FOREIGN KEY (kunden_id) REFERENCES kunden(kunden_id)
);
```

---

## 2.3 ALTER

Mit `ALTER` verändert man die Struktur einer bestehenden Tabelle.

Typische Aktionen:

* Spalte hinzufügen
* Spalte ändern
* Spalte löschen
* Constraint hinzufügen
* Constraint löschen

---

### Spalte hinzufügen

```sql
ALTER TABLE kunden
ADD telefon VARCHAR(20);
```

---

### Spalte ändern

Je nach SQL-Dialekt ist die Syntax unterschiedlich.

#### MySQL

```sql
ALTER TABLE kunden
MODIFY telefon VARCHAR(30);
```

#### PostgreSQL

```sql
ALTER TABLE kunden
ALTER COLUMN telefon TYPE VARCHAR(30);
```

---

### Spalte löschen

```sql
ALTER TABLE kunden
DROP COLUMN telefon;
```

---

### Constraint hinzufügen

```sql
ALTER TABLE kunden
ADD CONSTRAINT unique_email UNIQUE (email);
```

---

### Constraint löschen

Die Syntax hängt vom Datenbanksystem ab.

```sql
ALTER TABLE kunden
DROP CONSTRAINT unique_email;
```

---

## 2.4 DROP

Mit `DROP` löscht man Datenbankobjekte dauerhaft.

### Tabelle löschen

```sql
DROP TABLE kunden;
```

### Tabelle nur löschen, wenn sie existiert

```sql
DROP TABLE IF EXISTS kunden;
```

### Datenbank löschen

```sql
DROP DATABASE datenbankname;
```

### Wichtige Hinweise

`DROP` löscht die Struktur und die Daten dauerhaft.

Wenn andere Tabellen über Foreign Keys verbunden sind, kann das Löschen blockiert werden.

Häufige Optionen:

```sql
DROP TABLE kunden CASCADE;
```

oder

```sql
DROP TABLE kunden RESTRICT;
```

| Option     | Bedeutung                                              |
| ---------- | ------------------------------------------------------ |
| `CASCADE`  | Löscht abhängige Objekte mit                           |
| `RESTRICT` | Verhindert das Löschen, wenn Abhängigkeiten existieren |

---

## 2.5 TRUNCATE

`TRUNCATE` löscht alle Datensätze aus einer Tabelle, aber die Tabellenstruktur bleibt erhalten.

```sql
TRUNCATE TABLE kunden;
```

### Unterschied zu DELETE

| Befehl     | Wirkung                                     |
| ---------- | ------------------------------------------- |
| `DELETE`   | Löscht ausgewählte oder alle Datensätze     |
| `TRUNCATE` | Löscht alle Datensätze schneller            |
| `DROP`     | Löscht die ganze Tabelle inklusive Struktur |

---

## 3. DML – Data Manipulation Language

DML steht für **Data Manipulation Language**.

DML-Befehle werden verwendet, um Daten in Tabellen einzufügen, zu ändern oder zu löschen.

Wichtige DML-Befehle:

* `INSERT`
* `UPDATE`
* `DELETE`

---

## 3.1 INSERT

Mit `INSERT` fügt man neue Datensätze in eine Tabelle ein.

### Allgemeine Struktur

```sql
INSERT INTO tabellenname (spalte1, spalte2, spalte3)
VALUES (wert1, wert2, wert3);
```

### Beispiel

```sql
INSERT INTO kunden (kunden_id, name, email)
VALUES (1, 'Ali', 'ali@example.com');
```

---

### Mehrere Datensätze einfügen

```sql
INSERT INTO kunden (kunden_id, name, email)
VALUES 
(1, 'Ali', 'ali@example.com'),
(2, 'Sara', 'sara@example.com'),
(3, 'Omar', 'omar@example.com');
```

---

### INSERT mit SELECT

Daten können auch aus einer anderen Tabelle übernommen werden.

```sql
INSERT INTO kunden_archiv (kunden_id, name, email)
SELECT kunden_id, name, email
FROM kunden
WHERE erstellt_am < '2024-01-01';
```

---

## 3.2 UPDATE

Mit `UPDATE` ändert man bestehende Datensätze.

### Allgemeine Struktur

```sql
UPDATE tabellenname
SET spalte1 = wert1,
    spalte2 = wert2
WHERE bedingung;
```

### Beispiel

```sql
UPDATE kunden
SET email = 'neu@example.com'
WHERE kunden_id = 1;
```

### Wichtig

Ohne `WHERE` werden alle Datensätze geändert.

```sql
UPDATE kunden
SET email = 'test@example.com';
```

Dieser Befehl setzt die E-Mail-Adresse für alle Kunden auf denselben Wert.

---

## 3.3 DELETE

Mit `DELETE` löscht man Datensätze aus einer Tabelle.

### Allgemeine Struktur

```sql
DELETE FROM tabellenname
WHERE bedingung;
```

### Beispiel

```sql
DELETE FROM kunden
WHERE kunden_id = 1;
```

### Wichtig

Ohne `WHERE` werden alle Datensätze gelöscht.

```sql
DELETE FROM kunden;
```

Die Tabellenstruktur bleibt dabei erhalten.

---

## 4. Wichtige Clauses und Schlüsselwörter

## 4.1 WHERE

`WHERE` filtert Datensätze.

```sql
SELECT *
FROM kunden
WHERE name = 'Ali';
```

Bei `UPDATE` und `DELETE` ist `WHERE` besonders wichtig, damit nicht alle Datensätze betroffen sind.

---

## 4.2 IF EXISTS / IF NOT EXISTS

Diese Klauseln verhindern Fehlermeldungen.

### CREATE TABLE IF NOT EXISTS

```sql
CREATE TABLE IF NOT EXISTS kunden (
    kunden_id INT PRIMARY KEY,
    name VARCHAR(100)
);
```

### DROP TABLE IF EXISTS

```sql
DROP TABLE IF EXISTS kunden;
```

---

## 4.3 CASCADE und RESTRICT

Diese Optionen werden häufig bei `DROP` oder Foreign Keys verwendet.

```sql
DROP TABLE kunden CASCADE;
```

```sql
DROP TABLE kunden RESTRICT;
```

---

## 4.4 RETURNING

In manchen Datenbanksystemen, zum Beispiel PostgreSQL, kann man mit `RETURNING` geänderte oder eingefügte Daten direkt zurückgeben.

### INSERT mit RETURNING

```sql
INSERT INTO kunden (kunden_id, name)
VALUES (1, 'Ali')
RETURNING *;
```

### UPDATE mit RETURNING

```sql
UPDATE kunden
SET name = 'Mohamed'
WHERE kunden_id = 1
RETURNING *;
```

### DELETE mit RETURNING

```sql
DELETE FROM kunden
WHERE kunden_id = 1
RETURNING *;
```

---

## 5. Vergleich: DROP, DELETE und TRUNCATE

| Befehl     | Löscht Daten?  | Löscht Struktur? | WHERE möglich? |
| ---------- | -------------- | ---------------- | -------------- |
| `DELETE`   | Ja             | Nein             | Ja             |
| `TRUNCATE` | Ja, alle Daten | Nein             | Nein           |
| `DROP`     | Ja             | Ja               | Nein           |

---

## 6. Wichtige Merksätze

* `CREATE` erstellt neue Datenbankobjekte.
* `ALTER` verändert bestehende Tabellenstrukturen.
* `DROP` löscht Tabellen oder Datenbanken komplett.
* `INSERT` fügt neue Datensätze ein.
* `UPDATE` verändert bestehende Datensätze.
* `DELETE` löscht Datensätze.
* Bei `UPDATE` und `DELETE` sollte man fast immer eine `WHERE`-Bedingung verwenden.
* `DROP` löscht Struktur und Daten.
* `DELETE` löscht Daten, aber nicht die Tabelle.
* `TRUNCATE` löscht alle Daten schneller als `DELETE`, aber ohne `WHERE`.

---

## 7. Mini-Beispiel

```sql
CREATE TABLE kunden (
    kunden_id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE
);

INSERT INTO kunden (kunden_id, name, email)
VALUES (1, 'Ali', 'ali@example.com');

UPDATE kunden
SET email = 'ali.neu@example.com'
WHERE kunden_id = 1;

DELETE FROM kunden
WHERE kunden_id = 1;

DROP TABLE kunden;
```

# SQL Zusammenfassung – Teil 2: WHERE Operators

## 1. Was ist `WHERE`?

Die `WHERE`-Klausel wird verwendet, um Datensätze nach bestimmten Bedingungen zu filtern.

Sie wird häufig zusammen mit folgenden SQL-Befehlen verwendet:

* `SELECT`
* `UPDATE`
* `DELETE`

### Allgemeine Struktur

```sql
SELECT spalte1, spalte2
FROM tabellenname
WHERE bedingung;
```

### Beispiel

```sql
SELECT *
FROM kunden
WHERE stadt = 'Düsseldorf';
```

In diesem Beispiel werden nur Kunden angezeigt, deren Stadt `Düsseldorf` ist.

---

## 2. Vergleichsoperatoren

Vergleichsoperatoren vergleichen Werte miteinander.

| Operator | Bedeutung           | Beispiel            |
| -------- | ------------------- | ------------------- |
| `=`      | gleich              | `stadt = 'Berlin'`  |
| `<>`     | ungleich            | `stadt <> 'Berlin'` |
| `!=`     | ungleich            | `stadt != 'Berlin'` |
| `>`      | größer als          | `alter > 18`        |
| `>=`     | größer oder gleich  | `alter >= 18`       |
| `<`      | kleiner als         | `preis < 100`       |
| `<=`     | kleiner oder gleich | `preis <= 100`      |

### Beispiel mit `=`

```sql
SELECT *
FROM kunden
WHERE land = 'Deutschland';
```

### Beispiel mit `<>`

```sql
SELECT *
FROM kunden
WHERE land <> 'Deutschland';
```

### Beispiel mit `>`

```sql
SELECT *
FROM produkte
WHERE preis > 50;
```

---

## 3. Logische Operatoren

Logische Operatoren verbinden mehrere Bedingungen miteinander.

| Operator | Bedeutung                                |
| -------- | ---------------------------------------- |
| `AND`    | Alle Bedingungen müssen wahr sein        |
| `OR`     | Mindestens eine Bedingung muss wahr sein |
| `NOT`    | Kehrt eine Bedingung um                  |

---

## 3.1 AND

Mit `AND` müssen alle Bedingungen erfüllt sein.

```sql
SELECT *
FROM kunden
WHERE stadt = 'Düsseldorf'
AND alter >= 18;
```

Dieser Befehl zeigt nur Kunden aus Düsseldorf, die mindestens 18 Jahre alt sind.

---

## 3.2 OR

Mit `OR` muss mindestens eine Bedingung erfüllt sein.

```sql
SELECT *
FROM kunden
WHERE stadt = 'Düsseldorf'
OR stadt = 'Köln';
```

Dieser Befehl zeigt Kunden aus Düsseldorf oder Köln.

---

## 3.3 NOT

Mit `NOT` wird eine Bedingung umgekehrt.

```sql
SELECT *
FROM kunden
WHERE NOT stadt = 'Berlin';
```

Das ist ähnlich wie:

```sql
SELECT *
FROM kunden
WHERE stadt <> 'Berlin';
```

---

## 3.4 Klammern bei mehreren Bedingungen

Wenn mehrere Bedingungen kombiniert werden, sollten Klammern verwendet werden, damit die Logik eindeutig ist.

```sql
SELECT *
FROM kunden
WHERE (stadt = 'Düsseldorf' OR stadt = 'Köln')
AND alter >= 18;
```

Ohne Klammern kann das Ergebnis anders sein, weil SQL eine feste Reihenfolge bei Operatoren hat.

### Reihenfolge der logischen Operatoren

| Priorität | Operator |
| --------- | -------- |
| 1         | `NOT`    |
| 2         | `AND`    |
| 3         | `OR`     |

---

## 4. Range Operator: BETWEEN

`BETWEEN` prüft, ob ein Wert innerhalb eines Bereichs liegt.

### Allgemeine Struktur

```sql
SELECT *
FROM tabellenname
WHERE spalte BETWEEN wert1 AND wert2;
```

### Beispiel mit Zahlen

```sql
SELECT *
FROM produkte
WHERE preis BETWEEN 50 AND 100;
```

Dieser Befehl zeigt Produkte mit einem Preis zwischen 50 und 100.

Wichtig: `BETWEEN` schließt beide Grenzen ein.

Das bedeutet:

```sql
preis BETWEEN 50 AND 100
```

ist ähnlich wie:

```sql
preis >= 50 AND preis <= 100
```

---

### Beispiel mit Datum

```sql
SELECT *
FROM bestellungen
WHERE bestelldatum BETWEEN '2024-01-01' AND '2024-12-31';
```

Dieser Befehl zeigt Bestellungen aus dem Jahr 2024.

---

## 5. Membership Operator: IN

`IN` prüft, ob ein Wert in einer Liste von Werten enthalten ist.

### Allgemeine Struktur

```sql
SELECT *
FROM tabellenname
WHERE spalte IN (wert1, wert2, wert3);
```

### Beispiel

```sql
SELECT *
FROM kunden
WHERE stadt IN ('Düsseldorf', 'Köln', 'Berlin');
```

Dieser Befehl zeigt Kunden, die in Düsseldorf, Köln oder Berlin wohnen.

Das ist kürzer als:

```sql
SELECT *
FROM kunden
WHERE stadt = 'Düsseldorf'
OR stadt = 'Köln'
OR stadt = 'Berlin';
```

---

## 6. NOT IN

`NOT IN` prüft, ob ein Wert nicht in einer Liste enthalten ist.

```sql
SELECT *
FROM kunden
WHERE stadt NOT IN ('Berlin', 'Hamburg');
```

Dieser Befehl zeigt Kunden, die nicht in Berlin oder Hamburg wohnen.

---

## 7. Search Operator: LIKE

`LIKE` wird verwendet, um nach Mustern in Texten zu suchen.

Dabei verwendet man Platzhalter.

| Platzhalter | Bedeutung              |
| ----------- | ---------------------- |
| `%`         | beliebig viele Zeichen |
| `_`         | genau ein Zeichen      |

---

### Beispiel: beginnt mit einem bestimmten Text

```sql
SELECT *
FROM kunden
WHERE name LIKE 'A%';
```

Dieser Befehl zeigt alle Kunden, deren Name mit `A` beginnt.

Beispiele:

* Ali
* Ahmed
* Anna

---

### Beispiel: endet mit einem bestimmten Text

```sql
SELECT *
FROM kunden
WHERE name LIKE '%a';
```

Dieser Befehl zeigt alle Kunden, deren Name mit `a` endet.

---

### Beispiel: enthält einen bestimmten Text

```sql
SELECT *
FROM kunden
WHERE email LIKE '%gmail%';
```

Dieser Befehl zeigt alle Kunden, deren E-Mail-Adresse `gmail` enthält.

---

### Beispiel mit `_`

```sql
SELECT *
FROM kunden
WHERE name LIKE 'A_i';
```

Mögliche Treffer:

* Ali
* Ami

`_` steht hier für genau ein Zeichen.

---

## 8. NOT LIKE

`NOT LIKE` sucht nach Datensätzen, die einem Muster nicht entsprechen.

```sql
SELECT *
FROM kunden
WHERE email NOT LIKE '%gmail%';
```

Dieser Befehl zeigt alle Kunden, deren E-Mail-Adresse nicht `gmail` enthält.

---

## 9. IS NULL und IS NOT NULL

Für `NULL`-Werte verwendet man nicht `=` oder `<>`.

Falsch:

```sql
SELECT *
FROM kunden
WHERE telefon = NULL;
```

Richtig:

```sql
SELECT *
FROM kunden
WHERE telefon IS NULL;
```

### IS NOT NULL

```sql
SELECT *
FROM kunden
WHERE telefon IS NOT NULL;
```

Dieser Befehl zeigt alle Kunden, bei denen eine Telefonnummer vorhanden ist.

---

## 10. EXISTS

`EXISTS` prüft, ob eine Unterabfrage mindestens ein Ergebnis liefert.

### Beispiel

```sql
SELECT *
FROM kunden k
WHERE EXISTS (
    SELECT 1
    FROM bestellungen b
    WHERE b.kunden_id = k.kunden_id
);
```

Dieser Befehl zeigt alle Kunden, die mindestens eine Bestellung haben.

---

## 11. Wichtige Hinweise

* Textwerte werden in einfache Anführungszeichen geschrieben: `'Berlin'`
* Zahlen brauchen keine Anführungszeichen: `alter >= 18`
* Datumswerte werden meistens in einfache Anführungszeichen geschrieben: `'2024-01-01'`
* Bei `UPDATE` und `DELETE` ist `WHERE` besonders wichtig.
* Ohne `WHERE` betrifft `UPDATE` oder `DELETE` alle Datensätze.
* Für `NULL` immer `IS NULL` oder `IS NOT NULL` verwenden.
* Bei mehreren Bedingungen sollten Klammern verwendet werden.

---

## 12. Beispiele mit UPDATE und DELETE

### UPDATE mit WHERE

```sql
UPDATE kunden
SET stadt = 'Köln'
WHERE kunden_id = 1;
```

Dieser Befehl ändert nur den Kunden mit der ID `1`.

---

### DELETE mit WHERE

```sql
DELETE FROM kunden
WHERE kunden_id = 1;
```

Dieser Befehl löscht nur den Kunden mit der ID `1`.

---

## 13. Mini-Übersicht

| Kategorie      | Operatoren                            |
| -------------- | ------------------------------------- |
| Vergleich      | `=`, `<>`, `!=`, `>`, `>=`, `<`, `<=` |
| Logisch        | `AND`, `OR`, `NOT`                    |
| Bereich        | `BETWEEN`                             |
| Mitgliedschaft | `IN`, `NOT IN`                        |
| Suche          | `LIKE`, `NOT LIKE`                    |
| NULL-Prüfung   | `IS NULL`, `IS NOT NULL`              |
| Unterabfrage   | `EXISTS`, `NOT EXISTS`                |

---

## 14. Mini-Beispiel

```sql
SELECT *
FROM kunden
WHERE (stadt IN ('Düsseldorf', 'Köln') OR land = 'Deutschland')
AND alter BETWEEN 18 AND 35
AND email LIKE '%gmail%'
AND telefon IS NOT NULL;
```

Dieser Befehl zeigt Kunden, die:

* in Düsseldorf oder Köln wohnen oder aus Deutschland kommen,
* zwischen 18 und 35 Jahre alt sind,
* eine Gmail-Adresse haben,
* und eine Telefonnummer gespeichert haben.

```
```

# SQL Zusammenfassung – Teil 3: JOINs

## 1. Was sind JOINs?

`JOINs` werden verwendet, um Daten aus mehreren Tabellen miteinander zu verbinden.

Das ist besonders wichtig, wenn Informationen auf mehrere Tabellen verteilt sind.

Beispiel:

* Tabelle `kunden` enthält Kundendaten.
* Tabelle `bestellungen` enthält Bestellungen.
* Über `kunden_id` können beide Tabellen verbunden werden.

---

## 2. Beispieltabellen

### Tabelle: kunden

| kunden_id | name | stadt      |
| --------- | ---- | ---------- |
| 1         | Ali  | Düsseldorf |
| 2         | Sara | Köln       |
| 3         | Omar | Berlin     |
| 4         | Lina | Hamburg    |

### Tabelle: bestellungen

| bestellung_id | kunden_id | produkt  |
| ------------- | --------- | -------- |
| 101           | 1         | Laptop   |
| 102           | 1         | Maus     |
| 103           | 2         | Tastatur |
| 104           | 5         | Monitor  |

---

## 3. Grundstruktur eines JOINs

```sql
SELECT spalten
FROM tabelle1
JOIN tabelle2
ON tabelle1.spalte = tabelle2.spalte;
```

### Beispiel

```sql
SELECT kunden.name, bestellungen.produkt
FROM kunden
JOIN bestellungen
ON kunden.kunden_id = bestellungen.kunden_id;
```

---

## 4. Tabellen-Aliase

Bei JOINs verwendet man oft Aliase, damit der Code kürzer und lesbarer wird.

```sql
SELECT k.name, b.produkt
FROM kunden k
JOIN bestellungen b
ON k.kunden_id = b.kunden_id;
```

| Alias | Bedeutung                        |
| ----- | -------------------------------- |
| `k`   | Alias für Tabelle `kunden`       |
| `b`   | Alias für Tabelle `bestellungen` |

---

# 5. INNER JOIN

Ein `INNER JOIN` gibt nur Datensätze zurück, bei denen es in beiden Tabellen eine Übereinstimmung gibt.

### Struktur

```sql
SELECT spalten
FROM tabelle1
INNER JOIN tabelle2
ON tabelle1.spalte = tabelle2.spalte;
```

### Beispiel

```sql
SELECT k.kunden_id, k.name, b.produkt
FROM kunden k
INNER JOIN bestellungen b
ON k.kunden_id = b.kunden_id;
```

### Ergebnis

| kunden_id | name | produkt  |
| --------- | ---- | -------- |
| 1         | Ali  | Laptop   |
| 1         | Ali  | Maus     |
| 2         | Sara | Tastatur |

### Erklärung

* Ali erscheint zweimal, weil er zwei Bestellungen hat.
* Sara erscheint einmal, weil sie eine Bestellung hat.
* Omar und Lina erscheinen nicht, weil sie keine Bestellung haben.
* Die Bestellung mit `kunden_id = 5` erscheint nicht, weil es keinen passenden Kunden gibt.

---

# 6. LEFT JOIN

Ein `LEFT JOIN` gibt alle Datensätze aus der linken Tabelle zurück.

Wenn es keine passende Zeile in der rechten Tabelle gibt, werden dort `NULL`-Werte angezeigt.

### Struktur

```sql
SELECT spalten
FROM tabelle1
LEFT JOIN tabelle2
ON tabelle1.spalte = tabelle2.spalte;
```

### Beispiel

```sql
SELECT k.kunden_id, k.name, b.produkt
FROM kunden k
LEFT JOIN bestellungen b
ON k.kunden_id = b.kunden_id;
```

### Ergebnis

| kunden_id | name | produkt  |
| --------- | ---- | -------- |
| 1         | Ali  | Laptop   |
| 1         | Ali  | Maus     |
| 2         | Sara | Tastatur |
| 3         | Omar | NULL     |
| 4         | Lina | NULL     |

### Erklärung

* Alle Kunden werden angezeigt.
* Kunden ohne Bestellung bekommen bei `produkt` den Wert `NULL`.

---

# 7. RIGHT JOIN

Ein `RIGHT JOIN` gibt alle Datensätze aus der rechten Tabelle zurück.

Wenn es keine passende Zeile in der linken Tabelle gibt, werden dort `NULL`-Werte angezeigt.

### Struktur

```sql
SELECT spalten
FROM tabelle1
RIGHT JOIN tabelle2
ON tabelle1.spalte = tabelle2.spalte;
```

### Beispiel

```sql
SELECT k.kunden_id, k.name, b.produkt
FROM kunden k
RIGHT JOIN bestellungen b
ON k.kunden_id = b.kunden_id;
```

### Erklärung

* Alle Bestellungen werden angezeigt.
* Auch Bestellungen ohne passenden Kunden werden angezeigt.
* Fehlende Kundendaten erscheinen als `NULL`.

---

# 8. FULL OUTER JOIN

Ein `FULL OUTER JOIN` gibt alle Datensätze aus beiden Tabellen zurück.

Wenn es keine Übereinstimmung gibt, werden die fehlenden Werte mit `NULL` gefüllt.

### Struktur

```sql
SELECT spalten
FROM tabelle1
FULL OUTER JOIN tabelle2
ON tabelle1.spalte = tabelle2.spalte;
```

### Beispiel

```sql
SELECT k.kunden_id, k.name, b.produkt
FROM kunden k
FULL OUTER JOIN bestellungen b
ON k.kunden_id = b.kunden_id;
```

### Erklärung

Der `FULL OUTER JOIN` zeigt:

* Kunden mit Bestellungen
* Kunden ohne Bestellungen
* Bestellungen ohne passenden Kunden

### Hinweis

Nicht jedes Datenbanksystem unterstützt `FULL OUTER JOIN` direkt.

Zum Beispiel unterstützt MySQL `FULL OUTER JOIN` nicht direkt.

Eine mögliche Alternative ist eine Kombination aus `LEFT JOIN` und `RIGHT JOIN` mit `UNION`.

```sql
SELECT k.kunden_id, k.name, b.produkt
FROM kunden k
LEFT JOIN bestellungen b
ON k.kunden_id = b.kunden_id

UNION

SELECT k.kunden_id, k.name, b.produkt
FROM kunden k
RIGHT JOIN bestellungen b
ON k.kunden_id = b.kunden_id;
```

---

# 9. ANTI JOIN

Ein `ANTI JOIN` zeigt Datensätze aus einer Tabelle, für die es keine passenden Datensätze in einer anderen Tabelle gibt.

SQL hat meistens keinen direkten Befehl namens `ANTI JOIN`.

Man erreicht das häufig mit:

* `LEFT JOIN ... IS NULL`
* `NOT EXISTS`
* `NOT IN`

---

## 9.1 Anti Join mit LEFT JOIN

### Beispiel: Kunden ohne Bestellung

```sql
SELECT k.kunden_id, k.name
FROM kunden k
LEFT JOIN bestellungen b
ON k.kunden_id = b.kunden_id
WHERE b.kunden_id IS NULL;
```

### Erklärung

* Zuerst werden alle Kunden mit `LEFT JOIN` angezeigt.
* Kunden ohne Bestellung haben in der Bestellungstabelle `NULL`.
* Mit `WHERE b.kunden_id IS NULL` filtert man genau diese Kunden heraus.

---

## 9.2 Anti Join mit NOT EXISTS

```sql
SELECT k.kunden_id, k.name
FROM kunden k
WHERE NOT EXISTS (
    SELECT 1
    FROM bestellungen b
    WHERE b.kunden_id = k.kunden_id
);
```

### Erklärung

Dieser Befehl zeigt alle Kunden, für die keine passende Bestellung existiert.

`NOT EXISTS` ist oft eine saubere und sichere Lösung für Anti Joins.

---

## 9.3 Anti Join mit NOT IN

```sql
SELECT k.kunden_id, k.name
FROM kunden k
WHERE k.kunden_id NOT IN (
    SELECT b.kunden_id
    FROM bestellungen b
);
```

### Wichtiger Hinweis

Bei `NOT IN` muss man vorsichtig sein, wenn in der Unterabfrage `NULL`-Werte vorkommen.

Deshalb ist `NOT EXISTS` oft besser.

---

# 10. CROSS JOIN

Ein `CROSS JOIN` kombiniert jede Zeile der ersten Tabelle mit jeder Zeile der zweiten Tabelle.

Das Ergebnis ist ein kartesisches Produkt.

### Struktur

```sql
SELECT spalten
FROM tabelle1
CROSS JOIN tabelle2;
```

### Beispiel

Tabelle `farben`:

| farbe |
| ----- |
| Rot   |
| Blau  |

Tabelle `groessen`:

| groesse |
| ------- |
| S       |
| M       |
| L       |

```sql
SELECT f.farbe, g.groesse
FROM farben f
CROSS JOIN groessen g;
```

### Ergebnis

| farbe | groesse |
| ----- | ------- |
| Rot   | S       |
| Rot   | M       |
| Rot   | L       |
| Blau  | S       |
| Blau  | M       |
| Blau  | L       |

### Erklärung

Wenn Tabelle A 2 Zeilen hat und Tabelle B 3 Zeilen hat, dann hat das Ergebnis:

```sql
2 * 3 = 6 Zeilen
```

### Anwendung

`CROSS JOIN` wird verwendet, wenn alle möglichen Kombinationen gebildet werden sollen.

Beispiele:

* Farben und Größen
* Tage und Uhrzeiten
* Produkte und Rabattstufen

---

# 11. JOIN mit mehreren Tabellen

Man kann mehr als zwei Tabellen miteinander verbinden.

Beispiel:

* `kunden`
* `bestellungen`
* `produkte`

---

## 11.1 Beispieltabellen

### kunden

| kunden_id | name |
| --------- | ---- |
| 1         | Ali  |
| 2         | Sara |

### bestellungen

| bestellung_id | kunden_id | produkt_id |
| ------------- | --------- | ---------- |
| 101           | 1         | 10         |
| 102           | 2         | 20         |

### produkte

| produkt_id | produktname | preis |
| ---------- | ----------- | ----- |
| 10         | Laptop      | 900   |
| 20         | Tastatur    | 50    |

---

## 11.2 INNER JOIN mit mehreren Tabellen

```sql
SELECT 
    k.name,
    b.bestellung_id,
    p.produktname,
    p.preis
FROM kunden k
INNER JOIN bestellungen b
ON k.kunden_id = b.kunden_id
INNER JOIN produkte p
ON b.produkt_id = p.produkt_id;
```

### Ergebnis

| name | bestellung_id | produktname | preis |
| ---- | ------------- | ----------- | ----- |
| Ali  | 101           | Laptop      | 900   |
| Sara | 102           | Tastatur    | 50    |

---

## 11.3 LEFT JOIN mit mehreren Tabellen

```sql
SELECT 
    k.name,
    b.bestellung_id,
    p.produktname,
    p.preis
FROM kunden k
LEFT JOIN bestellungen b
ON k.kunden_id = b.kunden_id
LEFT JOIN produkte p
ON b.produkt_id = p.produkt_id;
```

### Erklärung

Dieser Befehl zeigt alle Kunden.

Wenn ein Kunde keine Bestellung hat, stehen bei Bestellung und Produkt `NULL`-Werte.

---

# 12. ON vs WHERE

`ON` beschreibt, wie Tabellen miteinander verbunden werden.

`WHERE` filtert das Ergebnis nach dem Join.

### Beispiel

```sql
SELECT k.name, b.produkt
FROM kunden k
LEFT JOIN bestellungen b
ON k.kunden_id = b.kunden_id
WHERE k.stadt = 'Düsseldorf';
```

### Erklärung

* `ON k.kunden_id = b.kunden_id` verbindet Kunden mit Bestellungen.
* `WHERE k.stadt = 'Düsseldorf'` filtert danach nur Kunden aus Düsseldorf.

---

## 12.1 Wichtig bei LEFT JOIN

Bei `LEFT JOIN` muss man vorsichtig sein, wenn man Bedingungen auf die rechte Tabelle setzt.

### Beispiel

```sql
SELECT k.name, b.produkt
FROM kunden k
LEFT JOIN bestellungen b
ON k.kunden_id = b.kunden_id
WHERE b.produkt = 'Laptop';
```

Dieser Befehl kann sich wie ein `INNER JOIN` verhalten, weil Zeilen mit `NULL` durch `WHERE` entfernt werden.

Besser, wenn man trotzdem alle Kunden behalten möchte:

```sql
SELECT k.name, b.produkt
FROM kunden k
LEFT JOIN bestellungen b
ON k.kunden_id = b.kunden_id
AND b.produkt = 'Laptop';
```

---

# 13. USING

Wenn beide Tabellen denselben Spaltennamen besitzen, kann man manchmal `USING` verwenden.

### Beispiel

```sql
SELECT k.name, b.produkt
FROM kunden k
JOIN bestellungen b
USING (kunden_id);
```

Das ist kürzer als:

```sql
SELECT k.name, b.produkt
FROM kunden k
JOIN bestellungen b
ON k.kunden_id = b.kunden_id;
```

### Hinweis

`USING` funktioniert nur, wenn die Spalte in beiden Tabellen gleich heißt.

---

# 14. SELF JOIN

Ein `SELF JOIN` verbindet eine Tabelle mit sich selbst.

Das ist nützlich, wenn Datensätze innerhalb derselben Tabelle miteinander verglichen werden.

### Beispiel: Mitarbeiter und Manager

```sql
SELECT 
    m.name AS mitarbeiter,
    manager.name AS manager
FROM mitarbeiter m
LEFT JOIN mitarbeiter manager
ON m.manager_id = manager.mitarbeiter_id;
```

### Erklärung

Die Tabelle `mitarbeiter` wird zweimal verwendet:

* einmal als `m` für Mitarbeiter
* einmal als `manager` für Manager

---

# 15. Übersicht der JOIN-Arten

| JOIN-Art          | Bedeutung                                                           |
| ----------------- | ------------------------------------------------------------------- |
| `INNER JOIN`      | Nur passende Datensätze aus beiden Tabellen                         |
| `LEFT JOIN`       | Alle Datensätze aus der linken Tabelle und passende aus der rechten |
| `RIGHT JOIN`      | Alle Datensätze aus der rechten Tabelle und passende aus der linken |
| `FULL OUTER JOIN` | Alle Datensätze aus beiden Tabellen                                 |
| `ANTI JOIN`       | Datensätze ohne passende Einträge in der anderen Tabelle            |
| `CROSS JOIN`      | Alle möglichen Kombinationen beider Tabellen                        |
| `SELF JOIN`       | Eine Tabelle wird mit sich selbst verbunden                         |

---

# 16. Häufige Fehler bei JOINs

## 16.1 JOIN-Bedingung vergessen

```sql
SELECT *
FROM kunden k
JOIN bestellungen b;
```

Das kann zu sehr vielen unerwarteten Kombinationen führen.

Besser:

```sql
SELECT *
FROM kunden k
JOIN bestellungen b
ON k.kunden_id = b.kunden_id;
```

---

## 16.2 Falsche Join-Spalte verwenden

```sql
ON k.kunden_id = b.bestellung_id
```

Das ist meistens falsch, weil eine Kunden-ID nicht mit einer Bestell-ID verglichen werden sollte.

Richtig:

```sql
ON k.kunden_id = b.kunden_id
```

---

## 16.3 Mehr Zeilen als erwartet

Wenn ein Kunde mehrere Bestellungen hat, erscheint der Kunde mehrfach.

Beispiel:

| kunden_id | name |
| --------- | ---- |
| 1         | Ali  |

Wenn Ali zwei Bestellungen hat, erscheint Ali im Join-Ergebnis zweimal.

Das ist kein Fehler, sondern normales JOIN-Verhalten.

---

# 17. Mini-Beispiel komplett

```sql
SELECT 
    k.kunden_id,
    k.name,
    b.bestellung_id,
    p.produktname,
    p.preis
FROM kunden k
LEFT JOIN bestellungen b
ON k.kunden_id = b.kunden_id
LEFT JOIN produkte p
ON b.produkt_id = p.produkt_id
WHERE k.stadt IN ('Düsseldorf', 'Köln')
ORDER BY k.name;
```

### Erklärung

Dieser Befehl:

* nimmt alle Kunden aus Düsseldorf oder Köln,
* verbindet sie mit ihren Bestellungen,
* verbindet die Bestellungen mit Produkten,
* zeigt auch Kunden ohne Bestellung,
* sortiert das Ergebnis nach dem Namen.

---

# 18. Merksätze

* `INNER JOIN` zeigt nur passende Datensätze.
* `LEFT JOIN` zeigt alle Datensätze aus der linken Tabelle.
* `RIGHT JOIN` zeigt alle Datensätze aus der rechten Tabelle.
* `FULL OUTER JOIN` zeigt alles aus beiden Tabellen.
* `ANTI JOIN` zeigt Datensätze ohne passende Verbindung.
* `CROSS JOIN` erzeugt alle Kombinationen.
* Mit mehreren `JOINs` kann man mehrere Tabellen verbinden.
* `ON` verbindet Tabellen.
* `WHERE` filtert das Ergebnis.
* Aliase machen JOIN-Abfragen übersichtlicher.


# SQL Zusammenfassung – Teil 4: Set Operators

## 1. Was sind Set Operators?

`Set Operators` werden verwendet, um die Ergebnisse von zwei oder mehreren `SELECT`-Abfragen miteinander zu kombinieren.

Wichtig: Set Operators verbinden **Zeilen untereinander**.

Sie verbinden nicht Tabellen nebeneinander wie `JOINs`.

| Konzept        | Bedeutung                                  |
| -------------- | ------------------------------------------ |
| `JOIN`         | Verbindet Tabellen horizontal über Spalten |
| `Set Operator` | Kombiniert Ergebnisse vertikal über Zeilen |

---

## 2. Wichtige Set Operators

| Operator    | Bedeutung                                                                     |
| ----------- | ----------------------------------------------------------------------------- |
| `UNION`     | Kombiniert Ergebnisse und entfernt Duplikate                                  |
| `UNION ALL` | Kombiniert Ergebnisse und behält Duplikate                                    |
| `EXCEPT`    | Gibt Zeilen aus der ersten Abfrage zurück, die nicht in der zweiten vorkommen |
| `INTERSECT` | Gibt nur Zeilen zurück, die in beiden Abfragen vorkommen                      |

---

## 3. Grundregeln für Set Operators

Damit Set Operators funktionieren, müssen bestimmte Regeln eingehalten werden.

### 3.1 Gleiche Anzahl von Spalten

Beide `SELECT`-Abfragen müssen die gleiche Anzahl an Spalten zurückgeben.

Richtig:

```sql
SELECT name, stadt
FROM kunden_2024

UNION

SELECT name, stadt
FROM kunden_2025;
```

Falsch:

```sql
SELECT name, stadt
FROM kunden_2024

UNION

SELECT name
FROM kunden_2025;
```

Die zweite Abfrage hat nur eine Spalte, die erste aber zwei.

---

### 3.2 Kompatible Datentypen

Die Datentypen der Spalten müssen zueinander passen.

Richtig:

```sql
SELECT name, alter
FROM kunden_2024

UNION

SELECT name, alter
FROM kunden_2025;
```

Falsch:

```sql
SELECT name, alter
FROM kunden_2024

UNION

SELECT name, geburtsdatum
FROM kunden_2025;
```

Hier wird in der zweiten Spalte einmal `alter` und einmal `geburtsdatum` verglichen. Das kann problematisch sein, weil die Datentypen oder Bedeutungen nicht zusammenpassen.

---

### 3.3 Reihenfolge der Spalten ist wichtig

Nicht die Spaltennamen sind entscheidend, sondern die Position der Spalten.

Problematisches Beispiel:

```sql
SELECT name, stadt
FROM kunden_2024

UNION

SELECT stadt, name
FROM kunden_2025;
```

SQL kombiniert hier:

| Erste Abfrage | Zweite Abfrage |
| ------------- | -------------- |
| `name`        | `stadt`        |
| `stadt`       | `name`         |

Das Ergebnis ist fachlich falsch, obwohl beide Abfragen zwei Textspalten haben.

Besser:

```sql
SELECT name, stadt
FROM kunden_2024

UNION

SELECT name, stadt
FROM kunden_2025;
```

---

### 3.4 Die erste Abfrage bestimmt die Spaltennamen

Die Spaltennamen im Endergebnis kommen aus der ersten `SELECT`-Abfrage.

```sql
SELECT name AS kundenname, stadt AS wohnort
FROM kunden_2024

UNION

SELECT full_name, city
FROM kunden_2025;
```

Das Ergebnis hat die Spaltennamen:

| kundenname | wohnort |
| ---------- | ------- |

Die Aliasnamen aus der zweiten Abfrage werden für das Endergebnis nicht verwendet.

---

### 3.5 ORDER BY steht am Ende

Wenn das Gesamtergebnis sortiert werden soll, steht `ORDER BY` ganz am Ende.

```sql
SELECT name, stadt
FROM kunden_2024

UNION

SELECT name, stadt
FROM kunden_2025

ORDER BY name;
```

Nicht jede einzelne Abfrage bekommt ein eigenes `ORDER BY`.

---

## 4. UNION

`UNION` kombiniert die Ergebnisse von zwei Abfragen und entfernt doppelte Zeilen.

### Allgemeine Struktur

```sql
SELECT spalte1, spalte2
FROM tabelle1

UNION

SELECT spalte1, spalte2
FROM tabelle2;
```

### Beispiel

```sql
SELECT stadt
FROM kunden_2024

UNION

SELECT stadt
FROM kunden_2025;
```

### Ergebnisidee

Wenn beide Tabellen diese Werte enthalten:

| kunden_2024 |
| ----------- |
| Düsseldorf  |
| Köln        |
| Berlin      |

| kunden_2025 |
| ----------- |
| Köln        |
| Hamburg     |

Dann ergibt `UNION`:

| stadt      |
| ---------- |
| Düsseldorf |
| Köln       |
| Berlin     |
| Hamburg    |

`Köln` erscheint nur einmal, weil `UNION` Duplikate entfernt.

---

## 5. UNION ALL

`UNION ALL` kombiniert die Ergebnisse von zwei Abfragen und behält doppelte Zeilen.

### Allgemeine Struktur

```sql
SELECT spalte1, spalte2
FROM tabelle1

UNION ALL

SELECT spalte1, spalte2
FROM tabelle2;
```

### Beispiel

```sql
SELECT stadt
FROM kunden_2024

UNION ALL

SELECT stadt
FROM kunden_2025;
```

### Ergebnisidee

| stadt      |
| ---------- |
| Düsseldorf |
| Köln       |
| Berlin     |
| Köln       |
| Hamburg    |

`Köln` erscheint zweimal, weil `UNION ALL` Duplikate nicht entfernt.

---

## 6. Unterschied zwischen UNION und UNION ALL

| Operator    | Duplikate          | Geschwindigkeit | Verwendung                                 |
| ----------- | ------------------ | --------------- | ------------------------------------------ |
| `UNION`     | Entfernt Duplikate | Meist langsamer | Wenn eindeutige Ergebnisse benötigt werden |
| `UNION ALL` | Behält Duplikate   | Meist schneller | Wenn alle Zeilen erhalten bleiben sollen   |

### Merksatz

`UNION` bedeutet: zusammenfügen und Duplikate entfernen.

`UNION ALL` bedeutet: alles zusammenfügen, auch doppelte Zeilen.

---

## 7. EXCEPT

`EXCEPT` gibt nur die Zeilen zurück, die in der ersten Abfrage vorkommen, aber nicht in der zweiten.

Man kann es lesen als:

```text
Ergebnis von Abfrage 1 minus Ergebnis von Abfrage 2
```

### Allgemeine Struktur

```sql
SELECT spalte1, spalte2
FROM tabelle1

EXCEPT

SELECT spalte1, spalte2
FROM tabelle2;
```

### Beispiel

```sql
SELECT kunden_id
FROM kunden

EXCEPT

SELECT kunden_id
FROM bestellungen;
```

### Erklärung

Dieser Befehl zeigt Kunden, die keine Bestellung haben.

Die erste Abfrage liefert alle Kunden.

Die zweite Abfrage liefert Kunden, die bestellt haben.

`EXCEPT` entfernt aus der ersten Menge alle Kunden, die in der zweiten Menge vorkommen.

---

## 8. EXCEPT ALL

Manche Datenbanksysteme unterstützen auch `EXCEPT ALL`.

`EXCEPT` entfernt Duplikate.

`EXCEPT ALL` berücksichtigt Duplikate.

### Beispiel

```sql
SELECT produkt
FROM einkaufsliste_a

EXCEPT ALL

SELECT produkt
FROM einkaufsliste_b;
```

### Hinweis

Nicht jedes Datenbanksystem unterstützt `EXCEPT ALL`.

---

## 9. INTERSECT

`INTERSECT` gibt nur die Zeilen zurück, die in beiden Abfragen vorkommen.

Man kann es lesen als:

```text
Schnittmenge von Abfrage 1 und Abfrage 2
```

### Allgemeine Struktur

```sql
SELECT spalte1, spalte2
FROM tabelle1

INTERSECT

SELECT spalte1, spalte2
FROM tabelle2;
```

### Beispiel

```sql
SELECT kunden_id
FROM newsletter_kunden

INTERSECT

SELECT kunden_id
FROM bestellungen;
```

### Erklärung

Dieser Befehl zeigt Kunden, die:

* im Newsletter eingetragen sind,
* und mindestens eine Bestellung haben.

Nur Werte, die in beiden Ergebnissen vorkommen, werden angezeigt.

---

## 10. INTERSECT ALL

Manche Datenbanksysteme unterstützen auch `INTERSECT ALL`.

`INTERSECT` entfernt Duplikate.

`INTERSECT ALL` berücksichtigt Duplikate.

### Beispiel

```sql
SELECT produkt
FROM liste_a

INTERSECT ALL

SELECT produkt
FROM liste_b;
```

### Hinweis

Nicht jedes Datenbanksystem unterstützt `INTERSECT ALL`.

---

## 11. Vergleich der Set Operators

| Operator    | Bedeutung                                            | Duplikate          |
| ----------- | ---------------------------------------------------- | ------------------ |
| `UNION`     | Alles aus beiden Abfragen                            | Entfernt Duplikate |
| `UNION ALL` | Alles aus beiden Abfragen                            | Behält Duplikate   |
| `EXCEPT`    | Nur Werte aus Abfrage 1, die nicht in Abfrage 2 sind | Entfernt Duplikate |
| `INTERSECT` | Nur Werte, die in beiden Abfragen vorkommen          | Entfernt Duplikate |

---

## 12. Set Operators mit mehreren Spalten

Set Operators vergleichen immer komplette Zeilen, nicht nur eine einzelne Spalte.

### Beispiel

```sql
SELECT name, stadt
FROM kunden_2024

INTERSECT

SELECT name, stadt
FROM kunden_2025;
```

Eine Zeile kommt nur dann im Ergebnis vor, wenn `name` und `stadt` in beiden Abfragen gleich sind.

Beispiel:

| kunden_2024 |            |
| ----------- | ---------- |
| Ali         | Düsseldorf |

| kunden_2025 |      |
| ----------- | ---- |
| Ali         | Köln |

Diese Zeile gilt nicht als gleich, weil die Stadt unterschiedlich ist.

---

## 13. Set Operators mit WHERE

Set Operators können mit `WHERE` kombiniert werden.

```sql
SELECT name, stadt
FROM kunden_2024
WHERE stadt = 'Düsseldorf'

UNION

SELECT name, stadt
FROM kunden_2025
WHERE stadt = 'Düsseldorf';
```

Dieser Befehl zeigt alle eindeutigen Kunden aus Düsseldorf aus beiden Tabellen.

---

## 14. Set Operators mit ORDER BY

`ORDER BY` wird am Ende der gesamten Abfrage geschrieben.

```sql
SELECT name, stadt
FROM kunden_2024

UNION ALL

SELECT name, stadt
FROM kunden_2025

ORDER BY stadt, name;
```

Das Gesamtergebnis wird zuerst nach `stadt` und dann nach `name` sortiert.

---

## 15. Set Operators mit Alias

Die erste Abfrage bestimmt die Spaltennamen.

```sql
SELECT name AS person, stadt AS ort
FROM kunden_2024

UNION

SELECT mitarbeiter_name, standort
FROM mitarbeiter;
```

Das Ergebnis hat die Spaltennamen:

| person | ort |
| ------ | --- |

---

## 16. Typische Anwendungsfälle

| Situation                                                   | Passender Operator |
| ----------------------------------------------------------- | ------------------ |
| Zwei Kundenlisten zusammenführen und Duplikate entfernen    | `UNION`            |
| Zwei Log-Tabellen zusammenführen und alle Einträge behalten | `UNION ALL`        |
| Kunden finden, die in Liste A, aber nicht in Liste B sind   | `EXCEPT`           |
| Kunden finden, die in beiden Listen vorkommen               | `INTERSECT`        |

---

## 17. Häufige Fehler

### 17.1 Unterschiedliche Anzahl von Spalten

Falsch:

```sql
SELECT name, stadt
FROM kunden

UNION

SELECT name
FROM mitarbeiter;
```

Beide Abfragen müssen gleich viele Spalten haben.

---

### 17.2 Falsche Spaltenreihenfolge

Problematisch:

```sql
SELECT name, stadt
FROM kunden

UNION

SELECT stadt, name
FROM mitarbeiter;
```

Die erste Spalte wird mit der ersten Spalte kombiniert, die zweite mit der zweiten.

---

### 17.3 Falsche Annahme über Spaltennamen

Die Spaltennamen der zweiten Abfrage bestimmen nicht das Endergebnis.

```sql
SELECT name AS kundenname
FROM kunden

UNION

SELECT mitarbeiter_name AS mitarbeitername
FROM mitarbeiter;
```

Das Endergebnis heißt `kundenname`, nicht `mitarbeitername`.

---

### 17.4 ORDER BY an der falschen Stelle

Falsch:

```sql
SELECT name
FROM kunden
ORDER BY name

UNION

SELECT name
FROM mitarbeiter;
```

Richtig:

```sql
SELECT name
FROM kunden

UNION

SELECT name
FROM mitarbeiter

ORDER BY name;
```

---

## 18. Mini-Beispiel komplett

```sql
SELECT kunden_id, name, 'Kunde 2024' AS quelle
FROM kunden_2024

UNION ALL

SELECT kunden_id, name, 'Kunde 2025' AS quelle
FROM kunden_2025

ORDER BY name;
```

### Erklärung

Dieser Befehl:

* nimmt Kunden aus `kunden_2024`,
* nimmt Kunden aus `kunden_2025`,
* behält doppelte Einträge,
* fügt eine zusätzliche Spalte `quelle` hinzu,
* sortiert das Gesamtergebnis nach `name`.

---

## 19. Merksätze

* Set Operators kombinieren Ergebnisse von mehreren `SELECT`-Abfragen.
* Set Operators verbinden Zeilen untereinander.
* Beide Abfragen müssen die gleiche Anzahl von Spalten haben.
* Die Datentypen der Spalten müssen kompatibel sein.
* Die Reihenfolge der Spalten ist wichtig.
* Die erste Abfrage bestimmt die Spaltennamen im Ergebnis.
* `UNION` entfernt Duplikate.
* `UNION ALL` behält Duplikate.
* `EXCEPT` zeigt Werte aus der ersten Abfrage, die nicht in der zweiten vorkommen.
* `INTERSECT` zeigt nur Werte, die in beiden Abfragen vorkommen.
* `ORDER BY` steht am Ende der gesamten Set-Operation.


# SQL Zusammenfassung – Teil 5: Functions

## 1. Was sind SQL Functions?

SQL-Funktionen werden verwendet, um Werte zu bearbeiten, zu berechnen, zu formatieren oder zu prüfen.

Man kann Funktionen zum Beispiel in folgenden Teilen einer SQL-Abfrage verwenden:

* `SELECT`
* `WHERE`
* `ORDER BY`
* `GROUP BY`
* `HAVING`

### Allgemeine Struktur

```sql
SELECT FUNCTION(spalte)
FROM tabellenname;
```

### Beispiel

```sql
SELECT UPPER(name)
FROM kunden;
```

Dieser Befehl gibt die Namen der Kunden in Großbuchstaben aus.

---

## 2. Arten von SQL Functions

Es gibt viele SQL-Funktionen. Häufig werden sie in vier Gruppen eingeteilt:

| Kategorie               | Bedeutung                        | Beispiele                                       |
| ----------------------- | -------------------------------- | ----------------------------------------------- |
| String Functions        | Funktionen für Texte             | `CONCAT`, `UPPER`, `LOWER`, `TRIM`, `SUBSTRING` |
| Number Functions        | Funktionen für Zahlen            | `ROUND`, `CEIL`, `FLOOR`, `ABS`                 |
| Date and Time Functions | Funktionen für Datum und Uhrzeit | `NOW`, `CURRENT_DATE`, `DATEADD`, `DATEDIFF`    |
| Null Functions          | Funktionen für `NULL`-Werte      | `COALESCE`, `NULLIF`, `ISNULL`, `IFNULL`        |

In diesem Teil geht es zuerst um **String Functions**.

---

# 3. String Functions

String Functions werden verwendet, um Texte zu bearbeiten, zu verändern, zu zählen oder Teile aus Texten zu extrahieren.

Beispielhafte Anwendungsfälle:

* Namen in Großbuchstaben umwandeln
* Leerzeichen entfernen
* Texte zusammenfügen
* Länge eines Textes berechnen
* Teile eines Textes extrahieren
* Wörter oder Zeichen ersetzen

---

## 3.1 Übersicht der wichtigsten String Functions

| Kategorie    | Funktion         | Bedeutung                         |
| ------------ | ---------------- | --------------------------------- |
| Manipulation | `CONCAT`         | Texte zusammenfügen               |
| Manipulation | `UPPER`          | Text in Großbuchstaben umwandeln  |
| Manipulation | `LOWER`          | Text in Kleinbuchstaben umwandeln |
| Manipulation | `TRIM`           | Leerzeichen entfernen             |
| Manipulation | `LTRIM`          | Leerzeichen links entfernen       |
| Manipulation | `RTRIM`          | Leerzeichen rechts entfernen      |
| Manipulation | `REPLACE`        | Zeichen oder Wörter ersetzen      |
| Calculation  | `LEN` / `LENGTH` | Länge eines Textes berechnen      |
| Extraction   | `LEFT`           | Zeichen von links extrahieren     |
| Extraction   | `RIGHT`          | Zeichen von rechts extrahieren    |
| Extraction   | `SUBSTRING`      | Teil eines Textes extrahieren     |

---

# 4. String Manipulation

String Manipulation bedeutet, dass ein Text verändert oder bearbeitet wird.

---

## 4.1 CONCAT

`CONCAT` verbindet mehrere Texte miteinander.

### Allgemeine Struktur

```sql
SELECT CONCAT(text1, text2, text3)
FROM tabellenname;
```

### Beispiel

```sql
SELECT CONCAT(vorname, ' ', nachname) AS voller_name
FROM kunden;
```

### Erklärung

Wenn die Tabelle so aussieht:

| vorname | nachname |
| ------- | -------- |
| Ali     | Amrani   |

Dann ist das Ergebnis:

| voller_name |
| ----------- |
| Ali Amrani  |

---

## 4.2 CONCAT mit mehreren Spalten

```sql
SELECT CONCAT(name, ' - ', stadt) AS kunde_info
FROM kunden;
```

### Beispielergebnis

| kunde_info       |
| ---------------- |
| Ali - Düsseldorf |
| Sara - Köln      |

---

## 4.3 Hinweis zu CONCAT je nach Datenbanksystem

Nicht jedes Datenbanksystem verwendet exakt dieselbe Syntax.

| Datenbanksystem | Möglichkeit                                                      |   |     |   |           |
| --------------- | ---------------------------------------------------------------- | - | --- | - | --------- |
| MySQL           | `CONCAT(vorname, ' ', nachname)`                                 |   |     |   |           |
| PostgreSQL      | `CONCAT(vorname, ' ', nachname)` oder `vorname                   |   | ' ' |   | nachname` |
| SQL Server      | `CONCAT(vorname, ' ', nachname)` oder `vorname + ' ' + nachname` |   |     |   |           |

---

## 4.4 UPPER

`UPPER` wandelt Text in Großbuchstaben um.

### Beispiel

```sql
SELECT UPPER(name) AS name_gross
FROM kunden;
```

### Ergebnisidee

| name | name_gross |
| ---- | ---------- |
| Ali  | ALI        |
| Sara | SARA       |

---

## 4.5 LOWER

`LOWER` wandelt Text in Kleinbuchstaben um.

### Beispiel

```sql
SELECT LOWER(email) AS email_klein
FROM kunden;
```

### Ergebnisidee

| email                                     | email_klein                               |
| ----------------------------------------- | ----------------------------------------- |
| [ALI@EXAMPLE.COM](mailto:ALI@EXAMPLE.COM) | [ali@example.com](mailto:ali@example.com) |

---

## 4.6 TRIM

`TRIM` entfernt Leerzeichen am Anfang und am Ende eines Textes.

### Beispiel

```sql
SELECT TRIM(name) AS sauberer_name
FROM kunden;
```

### Ergebnisidee

| name  | sauberer_name |
| ----- | ------------- |
| `Ali` | `Ali`         |

---

## 4.7 LTRIM und RTRIM

`LTRIM` entfernt Leerzeichen links.

`RTRIM` entfernt Leerzeichen rechts.

### Beispiel

```sql
SELECT 
    LTRIM(name) AS links_bereinigt,
    RTRIM(name) AS rechts_bereinigt
FROM kunden;
```

---

## 4.8 REPLACE

`REPLACE` ersetzt bestimmte Zeichen oder Wörter in einem Text.

### Allgemeine Struktur

```sql
SELECT REPLACE(spalte, alter_text, neuer_text)
FROM tabellenname;
```

### Beispiel

```sql
SELECT REPLACE(telefon, '-', '') AS telefon_bereinigt
FROM kunden;
```

### Erklärung

Dieser Befehl entfernt Bindestriche aus Telefonnummern.

| telefon     | telefon_bereinigt |
| ----------- | ----------------- |
| 0170-123456 | 0170123456        |

---

## 4.9 Beispiel mit REPLACE bei Text

```sql
SELECT REPLACE(stadt, 'Duesseldorf', 'Düsseldorf') AS korrigierte_stadt
FROM kunden;
```

---

# 5. String Calculation

String Calculation bedeutet, dass Informationen über einen Text berechnet werden.

---

## 5.1 LEN / LENGTH

Mit `LEN` oder `LENGTH` berechnet man die Länge eines Textes.

Wichtig: Die Funktion hängt vom Datenbanksystem ab.

| Datenbanksystem | Funktion                        |
| --------------- | ------------------------------- |
| SQL Server      | `LEN()`                         |
| MySQL           | `LENGTH()` oder `CHAR_LENGTH()` |
| PostgreSQL      | `LENGTH()`                      |
| SQLite          | `LENGTH()`                      |

---

### Beispiel mit SQL Server

```sql
SELECT LEN(name) AS name_laenge
FROM kunden;
```

---

### Beispiel mit MySQL / PostgreSQL

```sql
SELECT LENGTH(name) AS name_laenge
FROM kunden;
```

---

### Beispielergebnis

| name    | name_laenge |
| ------- | ----------- |
| Ali     | 3           |
| Mohamed | 7           |

---

## 5.2 CHAR_LENGTH

`CHAR_LENGTH` zählt die Anzahl der Zeichen.

In MySQL ist `LENGTH()` manchmal die Länge in Bytes, während `CHAR_LENGTH()` die Zeichen zählt.

### Beispiel

```sql
SELECT CHAR_LENGTH(name) AS anzahl_zeichen
FROM kunden;
```

---

# 6. String Extraction

String Extraction bedeutet, dass man bestimmte Teile aus einem Text herausholt.

---

## 6.1 LEFT

`LEFT` gibt eine bestimmte Anzahl von Zeichen von links zurück.

### Allgemeine Struktur

```sql
SELECT LEFT(spalte, anzahl)
FROM tabellenname;
```

### Beispiel

```sql
SELECT LEFT(name, 3) AS erste_zeichen
FROM kunden;
```

### Ergebnisidee

| name    | erste_zeichen |
| ------- | ------------- |
| Mohamed | Moh           |
| Sara    | Sar           |

---

## 6.2 RIGHT

`RIGHT` gibt eine bestimmte Anzahl von Zeichen von rechts zurück.

### Allgemeine Struktur

```sql
SELECT RIGHT(spalte, anzahl)
FROM tabellenname;
```

### Beispiel

```sql
SELECT RIGHT(telefon, 4) AS letzte_ziffern
FROM kunden;
```

### Ergebnisidee

| telefon    | letzte_ziffern |
| ---------- | -------------- |
| 0170123456 | 3456           |

---

## 6.3 SUBSTRING

`SUBSTRING` extrahiert einen Teil eines Textes.

### Allgemeine Struktur

```sql
SELECT SUBSTRING(spalte, start_position, anzahl_zeichen)
FROM tabellenname;
```

### Beispiel

```sql
SELECT SUBSTRING(name, 1, 3) AS teil_name
FROM kunden;
```

### Erklärung

Dieser Befehl nimmt aus `name` ab Position `1` insgesamt `3` Zeichen.

| name    | teil_name |
| ------- | --------- |
| Mohamed | Moh       |
| Sara    | Sar       |

---

## 6.4 SUBSTRING aus der Mitte

```sql
SELECT SUBSTRING(telefon, 5, 3) AS teil_nummer
FROM kunden;
```

Wenn `telefon = '0170123456'`, dann kann das Ergebnis zum Beispiel sein:

| teil_nummer |
| ----------- |
| 123         |

---

# 7. Weitere nützliche String Functions

## 7.1 POSITION / CHARINDEX / INSTR

Diese Funktionen suchen die Position eines Zeichens oder Textes innerhalb eines Strings.

Die Syntax hängt vom Datenbanksystem ab.

| Datenbanksystem | Funktion                     |
| --------------- | ---------------------------- |
| PostgreSQL      | `POSITION('text' IN spalte)` |
| SQL Server      | `CHARINDEX('text', spalte)`  |
| MySQL           | `INSTR(spalte, 'text')`      |

---

### PostgreSQL Beispiel

```sql
SELECT POSITION('@' IN email) AS position_at
FROM kunden;
```

---

### SQL Server Beispiel

```sql
SELECT CHARINDEX('@', email) AS position_at
FROM kunden;
```

---

### MySQL Beispiel

```sql
SELECT INSTR(email, '@') AS position_at
FROM kunden;
```

---

## 7.2 Praktisches Beispiel: Domain aus E-Mail extrahieren

### SQL Server

```sql
SELECT SUBSTRING(email, CHARINDEX('@', email) + 1, LEN(email)) AS domain
FROM kunden;
```

### MySQL

```sql
SELECT SUBSTRING(email, INSTR(email, '@') + 1) AS domain
FROM kunden;
```

### PostgreSQL

```sql
SELECT SUBSTRING(email FROM POSITION('@' IN email) + 1) AS domain
FROM kunden;
```

---

# 8. String Functions mit WHERE

String Functions können auch in `WHERE` verwendet werden.

### Beispiel: Groß- und Kleinschreibung ignorieren

```sql
SELECT *
FROM kunden
WHERE LOWER(email) = 'ali@example.com';
```

### Beispiel: Namen bereinigen

```sql
SELECT *
FROM kunden
WHERE TRIM(name) = 'Ali';
```

---

# 9. String Functions mit ORDER BY

Man kann Ergebnisse auch nach berechneten Textwerten sortieren.

```sql
SELECT name
FROM kunden
ORDER BY LOWER(name);
```

Dieser Befehl sortiert Namen unabhängig von Groß- und Kleinschreibung.

---

# 10. Kombination mehrerer String Functions

String Functions können miteinander kombiniert werden.

### Beispiel

```sql
SELECT UPPER(TRIM(name)) AS name_bereinigt
FROM kunden;
```

### Erklärung

Dieser Befehl:

* entfernt zuerst Leerzeichen mit `TRIM`,
* wandelt danach den Namen mit `UPPER` in Großbuchstaben um.

---

## 11. Praktisches Komplettbeispiel

```sql
SELECT
    kunden_id,
    CONCAT(TRIM(vorname), ' ', TRIM(nachname)) AS voller_name,
    LOWER(email) AS email_klein,
    LEFT(telefon, 4) AS telefon_vorwahl,
    RIGHT(telefon, 4) AS telefon_ende,
    LENGTH(name) AS name_laenge
FROM kunden;
```

### Erklärung

Dieser Befehl:

* verbindet Vorname und Nachname,
* entfernt unnötige Leerzeichen,
* wandelt E-Mail-Adressen in Kleinbuchstaben um,
* zeigt die ersten vier Zeichen der Telefonnummer,
* zeigt die letzten vier Zeichen der Telefonnummer,
* berechnet die Länge des Namens.

---

# 12. Häufige Fehler

## 12.1 Falsche Funktion für das Datenbanksystem

Beispiel:

```sql
SELECT LEN(name)
FROM kunden;
```

Das funktioniert in SQL Server, aber nicht unbedingt in MySQL oder PostgreSQL.

Für MySQL oder PostgreSQL verwendet man meistens:

```sql
SELECT LENGTH(name)
FROM kunden;
```

---

## 12.2 Anführungszeichen vergessen

Falsch:

```sql
SELECT REPLACE(stadt, Duesseldorf, Düsseldorf)
FROM kunden;
```

Richtig:

```sql
SELECT REPLACE(stadt, 'Duesseldorf', 'Düsseldorf')
FROM kunden;
```

Textwerte müssen in einfache Anführungszeichen geschrieben werden.

---

## 12.3 Position bei SUBSTRING falsch verstehen

Viele SQL-Datenbanksysteme beginnen bei Strings mit Position `1`, nicht mit Position `0`.

```sql
SELECT SUBSTRING('Mohamed', 1, 3);
```

Ergebnis:

| substring |
| --------- |
| Moh       |

---

# 13. Mini-Übersicht

| Funktion    | Bedeutung                           | Beispiel                         |
| ----------- | ----------------------------------- | -------------------------------- |
| `CONCAT`    | Texte verbinden                     | `CONCAT(vorname, ' ', nachname)` |
| `UPPER`     | Großbuchstaben                      | `UPPER(name)`                    |
| `LOWER`     | Kleinbuchstaben                     | `LOWER(email)`                   |
| `TRIM`      | Leerzeichen entfernen               | `TRIM(name)`                     |
| `LTRIM`     | Leerzeichen links entfernen         | `LTRIM(name)`                    |
| `RTRIM`     | Leerzeichen rechts entfernen        | `RTRIM(name)`                    |
| `REPLACE`   | Text ersetzen                       | `REPLACE(telefon, '-', '')`      |
| `LEN`       | Länge berechnen in SQL Server       | `LEN(name)`                      |
| `LENGTH`    | Länge berechnen in MySQL/PostgreSQL | `LENGTH(name)`                   |
| `LEFT`      | Zeichen von links                   | `LEFT(name, 3)`                  |
| `RIGHT`     | Zeichen von rechts                  | `RIGHT(telefon, 4)`              |
| `SUBSTRING` | Teiltext extrahieren                | `SUBSTRING(name, 1, 3)`          |

---

# 14. Merksätze

* String Functions bearbeiten Textwerte.
* `CONCAT` verbindet Texte.
* `UPPER` macht Text groß.
* `LOWER` macht Text klein.
* `TRIM` entfernt Leerzeichen am Anfang und Ende.
* `REPLACE` ersetzt Zeichen oder Wörter.
* `LEN` oder `LENGTH` berechnet die Textlänge.
* `LEFT` nimmt Zeichen von links.
* `RIGHT` nimmt Zeichen von rechts.
* `SUBSTRING` nimmt einen bestimmten Teil eines Textes.
* Die genaue Syntax kann je nach Datenbanksystem unterschiedlich sein.


# SQL Zusammenfassung – Teil 6: Number Functions

## 1. Was sind Number Functions?

`Number Functions` werden verwendet, um numerische Werte zu berechnen, zu runden oder umzuwandeln.

Sie werden häufig verwendet bei:

* Preisen
* Rabatten
* Durchschnittswerten
* Beträgen
* Messwerten
* mathematischen Berechnungen

In diesem Teil geht es um:

* `ROUND`
* `ABS`

---

## 2. ROUND

`ROUND` wird verwendet, um Zahlen auf eine bestimmte Anzahl von Dezimalstellen zu runden.

---

## 2.1 Allgemeine Struktur

```sql
SELECT ROUND(zahl, dezimalstellen);
```

### Bedeutung

| Teil             | Bedeutung                                      |
| ---------------- | ---------------------------------------------- |
| `zahl`           | Die Zahl oder Spalte, die gerundet werden soll |
| `dezimalstellen` | Anzahl der Nachkommastellen                    |

---

## 2.2 Beispiel mit direkter Zahl

```sql
SELECT ROUND(12.567, 2) AS gerundeter_wert;
```

### Ergebnis

| gerundeter_wert |
| --------------- |
| 12.57           |

---

## 2.3 Beispiel mit Tabelle

```sql
SELECT 
    produktname,
    preis,
    ROUND(preis, 2) AS preis_gerundet
FROM produkte;
```

### Beispielergebnis

| produktname | preis   | preis_gerundet |
| ----------- | ------- | -------------- |
| Laptop      | 899.995 | 900.00         |
| Maus        | 19.876  | 19.88          |
| Tastatur    | 49.234  | 49.23          |

---

## 2.4 ROUND auf ganze Zahlen

Wenn man `0` als Dezimalstelle angibt, wird auf eine ganze Zahl gerundet.

```sql
SELECT ROUND(12.567, 0) AS gerundeter_wert;
```

### Ergebnis

| gerundeter_wert |
| --------------- |
| 13              |

---

## 2.5 ROUND mit Berechnung

`ROUND` kann auch mit Berechnungen kombiniert werden.

```sql
SELECT 
    produktname,
    preis,
    rabatt,
    ROUND(preis - rabatt, 2) AS endpreis
FROM produkte;
```

### Erklärung

Dieser Befehl:

* nimmt den Preis,
* zieht den Rabatt ab,
* rundet den Endpreis auf zwei Nachkommastellen.

---

## 2.6 ROUND mit Durchschnitt

```sql
SELECT ROUND(AVG(preis), 2) AS durchschnittspreis
FROM produkte;
```

### Erklärung

Dieser Befehl berechnet den durchschnittlichen Preis aller Produkte und rundet das Ergebnis auf zwei Nachkommastellen.

---

## 2.7 ROUND je nach Datenbanksystem

Die Grundfunktion ist in vielen SQL-Datenbanken ähnlich:

```sql
ROUND(zahl, dezimalstellen)
```

Beispiele:

| Datenbanksystem | Beispiel          |
| --------------- | ----------------- |
| MySQL           | `ROUND(preis, 2)` |
| PostgreSQL      | `ROUND(preis, 2)` |
| SQL Server      | `ROUND(preis, 2)` |
| SQLite          | `ROUND(preis, 2)` |

---

# 3. ABS

`ABS` steht für **Absolute Value**.

`ABS` gibt den absoluten Wert einer Zahl zurück.

Das bedeutet: Das Vorzeichen wird entfernt.

---

## 3.1 Allgemeine Struktur

```sql
SELECT ABS(zahl);
```

---

## 3.2 Beispiel mit negativer Zahl

```sql
SELECT ABS(-15) AS absoluter_wert;
```

### Ergebnis

| absoluter_wert |
| -------------- |
| 15             |

---

## 3.3 Beispiel mit positiver Zahl

```sql
SELECT ABS(15) AS absoluter_wert;
```

### Ergebnis

| absoluter_wert |
| -------------- |
| 15             |

---

## 3.4 Beispiel mit Tabelle

```sql
SELECT 
    konto_id,
    kontostand,
    ABS(kontostand) AS betrag
FROM konten;
```

### Beispielergebnis

| konto_id | kontostand | betrag |
| -------- | ---------- | ------ |
| 1        | 500        | 500    |
| 2        | -200       | 200    |
| 3        | -50        | 50     |

---

## 3.5 ABS bei Differenzen

`ABS` ist besonders nützlich, wenn nur die Differenz wichtig ist, aber nicht die Richtung.

```sql
SELECT 
    produktname,
    soll_preis,
    ist_preis,
    ABS(soll_preis - ist_preis) AS preisabweichung
FROM produkte;
```

### Erklärung

Dieser Befehl berechnet die absolute Abweichung zwischen Soll-Preis und Ist-Preis.

Wenn:

| soll_preis | ist_preis |
| ---------- | --------- |
| 100        | 90        |

Dann ist die Differenz eigentlich:

```sql
100 - 90 = 10
```

Wenn:

| soll_preis | ist_preis |
| ---------- | --------- |
| 90         | 100       |

Dann ist die Differenz eigentlich:

```sql
90 - 100 = -10
```

Mit `ABS` wird daraus in beiden Fällen:

```sql
10
```

---

# 4. ROUND und ABS kombinieren

Man kann `ROUND` und `ABS` auch zusammen verwenden.

```sql
SELECT 
    produktname,
    ROUND(ABS(soll_preis - ist_preis), 2) AS gerundete_preisabweichung
FROM produkte;
```

### Erklärung

Dieser Befehl:

* berechnet die Differenz zwischen Soll-Preis und Ist-Preis,
* macht daraus einen positiven Wert mit `ABS`,
* rundet das Ergebnis auf zwei Nachkommastellen mit `ROUND`.

---

# 5. Number Functions mit WHERE

Number Functions können auch in `WHERE` verwendet werden.

### Beispiel mit ABS

```sql
SELECT *
FROM produkte
WHERE ABS(soll_preis - ist_preis) > 10;
```

### Erklärung

Dieser Befehl zeigt alle Produkte, bei denen die Preisabweichung größer als 10 ist.

---

# 6. Number Functions mit ORDER BY

Man kann auch nach berechneten Zahlen sortieren.

```sql
SELECT 
    produktname,
    preis
FROM produkte
ORDER BY ABS(preis - 100);
```

### Erklärung

Dieser Befehl sortiert Produkte danach, wie weit ihr Preis von `100` entfernt ist.

---

# 7. Häufige Fehler

## 7.1 ROUND ohne Dezimalstellen falsch verstehen

```sql
SELECT ROUND(12.567, 0);
```

Das Ergebnis ist `13`, weil auf eine ganze Zahl gerundet wird.

---

## 7.2 ABS verändert nicht die ursprünglichen Daten

```sql
SELECT ABS(kontostand)
FROM konten;
```

Dieser Befehl zeigt nur den absoluten Wert im Ergebnis an.

Die Werte in der Tabelle werden dadurch nicht geändert.

---

## 7.3 ROUND verändert nicht die gespeicherten Werte

```sql
SELECT ROUND(preis, 2)
FROM produkte;
```

Auch hier wird nur die Ausgabe gerundet.

Der gespeicherte Wert in der Tabelle bleibt gleich.

Um Werte dauerhaft zu ändern, braucht man `UPDATE`.

---

# 8. Mini-Übersicht

| Funktion | Bedeutung                    | Beispiel           | Ergebnis |
| -------- | ---------------------------- | ------------------ | -------- |
| `ROUND`  | Zahl runden                  | `ROUND(12.567, 2)` | `12.57`  |
| `ROUND`  | Auf ganze Zahl runden        | `ROUND(12.567, 0)` | `13`     |
| `ABS`    | Absoluten Wert berechnen     | `ABS(-15)`         | `15`     |
| `ABS`    | Positive Zahl bleibt positiv | `ABS(15)`          | `15`     |

---

# 9. Mini-Beispiel komplett

```sql
SELECT
    produktname,
    preis,
    rabatt,
    ROUND(preis - rabatt, 2) AS endpreis,
    ABS(preis - rabatt) AS absolute_berechnung
FROM produkte;
```

### Erklärung

Dieser Befehl:

* zeigt den Produktnamen,
* zeigt den Preis,
* zeigt den Rabatt,
* berechnet den Endpreis,
* rundet den Endpreis auf zwei Nachkommastellen,
* berechnet zusätzlich den absoluten Wert der Berechnung.

---

# 10. Merksätze

* `ROUND` rundet Zahlen.
* `ROUND(zahl, 2)` rundet auf zwei Nachkommastellen.
* `ROUND(zahl, 0)` rundet auf eine ganze Zahl.
* `ABS` macht negative Zahlen positiv.
* `ABS` ist nützlich bei Differenzen und Abweichungen.
* `ROUND` und `ABS` ändern bei `SELECT` nur die Anzeige, nicht die gespeicherten Daten.
* Um gespeicherte Werte dauerhaft zu ändern, braucht man `UPDATE`.


# SQL Zusammenfassung – Teil 7: Date and Datetime Functions

## 1. Was sind Date and Datetime Functions?

`Date and Datetime Functions` werden verwendet, um mit Datum und Uhrzeit zu arbeiten.

Man kann damit zum Beispiel:

* Teile eines Datums extrahieren
* Datumswerte formatieren
* Datumswerte vergleichen
* Tage, Monate oder Jahre addieren
* Differenzen zwischen zwei Datumswerten berechnen
* Datumswerte konvertieren
* prüfen, ob ein Wert ein gültiges Datum ist

In diesem Teil geht es hauptsächlich um SQL-Server-Funktionen.

Wichtige Funktionen:

* `DATEPART`
* `DATENAME`
* `DATETRUNC`
* `EOMONTH`
* `FORMAT`
* `CONVERT`
* `CAST`
* `DATEADD`
* `DATEDIFF`
* `ISDATE`

Zusätzlich oft wichtig:

* `GETDATE`
* `SYSDATETIME`
* `CURRENT_TIMESTAMP`

---

## 2. Beispiel-Tabelle

Für die Beispiele verwenden wir eine Tabelle `bestellungen`.

| bestellung_id | kunde | bestelldatum        |
| ------------- | ----- | ------------------- |
| 1             | Ali   | 2024-01-15 10:30:00 |
| 2             | Sara  | 2024-02-20 14:15:00 |
| 3             | Omar  | 2024-12-05 09:00:00 |

---

# 3. Aktuelles Datum und aktuelle Uhrzeit

## 3.1 GETDATE

`GETDATE()` gibt das aktuelle Datum und die aktuelle Uhrzeit zurück.

```sql
SELECT GETDATE() AS aktuelles_datum;
```

Beispielergebnis:

| aktuelles_datum     |
| ------------------- |
| 2025-01-10 15:30:20 |

---

## 3.2 SYSDATETIME

`SYSDATETIME()` gibt ebenfalls das aktuelle Datum und die aktuelle Uhrzeit zurück, aber mit höherer Genauigkeit.

```sql
SELECT SYSDATETIME() AS aktuelle_zeit_genau;
```

---

## 3.3 CURRENT_TIMESTAMP

`CURRENT_TIMESTAMP` ist ähnlich wie `GETDATE()`.

```sql
SELECT CURRENT_TIMESTAMP AS aktueller_timestamp;
```

---

# 4. DATEPART

`DATEPART` extrahiert einen bestimmten numerischen Teil aus einem Datum.

Zum Beispiel:

* Jahr
* Monat
* Tag
* Stunde
* Minute
* Sekunde

## 4.1 Allgemeine Struktur

```sql
DATEPART(datepart, datum)
```

## 4.2 Beispiel

```sql
SELECT 
    bestelldatum,
    DATEPART(year, bestelldatum) AS jahr,
    DATEPART(month, bestelldatum) AS monat,
    DATEPART(day, bestelldatum) AS tag
FROM bestellungen;
```

### Beispielergebnis

| bestelldatum        | jahr | monat | tag |
| ------------------- | ---: | ----: | --: |
| 2024-01-15 10:30:00 | 2024 |     1 |  15 |

---

## 4.3 Wichtige DATEPART-Werte

| Datepart    | Bedeutung          | Beispiel                   |
| ----------- | ------------------ | -------------------------- |
| `year`      | Jahr               | `2024`                     |
| `quarter`   | Quartal            | `1`, `2`, `3`, `4`         |
| `month`     | Monat              | `1` bis `12`               |
| `day`       | Tag im Monat       | `1` bis `31`               |
| `dayofyear` | Tag im Jahr        | `1` bis `366`              |
| `week`      | Kalenderwoche      | `1` bis `53`               |
| `weekday`   | Wochentag als Zahl | abhängig von Einstellungen |
| `hour`      | Stunde             | `0` bis `23`               |
| `minute`    | Minute             | `0` bis `59`               |
| `second`    | Sekunde            | `0` bis `59`               |

---

# 5. DATENAME

`DATENAME` extrahiert einen Teil aus einem Datum als Text.

Der Unterschied zu `DATEPART` ist:

| Funktion   | Rückgabe |
| ---------- | -------- |
| `DATEPART` | Zahl     |
| `DATENAME` | Text     |

## 5.1 Allgemeine Struktur

```sql
DATENAME(datepart, datum)
```

## 5.2 Beispiel

```sql
SELECT 
    bestelldatum,
    DATENAME(month, bestelldatum) AS monatsname,
    DATENAME(weekday, bestelldatum) AS wochentag
FROM bestellungen;
```

### Beispielergebnis

| bestelldatum        | monatsname | wochentag |
| ------------------- | ---------- | --------- |
| 2024-01-15 10:30:00 | January    | Monday    |

Die Sprache der Ausgabe hängt von den Spracheinstellungen der Datenbank beziehungsweise Session ab.

---

# 6. DATETRUNC

`DATETRUNC` kürzt ein Datum auf eine bestimmte Einheit.

Das bedeutet: Kleinere Zeiteinheiten werden auf den Anfang gesetzt.

## 6.1 Allgemeine Struktur

```sql
DATETRUNC(datepart, datum)
```

## 6.2 Beispiel: auf Jahr kürzen

```sql
SELECT DATETRUNC(year, bestelldatum) AS start_des_jahres
FROM bestellungen;
```

Wenn `bestelldatum` den Wert `2024-05-18 14:30:00` hat, dann ist das Ergebnis:

| start_des_jahres    |
| ------------------- |
| 2024-01-01 00:00:00 |

---

## 6.3 Beispiel: auf Monat kürzen

```sql
SELECT DATETRUNC(month, bestelldatum) AS start_des_monats
FROM bestellungen;
```

Wenn `bestelldatum` den Wert `2024-05-18 14:30:00` hat, dann ist das Ergebnis:

| start_des_monats    |
| ------------------- |
| 2024-05-01 00:00:00 |

---

## 6.4 Beispiel: auf Tag kürzen

```sql
SELECT DATETRUNC(day, bestelldatum) AS start_des_tages
FROM bestellungen;
```

Wenn `bestelldatum` den Wert `2024-05-18 14:30:00` hat, dann ist das Ergebnis:

| start_des_tages     |
| ------------------- |
| 2024-05-18 00:00:00 |

---

## 6.5 Hinweis

`DATETRUNC` gibt es in SQL Server erst ab neueren Versionen.

In PostgreSQL heißt die Funktion ähnlich:

```sql
DATE_TRUNC('month', bestelldatum)
```

---

# 7. EOMONTH

`EOMONTH` gibt den letzten Tag eines Monats zurück.

EOMONTH steht für:

```text
End Of Month
```

## 7.1 Allgemeine Struktur

```sql
EOMONTH(datum)
```

## 7.2 Beispiel

```sql
SELECT EOMONTH('2024-02-10') AS letzter_tag_des_monats;
```

### Ergebnis

| letzter_tag_des_monats |
| ---------------------- |
| 2024-02-29             |

2024 ist ein Schaltjahr, deshalb hat Februar 29 Tage.

---

## 7.3 EOMONTH mit Monatsverschiebung

Man kann auch einen zweiten Parameter verwenden.

```sql
EOMONTH(datum, monate)
```

### Beispiel

```sql
SELECT EOMONTH('2024-02-10', 1) AS letzter_tag_naechster_monat;
```

### Ergebnis

| letzter_tag_naechster_monat |
| --------------------------- |
| 2024-03-31                  |

---

# 8. FORMAT

`FORMAT` formatiert Datumswerte oder Zahlen als Text.

## 8.1 Allgemeine Struktur

```sql
FORMAT(wert, format)
```

## 8.2 Datum formatieren

```sql
SELECT FORMAT(bestelldatum, 'dd.MM.yyyy') AS datum_deutsch
FROM bestellungen;
```

### Beispielergebnis

| bestelldatum        | datum_deutsch |
| ------------------- | ------------- |
| 2024-01-15 10:30:00 | 15.01.2024    |

---

## 8.3 Datum mit Uhrzeit formatieren

```sql
SELECT FORMAT(bestelldatum, 'dd.MM.yyyy HH:mm') AS datum_uhrzeit
FROM bestellungen;
```

### Beispielergebnis

| datum_uhrzeit    |
| ---------------- |
| 15.01.2024 10:30 |

---

## 8.4 Format mit Kultur

```sql
SELECT FORMAT(bestelldatum, 'D', 'de-DE') AS deutsches_datum
FROM bestellungen;
```

### Beispielergebnis

| deutsches_datum         |
| ----------------------- |
| Montag, 15. Januar 2024 |

---

## 8.5 Hinweis zu FORMAT

`FORMAT` gibt einen Text zurück.

Für reine Anzeige ist `FORMAT` praktisch.

Für Berechnungen oder Filter sollte man lieber mit echten Datumswerten arbeiten.

---

# 9. CONVERT

`CONVERT` wird verwendet, um einen Wert in einen anderen Datentyp umzuwandeln.

Bei Datumswerten kann man zusätzlich einen Style-Code verwenden.

## 9.1 Allgemeine Struktur

```sql
CONVERT(datentyp, wert)
```

oder:

```sql
CONVERT(datentyp, wert, style)
```

---

## 9.2 Datum zu Text konvertieren

```sql
SELECT CONVERT(VARCHAR(10), bestelldatum, 104) AS datum_deutsch
FROM bestellungen;
```

### Beispielergebnis

| datum_deutsch |
| ------------- |
| 15.01.2024    |

Der Style-Code `104` steht in SQL Server für deutsches Datumsformat:

```text
dd.mm.yyyy
```

---

## 9.3 Text zu Datum konvertieren

```sql
SELECT CONVERT(DATE, '2024-01-15') AS datum;
```

---

## 9.4 Häufige CONVERT-Styles in SQL Server

| Style | Format                | Beispiel              |
| ----: | --------------------- | --------------------- |
|  `23` | `yyyy-mm-dd`          | `2024-01-15`          |
| `104` | `dd.mm.yyyy`          | `15.01.2024`          |
| `105` | `dd-mm-yyyy`          | `15-01-2024`          |
| `101` | `mm/dd/yyyy`          | `01/15/2024`          |
| `120` | `yyyy-mm-dd hh:mi:ss` | `2024-01-15 10:30:00` |

---

# 10. CAST

`CAST` wandelt einen Wert in einen anderen Datentyp um.

`CAST` ist standardisierter als `CONVERT`, aber bietet keine Style-Codes wie `CONVERT`.

## 10.1 Allgemeine Struktur

```sql
CAST(wert AS datentyp)
```

## 10.2 Datetime zu Date

```sql
SELECT CAST(bestelldatum AS DATE) AS nur_datum
FROM bestellungen;
```

### Beispielergebnis

| bestelldatum        | nur_datum  |
| ------------------- | ---------- |
| 2024-01-15 10:30:00 | 2024-01-15 |

---

## 10.3 Text zu Date

```sql
SELECT CAST('2024-01-15' AS DATE) AS datum;
```

---

## 10.4 Date zu Text

```sql
SELECT CAST(bestelldatum AS VARCHAR(20)) AS datum_als_text
FROM bestellungen;
```

---

# 11. Unterschied zwischen CAST und CONVERT

| Funktion  | Bedeutung             | Besonderheit                                   |
| --------- | --------------------- | ---------------------------------------------- |
| `CAST`    | Wandelt Datentypen um | Standard-SQL, einfacher                        |
| `CONVERT` | Wandelt Datentypen um | SQL Server spezifisch, unterstützt Style-Codes |

### Beispiel CAST

```sql
SELECT CAST(bestelldatum AS DATE) AS datum
FROM bestellungen;
```

### Beispiel CONVERT

```sql
SELECT CONVERT(VARCHAR(10), bestelldatum, 104) AS datum_deutsch
FROM bestellungen;
```

---

# 12. DATEADD

`DATEADD` addiert oder subtrahiert Zeit zu einem Datum.

## 12.1 Allgemeine Struktur

```sql
DATEADD(datepart, anzahl, datum)
```

| Teil       | Bedeutung                                    |
| ---------- | -------------------------------------------- |
| `datepart` | Einheit, zum Beispiel `day`, `month`, `year` |
| `anzahl`   | Wie viel addiert oder subtrahiert wird       |
| `datum`    | Ausgangsdatum                                |

---

## 12.2 Tage addieren

```sql
SELECT DATEADD(day, 7, '2024-01-15') AS neues_datum;
```

### Ergebnis

| neues_datum |
| ----------- |
| 2024-01-22  |

---

## 12.3 Monate addieren

```sql
SELECT DATEADD(month, 3, '2024-01-15') AS neues_datum;
```

### Ergebnis

| neues_datum |
| ----------- |
| 2024-04-15  |

---

## 12.4 Jahre addieren

```sql
SELECT DATEADD(year, 1, '2024-01-15') AS neues_datum;
```

### Ergebnis

| neues_datum |
| ----------- |
| 2025-01-15  |

---

## 12.5 Zeit subtrahieren

Mit negativen Zahlen kann man Zeit abziehen.

```sql
SELECT DATEADD(day, -7, GETDATE()) AS vor_7_tagen;
```

---

# 13. DATEDIFF

`DATEDIFF` berechnet die Differenz zwischen zwei Datumswerten.

## 13.1 Allgemeine Struktur

```sql
DATEDIFF(datepart, startdatum, enddatum)
```

| Teil         | Bedeutung                                                  |
| ------------ | ---------------------------------------------------------- |
| `datepart`   | Einheit der Differenz, zum Beispiel `day`, `month`, `year` |
| `startdatum` | Anfangsdatum                                               |
| `enddatum`   | Enddatum                                                   |

---

## 13.2 Differenz in Tagen

```sql
SELECT DATEDIFF(day, '2024-01-01', '2024-01-15') AS tage;
```

### Ergebnis

| tage |
| ---: |
|   14 |

---

## 13.3 Differenz in Monaten

```sql
SELECT DATEDIFF(month, '2024-01-01', '2024-04-01') AS monate;
```

### Ergebnis

| monate |
| -----: |
|      3 |

---

## 13.4 Differenz in Jahren

```sql
SELECT DATEDIFF(year, '2000-01-01', GETDATE()) AS jahre;
```

---

## 13.5 Beispiel: Alter einer Bestellung

```sql
SELECT 
    bestellung_id,
    bestelldatum,
    DATEDIFF(day, bestelldatum, GETDATE()) AS tage_seit_bestellung
FROM bestellungen;
```

---

## 13.6 Wichtig bei DATEDIFF

`DATEDIFF` zählt die überschrittenen Grenzen der angegebenen Einheit.

Beispiel:

```sql
SELECT DATEDIFF(year, '2023-12-31', '2024-01-01') AS jahre;
```

Ergebnis:

| jahre |
| ----: |
|     1 |

Obwohl nur ein Tag vergangen ist, wurde eine Jahresgrenze überschritten.

---

# 14. ISDATE

`ISDATE` prüft, ob ein Wert ein gültiges Datum ist.

## 14.1 Allgemeine Struktur

```sql
ISDATE(wert)
```

## 14.2 Beispiel

```sql
SELECT ISDATE('2024-01-15') AS ist_datum;
```

### Ergebnis

| ist_datum |
| --------: |
|         1 |

`1` bedeutet: gültiges Datum.

`0` bedeutet: kein gültiges Datum.

---

## 14.3 Ungültiges Datum

```sql
SELECT ISDATE('abc') AS ist_datum;
```

### Ergebnis

| ist_datum |
| --------: |
|         0 |

---

## 14.4 ISDATE in WHERE

```sql
SELECT *
FROM import_daten
WHERE ISDATE(datumswert) = 1;
```

Dieser Befehl zeigt nur Zeilen, bei denen `datumswert` ein gültiges Datum ist.

---

# 15. Funktionen in SELECT, WHERE und ORDER BY

## 15.1 In SELECT

```sql
SELECT 
    bestellung_id,
    CAST(bestelldatum AS DATE) AS nur_datum,
    DATEPART(month, bestelldatum) AS monat
FROM bestellungen;
```

---

## 15.2 In WHERE

```sql
SELECT *
FROM bestellungen
WHERE DATEPART(year, bestelldatum) = 2024;
```

Dieser Befehl zeigt alle Bestellungen aus dem Jahr 2024.

Besser für große Tabellen ist oft:

```sql
SELECT *
FROM bestellungen
WHERE bestelldatum >= '2024-01-01'
AND bestelldatum < '2025-01-01';
```

Diese Variante kann Indexe besser nutzen.

---

## 15.3 In ORDER BY

```sql
SELECT *
FROM bestellungen
ORDER BY bestelldatum DESC;
```

Oder sortiert nach Monat:

```sql
SELECT *
FROM bestellungen
ORDER BY DATEPART(month, bestelldatum);
```

---

# 16. Praktisches Komplettbeispiel

```sql
SELECT
    bestellung_id,
    kunde,
    bestelldatum,
    CAST(bestelldatum AS DATE) AS nur_datum,
    DATEPART(year, bestelldatum) AS jahr,
    DATEPART(month, bestelldatum) AS monat,
    DATENAME(month, bestelldatum) AS monatsname,
    DATETRUNC(month, bestelldatum) AS monatsbeginn,
    EOMONTH(bestelldatum) AS monatsende,
    DATEADD(day, 7, bestelldatum) AS lieferdatum,
    DATEDIFF(day, bestelldatum, GETDATE()) AS tage_seit_bestellung,
    FORMAT(bestelldatum, 'dd.MM.yyyy') AS datum_formatiert
FROM bestellungen
WHERE bestelldatum >= '2024-01-01'
AND bestelldatum < '2025-01-01'
ORDER BY bestelldatum;
```

### Erklärung

Dieser Befehl:

* zeigt Bestellungen aus dem Jahr 2024,
* extrahiert Jahr und Monat,
* zeigt den Monatsnamen,
* berechnet den Monatsanfang,
* berechnet das Monatsende,
* berechnet ein mögliches Lieferdatum,
* berechnet die Anzahl der Tage seit der Bestellung,
* formatiert das Datum im deutschen Format.

---

# 17. Mini-Übersicht

| Funktion            | Bedeutung                           | Beispiel                      |
| ------------------- | ----------------------------------- | ----------------------------- |
| `GETDATE()`         | Aktuelles Datum mit Uhrzeit         | `GETDATE()`                   |
| `SYSDATETIME()`     | Aktuelles Datum mit genauer Uhrzeit | `SYSDATETIME()`               |
| `CURRENT_TIMESTAMP` | Aktueller Zeitstempel               | `CURRENT_TIMESTAMP`           |
| `DATEPART`          | Datumsteil als Zahl                 | `DATEPART(month, datum)`      |
| `DATENAME`          | Datumsteil als Text                 | `DATENAME(month, datum)`      |
| `DATETRUNC`         | Datum auf Einheit kürzen            | `DATETRUNC(month, datum)`     |
| `EOMONTH`           | Letzter Tag des Monats              | `EOMONTH(datum)`              |
| `FORMAT`            | Wert als Text formatieren           | `FORMAT(datum, 'dd.MM.yyyy')` |
| `CONVERT`           | Datentyp umwandeln                  | `CONVERT(DATE, wert)`         |
| `CAST`              | Datentyp umwandeln                  | `CAST(wert AS DATE)`          |
| `DATEADD`           | Zeit addieren oder abziehen         | `DATEADD(day, 7, datum)`      |
| `DATEDIFF`          | Differenz berechnen                 | `DATEDIFF(day, start, ende)`  |
| `ISDATE`            | Prüft gültiges Datum                | `ISDATE(wert)`                |

---

# 18. Häufige Fehler

## 18.1 Falsche Reihenfolge bei DATEDIFF

Falsch verstanden:

```sql
DATEDIFF(day, enddatum, startdatum)
```

Richtig:

```sql
DATEDIFF(day, startdatum, enddatum)
```

Die Reihenfolge ist wichtig.

---

## 18.2 FORMAT für Berechnungen verwenden

`FORMAT` gibt Text zurück.

Für Berechnungen sollte man echte Datumswerte verwenden, nicht formatierte Texte.

---

## 18.3 Datum als Text vergleichen

Problematisch:

```sql
WHERE FORMAT(bestelldatum, 'yyyy') = '2024'
```

Besser:

```sql
WHERE bestelldatum >= '2024-01-01'
AND bestelldatum < '2025-01-01'
```

---

## 18.4 Sprache bei DATENAME

`DATENAME(month, datum)` kann je nach Spracheinstellung `January` oder `Januar` zurückgeben.

---

## 18.5 Nicht jedes System unterstützt jede Funktion

Viele der genannten Funktionen sind typisch für SQL Server.

Andere Datenbanksysteme haben teilweise andere Namen.

Beispiele:

| SQL Server  | PostgreSQL / MySQL ähnlich |
| ----------- | -------------------------- |
| `DATETRUNC` | PostgreSQL: `DATE_TRUNC`   |
| `GETDATE()` | PostgreSQL: `NOW()`        |
| `EOMONTH`   | teilweise andere Lösungen  |
| `ISDATE`    | teilweise andere Lösungen  |

---

# 19. Merksätze

* `DATEPART` gibt einen Datumsteil als Zahl zurück.
* `DATENAME` gibt einen Datumsteil als Text zurück.
* `DATETRUNC` setzt ein Datum auf den Anfang einer Einheit.
* `EOMONTH` gibt den letzten Tag eines Monats zurück.
* `FORMAT` formatiert Werte als Text.
* `CAST` und `CONVERT` wandeln Datentypen um.
* `CONVERT` kann in SQL Server zusätzlich Format-Styles verwenden.
* `DATEADD` addiert oder subtrahiert Zeit.
* `DATEDIFF` berechnet Differenzen zwischen zwei Datumswerten.
* `ISDATE` prüft, ob ein Wert ein gültiges Datum ist.
* Für Filter nach Jahr oder Monat sind Bereichsvergleiche oft besser als Funktionen in `WHERE`.


# SQL Zusammenfassung – Teil 8: Null Functions

## 1. Was ist `NULL`?

`NULL` bedeutet, dass ein Wert fehlt oder unbekannt ist.

Wichtig:

```sql
NULL
```

ist nicht dasselbe wie:

```sql
0
```

und auch nicht dasselbe wie:

```sql
''
```

Also:

| Wert   | Bedeutung                    |
| ------ | ---------------------------- |
| `NULL` | Kein Wert / unbekannter Wert |
| `0`    | Zahl Null                    |
| `''`   | Leerer Text                  |

---

## 2. Warum sind NULL Functions wichtig?

NULL-Werte können bei Berechnungen und Vergleichen Probleme verursachen.

Beispiel:

```sql
SELECT 100 + NULL AS ergebnis;
```

Das Ergebnis ist:

| ergebnis |
| -------- |
| NULL     |

Wenn ein Wert in einer Berechnung `NULL` ist, wird oft das ganze Ergebnis `NULL`.

Deshalb verwendet man Null Functions, um solche Fälle zu behandeln.

---

# 3. IS NULL

Mit `IS NULL` prüft man, ob ein Wert `NULL` ist.

Man verwendet nicht `= NULL`.

## 3.1 Allgemeine Struktur

```sql
SELECT spalten
FROM tabellenname
WHERE spalte IS NULL;
```

## 3.2 Beispiel

```sql
SELECT *
FROM kunden
WHERE telefon IS NULL;
```

### Erklärung

Dieser Befehl zeigt alle Kunden, bei denen keine Telefonnummer gespeichert ist.

---

## 3.3 Falsches Beispiel

```sql
SELECT *
FROM kunden
WHERE telefon = NULL;
```

Das ist falsch, weil `NULL` nicht mit `=` verglichen wird.

---

# 4. IS NOT NULL

Mit `IS NOT NULL` prüft man, ob ein Wert vorhanden ist.

## 4.1 Allgemeine Struktur

```sql
SELECT spalten
FROM tabellenname
WHERE spalte IS NOT NULL;
```

## 4.2 Beispiel

```sql
SELECT *
FROM kunden
WHERE telefon IS NOT NULL;
```

### Erklärung

Dieser Befehl zeigt alle Kunden, bei denen eine Telefonnummer gespeichert ist.

---

# 5. COALESCE

`COALESCE` gibt den ersten Wert zurück, der nicht `NULL` ist.

## 5.1 Allgemeine Struktur

```sql
COALESCE(wert1, wert2, wert3, ...)
```

SQL prüft die Werte von links nach rechts.

Der erste Wert, der nicht `NULL` ist, wird zurückgegeben.

---

## 5.2 Einfaches Beispiel

```sql
SELECT COALESCE(NULL, NULL, 'Kein Wert') AS ergebnis;
```

### Ergebnis

| ergebnis  |
| --------- |
| Kein Wert |

---

## 5.3 Beispiel mit Tabelle

```sql
SELECT
    kunden_id,
    name,
    COALESCE(telefon, 'Keine Telefonnummer') AS telefon_anzeige
FROM kunden;
```

### Erklärung

Wenn `telefon` einen Wert hat, wird die Telefonnummer angezeigt.

Wenn `telefon` `NULL` ist, wird stattdessen `'Keine Telefonnummer'` angezeigt.

---

## 5.4 COALESCE mit mehreren Spalten

```sql
SELECT
    kunden_id,
    name,
    COALESCE(mobilnummer, telefon, email, 'Keine Kontaktinfo') AS kontakt
FROM kunden;
```

### Erklärung

SQL nimmt den ersten vorhandenen Wert in dieser Reihenfolge:

1. `mobilnummer`
2. `telefon`
3. `email`
4. `'Keine Kontaktinfo'`

---

## 5.5 COALESCE bei Berechnungen

```sql
SELECT
    produktname,
    preis,
    rabatt,
    preis - COALESCE(rabatt, 0) AS endpreis
FROM produkte;
```

### Erklärung

Wenn `rabatt` `NULL` ist, wird stattdessen `0` verwendet.

Dadurch wird die Berechnung nicht `NULL`.

---

# 6. ISNULL

`ISNULL` ersetzt einen `NULL`-Wert durch einen anderen Wert.

Diese Funktion wird häufig in SQL Server verwendet.

## 6.1 Allgemeine Struktur

```sql
ISNULL(wert, ersatzwert)
```

---

## 6.2 Beispiel

```sql
SELECT
    kunden_id,
    name,
    ISNULL(telefon, 'Keine Telefonnummer') AS telefon_anzeige
FROM kunden;
```

### Erklärung

Wenn `telefon` `NULL` ist, wird `'Keine Telefonnummer'` angezeigt.

---

## 6.3 ISNULL bei Zahlen

```sql
SELECT
    produktname,
    preis,
    rabatt,
    preis - ISNULL(rabatt, 0) AS endpreis
FROM produkte;
```

### Erklärung

Wenn `rabatt` `NULL` ist, wird `0` verwendet.

---

## 6.4 Unterschied zwischen COALESCE und ISNULL

| Funktion   | Bedeutung                              | Hinweis                                         |
| ---------- | -------------------------------------- | ----------------------------------------------- |
| `COALESCE` | Gibt den ersten Nicht-NULL-Wert zurück | Standard-SQL, mehrere Werte möglich             |
| `ISNULL`   | Ersetzt NULL durch Ersatzwert          | Typisch für SQL Server, meistens nur zwei Werte |

### Beispiel COALESCE

```sql
SELECT COALESCE(mobilnummer, telefon, email, 'Keine Kontaktinfo')
FROM kunden;
```

### Beispiel ISNULL

```sql
SELECT ISNULL(telefon, 'Keine Telefonnummer')
FROM kunden;
```

---

## 6.5 Hinweis zu Datenbanksystemen

`ISNULL` ist nicht überall gleich.

| Datenbanksystem | Funktion                              |
| --------------- | ------------------------------------- |
| SQL Server      | `ISNULL(wert, ersatzwert)`            |
| MySQL           | meistens `IFNULL(wert, ersatzwert)`   |
| PostgreSQL      | meistens `COALESCE(wert, ersatzwert)` |

Für eine allgemeine SQL-Zusammenfassung ist `COALESCE` oft die bessere und flexiblere Variante.

---

# 7. NULLIF

`NULLIF` vergleicht zwei Werte.

Wenn beide Werte gleich sind, gibt `NULLIF` `NULL` zurück.

Wenn die Werte unterschiedlich sind, gibt `NULLIF` den ersten Wert zurück.

## 7.1 Allgemeine Struktur

```sql
NULLIF(wert1, wert2)
```

---

## 7.2 Beispiel: gleiche Werte

```sql
SELECT NULLIF(10, 10) AS ergebnis;
```

### Ergebnis

| ergebnis |
| -------- |
| NULL     |

---

## 7.3 Beispiel: unterschiedliche Werte

```sql
SELECT NULLIF(10, 5) AS ergebnis;
```

### Ergebnis

| ergebnis |
| -------- |
| 10       |

---

## 7.4 Praktisches Beispiel: Leeren Text als NULL behandeln

```sql
SELECT
    kunden_id,
    name,
    NULLIF(email, '') AS email_bereinigt
FROM kunden;
```

### Erklärung

Wenn `email` ein leerer Text `''` ist, wird daraus `NULL`.

Das ist nützlich, wenn fehlende Werte als leerer Text gespeichert wurden.

---

## 7.5 NULLIF gegen Division durch Null

`NULLIF` wird oft verwendet, um eine Division durch `0` zu vermeiden.

```sql
SELECT
    produktname,
    umsatz,
    menge,
    umsatz / NULLIF(menge, 0) AS preis_pro_stueck
FROM verkauf;
```

### Erklärung

Wenn `menge = 0` ist, wird daraus:

```sql
umsatz / NULL
```

Das Ergebnis ist dann `NULL`, aber es entsteht kein Fehler durch Division durch Null.

---

# 8. Kombination von NULLIF und COALESCE

Man kann `NULLIF` und `COALESCE` kombinieren.

```sql
SELECT
    kunden_id,
    name,
    COALESCE(NULLIF(email, ''), 'Keine E-Mail') AS email_anzeige
FROM kunden;
```

### Erklärung

Dieser Befehl:

* behandelt leere E-Mails `''` als `NULL`,
* ersetzt danach `NULL` durch `'Keine E-Mail'`.

---

# 9. NULL in WHERE-Bedingungen

## 9.1 Kunden ohne Telefonnummer

```sql
SELECT *
FROM kunden
WHERE telefon IS NULL;
```

---

## 9.2 Kunden mit Telefonnummer

```sql
SELECT *
FROM kunden
WHERE telefon IS NOT NULL;
```

---

## 9.3 Kunden ohne Telefonnummer oder ohne E-Mail

```sql
SELECT *
FROM kunden
WHERE telefon IS NULL
OR email IS NULL;
```

---

## 9.4 Kunden mit Telefonnummer und E-Mail

```sql
SELECT *
FROM kunden
WHERE telefon IS NOT NULL
AND email IS NOT NULL;
```

---

# 10. NULL in JOINs

Bei `LEFT JOIN` entstehen oft `NULL`-Werte, wenn es keine passende Zeile in der rechten Tabelle gibt.

## Beispiel: Kunden ohne Bestellung

```sql
SELECT
    k.kunden_id,
    k.name
FROM kunden k
LEFT JOIN bestellungen b
ON k.kunden_id = b.kunden_id
WHERE b.kunden_id IS NULL;
```

### Erklärung

Dieser Befehl zeigt Kunden, die keine Bestellung haben.

Das ist ein typisches Beispiel für einen Anti Join.

---

# 11. NULL in Aggregatfunktionen

Viele Aggregatfunktionen ignorieren `NULL`-Werte.

| Funktion        | Verhalten mit NULL         |
| --------------- | -------------------------- |
| `COUNT(spalte)` | zählt nur Nicht-NULL-Werte |
| `COUNT(*)`      | zählt alle Zeilen          |
| `SUM(spalte)`   | ignoriert NULL             |
| `AVG(spalte)`   | ignoriert NULL             |
| `MIN(spalte)`   | ignoriert NULL             |
| `MAX(spalte)`   | ignoriert NULL             |

---

## 11.1 Beispiel COUNT

```sql
SELECT
    COUNT(*) AS alle_kunden,
    COUNT(telefon) AS kunden_mit_telefon
FROM kunden;
```

### Erklärung

* `COUNT(*)` zählt alle Kunden.
* `COUNT(telefon)` zählt nur Kunden, bei denen `telefon` nicht `NULL` ist.

---

## 11.2 Beispiel AVG mit COALESCE

```sql
SELECT AVG(COALESCE(rabatt, 0)) AS durchschnittlicher_rabatt
FROM produkte;
```

### Erklärung

Hier werden fehlende Rabatte als `0` behandelt.

Ohne `COALESCE` würden `NULL`-Werte ignoriert werden.

---

# 12. Häufige Fehler

## 12.1 `= NULL` verwenden

Falsch:

```sql
SELECT *
FROM kunden
WHERE telefon = NULL;
```

Richtig:

```sql
SELECT *
FROM kunden
WHERE telefon IS NULL;
```

---

## 12.2 `<> NULL` verwenden

Falsch:

```sql
SELECT *
FROM kunden
WHERE telefon <> NULL;
```

Richtig:

```sql
SELECT *
FROM kunden
WHERE telefon IS NOT NULL;
```

---

## 12.3 NULL bei Berechnungen vergessen

Problematisch:

```sql
SELECT preis - rabatt AS endpreis
FROM produkte;
```

Wenn `rabatt` `NULL` ist, wird `endpreis` auch `NULL`.

Besser:

```sql
SELECT preis - COALESCE(rabatt, 0) AS endpreis
FROM produkte;
```

---

## 12.4 `NOT IN` mit NULL

Bei `NOT IN` muss man vorsichtig sein, wenn die Unterabfrage `NULL` enthält.

Problematisch:

```sql
SELECT *
FROM kunden
WHERE kunden_id NOT IN (
    SELECT kunden_id
    FROM bestellungen
);
```

Wenn in der Unterabfrage `NULL` vorkommt, kann das Ergebnis unerwartet sein.

Besser ist oft:

```sql
SELECT *
FROM kunden k
WHERE NOT EXISTS (
    SELECT 1
    FROM bestellungen b
    WHERE b.kunden_id = k.kunden_id
);
```

---

# 13. Mini-Übersicht

| Funktion / Operator | Bedeutung                                     | Beispiel                                 |
| ------------------- | --------------------------------------------- | ---------------------------------------- |
| `IS NULL`           | Prüft, ob ein Wert NULL ist                   | `telefon IS NULL`                        |
| `IS NOT NULL`       | Prüft, ob ein Wert vorhanden ist              | `telefon IS NOT NULL`                    |
| `COALESCE`          | Gibt den ersten Nicht-NULL-Wert zurück        | `COALESCE(telefon, email, 'Keine Info')` |
| `ISNULL`            | Ersetzt NULL durch Ersatzwert                 | `ISNULL(telefon, 'Keine Telefonnummer')` |
| `NULLIF`            | Gibt NULL zurück, wenn zwei Werte gleich sind | `NULLIF(menge, 0)`                       |

---

# 14. Praktisches Komplettbeispiel

```sql
SELECT
    kunden_id,
    name,
    COALESCE(NULLIF(email, ''), 'Keine E-Mail') AS email_anzeige,
    COALESCE(telefon, 'Keine Telefonnummer') AS telefon_anzeige
FROM kunden
WHERE telefon IS NULL
OR email IS NULL
OR email = '';
```

### Erklärung

Dieser Befehl:

* zeigt Kunden mit fehlenden Kontaktdaten,
* behandelt leere E-Mail-Felder als fehlende Werte,
* ersetzt fehlende E-Mails durch `'Keine E-Mail'`,
* ersetzt fehlende Telefonnummern durch `'Keine Telefonnummer'`.

---

# 15. Merksätze

* `NULL` bedeutet: Wert fehlt oder ist unbekannt.
* `NULL` ist nicht gleich `0`.
* `NULL` ist nicht gleich leerer Text `''`.
* Für `NULL` verwendet man `IS NULL`, nicht `= NULL`.
* Für vorhandene Werte verwendet man `IS NOT NULL`, nicht `<> NULL`.
* `COALESCE` gibt den ersten Wert zurück, der nicht `NULL` ist.
* `ISNULL` ersetzt `NULL` durch einen Ersatzwert.
* `NULLIF` gibt `NULL` zurück, wenn zwei Werte gleich sind.
* Bei Berechnungen mit `NULL` wird oft das Ergebnis auch `NULL`.
* Mit `COALESCE` oder `ISNULL` kann man fehlende Werte ersetzen.


# SQL Zusammenfassung – Teil 9: CASE WHEN

## 1. Was ist `CASE WHEN`?

`CASE WHEN` wird verwendet, um Bedingungen in SQL zu prüfen und abhängig davon unterschiedliche Werte zurückzugeben.

Man kann es sich ähnlich vorstellen wie:

```text
WENN Bedingung erfüllt ist, DANN gib diesen Wert zurück,
SONST gib einen anderen Wert zurück.
```

`CASE WHEN` wird häufig verwendet in:

* `SELECT`
* `ORDER BY`
* `GROUP BY`
* `UPDATE`
* Aggregatfunktionen wie `SUM` oder `COUNT`

---

## 2. Allgemeine Struktur

```sql
CASE
    WHEN bedingung1 THEN ergebnis1
    WHEN bedingung2 THEN ergebnis2
    ELSE anderes_ergebnis
END
```

### Beispiel

```sql
SELECT
    name,
    alter,
    CASE
        WHEN alter >= 18 THEN 'Volljährig'
        ELSE 'Minderjährig'
    END AS altersgruppe
FROM kunden;
```

### Erklärung

Dieser Befehl prüft das Alter:

* Wenn `alter >= 18`, dann wird `'Volljährig'` ausgegeben.
* Sonst wird `'Minderjährig'` ausgegeben.
* Die neue Spalte heißt `altersgruppe`.

---

## 3. Beispiel mit mehreren Bedingungen

```sql
SELECT
    produktname,
    preis,
    CASE
        WHEN preis < 50 THEN 'Günstig'
        WHEN preis BETWEEN 50 AND 200 THEN 'Mittel'
        WHEN preis > 200 THEN 'Teuer'
        ELSE 'Unbekannt'
    END AS preiskategorie
FROM produkte;
```

### Erklärung

| Bedingung                  | Ergebnis    |
| -------------------------- | ----------- |
| `preis < 50`               | `Günstig`   |
| `preis BETWEEN 50 AND 200` | `Mittel`    |
| `preis > 200`              | `Teuer`     |
| sonst                      | `Unbekannt` |

---

## 4. Reihenfolge der Bedingungen ist wichtig

SQL prüft die Bedingungen von oben nach unten.

Sobald eine Bedingung wahr ist, wird das passende Ergebnis zurückgegeben.

```sql
SELECT
    name,
    punkte,
    CASE
        WHEN punkte >= 90 THEN 'Sehr gut'
        WHEN punkte >= 75 THEN 'Gut'
        WHEN punkte >= 50 THEN 'Bestanden'
        ELSE 'Nicht bestanden'
    END AS bewertung
FROM pruefungen;
```

### Erklärung

Wenn jemand `95` Punkte hat:

* `punkte >= 90` ist wahr
* Ergebnis: `Sehr gut`
* Die weiteren Bedingungen werden nicht mehr geprüft.

---

## 5. CASE WHEN ohne ELSE

`ELSE` ist optional.

Wenn keine Bedingung erfüllt ist und kein `ELSE` vorhanden ist, gibt SQL `NULL` zurück.

```sql
SELECT
    name,
    alter,
    CASE
        WHEN alter >= 18 THEN 'Volljährig'
    END AS status
FROM kunden;
```

Wenn `alter < 18` ist, wird `NULL` ausgegeben.

Besser ist meistens:

```sql
SELECT
    name,
    alter,
    CASE
        WHEN alter >= 18 THEN 'Volljährig'
        ELSE 'Minderjährig'
    END AS status
FROM kunden;
```

---

## 6. Simple CASE

Neben `CASE WHEN` gibt es auch die einfache `CASE`-Form.

Diese Form vergleicht eine Spalte mit mehreren festen Werten.

### Struktur

```sql
CASE spalte
    WHEN wert1 THEN ergebnis1
    WHEN wert2 THEN ergebnis2
    ELSE anderes_ergebnis
END
```

### Beispiel

```sql
SELECT
    name,
    land,
    CASE land
        WHEN 'DE' THEN 'Deutschland'
        WHEN 'FR' THEN 'Frankreich'
        WHEN 'ES' THEN 'Spanien'
        ELSE 'Unbekannt'
    END AS land_name
FROM kunden;
```

### Unterschied

| Form               | Verwendung                  |
| ------------------ | --------------------------- |
| `CASE WHEN`        | Für komplexe Bedingungen    |
| `CASE spalte WHEN` | Für einfache Wertvergleiche |

---

## 7. CASE WHEN mit NULL

Für `NULL` muss man `IS NULL` verwenden.

```sql
SELECT
    name,
    telefon,
    CASE
        WHEN telefon IS NULL THEN 'Keine Telefonnummer'
        ELSE telefon
    END AS telefon_anzeige
FROM kunden;
```

Das ist ähnlich wie:

```sql
SELECT
    name,
    COALESCE(telefon, 'Keine Telefonnummer') AS telefon_anzeige
FROM kunden;
```

---

## 8. CASE WHEN mit ORDER BY

`CASE WHEN` kann verwendet werden, um eigene Sortierlogik zu definieren.

```sql
SELECT
    name,
    status
FROM kunden
ORDER BY
    CASE
        WHEN status = 'VIP' THEN 1
        WHEN status = 'Aktiv' THEN 2
        WHEN status = 'Inaktiv' THEN 3
        ELSE 4
    END;
```

### Erklärung

Die Sortierung erfolgt in dieser Reihenfolge:

1. VIP
2. Aktiv
3. Inaktiv
4. alle anderen

---

## 9. CASE WHEN mit UPDATE

`CASE WHEN` kann auch verwendet werden, um Werte in einer Tabelle zu aktualisieren.

```sql
UPDATE kunden
SET status =
    CASE
        WHEN umsatz >= 1000 THEN 'VIP'
        WHEN umsatz >= 100 THEN 'Aktiv'
        ELSE 'Normal'
    END;
```

### Erklärung

Der `status` wird abhängig vom Umsatz gesetzt.

Wichtig: Ohne `WHERE` werden alle Zeilen aktualisiert.

Mit `WHERE` kann man einschränken:

```sql
UPDATE kunden
SET status =
    CASE
        WHEN umsatz >= 1000 THEN 'VIP'
        WHEN umsatz >= 100 THEN 'Aktiv'
        ELSE 'Normal'
    END
WHERE land = 'Deutschland';
```

---

## 10. CASE WHEN mit Aggregatfunktionen

`CASE WHEN` wird oft für bedingte Berechnungen verwendet.

### Beispiel: Anzahl aktiver Kunden zählen

```sql
SELECT
    COUNT(CASE WHEN status = 'Aktiv' THEN 1 END) AS aktive_kunden
FROM kunden;
```

### Beispiel: Umsatz nach Bedingung summieren

```sql
SELECT
    SUM(
        CASE
            WHEN land = 'Deutschland' THEN umsatz
            ELSE 0
        END
    ) AS umsatz_deutschland
FROM kunden;
```

### Erklärung

Nur Umsätze aus Deutschland werden addiert.

Alle anderen Zeilen zählen als `0`.

---

## 11. CASE WHEN mit GROUP BY

```sql
SELECT
    CASE
        WHEN preis < 50 THEN 'Günstig'
        WHEN preis BETWEEN 50 AND 200 THEN 'Mittel'
        ELSE 'Teuer'
    END AS preiskategorie,
    COUNT(*) AS anzahl_produkte
FROM produkte
GROUP BY
    CASE
        WHEN preis < 50 THEN 'Günstig'
        WHEN preis BETWEEN 50 AND 200 THEN 'Mittel'
        ELSE 'Teuer'
    END;
```

### Erklärung

Produkte werden zuerst in Kategorien eingeteilt.

Danach wird gezählt, wie viele Produkte pro Kategorie existieren.

---

## 12. CASE WHEN mit mehreren Spalten

```sql
SELECT
    name,
    alter,
    stadt,
    CASE
        WHEN alter >= 18 AND stadt = 'Düsseldorf' THEN 'Erwachsen in Düsseldorf'
        WHEN alter < 18 AND stadt = 'Düsseldorf' THEN 'Minderjährig in Düsseldorf'
        ELSE 'Andere Gruppe'
    END AS gruppe
FROM kunden;
```

Hier werden mehrere Bedingungen kombiniert.

---

## 13. Verschachteltes CASE WHEN

Man kann `CASE WHEN` auch verschachteln.

```sql
SELECT
    name,
    status,
    umsatz,
    CASE
        WHEN status = 'Aktiv' THEN
            CASE
                WHEN umsatz >= 1000 THEN 'Aktiver VIP-Kunde'
                ELSE 'Aktiver Kunde'
            END
        ELSE 'Nicht aktiv'
    END AS kundenklasse
FROM kunden;
```

### Hinweis

Verschachtelte `CASE WHEN`-Abfragen können schnell unübersichtlich werden.

Wenn möglich, sollte man die Logik einfach halten.

---

## 14. Häufige Fehler

### 14.1 `END` vergessen

Falsch:

```sql
SELECT
    name,
    CASE
        WHEN alter >= 18 THEN 'Volljährig'
        ELSE 'Minderjährig'
FROM kunden;
```

Richtig:

```sql
SELECT
    name,
    CASE
        WHEN alter >= 18 THEN 'Volljährig'
        ELSE 'Minderjährig'
    END AS status
FROM kunden;
```

---

### 14.2 Alias vergessen

Ohne Alias hat die berechnete Spalte oft keinen schönen Namen.

Besser:

```sql
SELECT
    name,
    CASE
        WHEN alter >= 18 THEN 'Volljährig'
        ELSE 'Minderjährig'
    END AS altersstatus
FROM kunden;
```

---

### 14.3 Falscher Vergleich mit NULL

Falsch:

```sql
CASE
    WHEN telefon = NULL THEN 'Keine Telefonnummer'
    ELSE telefon
END
```

Richtig:

```sql
CASE
    WHEN telefon IS NULL THEN 'Keine Telefonnummer'
    ELSE telefon
END
```

---

### 14.4 Unterschiedliche Datentypen im Ergebnis

Problematisch:

```sql
CASE
    WHEN preis > 100 THEN 'Teuer'
    ELSE 0
END
```

Hier wird einmal Text und einmal Zahl zurückgegeben.

Besser:

```sql
CASE
    WHEN preis > 100 THEN 'Teuer'
    ELSE 'Nicht teuer'
END
```

Die Ergebnisse sollten möglichst denselben oder kompatiblen Datentyp haben.

---

## 15. Mini-Übersicht

| Bestandteil | Bedeutung                               |
| ----------- | --------------------------------------- |
| `CASE`      | Start der Bedingungslogik               |
| `WHEN`      | Bedingung                               |
| `THEN`      | Ergebnis, wenn Bedingung wahr ist       |
| `ELSE`      | Ergebnis, wenn keine Bedingung wahr ist |
| `END`       | Ende der CASE-Logik                     |
| `AS`        | Alias für die neue Spalte               |

---

## 16. Praktisches Komplettbeispiel

```sql
SELECT
    kunden_id,
    name,
    stadt,
    umsatz,
    CASE
        WHEN umsatz >= 1000 THEN 'VIP'
        WHEN umsatz >= 500 THEN 'Premium'
        WHEN umsatz > 0 THEN 'Normal'
        ELSE 'Kein Umsatz'
    END AS kundenstatus,
    CASE
        WHEN telefon IS NULL THEN 'Telefon fehlt'
        ELSE 'Telefon vorhanden'
    END AS telefon_status
FROM kunden
WHERE stadt IN ('Düsseldorf', 'Köln')
ORDER BY
    CASE
        WHEN umsatz >= 1000 THEN 1
        WHEN umsatz >= 500 THEN 2
        WHEN umsatz > 0 THEN 3
        ELSE 4
    END;
```

### Erklärung

Dieser Befehl:

* zeigt Kunden aus Düsseldorf oder Köln,
* kategorisiert Kunden nach Umsatz,
* prüft, ob eine Telefonnummer vorhanden ist,
* sortiert Kunden nach eigener Priorität.

---

## 17. Merksätze

* `CASE WHEN` erzeugt bedingte Ausgaben in SQL.
* SQL prüft Bedingungen von oben nach unten.
* Die erste wahre Bedingung gewinnt.
* `ELSE` ist optional, aber empfehlenswert.
* Ohne `ELSE` wird `NULL` zurückgegeben, wenn keine Bedingung passt.
* `END` beendet die CASE-Logik.
* Mit `AS` gibt man der neuen Spalte einen Namen.
* Für `NULL` verwendet man `IS NULL`, nicht `= NULL`.
* `CASE WHEN` kann in `SELECT`, `ORDER BY`, `GROUP BY` und `UPDATE` verwendet werden.


# SQL Zusammenfassung – Teil 10: Aggregate Functions

## 1. Was sind Aggregate Functions?

`Aggregate Functions` berechnen einen Wert aus mehreren Zeilen.

Sie werden häufig verwendet, um Daten zusammenzufassen.

Beispiele:

* Anzahl berechnen
* Summe berechnen
* Durchschnitt berechnen
* Minimum finden
* Maximum finden

Wichtige Aggregate Functions:

| Funktion | Bedeutung                      |
| -------- | ------------------------------ |
| `COUNT`  | zählt Zeilen                   |
| `SUM`    | berechnet die Summe            |
| `AVG`    | berechnet den Durchschnitt     |
| `MIN`    | gibt den kleinsten Wert zurück |
| `MAX`    | gibt den größten Wert zurück   |

---

## 2. COUNT

`COUNT` zählt Datensätze.

### Alle Zeilen zählen

```sql
SELECT COUNT(*) AS anzahl_kunden
FROM kunden;
```

### Nur Nicht-NULL-Werte zählen

```sql
SELECT COUNT(telefon) AS kunden_mit_telefon
FROM kunden;
```

### Unterschied

| Ausdruck        | Bedeutung                                               |
| --------------- | ------------------------------------------------------- |
| `COUNT(*)`      | zählt alle Zeilen                                       |
| `COUNT(spalte)` | zählt nur Zeilen, bei denen die Spalte nicht `NULL` ist |

---

## 3. SUM

`SUM` berechnet die Summe einer numerischen Spalte.

```sql
SELECT SUM(umsatz) AS gesamtumsatz
FROM kunden;
```

Beispiel:

| umsatz |
| -----: |
|    100 |
|    200 |
|    300 |

Ergebnis:

| gesamtumsatz |
| -----------: |
|          600 |

---

## 4. AVG

`AVG` berechnet den Durchschnitt.

```sql
SELECT AVG(preis) AS durchschnittspreis
FROM produkte;
```

Optional mit `ROUND`:

```sql
SELECT ROUND(AVG(preis), 2) AS durchschnittspreis
FROM produkte;
```

---

## 5. MIN

`MIN` gibt den kleinsten Wert zurück.

```sql
SELECT MIN(preis) AS niedrigster_preis
FROM produkte;
```

---

## 6. MAX

`MAX` gibt den größten Wert zurück.

```sql
SELECT MAX(preis) AS hoechster_preis
FROM produkte;
```

---

## 7. Aggregate Functions mit WHERE

Mit `WHERE` kann man die Daten vor der Berechnung filtern.

```sql
SELECT SUM(umsatz) AS umsatz_deutschland
FROM kunden
WHERE land = 'Deutschland';
```

### Erklärung

Zuerst werden nur Kunden aus Deutschland gefiltert.

Danach wird die Summe berechnet.

---

## 8. Aggregate Functions mit GROUP BY

`GROUP BY` gruppiert Zeilen.

Die Aggregate Function wird dann pro Gruppe berechnet.

### Beispiel: Umsatz pro Stadt

```sql
SELECT
    stadt,
    SUM(umsatz) AS gesamtumsatz
FROM kunden
GROUP BY stadt;
```

### Beispielergebnis

| stadt      | gesamtumsatz |
| ---------- | -----------: |
| Düsseldorf |         1200 |
| Köln       |          900 |
| Berlin     |          700 |

---

## 9. GROUP BY mit mehreren Aggregaten

```sql
SELECT
    stadt,
    COUNT(*) AS anzahl_kunden,
    SUM(umsatz) AS gesamtumsatz,
    AVG(umsatz) AS durchschnittsumsatz,
    MIN(umsatz) AS kleinster_umsatz,
    MAX(umsatz) AS groesster_umsatz
FROM kunden
GROUP BY stadt;
```

---

## 10. HAVING

`HAVING` filtert Gruppen nach der Aggregation.

```sql
SELECT
    stadt,
    SUM(umsatz) AS gesamtumsatz
FROM kunden
GROUP BY stadt
HAVING SUM(umsatz) > 1000;
```

### Unterschied zwischen WHERE und HAVING

| Clause   | Bedeutung                            |
| -------- | ------------------------------------ |
| `WHERE`  | filtert Zeilen vor der Gruppierung   |
| `HAVING` | filtert Gruppen nach der Gruppierung |

---

## 11. COUNT DISTINCT

Mit `COUNT DISTINCT` zählt man eindeutige Werte.

```sql
SELECT COUNT(DISTINCT stadt) AS anzahl_staedte
FROM kunden;
```

### Erklärung

Wenn mehrere Kunden aus derselben Stadt kommen, wird die Stadt trotzdem nur einmal gezählt.

---

## 12. NULL bei Aggregate Functions

Viele Aggregate Functions ignorieren `NULL`-Werte.

| Funktion        | Verhalten         |
| --------------- | ----------------- |
| `COUNT(*)`      | zählt alle Zeilen |
| `COUNT(spalte)` | ignoriert `NULL`  |
| `SUM(spalte)`   | ignoriert `NULL`  |
| `AVG(spalte)`   | ignoriert `NULL`  |
| `MIN(spalte)`   | ignoriert `NULL`  |
| `MAX(spalte)`   | ignoriert `NULL`  |

---

## 13. Reihenfolge bei normalen Aggregationen

Typische Reihenfolge:

```sql
SELECT
    spalte,
    AGG_FUNCTION(spalte)
FROM tabelle
WHERE bedingung
GROUP BY spalte
HAVING aggregat_bedingung
ORDER BY spalte;
```

---

## 14. Mini-Beispiel komplett

```sql
SELECT
    land,
    COUNT(*) AS anzahl_kunden,
    SUM(umsatz) AS gesamtumsatz,
    ROUND(AVG(umsatz), 2) AS durchschnittsumsatz,
    MIN(umsatz) AS kleinster_umsatz,
    MAX(umsatz) AS groesster_umsatz
FROM kunden
WHERE umsatz IS NOT NULL
GROUP BY land
HAVING SUM(umsatz) > 1000
ORDER BY gesamtumsatz DESC;
```

### Erklärung

Dieser Befehl:

* filtert Kunden mit vorhandenem Umsatz,
* gruppiert nach Land,
* berechnet Anzahl, Summe, Durchschnitt, Minimum und Maximum,
* zeigt nur Länder mit mehr als `1000` Gesamtumsatz,
* sortiert nach Gesamtumsatz absteigend.

---

## 15. Merksätze

* Aggregate Functions fassen mehrere Zeilen zu einem Ergebnis zusammen.
* `COUNT(*)` zählt alle Zeilen.
* `COUNT(spalte)` zählt nur Nicht-NULL-Werte.
* `SUM` berechnet Summen.
* `AVG` berechnet Durchschnittswerte.
* `MIN` findet den kleinsten Wert.
* `MAX` findet den größten Wert.
* `GROUP BY` berechnet Aggregate pro Gruppe.
* `HAVING` filtert Gruppen nach der Aggregation.
* `WHERE` kommt vor `GROUP BY`, `HAVING` kommt danach.


# SQL Zusammenfassung – Teil 11: Window Functions – Aggregate Window Functions

## 1. Was sind Window Functions?

`Window Functions` berechnen Werte über mehrere Zeilen, ohne die einzelnen Zeilen zusammenzufassen.

Der wichtigste Unterschied zu normalen Aggregate Functions ist:

| Normale Aggregation                          | Window Function                            |
| -------------------------------------------- | ------------------------------------------ |
| fasst mehrere Zeilen zu einer Zeile zusammen | behält alle Zeilen                         |
| verwendet oft `GROUP BY`                     | verwendet `OVER()`                         |
| Ergebnis ist pro Gruppe eine Zeile           | Ergebnis bleibt pro Originalzeile erhalten |

---

## 2. Arten von Window Functions

Window Functions kann man grob in drei Gruppen einteilen:

| Kategorie                  | Beispiele                                   |
| -------------------------- | ------------------------------------------- |
| Aggregate Window Functions | `SUM`, `AVG`, `COUNT`, `MIN`, `MAX`         |
| Ranking Window Functions   | `ROW_NUMBER`, `RANK`, `DENSE_RANK`, `NTILE` |
| Value Window Functions     | `LAG`, `LEAD`, `FIRST_VALUE`, `LAST_VALUE`  |

In diesem Teil geht es nur um **Aggregate Window Functions**.

---

# 3. Grundstruktur

```sql
SELECT
    spalte1,
    spalte2,
    AGG_FUNCTION(spalte) OVER (
        PARTITION BY gruppierung
        ORDER BY sortierung
    ) AS neuer_spaltenname
FROM tabelle;
```

Wichtige Bestandteile:

| Bestandteil      | Bedeutung                                           |
| ---------------- | --------------------------------------------------- |
| `AGG_FUNCTION`   | z. B. `SUM`, `AVG`, `COUNT`, `MIN`, `MAX`           |
| `OVER()`         | macht aus der Aggregatfunktion eine Window Function |
| `PARTITION BY`   | teilt die Daten in Gruppen/Fenster                  |
| `ORDER BY`       | sortiert Zeilen innerhalb des Fensters              |
| `ROWS` / `RANGE` | definiert genauer, welche Zeilen berechnet werden   |

---

# 4. Beispiel-Tabelle

Tabelle: `verkaeufe`

| verkauf_id | kunde | land        | datum      | betrag |
| ---------- | ----- | ----------- | ---------- | -----: |
| 1          | Ali   | Deutschland | 2024-01-01 |    100 |
| 2          | Sara  | Deutschland | 2024-01-03 |    200 |
| 3          | Omar  | Frankreich  | 2024-01-04 |    150 |
| 4          | Lina  | Deutschland | 2024-01-05 |    300 |
| 5          | Adam  | Frankreich  | 2024-01-06 |    250 |

---

# 5. SUM als Window Function

Mit `SUM() OVER()` kann man Summen berechnen, ohne Zeilen zusammenzufassen.

## 5.1 Gesamtsumme neben jeder Zeile anzeigen

```sql
SELECT
    verkauf_id,
    kunde,
    land,
    betrag,
    SUM(betrag) OVER () AS gesamtumsatz
FROM verkaeufe;
```

### Erklärung

`SUM(betrag) OVER ()` berechnet die Summe über alle Zeilen.

Die Originalzeilen bleiben erhalten.

### Ergebnisidee

| kunde | land        | betrag | gesamtumsatz |
| ----- | ----------- | -----: | -----------: |
| Ali   | Deutschland |    100 |         1000 |
| Sara  | Deutschland |    200 |         1000 |
| Omar  | Frankreich  |    150 |         1000 |
| Lina  | Deutschland |    300 |         1000 |
| Adam  | Frankreich  |    250 |         1000 |

---

## 5.2 Summe pro Gruppe mit PARTITION BY

```sql
SELECT
    verkauf_id,
    kunde,
    land,
    betrag,
    SUM(betrag) OVER (PARTITION BY land) AS umsatz_pro_land
FROM verkaeufe;
```

### Erklärung

`PARTITION BY land` bedeutet:

* Berechne die Summe separat pro Land.
* Die einzelnen Zeilen bleiben trotzdem sichtbar.

### Ergebnisidee

| kunde | land        | betrag | umsatz_pro_land |
| ----- | ----------- | -----: | --------------: |
| Ali   | Deutschland |    100 |             600 |
| Sara  | Deutschland |    200 |             600 |
| Lina  | Deutschland |    300 |             600 |
| Omar  | Frankreich  |    150 |             400 |
| Adam  | Frankreich  |    250 |             400 |

---

# 6. Running Total mit SUM und ORDER BY

Ein **Running Total** ist eine laufende Summe.

```sql
SELECT
    verkauf_id,
    kunde,
    datum,
    betrag,
    SUM(betrag) OVER (
        ORDER BY datum
    ) AS laufende_summe
FROM verkaeufe;
```

### Erklärung

Die Summe wird nach Datum Schritt für Schritt aufgebaut.

### Ergebnisidee

| datum      | kunde | betrag | laufende_summe |
| ---------- | ----- | -----: | -------------: |
| 2024-01-01 | Ali   |    100 |            100 |
| 2024-01-03 | Sara  |    200 |            300 |
| 2024-01-04 | Omar  |    150 |            450 |
| 2024-01-05 | Lina  |    300 |            750 |
| 2024-01-06 | Adam  |    250 |           1000 |

---

## 6.1 Running Total pro Gruppe

```sql
SELECT
    verkauf_id,
    kunde,
    land,
    datum,
    betrag,
    SUM(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
    ) AS laufende_summe_pro_land
FROM verkaeufe;
```

### Erklärung

Hier wird die laufende Summe separat pro Land berechnet.

---

# 7. AVG als Window Function

Mit `AVG() OVER()` berechnet man Durchschnittswerte, ohne die Zeilen zu gruppieren.

## 7.1 Durchschnitt über alle Zeilen

```sql
SELECT
    kunde,
    betrag,
    AVG(betrag) OVER () AS durchschnitt_gesamt
FROM verkaeufe;
```

---

## 7.2 Durchschnitt pro Gruppe

```sql
SELECT
    kunde,
    land,
    betrag,
    AVG(betrag) OVER (PARTITION BY land) AS durchschnitt_pro_land
FROM verkaeufe;
```

### Erklärung

Für jedes Land wird ein eigener Durchschnitt berechnet.

---

# 8. COUNT als Window Function

Mit `COUNT() OVER()` kann man zählen, ohne die Zeilen zusammenzufassen.

## 8.1 Gesamtanzahl der Zeilen

```sql
SELECT
    kunde,
    land,
    betrag,
    COUNT(*) OVER () AS anzahl_gesamt
FROM verkaeufe;
```

---

## 8.2 Anzahl pro Gruppe

```sql
SELECT
    kunde,
    land,
    betrag,
    COUNT(*) OVER (PARTITION BY land) AS anzahl_pro_land
FROM verkaeufe;
```

### Erklärung

Dieser Befehl zeigt bei jeder Zeile, wie viele Verkäufe es im jeweiligen Land gibt.

---

# 9. MIN und MAX als Window Function

Mit `MIN() OVER()` und `MAX() OVER()` kann man kleinste und größte Werte innerhalb eines Fensters anzeigen.

## 9.1 Minimum und Maximum pro Land

```sql
SELECT
    kunde,
    land,
    betrag,
    MIN(betrag) OVER (PARTITION BY land) AS kleinster_betrag_land,
    MAX(betrag) OVER (PARTITION BY land) AS groesster_betrag_land
FROM verkaeufe;
```

### Erklärung

Für jedes Land wird angezeigt:

* kleinster Betrag
* größter Betrag

Die Originalzeilen bleiben erhalten.

---

# 10. Prozentanteil am Gesamtumsatz

Window Functions sind sehr praktisch, um Anteile zu berechnen.

```sql
SELECT
    kunde,
    betrag,
    SUM(betrag) OVER () AS gesamtumsatz,
    ROUND(
        betrag * 100.0 / SUM(betrag) OVER (),
        2
    ) AS anteil_am_gesamtumsatz
FROM verkaeufe;
```

### Erklärung

Dieser Befehl berechnet:

```text
betrag / gesamtumsatz * 100
```

So sieht man, wie viel Prozent jede Zeile zum Gesamtumsatz beiträgt.

---

# 11. Prozentanteil innerhalb einer Gruppe

```sql
SELECT
    kunde,
    land,
    betrag,
    SUM(betrag) OVER (PARTITION BY land) AS umsatz_pro_land,
    ROUND(
        betrag * 100.0 / SUM(betrag) OVER (PARTITION BY land),
        2
    ) AS anteil_im_land
FROM verkaeufe;
```

### Erklärung

Hier wird der Anteil eines Verkaufs innerhalb seines Landes berechnet.

---

# 12. Moving Average

Ein **Moving Average** ist ein gleitender Durchschnitt.

Beispiel: Durchschnitt aus aktueller Zeile und den zwei vorherigen Zeilen.

```sql
SELECT
    datum,
    betrag,
    AVG(betrag) OVER (
        ORDER BY datum
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS moving_average
FROM verkaeufe;
```

### Erklärung

Für jede Zeile wird der Durchschnitt berechnet aus:

* den zwei vorherigen Zeilen
* der aktuellen Zeile

---

# 13. Window Frame: ROWS BETWEEN

Mit `ROWS BETWEEN` kann man genauer bestimmen, welche Zeilen im Fenster berücksichtigt werden.

## 13.1 Wichtige Varianten

| Ausdruck                                                   | Bedeutung                                    |
| ---------------------------------------------------------- | -------------------------------------------- |
| `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`         | von der ersten Zeile bis zur aktuellen Zeile |
| `ROWS BETWEEN 1 PRECEDING AND CURRENT ROW`                 | vorherige Zeile und aktuelle Zeile           |
| `ROWS BETWEEN 2 PRECEDING AND CURRENT ROW`                 | zwei vorherige Zeilen und aktuelle Zeile     |
| `ROWS BETWEEN CURRENT ROW AND 1 FOLLOWING`                 | aktuelle Zeile und nächste Zeile             |
| `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING` | alle Zeilen im Fenster                       |

---

## 13.2 Beispiel: laufende Summe mit explizitem Frame

```sql
SELECT
    datum,
    betrag,
    SUM(betrag) OVER (
        ORDER BY datum
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS laufende_summe
FROM verkaeufe;
```

### Erklärung

Das ist eine klare Schreibweise für eine laufende Summe.

---

# 14. Unterschied: GROUP BY vs Window Function

## GROUP BY

```sql
SELECT
    land,
    SUM(betrag) AS umsatz_pro_land
FROM verkaeufe
GROUP BY land;
```

### Ergebnis

| land        | umsatz_pro_land |
| ----------- | --------------: |
| Deutschland |             600 |
| Frankreich  |             400 |

Hier gibt es nur eine Zeile pro Land.

---

## Window Function

```sql
SELECT
    kunde,
    land,
    betrag,
    SUM(betrag) OVER (PARTITION BY land) AS umsatz_pro_land
FROM verkaeufe;
```

### Ergebnis

| kunde | land        | betrag | umsatz_pro_land |
| ----- | ----------- | -----: | --------------: |
| Ali   | Deutschland |    100 |             600 |
| Sara  | Deutschland |    200 |             600 |
| Lina  | Deutschland |    300 |             600 |
| Omar  | Frankreich  |    150 |             400 |
| Adam  | Frankreich  |    250 |             400 |

Hier bleiben alle Originalzeilen erhalten.

---

# 15. Window Functions mit ORDER BY

`ORDER BY` innerhalb von `OVER()` ist nicht dasselbe wie `ORDER BY` am Ende der Abfrage.

```sql
SELECT
    kunde,
    datum,
    betrag,
    SUM(betrag) OVER (ORDER BY datum) AS laufende_summe
FROM verkaeufe
ORDER BY kunde;
```

### Erklärung

| Ausdruck                     | Bedeutung                                   |
| ---------------------------- | ------------------------------------------- |
| `ORDER BY datum` in `OVER()` | bestimmt die Berechnung der laufenden Summe |
| `ORDER BY kunde` am Ende     | sortiert das finale Ergebnis                |

---

# 16. Window Functions können nicht direkt in WHERE verwendet werden

Window Functions werden nach `WHERE` berechnet.

Deshalb funktioniert so etwas meistens nicht:

```sql
SELECT
    kunde,
    betrag,
    SUM(betrag) OVER (PARTITION BY land) AS umsatz_pro_land
FROM verkaeufe
WHERE SUM(betrag) OVER (PARTITION BY land) > 500;
```

Besser mit einer Subquery oder CTE:

```sql
WITH daten AS (
    SELECT
        kunde,
        land,
        betrag,
        SUM(betrag) OVER (PARTITION BY land) AS umsatz_pro_land
    FROM verkaeufe
)
SELECT *
FROM daten
WHERE umsatz_pro_land > 500;
```

---

# 17. Häufige Aggregate Window Functions

| Funktion         | Bedeutung                          |
| ---------------- | ---------------------------------- |
| `SUM() OVER()`   | Summe als Window Function          |
| `AVG() OVER()`   | Durchschnitt als Window Function   |
| `COUNT() OVER()` | Anzahl als Window Function         |
| `MIN() OVER()`   | kleinster Wert als Window Function |
| `MAX() OVER()`   | größter Wert als Window Function   |

---

# 18. Praktisches Komplettbeispiel

```sql
SELECT
    verkauf_id,
    kunde,
    land,
    datum,
    betrag,

    SUM(betrag) OVER () AS gesamtumsatz,

    SUM(betrag) OVER (
        PARTITION BY land
    ) AS umsatz_pro_land,

    AVG(betrag) OVER (
        PARTITION BY land
    ) AS durchschnitt_pro_land,

    COUNT(*) OVER (
        PARTITION BY land
    ) AS anzahl_verkaeufe_pro_land,

    SUM(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS laufende_summe_pro_land

FROM verkaeufe
ORDER BY land, datum;
```

### Erklärung

Dieser Befehl zeigt pro Zeile:

* den Gesamtumsatz über alle Verkäufe,
* den Umsatz pro Land,
* den Durchschnitt pro Land,
* die Anzahl der Verkäufe pro Land,
* die laufende Summe pro Land nach Datum.

---

# 19. Merksätze

* Window Functions verwenden `OVER()`.
* Aggregate Window Functions sind z. B. `SUM`, `AVG`, `COUNT`, `MIN`, `MAX`.
* Im Gegensatz zu `GROUP BY` bleiben alle Originalzeilen erhalten.
* `PARTITION BY` teilt Daten in Gruppen.
* `ORDER BY` innerhalb von `OVER()` bestimmt die Reihenfolge der Berechnung.
* `ROWS BETWEEN` definiert genauer, welche Zeilen in die Berechnung einfließen.
* Window Functions können nicht direkt in `WHERE` verwendet werden.
* Zum Filtern nach Window-Ergebnissen verwendet man eine Subquery oder CTE.


# SQL Zusammenfassung – Teil 12: Ranking Window Functions

## 1. Was sind Ranking Window Functions?

`Ranking Window Functions` werden verwendet, um Zeilen innerhalb eines Ergebnisses zu nummerieren oder zu bewerten.

Sie gehören zu den `Window Functions` und verwenden immer `OVER()`.

Typische Ranking Functions:

| Funktion         | Bedeutung                            |
| ---------------- | ------------------------------------ |
| `ROW_NUMBER()`   | eindeutige Zeilennummer              |
| `RANK()`         | Rang mit Lücken bei gleichen Werten  |
| `DENSE_RANK()`   | Rang ohne Lücken bei gleichen Werten |
| `CUME_DIST()`    | kumulative Verteilung                |
| `PERCENT_RANK()` | prozentualer Rang                    |
| `NTILE(n)`       | teilt Zeilen in Gruppen ein          |

---

## 2. Grundstruktur

```sql
SELECT
    spalte1,
    spalte2,
    RANKING_FUNCTION() OVER (
        PARTITION BY gruppierung
        ORDER BY sortierung
    ) AS neuer_spaltenname
FROM tabelle;
```

### Wichtige Bestandteile

| Bestandteil      | Bedeutung                                   |
| ---------------- | ------------------------------------------- |
| `OVER()`         | macht die Funktion zu einer Window Function |
| `PARTITION BY`   | teilt die Daten in Gruppen                  |
| `ORDER BY`       | bestimmt die Reihenfolge für das Ranking    |
| Ranking Function | z. B. `ROW_NUMBER`, `RANK`, `DENSE_RANK`    |

`ORDER BY` innerhalb von `OVER()` ist bei Ranking Functions besonders wichtig.

---

## 3. Beispiel-Tabelle

Tabelle: `verkaeufe`

| verkauf_id | kunde | land        | betrag |
| ---------- | ----- | ----------- | -----: |
| 1          | Ali   | Deutschland |    500 |
| 2          | Sara  | Deutschland |    700 |
| 3          | Omar  | Deutschland |    700 |
| 4          | Lina  | Frankreich  |    300 |
| 5          | Adam  | Frankreich  |    600 |
| 6          | Nora  | Frankreich  |    600 |

---

# 4. ROW_NUMBER

`ROW_NUMBER()` vergibt eine eindeutige Nummer für jede Zeile.

Auch wenn zwei Werte gleich sind, bekommt jede Zeile eine eigene Nummer.

## 4.1 Beispiel

```sql
SELECT
    kunde,
    land,
    betrag,
    ROW_NUMBER() OVER (
        ORDER BY betrag DESC
    ) AS zeilennummer
FROM verkaeufe;
```

### Ergebnisidee

| kunde | betrag | zeilennummer |
| ----- | -----: | -----------: |
| Sara  |    700 |            1 |
| Omar  |    700 |            2 |
| Adam  |    600 |            3 |
| Nora  |    600 |            4 |
| Ali   |    500 |            5 |
| Lina  |    300 |            6 |

### Erklärung

* Jede Zeile bekommt eine eindeutige Nummer.
* Auch gleiche Beträge bekommen unterschiedliche Nummern.
* Die Reihenfolge bei gleichen Werten kann ohne zusätzliche Sortierung zufällig sein.

Besser:

```sql
SELECT
    kunde,
    land,
    betrag,
    ROW_NUMBER() OVER (
        ORDER BY betrag DESC, kunde ASC
    ) AS zeilennummer
FROM verkaeufe;
```

---

## 4.2 ROW_NUMBER mit PARTITION BY

```sql
SELECT
    kunde,
    land,
    betrag,
    ROW_NUMBER() OVER (
        PARTITION BY land
        ORDER BY betrag DESC
    ) AS nummer_pro_land
FROM verkaeufe;
```

### Erklärung

Die Nummerierung beginnt für jedes Land neu.

---

# 5. RANK

`RANK()` vergibt Rangplätze.

Wenn mehrere Zeilen denselben Wert haben, bekommen sie denselben Rang.

Danach entstehen aber Lücken.

## 5.1 Beispiel

```sql
SELECT
    kunde,
    betrag,
    RANK() OVER (
        ORDER BY betrag DESC
    ) AS rang
FROM verkaeufe;
```

### Ergebnisidee

| kunde | betrag | rang |
| ----- | -----: | ---: |
| Sara  |    700 |    1 |
| Omar  |    700 |    1 |
| Adam  |    600 |    3 |
| Nora  |    600 |    3 |
| Ali   |    500 |    5 |
| Lina  |    300 |    6 |

### Erklärung

* Sara und Omar haben beide Rang `1`.
* Danach wird Rang `2` übersprungen.
* Adam und Nora bekommen Rang `3`.
* Danach wird Rang `4` übersprungen.

---

## 5.2 RANK mit PARTITION BY

```sql
SELECT
    kunde,
    land,
    betrag,
    RANK() OVER (
        PARTITION BY land
        ORDER BY betrag DESC
    ) AS rang_pro_land
FROM verkaeufe;
```

### Erklärung

Der Rang wird innerhalb jedes Landes berechnet.

---

# 6. DENSE_RANK

`DENSE_RANK()` ist ähnlich wie `RANK()`.

Der Unterschied: Es entstehen keine Lücken.

## 6.1 Beispiel

```sql
SELECT
    kunde,
    betrag,
    DENSE_RANK() OVER (
        ORDER BY betrag DESC
    ) AS dichter_rang
FROM verkaeufe;
```

### Ergebnisidee

| kunde | betrag | dichter_rang |
| ----- | -----: | -----------: |
| Sara  |    700 |            1 |
| Omar  |    700 |            1 |
| Adam  |    600 |            2 |
| Nora  |    600 |            2 |
| Ali   |    500 |            3 |
| Lina  |    300 |            4 |

### Erklärung

* Gleiche Beträge bekommen denselben Rang.
* Danach geht es ohne Lücke weiter.
* Nach Rang `1` kommt direkt Rang `2`.

---

# 7. Vergleich: ROW_NUMBER, RANK und DENSE_RANK

```sql
SELECT
    kunde,
    betrag,
    ROW_NUMBER() OVER (ORDER BY betrag DESC) AS row_number,
    RANK() OVER (ORDER BY betrag DESC) AS rank,
    DENSE_RANK() OVER (ORDER BY betrag DESC) AS dense_rank
FROM verkaeufe;
```

### Ergebnisidee

| kunde | betrag | row_number | rank | dense_rank |
| ----- | -----: | ---------: | ---: | ---------: |
| Sara  |    700 |          1 |    1 |          1 |
| Omar  |    700 |          2 |    1 |          1 |
| Adam  |    600 |          3 |    3 |          2 |
| Nora  |    600 |          4 |    3 |          2 |
| Ali   |    500 |          5 |    5 |          3 |
| Lina  |    300 |          6 |    6 |          4 |

### Merksatz

| Funktion       | Verhalten bei gleichen Werten                         |
| -------------- | ----------------------------------------------------- |
| `ROW_NUMBER()` | immer unterschiedliche Nummern                        |
| `RANK()`       | gleiche Werte bekommen gleichen Rang, aber mit Lücken |
| `DENSE_RANK()` | gleiche Werte bekommen gleichen Rang, ohne Lücken     |

---

# 8. CUME_DIST

`CUME_DIST()` steht für **Cumulative Distribution**.

Es zeigt, welcher Anteil der Zeilen kleiner oder gleich dem aktuellen Wert ist.

Der Rückgabewert liegt zwischen `0` und `1`.

## 8.1 Struktur

```sql
CUME_DIST() OVER (
    ORDER BY spalte
)
```

## 8.2 Beispiel

```sql
SELECT
    kunde,
    betrag,
    CUME_DIST() OVER (
        ORDER BY betrag
    ) AS kumulative_verteilung
FROM verkaeufe;
```

### Erklärung

Wenn `CUME_DIST()` den Wert `0.50` zurückgibt, bedeutet das:

```text
50 % der Zeilen liegen bis zu diesem Wert.
```

---

## 8.3 CUME_DIST mit PARTITION BY

```sql
SELECT
    kunde,
    land,
    betrag,
    CUME_DIST() OVER (
        PARTITION BY land
        ORDER BY betrag
    ) AS kumulative_verteilung_pro_land
FROM verkaeufe;
```

### Erklärung

Die kumulative Verteilung wird separat pro Land berechnet.

---

# 9. PERCENT_RANK

`PERCENT_RANK()` berechnet die relative Position einer Zeile innerhalb einer sortierten Gruppe.

Der Rückgabewert liegt zwischen `0` und `1`.

## 9.1 Struktur

```sql
PERCENT_RANK() OVER (
    ORDER BY spalte
)
```

## 9.2 Beispiel

```sql
SELECT
    kunde,
    betrag,
    PERCENT_RANK() OVER (
        ORDER BY betrag
    ) AS prozent_rang
FROM verkaeufe;
```

### Erklärung

* Die erste Zeile bekommt meistens `0`.
* Die letzte Zeile bekommt oft `1`.
* Werte dazwischen zeigen die relative Position.

---

## 9.3 Unterschied zwischen CUME_DIST und PERCENT_RANK

| Funktion         | Bedeutung                                            |
| ---------------- | ---------------------------------------------------- |
| `CUME_DIST()`    | Anteil der Zeilen bis einschließlich aktueller Zeile |
| `PERCENT_RANK()` | relative Rangposition der aktuellen Zeile            |

Vereinfacht:

* `CUME_DIST()` fragt: Wie viel Prozent der Daten liegen bis hier?
* `PERCENT_RANK()` fragt: Wo liegt diese Zeile relativ im Ranking?

---

# 10. NTILE

`NTILE(n)` teilt die Zeilen in `n` Gruppen ein.

Das wird häufig verwendet, um Daten in Quartile, Dezile oder andere Gruppen zu teilen.

## 10.1 Struktur

```sql
NTILE(anzahl_gruppen) OVER (
    ORDER BY spalte
)
```

---

## 10.2 Beispiel: 4 Gruppen

```sql
SELECT
    kunde,
    betrag,
    NTILE(4) OVER (
        ORDER BY betrag DESC
    ) AS quartil
FROM verkaeufe;
```

### Erklärung

Die Daten werden in 4 Gruppen eingeteilt.

| Quartil | Bedeutung       |
| ------- | --------------- |
| `1`     | oberste Gruppe  |
| `2`     | zweite Gruppe   |
| `3`     | dritte Gruppe   |
| `4`     | unterste Gruppe |

Wenn man `ORDER BY betrag DESC` verwendet, sind die höchsten Beträge in Gruppe `1`.

---

## 10.3 NTILE mit PARTITION BY

```sql
SELECT
    kunde,
    land,
    betrag,
    NTILE(2) OVER (
        PARTITION BY land
        ORDER BY betrag DESC
    ) AS gruppe_pro_land
FROM verkaeufe;
```

### Erklärung

Die Kunden werden innerhalb jedes Landes in 2 Gruppen aufgeteilt.

---

# 11. Top-N pro Gruppe

Ranking Functions werden oft verwendet, um die besten N Zeilen pro Gruppe zu finden.

## Beispiel: Top 2 Kunden pro Land nach Betrag

```sql
WITH ranking AS (
    SELECT
        kunde,
        land,
        betrag,
        ROW_NUMBER() OVER (
            PARTITION BY land
            ORDER BY betrag DESC
        ) AS rn
    FROM verkaeufe
)
SELECT *
FROM ranking
WHERE rn <= 2;
```

### Erklärung

* `ROW_NUMBER()` nummeriert Kunden pro Land.
* Die höchsten Beträge bekommen die kleinsten Nummern.
* Danach werden nur die ersten 2 pro Land angezeigt.

---

# 12. Ranking Functions können nicht direkt in WHERE verwendet werden

Das funktioniert meistens nicht:

```sql
SELECT
    kunde,
    land,
    betrag,
    ROW_NUMBER() OVER (
        PARTITION BY land
        ORDER BY betrag DESC
    ) AS rn
FROM verkaeufe
WHERE rn <= 2;
```

Warum?

`WHERE` wird vor der Window Function ausgeführt.

Besser mit CTE:

```sql
WITH ranking AS (
    SELECT
        kunde,
        land,
        betrag,
        ROW_NUMBER() OVER (
            PARTITION BY land
            ORDER BY betrag DESC
        ) AS rn
    FROM verkaeufe
)
SELECT *
FROM ranking
WHERE rn <= 2;
```

---

# 13. Praktisches Komplettbeispiel

```sql
SELECT
    kunde,
    land,
    betrag,

    ROW_NUMBER() OVER (
        PARTITION BY land
        ORDER BY betrag DESC
    ) AS row_number_pro_land,

    RANK() OVER (
        PARTITION BY land
        ORDER BY betrag DESC
    ) AS rank_pro_land,

    DENSE_RANK() OVER (
        PARTITION BY land
        ORDER BY betrag DESC
    ) AS dense_rank_pro_land,

    CUME_DIST() OVER (
        PARTITION BY land
        ORDER BY betrag
    ) AS cume_dist_pro_land,

    PERCENT_RANK() OVER (
        PARTITION BY land
        ORDER BY betrag
    ) AS percent_rank_pro_land,

    NTILE(4) OVER (
        PARTITION BY land
        ORDER BY betrag DESC
    ) AS quartil_pro_land

FROM verkaeufe
ORDER BY land, betrag DESC;
```

---

# 14. Mini-Übersicht

| Funktion         | Beschreibung                |
| ---------------- | --------------------------- |
| `ROW_NUMBER()`   | eindeutige Nummer pro Zeile |
| `RANK()`         | Rang mit Lücken             |
| `DENSE_RANK()`   | Rang ohne Lücken            |
| `CUME_DIST()`    | kumulative Verteilung       |
| `PERCENT_RANK()` | relative Rangposition       |
| `NTILE(n)`       | teilt Zeilen in `n` Gruppen |

---

# 15. Merksätze

* Ranking Window Functions brauchen fast immer `ORDER BY` in `OVER()`.
* `ROW_NUMBER()` gibt jeder Zeile eine eindeutige Nummer.
* `RANK()` erzeugt gleiche Ränge bei gleichen Werten, aber mit Lücken.
* `DENSE_RANK()` erzeugt gleiche Ränge bei gleichen Werten, aber ohne Lücken.
* `CUME_DIST()` zeigt die kumulative Verteilung.
* `PERCENT_RANK()` zeigt die relative Rangposition.
* `NTILE(n)` teilt Daten in `n` Gruppen.
* `PARTITION BY` startet das Ranking pro Gruppe neu.
* Zum Filtern nach Ranking-Ergebnissen verwendet man meistens eine CTE oder Subquery.


# SQL Zusammenfassung – Teil 13: Value Window Functions

## 1. Was sind Value Window Functions?

`Value Window Functions` werden verwendet, um Werte aus anderen Zeilen innerhalb eines Fensters zu lesen.

Sie verändern die Anzahl der Zeilen nicht.

Wichtige Value Functions:

| Funktion        | Bedeutung                                  |
| --------------- | ------------------------------------------ |
| `LAG()`         | Holt einen Wert aus einer vorherigen Zeile |
| `LEAD()`        | Holt einen Wert aus einer nächsten Zeile   |
| `FIRST_VALUE()` | Holt den ersten Wert im Fenster            |
| `LAST_VALUE()`  | Holt den letzten Wert im Fenster           |

---

## 2. Grundstruktur

```sql
SELECT
    spalte1,
    spalte2,
    VALUE_FUNCTION(spalte) OVER (
        PARTITION BY gruppierung
        ORDER BY sortierung
    ) AS neuer_spaltenname
FROM tabelle;
```

### Wichtige Bestandteile

| Bestandteil                  | Bedeutung                                    |
| ---------------------------- | -------------------------------------------- |
| `OVER()`                     | definiert das Fenster                        |
| `PARTITION BY`               | teilt Daten in Gruppen                       |
| `ORDER BY`                   | bestimmt die Reihenfolge                     |
| `LAG` / `LEAD`               | greifen auf vorherige oder nächste Zeilen zu |
| `FIRST_VALUE` / `LAST_VALUE` | greifen auf ersten oder letzten Wert zu      |

---

## 3. Beispiel-Tabelle

Tabelle: `verkaeufe`

| verkauf_id | kunde | land        | datum      | betrag |
| ---------- | ----- | ----------- | ---------- | -----: |
| 1          | Ali   | Deutschland | 2024-01-01 |    100 |
| 2          | Sara  | Deutschland | 2024-01-03 |    200 |
| 3          | Omar  | Deutschland | 2024-01-05 |    150 |
| 4          | Lina  | Frankreich  | 2024-01-02 |    300 |
| 5          | Adam  | Frankreich  | 2024-01-04 |    250 |

---

# 4. LAG

`LAG()` gibt einen Wert aus einer vorherigen Zeile zurück.

Das ist nützlich, um aktuelle Werte mit vorherigen Werten zu vergleichen.

## 4.1 Allgemeine Struktur

```sql
LAG(spalte) OVER (
    ORDER BY sortierung
)
```

## 4.2 Beispiel

```sql
SELECT
    kunde,
    datum,
    betrag,
    LAG(betrag) OVER (
        ORDER BY datum
    ) AS vorheriger_betrag
FROM verkaeufe;
```

### Ergebnisidee

| kunde | datum      | betrag | vorheriger_betrag |
| ----- | ---------- | -----: | ----------------: |
| Ali   | 2024-01-01 |    100 |              NULL |
| Lina  | 2024-01-02 |    300 |               100 |
| Sara  | 2024-01-03 |    200 |               300 |
| Adam  | 2024-01-04 |    250 |               200 |
| Omar  | 2024-01-05 |    150 |               250 |

### Erklärung

* Die erste Zeile hat keinen vorherigen Wert.
* Deshalb ist `vorheriger_betrag` dort `NULL`.

---

## 4.3 LAG mit PARTITION BY

```sql
SELECT
    kunde,
    land,
    datum,
    betrag,
    LAG(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
    ) AS vorheriger_betrag_im_land
FROM verkaeufe;
```

### Erklärung

`PARTITION BY land` bedeutet:

* Die vorherige Zeile wird nur innerhalb desselben Landes gesucht.
* Für jedes Land beginnt die Berechnung neu.

---

## 4.4 LAG mit Offset

Man kann bestimmen, wie viele Zeilen zurückgeschaut werden soll.

```sql
SELECT
    kunde,
    datum,
    betrag,
    LAG(betrag, 2) OVER (
        ORDER BY datum
    ) AS betrag_vor_zwei_zeilen
FROM verkaeufe;
```

### Erklärung

`LAG(betrag, 2)` nimmt den Wert aus zwei Zeilen davor.

---

## 4.5 LAG mit Default-Wert

Man kann einen Ersatzwert angeben, wenn keine vorherige Zeile existiert.

```sql
SELECT
    kunde,
    datum,
    betrag,
    LAG(betrag, 1, 0) OVER (
        ORDER BY datum
    ) AS vorheriger_betrag
FROM verkaeufe;
```

### Erklärung

Wenn kein vorheriger Wert existiert, wird `0` zurückgegeben.

---

# 5. LEAD

`LEAD()` gibt einen Wert aus einer nächsten Zeile zurück.

Das Gegenteil von `LAG()`.

## 5.1 Allgemeine Struktur

```sql
LEAD(spalte) OVER (
    ORDER BY sortierung
)
```

## 5.2 Beispiel

```sql
SELECT
    kunde,
    datum,
    betrag,
    LEAD(betrag) OVER (
        ORDER BY datum
    ) AS naechster_betrag
FROM verkaeufe;
```

### Ergebnisidee

| kunde | datum      | betrag | naechster_betrag |
| ----- | ---------- | -----: | ---------------: |
| Ali   | 2024-01-01 |    100 |              300 |
| Lina  | 2024-01-02 |    300 |              200 |
| Sara  | 2024-01-03 |    200 |              250 |
| Adam  | 2024-01-04 |    250 |              150 |
| Omar  | 2024-01-05 |    150 |             NULL |

### Erklärung

* Die letzte Zeile hat keinen nächsten Wert.
* Deshalb ist `naechster_betrag` dort `NULL`.

---

## 5.3 LEAD mit PARTITION BY

```sql
SELECT
    kunde,
    land,
    datum,
    betrag,
    LEAD(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
    ) AS naechster_betrag_im_land
FROM verkaeufe;
```

### Erklärung

Der nächste Wert wird nur innerhalb desselben Landes gesucht.

---

## 5.4 LEAD mit Offset und Default-Wert

```sql
SELECT
    kunde,
    datum,
    betrag,
    LEAD(betrag, 1, 0) OVER (
        ORDER BY datum
    ) AS naechster_betrag
FROM verkaeufe;
```

### Erklärung

Wenn kein nächster Wert existiert, wird `0` zurückgegeben.

---

# 6. Differenz mit LAG berechnen

`LAG()` wird sehr oft verwendet, um Veränderungen zu berechnen.

```sql
SELECT
    kunde,
    datum,
    betrag,
    LAG(betrag) OVER (
        ORDER BY datum
    ) AS vorheriger_betrag,
    betrag - LAG(betrag) OVER (
        ORDER BY datum
    ) AS differenz_zum_vorherigen
FROM verkaeufe;
```

### Erklärung

Dieser Befehl berechnet die Differenz zwischen:

* aktuellem Betrag
* vorherigem Betrag

---

## 6.1 Differenz pro Gruppe

```sql
SELECT
    kunde,
    land,
    datum,
    betrag,
    betrag - LAG(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
    ) AS differenz_zum_vorherigen_im_land
FROM verkaeufe;
```

### Erklärung

Die Differenz wird pro Land berechnet.

---

# 7. FIRST_VALUE

`FIRST_VALUE()` gibt den ersten Wert innerhalb eines Fensters zurück.

## 7.1 Allgemeine Struktur

```sql
FIRST_VALUE(spalte) OVER (
    PARTITION BY gruppierung
    ORDER BY sortierung
)
```

## 7.2 Beispiel

```sql
SELECT
    kunde,
    land,
    datum,
    betrag,
    FIRST_VALUE(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
    ) AS erster_betrag_im_land
FROM verkaeufe;
```

### Erklärung

Dieser Befehl zeigt bei jeder Zeile den ersten Betrag des jeweiligen Landes nach Datum.

---

## 7.3 FIRST_VALUE mit anderem Sortierkriterium

```sql
SELECT
    kunde,
    land,
    betrag,
    FIRST_VALUE(kunde) OVER (
        PARTITION BY land
        ORDER BY betrag DESC
    ) AS kunde_mit_hoechstem_betrag_im_land
FROM verkaeufe;
```

### Erklärung

Durch `ORDER BY betrag DESC` steht der höchste Betrag zuerst.

Deshalb gibt `FIRST_VALUE(kunde)` den Kunden mit dem höchsten Betrag pro Land zurück.

---

# 8. LAST_VALUE

`LAST_VALUE()` gibt den letzten Wert innerhalb eines Fensters zurück.

## 8.1 Allgemeine Struktur

```sql
LAST_VALUE(spalte) OVER (
    PARTITION BY gruppierung
    ORDER BY sortierung
)
```

## 8.2 Wichtig bei LAST_VALUE

Bei `LAST_VALUE()` muss man oft einen Window Frame angeben.

Ohne Frame kann `LAST_VALUE()` unerwartet wirken, weil SQL standardmäßig oft nur bis zur aktuellen Zeile schaut.

Problematisch:

```sql
SELECT
    kunde,
    land,
    datum,
    betrag,
    LAST_VALUE(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
    ) AS letzter_betrag_im_land
FROM verkaeufe;
```

Besser:

```sql
SELECT
    kunde,
    land,
    datum,
    betrag,
    LAST_VALUE(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS letzter_betrag_im_land
FROM verkaeufe;
```

### Erklärung

```sql
ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
```

bedeutet:

* von der ersten Zeile der Gruppe
* bis zur letzten Zeile der Gruppe

Dadurch wird wirklich der letzte Wert der gesamten Gruppe genommen.

---

# 9. FIRST_VALUE und LAST_VALUE zusammen

```sql
SELECT
    kunde,
    land,
    datum,
    betrag,
    FIRST_VALUE(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS erster_betrag_im_land,
    LAST_VALUE(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS letzter_betrag_im_land
FROM verkaeufe;
```

### Erklärung

Dieser Befehl zeigt:

* den ersten Betrag pro Land
* den letzten Betrag pro Land

---

# 10. Typische Anwendungsfälle

| Funktion        | Typische Anwendung            |
| --------------- | ----------------------------- |
| `LAG()`         | Vergleich mit vorherigem Wert |
| `LEAD()`        | Vergleich mit nächstem Wert   |
| `FIRST_VALUE()` | erster Wert pro Gruppe        |
| `LAST_VALUE()`  | letzter Wert pro Gruppe       |

---

# 11. Value Functions können nicht direkt in WHERE verwendet werden

Window Functions werden nach `WHERE` berechnet.

Das funktioniert meistens nicht:

```sql
SELECT
    kunde,
    land,
    betrag,
    LAG(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
    ) AS vorheriger_betrag
FROM verkaeufe
WHERE vorheriger_betrag IS NOT NULL;
```

Besser mit CTE:

```sql
WITH daten AS (
    SELECT
        kunde,
        land,
        datum,
        betrag,
        LAG(betrag) OVER (
            PARTITION BY land
            ORDER BY datum
        ) AS vorheriger_betrag
    FROM verkaeufe
)
SELECT *
FROM daten
WHERE vorheriger_betrag IS NOT NULL;
```

---

# 12. Praktisches Komplettbeispiel

```sql
SELECT
    kunde,
    land,
    datum,
    betrag,

    LAG(betrag, 1, 0) OVER (
        PARTITION BY land
        ORDER BY datum
    ) AS vorheriger_betrag,

    LEAD(betrag, 1, 0) OVER (
        PARTITION BY land
        ORDER BY datum
    ) AS naechster_betrag,

    FIRST_VALUE(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS erster_betrag_im_land,

    LAST_VALUE(betrag) OVER (
        PARTITION BY land
        ORDER BY datum
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS letzter_betrag_im_land

FROM verkaeufe
ORDER BY land, datum;
```

### Erklärung

Dieser Befehl zeigt pro Zeile:

* den vorherigen Betrag im gleichen Land,
* den nächsten Betrag im gleichen Land,
* den ersten Betrag im gleichen Land,
* den letzten Betrag im gleichen Land.

---

# 13. Mini-Übersicht

| Funktion        | Bedeutung                 | Beispiel                             |
| --------------- | ------------------------- | ------------------------------------ |
| `LAG()`         | Wert aus vorheriger Zeile | `LAG(betrag) OVER (ORDER BY datum)`  |
| `LEAD()`        | Wert aus nächster Zeile   | `LEAD(betrag) OVER (ORDER BY datum)` |
| `FIRST_VALUE()` | erster Wert im Fenster    | `FIRST_VALUE(betrag) OVER (...)`     |
| `LAST_VALUE()`  | letzter Wert im Fenster   | `LAST_VALUE(betrag) OVER (...)`      |

---

# 14. Merksätze

* Value Window Functions lesen Werte aus anderen Zeilen.
* `LAG()` schaut nach hinten.
* `LEAD()` schaut nach vorne.
* `FIRST_VALUE()` gibt den ersten Wert im Fenster zurück.
* `LAST_VALUE()` gibt den letzten Wert im Fenster zurück.
* Bei `LAST_VALUE()` sollte man oft einen vollständigen Window Frame angeben.
* `PARTITION BY` begrenzt die Berechnung auf Gruppen.
* `ORDER BY` bestimmt die Reihenfolge innerhalb der Gruppe.
* Zum Filtern nach Window-Ergebnissen verwendet man eine CTE oder Subquery.


# SQL Zusammenfassung – Teil 14: Subqueries

## 1. Was ist eine Subquery?

Eine `Subquery` ist eine Abfrage innerhalb einer anderen SQL-Abfrage.

Sie wird auch **Unterabfrage** genannt.

Eine Subquery kann verwendet werden in:

* `SELECT`
* `FROM`
* `WHERE`
* `HAVING`

---

## 2. Allgemeine Struktur

```sql
SELECT spalten
FROM tabelle
WHERE spalte operator (
    SELECT spalte
    FROM andere_tabelle
    WHERE bedingung
);
```

---

## 3. Subquery in WHERE

Eine Subquery wird oft in `WHERE` verwendet, um Werte aus einer anderen Tabelle zu vergleichen.

### Beispiel: Kunden mit Bestellungen

```sql
SELECT *
FROM kunden
WHERE kunden_id IN (
    SELECT kunden_id
    FROM bestellungen
);
```

### Erklärung

Die innere Abfrage liefert alle `kunden_id`, die eine Bestellung haben.

Die äußere Abfrage zeigt dann nur diese Kunden.

---

## 4. Subquery mit IN

`IN` wird verwendet, wenn die Subquery mehrere Werte zurückgeben kann.

```sql
SELECT *
FROM kunden
WHERE stadt IN (
    SELECT stadt
    FROM filialen
);
```

### Erklärung

Es werden nur Kunden angezeigt, deren Stadt auch in der Tabelle `filialen` vorkommt.

---

## 5. Subquery mit NOT IN

`NOT IN` zeigt Werte, die nicht in der Subquery vorkommen.

```sql
SELECT *
FROM kunden
WHERE kunden_id NOT IN (
    SELECT kunden_id
    FROM bestellungen
);
```

### Erklärung

Dieser Befehl zeigt Kunden ohne Bestellung.

### Wichtiger Hinweis

Bei `NOT IN` muss man vorsichtig sein, wenn die Subquery `NULL`-Werte enthält.

Oft ist `NOT EXISTS` sicherer.

---

## 6. Subquery mit EXISTS

`EXISTS` prüft, ob eine Subquery mindestens eine Zeile zurückgibt.

```sql
SELECT *
FROM kunden k
WHERE EXISTS (
    SELECT 1
    FROM bestellungen b
    WHERE b.kunden_id = k.kunden_id
);
```

### Erklärung

Dieser Befehl zeigt Kunden, für die mindestens eine Bestellung existiert.

---

## 7. Subquery mit NOT EXISTS

`NOT EXISTS` prüft, ob keine passende Zeile existiert.

```sql
SELECT *
FROM kunden k
WHERE NOT EXISTS (
    SELECT 1
    FROM bestellungen b
    WHERE b.kunden_id = k.kunden_id
);
```

### Erklärung

Dieser Befehl zeigt Kunden ohne Bestellung.

---

## 8. Subquery in SELECT

Eine Subquery kann auch in `SELECT` verwendet werden.

```sql
SELECT
    name,
    umsatz,
    (SELECT AVG(umsatz) FROM kunden) AS durchschnittsumsatz
FROM kunden;
```

### Erklärung

Bei jeder Zeile wird zusätzlich der Durchschnittsumsatz aller Kunden angezeigt.

---

## 9. Subquery in FROM

Eine Subquery in `FROM` wird wie eine temporäre Tabelle verwendet.

```sql
SELECT *
FROM (
    SELECT
        land,
        SUM(umsatz) AS gesamtumsatz
    FROM kunden
    GROUP BY land
) AS land_umsatz
WHERE gesamtumsatz > 1000;
```

### Erklärung

Die innere Abfrage berechnet zuerst den Umsatz pro Land.

Die äußere Abfrage filtert danach nur Länder mit mehr als `1000` Umsatz.

---

## 10. Correlated Subquery

Eine `Correlated Subquery` hängt von der äußeren Abfrage ab.

```sql
SELECT *
FROM kunden k
WHERE EXISTS (
    SELECT 1
    FROM bestellungen b
    WHERE b.kunden_id = k.kunden_id
);
```

### Erklärung

Die innere Abfrage verwendet `k.kunden_id` aus der äußeren Abfrage.

Dadurch wird die Subquery für jede Zeile der äußeren Abfrage geprüft.

---

## 11. Subquery mit Vergleichsoperatoren

Subqueries können auch mit Vergleichsoperatoren verwendet werden.

```sql
SELECT *
FROM produkte
WHERE preis > (
    SELECT AVG(preis)
    FROM produkte
);
```

### Erklärung

Dieser Befehl zeigt alle Produkte, deren Preis über dem Durchschnittspreis liegt.

---

## 12. Wichtige Operatoren mit Subqueries

| Operator     | Bedeutung                              |
| ------------ | -------------------------------------- |
| `IN`         | Wert ist in der Ergebnisliste          |
| `NOT IN`     | Wert ist nicht in der Ergebnisliste    |
| `EXISTS`     | Subquery liefert mindestens eine Zeile |
| `NOT EXISTS` | Subquery liefert keine Zeile           |
| `=`          | Vergleich mit genau einem Wert         |
| `>`          | größer als Ergebnis der Subquery       |
| `<`          | kleiner als Ergebnis der Subquery      |
| `>=`         | größer oder gleich                     |
| `<=`         | kleiner oder gleich                    |

---

## 13. Häufige Fehler

### 13.1 Subquery gibt mehrere Werte zurück

Problematisch:

```sql
SELECT *
FROM kunden
WHERE kunden_id = (
    SELECT kunden_id
    FROM bestellungen
);
```

Wenn die Subquery mehrere `kunden_id` zurückgibt, entsteht ein Fehler.

Besser:

```sql
SELECT *
FROM kunden
WHERE kunden_id IN (
    SELECT kunden_id
    FROM bestellungen
);
```

---

### 13.2 Alias bei Subquery in FROM vergessen

Falsch:

```sql
SELECT *
FROM (
    SELECT land, SUM(umsatz) AS gesamtumsatz
    FROM kunden
    GROUP BY land
);
```

Richtig:

```sql
SELECT *
FROM (
    SELECT land, SUM(umsatz) AS gesamtumsatz
    FROM kunden
    GROUP BY land
) AS land_umsatz;
```

---

## 14. Mini-Beispiel komplett

```sql
SELECT
    name,
    land,
    umsatz
FROM kunden
WHERE umsatz > (
    SELECT AVG(umsatz)
    FROM kunden
)
AND kunden_id IN (
    SELECT kunden_id
    FROM bestellungen
);
```

### Erklärung

Dieser Befehl zeigt Kunden, die:

* einen Umsatz über dem Durchschnitt haben,
* und mindestens eine Bestellung haben.

---

## 15. Merksätze

* Eine Subquery ist eine Abfrage innerhalb einer anderen Abfrage.
* Subqueries können in `SELECT`, `FROM`, `WHERE` und `HAVING` stehen.
* `IN` wird verwendet, wenn mehrere Werte zurückkommen.
* `EXISTS` prüft, ob mindestens eine passende Zeile existiert.
* `NOT EXISTS` ist oft sicherer als `NOT IN`, wenn `NULL` vorkommen kann.
* Eine Subquery in `FROM` braucht meistens einen Alias.
* Bei `=` darf die Subquery nur einen einzelnen Wert zurückgeben.



# SQL Zusammenfassung – Teil 15: CTE

## 1. Was ist eine CTE?

`CTE` steht für **Common Table Expression**.

Eine CTE ist eine temporäre Ergebnismenge, die innerhalb einer SQL-Abfrage verwendet wird.

Man kann sie sich wie eine benannte Subquery vorstellen.

CTEs machen komplexe SQL-Abfragen übersichtlicher und besser lesbar.

---

## 2. Allgemeine Struktur

```sql
WITH cte_name AS (
    SELECT spalte1, spalte2
    FROM tabelle
    WHERE bedingung
)
SELECT *
FROM cte_name;
```

---

## 3. Einfaches Beispiel

```sql
WITH kunden_deutschland AS (
    SELECT *
    FROM kunden
    WHERE land = 'Deutschland'
)
SELECT *
FROM kunden_deutschland;
```

### Erklärung

Die CTE `kunden_deutschland` enthält zuerst alle Kunden aus Deutschland.

Danach wird aus dieser CTE gelesen.

---

## 4. CTE mit Aggregation

```sql
WITH umsatz_pro_land AS (
    SELECT
        land,
        SUM(umsatz) AS gesamtumsatz
    FROM kunden
    GROUP BY land
)
SELECT *
FROM umsatz_pro_land
WHERE gesamtumsatz > 1000;
```

### Erklärung

Dieser Befehl:

* berechnet zuerst den Umsatz pro Land,
* speichert das Ergebnis temporär in der CTE,
* filtert danach Länder mit mehr als `1000` Gesamtumsatz.

---

## 5. CTE mit JOIN

```sql
WITH kunden_bestellungen AS (
    SELECT
        k.kunden_id,
        k.name,
        b.bestellung_id,
        b.bestelldatum
    FROM kunden k
    INNER JOIN bestellungen b
    ON k.kunden_id = b.kunden_id
)
SELECT *
FROM kunden_bestellungen;
```

### Erklärung

Die CTE verbindet Kunden mit ihren Bestellungen.

Danach kann das Ergebnis weiterverwendet werden.

---

## 6. Mehrere CTEs

Man kann mehrere CTEs in einer Abfrage verwenden.

```sql
WITH kunden_deutschland AS (
    SELECT *
    FROM kunden
    WHERE land = 'Deutschland'
),
aktive_kunden AS (
    SELECT *
    FROM kunden_deutschland
    WHERE status = 'Aktiv'
)
SELECT *
FROM aktive_kunden;
```

### Erklärung

* Die erste CTE filtert Kunden aus Deutschland.
* Die zweite CTE verwendet das Ergebnis der ersten CTE.
* Am Ende wird aus der zweiten CTE gelesen.

---

## 7. CTE mit Window Function

CTEs sind sehr nützlich, wenn man nach Ergebnissen von Window Functions filtern möchte.

```sql
WITH ranking AS (
    SELECT
        kunde,
        land,
        umsatz,
        ROW_NUMBER() OVER (
            PARTITION BY land
            ORDER BY umsatz DESC
        ) AS rn
    FROM kunden
)
SELECT *
FROM ranking
WHERE rn <= 3;
```

### Erklärung

Dieser Befehl zeigt die Top 3 Kunden pro Land nach Umsatz.

Warum CTE?

Window Functions können meistens nicht direkt in `WHERE` verwendet werden.

Deshalb wird zuerst das Ranking in der CTE berechnet und danach gefiltert.

---

## 8. CTE in UPDATE

CTEs können auch mit `UPDATE` verwendet werden.

```sql
WITH kunden_status AS (
    SELECT
        kunden_id,
        CASE
            WHEN umsatz >= 1000 THEN 'VIP'
            WHEN umsatz >= 100 THEN 'Aktiv'
            ELSE 'Normal'
        END AS neuer_status
    FROM kunden
)
UPDATE kunden
SET status = kunden_status.neuer_status
FROM kunden_status
WHERE kunden.kunden_id = kunden_status.kunden_id;
```

### Erklärung

Die CTE berechnet zuerst den neuen Status.

Danach wird die Tabelle `kunden` aktualisiert.

---

## 9. CTE in DELETE

CTEs können auch vor einem `DELETE` verwendet werden.

```sql
WITH alte_bestellungen AS (
    SELECT bestellung_id
    FROM bestellungen
    WHERE bestelldatum < '2020-01-01'
)
DELETE FROM bestellungen
WHERE bestellung_id IN (
    SELECT bestellung_id
    FROM alte_bestellungen
);
```

### Erklärung

Dieser Befehl löscht Bestellungen, die vor dem Jahr 2020 erstellt wurden.

---

## 10. Recursive CTE

Eine rekursive CTE kann sich selbst referenzieren.

Sie wird verwendet für hierarchische Daten, zum Beispiel:

* Mitarbeiter und Manager
* Kategorien und Unterkategorien
* Baumstrukturen

### Beispiel: Zahlen von 1 bis 5 erzeugen

```sql
WITH zahlen AS (
    SELECT 1 AS zahl

    UNION ALL

    SELECT zahl + 1
    FROM zahlen
    WHERE zahl < 5
)
SELECT *
FROM zahlen;
```

### Ergebnis

| zahl |
| ---: |
|    1 |
|    2 |
|    3 |
|    4 |
|    5 |

### Erklärung

Eine rekursive CTE besteht aus zwei Teilen:

| Teil            | Bedeutung                                                 |
| --------------- | --------------------------------------------------------- |
| Anchor Query    | Startpunkt der Rekursion                                  |
| Recursive Query | Wiederholt sich, bis die Bedingung nicht mehr erfüllt ist |

---

## 11. CTE vs Subquery

| CTE                                    | Subquery                          |
| -------------------------------------- | --------------------------------- |
| besser lesbar bei komplexen Abfragen   | gut für einfache Abfragen         |
| kann mehrfach logisch aufgebaut werden | oft direkt in `WHERE` oder `FROM` |
| beginnt mit `WITH`                     | steht innerhalb einer Abfrage     |
| gut für Window Functions               | gut für kurze Bedingungen         |

### Subquery

```sql
SELECT *
FROM (
    SELECT land, SUM(umsatz) AS gesamtumsatz
    FROM kunden
    GROUP BY land
) AS daten
WHERE gesamtumsatz > 1000;
```

### CTE

```sql
WITH daten AS (
    SELECT land, SUM(umsatz) AS gesamtumsatz
    FROM kunden
    GROUP BY land
)
SELECT *
FROM daten
WHERE gesamtumsatz > 1000;
```

---

## 12. CTE vs Temporary Table

| CTE                                 | Temporary Table                                    |
| ----------------------------------- | -------------------------------------------------- |
| existiert nur während einer Abfrage | kann während der Session länger existieren         |
| wird nicht dauerhaft gespeichert    | wird temporär gespeichert                          |
| gut für Lesbarkeit                  | gut für größere Zwischenergebnisse                 |
| beginnt mit `WITH`                  | wird mit `CREATE TEMP TABLE` oder `#temp` erstellt |

---

## 13. Häufige Fehler

### 13.1 CTE nicht direkt verwenden

Falsch:

```sql
WITH kunden_deutschland AS (
    SELECT *
    FROM kunden
    WHERE land = 'Deutschland'
);
```

Richtig:

```sql
WITH kunden_deutschland AS (
    SELECT *
    FROM kunden
    WHERE land = 'Deutschland'
)
SELECT *
FROM kunden_deutschland;
```

Nach einer CTE muss direkt eine Hauptabfrage kommen.

---

### 13.2 Komma zwischen mehreren CTEs vergessen

Falsch:

```sql
WITH cte1 AS (
    SELECT *
    FROM kunden
)
cte2 AS (
    SELECT *
    FROM bestellungen
)
SELECT *
FROM cte1;
```

Richtig:

```sql
WITH cte1 AS (
    SELECT *
    FROM kunden
),
cte2 AS (
    SELECT *
    FROM bestellungen
)
SELECT *
FROM cte1;
```

---

### 13.3 Alias oder Spaltennamen unklar

Besser ist es, klare Namen für CTEs und Spalten zu verwenden.

```sql
WITH umsatz_pro_land AS (
    SELECT
        land,
        SUM(umsatz) AS gesamtumsatz
    FROM kunden
    GROUP BY land
)
SELECT *
FROM umsatz_pro_land;
```

---

## 14. Mini-Beispiel komplett

```sql
WITH kunden_umsatz AS (
    SELECT
        kunden_id,
        name,
        land,
        umsatz,
        CASE
            WHEN umsatz >= 1000 THEN 'VIP'
            WHEN umsatz >= 500 THEN 'Premium'
            ELSE 'Normal'
        END AS kundenklasse
    FROM kunden
),
ranking AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY land
            ORDER BY umsatz DESC
        ) AS rang_pro_land
    FROM kunden_umsatz
)
SELECT *
FROM ranking
WHERE rang_pro_land <= 3
ORDER BY land, rang_pro_land;
```

### Erklärung

Dieser Befehl:

* erstellt zuerst eine Kundenklasse mit `CASE WHEN`,
* berechnet danach ein Ranking pro Land,
* zeigt nur die Top 3 Kunden pro Land,
* sortiert nach Land und Rang.

---

## 15. Merksätze

* `CTE` bedeutet `Common Table Expression`.
* Eine CTE beginnt mit `WITH`.
* Eine CTE ist wie eine benannte Subquery.
* Nach der CTE muss direkt eine Hauptabfrage folgen.
* CTEs machen komplexe Abfragen lesbarer.
* Mehrere CTEs werden mit Komma getrennt.
* CTEs sind sehr praktisch mit Window Functions.
* Rekursive CTEs können hierarchische Daten verarbeiten.
* Eine CTE existiert nur während der aktuellen Abfrage.



# SQL Zusammenfassung – Teil 16: Views

## 1. Was ist eine View?

Eine `View` ist eine gespeicherte SQL-Abfrage.

Man kann sich eine View wie eine virtuelle Tabelle vorstellen.

Die Daten werden meistens nicht direkt in der View gespeichert, sondern aus den zugrunde liegenden Tabellen gelesen.

Eine View basiert auf einer `SELECT`-Abfrage.

---

## 2. Warum verwendet man Views?

Views werden verwendet, um:

* komplexe Abfragen wiederzuverwenden
* SQL-Code übersichtlicher zu machen
* bestimmte Spalten oder Zeilen zu verstecken
* Zugriff auf Daten zu vereinfachen
* Sicherheit zu erhöhen
* Berichte oder Auswertungen vorzubereiten

---

## 3. Allgemeine Struktur

```sql
CREATE VIEW view_name AS
SELECT spalte1, spalte2
FROM tabelle
WHERE bedingung;
```

---

## 4. Einfaches Beispiel

```sql
CREATE VIEW kunden_deutschland AS
SELECT
    kunden_id,
    name,
    stadt,
    land
FROM kunden
WHERE land = 'Deutschland';
```

### Erklärung

Diese View enthält nur Kunden aus Deutschland.

Danach kann man die View wie eine Tabelle abfragen:

```sql
SELECT *
FROM kunden_deutschland;
```

---

## 5. View mit JOIN

Views sind besonders nützlich bei häufig verwendeten JOIN-Abfragen.

```sql
CREATE VIEW kunden_bestellungen AS
SELECT
    k.kunden_id,
    k.name,
    b.bestellung_id,
    b.bestelldatum,
    b.betrag
FROM kunden k
INNER JOIN bestellungen b
ON k.kunden_id = b.kunden_id;
```

Danach kann man einfach schreiben:

```sql
SELECT *
FROM kunden_bestellungen;
```

Statt jedes Mal den kompletten JOIN neu zu schreiben.

---

## 6. View mit Aggregation

```sql
CREATE VIEW umsatz_pro_land AS
SELECT
    land,
    COUNT(*) AS anzahl_kunden,
    SUM(umsatz) AS gesamtumsatz,
    AVG(umsatz) AS durchschnittsumsatz
FROM kunden
GROUP BY land;
```

### Verwendung

```sql
SELECT *
FROM umsatz_pro_land
WHERE gesamtumsatz > 1000;
```

---

## 7. View mit berechneten Spalten

```sql
CREATE VIEW produkt_preise AS
SELECT
    produkt_id,
    produktname,
    preis,
    rabatt,
    preis - COALESCE(rabatt, 0) AS endpreis
FROM produkte;
```

### Erklärung

Die View zeigt zusätzlich eine berechnete Spalte `endpreis`.

---

## 8. CREATE OR REPLACE VIEW

In manchen Datenbanksystemen kann man eine View direkt ersetzen.

```sql
CREATE OR REPLACE VIEW kunden_deutschland AS
SELECT
    kunden_id,
    name,
    email,
    stadt
FROM kunden
WHERE land = 'Deutschland';
```

### Hinweis

Die genaue Syntax hängt vom Datenbanksystem ab.

| Datenbanksystem | Syntax                     |
| --------------- | -------------------------- |
| PostgreSQL      | `CREATE OR REPLACE VIEW`   |
| MySQL           | `CREATE OR REPLACE VIEW`   |
| SQL Server      | oft `CREATE OR ALTER VIEW` |

---

## 9. CREATE OR ALTER VIEW in SQL Server

```sql
CREATE OR ALTER VIEW kunden_deutschland AS
SELECT
    kunden_id,
    name,
    email,
    stadt
FROM kunden
WHERE land = 'Deutschland';
```

### Erklärung

Wenn die View noch nicht existiert, wird sie erstellt.

Wenn sie bereits existiert, wird sie geändert.

---

## 10. ALTER VIEW

Mit `ALTER VIEW` kann man eine bestehende View ändern.

```sql
ALTER VIEW kunden_deutschland AS
SELECT
    kunden_id,
    name,
    email,
    stadt,
    land
FROM kunden
WHERE land = 'Deutschland';
```

### Wichtig

Man ändert nicht einzelne Spalten wie bei `ALTER TABLE`.

Stattdessen definiert man die gesamte `SELECT`-Abfrage der View neu.

---

## 11. DROP VIEW

Mit `DROP VIEW` löscht man eine View.

```sql
DROP VIEW kunden_deutschland;
```

Mit Sicherheitsprüfung:

```sql
DROP VIEW IF EXISTS kunden_deutschland;
```

### Erklärung

`DROP VIEW` löscht nur die View.

Die zugrunde liegenden Tabellen und Daten bleiben erhalten.

---

## 12. View abfragen

Eine View kann fast wie eine normale Tabelle verwendet werden.

```sql
SELECT *
FROM kunden_deutschland;
```

Mit Filter:

```sql
SELECT *
FROM kunden_deutschland
WHERE stadt = 'Düsseldorf';
```

Mit Sortierung:

```sql
SELECT *
FROM kunden_deutschland
ORDER BY name;
```

---

## 13. View mit Aliasen

In Views sollte man klare Spaltennamen verwenden.

```sql
CREATE VIEW kunden_kontakt AS
SELECT
    kunden_id,
    name AS kundenname,
    COALESCE(email, 'Keine E-Mail') AS email_anzeige,
    COALESCE(telefon, 'Keine Telefonnummer') AS telefon_anzeige
FROM kunden;
```

### Erklärung

Die View hat verständliche Spaltennamen:

* `kundenname`
* `email_anzeige`
* `telefon_anzeige`

---

## 14. Vorteile von Views

| Vorteil              | Erklärung                                                       |
| -------------------- | --------------------------------------------------------------- |
| Wiederverwendbarkeit | Komplexe Abfragen müssen nicht jedes Mal neu geschrieben werden |
| Lesbarkeit           | SQL-Code wird kürzer und übersichtlicher                        |
| Sicherheit           | Man kann nur bestimmte Spalten oder Zeilen freigeben            |
| Abstraktion          | Benutzer müssen die Tabellenstruktur nicht genau kennen         |
| Konsistenz           | Berechnungen und Logik werden zentral definiert                 |

---

## 15. Nachteile von Views

| Nachteil               | Erklärung                                                                  |
| ---------------------- | -------------------------------------------------------------------------- |
| Performance            | Komplexe Views können langsam sein                                         |
| Abhängigkeiten         | Änderungen an Tabellen können Views beschädigen                            |
| Nicht immer updatebar  | Nicht jede View kann mit `INSERT`, `UPDATE` oder `DELETE` verändert werden |
| Versteckte Komplexität | Eine einfache View kann intern eine sehr komplexe Abfrage enthalten        |

---

## 16. Updatebare Views

Manche Views können geändert werden.

Beispiel:

```sql
CREATE VIEW kunden_basis AS
SELECT
    kunden_id,
    name,
    stadt
FROM kunden;
```

Dann ist manchmal möglich:

```sql
UPDATE kunden_basis
SET stadt = 'Köln'
WHERE kunden_id = 1;
```

### Wichtig

Ob eine View updatebar ist, hängt vom Datenbanksystem und von der View-Struktur ab.

Views mit folgenden Elementen sind oft nicht updatebar:

* `GROUP BY`
* Aggregatfunktionen wie `SUM`, `AVG`, `COUNT`
* `DISTINCT`
* komplexe JOINs
* berechnete Spalten
* Set Operators wie `UNION`

---

## 17. WITH CHECK OPTION

`WITH CHECK OPTION` verhindert, dass über eine View Daten eingefügt oder geändert werden, die nicht zur View-Bedingung passen.

### Beispiel

```sql
CREATE VIEW kunden_deutschland AS
SELECT
    kunden_id,
    name,
    land
FROM kunden
WHERE land = 'Deutschland'
WITH CHECK OPTION;
```

### Erklärung

Diese View zeigt nur Kunden aus Deutschland.

Mit `WITH CHECK OPTION` darf man über diese View keinen Kunden einfügen oder ändern, dessen `land` nicht `Deutschland` ist.

Problematisch:

```sql
UPDATE kunden_deutschland
SET land = 'Frankreich'
WHERE kunden_id = 1;
```

Das wird durch `WITH CHECK OPTION` verhindert.

---

## 18. View für Sicherheit

Eine View kann genutzt werden, um sensible Daten zu verstecken.

### Tabelle kunden

| kunden_id | name | email | telefon | gehalt |
| --------- | ---- | ----- | ------- | -----: |

Nicht jeder Benutzer soll `gehalt` sehen.

Dafür kann man eine View erstellen:

```sql
CREATE VIEW kunden_oeffentlich AS
SELECT
    kunden_id,
    name,
    email,
    telefon
FROM kunden;
```

Die Spalte `gehalt` ist in der View nicht enthalten.

---

## 19. Materialized View

Eine normale View speichert meistens keine Daten.

Sie speichert nur die Abfrage.

Eine `Materialized View` speichert dagegen das Ergebnis der Abfrage physisch.

| View-Typ          | Speicherung                        |
| ----------------- | ---------------------------------- |
| Normale View      | speichert nur die SQL-Abfrage      |
| Materialized View | speichert das Ergebnis der Abfrage |

### Vorteil

Materialized Views können schneller sein, besonders bei großen Datenmengen.

### Nachteil

Die gespeicherten Daten müssen aktualisiert werden.

Beispiel in PostgreSQL:

```sql
CREATE MATERIALIZED VIEW umsatz_pro_land_mv AS
SELECT
    land,
    SUM(umsatz) AS gesamtumsatz
FROM kunden
GROUP BY land;
```

Aktualisieren:

```sql
REFRESH MATERIALIZED VIEW umsatz_pro_land_mv;
```

### Hinweis

Nicht jedes Datenbanksystem unterstützt Materialized Views gleich.

---

## 20. Häufige Fehler

### 20.1 ORDER BY in View falsch verwenden

Problematisch:

```sql
CREATE VIEW kunden_sortiert AS
SELECT *
FROM kunden
ORDER BY name;
```

In vielen Datenbanksystemen ist `ORDER BY` in einer View nicht sinnvoll oder nicht erlaubt, außer in speziellen Fällen.

Besser:

```sql
SELECT *
FROM kunden_sortiert
ORDER BY name;
```

Die Sortierung sollte meistens beim Abfragen der View erfolgen.

---

### 20.2 View mit Tabelle verwechseln

Eine normale View speichert meistens keine eigenen Daten.

Wenn sich Daten in der Tabelle ändern, ändert sich auch das Ergebnis der View.

---

### 20.3 Abhängige Tabellen ändern

Wenn eine View auf eine Spalte zugreift und diese Spalte in der Tabelle gelöscht oder umbenannt wird, kann die View nicht mehr funktionieren.

---

## 21. Mini-Beispiel komplett

```sql
CREATE VIEW kunden_report AS
SELECT
    k.kunden_id,
    k.name,
    k.land,
    k.stadt,
    COUNT(b.bestellung_id) AS anzahl_bestellungen,
    COALESCE(SUM(b.betrag), 0) AS gesamtumsatz
FROM kunden k
LEFT JOIN bestellungen b
ON k.kunden_id = b.kunden_id
GROUP BY
    k.kunden_id,
    k.name,
    k.land,
    k.stadt;
```

View verwenden:

```sql
SELECT *
FROM kunden_report
WHERE gesamtumsatz > 1000
ORDER BY gesamtumsatz DESC;
```

### Erklärung

Diese View:

* verbindet Kunden mit Bestellungen,
* zählt die Anzahl der Bestellungen,
* berechnet den Gesamtumsatz pro Kunde,
* zeigt auch Kunden ohne Bestellung,
* ersetzt fehlenden Umsatz mit `0`.

---

## 22. Mini-Übersicht

| Befehl                   | Bedeutung                                          |
| ------------------------ | -------------------------------------------------- |
| `CREATE VIEW`            | erstellt eine View                                 |
| `CREATE OR REPLACE VIEW` | erstellt oder ersetzt eine View                    |
| `CREATE OR ALTER VIEW`   | erstellt oder ändert eine View in SQL Server       |
| `ALTER VIEW`             | ändert eine bestehende View                        |
| `DROP VIEW`              | löscht eine View                                   |
| `WITH CHECK OPTION`      | verhindert Änderungen außerhalb der View-Bedingung |

---

## 23. Merksätze

* Eine View ist eine gespeicherte SQL-Abfrage.
* Eine View verhält sich wie eine virtuelle Tabelle.
* Normale Views speichern meistens keine eigenen Daten.
* Views machen komplexe Abfragen einfacher und wiederverwendbar.
* `DROP VIEW` löscht nur die View, nicht die Tabellen.
* Die erste Definition der View bestimmt die Spalten der View.
* Views können für Sicherheit verwendet werden.
* Nicht jede View ist updatebar.
* `WITH CHECK OPTION` schützt die Bedingung der View.
* Materialized Views speichern das Ergebnis physisch.



# SQL Zusammenfassung – Teil 19: Indexes

## 1. Was ist ein Index?

Ein `Index` ist eine Datenstruktur, die Abfragen schneller machen kann.

Man kann sich einen Index wie das Inhaltsverzeichnis eines Buches vorstellen.

Ohne Index muss die Datenbank viele oder alle Zeilen durchsuchen.

Mit Index kann die Datenbank schneller passende Zeilen finden.

---

## 2. Warum verwendet man Indexes?

Indexes werden verwendet, um Abfragen schneller zu machen, besonders bei:

* `WHERE`
* `JOIN`
* `ORDER BY`
* `GROUP BY`
* `DISTINCT`

### Beispiel

```sql
SELECT *
FROM kunden
WHERE email = 'ali@example.com';
```

Wenn auf `email` ein Index existiert, kann die Datenbank den Kunden schneller finden.

---

## 3. Einfacher Index

```sql
CREATE INDEX idx_kunden_email
ON kunden (email);
```

### Erklärung

| Teil               | Bedeutung                                |
| ------------------ | ---------------------------------------- |
| `CREATE INDEX`     | erstellt einen Index                     |
| `idx_kunden_email` | Name des Index                           |
| `ON kunden`        | Tabelle, auf der der Index erstellt wird |
| `(email)`          | Spalte, die indexiert wird               |

---

## 4. Index löschen

```sql
DROP INDEX idx_kunden_email ON kunden;
```

Hinweis: Die genaue Syntax kann je nach Datenbanksystem unterschiedlich sein.

---

# 5. Clustered Index

Ein `Clustered Index` bestimmt die physische oder logische Reihenfolge der Daten in einer Tabelle.

In SQL Server gilt:

* Eine Tabelle kann nur einen `Clustered Index` haben.
* Die Datenzeilen selbst sind nach diesem Index organisiert.
* Häufig wird der Primary Key als Clustered Index verwendet.

### Beispiel

```sql
CREATE CLUSTERED INDEX idx_kunden_id
ON kunden (kunden_id);
```

### Wann verwendet man einen Clustered Index?

Ein Clustered Index ist gut für:

* Primary Keys
* häufige Bereichsabfragen
* Sortierungen
* Spalten, nach denen oft gesucht oder sortiert wird

### Beispiel

```sql
SELECT *
FROM bestellungen
WHERE bestelldatum BETWEEN '2024-01-01' AND '2024-12-31';
```

Ein Clustered Index auf `bestelldatum` kann für solche Bereichsabfragen hilfreich sein.

---

# 6. Non-Clustered Index

Ein `Non-Clustered Index` ist eine separate Indexstruktur neben der Tabelle.

Er enthält:

* die indexierten Spalten
* einen Verweis auf die eigentlichen Datenzeilen

Eine Tabelle kann mehrere Non-Clustered Indexes haben.

### Beispiel

```sql
CREATE NONCLUSTERED INDEX idx_kunden_email
ON kunden (email);
```

### Wann verwendet man einen Non-Clustered Index?

Ein Non-Clustered Index ist gut für:

* häufige Suchbedingungen
* JOIN-Spalten
* Spalten in `WHERE`
* Spalten in `ORDER BY`

### Beispiel

```sql
SELECT *
FROM kunden
WHERE email = 'ali@example.com';
```

Ein Non-Clustered Index auf `email` ist hier sinnvoll.

---

# 7. Clustered vs Non-Clustered Index

| Eigenschaft        | Clustered Index                           | Non-Clustered Index              |
| ------------------ | ----------------------------------------- | -------------------------------- |
| Speicherort        | organisiert die Daten selbst              | separate Struktur                |
| Anzahl pro Tabelle | meistens nur einer                        | mehrere möglich                  |
| Typischer Einsatz  | Primary Key, Sortierung, Bereichsabfragen | Suche, Filter, JOINs             |
| Datenzugriff       | direkte Datenstruktur                     | verweist auf Datenzeilen         |
| Beispiel           | `kunden_id`                               | `email`, `stadt`, `bestelldatum` |

---

# 8. Rowstore Index

Ein `Rowstore Index` speichert Daten zeilenorientiert.

Das ist die klassische Speicherform in relationalen Datenbanken.

### Beispielhafte Speicherung

| kunden_id | name | stadt      |
| --------- | ---- | ---------- |
| 1         | Ali  | Düsseldorf |
| 2         | Sara | Köln       |

Die Werte einer Zeile werden zusammen gespeichert.

### Wann verwendet man Rowstore?

Rowstore ist gut für:

* OLTP-Systeme
* viele einzelne `INSERT`, `UPDATE`, `DELETE`
* einzelne Zeilen suchen
* Transaktionssysteme
* normale Anwendungen

Beispiele:

* Kundendatenbank
* Bestellsystem
* Login-System
* Warenkorb

---

# 9. Columnstore Index

Ein `Columnstore Index` speichert Daten spaltenorientiert.

Das bedeutet: Werte einer Spalte werden zusammen gespeichert.

### Vereinfachte Vorstellung

```text
kunden_id: 1, 2, 3, 4
name: Ali, Sara, Omar, Lina
umsatz: 100, 200, 300, 400
```

Columnstore ist besonders gut für große Analysen.

### Beispiel

```sql
CREATE COLUMNSTORE INDEX idx_verkaeufe_columnstore
ON verkaeufe (kunde_id, produkt_id, datum, betrag);
```

Oder als Clustered Columnstore Index:

```sql
CREATE CLUSTERED COLUMNSTORE INDEX idx_verkaeufe_cci
ON verkaeufe;
```

---

## 9.1 Wann verwendet man Columnstore?

Columnstore ist gut für:

* große Tabellen
* Reporting
* Analytics
* Data Warehousing
* Aggregationen
* Scans über viele Zeilen
* wenige ausgewählte Spalten aus sehr vielen Daten

### Beispiel

```sql
SELECT
    land,
    SUM(umsatz) AS gesamtumsatz
FROM verkaeufe
GROUP BY land;
```

Bei großen Tabellen kann Columnstore hier sehr effizient sein.

---

# 10. Rowstore vs Columnstore

| Eigenschaft       | Rowstore                             | Columnstore                         |
| ----------------- | ------------------------------------ | ----------------------------------- |
| Speicherung       | zeilenweise                          | spaltenweise                        |
| Gut für           | einzelne Zeilen                      | große Analysen                      |
| Typischer Bereich | OLTP                                 | OLAP / Data Warehouse               |
| Stärken           | `INSERT`, `UPDATE`, einzelne Lookups | `SUM`, `AVG`, `GROUP BY`, Scans     |
| Beispiel          | Kundensuche per ID                   | Umsatzanalyse über Millionen Zeilen |

### Merksatz

Rowstore ist gut für operative Systeme.

Columnstore ist gut für analytische Systeme.

---

# 11. Unique Index

Ein `Unique Index` stellt sicher, dass Werte in einer Spalte oder Spaltenkombination eindeutig sind.

### Beispiel

```sql
CREATE UNIQUE INDEX idx_kunden_email_unique
ON kunden (email);
```

### Erklärung

Damit darf dieselbe E-Mail-Adresse nicht mehrfach vorkommen.

---

## 11.1 Unique Index auf mehrere Spalten

```sql
CREATE UNIQUE INDEX idx_kunde_produkt_unique
ON bestellungen (kunden_id, produkt_id);
```

### Erklärung

Die Kombination aus `kunden_id` und `produkt_id` muss eindeutig sein.

Das bedeutet:

| kunden_id | produkt_id | erlaubt?      |
| --------- | ---------- | ------------- |
| 1         | 10         | Ja            |
| 1         | 10         | Nein, doppelt |
| 1         | 20         | Ja            |
| 2         | 10         | Ja            |

---

## 11.2 Unique Index vs Primary Key

| Eigenschaft         | Primary Key                           | Unique Index                 |
| ------------------- | ------------------------------------- | ---------------------------- |
| Zweck               | eindeutige Identifikation einer Zeile | eindeutige Werte erzwingen   |
| Anzahl pro Tabelle  | nur einer                             | mehrere möglich              |
| NULL erlaubt?       | normalerweise nein                    | abhängig vom Datenbanksystem |
| Erstellt oft Index? | ja                                    | ja                           |

---

# 12. Filtered Index

Ein `Filtered Index` ist ein Index auf nur einen Teil der Tabelle.

Er enthält nur Zeilen, die eine bestimmte Bedingung erfüllen.

Das ist besonders nützlich, wenn man oft nur einen bestimmten Teil der Daten abfragt.

### Beispiel

```sql
CREATE INDEX idx_aktive_kunden
ON kunden (kunden_id)
WHERE status = 'Aktiv';
```

### Erklärung

Der Index enthält nur Kunden mit:

```sql
status = 'Aktiv'
```

---

## 12.1 Wann verwendet man Filtered Indexes?

Filtered Indexes sind gut, wenn:

* nur ein Teil der Tabelle häufig abgefragt wird
* die Bedingung sehr oft vorkommt
* viele Zeilen für die Abfrage irrelevant sind
* man Speicherplatz sparen möchte

### Beispiel

```sql
SELECT *
FROM kunden
WHERE status = 'Aktiv';
```

Ein Filtered Index auf aktive Kunden kann diese Abfrage schneller machen.

---

## 12.2 Filtered Index für NULL-Werte

Ein häufiger Use Case ist ein Index auf nicht abgeschlossene Datensätze.

```sql
CREATE INDEX idx_offene_bestellungen
ON bestellungen (bestelldatum)
WHERE abgeschlossen_am IS NULL;
```

### Beispielabfrage

```sql
SELECT *
FROM bestellungen
WHERE abgeschlossen_am IS NULL
ORDER BY bestelldatum;
```

---

# 13. Composite Index

Ein `Composite Index` ist ein Index auf mehrere Spalten.

```sql
CREATE INDEX idx_kunden_land_stadt
ON kunden (land, stadt);
```

### Wichtig

Die Reihenfolge der Spalten ist wichtig.

Ein Index auf:

```sql
(land, stadt)
```

hilft gut bei:

```sql
WHERE land = 'Deutschland'
```

und bei:

```sql
WHERE land = 'Deutschland'
AND stadt = 'Düsseldorf'
```

Aber oft weniger gut bei:

```sql
WHERE stadt = 'Düsseldorf'
```

weil `stadt` nicht die erste Spalte im Index ist.

---

# 14. Covering Index und INCLUDE

Ein `Covering Index` enthält alle Spalten, die eine Abfrage braucht.

In SQL Server kann man zusätzliche Spalten mit `INCLUDE` hinzufügen.

```sql
CREATE INDEX idx_kunden_email_include
ON kunden (email)
INCLUDE (name, stadt);
```

### Beispiel

```sql
SELECT name, stadt
FROM kunden
WHERE email = 'ali@example.com';
```

Die Datenbank kann die Abfrage direkt aus dem Index beantworten, ohne extra in die Tabelle zu schauen.

---

# 15. Wann sollte man einen Index erstellen?

Ein Index ist sinnvoll auf Spalten, die häufig verwendet werden in:

| SQL-Teil   | Beispiel                                       |
| ---------- | ---------------------------------------------- |
| `WHERE`    | `WHERE email = ...`                            |
| `JOIN`     | `ON kunden.kunden_id = bestellungen.kunden_id` |
| `ORDER BY` | `ORDER BY bestelldatum`                        |
| `GROUP BY` | `GROUP BY land`                                |
| `UNIQUE`   | eindeutige E-Mail-Adressen                     |

---

# 16. Wann sollte man vorsichtig mit Indexes sein?

Zu viele Indexes können problematisch sein.

Nachteile:

* brauchen zusätzlichen Speicherplatz
* machen `INSERT`, `UPDATE`, `DELETE` langsamer
* müssen gepflegt werden
* können bei falscher Nutzung wenig bringen

### Nicht jede Spalte braucht einen Index.

Weniger sinnvoll sind Indexes oft bei:

* sehr kleinen Tabellen
* Spalten mit sehr wenigen unterschiedlichen Werten
* Spalten, die selten gefiltert werden
* Spalten, die sehr häufig geändert werden

---

# 17. Sargability

Eine Abfrage ist besser für Indexes geeignet, wenn die Spalte direkt vergleichbar bleibt.

Gut:

```sql
SELECT *
FROM bestellungen
WHERE bestelldatum >= '2024-01-01'
AND bestelldatum < '2025-01-01';
```

Problematisch:

```sql
SELECT *
FROM bestellungen
WHERE YEAR(bestelldatum) = 2024;
```

Warum?

Bei der zweiten Abfrage wird eine Funktion auf die Spalte angewendet.

Dadurch kann der Index auf `bestelldatum` oft schlechter genutzt werden.

---

# 18. Häufige Index-Typen im Überblick

| Index-Typ           | Zweck                                    | Beispiel                 |
| ------------------- | ---------------------------------------- | ------------------------ |
| Clustered Index     | Daten physisch/logisch sortieren         | Primary Key              |
| Non-Clustered Index | schnelle Suche über separate Struktur    | `email`, `stadt`         |
| Rowstore Index      | klassische zeilenorientierte Speicherung | OLTP                     |
| Columnstore Index   | spaltenorientierte Speicherung           | Analytics                |
| Unique Index        | Eindeutigkeit erzwingen                  | `email`                  |
| Filtered Index      | nur bestimmte Zeilen indexieren          | `WHERE status = 'Aktiv'` |
| Composite Index     | mehrere Spalten indexieren               | `(land, stadt)`          |
| Covering Index      | alle benötigten Spalten im Index         | `INCLUDE`                |

---

# 19. Praktisches Beispiel

```sql
CREATE TABLE kunden (
    kunden_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    land VARCHAR(100),
    stadt VARCHAR(100),
    status VARCHAR(20)
);
```

### Unique Index auf E-Mail

```sql
CREATE UNIQUE INDEX idx_kunden_email_unique
ON kunden (email);
```

### Composite Index auf Land und Stadt

```sql
CREATE INDEX idx_kunden_land_stadt
ON kunden (land, stadt);
```

### Filtered Index auf aktive Kunden

```sql
CREATE INDEX idx_kunden_aktiv
ON kunden (kunden_id)
WHERE status = 'Aktiv';
```

### Non-Clustered Index mit INCLUDE

```sql
CREATE INDEX idx_kunden_email_lookup
ON kunden (email)
INCLUDE (name, stadt);
```

---

# 20. Entscheidungsregel

| Situation                                          | Passender Index       |
| -------------------------------------------------- | --------------------- |
| Tabelle braucht eine Hauptsortierung / Primary Key | `Clustered Index`     |
| Häufige Suche nach einer Spalte                    | `Non-Clustered Index` |
| Werte müssen eindeutig sein                        | `Unique Index`        |
| Nur bestimmte Zeilen werden oft gesucht            | `Filtered Index`      |
| Analyse über sehr große Datenmengen                | `Columnstore Index`   |
| Mehrere Filterspalten werden zusammen verwendet    | `Composite Index`     |
| Abfrage soll komplett aus Index beantwortet werden | `Covering Index`      |

---

# 21. Merksätze

* Indexes machen Leseabfragen oft schneller.
* Indexes machen Schreiboperationen oft langsamer.
* Ein Clustered Index bestimmt die Datenreihenfolge.
* Eine Tabelle hat normalerweise nur einen Clustered Index.
* Eine Tabelle kann mehrere Non-Clustered Indexes haben.
* Rowstore ist gut für einzelne Zeilen und Transaktionen.
* Columnstore ist gut für große Analysen und Aggregationen.
* Unique Indexes verhindern doppelte Werte.
* Filtered Indexes indexieren nur einen Teil der Tabelle.
* Bei Composite Indexes ist die Reihenfolge der Spalten wichtig.
* Zu viele Indexes können Performance verschlechtern.


## Zusatz: Clustered vs Nonclustered Columnstore Index

## 1. Kurzer Vergleich zu Rowstore

Bei einem normalen **Rowstore Index** werden Daten zeilenorientiert gespeichert.

### Clustered Rowstore Index

Ein `Clustered Rowstore Index` bestimmt die Speicherstruktur der Tabelle.

Die Datenzeilen der Tabelle sind nach dem Clustered-Index-Key organisiert.

Beispiel:

```sql
CREATE CLUSTERED INDEX idx_kunden_id
ON kunden (kunden_id);
```

Typisch ist ein Clustered Index auf einer ID-Spalte.

Wichtig:

* Die Tabelle hat meistens nur einen Clustered Index.
* Der Clustered Index organisiert die Tabelle selbst.
* Die Daten sind logisch nach dem Index-Key geordnet.
* Trotzdem garantiert nur `ORDER BY` eine sortierte Ausgabe.

---

### Nonclustered Rowstore Index

Ein `Nonclustered Rowstore Index` ist eine zusätzliche separate Indexstruktur.

Die Tabelle selbst bleibt unverändert gespeichert.

Der Nonclustered Index enthält:

* die indexierte Spalte
* einen Verweis auf die eigentliche Tabellenzeile
* optional weitere Spalten mit `INCLUDE`

Beispiel:

```sql
CREATE NONCLUSTERED INDEX idx_kunden_email
ON kunden (email);
```

Verwendung:

```sql
SELECT *
FROM kunden
WHERE email = 'ali@example.com';
```

---

# 2. Clustered Columnstore Index

Ein `Clustered Columnstore Index` speichert die ganze Tabelle spaltenorientiert.

Das bedeutet:

Die Tabelle wird nicht mehr hauptsächlich zeilenweise gespeichert, sondern spaltenweise.

Beispiel:

```sql
CREATE CLUSTERED COLUMNSTORE INDEX idx_verkaeufe_cci
ON verkaeufe;
```

## Wichtig

Ein Clustered Columnstore Index sortiert die Tabelle nicht nach einer einzelnen Spalte wie ein Clustered Rowstore Index.

Stattdessen werden die Daten in sogenannten Rowgroups und Column Segments gespeichert.

Vereinfacht:

```text
kunden_id: 1, 2, 3, 4
produkt_id: 10, 20, 10, 30
betrag: 100, 200, 150, 300
datum: 2024-01-01, 2024-01-02, ...
```

Die Werte einer Spalte werden zusammen gespeichert.

## Wann verwendet man Clustered Columnstore?

Ein Clustered Columnstore Index ist gut für:

* große Tabellen
* Data Warehouse
* Reporting
* Analytics
* Aggregationen wie `SUM`, `AVG`, `COUNT`
* Abfragen über Millionen von Zeilen
* Tabellen, bei denen meistens viele Zeilen, aber wenige Spalten gelesen werden

Beispiel:

```sql
SELECT
    land,
    SUM(umsatz) AS gesamtumsatz
FROM verkaeufe
GROUP BY land;
```

---

# 3. Nonclustered Columnstore Index

Ein `Nonclustered Columnstore Index` ist eine zusätzliche spaltenorientierte Indexstruktur auf einer bestehenden Tabelle.

Die Originaltabelle bleibt dabei erhalten.

Das bedeutet:

* Die Tabelle kann weiterhin als Rowstore gespeichert sein.
* Zusätzlich gibt es einen Columnstore Index für analytische Abfragen.

Beispiel:

```sql
CREATE NONCLUSTERED COLUMNSTORE INDEX idx_verkaeufe_ncci
ON verkaeufe (kunde_id, produkt_id, datum, betrag);
```

## Erklärung

Die Tabelle `verkaeufe` bleibt normal gespeichert.

Zusätzlich werden die Spalten:

* `kunde_id`
* `produkt_id`
* `datum`
* `betrag`

spaltenorientiert im Nonclustered Columnstore Index gespeichert.

## Wann verwendet man Nonclustered Columnstore?

Ein Nonclustered Columnstore Index ist gut, wenn:

* die Tabelle weiterhin für normale Transaktionen verwendet wird
* man zusätzlich analytische Abfragen beschleunigen möchte
* man nicht die ganze Tabelle in Columnstore umwandeln will
* nur bestimmte Spalten für Analysen wichtig sind

Beispiel:

```sql
SELECT
    produkt_id,
    SUM(betrag) AS gesamtumsatz
FROM verkaeufe
GROUP BY produkt_id;
```

---

# 4. Clustered vs Nonclustered Columnstore

| Eigenschaft                        | Clustered Columnstore                | Nonclustered Columnstore                 |
| ---------------------------------- | ------------------------------------ | ---------------------------------------- |
| Speicherart der Tabelle            | Tabelle selbst wird columnstore      | zusätzliche columnstore-Struktur         |
| Originale Rowstore-Tabelle bleibt? | Nein, die Tabelle wird columnstore   | Ja, bleibt erhalten                      |
| Enthält welche Daten?              | meistens alle Spalten der Tabelle    | ausgewählte Spalten                      |
| Typischer Einsatz                  | Data Warehouse, große Faktentabellen | Mischung aus OLTP und Analytics          |
| Gut für                            | große Analysen                       | zusätzliche Analyse auf Rowstore-Tabelle |
| Sortiert nach einer ID?            | Nein                                 | Nein                                     |
| Vergleichbar mit                   | Hauptspeicherform der Tabelle        | zusätzlicher Analyseindex                |

---

# 5. Einfacher Merksatz

## Rowstore

Bei Rowstore geht es um Zeilen.

* `Clustered Rowstore`: Die Tabelle selbst ist nach dem Index-Key organisiert.
* `Nonclustered Rowstore`: Zusätzlicher Index mit Verweis auf die Tabelle.

## Columnstore

Bei Columnstore geht es um Spalten.

* `Clustered Columnstore`: Die ganze Tabelle wird spaltenorientiert gespeichert.
* `Nonclustered Columnstore`: Zusätzlicher spaltenorientierter Index auf einer bestehenden Tabelle.

---

# 6. Sehr einfache Erklärung

```text
Clustered Rowstore:
Die Tabelle selbst ist nach einer Spalte organisiert.

Nonclustered Rowstore:
Nur ein zusätzlicher Suchindex wird erstellt.

Clustered Columnstore:
Die ganze Tabelle wird spaltenweise gespeichert.

Nonclustered Columnstore:
Eine zusätzliche spaltenweise Kopie bestimmter Spalten wird erstellt.
```

---

# 7. Wann nehme ich was?

| Situation                                                 | Beste Wahl               |
| --------------------------------------------------------- | ------------------------ |
| Viele einzelne Zeilen suchen oder ändern                  | Rowstore                 |
| Primary Key / ID-basierte Suche                           | Clustered Rowstore       |
| Suche nach Email, Status, Datum usw.                      | Nonclustered Rowstore    |
| Große Analyse über sehr viele Zeilen                      | Clustered Columnstore    |
| Bestehende OLTP-Tabelle zusätzlich für Reports optimieren | Nonclustered Columnstore |
| Große `SUM`, `AVG`, `COUNT`, `GROUP BY` Abfragen          | Columnstore              |



# SQL Zusammenfassung – Teil 20: Table Partitioning

## 1. Was ist Table Partitioning?

`Table Partitioning` bedeutet, dass eine große Tabelle intern in kleinere Teile aufgeteilt wird.

Diese Teile nennt man **Partitionen**.

Für den Benutzer sieht es weiterhin wie **eine einzige Tabelle** aus.

Intern kann die Datenbank die Daten aber besser verwalten und schneller auf bestimmte Bereiche zugreifen.

---

## 2. Warum verwendet man Partitioning?

Partitioning wird vor allem bei sehr großen Tabellen verwendet.

Typische Ziele:

* bessere Performance bei großen Tabellen
* schnellere Abfragen auf bestimmte Zeiträume
* einfachere Verwaltung alter Daten
* schnelleres Laden oder Löschen großer Datenmengen
* bessere Wartung von großen Tabellen und Indexes

---

## 3. Einfaches Beispiel

Eine Tabelle `verkaeufe` enthält Daten aus mehreren Jahren.

| verkauf_id | datum      | betrag |
| ---------- | ---------- | -----: |
| 1          | 2022-03-10 |    100 |
| 2          | 2023-07-15 |    250 |
| 3          | 2024-01-20 |    300 |
| 4          | 2025-05-05 |    150 |

Die Tabelle kann nach Jahr partitioniert werden:

| Partition   | Daten             |
| ----------- | ----------------- |
| Partition 1 | Verkäufe aus 2022 |
| Partition 2 | Verkäufe aus 2023 |
| Partition 3 | Verkäufe aus 2024 |
| Partition 4 | Verkäufe aus 2025 |

---

# 4. Horizontal Partitioning

Bei normalem Table Partitioning spricht man meistens von **Horizontal Partitioning**.

Das bedeutet:

Die Tabelle wird nach **Zeilen** aufgeteilt.

Beispiel:

```text
Alle Verkäufe aus 2022 → Partition 1
Alle Verkäufe aus 2023 → Partition 2
Alle Verkäufe aus 2024 → Partition 3
```

Die Spalten bleiben gleich.

Nur die Zeilen werden aufgeteilt.

---

# 5. Partition Key

Der `Partition Key` ist die Spalte, nach der die Tabelle aufgeteilt wird.

Typische Partition Keys:

* Datum
* Jahr
* Monat
* Region
* Kundenbereich
* Kategorie

Beispiel:

```sql
datum
```

Wenn eine Tabelle nach `datum` partitioniert ist, entscheidet der Datumswert, in welche Partition eine Zeile gespeichert wird.

---

# 6. Partitioning nach Datum

Sehr häufig wird nach Datum partitioniert.

Beispiel:

```sql
verkaufsdatum
```

Mögliche Partitionen:

| Partition | Bereich        |
| --------- | -------------- |
| P1        | Daten vor 2023 |
| P2        | Daten aus 2023 |
| P3        | Daten aus 2024 |
| P4        | Daten aus 2025 |

Das ist besonders nützlich für Tabellen wie:

* Bestellungen
* Verkäufe
* Logs
* Messwerte
* Transaktionen

---

# 7. Partition Elimination

`Partition Elimination` bedeutet, dass die Datenbank nur relevante Partitionen durchsucht.

Beispiel:

```sql
SELECT *
FROM verkaeufe
WHERE verkaufsdatum >= '2024-01-01'
AND verkaufsdatum < '2025-01-01';
```

Wenn die Tabelle nach `verkaufsdatum` partitioniert ist, muss die Datenbank im Idealfall nur die Partition für 2024 lesen.

Sie muss nicht die Daten aus 2022, 2023 oder 2025 durchsuchen.

---

## 7.1 Wichtig für Partition Elimination

Die Abfrage sollte direkt auf den Partition Key filtern.

Gut:

```sql
WHERE verkaufsdatum >= '2024-01-01'
AND verkaufsdatum < '2025-01-01'
```

Problematisch:

```sql
WHERE YEAR(verkaufsdatum) = 2024
```

Warum?

Bei der zweiten Variante wird eine Funktion auf die Spalte angewendet.

Dadurch kann die Datenbank Partition Elimination eventuell schlechter nutzen.

---

# 8. Partitioning in SQL Server

In SQL Server verwendet man für Partitioning häufig:

* `PARTITION FUNCTION`
* `PARTITION SCHEME`
* partitionierte Tabelle

---

## 8.1 Partition Function

Die `PARTITION FUNCTION` definiert die Grenzen der Partitionen.

Beispiel:

```sql
CREATE PARTITION FUNCTION pf_verkaeufe_jahr (DATE)
AS RANGE RIGHT FOR VALUES
(
    '2023-01-01',
    '2024-01-01',
    '2025-01-01'
);
```

### Erklärung

Die Grenzwerte bestimmen, wo neue Partitionen beginnen.

Mit `RANGE RIGHT` gehört der Grenzwert zur rechten Partition.

Beispiel:

| Bereich                              | Partition   |
| ------------------------------------ | ----------- |
| vor `2023-01-01`                     | Partition 1 |
| ab `2023-01-01` bis vor `2024-01-01` | Partition 2 |
| ab `2024-01-01` bis vor `2025-01-01` | Partition 3 |
| ab `2025-01-01`                      | Partition 4 |

---

## 8.2 Partition Scheme

Das `PARTITION SCHEME` legt fest, auf welchen Speicherbereichen die Partitionen liegen.

Einfaches Beispiel:

```sql
CREATE PARTITION SCHEME ps_verkaeufe_jahr
AS PARTITION pf_verkaeufe_jahr
ALL TO ([PRIMARY]);
```

### Erklärung

Hier werden alle Partitionen auf `[PRIMARY]` gespeichert.

In größeren Systemen können Partitionen auch auf verschiedene Filegroups verteilt werden.

---

## 8.3 Partitionierte Tabelle erstellen

```sql
CREATE TABLE verkaeufe (
    verkauf_id INT,
    verkaufsdatum DATE,
    betrag DECIMAL(10,2)
)
ON ps_verkaeufe_jahr (verkaufsdatum);
```

### Erklärung

Die Tabelle `verkaeufe` wird anhand der Spalte `verkaufsdatum` partitioniert.

---

# 9. RANGE LEFT vs RANGE RIGHT

Bei SQL Server gibt es `RANGE LEFT` und `RANGE RIGHT`.

Sie bestimmen, zu welcher Partition der Grenzwert gehört.

## RANGE RIGHT

```sql
AS RANGE RIGHT FOR VALUES ('2024-01-01')
```

Dann gehört `2024-01-01` zur rechten Partition.

## RANGE LEFT

```sql
AS RANGE LEFT FOR VALUES ('2024-01-01')
```

Dann gehört `2024-01-01` zur linken Partition.

### Merksatz

Für Datumsbereiche wird oft `RANGE RIGHT` verwendet, weil ein neues Jahr oder ein neuer Monat sauber mit dem Grenzwert beginnt.

---

# 10. Beispiel: Monatliches Partitioning

Eine große Log-Tabelle kann nach Monaten partitioniert werden.

```sql
CREATE PARTITION FUNCTION pf_logs_monat (DATE)
AS RANGE RIGHT FOR VALUES
(
    '2024-01-01',
    '2024-02-01',
    '2024-03-01',
    '2024-04-01'
);
```

Dadurch entstehen Partitionen wie:

| Partition | Bereich         |
| --------- | --------------- |
| P1        | vor Januar 2024 |
| P2        | Januar 2024     |
| P3        | Februar 2024    |
| P4        | März 2024       |
| P5        | ab April 2024   |

---

# 11. Partitioned Index

Indexes können ebenfalls partitioniert sein.

Wenn Tabelle und Index nach demselben Partition Key partitioniert sind, spricht man oft von einem **aligned index**.

### Beispiel

```sql
CREATE INDEX idx_verkaeufe_datum
ON verkaeufe (verkaufsdatum)
ON ps_verkaeufe_jahr (verkaufsdatum);
```

### Vorteil

Ein partitionierter Index kann Wartung und Abfragen auf großen Tabellen verbessern.

---

# 12. Partition Switching

`Partition Switching` bedeutet, dass man eine ganze Partition sehr schnell austauschen oder verschieben kann.

Das ist besonders nützlich für:

* Archivierung
* Laden neuer Monatsdaten
* Entfernen alter Daten
* ETL-Prozesse

Beispielidee:

```text
Alte Partition aus Haupttabelle entfernen
und in Archiv-Tabelle verschieben.
```

In SQL Server wird dafür häufig `ALTER TABLE ... SWITCH` verwendet.

---

# 13. Partitioning vs Index

Partitioning und Indexes sind nicht dasselbe.

| Konzept      | Bedeutung                                  |
| ------------ | ------------------------------------------ |
| Partitioning | teilt eine große Tabelle in kleinere Teile |
| Index        | hilft, Daten schneller zu finden           |

Eine Tabelle kann partitioniert sein und zusätzlich Indexes haben.

### Beispiel

Eine Tabelle `verkaeufe` kann:

* nach `verkaufsdatum` partitioniert sein
* einen Index auf `kunde_id` haben
* einen Index auf `produkt_id` haben

---

# 14. Wann ist Partitioning sinnvoll?

Partitioning ist sinnvoll, wenn:

* die Tabelle sehr groß ist
* Daten natürlich in Bereiche aufgeteilt werden können
* oft nach dem Partition Key gefiltert wird
* alte Daten regelmäßig archiviert oder gelöscht werden
* große Datenmengen regelmäßig geladen werden
* Reports oft auf bestimmte Zeiträume zugreifen

Typische Tabellen:

| Tabelle         | Partition Key       |
| --------------- | ------------------- |
| `verkaeufe`     | `verkaufsdatum`     |
| `bestellungen`  | `bestelldatum`      |
| `logs`          | `log_datum`         |
| `messwerte`     | `messzeit`          |
| `transaktionen` | `transaktionsdatum` |

---

# 15. Wann ist Partitioning nicht sinnvoll?

Partitioning ist oft nicht sinnvoll, wenn:

* die Tabelle klein ist
* die Abfragen nicht nach dem Partition Key filtern
* die Daten keinen klaren Bereich haben
* die Verwaltung dadurch unnötig komplex wird
* normale Indexes bereits ausreichend sind

Beispiel:

```sql
SELECT *
FROM verkaeufe
WHERE kunde_id = 123;
```

Wenn die Tabelle nach `verkaufsdatum` partitioniert ist, aber die Abfrage nur nach `kunde_id` sucht, bringt Partitioning eventuell wenig.

---

# 16. Vorteile von Table Partitioning

| Vorteil               | Erklärung                                                  |
| --------------------- | ---------------------------------------------------------- |
| Bessere Verwaltung    | große Tabellen werden logisch aufgeteilt                   |
| Partition Elimination | irrelevante Partitionen werden übersprungen                |
| Schnellere Wartung    | einzelne Partitionen können bearbeitet werden              |
| Archivierung          | alte Daten können leichter verschoben werden               |
| Datenladen            | neue Partitionen können vorbereitet und eingespielt werden |

---

# 17. Nachteile von Table Partitioning

| Nachteil                    | Erklärung                                     |
| --------------------------- | --------------------------------------------- |
| Komplexität                 | Einrichtung und Wartung sind aufwendiger      |
| Nicht automatisch schneller | bringt nur Vorteile bei passenden Abfragen    |
| Falscher Partition Key      | kann Performance kaum verbessern              |
| Index-Wartung               | partitionierte Indexes müssen gepflegt werden |
| Kleine Tabellen             | profitieren meistens nicht                    |

---

# 18. Partitioning vs Sharding

Partitioning und Sharding werden manchmal verwechselt.

| Begriff      | Bedeutung                                       |
| ------------ | ----------------------------------------------- |
| Partitioning | Aufteilung innerhalb derselben Datenbank        |
| Sharding     | Aufteilung über mehrere Datenbanken oder Server |

### Merksatz

Partitioning passiert meistens innerhalb einer Datenbank.

Sharding verteilt Daten auf mehrere Systeme.

---

# 19. Praktisches Beispiel komplett

```sql
CREATE PARTITION FUNCTION pf_bestellungen_jahr (DATE)
AS RANGE RIGHT FOR VALUES
(
    '2023-01-01',
    '2024-01-01',
    '2025-01-01'
);

CREATE PARTITION SCHEME ps_bestellungen_jahr
AS PARTITION pf_bestellungen_jahr
ALL TO ([PRIMARY]);

CREATE TABLE bestellungen (
    bestellung_id INT,
    kunde_id INT,
    bestelldatum DATE,
    betrag DECIMAL(10,2)
)
ON ps_bestellungen_jahr (bestelldatum);
```

Abfrage:

```sql
SELECT *
FROM bestellungen
WHERE bestelldatum >= '2024-01-01'
AND bestelldatum < '2025-01-01';
```

### Erklärung

Dieser Befehl:

* erstellt eine Partition Function,
* erstellt ein Partition Scheme,
* erstellt eine partitionierte Tabelle,
* partitioniert die Tabelle nach `bestelldatum`,
* ermöglicht bei passenden Filtern Partition Elimination.

---

# 20. Mini-Übersicht

| Begriff                 | Bedeutung                                    |
| ----------------------- | -------------------------------------------- |
| `Partitioning`          | Aufteilen einer Tabelle in kleinere Teile    |
| `Partition`             | einzelner Teil einer partitionierten Tabelle |
| `Partition Key`         | Spalte, nach der partitioniert wird          |
| `Partition Function`    | definiert die Grenzen der Partitionen        |
| `Partition Scheme`      | legt Speicherort der Partitionen fest        |
| `Partition Elimination` | Datenbank liest nur relevante Partitionen    |
| `Partition Switching`   | schnelles Austauschen ganzer Partitionen     |

---

# 21. Entscheidungsregel

| Frage                                         | Empfehlung                          |
| --------------------------------------------- | ----------------------------------- |
| Ist die Tabelle sehr groß?                    | Partitioning kann sinnvoll sein     |
| Gibt es einen klaren Zeitbezug?               | Partitioning nach Datum ist oft gut |
| Werden oft bestimmte Zeiträume abgefragt?     | Partitioning kann helfen            |
| Werden alte Daten regelmäßig archiviert?      | Partitioning ist sehr nützlich      |
| Wird selten nach dem Partition Key gefiltert? | Partitioning bringt wenig           |
| Ist die Tabelle klein?                        | normale Indexes reichen meistens    |

---

# 22. Merksätze

* Table Partitioning teilt große Tabellen intern in kleinere Teile.
* Für Benutzer sieht es weiterhin wie eine Tabelle aus.
* Der Partition Key entscheidet, in welche Partition eine Zeile kommt.
* Häufig wird nach Datum partitioniert.
* Partition Elimination kann Abfragen schneller machen.
* Partitioning ist nicht automatisch schneller.
* Partitioning hilft nur, wenn Abfragen den Partition Key sinnvoll nutzen.
* Partitioning ist besonders nützlich für sehr große Tabellen, Archivierung und Data Warehousing.
* Indexes und Partitioning ergänzen sich.
* Sharding ist nicht dasselbe wie Partitioning.


# SQL Zusammenfassung – Teil 21: Stored Procedures

## 1. Was ist eine Stored Procedure?

Eine `Stored Procedure` ist ein gespeicherter SQL-Codeblock in der Datenbank.

Sie kann mehrere SQL-Befehle enthalten und später wieder ausgeführt werden.

Man kann sich eine Stored Procedure wie eine Funktion oder ein Programm in der Datenbank vorstellen.

---

## 2. Warum verwendet man Stored Procedures?

Stored Procedures werden verwendet, um:

* wiederkehrende SQL-Logik zu speichern
* komplexe Abläufe zu kapseln
* Parameter zu verwenden
* Daten zu lesen, einzufügen, zu ändern oder zu löschen
* Business-Logik zentral in der Datenbank abzulegen
* Sicherheit und Rechte besser zu kontrollieren

---

## 3. Allgemeine Struktur

```sql
CREATE PROCEDURE procedure_name
AS
BEGIN
    -- SQL-Befehle
END;
```

---

## 4. Einfaches Beispiel

```sql
CREATE PROCEDURE get_all_customers
AS
BEGIN
    SELECT *
    FROM kunden;
END;
```

Procedure ausführen:

```sql
EXEC get_all_customers;
```

oder:

```sql
EXECUTE get_all_customers;
```

---

## 5. Stored Procedure mit Parameter

Parameter machen eine Stored Procedure flexibler.

### Beispiel

```sql
CREATE PROCEDURE get_customers_by_city
    @stadt VARCHAR(100)
AS
BEGIN
    SELECT *
    FROM kunden
    WHERE stadt = @stadt;
END;
```

Procedure ausführen:

```sql
EXEC get_customers_by_city @stadt = 'Düsseldorf';
```

### Erklärung

* `@stadt` ist ein Parameter.
* Beim Ausführen wird ein konkreter Wert übergeben.
* Die Procedure gibt nur Kunden aus dieser Stadt zurück.

---

## 6. Stored Procedure mit mehreren Parametern

```sql
CREATE PROCEDURE get_customers_by_city_and_status
    @stadt VARCHAR(100),
    @status VARCHAR(50)
AS
BEGIN
    SELECT *
    FROM kunden
    WHERE stadt = @stadt
    AND status = @status;
END;
```

Ausführen:

```sql
EXEC get_customers_by_city_and_status
    @stadt = 'Köln',
    @status = 'Aktiv';
```

---

## 7. Stored Procedure mit INSERT

Stored Procedures können auch Daten einfügen.

```sql
CREATE PROCEDURE insert_customer
    @name VARCHAR(100),
    @email VARCHAR(100),
    @stadt VARCHAR(100)
AS
BEGIN
    INSERT INTO kunden (name, email, stadt)
    VALUES (@name, @email, @stadt);
END;
```

Ausführen:

```sql
EXEC insert_customer
    @name = 'Ali',
    @email = 'ali@example.com',
    @stadt = 'Düsseldorf';
```

---

## 8. Stored Procedure mit UPDATE

```sql
CREATE PROCEDURE update_customer_city
    @kunden_id INT,
    @neue_stadt VARCHAR(100)
AS
BEGIN
    UPDATE kunden
    SET stadt = @neue_stadt
    WHERE kunden_id = @kunden_id;
END;
```

Ausführen:

```sql
EXEC update_customer_city
    @kunden_id = 1,
    @neue_stadt = 'Hamburg';
```

---

## 9. Stored Procedure mit DELETE

```sql
CREATE PROCEDURE delete_customer
    @kunden_id INT
AS
BEGIN
    DELETE FROM kunden
    WHERE kunden_id = @kunden_id;
END;
```

Ausführen:

```sql
EXEC delete_customer @kunden_id = 1;
```

### Wichtig

Bei `UPDATE` und `DELETE` sollte fast immer eine `WHERE`-Bedingung verwendet werden.

---

## 10. Output Parameter

Eine Stored Procedure kann auch Werte zurückgeben.

Dafür kann man `OUTPUT`-Parameter verwenden.

### Beispiel

```sql
CREATE PROCEDURE get_customer_count
    @anzahl INT OUTPUT
AS
BEGIN
    SELECT @anzahl = COUNT(*)
    FROM kunden;
END;
```

Ausführen:

```sql
DECLARE @result INT;

EXEC get_customer_count @anzahl = @result OUTPUT;

SELECT @result AS anzahl_kunden;
```

### Erklärung

* `@anzahl` ist ein Output-Parameter.
* Das Ergebnis wird in `@result` gespeichert.
* Danach kann der Wert mit `SELECT` angezeigt werden.

---

## 11. RETURN-Wert

Eine Stored Procedure kann auch einen `RETURN`-Wert zurückgeben.

Dieser wird meistens für Statuscodes verwendet.

```sql
CREATE PROCEDURE check_customer_exists
    @kunden_id INT
AS
BEGIN
    IF EXISTS (
        SELECT 1
        FROM kunden
        WHERE kunden_id = @kunden_id
    )
        RETURN 1;
    ELSE
        RETURN 0;
END;
```

Ausführen:

```sql
DECLARE @status INT;

EXEC @status = check_customer_exists @kunden_id = 1;

SELECT @status AS status;
```

### Merksatz

* `OUTPUT` wird für echte Ergebniswerte verwendet.
* `RETURN` wird oft für Statuscodes verwendet.

---

## 12. Variablen in Stored Procedures

In Stored Procedures kann man Variablen deklarieren.

```sql
CREATE PROCEDURE calculate_average_revenue
AS
BEGIN
    DECLARE @avg_umsatz DECIMAL(10,2);

    SELECT @avg_umsatz = AVG(umsatz)
    FROM kunden;

    SELECT @avg_umsatz AS durchschnittsumsatz;
END;
```

---

## 13. IF ELSE in Stored Procedures

```sql
CREATE PROCEDURE check_revenue
    @kunden_id INT
AS
BEGIN
    DECLARE @umsatz DECIMAL(10,2);

    SELECT @umsatz = umsatz
    FROM kunden
    WHERE kunden_id = @kunden_id;

    IF @umsatz >= 1000
        SELECT 'VIP Kunde' AS status;
    ELSE
        SELECT 'Normaler Kunde' AS status;
END;
```

---

## 14. Stored Procedure mit Transaction

Bei wichtigen Änderungen kann man eine Transaction verwenden.

```sql
CREATE PROCEDURE transfer_money
    @from_account INT,
    @to_account INT,
    @amount DECIMAL(10,2)
AS
BEGIN
    BEGIN TRANSACTION;

    UPDATE konten
    SET kontostand = kontostand - @amount
    WHERE konto_id = @from_account;

    UPDATE konten
    SET kontostand = kontostand + @amount
    WHERE konto_id = @to_account;

    COMMIT TRANSACTION;
END;
```

### Erklärung

Beide Updates gehören zusammen.

Wenn ein Fehler passiert, sollte man normalerweise mit `ROLLBACK` zurückrollen.

---

## 15. Stored Procedure mit TRY CATCH

In SQL Server kann man Fehler mit `TRY CATCH` behandeln.

```sql
CREATE PROCEDURE transfer_money_safe
    @from_account INT,
    @to_account INT,
    @amount DECIMAL(10,2)
AS
BEGIN
    BEGIN TRY
        BEGIN TRANSACTION;

        UPDATE konten
        SET kontostand = kontostand - @amount
        WHERE konto_id = @from_account;

        UPDATE konten
        SET kontostand = kontostand + @amount
        WHERE konto_id = @to_account;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;

        SELECT ERROR_MESSAGE() AS fehler;
    END CATCH;
END;
```

---

## 16. ALTER PROCEDURE

Mit `ALTER PROCEDURE` kann man eine bestehende Stored Procedure ändern.

```sql
ALTER PROCEDURE get_customers_by_city
    @stadt VARCHAR(100)
AS
BEGIN
    SELECT kunden_id, name, email, stadt
    FROM kunden
    WHERE stadt = @stadt;
END;
```

---

## 17. DROP PROCEDURE

Mit `DROP PROCEDURE` löscht man eine Stored Procedure.

```sql
DROP PROCEDURE get_customers_by_city;
```

Mit Sicherheitsprüfung:

```sql
DROP PROCEDURE IF EXISTS get_customers_by_city;
```

---

## 18. Stored Procedure vs View vs Function

| Objekt             | Zweck                                            |
| ------------------ | ------------------------------------------------ |
| `View`             | gespeicherte SELECT-Abfrage                      |
| `Function`         | gibt einen Wert oder eine Tabelle zurück         |
| `Stored Procedure` | führt einen Ablauf mit mehreren SQL-Befehlen aus |

### Wann Stored Procedure?

Verwende eine Stored Procedure, wenn:

* mehrere SQL-Schritte ausgeführt werden sollen
* Daten geändert werden sollen
* Parameter gebraucht werden
* Transactions oder Fehlerbehandlung nötig sind
* ein Prozess automatisiert werden soll

---

## 19. Vorteile von Stored Procedures

| Vorteil              | Erklärung                                                          |
| -------------------- | ------------------------------------------------------------------ |
| Wiederverwendbarkeit | SQL-Code muss nicht jedes Mal neu geschrieben werden               |
| Struktur             | komplexe Abläufe werden gekapselt                                  |
| Sicherheit           | Benutzer können Procedure ausführen, ohne direkten Tabellenzugriff |
| Wartbarkeit          | Änderungen werden zentral in der Procedure gemacht                 |
| Parameter            | flexible Eingaben sind möglich                                     |

---

## 20. Nachteile von Stored Procedures

| Nachteil      | Erklärung                                             |
| ------------- | ----------------------------------------------------- |
| Komplexität   | Logik in der Datenbank kann schwerer zu debuggen sein |
| Abhängigkeit  | Syntax hängt stark vom Datenbanksystem ab             |
| Wartung       | viele Procedures können unübersichtlich werden        |
| Versionierung | Änderungen müssen sauber dokumentiert werden          |

---

## 21. Häufige Fehler

### 21.1 WHERE bei UPDATE oder DELETE vergessen

Problematisch:

```sql
UPDATE kunden
SET status = 'Inaktiv';
```

Besser:

```sql
UPDATE kunden
SET status = 'Inaktiv'
WHERE kunden_id = @kunden_id;
```

---

### 21.2 Parameter ohne Datentyp

Falsch:

```sql
CREATE PROCEDURE get_customer
    @kunden_id
AS
BEGIN
    SELECT *
    FROM kunden
    WHERE kunden_id = @kunden_id;
END;
```

Richtig:

```sql
CREATE PROCEDURE get_customer
    @kunden_id INT
AS
BEGIN
    SELECT *
    FROM kunden
    WHERE kunden_id = @kunden_id;
END;
```

---

### 21.3 OUTPUT vergessen

Problematisch:

```sql
EXEC get_customer_count @anzahl = @result;
```

Richtig:

```sql
EXEC get_customer_count @anzahl = @result OUTPUT;
```

---

## 22. Praktisches Komplettbeispiel

```sql
CREATE PROCEDURE create_customer_order
    @kunden_id INT,
    @produkt_id INT,
    @menge INT
AS
BEGIN
    BEGIN TRY
        BEGIN TRANSACTION;

        INSERT INTO bestellungen (kunden_id, bestelldatum)
        VALUES (@kunden_id, GETDATE());

        DECLARE @bestellung_id INT;

        SET @bestellung_id = SCOPE_IDENTITY();

        INSERT INTO bestellpositionen (bestellung_id, produkt_id, menge)
        VALUES (@bestellung_id, @produkt_id, @menge);

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;

        SELECT ERROR_MESSAGE() AS fehler;
    END CATCH;
END;
```

Ausführen:

```sql
EXEC create_customer_order
    @kunden_id = 1,
    @produkt_id = 10,
    @menge = 2;
```

### Erklärung

Diese Procedure:

* erstellt eine neue Bestellung,
* speichert die neue `bestellung_id`,
* fügt eine Bestellposition hinzu,
* verwendet eine Transaction,
* führt bei Fehlern ein `ROLLBACK` aus.

---

## 23. Mini-Übersicht

| Befehl              | Bedeutung                        |
| ------------------- | -------------------------------- |
| `CREATE PROCEDURE`  | erstellt eine Stored Procedure   |
| `ALTER PROCEDURE`   | ändert eine Stored Procedure     |
| `DROP PROCEDURE`    | löscht eine Stored Procedure     |
| `EXEC` / `EXECUTE`  | führt eine Stored Procedure aus  |
| `OUTPUT`            | gibt Werte über Parameter zurück |
| `RETURN`            | gibt einen Statuswert zurück     |
| `BEGIN TRANSACTION` | startet eine Transaction         |
| `COMMIT`            | bestätigt Änderungen             |
| `ROLLBACK`          | macht Änderungen rückgängig      |

---

## 24. Merksätze

* Eine Stored Procedure ist gespeicherter SQL-Code.
* Stored Procedures können Parameter haben.
* Sie können `SELECT`, `INSERT`, `UPDATE` und `DELETE` enthalten.
* Sie sind gut für wiederkehrende und komplexe Abläufe.
* Mit `EXEC` führt man eine Stored Procedure aus.
* Mit `OUTPUT` können Werte zurückgegeben werden.
* Mit `TRY CATCH` kann man Fehler behandeln.
* Mit Transactions kann man mehrere Änderungen sicher zusammen ausführen.
* Stored Procedures sind besonders nützlich für Prozesse, die regelmäßig ausgeführt werden.




# SQL Zusammenfassung – Teil 22: Triggers

## 1. Was ist ein Trigger?

Ein `Trigger` ist ein SQL-Codeblock, der automatisch ausgeführt wird, wenn ein bestimmtes Ereignis in der Datenbank passiert.

Typische Ereignisse sind:

* `INSERT`
* `UPDATE`
* `DELETE`

Man kann sich einen Trigger wie eine automatische Reaktion der Datenbank vorstellen.

Beispiel:

```text
Wenn ein Kunde gelöscht wird,
dann speichere automatisch einen Eintrag in einer Log-Tabelle.
```

---

## 2. Warum verwendet man Trigger?

Triggers werden verwendet für:

* automatische Protokollierung
* Audit-Logs
* automatische Prüfungen
* automatische Aktualisierung anderer Tabellen
* Schutz vor unerlaubten Änderungen
* Historisierung von Daten

---

## 3. Arten von Triggern

| Trigger-Art          | Bedeutung                                                        |
| -------------------- | ---------------------------------------------------------------- |
| `AFTER Trigger`      | wird nach `INSERT`, `UPDATE` oder `DELETE` ausgeführt            |
| `INSTEAD OF Trigger` | ersetzt die eigentliche Aktion                                   |
| `DDL Trigger`        | reagiert auf Strukturänderungen, z. B. `CREATE`, `ALTER`, `DROP` |

In der Praxis sind besonders `AFTER` und `INSTEAD OF` wichtig.

---

# 4. AFTER Trigger

Ein `AFTER Trigger` wird ausgeführt, nachdem eine Aktion erfolgreich passiert ist.

Beispiel:

* Zeile wird eingefügt
* danach startet der Trigger

---

## 4.1 AFTER INSERT Trigger

```sql
CREATE TRIGGER trg_after_insert_kunden
ON kunden
AFTER INSERT
AS
BEGIN
    INSERT INTO kunden_log (kunden_id, aktion, datum)
    SELECT
        kunden_id,
        'INSERT',
        GETDATE()
    FROM inserted;
END;
```

### Erklärung

Dieser Trigger wird ausgeführt, wenn neue Kunden eingefügt werden.

Die Tabelle `inserted` enthält die neu eingefügten Zeilen.

---

## 4.2 AFTER UPDATE Trigger

```sql
CREATE TRIGGER trg_after_update_kunden
ON kunden
AFTER UPDATE
AS
BEGIN
    INSERT INTO kunden_log (kunden_id, aktion, datum)
    SELECT
        kunden_id,
        'UPDATE',
        GETDATE()
    FROM inserted;
END;
```

### Erklärung

Dieser Trigger wird ausgeführt, wenn Kundendaten geändert werden.

Die Tabelle `inserted` enthält die neuen Werte nach dem Update.

---

## 4.3 AFTER DELETE Trigger

```sql
CREATE TRIGGER trg_after_delete_kunden
ON kunden
AFTER DELETE
AS
BEGIN
    INSERT INTO kunden_log (kunden_id, aktion, datum)
    SELECT
        kunden_id,
        'DELETE',
        GETDATE()
    FROM deleted;
END;
```

### Erklärung

Dieser Trigger wird ausgeführt, wenn Kunden gelöscht werden.

Die Tabelle `deleted` enthält die gelöschten Zeilen.

---

# 5. inserted und deleted Tabellen

In SQL Server gibt es bei Triggern zwei wichtige temporäre Tabellen:

| Tabelle    | Bedeutung          |
| ---------- | ------------------ |
| `inserted` | enthält neue Werte |
| `deleted`  | enthält alte Werte |

---

## 5.1 Verhalten bei INSERT

| Aktion   | inserted    | deleted |
| -------- | ----------- | ------- |
| `INSERT` | neue Zeilen | leer    |

---

## 5.2 Verhalten bei DELETE

| Aktion   | inserted | deleted          |
| -------- | -------- | ---------------- |
| `DELETE` | leer     | gelöschte Zeilen |

---

## 5.3 Verhalten bei UPDATE

| Aktion   | inserted   | deleted    |
| -------- | ---------- | ---------- |
| `UPDATE` | neue Werte | alte Werte |

Bei einem `UPDATE` passiert intern vereinfacht:

```text
Alte Version wird gelöscht
Neue Version wird eingefügt
```

Deshalb gibt es bei `UPDATE` beide Tabellen.

---

# 6. Beispiel: Alte und neue Werte speichern

```sql
CREATE TRIGGER trg_update_email_log
ON kunden
AFTER UPDATE
AS
BEGIN
    INSERT INTO kunden_email_log (
        kunden_id,
        alte_email,
        neue_email,
        geaendert_am
    )
    SELECT
        d.kunden_id,
        d.email AS alte_email,
        i.email AS neue_email,
        GETDATE()
    FROM deleted d
    INNER JOIN inserted i
    ON d.kunden_id = i.kunden_id
    WHERE d.email <> i.email;
END;
```

### Erklärung

Dieser Trigger speichert Änderungen an der E-Mail-Adresse.

| Tabelle    | Bedeutung  |
| ---------- | ---------- |
| `deleted`  | alte Werte |
| `inserted` | neue Werte |

---

# 7. INSTEAD OF Trigger

Ein `INSTEAD OF Trigger` wird anstelle der eigentlichen Aktion ausgeführt.

Das bedeutet:

* Die ursprüngliche Aktion wird nicht direkt ausgeführt.
* Der Trigger entscheidet, was passiert.

---

## 7.1 Beispiel: DELETE verhindern

```sql
CREATE TRIGGER trg_prevent_delete_kunden
ON kunden
INSTEAD OF DELETE
AS
BEGIN
    PRINT 'Kunden dürfen nicht gelöscht werden.';
END;
```

### Erklärung

Wenn jemand versucht, einen Kunden zu löschen, wird der Löschvorgang verhindert.

---

## 7.2 Beispiel: Soft Delete

Statt eine Zeile wirklich zu löschen, kann man sie als gelöscht markieren.

```sql
CREATE TRIGGER trg_soft_delete_kunden
ON kunden
INSTEAD OF DELETE
AS
BEGIN
    UPDATE kunden
    SET geloescht = 1
    WHERE kunden_id IN (
        SELECT kunden_id
        FROM deleted
    );
END;
```

### Erklärung

Der Kunde wird nicht wirklich gelöscht.

Stattdessen wird die Spalte `geloescht` auf `1` gesetzt.

---

# 8. Trigger für mehrere Aktionen

Ein Trigger kann auch auf mehrere Aktionen reagieren.

```sql
CREATE TRIGGER trg_kunden_changes
ON kunden
AFTER INSERT, UPDATE, DELETE
AS
BEGIN
    INSERT INTO audit_log (tabelle, aktion, datum)
    VALUES ('kunden', 'Änderung', GETDATE());
END;
```

### Hinweis

Wenn man genau unterscheiden möchte, ob es `INSERT`, `UPDATE` oder `DELETE` war, muss man `inserted` und `deleted` prüfen.

---

# 9. Aktion im Trigger erkennen

```sql
CREATE TRIGGER trg_kunden_audit
ON kunden
AFTER INSERT, UPDATE, DELETE
AS
BEGIN
    IF EXISTS (SELECT 1 FROM inserted)
       AND EXISTS (SELECT 1 FROM deleted)
    BEGIN
        INSERT INTO audit_log (aktion, datum)
        VALUES ('UPDATE', GETDATE());
    END
    ELSE IF EXISTS (SELECT 1 FROM inserted)
    BEGIN
        INSERT INTO audit_log (aktion, datum)
        VALUES ('INSERT', GETDATE());
    END
    ELSE IF EXISTS (SELECT 1 FROM deleted)
    BEGIN
        INSERT INTO audit_log (aktion, datum)
        VALUES ('DELETE', GETDATE());
    END
END;
```

---

# 10. DDL Trigger

Ein `DDL Trigger` reagiert auf Änderungen an der Datenbankstruktur.

Beispiele:

* `CREATE TABLE`
* `ALTER TABLE`
* `DROP TABLE`
* `CREATE PROCEDURE`
* `DROP VIEW`

### Beispiel

```sql
CREATE TRIGGER trg_prevent_drop_table
ON DATABASE
FOR DROP_TABLE
AS
BEGIN
    PRINT 'Tabellen dürfen nicht gelöscht werden.';
    ROLLBACK;
END;
```

### Erklärung

Dieser Trigger verhindert das Löschen von Tabellen.

---

# 11. Trigger ändern

Mit `ALTER TRIGGER` kann man einen bestehenden Trigger ändern.

```sql
ALTER TRIGGER trg_after_insert_kunden
ON kunden
AFTER INSERT
AS
BEGIN
    INSERT INTO kunden_log (kunden_id, aktion, datum)
    SELECT
        kunden_id,
        'NEUER KUNDE',
        GETDATE()
    FROM inserted;
END;
```

---

# 12. Trigger löschen

```sql
DROP TRIGGER trg_after_insert_kunden;
```

---

# 13. Trigger deaktivieren und aktivieren

In SQL Server kann man Trigger deaktivieren.

## Trigger deaktivieren

```sql
DISABLE TRIGGER trg_after_insert_kunden
ON kunden;
```

## Trigger aktivieren

```sql
ENABLE TRIGGER trg_after_insert_kunden
ON kunden;
```

---

# 14. Trigger vs Stored Procedure

| Stored Procedure                   | Trigger                              |
| ---------------------------------- | ------------------------------------ |
| wird manuell mit `EXEC` ausgeführt | wird automatisch ausgeführt          |
| braucht direkten Aufruf            | reagiert auf Ereignisse              |
| gut für Prozesse                   | gut für automatische Reaktionen      |
| Parameter möglich                  | keine normalen Eingabeparameter      |
| kontrollierter Ablauf              | kann versteckt im Hintergrund laufen |

---

# 15. Wann verwende ich Trigger?

Verwende Trigger, wenn:

* automatisch auf Datenänderungen reagiert werden soll
* Änderungen protokolliert werden müssen
* alte Werte gespeichert werden sollen
* Daten historisiert werden sollen
* eine Aktion verhindert oder ersetzt werden soll
* Audit-Logs gebraucht werden

---

# 16. Wann sollte man vorsichtig sein?

Triggers können problematisch sein, wenn sie zu viel Logik enthalten.

Nachteile:

* sie laufen automatisch im Hintergrund
* sie können Performance verschlechtern
* sie sind schwerer zu debuggen
* sie können unerwartete Seiteneffekte haben
* sie können andere Trigger auslösen
* sie machen Datenänderungen weniger transparent

### Merksatz

Trigger sollten kurz, klar und kontrollierbar bleiben.

---

# 17. Häufige Fehler

## 17.1 Trigger nur für eine Zeile schreiben

Falsch gedacht:

```text
Ein Trigger läuft nur für eine Zeile.
```

Richtig:

Ein Trigger läuft pro Statement, nicht pro Zeile.

Wenn ein `UPDATE` 100 Zeilen ändert, enthält `inserted` oder `deleted` 100 Zeilen.

Deshalb sollte man Trigger immer mengenbasiert schreiben.

---

## 17.2 Einzelne Variable statt inserted verwenden

Problematisch:

```sql
DECLARE @kunden_id INT;

SELECT @kunden_id = kunden_id
FROM inserted;
```

Wenn mehrere Zeilen eingefügt wurden, kann das zu falschen Ergebnissen führen.

Besser:

```sql
INSERT INTO kunden_log (kunden_id, aktion, datum)
SELECT
    kunden_id,
    'INSERT',
    GETDATE()
FROM inserted;
```

---

## 17.3 Zu komplexe Business-Logik im Trigger

Trigger sollten nicht zu viele komplexe Prozesse enthalten.

Besser ist oft:

* Stored Procedure
* Application Logic
* klare Constraints
* Foreign Keys
* Check Constraints

---

# 18. Praktisches Komplettbeispiel: Audit Trigger

```sql
CREATE TABLE kunden_audit (
    audit_id INT IDENTITY PRIMARY KEY,
    kunden_id INT,
    alte_email VARCHAR(100),
    neue_email VARCHAR(100),
    aktion VARCHAR(20),
    geaendert_am DATETIME
);
```

```sql
CREATE TRIGGER trg_kunden_audit_email
ON kunden
AFTER UPDATE
AS
BEGIN
    INSERT INTO kunden_audit (
        kunden_id,
        alte_email,
        neue_email,
        aktion,
        geaendert_am
    )
    SELECT
        d.kunden_id,
        d.email,
        i.email,
        'UPDATE',
        GETDATE()
    FROM deleted d
    INNER JOIN inserted i
    ON d.kunden_id = i.kunden_id
    WHERE d.email <> i.email;
END;
```

### Erklärung

Dieser Trigger:

* reagiert auf Änderungen in `kunden`,
* vergleicht alte und neue E-Mail-Adresse,
* speichert Änderungen in `kunden_audit`,
* verwendet `deleted` für alte Werte,
* verwendet `inserted` für neue Werte.

---

# 19. Mini-Übersicht

| Begriff              | Bedeutung                         |
| -------------------- | --------------------------------- |
| `Trigger`            | automatisch ausgeführter SQL-Code |
| `AFTER Trigger`      | läuft nach einer Aktion           |
| `INSTEAD OF Trigger` | ersetzt eine Aktion               |
| `DDL Trigger`        | reagiert auf Strukturänderungen   |
| `inserted`           | enthält neue Werte                |
| `deleted`            | enthält alte Werte                |
| `ALTER TRIGGER`      | ändert einen Trigger              |
| `DROP TRIGGER`       | löscht einen Trigger              |
| `DISABLE TRIGGER`    | deaktiviert einen Trigger         |
| `ENABLE TRIGGER`     | aktiviert einen Trigger           |

---

# 20. Merksätze

* Trigger laufen automatisch bei bestimmten Ereignissen.
* Häufige Trigger-Ereignisse sind `INSERT`, `UPDATE` und `DELETE`.
* `AFTER Trigger` laufen nach der Aktion.
* `INSTEAD OF Trigger` ersetzen die eigentliche Aktion.
* `inserted` enthält neue Werte.
* `deleted` enthält alte Werte.
* Bei `UPDATE` gibt es alte und neue Werte.
* Trigger laufen pro SQL-Statement, nicht pro einzelner Zeile.
* Trigger sind nützlich für Logging, Auditing und automatische Prüfungen.
* Trigger sollten nicht zu komplex werden.


# SQL Zusammenfassung – Teil 23: TCL und DCL

## 1. Überblick

Neben `DDL`, `DML` und `DQL` gibt es auch:

| Kategorie | Bedeutung                    | Befehle                           |
| --------- | ---------------------------- | --------------------------------- |
| `TCL`     | Transaction Control Language | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |
| `DCL`     | Data Control Language        | `GRANT`, `REVOKE`                 |

---

# 2. TCL – Transaction Control Language

`TCL` wird verwendet, um Transaktionen zu steuern.

Eine Transaktion ist eine Gruppe von SQL-Befehlen, die zusammengehören.

Entweder werden alle Änderungen gespeichert oder keine.

Typische TCL-Befehle:

* `COMMIT`
* `ROLLBACK`
* `SAVEPOINT`

---

## 3. Was ist eine Transaction?

Eine `Transaction` ist eine logische Einheit von mehreren SQL-Befehlen.

Beispiel: Geldüberweisung

```text
1. Geld von Konto A abziehen
2. Geld auf Konto B hinzufügen
```

Beide Schritte müssen zusammen funktionieren.

Wenn Schritt 1 funktioniert, aber Schritt 2 fehlschlägt, darf die Änderung nicht gespeichert werden.

Deshalb verwendet man eine Transaction.

---

## 4. COMMIT

`COMMIT` speichert alle Änderungen einer Transaktion dauerhaft.

### Allgemeine Struktur

```sql
BEGIN TRANSACTION;

-- SQL-Befehle

COMMIT;
```

### Beispiel

```sql
BEGIN TRANSACTION;

UPDATE konten
SET kontostand = kontostand - 100
WHERE konto_id = 1;

UPDATE konten
SET kontostand = kontostand + 100
WHERE konto_id = 2;

COMMIT;
```

### Erklärung

Dieser Code:

* zieht `100` von Konto 1 ab
* fügt `100` zu Konto 2 hinzu
* speichert beide Änderungen dauerhaft mit `COMMIT`

---

## 5. ROLLBACK

`ROLLBACK` macht Änderungen einer Transaktion rückgängig.

### Allgemeine Struktur

```sql
BEGIN TRANSACTION;

-- SQL-Befehle

ROLLBACK;
```

### Beispiel

```sql
BEGIN TRANSACTION;

UPDATE konten
SET kontostand = kontostand - 100
WHERE konto_id = 1;

UPDATE konten
SET kontostand = kontostand + 100
WHERE konto_id = 2;

ROLLBACK;
```

### Erklärung

Die Änderungen werden nicht gespeichert.

Die Datenbank geht zurück zum Zustand vor der Transaktion.

---

## 6. COMMIT vs ROLLBACK

| Befehl     | Bedeutung                      |
| ---------- | ------------------------------ |
| `COMMIT`   | speichert Änderungen dauerhaft |
| `ROLLBACK` | macht Änderungen rückgängig    |

---

## 7. Typischer Use Case für COMMIT und ROLLBACK

### Beispiel mit Fehlerbehandlung

```sql
BEGIN TRY
    BEGIN TRANSACTION;

    UPDATE konten
    SET kontostand = kontostand - 100
    WHERE konto_id = 1;

    UPDATE konten
    SET kontostand = kontostand + 100
    WHERE konto_id = 2;

    COMMIT;
END TRY
BEGIN CATCH
    ROLLBACK;

    SELECT ERROR_MESSAGE() AS fehler;
END CATCH;
```

### Erklärung

Wenn kein Fehler passiert:

```sql
COMMIT;
```

Wenn ein Fehler passiert:

```sql
ROLLBACK;
```

---

## 8. SAVEPOINT

Mit `SAVEPOINT` kann man innerhalb einer Transaktion einen Zwischenspeicherpunkt setzen.

Dann kann man später nur bis zu diesem Punkt zurückgehen.

### Allgemeine Struktur

```sql
BEGIN TRANSACTION;

-- Schritt 1

SAVEPOINT savepoint_name;

-- Schritt 2

ROLLBACK TO SAVEPOINT savepoint_name;

COMMIT;
```

### Hinweis zu SQL Server

In SQL Server heißt es meistens:

```sql
SAVE TRANSACTION savepoint_name;
```

Und zurückrollen:

```sql
ROLLBACK TRANSACTION savepoint_name;
```

---

## 9. SAVEPOINT Beispiel

```sql
BEGIN TRANSACTION;

UPDATE produkte
SET preis = preis * 1.10
WHERE kategorie = 'Laptop';

SAVE TRANSACTION nach_laptops;

UPDATE produkte
SET preis = preis * 1.50
WHERE kategorie = 'Maus';

ROLLBACK TRANSACTION nach_laptops;

COMMIT;
```

### Erklärung

Dieser Code:

* erhöht zuerst Laptop-Preise um 10 %
* setzt danach einen Savepoint
* erhöht Maus-Preise um 50 %
* macht nur die Änderung nach dem Savepoint rückgängig
* speichert die Laptop-Änderung mit `COMMIT`

---

## 10. Autocommit

Viele Datenbanksysteme arbeiten standardmäßig mit `Autocommit`.

Das bedeutet:

Jeder einzelne SQL-Befehl wird automatisch gespeichert.

Beispiel:

```sql
UPDATE kunden
SET stadt = 'Köln'
WHERE kunden_id = 1;
```

Wenn `Autocommit` aktiv ist, wird dieser Befehl automatisch gespeichert.

Wenn man mehrere Befehle zusammen kontrollieren möchte, verwendet man eine explizite Transaktion:

```sql
BEGIN TRANSACTION;

-- mehrere SQL-Befehle

COMMIT;
```

---

## 11. Wann verwendet man TCL?

TCL verwendet man, wenn mehrere Änderungen zusammengehören.

Typische Use Cases:

| Use Case             | Warum Transaction?                                                 |
| -------------------- | ------------------------------------------------------------------ |
| Geldüberweisung      | Abbuchung und Einzahlung müssen zusammen passieren                 |
| Bestellung erstellen | Bestellung und Bestellpositionen gehören zusammen                  |
| Batch-Update         | Viele Änderungen sollen komplett oder gar nicht gespeichert werden |
| Datenimport          | Bei Fehler soll der Import zurückgerollt werden                    |
| Kritische Änderungen | Fehler dürfen keine halbfertigen Daten erzeugen                    |

---

# 12. DCL – Data Control Language

`DCL` wird verwendet, um Rechte und Berechtigungen in der Datenbank zu steuern.

Wichtige DCL-Befehle:

* `GRANT`
* `REVOKE`

Zusätzlich gibt es in manchen Systemen, zum Beispiel SQL Server:

* `DENY`

---

## 13. GRANT

`GRANT` gibt einem Benutzer oder einer Rolle bestimmte Rechte.

### Allgemeine Struktur

```sql
GRANT rechte
ON objekt
TO benutzer_oder_rolle;
```

---

## 14. GRANT Beispiel: SELECT-Recht

```sql
GRANT SELECT
ON kunden
TO analyst_user;
```

### Erklärung

Der Benutzer `analyst_user` darf Daten aus der Tabelle `kunden` lesen.

---

## 15. GRANT mehrere Rechte

```sql
GRANT SELECT, INSERT, UPDATE
ON kunden
TO app_user;
```

### Erklärung

Der Benutzer `app_user` darf:

* Daten lesen
* Daten einfügen
* Daten ändern

---

## 16. GRANT auf Stored Procedure

```sql
GRANT EXECUTE
ON OBJECT::get_customers_by_city
TO app_user;
```

### Erklärung

Der Benutzer `app_user` darf die Stored Procedure `get_customers_by_city` ausführen.

Das ist nützlich, wenn Benutzer eine Procedure ausführen dürfen, aber keinen direkten Zugriff auf die Tabellen bekommen sollen.

---

## 17. Häufige Rechte

| Recht     | Bedeutung                                |
| --------- | ---------------------------------------- |
| `SELECT`  | Daten lesen                              |
| `INSERT`  | Daten einfügen                           |
| `UPDATE`  | Daten ändern                             |
| `DELETE`  | Daten löschen                            |
| `EXECUTE` | Stored Procedure oder Function ausführen |
| `ALTER`   | Objekt ändern                            |
| `CONTROL` | umfassende Kontrolle über ein Objekt     |

---

## 18. REVOKE

`REVOKE` entfernt zuvor vergebene Rechte.

### Allgemeine Struktur

```sql
REVOKE rechte
ON objekt
FROM benutzer_oder_rolle;
```

---

## 19. REVOKE Beispiel

```sql
REVOKE UPDATE
ON kunden
FROM app_user;
```

### Erklärung

Der Benutzer `app_user` verliert das Recht, die Tabelle `kunden` zu ändern.

---

## 20. REVOKE mehrere Rechte

```sql
REVOKE SELECT, INSERT
ON kunden
FROM app_user;
```

### Erklärung

Dem Benutzer werden `SELECT` und `INSERT` auf der Tabelle `kunden` entzogen.

---

## 21. GRANT vs REVOKE

| Befehl   | Bedeutung       |
| -------- | --------------- |
| `GRANT`  | gibt Rechte     |
| `REVOKE` | entfernt Rechte |

---

## 22. DENY

`DENY` gibt es besonders in SQL Server.

`DENY` verbietet ein Recht ausdrücklich.

```sql
DENY SELECT
ON kunden
TO test_user;
```

### Unterschied zwischen REVOKE und DENY

| Befehl   | Bedeutung                                |
| -------- | ---------------------------------------- |
| `REVOKE` | entfernt eine Berechtigung               |
| `DENY`   | verbietet eine Berechtigung ausdrücklich |

### Wichtig

`DENY` ist stärker als `GRANT`.

Wenn ein Benutzer über eine Rolle ein Recht bekommt, aber direkt ein `DENY` hat, gewinnt meistens `DENY`.

---

# 23. Rollen

In der Praxis gibt man Rechte oft nicht direkt an einzelne Benutzer, sondern an Rollen.

### Beispiel

```sql
CREATE ROLE reporting_role;
```

Recht an Rolle geben:

```sql
GRANT SELECT
ON kunden_report
TO reporting_role;
```

Benutzer zur Rolle hinzufügen:

```sql
ALTER ROLE reporting_role
ADD MEMBER analyst_user;
```

### Vorteil

Man muss Rechte nicht für jeden Benutzer einzeln verwalten.

---

# 24. Typische DCL Use Cases

| Use Case                          | Beispiel                                |
| --------------------------------- | --------------------------------------- |
| Analyst darf nur lesen            | `GRANT SELECT`                          |
| App darf Daten schreiben          | `GRANT INSERT, UPDATE`                  |
| Benutzer darf Procedure ausführen | `GRANT EXECUTE`                         |
| Zugriff wieder entfernen          | `REVOKE SELECT`                         |
| Sensible Tabelle schützen         | Rechte nur auf View geben               |
| Rollenbasierte Rechte             | Rechte an Rolle statt Benutzer vergeben |

---

## 25. Beispiel: Zugriff über View statt Tabelle

Manchmal soll ein Benutzer nicht alle Spalten einer Tabelle sehen.

Tabelle `kunden` enthält:

| kunden_id | name | email | telefon | gehalt |
| --------- | ---- | ----- | ------- | -----: |

Der Benutzer soll `gehalt` nicht sehen.

### View erstellen

```sql
CREATE VIEW kunden_public AS
SELECT
    kunden_id,
    name,
    email,
    telefon
FROM kunden;
```

### Recht auf View geben

```sql
GRANT SELECT
ON kunden_public
TO analyst_user;
```

### Optional: direkten Tabellenzugriff entziehen

```sql
REVOKE SELECT
ON kunden
FROM analyst_user;
```

### Erklärung

Der Benutzer darf nur die View sehen, aber nicht direkt die Tabelle mit der sensiblen Spalte.

---

# 26. TCL vs DCL

| Kategorie | Zweck                 | Beispiele                         |
| --------- | --------------------- | --------------------------------- |
| `TCL`     | steuert Transaktionen | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |
| `DCL`     | steuert Rechte        | `GRANT`, `REVOKE`                 |

---

# 27. Praktisches Komplettbeispiel

## 27.1 TCL Beispiel

```sql
BEGIN TRY
    BEGIN TRANSACTION;

    INSERT INTO bestellungen (kunden_id, bestelldatum)
    VALUES (1, GETDATE());

    UPDATE kunden
    SET letzte_bestellung = GETDATE()
    WHERE kunden_id = 1;

    COMMIT;
END TRY
BEGIN CATCH
    ROLLBACK;

    SELECT ERROR_MESSAGE() AS fehler;
END CATCH;
```

### Erklärung

Dieser Code:

* erstellt eine Bestellung
* aktualisiert den Kunden
* speichert beide Änderungen nur, wenn alles funktioniert
* macht bei Fehlern alles rückgängig

---

## 27.2 DCL Beispiel

```sql
GRANT SELECT
ON kunden_report
TO reporting_role;

GRANT EXECUTE
ON OBJECT::get_customer_report
TO reporting_role;

REVOKE UPDATE
ON kunden
FROM reporting_role;
```

### Erklärung

Dieser Code:

* erlaubt Lesen auf `kunden_report`
* erlaubt Ausführen der Procedure `get_customer_report`
* entfernt Änderungsrechte auf `kunden`

---

# 28. Mini-Übersicht

| Befehl              | Kategorie        | Bedeutung                      |
| ------------------- | ---------------- | ------------------------------ |
| `BEGIN TRANSACTION` | TCL              | startet eine Transaktion       |
| `COMMIT`            | TCL              | speichert Änderungen dauerhaft |
| `ROLLBACK`          | TCL              | macht Änderungen rückgängig    |
| `SAVEPOINT`         | TCL              | setzt einen Zwischenpunkt      |
| `GRANT`             | DCL              | gibt Rechte                    |
| `REVOKE`            | DCL              | entfernt Rechte                |
| `DENY`              | DCL / SQL Server | verbietet Rechte ausdrücklich  |

---

# 29. Merksätze

* `TCL` steuert Transaktionen.
* Eine Transaktion fasst mehrere SQL-Befehle logisch zusammen.
* `COMMIT` speichert Änderungen dauerhaft.
* `ROLLBACK` macht Änderungen rückgängig.
* `SAVEPOINT` erlaubt teilweises Zurückrollen.
* `DCL` steuert Benutzerrechte.
* `GRANT` gibt Rechte.
* `REVOKE` entfernt Rechte.
* Rechte werden oft über Rollen verwaltet.
* Für sensible Daten kann man Views verwenden und nur auf diese Views Rechte geben.




