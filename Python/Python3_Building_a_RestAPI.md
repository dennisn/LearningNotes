# Building a REST API with Python 3

- URL: <https://app.pluralsight.com/ilx/video-courses/python-3-building-rest-api/course-overview>
- Project source code: <https://bit.ly/python3-rest-api>
  - Redirected to <https://github.com/codesensei-courses/python_3_rest_api>

## Starting the Project

- Project environment configured is via `poetry`
- Server is inherited from `FastAPI`, running inside `uvicorn` HTTP server
  - Static files from `static` folder
- Install `pre-commit` --> setup github pre-commit hook with `.pre-commit-config.yaml`
  - Will run `black`, `mypy`, `flake8` and `isort` before commit --> automatically format our files, and it will check our type hints and code smells and sort our import statements.

## Creating a Datamodel

- Data model + SqlAlchemy (see [module on Db](./Python3_WorkingWithDatabases.md#using-an-orm---sqlalchemy))
- Using `dotenv` to load variable from "**.env**" file into environment variable --> use as db URL instead of hardcode
  - Sample: `load_dotenv(); SQLALCHEMY_DB_URL = os.getenv("SQLALCHEMY_DB_URL");`
  - Can move the `load_dotenv()` file into `__init__.py` --> loaded with the package
  - Normally, don't want to commit "**.env**" file into git --> local configuration settings

## Make Content accessible through an API

- Documentation is auto-generated, and can be viewed at "/docs"
- API endpoint by decorator, such as `@app.get("/hello")`, where "app" is the FastAPI object
  - NOTE: the catch all path "/" should be last, as it will match all URL
- Can inject session object with `Annotated`
  
  ```python
  from typing import Annotated

  def get_db_session() -> DbSession:
    session = DbSession.LoadLocal()
    try:
        yield session
    finally:
        session.close()

  @app.get("/event/{id}")
  def get_event(id: int, db: Annotated[DbSession, Depends(get_db_session)]):
    # Inject the "DbSession" object into variable db, by calling "get_db_session()" method
    pass
  ```

- Module `pydantic` --> define the response Schema in `@app.get("/event/{id}", response_model=Event)`
  - The actual object returned is `DbEvent`, which FastAPI will convert to the `Event` schema

  ```python
  from pydantic import BaseModel

  class Event(BaseModel):
    id: int
    product_code: str
    date: datetime.date
    price: decimal.Decimal
  ```

- FastAPI has `TestClient` to test FastAPI object
  - For session object, can use "dependency_overrides" to replace injection from "get_db_session()" with "get_test_db_sesion()" instead
  - Using following example, unittest can be injected with "client" object, which is of type TestClient

    ```python
    def get_test_db_session():
        ...

    @pytest.fixture()
    def client():
        """Create a TestClient that uses the test db"""
        # see https://fastapi.tiangolo.com/advanced/testing-database/
        app.dependency-overrides[get_db_session] = get_test_db_session
        yield TestClient(app)
        del app.dependency-overrides[get_db_session]
    ```

## Reading Content from Filesystem

- Front matter data from file can be found & read with `pathlib` --> data parsed with `regex`
- Database model object can be initialized with extra data (not from db) with `@reconstructor` --> run whenever it reads an object from the database