---
author: Lektion 6
date: MMMM dd, YYYY
paging: "%d / %d"
---

# Lektion 6

Hej och välkommen!

## Agenda

1. Frågor och repetition
2. Repetition och mer om joins
3. Introduktion till normalisering
4. Genomgång av övningar (6-9)
5. Övning med handledning
6. Projektbygge
7. Quiz frågor

---

# Typer av joins

- `INNER JOIN` - Hämtar alla rader med matchande värden
- `LEFT JOIN` - Hämtar alla rader från vänster, och de rader från höger som matchar
- `RIGHT JOIN` - Hämtar alla rader från höger, och de rader från vänster som matchar
- `FULL OUTER JOIN` - Hämtar alla rader och matchar de som går

---

# Joins exempel (One-to-Many)

```sql
CREATE TABLE users (id SERIAL PRIMARY KEY, name TEXT, password TEXT);
CREATE TABLE comments (id SERIAL PRIMARY KEY, content TEXT, user_id INT REFERENCES users(id));

-- Hämta all kommentarer från ett användarnamn
SELECT content from comments 
INNER JOIN users 
  ON users.id = comments.user_id 
WHERE users.name = 'Ironman';
```

---

# Joins och Many-to-Many

Many-to-Many kräver ibland två joins.

```sql
CREATE TABLE users (id SERIAL PRIMARY KEY, name TEXT);
CREATE TABLE repos (id SERIAL PRIMARY KEY, name TEXT, public BOOLEAN);
CREATE TABLE user_repos(repo_id INT REFERENCES repos(id), user_id INT REFERENCES users(id));

-- Hämta all repos från ett användarnamn
SELECT repos.name FROM repos 
INNER JOIN user_repos 
  ON repos.id = user_repos.repo_id 
INNER JOIN users 
  ON users.id = user_repos.user_id 
WHERE users.name = 'Ironman';
```

---

# Introduktion till normalisering

En samling med sätt, så kallade normalformer, att designa och strukturera tabeller för att uppnå integritet och förhinda data duplicering.

Normalformer: 1NF, 2NF & 3NF. Varje form har specifika regler att följa.

---

# Ett exempel utan normalisering

```sql
CREATE TABLE book_orders (
  isbn TEXT,
  title TEXT,
  author TEXT,
  author_email TEXT,
  customer_id INT,
  customer_name TEXT,
  date DATE,
  price DECIMAL
);
```

Data upprepas med den tabellen. Som exempel upprepas `author` för varje order med samma bok.

---

# Normalformer

## Första formen (1NF)

Varje rad och kolumn (cell) måste ha endast ett värde. En cell får inte innehålla en lista. "Listor" av saker hanteras genom relationer.

Följer inte 1NF:

| person_id | emails       |
| --------- | ------------ |
| 1         | mail1, mail2 |

## Andra formen (2NF)

Tabell måste ha endast en primär nyckel, eller, tabell måste kolumner som alla är helt beroende av alla primära nycklar.

Följer inte 2NF: `student_id | student_name | course_id | course_name | course_description`

## Tredje formen (3NF)

Tabell får inte ha beroenden till kolumner (i andra tabeller) som inte är nycklar.

Följer inte 3NF: `patient_id | patient_name | doctor_number`

---

# Exempel utan normalisering

```sql
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    date DATE,
    product_ids TEXT,
    product_amounts TEXT,
    total_price DECIMAL(10, 2),
);
```

---

# Exempel med normalisering

```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    date DATE,
);

CREATE TABLE order_items (
    order_id INT REFERENCES orders(id),
    product_id INT REFERENCES products(id),
    product_amount INT,
    PRIMARY KEY (order_id, product_id)
);

CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    description TEXT,
    price DECIMAL
);
```

---

# Tips vid design av tabeller

- Använd `SERIAL` eller `UUID` för primära nycklar
  - De är enkla att hantera, behöver aldrig ändras och är unika
- Separera tabeller konceptuellt
  - Exempel: studerande och kurser är inte samma sak, gör dem till separata tabeller
- Skapa nya tabeller för "listor" av saker, och bilda relationer
- Följ principer för normalisering

---

# Projekt: Reddit klon

- Registrera konto
- Logga in och ut
- Skapa inlägg
- Visa inlägg och kommentarer
  - Kommentera på inlägg
  - Gilla inlägg
  - Gilla kommentar

## Gruppövning: designa tabeller för app

Designa tabeller som kan stödja all funktionalitet. Alla grupper redovisar sina lösningar efteråt, och vi diskuterar tillsammans.

---

# Quiz frågor

- Ge ett exempel på en Many-to-Many relation.
- Vad kallas tabellen som bildar Many-to-Many relationer?
- Vad är det för skillnad på `LEFT JOIN` och `RIGHT JOIN?`
- Vad gör `FULL OUTER JOIN`?
- Vad gör `ON DELETE RESTRICT`?
- Nämn ett exempel då `ON DELETE SET NULL` är passande.
- Kan en query innehålla flera joins?
- Varför är normalisering viktigt?
- Vad säger den andra normalformen (2NF)?
- Vad säger den tredje normalformen (3NF)?
- Vilken constraint kan användas för att förhindra ändring av primary key?
- Varför följer inte tabellen normaliserings principer: `store_name | store_location | products`?
- Varför följer inte tabellen normaliserings principer: `patient_id | patient_name | doctor_number`?
- Vad är skillnaden på `INNER JOIN` och `LEFT JOIN`?
