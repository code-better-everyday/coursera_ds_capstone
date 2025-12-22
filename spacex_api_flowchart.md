# SpaceX Data Collection (High-Level Flow)

```mermaid
flowchart TD
    A([Start]) --> B[Define Objective: Predict landing success]
    B --> C[Import libraries: requests, pandas, numpy, datetime]

    C --> D{Acquire Launch Data}
    D -->|Primary| D1[GET static JSON\nAPI_call_spacex_api.json]
    D -->|Alternative| D2[GET SpaceX API\n/v4/launches/past]

    D1 --> E[Normalize JSON to DataFrame]
    D2 --> E

    E --> F[Select relevant fields:\nrocket, payloads, launchpad, cores, flight_number, date]
    F --> G[Flatten lists\n(payloads, cores) to scalars]
    G --> H[Convert date & restrict to\nlaunches on/before 2020-11-13]

    H --> I{Enrich via API lookups}
    I --> I1[For rocket id:\n/v4/rockets → BoosterVersion]
    I --> I2[For launchpad id:\n/v4/launchpads → Site, Lat, Long]
    I --> I3[For payload id:\n/v4/payloads → Mass, Orbit]
    I --> I4[For core details:\n/v4/cores → Block, Serial, Reuse, Outcome]

    I1 --> J[Assemble enriched dataset]
    I2 --> J
    I3 --> J
    I4 --> J

    J --> K{Filter}
    K --> K1[Keep Falcon 9\n(Exclude Falcon 1)]
    K1 --> K2[Reset FlightNumber sequence]

    K2 --> L{Data Cleaning}
    L --> L1[Handle missing values:\nFill PayloadMass with mean]
    L --> L2[Keep None for LandingPad\nwhen not applicable]

    L1 --> M[Review head & summary]
    L2 --> M

    M --> N[Save for next steps\nCSV export]
    N --> O([End])
```
