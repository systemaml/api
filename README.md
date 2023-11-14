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
  - 2.20. [GET /events/{code}/comments](#get-eventscodecomments)
  - 2.21. [DELETE /comments/{code}](#delete-commentscode)
  - 2.22. [GET /alerts](#get-alerts)
  - 2.23. [GET /alerts/{code}](#get-alertscode)
  - 2.24. [DELETE /alerts/{code}](#delete-alertscode)
  - 2.25. [POST /tasks](#post-tasks)
  - 2.26. [GET /tasks](#get-tasks)
  - 2.27. [GET /tasks/{code}](#get-taskscode)
  - 2.28. [DELETE /tasks/{code}](#delete-taskscode)
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
| **personalIdentityNumber** | TAK      | Numer PESEL podmiotu (w przypadku braku numeru PESEL wymagany jest parametr birthDate oraz birthCountry) |
| **birthDate**              | NIE      | Data urodzenia (wymagana jeśli nie ma numeru PESEL)                                             |
| **birthCountry**           | NIE      | Kraj urodzenia (wymagany jeśli nie ma numeru PESEL)                                             |
| **references**             | NIE      | Referencje własne                                                                               |
| **citizenship**            | NIE      | Obywatelstwo (kod kraju w standardzie ISO)                                                      |
| **birthCity**              | NIE      | Miejsce urodzenia                                                                               |
| **documentType**           | TAK      | Rodzaj dokumentu (nie jest wymagany jeśli nie ma numeru PESEL)                                  |
| **documentNumber**         | TAK      | Numer dokumentu (nie jest wymagany jeśli nie ma numeru PESEL)                                   |
| **documentExpirationDate** | NIE      | Termin ważności dokumentu                                                                       |
| **withoutExpirationDate**  | NIE      | Informacja czy dokument posiada datę ważności (bool)                                            |
| **politicallyExposed**           | TAK     | Informacja czy podmiot jest eksponowany politycznie ('yes' lub 'no')                                 |
| **politicallyExposedFamily**     | TAK     | Informacja czy podmiot jest rodziną osoby eksponowanej politycznie ('yes' lub 'no')                  |
| **politicallyExposedCoworker**   | TAK     | Informacja czy podmiot jest bliskim współpracownikiem osoby eksponowanej politycznie ('yes' lub 'no')|
| **createdByName**          | NIE      | Osoba wprowadzająca wpis                                                                        |
| **economicRelationStartDate**    | TAK     | Data rozpoczęcia stosunków gospodarczych                                                   |

b) sole_proprietorship - wszystkie powyższe oraz:

| Parametr                           | Wymagane | Opis                                                                |
| ---------------------------------- | -------- | ------------------------------------------------------------------- |
| **taxIdNumber**                    | TAK      | NIP prowadzonej działalności                                        |
| **registrationCountry**            | NIE      | Kraj rejestracji podmiotu (wymagany jeśli nie ma numeru NIP)        |
| **companyIdentifier**              | NIE      | Numer identyfikujący (wymagany jeśli nie ma numeru NIP)             |
| **nationalBusinessRegistryNumber** | NIE      | Regon prowadzonej działalności                                      |
| **companyName**                    | TAK      | Nazwa prowadzonej działalności                                      |
| **tradeNames**                     | NIE      | Tablica z nazwami handlowymi                                        |
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
| **registrationCountry**            | NIE      | Kraj rejestracji podmiotu (podawany jeśli nie ma numeru NIP)                 |
| **companyIdentifier**              | NIE      | Numer identyfikujący (wymagany jeśli nie ma numeru NIP)                      |
| **references**                     | NIE      | Referencje własne                                                            |
| **companyName**                    | NIE      | Nazwa działalności                                                           |
| **tradeNames**                     | NIE      | Tablica z nazwami handlowymi                                                 |
| **nationalBusinessRegistryNumber** | NIE      | Numer Regon                                                                  |
| **nationalCourtRegistryNumber**    | NIE      | Numer KRS                                                                    |
| **businessActivityForm**           | TAK      | Rodzaj prowadzonej działalności (nie jest wymagane jeśli nie ma numeru NIP)  |
| **website**                        | NIE      | Strona internetowa                                                           |
| **servicesDescription**            | NIE      | Opis usług                                                                   |
| **mainPkdCode**                    | TAK      | Obiekt z przeważającym kodem PKD (nie jest wymagany gdy nie ma NIP)          |
| **pkdCodes**                       | NIE      | Tablica z pozostałymi kodami PKD (tablica zawierająca obiekty jw.)           |
| **beneficiaries**                  | NIE      | Tablica obiektów z danymi beneficjentów                                      |
| **boardMembers**                   | NIE      | Tablica obiektów z danymi reprezentantów                                     |
| **createdByName**                  | NIE      | Osoba wprowadzająca wpis                                                     |


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
| **documentNumber**           | NIE      | Numer dokumentu                                                            |
| **documentExpirationDate**   | NIE      | Termin ważności dokumentu                                                  |
| **withoutExpirationDate**    | NIE      | Informacja czy dokument beneficjenta jest bezterminowy (bool)              |
| **birthDate**                | TAK      | Data urodzenia (wymagana jeśli nie ma numeru PESEL)                        |
| **birthCity**                | NIE      | Miejsce urodzenia                                                      |
| **citizenship**              | NIE      | Obywatelstwo (kod kraju w standardzie ISO)                                   |
| **birthCountry**             | NIE      | Kraj urodzenia                                                             |
| **politicallyExposed**       | TAK      | Informacja czy beneficjent jest eksponowany politycznie (bool)             |
| **accommodationAddress**     | NIE      | Obiekt zawierający adres zamieszkania (opis struktury w punkcie "a) adres")|

Struktura obiektu reprezentanta:

| Parametr                     | Wymagane | Opis                                                                       |
| ---------------------------- | -------- | -------------------------------------------------------------------------- |
| **firstName**                | TAK      | Imię reprezentanta                                                         |
| **lastName**                 | TAK      | Nazwisko reprezentanta                                                     |
| **personalIdentityNumber**   | TAK      | Numer PESEL reprezentanta (w przypadku braku numeru PESEL wymagany jest parametr (personalIdentifier) |
| **documentType**             | TAK      | Rodzaj dokumentu (nie jest wymagany jeśli nie ma numeru PESEL)             |
| **documentNumber**           | TAK      | Numer dokumentu (nie jest wymagany jeśli nie ma numeru PESEL)              |
| **documentExpirationDate**   | NIE      | Termin ważności dokumentu                                                  |
| **personalIdentifier**       | NIE      | Numer identyfikujący reprezentanta (wymagany jeśli nie ma numeru PESEL)    |
| **birthDate**                | NIE      | Data urodzenia (wymagana jeśli nie ma numeru PESEL)                        |
| **birthCountry**             | NIE      | Kraj urodzenia (wymagany jeśli nie ma numeru PESEL)                        |
| **citizenship**              | NIE      | Obywatelstwo (kod kraju w standardzie ISO)                                 |
| **birthCity**                | NIE      | Miejsce urodzenia                                                          |
| **withoutExpirationDate**    | NIE      | Informacja czy dokument posiada datę ważności (bool)                       |
| **references**               | NIE      | Referencje własne                                                          |
| **roleType**                 | TAK      | Pełniona rola. Aktualnie wspierane: president, board_member , proxy, other |
| **description**              | TAK      | Opis pełnione roli (wymagane jeśli roleType ma wartość other)              |
| **politicallyExposed**       | TAK      | Informacja czy reprezentant jest eksponowany politycznie (bool)            |

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
  "documentExpirationDate": "2025-05-15",
  "documentNumber": "aze123123",
  "documentType": "id_card",
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
  "birthCity": "Warszawa",
  "nationalBusinessRegistryNumber": "123456789",
  "birthCountry": "PL",
  "citizenship": "PL",
  "companyName": "Usługi programistyczne",
  "tradeNames": ["FiberPay", "SystemAML"],
  "createdByName": "Adam",
  "documentExpirationDate": "2025-05-08",
  "documentNumber": "aze123123",
  "documentType": "passport",
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
  "economicRelationStartDate": "2023-11-14",
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
  "type": "company",
  "companyName": "FiberPay",
  "economicRelationStartDate": "2023-11-14",
  "taxIdNumber": "7010634566",
  "nationalBusinessRegistryNumber": "147302566",
  "tradeNames": ["FiberPay"],
  "nationalCourtRegistryNumber": "0000512707",
  "businessActivityForm": "stock_company",
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
      "documentNumber": "aze123123",
      "documentType": "id_card",
      "firstName": "Jan",
      "lastName": "Kowalski",
      "ownedSharesAmount": "45",
      "ownedSharesUnit": "%",
      "personalIdentityNumber": "64091098920",
      "politicallyExposed": "no",
      "withoutExpirationDate": false,
    },
    {
      "birthCountry": "DE",
      "directRights": "3M",
      "birthCity": "Germany",
      "birthDate": "2002-10-01",
      "citizenship": "DE",
      "documentNumber": "aze423",
      "documentType": "id_card",
      "firstName": "Hans",
      "lastName": "Podolski",
      "ownedSharesAmount": "15",
      "ownedSharesUnit": "%",
      "politicallyExposed": "no",
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
      "documentNumber": "aze123123",
      "documentType": "id_card",
      "firstName": "Jan",
      "lastName": "Kowalski",
      "personalIdentityNumber": "31111161119",
      "politicallyExposed": "no",
      "roleType" : "president",
      "withoutExpirationDate": false,
    },
    {
      "birthCity": "Warszawa",
      "birthDate": "2001-01-01",
      "birthCountry": "PL",
      "citizenship": "PL",
      "description": "Wiceprezes",
      "documentNumber": "aze129923",
      "documentType": "id_card",
      "firstName": "Adam",
      "lastName": "Nowak",
      "politicallyExposed": "yes",
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
    "code": "ycrbvzhgf6wt",
    "type": "company",
    "status": "active",
    "riskStatus": null,
    "riskExplanation": "",
    "riskStatusChangedBy": "",
    "createdByName": null,
    "references": "qwerty",
    "economicRelationStartDate": "2023-11-14",
    "entity": {
      "code": "vde9t1n62m3b",
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
      "createdAt": "2023-11-14T15:03:06.000000Z",
      "registrationCountry": null,
      "companyIdentifier": null,
      "pkdCodes": [
        {
          "code": "em49danz1fyg",
          "pkdCode": "58.29.Z",
          "pkdName": "DZIAŁALNOŚĆ WYDAWNICZA W ZAKRESIE POZOSTAŁEGO OPROGRAMOWANIA",
          "mainPkd": false
        },
        {
          "code": "gfrdbumw67x5",
          "pkdCode": "62.01.Z",
          "pkdName": "DZIAŁALNOŚĆ ZWIĄZANA Z OPROGRAMOWANIEM",
          "mainPkd": false
        }
      ],
      "mainPkd": {
        "code": "4fzxqhuyw28t",
        "pkdCode": "64.99.Z",
        "pkdName": "POZOSTAŁA FINANSOWA DZIAŁALNOŚĆ USŁUGOWA, GDZIE INDZIEJ NIESKLASYFIKOWANA, Z WYŁĄCZENIEM UBEZPIECZEŃ I FUNDUSZÓW EMERYTALNYCH",
        "mainPkd": true
      }
    },
    "addresses": [
      {
        "code": "h7eyajum1cf3",
        "type": "business_address",
        "country": "PL",
        "city": "Warszawa",
        "street": "Grzybowska",
        "houseNumber": "4",
        "flatNumber": "106",
        "postalCode": "00-131",
        "createdAt": "2023-11-14T15:03:06.000000Z"
      }
    ],
    "contacts": [
      {
        "code": "ma6cr5vyn1k8",
        "type": "company",
        "emailAdress": "info@fiberpay.pl",
        "phoneCountry": "48",
        "phoneNumber": "123123123",
        "createdAt": "2023-11-14T15:03:06.000000Z"
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

W przypadku gdy użytkownik nie posiada utworzonych podmiotów, serwer zwraca obiekt:

- **STATUS 200 OK**

```json
{
  "status": "ERROR",
  "error": "No parties found"
}
```

### GET /parties/{code}

Pobranie szczegółów danego podmiotu.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": {
    "code": "6q4vcnb9sh7f",
    "type": "sole_proprietorship",
    "status": "active",
    "riskStatus": "pending",
    "riskExplanation": "Ryzyko na etapie oszacowania",
    "riskStatusChangedBy": "",
    "createdByName": "Adam",
    "references": "qwerty",
    "economicRelationStartDate": "2023-11-14",
    "entity": {
      "code": "7b8drp1x3shq",
      "firstName": "Jan",
      "lastName": "Kowalski",
      "personalIdentityNumber": "99120234518",
      "documentType": "passport",
      "documentNumber": "aze123123",
      "documentExpirationDate": "2025-05-08",
      "withoutExpirationDate": false,
      "citizenship": "PL",
      "birthCity": "Warszawa",
      "birthCountry": "PL",
      "politicallyExposed": "no",
      "politicallyExposedCoworker": "no",
      "politicallyExposedFamily": "yes",
      "createdAt": "2023-11-14T15:07:31.000000Z",
      "birthDate": null,
      "professions": [],
      "soleProprietorship": {
        "code": "pz7mua2836xq",
        "tradeNames": [
          "FiberPay",
          "SystemAML"
        ],
        "companyName": "Usługi programistyczne",
        "taxIdNumber": "3765151981",
        "nationalBusinessRegistryNumber": "123456789",
        "createdAt": "2023-11-14T15:07:31.000000Z",
        "registrationCountry": null,
        "companyIdentifier": null,
        "pkdCodes": [
          {
            "code": "tyfz7m65gnsa",
            "pkdCode": "01.15.Z",
            "pkdName": "Uprawa tytoniu",
            "mainPkd": false
          }
        ],
        "mainPkd": {
          "code": "duqzkn2peajs",
          "pkdCode": "01.12.Z",
          "pkdName": "Uprawa ryżu",
          "mainPkd": true
        }
      }
    },
    "addresses": [
      {
        "code": "f2kw96a4yzpm",
        "type": "forwarding_address",
        "country": "PL",
        "city": "Warszawa",
        "street": "Grzybowska",
        "houseNumber": "4",
        "flatNumber": "106",
        "postalCode": "00-131",
        "createdAt": "2023-11-14T15:07:31.000000Z"
      },
      {
        "code": "1ryht3mx7a45",
        "type": "business_address",
        "country": "PL",
        "city": "Warszawa",
        "street": "Grzybowska",
        "houseNumber": "4",
        "flatNumber": "106",
        "postalCode": "00-131",
        "createdAt": "2023-11-14T15:07:31.000000Z"
      },
      {
        "code": "tp2v4urmeawq",
        "type": "accommodation_address",
        "country": "PL",
        "city": "Warszawa",
        "street": "Grzybowska",
        "houseNumber": "4",
        "flatNumber": "106",
        "postalCode": "00-131",
        "createdAt": "2023-11-14T15:07:31.000000Z"
      }
    ],
    "contacts": [
      {
        "code": "zuxvhjc538nw",
        "type": "personal",
        "emailAdress": "info@fiberpay.pl",
        "phoneCountry": "48",
        "phoneNumber": "123123123",
        "createdAt": "2023-11-14T15:07:31.000000Z"
      },
      {
        "code": "164fv2xb8jpc",
        "type": "company",
        "emailAdress": "info@fiberpay.pl",
        "phoneCountry": "48",
        "phoneNumber": "123123123",
        "createdAt": "2023-11-14T15:07:31.000000Z"
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
| **documentNumber**           | NIE      | Numer dokumentu                                                            |
| **documentExpirationDate**   | NIE      | Termin ważności dokumentu                                                  |
| **withoutExpirationDate**    | NIE      | Informacja czy dokument beneficjenta jest bezterminowy (bool)              |
| **birthDate**                | TAK      | Data urodzenia (wymagana jeśli nie ma numeru PESEL)                        |
| **birthCity**                | NIE      | Miejsce urodzenia                                                      |
| **citizenship**              | NIE      | Obywatelstwo (kod kraju w standardzie ISO)                                 |
| **birthCountry**             | NIE      | Kraj urodzenia                                                             |
| **politicallyExposed**       | TAK      | Informacja czy beneficjent jest eksponowany politycznie (bool)             |
| **accommodationAddress**     | NIE      | Obiekt zawierający adres zamieszkania (opis struktury w punkcie "a) adres")|

#### Przykładowe dane do dodania beneficjenta rzeczywistego:

```json
{
  "birthCountry": "FR",
  "directRights": "Ford",
  "birthCity": "Paryż",
  "citizenship": "PL",
  "documentNumber": "ABC123",
  "documentType": "Dowód osobisty",
  "firstName": "Jan",
  "lastName": "Bożek",
  "ownedSharesAmount": "5",
  "ownedSharesUnit": "%",
  "personalIdentityNumber": "65122666817",
  "politicallyExposed": "no",
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
        "documentNumber": "ABC123",
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
        "registrationCountry": null,
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
| **documentNumber**           | TAK      | Numer dokumentu (nie jest wymagany jeśli nie ma numeru PESEL)              |
| **documentExpirationDate**   | NIE      | Termin ważności dokumentu                                                  |
| **personalIdentifier**       | NIE      | Numer identyfikujący reprezentanta  (wymagany jeśli nie ma numeru PESEL) |
| **birthDate**                | NIE      | Data urodzenia (wymagana jeśli nie ma numeru PESEL)                        |
| **birthCountry**             | NIE      | Kraj urodzenia (wymagany jeśli nie ma numeru PESEL)                        |
| **citizenship**              | NIE      | Obywatelstwo (kod kraju w standardzie ISO)                                 |
| **birthCity**                | NIE      | Miejsce urodzenia                                                      |
| **withoutExpirationDate**    | NIE      | Informacja czy dokument posiada datę ważności (bool)                       |
| **references**               | NIE      | Referencje własne                                                          |
| **roleType**                 | TAK      | Pełniona rola. Aktualnie wspierane: president, board_member , proxy, other |
| **description**              | TAK      | Opis pełnione roli (wymagane jeśli roleType ma wartość other)              |
| **politicallyExposed**       | TAK      | Informacja czy beneficjent jest eksponowany politycznie (bool)             |

#### Przykładowe dane do dodania reprezentanta:

```json
{
  "birthCity": "Warszawa",
  "birthDate": "2002-01-01",
  "birthCountry": "PL",
  "citizenship": "PL",
  "description": "Prezes",
  "documentNumber": "aze1f123",
  "documentType": "id_card",
  "firstName": "Jan",
  "lastName": "Nowak",
  "personalIdentityNumber": "97120824889",
  "politicallyExposed": "yes",
  "roleType" : "proxy",
  "withoutExpirationDate": false,
}
```

#### Przykładowa odpowiedź serwera:

- **STATUS 201 CREATED**

```json
{
  "data": {
    "code": "usm3qyex6azk",
    "description": "prokurent",
    "roleType": "proxy",
    "individualEntity": {
      "code": "5mad1q3y8v7t",
      "firstName": "Jan",
      "lastName": "Nowak",
      "personalIdentityNumber": "97120824889",
      "documentType": "id_card",
      "documentNumber": "aze1f123",
      "documentExpirationDate": null,
      "withoutExpirationDate": false,
      "citizenship": "PL",
      "birthCity": "Warszawa",
      "birthCountry": "PL",
      "politicallyExposed": "yes",
      "politicallyExposedCoworker": "not_defined",
      "politicallyExposedFamily": "not_defined",
      "createdAt": "2023-11-14T15:14:43.000000Z",
      "birthDate": null,
      "professions": []
    },
    "company": {
      "legalEntity": {
        "code": "qf6p5tcmbz98",
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
        "createdAt": "2023-11-14T15:11:27.000000Z",
        "registrationCountry": null,
        "companyIdentifier": null,
        "pkdCodes": [
          {
            "code": "cypazkgw9ru7",
            "pkdCode": "58.29.Z",
            "pkdName": "DZIAŁALNOŚĆ WYDAWNICZA W ZAKRESIE POZOSTAŁEGO OPROGRAMOWANIA",
            "mainPkd": false
          },
          {
            "code": "p7vn6rwkqztx",
            "pkdCode": "62.01.Z",
            "pkdName": "DZIAŁALNOŚĆ ZWIĄZANA Z OPROGRAMOWANIEM",
            "mainPkd": false
          }
        ],
        "mainPkd": {
          "code": "sxzfra8k6y4q",
          "pkdCode": "64.99.Z",
          "pkdName": "POZOSTAŁA FINANSOWA DZIAŁALNOŚĆ USŁUGOWA, GDZIE INDZIEJ NIESKLASYFIKOWANA, Z WYŁĄCZENIEM UBEZPIECZEŃ I FUNDUSZÓW EMERYTALNYCH",
          "mainPkd": true
        }
      },
      "addresses": [
        {
          "code": "4chmzytqpker",
          "type": "business_address",
          "country": "PL",
          "city": "Warszawa",
          "street": "Grzybowska",
          "houseNumber": "4",
          "flatNumber": "106",
          "postalCode": "00-131",
          "createdAt": "2023-11-14T15:11:27.000000Z"
        }
      ],
      "contacts": [
        {
          "code": "wqc48js29fgv",
          "type": "company",
          "emailAdress": "info@fiberpay.pl",
          "phoneCountry": "48",
          "phoneNumber": "123123123",
          "createdAt": "2023-11-14T15:11:27.000000Z"
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
        "professions": []
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
| **type**                  | TAK      | Typ transakcji. Aktualnie wspierane: buyer, vender, transfer, other |
| **status**                | TAK      | Status transakcji. Aktualnie wspierane: draft, in_acceptance, accepted, cancelled |
| **occasionalTransaction** | TAK      | Informacja czy transakcja jest okazjonalna (bool)        |

Jeśli transakcja jest oznaczona jako okazjonalna wymagane są następujące parametry:

| Parametr          | Wymagane | Opis                                                       |
| ----------------- | -------- | ---------------------------------------------------------- |
| **amount**        | TAK      | Kwota transakcji                                           |
| **currency**      | TAK      | Waluta transakcji                                          |
| **bookedAt**      | TAK      | Data zaksięgowania transakcji                              |
| **paymentMethod** | TAK      | Sposób płatności                                           |
| **title**         | TAK      | Tytuł transakcji                                           |
| **location**      | NIE      | Kraj w którym została przeprowadzona transakcja            |
| **description**   | NIE      | Opis transakcji (przedmiot transakcji, komentarz itd.)     |
| **references**    | NIE      | Referencje własne                                          |
| **createdByName** | NIE      | Dane osoby wprowadzającej wpis                             |
| **entities**      | NIE      | Tablica obiektów z danymi stron transakcji, wymagane w zależności od occasionalTransaction i typu transakcji |


Obiekt pojedynczej strony transakcji powinien zawierać poniższe parametry:

| Parametr                | Wymagane | Opis                                                                 |
| ----------------------- | -------- | -------------------------------------------------------------------- |
| **type**                | TAK      | Typ strony transakcji. Aktualnie wspierane: receiver, sender, seller, buyer, payer|
| **partyCode**           | NIE      | Kod podmiotu strony transakcji                                       |
| **firstName**           | NIE      | Imię strony transakcji (wymagany jeśli nie jest podany kod strony transakcji)          |
| **lastName**            | NIE      | Nazwisko strony transakcji (wymagany jeśli nie jest podany kod strony transakcji)      |
| **companyName**         | NIE      | Nazwa firmy strony transakcji (wymagany jeśli nie jest podany kod strony transakcji)     |
| **description**         | NIE      | Opis strony transakcji                                               |
| **iban**                | NIE      | Numer iban strony transakcji (wymagany jeśli paymentMethod === bank_transfer) |

Dla transakcji typu buyer wymagane jest podanie strony transakcji o typie seller.
Dla transakcji typu vender wymagane jest podanie strony transakcji o typie buyer.
Dla transakcji typu transfer wymagane jest podanie stron transakcji o typach payer oraz receiver.
#### Przykładowe dane do utworzenia transakcji:

```json
{
  "status": "accepted",
  "type": "vender",
  "occasionalTransaction": false,
  "amount": "780",
  "currency": "PLN",
  "bookedAt": "2023-09-10 10:10:10",
  "paymentMethod": "blik",
  "title": "transakcja testowa",
  "location": "PL",
  "references": "qwerty",
  "createdByName": "Tester",
  "entities": [
    {
      "type" : "buyer",
      "description" : "Testowa strona transakcji",
      "firstName" : "Jan",
      "lastName" : "Kowalski",
    }
  ]
}
```

#### Przykładowa odpowiedź serwera:

- **STATUS 201 CREATED**

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
| **partyCode**       | NIE      | Kod podmiotu który będzie powiązany z zadaniem   |
| **transactionCode** | NIE      | Kod transakcji która będzie powiązana z zadaniem |
| **alertCode**       | NIE      | Kod alertu który będzie powiązany z zadaniem     |

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
    "code": "nga1jz6c8v45",
    "content": "Utworzyć przykładowe zadanie do celów reprezentacyjnych w dokumentacji",
    "status": "new",
    "createdAt": "2023-04-21T11:08:42.000000Z"
  }
}
```

### GET /tasks
Pobranie zadań powiązanych z danym użytkownikiem.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": {
    "code": "x59w7bjksazc",
    "content": "Uzupełnij dane niezbędne do wystawienia faktury za abonament",
    "status": "displayed",
    "alertCode": "6eky4dmqvcwf",
    "partyCode": null,
    "transactionCode": null,
    "expirationDate": null
  },
  {
    "code": "nga1jz6c8v45",
    "content": "Utworzyć przykładowe zadanie do celów reprezentacyjnych w dokumentacji",
    "status": "new",
    "alertCode": null,
    "partyCode": null,
    "transactionCode": null,
    "expirationDate": null,
    "createdAt": "2023-04-21T11:08:42.000000Z"
  }
}
```

### GET /tasks/{code}
Pobranie szczegółów zadania wskazanego kodem.

- **STATUS 200 OK**

```json
{
  "data": {
    "code": "x59w7bjksazc",
    "content": "Uzupełnij dane niezbędne do wystawienia faktury za abonament",
    "status": "displayed",
    "alertCode": "6eky4dmqvcwf",
    "partyCode": null,
    "transactionCode": null,
    "expirationDate": null
  }
}
```

### DELETE /tasks/{code}

Usunięcie zadania wskazanego kodem identyfikującym.
