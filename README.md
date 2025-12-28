# AwesomePizza

Simple Spring Boot REST API that simulates a pizza order moving through a small state machine (`IN_LAVORAZIONE` -> `INFORNATA` -> `COTTA`) and lets you query its status.

## Requirements

- Java 17 (the project targets Java 17; newer JDKs can work, but ensure the build compiles with `--release 17`)
- Maven (or use the included Maven Wrapper: `mvnw` / `mvnw.cmd`)

## Run

Using Maven Wrapper (recommended):

- Windows: `.\mvnw.cmd spring-boot:run`
- macOS/Linux: `./mvnw spring-boot:run`

The app starts on `http://localhost:8080` (default Spring Boot port).

## API

### Create an order

`POST /orders`

- Body: a single integer (your `orderNum`)
- Content-Type: `application/json`

Example:

```bash
curl -X POST "http://localhost:8080/orders" -H "Content-Type: application/json" -d "123"
```

Notes:

- The request blocks for ~10 seconds because the pizzaiolo simulation runs in the request thread.
- The response is an integer, but status lookups are keyed by the `orderNum` you send in the request body.

### Get order status

`GET /orders?orderId=<orderNum>`

Example:

```bash
curl "http://localhost:8080/orders?orderId=123"
```

Response is a plain string in Italian, e.g. `La sua pizza ...: INFORNATA`.

## How it works (high level)

- Orders are stored in-memory (`InMemoryMapRepository`), so restarting the app forgets everything.
- A single pizzaiolo (`Pizzaiolo.GIGI`) advances the `PizzaState` with timed delays to simulate preparation and baking.

## Troubleshooting

- If you see `java.lang.NoSuchFieldError: ... JCTree$JCImport ... qualid`, verify which JDK Maven is using with `.\mvnw.cmd -v` and use a compatible JDK (recommended: 17).
