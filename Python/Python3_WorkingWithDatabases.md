# Working with Databases in Python 3

- Course URL: <https://app.pluralsight.com/ilx/video-courses/python-3-working-databases/course-overview>

## Using a local Relational Database - SQLite

- Usage: embedded db for demo or prototyping during development
- Module `sqlite3`
  - connect to db: `db = sqlite3.connect(<db_filename>)`
  - Create cursor: `cursor = db.cursor()`
    - Execute query with cursor: `cursor.execute(create_table_query)`
    - Commit change: `db.commit()`
    - Select query: `result = cursor.execute(select_query, <optional_params_tuple>)`
      - Get the result: `result.fetchall()` or `result.fetchone()`
  - Row factory: auto-convert row tuple results into class instance --> don't have to remember & use index only

    ```python
    # Have a class represent a db

    def investment_row_factory(_, row):
        return Investment(
            coin_id = row[0], 
            currency = row[1], 
            amount = row[2], 
            sell = bool(row[3]), 
            date = datetime.datetime, strptime(row[4], "%Y-%m-%d"))
    # ....

    # setting row_factory method --> Must be "before" creating cursor()
    db.row_factory = investment_row_factory
    ```

## Using a Relational Database - PostgreSQL and psycopg2

- Module `psycopg2`: most popular
  - Could install from source, but easier with binary
  - Sub-module `psycopg2.extras`: helper for "psycopg2", such as parameterised query

### Example

```python
import psycopg2

# Connection
dbcon = psycopg2.connect(
    host="localhost",
    database="manager",
    user="postgres",
    password="pgpassword"
)

# cursor for execution
cursor = dbcon.cursor()

# retrieval
cursor.execute("SELECT * FROM investment;")
data = cursor.fetchall()

# Clean up
cursor.close()
dbcon.close()
```

### Inserting Data

```python
import psycopg2.extras

add_investment_sql = "insert into investment(coin, currency, amount) values %s"

data = [
    ("ethereum", "GBP", 10.0),
    ("dogecoin", "EUR", 100.0)
]

psycopg2.extras.execute_values(cursor, add_investment_sql, data)
dbcon.commit()
```

### Retrieving Data

- Has built-in cursor factory: `cursor = dbcon.cursor(cursor_factory=psycopg2.extras.RealDictCursor)`
  - Fetch now returned a dictionary instead of tuple
- To convert row tuple into instance of dataclass: `data = [Investment(**dict(row)) for row in cursor.fetchall()]`

## Using an ORM - SQLAlchemy

- Object Relational Mapper (ORM) --> Use python syntax for db operations
  - Use a single language
  - Can switch database with minimal changes
- Module SQLAlchemy --> provide a Core API, and the ORM built on top of that API

### Data Modeling

- All model classes must inherit from `DeclarativeBase`, but not directly
  - Must have a "Base" class inherit `DeclarativeBase`
- Attribute to column mappings are defined using type annotations
- Basic workflows: engine connect to db --> session create --> interact with db through session

```python

# Base class
from sqlalchemy.orm import DeclarativeBase

class Base(DeclarativeBase):
    pass

# Model class
from sqlalchemy import String, Numeric
from sqlalchemy.orm import Mapped, mapped_column

class Investment(Base):
    __tablename__ = "investment"
    id: Mapped[int] = mapped_column(primary_key=True)
    coin: Mapped[str] = mapped_column(String(32))
    currency: Mapped[str] = mapped_column(String(3))
    amount: Mapped[float] = mapped_column(Numeric(5, 2))

# Model
from sqlalchemy import create_engine

engine = create_engine("sqlite:///data.db")
Base.metadata.create_all(engine)

# Insert data
from sqlalchemy.orm import Session

with Session(engine) as session:
    session.add(Investment(coin="bitcoin", currency="USD", amount=1.0))
    session.commit()

# Select data
from sqlalchemy import select

stmt = select(Investment).where(Investment.coin == "bitcoin")
with Session(engine) as session:
    bitcoin = session.execute(stmt).scalar_one()

    # Update & Save --> changed object will be added to session.dirty
    bitcoin.amount = 1.234
    # Persist the change & reset the dirty flag
    session.commit()

    # Deletion --> deleted objects can be viewed in session.deleted
    dogecoin = session.get(Investment, 3)
    session.delete(dogecoin)
    session.commit()
```

### Relationships

- To model relationship

```python

# Model the relationship
class Portfolio(Base):
    __tablename__ = "portfolio"
    id: Maped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(256))
    description: Mapped[str] = mapped_column(Text())
    investments: Mapped[List["Investment"]] = relationship(back_populates="portfolio")

class Investment(Base):
    __tablename__ = "investment"
    ...
    portfolio_id: Mapped[int] = mapped_column(ForeignKey("portolio.id")) # name of the table, not the model class
    portfolio: Mapped["Portfolio"] = relationship(back_populates="investments") # optional, but convenient

# Adding 
portfolio = Portfolio("My Portfolio")

bitcoin = Investment("bitcoin", "USD", 1.0)
ethereum = Investment("ethereum", "GBP", 10.0)
dogecoin = Investment("dogecoin", "EUR", 100.0)

# Can "populate" relationship either ways
bitcoin.portfolio = portfolio

portfolio.investments.extend([ethereum, dogecoin])

# Only need to save the portoflio to get everythin in db
with Session(engine) as session:
    session.add(portfolio)
    session.commit()
```

### Swapping Databases

- To switch db, only need to change the connection string
  - Sqlite: `engine = create_engine("sqlite:///manager.db")`
  - PostgreSQL: `engine = create_enigne("postgresql://postgres:pgpassword@localhost/manager")`

## Using a Local NoSQL database - Mongita

- Conceptual: MongoDb is a group of collections --> collection: group of documents --> document: data in JSON
- Mongita: subset of MongoDb API (i.e. `PyMongo`), but for local files --> Python only package
  - Used for embedded database, or unit testing

```python
from mongita import MongitaClientDisk

client = MongitaClientDisk

# get/create the portfolio database
db = client.portfolio 

# get/create the investments collection
investments = db.investments

# insert a document into the investments collection 
# insert_many() for many documents
investments.insert_one({"coin_id": "bitcoin", "currency": "usd", "amount": 1.0})

# get all documents with a coin_id of bitcoin --> return a cursor, or None if none is found
# Use find_one() to find "first" document
bitcoin_inv = investments.find({"coin_id": "bitcoin"})
all_investments = investments.find({})
```

- `update_many(<filter_json>, {<update_op>: {'key': 'value'}})`: update all documents matching filter, using the update operation
- `delete_one(<filter_json>)`: delete the first document matching filter --> use `delete_many()` for deletion of all matching documents

## Using a NoSQL Database - MongoDB and pymongo

- Everything in Mongita works with PyMongo
- Connecting with pymongo:

  ```python
  from pymongo import MongoClient

  client = MongoClient() # default connection to localhost:27017
  ```

- Filtering with multiple conditions

  ```python
  investments.find({"$and": [
    {"coin": "bitcoin"},
    {"amount": {"$gt": 2}}
  ]})
  ```

- Embedded documents & lists --> model relationship
  - For 1-1 relationship --> 1 embedded dictionary
  - For 1-Many relationship --> a field with a list of dictionaries
  - Use `$push` & `$pull` operators to add/remove embedded documents

## Using an ODM - MongoEngine
