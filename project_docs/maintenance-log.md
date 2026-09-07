\## Maintenance Task 1 - Corrective Maintenance



\### Problem

When different requests in the same Postman collection used different

authentication headers, postman2pytest combined them into one shared

auth\_headers fixture.



As a result, every authenticated request received all authentication

headers from the collection.



Example:

\- Request A used Authorization: Bearer ...

\- Request B used X-Api-Key: ...



Both generated tests received both authentication headers.



\### Reproduction

A new regression test was added:



test\_different\_auth\_schemes\_are\_not\_mixed\_between\_requests



The test initially failed and showed that the shared auth\_headers fixture

was applied completely to both requests.



\### Root Cause

The generator only stored whether a request had authentication:



has\_auth\_per\_request



It did not store which authentication headers belonged to each request.



The Jinja template therefore generated:



\*\*auth\_headers



for every authenticated request.



\### Solution

The generator was changed to keep track of authentication header names

for each individual request using:



auth\_names\_per\_request



The template was then changed so that each request selects only the

authentication headers it actually requires.



Example generated code:



Bearer request:



"Authorization": auth\_headers\["Authorization"]



API key request:



"X-Api-Key": auth\_headers\["X-Api-Key"]



\### Files Changed

\- core/generator.py

\- templates/test\_collection.jinja2

\- tests/test\_auth\_fixtures.py

\- tests/test\_generator.py



\### Testing

Original baseline:

209 tests passed.



A new regression test was added, bringing the total to 210 tests.



Final result:

210 tests passed.



\### Maintenance Type

Corrective maintenance.



The change corrects incorrect behavior in the generated pytest tests

when multiple authentication schemes are used in the same collection.



\### Status

Completed.

