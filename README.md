---
Wspomaganie działań przeciwdziałania praniu pieniędzy i finansowania terroryzmu
---

# SystemAML

###

## Spis treści:

- 1. [Informacje ogólne](#informacje-ogólne)
  - 1.1. [Klucze API](#klucze-api)
  - 1.2. [Nagłówek zapytania](#nagłówek-zapytania)
  - 1.3. [Ciało zapytania](#ciało-zapytania)
  - 1.4. [Mechanizm webhooków](#mechanizm-webhooków)
- 2. [Opis usług](#opis-usług)
  - 2.1. [POST /parties](#post-parties)
  - 2.2. [GET /parties](#get-parties)
  - 2.3. [GET /parties/{code}](#get-partiescode)
  - 2.4. [DELETE /parties/{code}](#delete-partiescode)
  - 2.5. [POST /parties/{code}/beneficiaries](#post-partiescodebeneficiaries)
  - 2.6. [GET /parties/{code}/beneficiaries](#get-partiescodebeneficiaries)
  - 2.7. [DELETE /beneficiaries/{code}](#delete-beneficiariescode)
  - 2.8. [POST /parties/{code}/boardmembers](#post-partiescodeboardmembers)
  - 2.9. [GET /parties/{code}/boardmembers](#get-partiescodeboardmembers)
  - 2.10. [DELETE /boardmembers/{code}](#delete-boardmemberscode)
  - 2.11. [POST /transactions](#post-transactions)
  - 2.12. [GET /transactions](#get-transactions)
  - 2.13. [GET /transactions/{code}](#get-transactionscode)
  - 2.14. [DELETE /transactions/{code}](#delete-transactionscode)
  - 2.15. [POST /history-events](#post-history-events)
  - 2.16. [GET /history-events](#get-history-events)
  - 2.17. [GET /history-events/{code}](#get-history-eventscode)
  - 2.18. [DELETE /history-events/{code}](#delete-history-eventscode)
  - 2.19. [POST /comments](#post-comments)
  - 2.20. [GET /history-events/{code}/comments](#get-history-eventscodecomments)
  - 2.21. [DELETE /comments/{code}](#delete-commentscode)
  - 2.22. [GET /alerts](#get-alerts)
  - 2.23. [GET /alerts/{code}](#get-alertscode)
  - 2.24. [DELETE /alerts/{code}](#delete-alertscode)
  - 2.25. [POST /tasks](#post-tasks)
  - 2.26. [GET /tasks](#get-tasks)
  - 2.27. [GET /tasks/{code}](#get-taskscode)
  - 2.28. [DELETE /tasks/{code}](#delete-taskscode)
  - 2.29. [POST /sanctions-lists/search](#post-sanctions-listssearch)
  - 2.30. [GET /sanctions/{code}/pdf](#get-sanctionscodepdf)
  - 2.31. [POST /parties/applicants](#post-partiesapplicants)
  - 2.32. [GET /parties/{code}/applicants](#get-partiescodeapplicants)
  - 2.33. [GET /parties/{code}/applicants/current](#get-partiescodeapplicantscurrent)
  - 2.34. [GET /applicants/{code}](#get-applicantscode)
  - 2.35. [DELETE /applicants/{code}](#delete-applicantscode)
  - 2.36. [POST /applicants/{code}/acceptance](#post-applicantscodeacceptance)
#

###

## Informacje ogólne

Głównym zastosowaniem API jest katalogowanie podmiotów, przez działalności od których wymagane jest prowadzenie ewidencji klientów.

### Adresy serwerów

| Funkcjonalność | Środowisko | URL                                                                 |
| -------- | -------- | --------------------------------------------------------------------------- |
| Serwer API | Produkcyjne   | https://api.systemaml.pl/1.0/ |
| Frontend aplikacji | Produkcyjne   | https://systemaml.pl/ |
| Serwer API | Testowe   | http://apitest.systemaml.pl/1.0/ |
| Frontend aplikacji | Testowe   | https://test.systemaml.pl/ |

### UWAGA!

Są to zupełnie rozdzielone środowiska (łącznie z infrastrukturą). Klucze API z jednego środowiska nie będą działać w drugim.

### Klucze API

Do korzystania z API konieczne jest wygenerowanie kluczy:

- jawnego (apiKey) - używanego do przesyłania w ramach żądań API
- tajnego (secretKey) - używanego do podpisywania żądań (nigdy nie powinien by przesyłany lub ujawniany)

W celu uzyskania danych dostępowych niezbędnych do poprawnego korzystania z API należy wygenerować klucze na frontendzie aplikacji lub skontaktować się bezpośrednio z usługodawcą (info@fiberpay.pl).

W przypadku niepoprawnego wykorzystania kluczy dostępowych serwer zwraca następujące błędy:

**STATUS 401 Unauthorized**

```json
{
  "status": "ERROR",
  "error": "No Api Key provided"
}
```

```json
{
  "status": "ERROR",
  "error": "Api key invalid"
}
```

```json
{
  "status": "ERROR",
  "error": "Wrong signature"
}
```

### Nagłówek zapytania

Każde żądanie do API powinno posiadać następujący nagłówek:

- Api-Key – wygenerowany klucz jawny

W przypadku zapytań nie posiadających body, należy wysłać żądanie z nagłówkiem Authorization o wartości "Bearer {token}" (token - pusty string w postaci JWT z odpowiednią sygnaturą).

### Ciało zapytania

Każde ciało zapytania jest przekazywane za pomocą JWT z wykorzystaniem odpowiedniej sygnatury. Body żądania powinno być tekstem (JWT).

#### Przykładowy skrypt tworzenia ciała zapytania JWT

```javascript
const { encode } = require("jwt-simple");
const SECRET = "<secretKey>";
 //Przykładowy payload do endpointu do wyszukiwania na listach sankcyjnych
const payload = {
  "entityType": "any",
  "name": "Nazwa podmiotu",
}
const encoded = encode(payload, SECRET, "HS256");

```
### Mechanizm webhooków

SystemAML automatycznie wysyła żądania HTTP POST na wcześniej skonfigurowany adres URL w momencie wystąpienia określonych zdarzeń w systemie (np. zmiana statusu podmiotu, utworzenie transakcji).

W ciele (body) żądania HTTP POST będzie zawarty **token JWT** zawierający dane o zdarzeniu.

**Token JWT** należy zweryfikować używając algorytmu **HS256** oraz **webhook secret** wygenerowanego w [panelu konfiguracji webhooków](https://systemaml.pl/dashboard/settings/api/).
Weryfikacja sygnatury tokenu potwierdza autentyczność danych i gwarantuje, że zostały wysłane przez SystemAML.

#### Przykładowy webhook w postaci JWT dla typu TRANSACTION_DATA_UPDATED:
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJwYXlsb2FkIjp7InR5cGUiOiJUUkFOU0FDVElPTl9EQVRBX1VQREFURUQiLCJjb2RlIjoieDh5NnBxZTZ5c3M2IiwiZGF0YSI6eyJ0cmFuc2FjdGlvbiI6eyJjb2RlIjoic3pyenZ6ODlxajhlIiwicmVmZXJlbmNlcyI6bnVsbCwidHlwZSI6ImJ1eWVyIiwic3RhdHVzIjoiYWNjZXB0ZWQifX19LCJpc3MiOiJTeXN0ZW1BTUwiLCJpYXQiOjE3NDE3NzY5NjQsImV2ZW50VGltZSI6IjIwMjUtMDMtMTRUMTI6NDk6MjQuMDAwMDAwWiJ9.Xq3HDU9P6m8Va_l4VW-czoUGyGpX446H_8VXcHDv0Mc
```
#### Po zdekodowaniu:

```json
{
  "payload": {
    "type": "transaction_data_updated",
    "code": "x8y6pqe6yss6",
    "eventTime": 1741776952,
    "data": {
      "transaction": {
        "code": "szrzvz89qj8e",
        "references": null,
        "type": "buyer",
        "status": "accepted"
      }
    }
  },
  "iss": "SystemAML",
  "iat": 1741776964
}
```

### Lista typów webhooków

| Typ | Opis zdarzenia |
| -------- | --------------------------------------|
| **party_profile_updated** | Aktualizacja danych podmiotu |
| **party_status_change** | Zmiana statusu podmiotu |
| **party_risk_change** | Zmiana statusu ryzyka podmiotu |
| **party_deleted** | Usunięcie podmiotu |
| **transaction_data_updated** | Aktualizacja danych transakcji |
| **transaction_status_change** | Zmiana statusu transakcji |
| **transaction_risk_change** | Zmiana statusu ryzyka transakcji |
| **transaction_deleted** | Usunięcie transakcji |
| **applicant_created** | Utworzenie aplikanta KYC |
| **applicant_status_change** | Zmiana statusu aplikanta KYC |

Informację o tokenach JWT oraz bibliotekach do weryfikacji tokenów w różnych językach programowania znajdziesz pod adresem: [https://jwt.io/](https://jwt.io/)


## Opis usług

### POST /parties

Utworzenie nowego podmiotu. Parametry żądania:

| Parametr | Wymagane | Opis                                                                        |
| -------- | -------- | --------------------------------------------------------------------------- |
| **type** | TAK      | Typ podmiotu. Aktualnie wspierane: individual, sole_proprietorship, company |
| **status** | TAK    | Status podmiotu. Aktualnie wspierane: draft, active, inactive, in_acceptance |

W zależności od wybranego typu wymagane są następujące parametry:

a) individual:

| Parametr                   | Wymagane | Opis                                                                                            |
| -------------------------- | -------- | ----------------------------------------------------------------------------------------------- |
| **firstName**              | TAK      | Imię podmiotu                                                                                   |
| **lastName**               | TAK      | Nazwisko podmiotu                                                                               |
| **middleName**             | NIE      | Drugie imię podmiotu                                                                            |
| **familyName**             | NIE      | Nazwisko rodowe                                                                               |
| **personalIdentityNumber** | TAK      | Numer PESEL podmiotu (w przypadku braku numeru PESEL wymagany jest parametr birthDate oraz birthCountry) |
| **birthDate**              | NIE      | Data urodzenia (wymagana jeśli nie ma numeru PESEL)                                             |
| **birthCountry**           | NIE      | Kraj urodzenia (wymagany jeśli nie ma numeru PESEL)                                             |
| **references**             | NIE      | Referencje własne                                                                               |
| **citizenship**            | NIE      | Obywatelstwo (kod kraju w standardzie ISO)                                                      |
| **birthCity**              | NIE      | Miejsce urodzenia                                                                               |
| **documentType**           | TAK      | Rodzaj dokumentu takie jak: "id_card", "electronic_id_card", "passport", "residency_card", "other" (nie jest wymagany jeśli nie ma numeru PESEL)                                  |
| **documentNumber**         | NIE      | Numer dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                                  |
| **documentIssueCountry**         | NIE      | Kraj wydania dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                       |
| **documentTypeOther**         | NIE      | Rodzaj innego dokumentu, jest wymagany tylko w przypadku kiedy "documentType": "other"       |
| **documentExpirationDate** | NIE      | Termin ważności dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                                                                       |
| **economicRelationStartDate**    | TAK     | Data rozpoczęcia stosunków gospodarczych                                                   |
| **withoutExpirationDate**  | NIE      | Informacja czy dokument posiada datę ważności (bool)                                            |
| **politicallyExposed**           | TAK     | Informacja czy podmiot jest eksponowany politycznie ('yes' lub 'no')                                 |
| **politicallyExposedFamily**     | TAK     | Informacja czy podmiot jest rodziną osoby eksponowanej politycznie ('yes' lub 'no')                  |
| **politicallyExposedCoworker**   | TAK     | Informacja czy podmiot jest bliskim współpracownikiem osoby eksponowanej politycznie ('yes' lub 'no')|
| **employmentType**         | NIE      | Stan zatrudnienia. Wartości proponowane przez system: student, retiree, pensioner, entrepreneur, employedUOP, employedUZUOD, unemployed, jobless, annuitant student|
| **createdByName**          | NIE      | Osoba wprowadzająca wpis


b) sole_proprietorship - wszystkie powyższe oraz:

| Parametr                           | Wymagane | Opis                                                                |
| ---------------------------------- | -------- | ------------------------------------------------------------------- |
| **taxIdNumber**                    | TAK      | NIP prowadzonej działalności                                        |
| **registrationCountry**            | TAK      | Kraj rejestracji podmiotu                                           |
| **companyIdentifier**              | NIE      | Numer identyfikujący (wymagany jeśli nie ma numeru NIP)             |
| **nationalBusinessRegistryNumber** | NIE      | Regon prowadzonej działalności                                      |
| **companyName**                    | TAK      | Nazwa prowadzonej działalności                                      |
| **tradeNames**                     | NIE      | Tablica z nazwami handlowymi                                        |
| **terminationDate**                | NIE      | Data zakończenia działalności                                       |
| **mainPkdCode**                    | TAK      | Obiekt z przeważającym kodem PKD (nie jest wymagany gdy nie ma NIP) |
| **pkdCodes**                       | NIE      | Tablica z pozostałymi kodami PKD (tablica zawierająca obiekty jw.)  |

Struktura obiektu z kodem PKD:

| Parametr    | Wymagane | Opis                                |
| ----------- | -------- | ----------------------------------- |
| **pkdCode** | TAK      | Numer kodu PKD w formacie (00.00.X) |
| **pkdName** | NIE      | Opis kodu PKD                       |

c) company:

| Parametr                           | Wymagane | Opis                                                                         |
| ---------------------------------- | -------- | ---------------------------------------------------------------------------- |
| **taxIdNumber**                    | TAK      | Numer NIP                                                                    |
| **registrationCountry**            | TAK      | Kraj rejestracji podmiotu                                                    |
| **companyIdentifier**              | NIE      | Numer identyfikujący (wymagany jeśli nie ma numeru NIP)                      |
| **references**                     | NIE      | Referencje własne                                                            |
| **companyName**                    | TAK      | Nazwa działalności                                                           |
| **tradeNames**                     | NIE      | Tablica z nazwami handlowymi                                                 |
| **nationalBusinessRegistryNumber** | NIE      | Numer Regon                                                                  |
| **nationalCourtRegistryNumber**    | NIE      | Numer KRS                                                                    |
| **terminationDate**                | NIE      | Data zakończenia działalności                                                |
| **businessActivityForm**           | TAK      | Rodzaj prowadzonej działalności (nie jest wymagane jeśli nie ma numeru NIP)  |
| **website**                        | NIE      | Strona internetowa                                                           |
| **servicesDescription**            | NIE      | Opis usług                                                                   |
| **mainPkdCode**                    | TAK      | Obiekt z przeważającym kodem PKD (nie jest wymagany gdy nie ma NIP)          |
| **pkdCodes**                       | NIE      | Tablica z pozostałymi kodami PKD (tablica zawierająca obiekty jw.)           |
| **beneficiaries**                  | NIE      | Tablica obiektów z danymi beneficjentów                                      |
| **boardMembers**                   | NIE      | Tablica obiektów z danymi reprezentantów                                     |
| **createdByName**                  | NIE      | Osoba wprowadzająca wpis                                                     |
| **economicRelationStartDate**      | TAK      | Data rozpoczęcia stosunków gospodarczych                                     |
| **listedOnStock**                  | NIE      | Podmiot notowany na giełdzie                                                 |


Struktura obiektu beneficjenta:

| Parametr                     | Wymagane | Opis                                                                       |
| ---------------------------- | -------- | -------------------------------------------------------------------------  |
| **directRights**             | NIE      | Bezpośrednie uprawnienia                                                   |
| **ownedSharesAmount**        | NIE      | Liczba posiadanych udziałów                                                |
| **ownedSharesUnit**          | NIE      | Jednostka posiadanych udziałów ('%' lub 'PLN')                             |
| **directRightsPrivilegeType**           | NIE      | Rodzaj uprzywilejowania                                         |
| **directRightsPrivilegeDescription**    | NIE      | Opis uprzywilejowania                                           |
| **indirectRights**           | NIE      | Pośrednie uprawnienia                                                      |
| **otherRights**              | NIE      | Inne uprawnienia                                                           |
| **otherRightsDescription**   | NIE      | Opis uprawnień                                                             |
| **additionalInformation**    | TAK      | Dodatkowe informacje, nie są wymagane, o ile przynajmniej jeden z parametrów (directRights, ownedSharesAmount, ownedSharesUnit, directRightsPrivilegeType, indirectRights, otherRights, otherRightsDescription) posiada wartość |
| **firstName**                | TAK      | Imię beneficjenta                                                          |
| **lastName**                 | TAK      | Nazwisko beneficjenta                                                      |
| **personalIdentityNumber**   | TAK      | Numer PESEL beneficjenta (w przypadku braku numeru PESEL wymagany jest parametr birthDate) |
| **documentType**             | NIE      | Rodzaj dokumentu                                                           |
| **documentNumber**           | NIE      | Numer dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                                                            |
| **documentIssueCountry**         | NIE      | Kraj wydania dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                       |
| **documentExpirationDate**   | NIE      | Termin ważności dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                                                   |
| **withoutExpirationDate**    | NIE      | Informacja czy dokument beneficjenta jest bezterminowy (bool) jest wymagane kiedy jest podany jakikolwiek documentType i dokument jest bezterminowy             |
| **birthDate**                | TAK      | Data urodzenia (wymagana jeśli nie ma numeru PESEL)                        |
| **birthCity**                | NIE      | Miejsce urodzenia                                                      |
| **citizenship**              | NIE      | Obywatelstwo (kod kraju w standardzie ISO)                                   |
| **birthCountry**             | NIE      | Kraj urodzenia                                                             |
| **politicallyExposed**       | TAK      | Informacja czy beneficjent jest eksponowany politycznie (bool)             |
| **politicallyExposedFamily**     | TAK     | Informacja czy beneficjent jest rodziną osoby eksponowanej politycznie ('yes' lub 'no')                  |
| **politicallyExposedCoworker**   | TAK     | Informacja czy beneficjent jest bliskim współpracownikiem osoby eksponowanej politycznie ('yes' lub 'no')|
| **accommodationAddress**     | NIE      | Obiekt zawierający adres zamieszkania (opis struktury w punkcie "a) adres")|

Struktura obiektu reprezentanta:

| Parametr                     | Wymagane | Opis                                                                       |
| ---------------------------- | -------- | -------------------------------------------------------------------------- |
| **firstName**                | TAK      | Imię reprezentanta                                                         |
| **lastName**                 | TAK      | Nazwisko reprezentanta                                                     |
| **personalIdentityNumber**   | TAK      | Numer PESEL reprezentanta (w przypadku braku numeru PESEL wymagany jest parametr (personalIdentifier) |
| **documentType**             | TAK      | Rodzaj dokumentu (nie jest wymagany jeśli nie ma numeru PESEL)             |
| **documentNumber**           | NIE      | Numer dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)            |
| **documentIssueCountry**     | NIE      | Kraj wydania dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                       |
| **documentExpirationDate**   | NIE      | Termin ważności dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                                                 |
| **personalIdentifier**       | NIE      | Numer identyfikujący reprezentanta (wymagany jeśli nie ma numeru PESEL)    |
| **birthDate**                | NIE      | Data urodzenia (wymagana jeśli nie ma numeru PESEL)                        |
| **birthCountry**             | NIE      | Kraj urodzenia (wymagany jeśli nie ma numeru PESEL)                        |
| **citizenship**              | NIE      | Obywatelstwo (kod kraju w standardzie ISO)                                 |
| **birthCity**                | NIE      | Miejsce urodzenia                                                          |
| **withoutExpirationDate**    | NIE      | Informacja czy dokument posiada datę ważności (bool) jest wymagane kiedy jest podany jakikolwiek documentType i dokument jest bezterminowy                        |
| **references**               | NIE      | Referencje własne                                                          |
| **roleType**                 | TAK      | Pełniona rola. Aktualnie wspierane: president, board_member , proxy, other |
| **description**              | TAK      | Opis pełnione roli (wymagane jeśli roleType ma wartość other)              |
| **politicallyExposed**       | TAK      | Informacja czy reprezentant jest eksponowany politycznie (bool)            |
| **politicallyExposedFamily**     | TAK     | Informacja czy reprezentant jest rodziną osoby eksponowanej politycznie ('yes' lub 'no')                  |
| **politicallyExposedCoworker**   | TAK     | Informacja czy reprezentant jest bliskim współpracownikiem osoby eksponowanej politycznie ('yes' lub 'no')|

Do każdego z typów podmiotu można dodać dane kontaktowe.

| Parametr                 | Wymagane | Opis                                              |
| ------------------------ | -------- | ------------------------------------------------- |
| **accommodationAddress** | NIE      | Obiekt zawierający adres zamieszkania             |
| **forwardAddress**       | NIE      | Obiekt zawierający adres korespondencyjny         |
| **businessAddress**      | NIE      | Obiekt zawierający adres prowadzenia działalności |
| **personalContact**      | NIE      | Obiekt zawierający dane kontaktowe                |
| **companyContact**       | NIE      | Obiekt zawierający dane kontaktowe działalności   |

Struktura obiektów:&#x20;

a) adres:

| Parametr        | Wymagane | Opis                              |
| --------------- | -------- | --------------------------------- |
| **country**     | NIE      | Nazwa kraju (kod standardzie ISO) |
| **city**        | NIE      | Miasto                            |
| **street**      | NIE      | Ulica                             |
| **houseNumber** | NIE      | Numer domu                        |
| **flatNumber**  | NIE      | Numer mieszkania                  |
| **postalCode**  | NIE      | Kod pocztowy                      |

a) kontakt:

| Parametr         | Wymagane | Opis                   |
| ---------------- | -------- | ---------------------- |
| **emailAdress**  | NIE      | Adres email            |
| **phoneCountry** | NIE      | Prefix numeru telefonu |
| **phoneNumber**  | NIE      | Numer telefonu         |

#### Przykładowe dane do utworzenia podmiotu typu 'individual':

```json
{
  "birthCity": "Warszawa",
  "birthDate": "2000-01-12",
  "birthCountry": "PL",
  "citizenship": "PL",
  "createdByName": "Wojtek",
  "documentExpirationDate": "2026-05-15",
  "documentNumber": "SQT233656",
  "documentType": "id_card",
  "documentIssueCountry": "PL",
  "economicRelationStartDate": "2023-11-14",
  "firstName": "Jan",
  "lastName": "Kowalski",
  "personalIdentityNumber": "09271573233",
  "politicallyExposed": "no",
  "politicallyExposedCoworker":"no",
  "politicallyExposedFamily":"yes",
  "references": "qwerty",
  "status": "active",
  "type": "individual",
  "withoutExpirationDate": false,
  "accommodationAddress": {
    "country": "PL",
    "city": "Warszawa",
    "street": "Grzybowska",
    "houseNumber": "4",
    "flatNumber": "106",
    "postalCode": "00-131"
  },
  "forwardAddress": {
    "country": "PL",
    "city": "Warszawa",
    "street": "Grzybowska",
    "houseNumber": "4",
    "flatNumber": "106",
    "postalCode": "00-131"
  },
  "personalContact": {
    "emailAdress": "info@fiberpay.pl",
    "phoneCountry": "48",
    "phoneNumber": "123123123"
  }
}
```

#### Przykładowe dane do utworzenia podmiotu typu 'sole_proprietorship':

```json
{
  "registrationCountry": "PL",
  "birthCity": "Warszawa",
  "nationalBusinessRegistryNumber": "632702201",
  "birthCountry": "PL",
  "citizenship": "PL",
  "companyName": "Usługi programistyczne",
  "tradeNames": ["FiberPay", "SystemAML"],
  "createdByName": "Adam",
  "documentExpirationDate": "2026-05-08",
  "documentNumber": "aze123123",
  "documentType": "passport",
  "documentIssueCountry": "PL",
  "economicRelationStartDate": "2023-11-14",
  "firstName": "Jan",
  "lastName": "Kowalski",
  "mainPkdCode": {
    "pkdCode": "01.12.Z",
    "pkdName": "Uprawa ryżu"
  },
  "personalIdentityNumber": "99120234518",
  "pkdCodes": [
    {
      "pkdCode": "01.15.Z",
      "pkdName": "Uprawa tytoniu"
    }
  ],
  "politicallyExposed": "no",
  "politicallyExposedCoworker": "no",
  "politicallyExposedFamily": "yes",
  "references": "qwerty",
  "status": "active",
  "taxIdNumber": "3765151981",
  "type": "sole_proprietorship",
  "withoutExpirationDate": false,
  "forwardAddress": {
    "country": "PL",
    "city": "Warszawa",
    "street": "Grzybowska",
    "houseNumber": "4",
    "flatNumber": "106",
    "postalCode": "00-131"
  },
  "businessAddress": {
    "country": "PL",
    "city": "Warszawa",
    "street": "Grzybowska",
    "houseNumber": "4",
    "flatNumber": "106",
    "postalCode": "00-131"
  },
  "accommodationAddress": {
    "country": "PL",
    "city": "Warszawa",
    "street": "Grzybowska",
    "houseNumber": "4",
    "flatNumber": "106",
    "postalCode": "00-131"
  },
  "personalContact": {
    "emailAdress": "info@fiberpay.pl",
    "phoneCountry": "48",
    "phoneNumber": "123123123"
  },
  "companyContact": {
    "emailAdress": "info@fiberpay.pl",
    "phoneCountry": "48",
    "phoneNumber": "123123123"
  }
}
```

#### Przykładowe dane do utworzenia podmiotu typu 'company':

```json
{
  "registrationCountry": "PL",
  "type": "company",
  "companyName": "FiberPay",
  "economicRelationStartDate": "2023-11-14",
  "taxIdNumber": "7010634566",
  "nationalBusinessRegistryNumber": "147302566",
  "tradeNames": ["FiberPay"],
  "nationalCourtRegistryNumber": "0000512707",
  "businessActivityForm": "stock_company",
  "listedOnStock": "no",
  "website": "fiberpay.pl",
  "references": "qwerty",
  "status": "active",
  "businessAddress": {
    "country": "PL",
    "city": "Warszawa",
    "street": "Grzybowska",
    "houseNumber": "4",
    "flatNumber": "106",
    "postalCode": "00-131"
  },
  "companyContact": {
    "emailAdress": "info@fiberpay.pl",
    "phoneCountry": "48",
    "phoneNumber": "123123123"
  },
  "mainPkdCode": {
    "pkdCode": "64.99.Z",
    "pkdName": "POZOSTAŁA FINANSOWA DZIAŁALNOŚĆ USŁUGOWA, GDZIE INDZIEJ NIESKLASYFIKOWANA, Z WYŁĄCZENIEM UBEZPIECZEŃ I FUNDUSZÓW EMERYTALNYCH"
  },
  "pkdCodes": [
    {
      "pkdCode": "58.29.Z",
      "pkdName": "DZIAŁALNOŚĆ WYDAWNICZA W ZAKRESIE POZOSTAŁEGO OPROGRAMOWANIA"
    },
    {
      "pkdCode": "62.01.Z",
      "pkdName": "DZIAŁALNOŚĆ ZWIĄZANA Z OPROGRAMOWANIEM"
    }
  ],
  "beneficiaries": [
    {
      "birthCountry": "PL",
      "directRights": "ABB",
      "birthCity": "Warszawa",
      "citizenship": "PL",
      "documentNumber": "JET449773",
      "documentType": "id_card",
      "documentIssueCountry": "PL",
      "firstName": "Jan",
      "lastName": "Kowalski",
      "ownedSharesAmount": "45",
      "ownedSharesUnit": "%",
      "personalIdentityNumber": "64091098920",
      "politicallyExposed": "no",
      "politicallyExposedCoworker":"no",
      "politicallyExposedFamily":"no",
      "withoutExpirationDate": false,
    },
    {
      "birthCountry": "DE",
      "directRights": "3M",
      "birthCity": "Germany",
      "birthDate": "2002-10-01",
      "citizenship": "DE",
      "documentNumber": "aze423",
      "documentType": "passport",
      "firstName": "Hans",
      "lastName": "Podolski",
      "ownedSharesAmount": "15",
      "ownedSharesUnit": "%",
      "politicallyExposed": "no",
      "politicallyExposedCoworker":"no",
      "politicallyExposedFamily":"no",
      "withoutExpirationDate": false,
    },
  ],
  "boardMembers": [
    {
      "birthCity": "Warszawa",
      "birthDate": "2001-01-01",
      "birthCountry": "PL",
      "citizenship": "PL",
      "description": "Prezes spółki",
      "documentNumber": "GLD358884",
      "documentType": "id_card",
      "documentIssueCountry": "PL",
      "firstName": "Jan",
      "lastName": "Kowalski",
      "personalIdentityNumber": "31111161119",
      "politicallyExposed": "no",
      "politicallyExposedCoworker":"no",
      "politicallyExposedFamily":"no",
      "roleType" : "president",
      "withoutExpirationDate": false,
    },
    {
      "birthCity": "Warszawa",
      "birthDate": "2001-01-01",
      "birthCountry": "PL",
      "citizenship": "PL",
      "description": "Wiceprezes",
      "documentNumber": "OBG470534",
      "documentType": "id_card",
      "firstName": "Adam",
      "lastName": "Nowak",
      "politicallyExposed": "yes",
      "politicallyExposedCoworker":"no",
      "politicallyExposedFamily":"no",
      "roleType" : "other",
      "withoutExpirationDate": false,
    }
  ]
}
```

#### Przykładowa odpowiedź serwera:
- **STATUS 201 CREATED**

```json
{
  "data": {
    "code": "su619f8r7jkp",
    "type": "company",
    "status": "active",
    "riskStatus": null,
    "riskExplanation": "",
    "riskStatusChangedBy": "",
    "createdByName": null,
    "references": "qwerty",
    "economicRelationStartDate": "2023-11-14",
    "entity": {
      "code": "huv9q4acby1p",
      "companyName": "FiberPay",
      "tradeNames": [
        "FiberPay"
      ],
      "taxIdNumber": "7010634566",
      "nationalBusinessRegistryNumber": "147302566",
      "nationalCourtRegistryNumber": "0000512707",
      "businessActivityForm": "stock_company",
      "listedOnStock": "no",
      "industry": null,
      "servicesDescription": null,
      "website": "fiberpay.pl",
      "createdAt": "2023-11-20T16:38:11.000000Z",
      "registrationCountry": "PL",
      "companyIdentifier": null,
      "pkdCodes": [
        {
          "code": "z92xcphmdb5r",
          "pkdCode": "58.29.Z",
          "pkdName": "DZIAŁALNOŚĆ WYDAWNICZA W ZAKRESIE POZOSTAŁEGO OPROGRAMOWANIA",
          "mainPkd": false
        },
        {
          "code": "d8s5qfku9rn4",
          "pkdCode": "62.01.Z",
          "pkdName": "DZIAŁALNOŚĆ ZWIĄZANA Z OPROGRAMOWANIEM",
          "mainPkd": false
        }
      ],
      "mainPkd": {
        "code": "ce1byqmfnr5x",
        "pkdCode": "64.99.Z",
        "pkdName": "POZOSTAŁA FINANSOWA DZIAŁALNOŚĆ USŁUGOWA, GDZIE INDZIEJ NIESKLASYFIKOWANA, Z WYŁĄCZENIEM UBEZPIECZEŃ I FUNDUSZÓW EMERYTALNYCH",
        "mainPkd": true
      }
    },
    "addresses": [
      {
        "code": "53m8jqvhcsa4",
        "type": "business_address",
        "country": "PL",
        "city": "Warszawa",
        "street": "Grzybowska",
        "houseNumber": "4",
        "flatNumber": "106",
        "postalCode": "00-131",
        "createdAt": "2023-11-20T16:38:11.000000Z"
      }
    ],
    "contacts": [
      {
        "code": "et28pr6cqx5s",
        "type": "company",
        "emailAdress": "info@fiberpay.pl",
        "phoneCountry": "48",
        "phoneNumber": "123123123",
        "createdAt": "2023-11-20T16:38:11.000000Z"
      }
    ],
    "creationIp": null,
    "creationUserAgent": null
  }
}
```

### GET /parties

Zwraca podmioty utworzone przez użytkownika.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": [
    {
      "code": "9hf2b4ku3xas",
      "status": "active",
      "type": "company",
      "fullName": "FiberPay",
      "firstName": null,
      "lastName": null,
      "companyName": "FiberPay",
      "personalIdentityNumber": null,
      "taxIdNumber": "7010634566"
    },
    {
      "code": "cht8z7r24pq5",
      "status": "active",
      "type": "sole_proprietorship",
      "fullName": "Usługi programistyczne | Jan Kowalski",
      "firstName": "Jan",
      "lastName": "Kowalski",
      "companyName": "Usługi programistyczne",
      "personalIdentityNumber": "99120234518",
      "taxIdNumber": "3765151981"
    },
    {
      "code": "79w46ncruvjg",
      "status": "active",
      "type": "individual",
      "fullName": "Jan Kowalski",
      "firstName": "Jan",
      "lastName": "Kowalski",
      "companyName": null,
      "personalIdentityNumber": "09271573233",
      "taxIdNumber": null
    }
  ]
}
```


### GET /parties/{code}

Pobranie szczegółów danego podmiotu.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": {
    "code": "yqx7rwu59vbc",
    "type": "sole_proprietorship",
    "status": "active",
    "riskStatus": "pending",
    "riskExplanation": "Ryzyko na etapie oszacowania",
    "riskStatusChangedBy": "",
    "createdByName": "Adam",
    "references": "qwerty",
    "economicRelationStartDate": "2023-11-14",
    "entity": {
      "code": "stzyha1j76fm",
      "firstName": "Jan",
      "lastName": "Kowalski",
      "personalIdentityNumber": "99120234518",
      "documentType": "passport",
      "documentNumber": "aze123123",
      "documentIssueCountry": "PL",
      "documentExpirationDate": "2025-05-08",
      "withoutExpirationDate": false,
      "citizenship": "PL",
      "birthCity": "Warszawa",
      "birthCountry": "PL",
      "politicallyExposed": "no",
      "politicallyExposedCoworker": "no",
      "politicallyExposedFamily": "yes",
      "createdAt": "2023-11-20T16:49:44.000000Z",
      "birthDate": null,
      "employmentType": null,
      "soleProprietorship": {
        "code": "w6mx82nj7k1q",
        "tradeNames": [
          "FiberPay",
          "SystemAML"
        ],
        "companyName": "Usługi programistyczne",
        "taxIdNumber": "3765151981",
        "nationalBusinessRegistryNumber": "632702201",
        "createdAt": "2023-11-20T16:49:44.000000Z",
        "registrationCountry": "PL",
        "companyIdentifier": null,
        "pkdCodes": [
          {
            "code": "cf4jgdpbhayn",
            "pkdCode": "01.15.Z",
            "pkdName": "Uprawa tytoniu",
            "mainPkd": false
          }
        ],
        "mainPkd": {
          "code": "c7rdjge2zqs8",
          "pkdCode": "01.12.Z",
          "pkdName": "Uprawa ryżu",
          "mainPkd": true
        }
      }
    },
    "addresses": [
      {
        "code": "nm84r15aq6z2",
        "type": "forwarding_address",
        "country": "PL",
        "city": "Warszawa",
        "street": "Grzybowska",
        "houseNumber": "4",
        "flatNumber": "106",
        "postalCode": "00-131",
        "createdAt": "2023-11-20T16:49:44.000000Z"
      },
      {
        "code": "uv63gb9h57wc",
        "type": "business_address",
        "country": "PL",
        "city": "Warszawa",
        "street": "Grzybowska",
        "houseNumber": "4",
        "flatNumber": "106",
        "postalCode": "00-131",
        "createdAt": "2023-11-20T16:49:44.000000Z"
      },
      {
        "code": "ewrjvy2b17f9",
        "type": "accommodation_address",
        "country": "PL",
        "city": "Warszawa",
        "street": "Grzybowska",
        "houseNumber": "4",
        "flatNumber": "106",
        "postalCode": "00-131",
        "createdAt": "2023-11-20T16:49:44.000000Z"
      }
    ],
    "contacts": [
      {
        "code": "uema7tp12b8c",
        "type": "personal",
        "emailAdress": "info@fiberpay.pl",
        "phoneCountry": "48",
        "phoneNumber": "123123123",
        "createdAt": "2023-11-20T16:49:44.000000Z"
      },
      {
        "code": "haf6b4vgsz7u",
        "type": "company",
        "emailAdress": "info@fiberpay.pl",
        "phoneCountry": "48",
        "phoneNumber": "123123123",
        "createdAt": "2023-11-20T16:49:44.000000Z"
      }
    ],
    "creationIp": null,
    "creationUserAgent": null
  }
}
```

### DELETE /parties/{code}

Usunięcie podmiotu wskazanego kodem identyfikującym.


### POST /parties/{code}/beneficiaries

Dodanie beneficjenta rzeczywistego do podmiotu typu company. Parametry żądania:

| Parametr                     | Wymagane | Opis                                                                       |
| ---------------------------- | -------- | -------------------------------------------------------------------------  |
| **directRights**             | NIE      | Bezpośrednie uprawnienia                                                   |
| **ownedSharesAmount**        | NIE      | Liczba posiadanych udziałów                                                |
| **ownedSharesUnit**          | NIE      | Jednostka posiadanych udziałów ('%' lub 'PLN')                             |
| **directRightsPrivilegeType**           | NIE      | Rodzaj uprzywilejowania                                         |
| **directRightsPrivilegeDescription**    | NIE      | Opis uprzywilejowania                                           |
| **indirectRights**           | NIE      | Pośrednie uprawnienia                                                      |
| **otherRights**              | NIE      | Inne uprawnienia                                                           |
| **otherRightsDescription**   | NIE      | Opis uprawnień                                                             |
| **additionalInformation**    | TAK      | Dodatkowe informacje, nie są wymagane, o ile przynajmniej jeden z parametrów (directRights, ownedSharesAmount, ownedSharesUnit, directRightsPrivilegeType, indirectRights, otherRights, otherRightsDescription) posiada wartość |
| **firstName**                | TAK      | Imię beneficjenta                                                          |
| **lastName**                 | TAK      | Nazwisko beneficjenta                                                      |
| **personalIdentityNumber**   | TAK      | Numer PESEL beneficjenta (w przypadku braku numeru PESEL wymagany jest parametr birthDate) |
| **documentType**             | NIE      | Rodzaj dokumentu                                                           |
| **documentNumber**           | NIE      | Numer dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                                                             |
| **documentIssueCountry**     | NIE      | Kraj wydania dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                       |
| **documentExpirationDate**   | NIE      | Termin ważności dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                                                  |
| **withoutExpirationDate**    | NIE      | Informacja czy dokument beneficjenta jest bezterminowy (bool) jest wymagane kiedy jest podany jakikolwiek documentType i dokument jest bezterminowy              |
| **birthDate**                | TAK      | Data urodzenia (wymagana jeśli nie ma numeru PESEL)                        |
| **birthCity**                | NIE      | Miejsce urodzenia                                                      |
| **citizenship**              | NIE      | Obywatelstwo (kod kraju w standardzie ISO)                                 |
| **birthCountry**             | NIE      | Kraj urodzenia                                                             |
| **politicallyExposed**       | TAK      | Informacja czy beneficjent jest eksponowany politycznie (bool)             |
| **politicallyExposedFamily**     | TAK     | Informacja czy beneficjent jest rodziną osoby eksponowanej politycznie ('yes' lub 'no')                  |
| **politicallyExposedCoworker**   | TAK     | Informacja czy beneficjent jest bliskim współpracownikiem osoby eksponowanej politycznie ('yes' lub 'no')|
| **accommodationAddress**     | NIE      | Obiekt zawierający adres zamieszkania (opis struktury w punkcie "a) adres")|

#### Przykładowe dane do dodania beneficjenta rzeczywistego:

```json
{
  "birthCountry": "FR",
  "directRights": "Ford",
  "birthCity": "Paryż",
  "citizenship": "PL",
  "documentNumber": "PVL852925",
  "documentType": "id_card",
  "documentIssueCountry": "PL",
  "firstName": "Jan",
  "lastName": "Bożek",
  "ownedSharesAmount": "5",
  "ownedSharesUnit": "%",
  "personalIdentityNumber": "65122666817",
  "politicallyExposed": "no",
  "politicallyExposedCoworker":"no",
  "politicallyExposedFamily":"no",
  "withoutExpirationDate": false,
  "accommodationAddress": {
    "country": "PL",
    "city": "Krakow",
    "street": "Kazimierza",
    "houseNumber": "41",
    "flatNumber": "10",
    "postalCode": "20-131"
  }
}
```

#### Przykładowa odpowiedź serwera:

- **STATUS 201 CREATED**

```json
{
  "data": {
    "code": "wn6hmp7e2x98",
    "ownedSharesAmount": "5.00",
    "ownedSharesUnit": "%",
    "description": null,
    "beneficiary": {
      "individualEntity": {
        "code": "yrfp7ug51vwn",
        "firstName": "Jan",
        "lastName": "Bożek",
        "personalIdentityNumber": "65122666817",
        "documentType": "Dowód osobisty",
        "documentNumber": "PVL852925",
        "documentIssueCountry": "PL",
        "documentExpirationDate": null,
        "withoutExpirationDate": false,
        "citizenship": "PL",
        "birthCity": "Paryż",
        "birthCountry": "FR",
        "politicallyExposed": "no",
        "politicallyExposedCoworker": "not_defined",
        "politicallyExposedFamily": "not_defined",
        "createdAt": "2023-08-24T15:51:27.000000Z",
        "birthDate": null
      }
    },
    "company": {
      "legalEntity": {
        "code": "cga7z8hf3j6r",
        "companyName": "FiberPay",
        "tradeName": "FiberPay",
        "taxIdNumber": "7010634566",
        "nationalBusinessRegistryNumber": "147302566",
        "nationalCourtRegistryNumber": "0000512707",
        "businessActivityForm": "stock_company",
        "industry": null,
        "servicesDescription": null,
        "website": "fiberpay.pl",
        "createdAt": "2023-08-24T15:48:19.000000Z",
        "registrationCountry": "PL",
        "companyIdentifier": null,
        "pkdCodes": [
          {
            "code": "gf218nj4y5rs",
            "pkdCode": "58.29.Z",
            "pkdName": "DZIAŁALNOŚĆ WYDAWNICZA W ZAKRESIE POZOSTAŁEGO OPROGRAMOWANIA",
            "mainPkd": false
          },
          {
            "code": "qn6vrazgymbp",
            "pkdCode": "62.01.Z",
            "pkdName": "DZIAŁALNOŚĆ ZWIĄZANA Z OPROGRAMOWANIEM",
            "mainPkd": false
          }
        ],
        "mainPkd": {
          "code": "mfxw2yqt7zed",
          "pkdCode": "64.99.Z",
          "pkdName": "POZOSTAŁA FINANSOWA DZIAŁALNOŚĆ USŁUGOWA, GDZIE INDZIEJ NIESKLASYFIKOWANA, Z WYŁĄCZENIEM UBEZPIECZEŃ I FUNDUSZÓW EMERYTALNYCH",
          "mainPkd": true
        }
      },
      "addresses": [
        {
          "code": "9fn2jukbtv7q",
          "type": "business_address",
          "country": "PL",
          "city": "Warszawa",
          "street": "Grzybowska",
          "houseNumber": "4",
          "flatNumber": "106",
          "postalCode": "00-131",
          "createdAt": "2023-08-24T15:48:19.000000Z"
        }
      ],
      "contacts": [
        {
          "code": "5b1gk4jpe9fn",
          "type": "company",
          "emailAdress": "info@fiberpay.pl",
          "phoneCountry": "48",
          "phoneNumber": "123123123",
          "createdAt": "2023-08-24T15:48:19.000000Z"
        }
      ]
    }
  }
}
```

### GET /parties/{code}/beneficiaries

Pobranie beneficjentów rzeczywistych wskazanego kodem podmiotu typu company.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": [
    {
      "code": "vpen5qj8rbkx",
      "ownedSharesAmount": "45.00",
      "ownedSharesUnit": "%",
      "additionalInformation": null,
      "directRights": "ABB",
      "directRightsPrivilegeType": null,
      "directRightsPrivilegeDescription": null,
      "indirectRights": null,
      "otherRights": null,
      "otherRightsDescription": null,
      "individualEntity": {
        "code": "r2v3baphk649",
        "firstName": "Jan",
        "lastName": "Kowalski",
        "personalIdentityNumber": "64091098920",
        "documentType": "id_card",
        "documentNumber": "aze123123",
        "documentIssueCountry": "PL",
        "documentExpirationDate": null,
        "withoutExpirationDate": false,
        "citizenship": "PL",
        "birthCity": "Warszawa",
        "birthCountry": "PL",
        "politicallyExposed": "no",
        "politicallyExposedCoworker": "not_defined",
        "politicallyExposedFamily": "not_defined",
        "createdAt": "2023-08-24T15:48:19.000000Z",
        "birthDate": null
      },
      "accommodationAddress": null
    }
  ]
}
```

W przypadku gdy podmiot nie posiada dodanych beneficjentów rzeczywistych zwracana tablica jest pusta

### DELETE /beneficiaries/{code}

Usunięcie beneficjenta rzeczywistego wskazanego kodem identyfikującym.


### POST /parties/{code}/boardmembers

Dodanie reprezentanta do podmiotu typu osoba prawna (company). Parametry żądania:


| Parametr                     | Wymagane | Opis                                                                       |
| ---------------------------- | -------- | -------------------------------------------------------------------------- |
| **firstName**                | TAK      | Imię reprezentanta                                                         |
| **lastName**                 | TAK      | Nazwisko reprezentanta                                                     |
| **personalIdentityNumber**   | TAK      | Numer PESEL reprezentanta  (w przypadku braku numeru PESEL wymagany jest parametr personalIdentifier) |
| **documentType**             | TAK      | Rodzaj dokumentu (nie jest wymagany jeśli nie ma numeru PESEL)             |
| **documentNumber**           | NIE      | Numer dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)           |
| **documentIssueCountry**     | NIE      | Kraj wydania dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                       |
| **documentExpirationDate**   | NIE      | Termin ważności dokumentu (jest wymagane kiedy jest podany jakikolwiek documentType)                                                |
| **personalIdentifier**       | NIE      | Numer identyfikujący reprezentanta  (wymagany jeśli nie ma numeru PESEL) |
| **birthDate**                | NIE      | Data urodzenia (wymagana jeśli nie ma numeru PESEL)                        |
| **birthCountry**             | NIE      | Kraj urodzenia (wymagany jeśli nie ma numeru PESEL)                        |
| **citizenship**              | NIE      | Obywatelstwo (kod kraju w standardzie ISO)                                 |
| **birthCity**                | NIE      | Miejsce urodzenia                                                      |
| **withoutExpirationDate**    | NIE      | Informacja czy dokument posiada datę ważności (bool) jest wymagane kiedy jest podany jakikolwiek documentType i dokument jest bezterminowy                       |
| **references**               | NIE      | Referencje własne                                                          |
| **roleType**                 | TAK      | Pełniona rola. Aktualnie wspierane: president, board_member , proxy, other |
| **description**              | TAK      | Opis pełnione roli (wymagane jeśli roleType ma wartość other)              |
| **politicallyExposed**       | TAK      | Informacja czy reprezentant jest eksponowany politycznie (bool)             |
| **politicallyExposedFamily**     | TAK     | Informacja czy reprezentant jest rodziną osoby eksponowanej politycznie ('yes' lub 'no')                  |
| **politicallyExposedCoworker**   | TAK     | Informacja czy reprezentant jest bliskim współpracownikiem osoby eksponowanej politycznie ('yes' lub 'no')|

#### Przykładowe dane do dodania reprezentanta:

```json
{
  "birthCity": "Warszawa",
  "birthDate": "2002-01-01",
  "birthCountry": "PL",
  "citizenship": "PL",
  "description": "Prezes",
  "documentNumber": "XIK941595",
  "documentType": "id_card",
  "documentIssueCountry": "PL",
  "firstName": "Jan",
  "lastName": "Nowak",
  "personalIdentityNumber": "97120824889",
  "politicallyExposed": "yes",
  "politicallyExposedCoworker":"no",
  "politicallyExposedFamily":"no",
  "roleType" : "proxy",
  "withoutExpirationDate": false,
}
```

#### Przykładowa odpowiedź serwera:

- **STATUS 201 CREATED**

```json
{
  "data": {
    "code": "9c281v6rkzty",
    "description": "Prezes",
    "roleType": "proxy",
    "individualEntity": {
      "code": "qmce687su31n",
      "firstName": "Jan",
      "lastName": "Nowak",
      "personalIdentityNumber": "97120824889",
      "documentType": "id_card",
      "documentNumber": "XIK941595",
      "documentIssueCountry": "PL",
      "documentExpirationDate": null,
      "withoutExpirationDate": false,
      "citizenship": "PL",
      "birthCity": "Warszawa",
      "birthCountry": "PL",
      "politicallyExposed": "yes",
      "politicallyExposedCoworker": "not_defined",
      "politicallyExposedFamily": "not_defined",
      "createdAt": "2023-11-20T16:42:39.000000Z",
      "birthDate": null
    },
    "company": {
      "legalEntity": {
        "code": "jx86zfnrhks9",
        "companyName": "FiberPay",
        "tradeNames": [
          "FiberPay"
        ],
        "taxIdNumber": "7010634566",
        "nationalBusinessRegistryNumber": "147302566",
        "nationalCourtRegistryNumber": "0000512707",
        "businessActivityForm": "stock_company",
        "industry": null,
        "servicesDescription": null,
        "website": "fiberpay.pl",
        "createdAt": "2023-11-20T16:39:46.000000Z",
        "registrationCountry": "PL",
        "companyIdentifier": null,
        "pkdCodes": [
          {
            "code": "3puhe59mv87k",
            "pkdCode": "58.29.Z",
            "pkdName": "DZIAŁALNOŚĆ WYDAWNICZA W ZAKRESIE POZOSTAŁEGO OPROGRAMOWANIA",
            "mainPkd": false
          },
          {
            "code": "5st34zgp2db7",
            "pkdCode": "62.01.Z",
            "pkdName": "DZIAŁALNOŚĆ ZWIĄZANA Z OPROGRAMOWANIEM",
            "mainPkd": false
          }
        ],
        "mainPkd": {
          "code": "vsuxzfwbhyk1",
          "pkdCode": "64.99.Z",
          "pkdName": "POZOSTAŁA FINANSOWA DZIAŁALNOŚĆ USŁUGOWA, GDZIE INDZIEJ NIESKLASYFIKOWANA, Z WYŁĄCZENIEM UBEZPIECZEŃ I FUNDUSZÓW EMERYTALNYCH",
          "mainPkd": true
        }
      },
      "addresses": [
        {
          "code": "38z5gseqyp6t",
          "type": "business_address",
          "country": "PL",
          "city": "Warszawa",
          "street": "Grzybowska",
          "houseNumber": "4",
          "flatNumber": "106",
          "postalCode": "00-131",
          "createdAt": "2023-11-20T16:39:46.000000Z"
        }
      ],
      "contacts": [
        {
          "code": "ers27txw6ync",
          "type": "company",
          "emailAdress": "info@fiberpay.pl",
          "phoneCountry": "48",
          "phoneNumber": "123123123",
          "createdAt": "2023-11-20T16:39:46.000000Z"
        }
      ]
    }
  }
}
```

### GET /parties/{code}/boardmembers

Pobranie beneficjentów rzeczywistych wskazanego kodem podmiotu typu company.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": [
    {
      "code": "r7jxq51gazu3",
      "description": "Prezes spółki",
      "roleType": "president",
      "individualEntity": {
        "code": "ux9e7pfza6jq",
        "firstName": "Jan",
        "lastName": "Kowalski",
        "personalIdentityNumber": "31111161119",
        "documentType": "id_card",
        "documentNumber": "aze123123",
        "documentIssueCountry": "PL",
        "documentExpirationDate": null,
        "withoutExpirationDate": false,
        "citizenship": "PL",
        "birthCity": "Warszawa",
        "birthCountry": "PL",
        "politicallyExposed": "no",
        "politicallyExposedCoworker": "not_defined",
        "politicallyExposedFamily": "not_defined",
        "createdAt": "2023-11-14T15:11:27.000000Z",
        "birthDate": null,
      }
    }
  ]
}
```
W przypadku gdy podmiot nie posiada dodanych reprezentantów zwracana tablica jest pusta

### DELETE /boardmembers/{code}

Usunięcie reprezentanta wskazanego kodem identyfikującym.

## POST /transactions

Utworzenie nowej transakcji. Parametry żądania:

| Parametr                  | Wymagane | Opis                                                     |
| ------------------------- | -------- | -------------------------------------------------------- |
| **type**                  | TAK      | Typ transakcji. Aktualnie wspierane: buyer, seller, transfer, other, seller_crypto, buyer_crypto, exchange_fiat |
| **status**                | TAK      | Status transakcji. Aktualnie wspierane: draft, in_acceptance, accepted, cancelled |
| **occasionalTransaction** | TAK      | Informacja czy transakcja jest okazjonalna (bool)        |

Jeśli transakcja jest oznaczona jako okazjonalna wymagane są następujące parametry:

| Parametr          | Wymagane | Opis                                                       |
| ----------------- | -------- | ---------------------------------------------------------- |
| **amount**        | TAK      | Kwota transakcji                                           |
| **currency**      | TAK      | Waluta transakcji                                          |
| **bookedAt**      | TAK      | Data zaksięgowania transakcji                              |
| **paymentMethod** | TAK      | Sposób płatności                                           |
| **paymentMethodOther** | NIE      | Sposób płatności (wymagany kiedy paymentMethod === other)                                           |
| **title**         | TAK      | Tytuł transakcji                                           |
| **location**      | NIE      | Kraj w którym została przeprowadzona transakcja            |
| **description**   | NIE      | Opis transakcji (przedmiot transakcji, komentarz itd.)     |
| **references**    | NIE      | Referencje własne                                          |
| **createdByName** | NIE      | Dane osoby wprowadzającej wpis                             |
| **entities**      | NIE      | Tablica obiektów z danymi stron transakcji, wymagane w zależności od occasionalTransaction i typu transakcji |


Obiekt pojedynczej strony transakcji powinien zawierać poniższe parametry:

| Parametr                | Wymagane | Opis                                                                 |
| ----------------------- | -------- | -------------------------------------------------------------------- |
| **type**                | TAK      | Typ strony transakcji. Aktualnie wspierane: receiver, seller, buyer, payer, buyer_crypto, seller_crypto, exchange_fiat_client, other|
| **typeOther**           | NIE      | Typ strony transakcji (wymagany jeśli type === other)|
| **partyCode**           | NIE      | Kod podmiotu strony transakcji                                       |
| **firstName**           | NIE      | Imię strony transakcji (wymagany jeśli nie jest podany kod strony transakcji)          |
| **lastName**            | NIE      | Nazwisko strony transakcji (wymagany jeśli nie jest podany kod strony transakcji)      |
| **companyName**         | NIE      | Nazwa firmy strony transakcji (wymagany jeśli nie jest podany kod strony transakcji)     |
| **description**         | NIE      | Opis strony transakcji                                               |
| **amount**              | NIE      | Kwota transakcji                                           |
| **currency**            | NIE      | Waluta transakcji                                          |
| **currencyCustom**      | NIE      | Waluta transakcji kiedy waluta nie istnieje na liście, w transakcjach typu buyer_crypto, seller_crypto, exchange_fiat |
| **currencyOther**       | NIE      | Nazwa waluty kiedy nie istnieje na liście, w transakcjach typu buyer_crypto, seller_crypto, exchange_fiat |
| **currencyType**        | NIE      | Rodzaj waluty w transakcjach typu buyer_crypto, seller_crypto, exchange_fiat  |
| **iban**                | NIE      | Numer iban strony transakcji (wymagany jeśli paymentMethod === bank_transfer) |
| **txId**                | NIE      | Numer identyfikacyjny transakcji waluty wirtualnej w sieci blockchain, w transakcjach typu buyer_crypto, seller_crypto  |
| **cryptoAddress**       | NIE      | Adres portfela waluty wirtualnej, w transakcjach typu buyer_crypto, seller_crypto |
| **ip**                  | NIE      | Adres IP strony transakcji  |
| **isEntityAWalletOwner**  | NIE*      | Informacja czy podmiot wskazany w transakcji jest faktycznym właścicielem portfela kryptowalutowego użytego w transakcji. Przyjmuje wartości 'yes', 'no'. *Wymagane dla transakcji typu 'buyer_crypto', 'seller_crypto'  |
| **addressDataFromRelatedParty** | NIE* | Informacja czy dane adresowe właściciela portfela kryptowalutowego są takie same jak wskazanym podmiocie. Przyjmuje wartości 'yes', 'no'. Pole jest wymagane gdy wskazany jest podmiot, Wykorzystywane tylko przy transakcjach typu 'buyer_crypto', 'seller_crypto' |
| **isVASPEntity** | NIE | Informacja czy portfel kryptowalutowy jest hostowany przez VASP. Przyjmuje wartości 'yes', 'no'. Wykorzystywane tylko przy transakcjach typu 'buyer_crypto', 'seller_crypto'|
| **vaspName** | NIE* | Nazwa docelowego VASP. *Wymagane dla transakcji typu 'buyer_crypto', 'seller_crypto' gdy isVASPEntity === 'yes' |
| **vaspLeix** | NIE* | VASP LEIX. *Co najmniej jedno z pól vasp jest wymagane dla transakcji typu 'buyer_crypto', 'seller_crypto' gdy isVASPEntity === 'yes' |
| **vaspRaid** | NIE* | VASP RAID. *Co najmniej jedno z pól vasp jest wymagane dla transakcji typu 'buyer_crypto', 'seller_crypto' gdy isVASPEntity === 'yes' |
| **vaspTxid** | NIE* | VASP TXID. *Co najmniej jedno z pól vasp jest wymagane dla transakcji typu 'buyer_crypto', 'seller_crypto' gdy isVASPEntity === 'yes' |
| **vaspMisc** | NIE* | VASP MISC. *Co najmniej jedno z pól vasp jest wymagane dla transakcji typu 'buyer_crypto', 'seller_crypto' gdy isVASPEntity === 'yes' |
| **vaspCustomerId** | NIE* | Id klienta VASP. *Co najmniej jedno z pól vasp jest wymagane dla transakcji typu 'buyer_crypto', 'seller_crypto' gdy isVASPEntity === 'yes' |
| **walletOwnerData** | NIE* | Obiekt reprezentujące dane właściciela portfela kryptowalutowego. Wymagane dla transakcji typu 'buyer_crypto', 'seller_crypto' gdy isEntityAWalletOwner === 'no'|

Struktura obiektu walletOwnerData:
| Parametr                | Wymagane | Opis                                                                 |
| ----------------------- | -------- | -------------------------------------------------------------------- |
| **partyType**                | TAK      | Rodzaj danych właściciela portfela kryptowalutowego. Aktualnie wspierane 'individual', 'sole_proprietorship', 'company' |
| **firstName**                | NIE*     | Imię. Wymagane gdy partyType to 'individual' lub 'sole_proprietorship' |
| **lastName**                 | NIE*     | Nazwisko. Wymagane gdy partyType to 'individual' lub 'sole_proprietorship' |
| **companyName**              | NIE*     | Nazwa firmy. Wymagane gdy partyType to 'company' lub 'sole_proprietorship' |
| **postalCode**               | NIE*     | Kod pocztowy. Wymagane gdy 'isEntityAWalletOwner' === 'no' lub 'addressDataFromRelatedParty' === 'no' |
| **country**                  | NIE*     | Kraj. Wymagane gdy 'isEntityAWalletOwner' === 'no' lub 'addressDataFromRelatedParty' === 'no' |
| **city**                     | NIE*     | Miejscowość. Wymagane gdy 'isEntityAWalletOwner' === 'no' lub 'addressDataFromRelatedParty' === 'no' |
| **street**                   | NIE*     | Ulica. Wymagane gdy 'isEntityAWalletOwner' === 'no' lub 'addressDataFromRelatedParty' === 'no' |
| **houseNumber**              | NIE*     | Numer domu. Wymagane gdy 'isEntityAWalletOwner' === 'no' lub 'addressDataFromRelatedParty' === 'no' |
| **flatNumber**               | NIE     | Numer mieszkania. |
| **taxIdNumber**              | NIE*     | Numer identyfikacji podatkowej. Wymagane gdy partyType to 'company' lub 'sole_proprietorship' i 'isEntityAWalletOwner' === 'no' lub 'addressDataFromRelatedParty' === 'no' |
| **taxIdCountry**             | NIE*     | Kraj identyfikacji podatkowej. Wymagane podany jest taxIdNumber|

Dla transakcji typu buyer wymagane jest podanie strony transakcji o typie seller.
Dla transakcji typu seller wymagane jest podanie strony transakcji o typie buyer.
Dla transakcji typu transfer wymagane jest podanie stron transakcji o typach payer oraz receiver.
Dla transakcji typu seller_crypto wymagane jest podanie strony transakcji o typie buyer_crypto.
Dla transakcji typu buyer_crypto wymagane jest podanie strony transakcji o typie seller_crypto.
Dla transakcji typu other wymagane jest podanie strony transakcji o typach receiver, seller, buyer lub payer.
Dla transakcji typu exchange_fiat wymagane jest podanie strony transakcji o typie exchange_fiat_client.
#### Przykładowe dane do utworzenia transakcji:

```json
{
    "type": "buyer",
    "status": "accepted",
    "amount": "100.00",
    "currency": "PLN",
    "paymentMethod": "cash",
    "location": "PL",
    "bookedAt": "2024-05-07T13:03:48.000000Z",
    "title": "testowa transakcja",
    "entities": [
            {
                "type": "seller",
                "firstName": "Adam",
                "lastName": "Kowalski",
            }
    ],
    "createdAt": "2024-05-07T13:04:33.000000Z",
    "occasionalTransaction": false,
}
```

#### Przykładowa odpowiedź serwera:

- **STATUS 201 CREATED**

```json
{
    "data": {
        "code": "8c2vdbhrn5zm",
        "type": "buyer",
        "status": "accepted",
        "amount": "100.00",
        "currency": "PLN",
        "paymentMethod": "cash",
        "paymentMethodOther": null,
        "location": "PL",
        "bookedAt": "2024-05-07T11:03:48.000000Z",
        "description": null,
        "title": "testowa transakcja",
        "entities": [
            {
                "code": "vpgsq76murxz",
                "partyCode": null,
                "type": "seller",
                "typeOther": null,
                "description": null,
                "firstName": "Adam",
                "lastName": "Kowalski",
                "companyName": null,
                "currency": null,
                "currencyType": null,
                "currencyOther": null,
                "currencyCustom": false,
                "amount": null,
                "iban": null,
                "ip": null,
                "txId": null,
                "cryptoAddress": null,
            }
        ],
        "references": null,
        "createdAt": "2024-05-15T09:30:00.000000Z",
        "occasionalTransaction": false,
        "createdByName": null
    }
}
```

### GET /transactions

Zwraca transakcje utworzone przez użytkownika.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": [
    {
      "mainEntityType": "buyer",
      "mainEntityFirstName": "Jan",
      "mainEntityLastName": "Kowalski",
      "mainEntityCompanyName": "",
      "code": "c1ehu5my3s97",
      "type": "vender",
      "status": "accepted",
      "description": null,
      "title": "transakcja z klienta",
      "amount": "780.00",
      "currency": "PLN"
    },
    {
      "code": "td4r9v6weunk",
      "type": "vender",
      "status": "cancelled",
      "description": null,
      "title": "transakcja z klienta",
      "amount": "780.00",
      "currency": "PLN"
    },
    {
      "mainEntityType": "buyer",
      "mainEntityFirstName": "Jan",
      "mainEntityLastName": "Kowalski",
      "mainEntityCompanyName": null,
      "code": "q9hyv32w4b68",
      "type": "vender",
      "status": "accepted",
      "description": null,
      "title": "transakcja z klienta",
      "amount": "780.00",
      "currency": "PLN"
    }
  ]
}
```

### GET /transactions/{code}

Pobranie szczegółów danej transakcji.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": {
    "code": "c1ehu5my3s97",
    "type": "vender",
    "status": "accepted",
    "amount": "780.00",
    "currency": "PLN",
    "paymentMethod": "blik",
    "location": "PL",
    "bookedAt": "2023-09-10T08:10:10.000000Z",
    "description": null,
    "title": "transakcja testowa",
    "entities": [
      {
        "code": "rtkmyfbavnug",
        "partyCode": null,
        "type": "buyer",
        "description": "Testowa strona transakcji",
        "firstName": "Jan",
        "lastName": "Kowalski",
        "companyName": "",
        "amount": null,
        "currency": null,
        "paymentMethod": null,
        "iban": "",
        "ip": null,
        "bookedAt": null
      }
    ],
    "references": "qwerty",
    "createdAt": "2023-09-22T08:36:20.000000Z",
    "occasionalTransaction": false,
    "createdByName": "Tester"
  }
}
```


### DELETE /transactions/{code}

Usunięcie transakcji wskazanej kodem identyfikującym.

### POST /history-events

Tworzenie nowego zdarzenia w systemie. Parametry żądania:

| Parametr            | Wymagane | Opis                                                          |
| ------------------- | -------- | ------------------------------------------------------------- |
| **description**     | TAK      | Opis zdarzenia                                                |
| **significance**    | TAK      | Ważność zdarzenia. Aktualnie wspierane: info, warning, urgent |
| **party**           | NIE      | Obiekt zawierający kod powiązanego podmiotu                   |
| **transaction**     | NIE      | Obiekt zawierający kod powiązanej transakcji                  |
| **type**            | NIE      | Typ zgłoszenia. Aktualnie wspierane : user, system            |
| **occursAt**        | TAK      | Data wystąpienia zdarzenia                                    |
| **createdByName**   | NIE      | Osoba tworząca zdarzenie                                      |

Struktura obiektu party:

| Parametr        | Wymagane | Opis                              |
| --------------- | -------- | --------------------------------- |
| **code**        | NIE      | Kod powiązanego podmiotu          |


Struktura obiektu transaction:

| Parametr        | Wymagane | Opis                              |
| --------------- | -------- | --------------------------------- |
| **code**        | NIE      | Kod powiązanej transakcji         |



#### Przykładowe dane do utworzenia zdarzenia:

```json
{
  "description": "zdarzenie testowe",
  "significance": "urgent",
  "occursAt": "2025-08-03 18:42:40",
}
```

#### Przykładowa odpowiedź serwera:

- **STATUS 201 CREATED**

```json
{
  "data": {
    "code": "ckfgq8z6y2s5",
    "significance": "urgent",
    "description": "zdarzenie testowe",
    "type": "user",
    "party": null,
    "transaction": null,
    "createdByName": null,
    "hasComments": false,
    "occursAt": "2025-08-03T16:42:40.000000Z"
  }
}
```

### GET /history-events

Zwraca zdarzenia przypisane do użytkownika.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": [
    {
      "code": "ckfgq8z6y2s5",
      "significance": "urgent",
      "description": "zdarzenie testowe",
      "type": "user",
      "party": null,
      "transaction": null,
      "createdByName": null,
      "hasComments": false,
      "occursAt": "2025-08-03 18:42:40"
    },
    {
      "code": "m5u9p4zgr2qy",
      "significance": "info",
      "description": "fsdf",
      "type": "user",
      "party": null,
      "transaction": null,
      "createdByName": null,
      "hasComments": false,
      "occursAt": "2023-11-14 16:39:59"
    },
  ]
}
```

Jeśli użytkownik nie posiada żadnych zdarzeń zwracany jest adekwatny komunikat ze statusem 200.

### GET /history-events/{code}

Zwraca zdarzenie o podanym identyfikatorze wraz z liczbą komentarzy.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": {
    "code": "ame15yfgvhzk",
    "partyCode": "7u2nbzjx83dg",
    "transactionCode": null,
    "description": "89b88",
    "type": "user",
    "significance": "warning",
    "occursAt": "2023-08-03 18:42:40",
    "createdByName": null,
    "commentsAmount": 2
  }
}
```

Jeśli zdarzenie nie posiada przypisanych komentarzy zmienna zwracana przy kluczu "commentsAmount" równa się 0

### DELETE /history-events/{code}

Usunięcie zdarzenia wskazanego kodem identyfikującym.

### POST /comments

Tworzenie nowego komentarza do zdarzenia w systemie. Parametry żądania:

| Parametr      | Wymagane | Opis                                                           |
| ------------- | -------- | -------------------------------------------------------------- |
| **content**   | TAK      | Treść komentarza                                               |
| **eventCode** | TAK      | Identyfikator zdarzenia do którego będzie przypisany komentarz |
| **occursAt**  | TAK      | Data wystąpienia zdarzenia                                     |

#### Przykładowe dane do utworzenia komentarza:

```json
{
  "content": "testowy komentarz",
  "eventCode": "gcw1rhn39ufz",
  "occursAt": "2025-08-03 18:42:40",
 }

```

#### Przykładowa odpowiedź serwera:

- **STATUS 201 CREATED**

```json
{
  "data": {
    "code": "17zw82gyk3ma",
    "content": "testowy komentarz",
    "created": "2023-09-05T15:44:43.000000Z",
    "occursAt": "2025-08-03T16:42:40.000000Z"
  }
}
```

### GET /events/{code}/comments

Zwraca komentarze przypisane do zdarzenia.

### DELETE /comments/{code}

Usunięcie komentarza wskazanego kodem identyfikującym.

### GET /alerts

Pobranie alertów powiązanych z danym użytkownikiem.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": [
    {
      "code": "9u187g4y2fcq",
      "content": "alert testowy api",
      "type": "task",
      "status": "new",
      "partyCode": null,
      "transactionCode": null
    }
  ]
}
```

### GET /alerts/{code}

Pobranie szczegółów alertu wskazanego kodem.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": {
    "code": "9u187g4y2fcq",
    "content": "alert testowy api",
    "type": "task",
    "status": "new",
    "taskDone": false,
    "partyCode": null,
    "transactionCode": null,
    "createdAt": "2022-07-18T13:22:42.000000Z"
  }
}
```

### DELETE /alerts/{code}

Usunięcie alertu wskazanego kodem identyfikującym.

### POST /tasks

Tworzenie nowego zadania w systemie. Parametry żądania:

| Parametr            | Wymagane | Opis                                             |
| ------------------- | -------- | ------------------------------------------------ |
| **content**         | TAK      | Treść zadania                                    |
| **expirationDate**  | NIE      | Data przed którą zadanie powinno zostać wykonane |
| **alert**           | NIE      | Obiekt zawierający kod powiązanego alertu        |
| **party**           | NIE      | Obiekt zawierający kod powiązanego podmiotu      |
| **transaction**     | NIE      | Obiekt zawierający kod powiązanej transakcji     |
| **alertCode**       | NIE      | Kod alertu który będzie powiązany z zadaniem     |


Struktura obiektu party:

| Parametr        | Wymagane | Opis                              |
| --------------- | -------- | --------------------------------- |
| **code**        | NIE      | Kod powiązanego podmiotu          |


Struktura obiektu transaction:

| Parametr        | Wymagane | Opis                              |
| --------------- | -------- | --------------------------------- |
| **code**        | NIE      | Kod powiązanej transakcji         |

Struktura obiektu alert:

| Parametr        | Wymagane | Opis                              |
| --------------- | -------- | --------------------------------- |
| **code**        | NIE      | Kod powiązanej transakcji         |


#### Przykładowe dane do utworzenia zadania:

```json
{
  "content": "Utworzyć przykładowe zadanie do celów reprezentacyjnych w dokumentacji",
  "expirationDate": "2023-04-24",
}
```

#### Przykładowa odpowiedź serwera:

- **STATUS 201 CREATED**

```json
{
  "data": {
    "code": "dcsur627nf3w",
    "content": "Utworzyć przykładowe zadanie do celów reprezentacyjnych w dokumentacji",
    "status": null,
    "alert": null,
    "party": null,
    "transaction": null,
    "expirationDate": null,
    "createdAt": "2023-11-14T15:57:32.000000Z"
  }
}
```

### GET /tasks
Pobranie zadań powiązanych z danym użytkownikiem.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
  {
      "code": "94dwpaxk5rzy",
      "content": "Wymagane manualne ustawienie oceny ryzyka w podmiocie",
      "status": "new",
      "alert": {
        "code": "7fp9srh2vq16",
        "content": "Sprawdź dane"
      },
      "party": {
        "code": "pum7n95fbwqk",
        "firstName": "234",
        "lastName": "234"
      },
      "transaction": {
        "code": "wjnkx4dr2ag9",
        "title": "Opłata za kupno"
      },
      "expirationDate": null
    },
    {
      "code": "svxpezabyg9h",
      "content": "Przeprowadź środki bezpieczeństwa finansowego - do 2022-06-05 (Od zarejestrowania podmiotu minęło 870 dni)",
      "status": "done",
      "alert": null,
      "party": {
        "code": "s1vcm5ew9fd4",
        "firstName": "Jan",
        "lastName": "Kowalski",
      },
      "transaction": null,
      "expirationDate": null
    },
```

### GET /tasks/{code}
Pobranie szczegółów zadania wskazanego kodem.

- **STATUS 200 OK**

```json
{
  "data": {
    "code": "dcsur627nf3w",
    "content": "Utworzyć przykładowe zadanie do celów reprezentacyjnych w dokumentacji",
    "status": "new",
    "alert": null,
    "party": null,
    "transaction": null,
    "expirationDate": null,
    "createdAt": "2023-11-14T15:57:32.000000Z"
  }
}
```

### DELETE /tasks/{code}

Usunięcie zadania wskazanego kodem identyfikującym.

### POST /sanctions-lists/search

Sprawdzenie podanych danych na listach sankcyjnych. Parametry żądania:

| Parametr            | Wymagane | Opis                                                                         |
| ------------------- | -------- | ---------------------------------------------------------------------------- |
| **entityType**      | TAK      | Rodzaj przesyłanych danych. Aktualnie akceptowane: individual, company, any, crypto_address, email  |
| **name**            | NIE *    | Imię, nazwisko lub nazwa firmy (* Wymagane gdy entityType === any)           |
| **firstName**       | NIE *    | Imię (* Wymagane gdy entityType === individual)                              |
| **middleName**      | NIE      | Drugie oraz kolejne imiona                                                   |
| **lastName**        | NIE *    | Nazwisko (* Wymagane gdy entityType === individual)                          |
| **companyName**     | NIE *    | Nazwa firmy (* Wymagane gdy entityType === company)                          |
| **email**           | NIE *    | Adres email (* Wymagane gdy entityType === email)                            |
| **cryptoAddress**   | NIE *    | Adres portfela waluty wirtualnej (* Wymagane gdy entityType === crypto_address) |

#### Przykładowe dane do wyszukania osoby fizycznej (entityType === 'individual'):

```json
{
  "entityType": "individual",
  "firstName": "Wladimir",
  "lastName": "Putin"
}
```

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "isMatch": true,
  "code": "pwzcqczp6nwz",
  "matchedEntities": [
    {
      "listName": "sanctions_uk",
      "name": "Vladimir Vladimirovich PUTIN",
      "aliases": [
        "Vladimir Vladimirovich PUTIN",
        "Vladimir Putin",
        "владимир владимирович путин"
      ],
      "recordType": "individual",
      "sourceData": {
        "type": "individual",
        "addresses": "{\"Address\":{\"AddressLine6\":\"Moscow\",\"AddressCountry\":\"Russia\"}}",
        "unique_id": "RUS0251",
        "ofsi_group_id": "14196",
        "name_type": "PRIMARY NAME",
        "first_name": "Vladimir",
        "second_name": "Vladimirovich",
        "last_name": "PUTIN",
        "regime_name": "The Russia (Sanctions) (EU Exit) Regulations 2019",
        "designation_source": "UK",
        "sanctions_imposed": "Asset freeze|Trust Services Sanctions"
      }
    },
    {
      "listName": "eu_financial_sanctions",
      "name": "Vladimir PUTIN",
      "aliases": [
        "Влади́мир ПУ́ТИН",
        "Vladimir PUTIN",
        "Vladimir POUTINE",
        "Vladimir PUTIN"
      ],
      "recordType": "individual",
      "sourceData": {
        "entity_logical_id": "135909",
        "entity_eu_reference_number": "EU.7510.16",
        "entity_remark": "(Date of UN designation: 2022-02-25)",
        "entity_subject_type_classification_code": "person",
        "entity_regulation_number_title": "2022/332 (OJ L53)",
        "entity_regulation_publication_date": "2022-02-25",
        "entity_regulation_type": "amendment",
        "entity_regulation_programme": "UKR",
        "entity_regulation_publication_url": "https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=uriserv%3AOJ.L_.2022.053.01.0001.01.ENG&toc=OJ%3AL%3A2022%3A053%3ATOC"
      }
    }
  ]
}
```

Jeśli dane zostaną odnalezione zmienna **isMatch** przyjmuję wartość true (boolean) w przeciwnym wypadku przyjmuję wartość false (boolean)

### GET /sanctions/{code}/pdf

Pobranie raportu pdf zawierającego wynik wyszukiwania na listach sankcyjnych. Parametry żądania:

| Parametr      | Wymagane | Opis                                                           |
| ------------- | -------- | -------------------------------------------------------------- |
| **code**      | TAK      | Kod zapisany w wynikach wyszukiwania na listach sankcyjnych    |


### POST /parties/applicants

Utworzenie nowego formualarza KYC.

| Parametr      | Wymagane | Opis                                                           |
| ------------- | -------- | -------------------------------------------------------------- |
| **companyName**      | NIE      | Nazwa firmy wyświetlana na formularzu                   |
| **description**      | NIE      | Opis wyświetlany na formularzu                          |
| **redirectUrl**      | NIE      | URL przekierowania po wypełnieniu formualarza           |

#### Przykładowe dane do utworzenia aplikanta kyc:

```json
{
  "companyName": "Fiberpay",
  "description": "Weryfikacja na potrzeby regulaminu",
  "redirectUrl": "https://fiberpay.pl"
}
```

#### Przykładowa odpowiedź serwera:

- **STATUS 201 OK**

```json
{
  "data": {
    "code": "s6k2r8n342jw",
    "partyCode": "mjnrdbfpdp1m",
    "partyStatus": "kyc_in_progress",
    "status": "NEW",
    "payload": [],
    "childrenApplicants": [],
    "description": "Weryfikacja na potrzeby regulaminu",
    "companyName": "Fiberpay",
    "redirectUrl": "https://fiberpay.pl",
    "formUrl": "https://test.systemaml.pl/kyc/s6k2r8n342jw",
    "parentApplicantCode": null,
    "createdAt": "2024-09-24T06:56:43.000000Z",
    "updatedAt": "2024-09-24T06:56:43.000000Z",
    "identityVerification": null,
    "availableIdentityVerificationMethods": []
  }
}
```

### GET /parties/{code}/applicants

Pobranie listy aplikantów powiązanych z danym podmiotem. Parametry żądania:

| Parametr      | Wymagane | Opis                                                           |
| ------------- | -------- | -------------------------------------------------------------- |
| **code**      | TAK      | Kod podmiotu                                                   |

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
    "data": [
        {
            "code": "8j4kttg46ax6",
            "partyCode": "rx3pt8kygcnn",
            "partyStatus": "kyc_in_progress",
            "status": "NEW",
            "payload": [],
            "childrenApplicants": [],
            "description": null,
            "companyName": null,
            "redirectUrl": null,
            "formUrl": "https://test.systemaml.pl/kyc/8j4kttg46ax6",
            "parentApplicantCode": null,
            "createdAt": "2025-09-29T11:51:36.000000Z",
            "updatedAt": "2025-09-29T11:51:36.000000Z",
            "identityVerification": null,
            "availableIdentityVerificationMethods": []
        }
    ],
    "links": {
        "first": "http://apitest.systemaml.pl/1.0/parties/rx3pt8kygcnn/applicants?page=1",
        "last": "http://apitest.systemaml.pl/1.0/parties/rx3pt8kygcnn/applicants?page=1",
        "prev": null,
        "next": null
    },
    "meta": {
        "current_page": 1,
        "from": 1,
        "last_page": 1,
        "links": [
            {
                "url": null,
                "label": "&laquo; Previous",
                "active": false
            },
            {
                "url": "http://apitest.systemaml.pl/1.0/parties/rx3pt8kygcnn/applicants?page=1",
                "label": "1",
                "active": true
            },
            {
                "url": null,
                "label": "Next &raquo;",
                "active": false
            }
        ],
        "path": "http://apitest.systemaml.pl/1.0/parties/rx3pt8kygcnn/applicants",
        "per_page": 15,
        "to": 1,
        "total": 1
    }
}
```

### GET /parties/{code}/applicants/current

Pobranie szczegółów aplikanta powiązanego z danym podmiotem, którego proces weryfikacji nie został zakończony. Parametry żądania:

| Parametr      | Wymagane | Opis                                                           |
| ------------- | -------- | -------------------------------------------------------------- |
| **code**      | TAK      | Kod podmiotu                                                   |

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
    "data": {
        "code": "m5hnnwd5u6v5",
        "partyCode": "zdgmr8v8aqx3",
        "partyStatus": "kyc_in_progress",
        "status": "REPRESENTATIVES_VERIFICATIONS_PENDING",
        "payload": {
            "tradeNames": [],
            "taxIdNumber": "7010634566",
            "registrationCountry": "PL",
            "companyName": "FIBERPAY SPÓŁKA Z OGRANICZONĄ ODPOWIEDZIALNOŚCIĄ",
            "businessActivityForm": "limited_liability_company",
            "businessActivityFormOther": null,
            "nationalBusinessRegistryNumber": "365899489",
            "nationalCourtRegistryNumber": "0000647662",
            "industry": null,
            "servicesDescription": null,
            "website": null,
            "listedOnStock": "no",
            "terminationDate": null,
            "mainPkdCode": {
                "pkdCode": "64.19.Z",
                "pkdName": "POZOSTAŁE POŚREDNICTWO PIENIĘŻNE"
            },
            "beneficiaries": [
                {
                    "ownedSharesAmount": "45000",
                    "ownedSharesUnit": "PLN",
                    "directRights": "wspólnik spółki z o.o.",
                    "directRightsPrivilegeType": "brak",
                    "directRightsPrivilegeDescription": null,
                    "indirectRights": "BENEFICJENT POSIADA 41,97% AKCJI W SPÓŁCE INPAY SA (KRS 0000512707) I POSIADA 50% UDZIAŁÓW W SPÓŁCE SWAPLY INT OU (ESTONIA, COMPANY NUMBER 14450623)",
                    "otherRights": null,
                    "otherRightsDescription": null,
                    "additionalInformation": null,
                    "personalIdentityNumber": "13120880234",
                    "birthDate": null,
                    "birthCountry": null,
                    "firstName": "JAN",
                    "lastName": "KOWALSKI",
                    "middleName": null,
                    "familyName": null,
                    "documentType": null,
                    "documentTypeOther": null,
                    "documentNumber": null,
                    "documentIssueCountry": null,
                    "documentExpirationDate": null,
                    "withoutExpirationDate": false,
                    "citizenship": "PL",
                    "birthCity": null,
                    "politicallyExposed": "no",
                    "politicallyExposedFamily": "no",
                    "politicallyExposedCoworker": "no",
                    "accommodationAddress": {
                        "country": "PL"
                    }
                },
                {
                    "ownedSharesAmount": "45000",
                    "ownedSharesUnit": "PLN",
                    "directRights": "wspólnik spółki z o.o.",
                    "directRightsPrivilegeType": "brak",
                    "directRightsPrivilegeDescription": null,
                    "indirectRights": "BENEFICJENT POSIADA 42% AKCJI W SPÓŁCE INPAY SA (KRS 0000512707) I POSIADA 50% UDZIAŁÓW W SPÓŁCE SWAPLY INT OU (ESTONIA, COMPANY NUMBER 14450623)",
                    "otherRights": null,
                    "otherRightsDescription": null,
                    "additionalInformation": null,
                    "personalIdentityNumber": "12032665597",
                    "birthDate": null,
                    "birthCountry": null,
                    "firstName": "ADAM",
                    "lastName": "NOWAK",
                    "middleName": null,
                    "familyName": null,
                    "documentType": null,
                    "documentTypeOther": null,
                    "documentNumber": null,
                    "documentIssueCountry": null,
                    "documentExpirationDate": null,
                    "withoutExpirationDate": false,
                    "citizenship": "PL",
                    "birthCity": null,
                    "politicallyExposed": "no",
                    "politicallyExposedFamily": "no",
                    "politicallyExposedCoworker": "no",
                    "accommodationAddress": {
                        "country": "PL"
                    }
                }
            ],
            "businessAddress": {
                "country": "PL",
                "city": "Warszawa",
                "street": "Sienna",
                "houseNumber": "86",
                "flatNumber": "47",
                "postalCode": "00-815"
            },
            "companyContact": {
                "emailAdress": "fiberpay@info.pl",
                "phoneNumber": "123123123",
                "phoneCountry": "48"
            },
            "sourcesOfIncome": {
                "employment_contract": "no",
                "self_employment": "yes",
                "consulting": "no",
                "construction": "no",
                "cash_operations": "no",
                "tourism": "no",
                "currency_exchange": "no",
                "real_estate": "no",
                "art_antique_trading": "no",
                "pharma_healthcare": "no",
                "defense": "no",
                "money_transfers": "no",
                "public_procurement": "no",
                "trade_goods": "no",
                "tax_haven": "no",
                "tax_exempt": "no",
                "rental_income": "no",
                "investment_income": "no",
                "donation": "no",
                "savings": "no",
                "loan": "no",
                "other": "no"
            },
            "sourceOfIncomeOther": null,
            "boardMembers": [
                {
                    "firstName": "JAN",
                    "lastName": "KOWALSKI",
                    "applicantCode": "nwbbm895teaa"
                }
            ],
            "type": "company"
        },
        "childrenApplicants": [
            {
                "code": "nwbbm895teaa",
                "partyCode": "zdgmr8v8aqx3",
                "partyStatus": "kyc_in_progress",
                "status": "NEW",
                "payload": {
                    "type": "representative",
                    "firstName": "JAN",
                    "lastName": "KOWALSKI"
                },
                "childrenApplicants": [],
                "description": "KYC na potrzeby wewnętrzne",
                "companyName": "Fiberpay Sp. z o.o.",
                "redirectUrl": "https://fiberpay.pl",
                "formUrl": "https://test.systemaml.pl/kyc/nwbbm895teaa",
                "parentApplicantCode": "m5hnnwd5u6v5",
                "createdAt": "2025-09-29T12:08:28.000000Z",
                "updatedAt": "2025-09-29T12:08:28.000000Z",
                "identityVerification": null,
                "availableIdentityVerificationMethods": []
            }
        ],
        "description": "KYC na potrzeby wewnętrzne",
        "companyName": "Fiberpay Sp. z o.o.",
        "redirectUrl": "https://fiberpay.pl",
        "formUrl": "https://test.systemaml.pl/kyc/m5hnnwd5u6v5",
        "parentApplicantCode": null,
        "createdAt": "2025-09-29T12:04:36.000000Z",
        "updatedAt": "2025-09-29T12:08:28.000000Z",
        "identityVerification": null,
        "availableIdentityVerificationMethods": []
    }
}
```

### GET /applicants/{code}

Pobranie szczegółów aplikanta wskazanego kodem. Parametry żądania:

| Parametr      | Wymagane | Opis                                                           |
| ------------- | -------- | -------------------------------------------------------------- |
| **code**      | TAK      | Kod aplikanta                                                  |

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
    "data": {
        "code": "nwbbm895teaa",
        "partyCode": "zdgmr8v8aqx3",
        "partyStatus": "kyc_in_progress",
        "status": "FORM_SUBMITTED",
        "payload": {
            "personalIdentityNumber": "86041830316",
            "firstName": "JAN",
            "lastName": "KOWALSKI",
            "middleName": null,
            "familyName": null,
            "documentType": "passport",
            "documentTypeOther": null,
            "documentNumber": "PO144180",
            "documentIssueCountry": "PL",
            "documentExpirationDate": null,
            "withoutExpirationDate": true,
            "citizenship": "PL",
            "birthCity": "Warszawa",
            "birthCountry": "PL",
            "politicallyExposed": "no",
            "politicallyExposedCoworker": "no",
            "politicallyExposedFamily": "no",
            "roleType": "board_member",
            "type": "representative",
            "code": null
        },
        "childrenApplicants": [],
        "description": "KYC na potrzeby wewnętrzne",
        "companyName": "Fiberpay Sp. z o.o.",
        "redirectUrl": "https://fiberpay.pl",
        "formUrl": "https://test.systemaml.pl/kyc/nwbbm895teaa",
        "parentApplicantCode": "m5hnnwd5u6v5",
        "createdAt": "2025-09-29T12:08:28.000000Z",
        "updatedAt": "2025-09-29T12:16:55.000000Z",
        "identityVerification": null,
        "availableIdentityVerificationMethods": [
            "BANK_TRANSFER",
            "SUMSUB",
            "EPUAP_SIGNATURE",
            "QUALIFIED_ELECTRONIC_SIGNATURE"
        ]
    }
}
```


### DELETE /applicants/{code}

Usunięcie aplikanta wskazanego kodem.

### POST /applicants/{code}/acceptance

Akceptacja deklarowanych danych aplikanta wskazanego kodem.

Parametry żądania:
| Parametr      | Wymagane | Opis                                                           |
| ------------- | -------- | -------------------------------------------------------------- |
| **isAccepted**      | TAK      | Status weryfikacji. Boolean                                                  |
| **reason**      | TAK      | Powód odrzucenia weryfikacji. Argument obsługiwany tylko wtedy gdy odrzucana jest weryfikacja głównego aplikanta. Jest wymagany. String                                                  |
| **partyStatus**      | NIE*      | Status podmiotu. Argument obsługiwany tylko wtedy gdy akceptowana jest weryfikacja głównego aplikanta. Jest wymagany. Aktualnie wspierane: draft, active, inactive, in_acceptance |
| **economicRelationStartDate**      | NIE*      | Data rozpoczęcia stosunków gospodarczych. Argument obsługiwany tylko wtedy gdy akceptowana jest weryfikacja głównego aplikanta. Jest wymagany. Data YYYY-MM-DD |
| **references**      | NIE      | Referencje własne podmiotu. Argument obsługiwany tylko wtedy gdy akceptowana jest weryfikacja głównego aplikanta. Nie jest wymagany. String |

#### Przykładowe dane do zaakceptowania weryfikacji:

```json
{
  "isAccepted": true,
  "partyStatus": "active",
  "references": "KYC-12345",
  "economicRelationStartDate": "2023-01-01"
}
```

#### Przykładowa odpowiedź serwera:
- **STATUS 200 OK**

```json
{
    "data": {
        "code": "dmrkh5bn77mv",
        "partyCode": "fsjww9j6ja7h",
        "partyStatus": "active",
        "status": "ACCEPTED",
        "payload": {
            "personalIdentityNumber": "67071212695",
            "firstName": "JAN",
            "lastName": "KOWALSKI",
            "middleName": null,
            "familyName": null,
            "documentType": "passport",
            "documentTypeOther": null,
            "documentNumber": "TEST 1234",
            "documentIssueCountry": "PL",
            "documentExpirationDate": null,
            "withoutExpirationDate": true,
            "citizenship": "PL",
            "birthCity": "Warszawa",
            "birthCountry": "PL",
            "politicallyExposed": "no",
            "politicallyExposedCoworker": "no",
            "politicallyExposedFamily": "no",
            "employmentType": "entrepreneur",
            "employmentTypeOther": null,
            "accommodationAddress": {
                "street": "Sienna",
                "houseNumber": "86",
                "flatNumber": "47",
                "postalCode": "00-815",
                "city": "Warszawa",
                "country": "PL"
            },
            "personalContact": {
                "emailAdress": "kontakt@systemaml.pl",
                "phoneCountry": "48",
                "phoneNumber": "222302622"
            },
            "sourcesOfIncome": {
                "employment_contract": "yes",
                "self_employment": "no",
                "consulting": "no",
                "construction": "no",
                "cash_operations": "no",
                "tourism": "no",
                "currency_exchange": "no",
                "real_estate": "no",
                "art_antique_trading": "no",
                "pharma_healthcare": "no",
                "defense": "no",
                "money_transfers": "no",
                "public_procurement": "no",
                "trade_goods": "no",
                "tax_haven": "no",
                "tax_exempt": "no",
                "rental_income": "no",
                "investment_income": "no",
                "donation": "no",
                "savings": "no",
                "loan": "no",
                "other": "no"
            },
            "sourceOfIncomeOther": null,
            "type": "individual"
        },
        "childrenApplicants": [],
        "description": null,
        "companyName": null,
        "redirectUrl": null,
        "formUrl": "https://test.systemaml.pl/kyc/dmrkh5bn77mv",
        "parentApplicantCode": null,
        "createdAt": "2025-09-24T07:59:43.000000Z",
        "updatedAt": "2025-09-29T14:08:18.000000Z",
        "identityVerification": {
            "code": "9pb8gcup29m5",
            "method": "BANK_TRANSFER",
            "status": "ACCEPTED",
            "resultData": {
                "contractor": {
                    "name": "JAN KOWALSKI",
                    "iban": "50920600096905706312480431"
                }
            },
            "redirectUrl": "https://fiberpay.pl/order/hbfd2qje57a8"
        },
        "availableIdentityVerificationMethods": [
            "BANK_TRANSFER",
            "SUMSUB",
            "EPUAP_SIGNATURE",
            "QUALIFIED_ELECTRONIC_SIGNATURE"
        ]
    }
}
```