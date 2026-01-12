---
Wspomaganie działań przeciwdziałania praniu pieniędzy i finansowania terroryzmu
---

# SystemAML

## Spis treści

## 1. Konfiguracja
- [1.1. Informacje ogólne](#11-informacje-ogólne)
- [1.2. Adresy serwerów](#12-adresy-serwerów)
- [1.3. Klucze API](#13-klucze-api)
- [1.4. Webhooks](#14-webhooks)

## 2. Zarządzanie podmiotami

### 2.1. Podmioty
  - [2.1.1. Tworzenie podmiotu](#211-post-parties) `POST /parties`
  - [2.1.2. Lista podmiotów](#212-get-parties) `GET /parties`
  - [2.1.3. Szczegóły podmiotu](#213-get-partiescode) `GET /parties/{code}`
  - [2.1.4. Zmiana statusu podmiotu](#214-post-partiescodestatus) `POST /parties/{code}/status`
  - [2.1.5. Usuwanie podmiotu](#215-delete-partiescode) `DELETE /parties/{code}`

### 2.2. Beneficjenci
  - [2.2.1. Dodawanie beneficjenta](#221-post-partiescodebeneficiaries) `POST /parties/{code}/beneficiaries`
  - [2.2.2. Lista beneficjentów](#222-get-partiescodebeneficiaries) `GET /parties/{code}/beneficiaries`
  - [2.2.3. Usuwanie beneficjenta](#223-delete-beneficiariescode) `DELETE /beneficiaries/{code}`

### 2.3. Reprezentanci
  - [2.3.1. Dodawanie reprezentanta](#231-post-partiescodeboardmembers) `POST /parties/{code}/boardmembers`
  - [2.3.2. Lista reprezentantów](#232-get-partiescodeboardmembers) `GET /parties/{code}/boardmembers`
  - [2.3.3. Usuwanie reprezentanta](#233-delete-boardmemberscode) `DELETE /boardmembers/{code}`

## 3. Transakcje
- [3.1. Tworzenie transakcji](#31-post-transactions) `POST /transactions`
- [3.2. Lista transakcji](#32-get-transactions) `GET /transactions`
- [3.3. Szczegóły transakcji](#33-get-transactionscode) `GET /transactions/{code}`
- [3.4. Zmiana statusu transakcji](#34-post-transactionscodestatus) `POST /transactions/{code}/status`
- [3.5. Usuwanie transakcji](#35-delete-transactionscode) `DELETE /transactions/{code}`

## 4. Zdarzenia i zadania

### 4.1. Zdarzenia
  - [4.1.1. Tworzenie zdarzenia](#411-post-history-events) `POST /history-events`
  - [4.1.2. Lista zdarzeń](#412-get-history-events) `GET /history-events`
  - [4.1.3. Szczegóły zdarzenia](#413-get-history-eventscode) `GET /history-events/{code}`
  - [4.1.4. Usuwanie zdarzenia](#414-delete-history-eventscode) `DELETE /history-events/{code}`

### 4.2. Komentarze do zdarzeń
  - [4.2.1. Dodaj komentarz](#421-post-comments) `POST /comments`
  - [4.2.2. Lista komentarzy](#422-get-history-eventscodecomments) `GET /history-events/{code}/comments`
  - [4.2.3. Usuń komentarz](#423-delete-commentscode) `DELETE /comments/{code}`

### 4.3. Zadania
  - [4.3.1. Tworzenie zadania](#431-post-tasks) `POST /tasks`
  - [4.3.2. Lista zadań](#432-get-tasks) `GET /tasks`
  - [4.3.3. Szczegóły zadania](#433-get-taskscode) `GET /tasks/{code}`
  - [4.3.4. Usuwanie zadania](#434-delete-taskscode) `DELETE /tasks/{code}`

### 4.4. Komentarze do zadań
  - [4.4.1. Dodaj komentarz](#441-post-taskscodecomments) `POST /tasks/{code}/comments`
  - [4.4.2. Lista komentarzy](#442-get-taskscodecomments) `GET /tasks/{code}/comments`
  - [4.4.3. Usuń komentarz](#443-delete-taskscodecommentscommentscode) `DELETE /tasks/{code}/comments/{commentsCode}`
  - [4.4.4. Edytuj komentarz](#444-patch-taskscodecommentscommentscode) `PATCH /tasks/{code}/comments/{commentsCode}`

## 5. Alerty
- [5.1. Lista alertów](#51-get-alerts) `GET /alerts`
- [5.2. Szczegóły alertu](#52-get-alertscode) `GET /alerts/{code}`
- [5.3. Usuwanie alertu](#53-delete-alertscode) `DELETE /alerts/{code}`

## 6. Compliance i weryfikacja

### 6.1. Listy sankcyjne
  - [6.1.1. Wyszukiwanie](#611-post-sanctions-listssearch) `POST /sanctions-lists/search`
  - [6.1.2. Raport PDF](#612-get-sanctionscodepdf) `GET /sanctions/{code}/pdf`

### 6.2. Proces KYC
  - [6.2.1. Utworzenie formularza](#621-post-partiesapplicants) `POST /parties/applicants`
  - [6.2.2. Lista aplikantów](#622-get-partiescodeapplicants) `GET /parties/{code}/applicants`
  - [6.2.3. Aktualny aplikant](#623-get-partiescodeapplicantscurrent) `GET /parties/{code}/applicants/current`
  - [6.2.4. Szczegóły aplikanta](#624-get-applicantscode) `GET /applicants/{code}`
  - [6.2.5. Usuwanie aplikanta](#625-delete-applicantscode) `DELETE /applicants/{code}`
  - [6.2.6. Akceptacja aplikanta](#626-post-applicantscodeacceptance) `POST /applicants/{code}/acceptance`

## 1. Konfiguracja

### 1.1. Informacje ogólne

API SystemAML umożliwia automatyzację procesów związanych z:
- Zarządzaniem bazą klientów (podmiotów)
- Rejestrowaniem transakcji
- Weryfikacją na listach sankcyjnych
- Przeprowadzaniem procesu KYC (Know Your Customer)
- Tworzeniem alertów i zarządzaniem zadaniami

#### Dla kogo?
API przeznaczone jest dla instytucji zobowiązanych do prowadzenia ewidencji klientów zgodnie z ustawą o przeciwdziałaniu praniu pieniędzy.

### 1.2. Adresy serwerów

| Środowisko | Funkcjonalność | Ścieżka bazowa |
|:------------:|:----------------:|:----------------:|
| **Produkcyjne** | API | `/1.0/` |
| **Produkcyjne** | Panel aplikacji | `/` |
| **Testowe** | API | `/1.0/` |
| **Testowe** | Panel aplikacji | `/` |

> 📧 **Aby uzyskać właściwe adresy URL:** Skontaktuj się z zespołem pod adresem info@fiberpay.pl

### ⚠️ Ważne informacje o środowiskach

| Aspekt | Opis |
|:--------:|:------:|
| **Separacja** | Środowiska są całkowicie rozdzielone (osobna infrastruktura) |
| **Klucze API** | Klucze z jednego środowiska **nie działają** w drugim |
| **Dane** | Dane nie są synchronizowane między środowiskami |
| **Testowe** | Służy do testów integracji, dane nie są prawdziwe |
| **Produkcyjne** | Środowisko operacyjne z prawdziwymi danymi |

### 1.3. Klucze API

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
### 1.4. Webhooks

Webhooks umożliwiają automatyczne powiadomienia o zdarzeniach w SystemAML.

#### Jak to działa?

```mermaid
sequenceDiagram
    SystemAML->>Twój serwer: POST /webhook (JWT token)
    Twój serwer->>Twój serwer: Weryfikacja podpisu JWT
    Twój serwer->>SystemAML: 200 OK
```

#### Konfiguracja

1. Zaloguj się do [panelu SystemAML](/dashboard/settings/api/)
2. Przejdź do **Ustawienia → API → Webhooks**
3. Dodaj URL swojego endpointu: `https://twoja-domena.pl/webhook`
4. Wygeneruj **Webhook Secret** (klucz do weryfikacji)
5. Wybierz typy zdarzeń, które Cię interesują

#### Format webhook

SystemAML wysyła **POST** z tokenem JWT w body:

```http
POST /webhook HTTP/1.1
Host: twoja-domena.pl
Content-Type: text/plain
```

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

#### Typy zdarzeń webhooków

##### Podmioty
| Typ | Opis | Przykładowe dane |
|:-----:|:------:|:------------------:|
| `party_profile_updated` | Aktualizacja danych podmiotu | `{ party: { code, firstName, lastName } }` |
| `party_status_change` | Zmiana statusu | `{ party: { code, status } }` |
| `party_risk_change` | Zmiana ryzyka | `{ party: { code, riskStatus } }` |
| `party_deleted` | Usunięcie | `{ party: { code } }` |

##### Transakcje
| Typ | Opis |
|:-----:|:------:|
| `transaction_data_updated` | Aktualizacja danych transakcji |
| `transaction_status_change` | Zmiana statusu transakcji |
| `transaction_risk_change` | Zmiana ryzyka transakcji |
| `transaction_deleted` | Usunięcie transakcji |

##### Proces KYC
| Typ | Opis |
|:-----:|:------:|
| `applicant_created` | Utworzenie nowego aplikanta |
| `applicant_status_change` | Zmiana statusu weryfikacji |

#### Ponowne wysyłanie

Jeśli Twój serwer nie odpowie statusem `2xx`:
- SystemAML ponowi próbę **3 razy**
- Z odstępem **5 minut**

#### Testowanie webhooków

```bash
# Użyj ngrok do lokalnego testowania
ngrok http 3000

# Ustaw URL w panelu:
https://abc123.ngrok.io/webhook
```

#### Dodatkowe zasoby
- [JWT.io - Debugowanie tokenów](https://jwt.io/)
- [Ngrok - Tunelowanie lokalne](https://ngrok.com/)


## 2. Zarządzanie podmiotami

### 2.1. Podmioty

#### 2.1.1. POST /parties

Tworzy nowy podmiot w systemie AML.

#### Typy podmiotów

SystemAML wspiera trzy typy podmiotów:

| Typ | Kod | Opis | Przykład użycia |
|:-----:|:-----:|:------:|:-----------------:|
| **Osoba fizyczna** | `individual` | Klient indywidualny | Kupujący kryptowaluty |
| **Jednoosobowa działalność** | `sole_proprietorship` | Przedsiębiorca | Freelancer, konsultant |
| **Firma** | `company` | Organizacja | Sp. z o.o., S.A. |

#### Parametry wspólne dla wszystkich typów

| Parametr | Typ | Wymagane | Wartości | Opis |
|:----------:|:-----:|:----------:|:----------:|:------:|
| **type** | string | TAK | `individual`, `sole_proprietorship`, `company` | Typ podmiotu |
| **status** | string | TAK | `draft`, `active`, `occasional`, `inactive`, `in_acceptance` | Status podmiotu |
| **economicRelationStartDate** | date | TAK | `YYYY-MM-DD` | Data rozpoczęcia współpracy |
| **references** | string | NIE | - | Własny identyfikator/notatka |
| **createdByName** | string | NIE | - | Osoba wprowadzająca wpis |

W zależności od wybranego typu wymagane są dodatkowe parametry opisane poniżej.

## Typ 1: Individual (Osoba fizyczna)

### Parametry podstawowe

| Parametr | Typ | Wymagane | Opis | Przykład | Walidacja |
|:----------:|:-----:|:----------:|:------:|:----------:|:-----------:|
| **firstName** | string | TAK | Imię | `"Jan"` | Max 255 znaków |
| **lastName** | string | TAK | Nazwisko | `"Kowalski"` | Max 255 znaków |
| **personalIdentityNumber** | string | WARUNKOWO* | PESEL | `"09271573233"` | 11 cyfr + suma kontrolna |
| **birthDate** | date | WARUNKOWO* | Data urodzenia | `"2000-01-12"` | Format: YYYY-MM-DD |
| **birthCountry** | string | WARUNKOWO* | Kraj urodzenia | `"PL"` | Kod ISO (2 znaki) |
| **birthCity** | string | NIE | Miejsce urodzenia | `"Warszawa"` | Max 255 znaków |
| **citizenship** | string | NIE | Obywatelstwo | `"PL"` | Kod ISO (2 znaki) |
| **middleName** | string | NIE | Drugie imię | `"Andrzej"` | Max 255 znaków |
| **familyName** | string | NIE | Nazwisko rodowe | `"Nowak"` | Max 255 znaków |

> **Uwaga dotycząca PESEL:**
> `*` W przypadku braku numeru PESEL wymagany jest parametr `birthDate`, `birthCountry`


### Dokument tożsamości

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **documentType** | TAK | Typ dokumentu | `"id_card"` |
| **documentNumber** | TAK | Numer dokumentu | `"SQT233656"` |
| **documentIssueCountry** | TAK | Kraj wydania | `"PL"` |
| **documentExpirationDate** | TAK | Data ważności | `"2026-05-15"` |
| **withoutExpirationDate** | NIE | Dokument bezterminowy? | `true`/`false` |
| **documentTypeOther** | WARUNKOWO* | Opis innego dokumentu | - |

**Wymagalność:**
- `*` Wymagane gdy `documentType: "other"`

#### Typy dokumentów (documentType)

| Kod | Nazwa |
|:-----:|:-------:|
| `id_card` | Dowód osobisty |
| `electronic_id_card` | E-dowód |
| `passport` | Paszport |
| `residency_card` | Karta pobytu |
| `other` | Inny dokument |

### Status zatrudnienia (employmentType)

| Kod | Nazwa |
|:-----:|:-------:|
| `student` | Student |
| `retiree` | Emeryt |
| `pensioner` | Rencista |
| `entrepreneur` | Przedsiębiorca |
| `employed_uop` | Zatrudniony na UOP |
| `employed_uzuod` | Zatrudniony na UZ/UOD |
| `unemployed` | Niezatrudniony |
| `jobless` | Bezrobotny |
| `annuitant` | Rentier |
| `other` | Inny |

### Dane Politically Exposed Person (PEP) (wymagane)

| Parametr | Wartości | Opis |
|:----------:|:----------:|:------:|
| **politicallyExposed** | `yes`, `no` | Czy osoba jest PEP? |
| **politicallyExposedFamily** | `yes`, `no` | Czy jest rodziną PEP? |
| **politicallyExposedCoworker** | `yes`, `no` | Czy jest współpracownikiem PEP? |

### Przykładowe dane do utworzenia podmiotu typu 'individual':
```json
{
  "type": "individual",
  "status": "active",
  "firstName": "Jan",
  "lastName": "Kowalski",
  "personalIdentityNumber": "09271573233",
  "documentType": "id_card",
  "documentNumber": "SQT233656",
  "documentIssueCountry": "PL",
  "documentExpirationDate": "2026-05-15",
  "withoutExpirationDate": false,
  "citizenship": "PL",
  "birthCity": "Warszawa",
  "birthCountry": "PL",
  "birthDate": "2000-01-12",
  "economicRelationStartDate": "2023-11-14",
  "politicallyExposed": "no",
  "politicallyExposedFamily": "no",
  "politicallyExposedCoworker": "no",
  "createdByName": "Wojtek",
  "references": "KL-2023-001",
  "accommodationAddress": {
    "country": "PL",
    "city": "Warszawa",
    "street": "Sienna",
    "houseNumber": "86",
    "flatNumber": "47",
    "postalCode": "00-815"
  },
  "personalContact": {
    "emailAdress": "jan.kowalski@example.com",
    "phoneCountry": "48",
    "phoneNumber": "123123123"
  }
}
```


## Typ 2: Sole Proprietorship (Jednoosobowa działalność)

**Wszystkie parametry z typu Individual + dodatkowe poniżej**

### Parametry działalności gospodarczej

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **taxIdNumber** | TAK | NIP działalności | `"3765151981"` |
| **registrationCountry** | TAK | Kraj rejestracji | `"PL"` |
| **companyName** | TAK | Nazwa działalności | `"Usługi IT Jan Kowalski"` |
| **companyIdentifier** | WARUNKOWO* | Numer identyfikacyjny | - |
| **nationalBusinessRegistryNumber** | NIE | REGON | `"632702201"` |
| **tradeNames** | NIE | Nazwy handlowe | `["FiberPay", "SystemAML"]` |
| **mainPkdCode** | WARUNKOWO** | Główny kod PKD | Zobacz strukturę |
| **pkdCodes** | NIE | Dodatkowe kody PKD | Tablica obiektów |
| **terminationDate** | NIE | Data zakończenia działalności | `"2024-12-31"` |

**Wymagalność:**
- `*` Wymagane gdy brak NIP
- `**` Wymagane gdy podany jest NIP

### Struktura PKD

```json
{
  "pkdCode": "62.01.Z",
  "pkdName": "Działalność związana z oprogramowaniem"
}
```

### Typy adresów dla jednoosobowej działalności

| Parametr | Opis | Wymagane |
|:----------:|:------:|:----------:|
| **accommodationAddress** | Adres zamieszkania właściciela | NIE |
| **forwardAddress** | Adres korespondencyjny | NIE |
| **businessAddress** | Adres prowadzenia działalności | NIE |

### Typy kontaktów dla jednoosobowej działalności

| Parametr | Opis | Wymagane |
|:----------:|:------:|:----------:|
| **personalContact** | Kontakt osobisty właściciela | NIE |
| **companyContact** | Kontakt firmowy działalności | NIE |

### Przykład - Jednoosobowa działalność

```json
{
  "type": "sole_proprietorship",
  "status": "active",
  "registrationCountry": "PL",
  "firstName": "Jan",
  "lastName": "Kowalski",
  "personalIdentityNumber": "99120234518",
  "documentType": "passport",
  "documentNumber": "ABC123456",
  "documentIssueCountry": "PL",
  "documentExpirationDate": "2026-05-08",
  "withoutExpirationDate": false,
  "citizenship": "PL",
  "birthCity": "Warszawa",
  "birthCountry": "PL",
  "politicallyExposed": "no",
  "politicallyExposedFamily": "no",
  "politicallyExposedCoworker": "no",
  "companyName": "Usługi programistyczne Jan Kowalski",
  "tradeNames": ["CodeMaster", "DevPro"],
  "taxIdNumber": "3765151981",
  "nationalBusinessRegistryNumber": "632702201",
  "economicRelationStartDate": "2023-11-14",
  "mainPkdCode": {
    "pkdCode": "62.01.Z",
    "pkdName": "Działalność związana z oprogramowaniem"
  },
  "pkdCodes": [
    {
      "pkdCode": "63.11.Z",
      "pkdName": "Przetwarzanie danych"
    }
  ],
  "accommodationAddress": {
    "country": "PL",
    "city": "Warszawa",
    "street": "Sienna",
    "houseNumber": "86",
    "flatNumber": "47",
    "postalCode": "00-815"
  },
  "businessAddress": {
    "country": "PL",
    "city": "Warszawa",
    "street": "Sienna",
    "houseNumber": "86",
    "postalCode": "00-815"
  },
  "forwardAddress": {
    "country": "PL",
    "city": "Warszawa",
    "street": "Sienna",
    "houseNumber": "86",
    "flatNumber": "47",
    "postalCode": "00-815"
  },
  "personalContact": {
    "emailAdress": "jan@example.com",
    "phoneCountry": "48",
    "phoneNumber": "123123123"
  },
  "companyContact": {
    "emailAdress": "biuro@example.com",
    "phoneCountry": "48",
    "phoneNumber": "222302622"
  }
}
```


## Typ 3: Company (Organizacja)

### Parametry podstawowe firmy

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **taxIdNumber** | TAK | NIP | `"7010634566"` |
| **registrationCountry** | TAK | Kraj rejestracji | `"PL"` |
| **companyName** | TAK | Nazwa firmy | `"FiberPay Sp. z o.o."` |
| **businessActivityForm** | WARUNKOWO* | Forma prawna | Zobacz [formy prawne](#formy-prawne-businessactivityform) |
| **companyIdentifier** | WARUNKOWO** | Numer identyfikacyjny | - |
| **nationalBusinessRegistryNumber** | NIE | REGON | `"147302566"` |
| **nationalCourtRegistryNumber** | NIE | KRS | `"0000512707"` |
| **tradeNames** | NIE | Nazwy handlowe | `["FiberPay"]` |
| **mainPkdCode** | WARUNKOWO* | Główny PKD | Zobacz [strukturę PKD](#struktura-pkd) |
| **pkdCodes** | NIE | Dodatkowe kody PKD | Tablica obiektów |
| **website** | NIE | Strona WWW | `"fiberpay.pl"` |
| **servicesDescription** | NIE | Opis usług | - |
| **listedOnStock** | NIE | Notowana na giełdzie? | `"yes"`, `"no"` |
| **terminationDate** | NIE | Data zakończenia działalności | `"2024-12-31"` |
| **economicRelationStartDate** | TAK | Data rozpoczęcia współpracy | `"2023-11-14"` |
| **references** | NIE | Własny identyfikator/notatka | - |
| **createdByName** | NIE | Osoba wprowadzająca wpis | - |

**Wymagalność:**
- `*` Wymagane gdy podany jest NIP
- `**` Wymagane gdy brak NIP

### Formy prawne (businessActivityForm)

| Kod | Nazwa |
|:-----:|:-------:|
| `limited_liability_company` | Spółka z ograniczoną odpowiedzialnością |
| `stock_company` | Spółka akcyjna |
| `simple_joint_stock_company` | Prosta spółka akcyjna |
| `civil_partnership_company` | Spółka cywilna |
| `general_partnership_company` | Spółka jawna |
| `professional_partnership_company` | Spółka partnerska |
| `limited_partnership_company` | Spółka komandytowa |
| `limited_joint_stock_partnership_company` | Spółka komandytowo-akcyjna |
| `branches_of_foreign_entrepreneur` | Oddziały zagranicznych przedsiębiorców |
| `other` | Inna |

### Struktura PKD

```json
{
  "pkdCode": "64.99.Z",
  "pkdName": "Pozostała finansowa działalność usługowa"
}
```

---

## Beneficjenci rzeczywiści

**Dodawane w tablicy `beneficiaries`**

Beneficjenci rzeczywiści to osoby fizyczne, które:
- Posiadają minimum 25% udziałów lub akcji
- Wywierają kontrolę nad spółką w inny sposób
- Są ostatecznymi odbiorcami korzyści finansowych

### Parametry podstawowe beneficjenta

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **firstName** | TAK | Imię | `"Jan"` |
| **lastName** | TAK | Nazwisko | `"Kowalski"` |
| **personalIdentityNumber** | WARUNKOWO* | PESEL | `"64091098920"` |
| **birthDate** | WARUNKOWO* | Data urodzenia | `"1985-06-15"` |
| **birthCountry** | NIE | Kraj urodzenia | `"PL"` |
| **birthCity** | NIE | Miejsce urodzenia | `"Warszawa"` |
| **citizenship** | NIE | Obywatelstwo | `"PL"` |

**Wymagalność:**
- `*` PESEL lub `birthDate` - jedno z nich jest wymagane

### Dokument tożsamości beneficjenta

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **documentType** | NIE | Typ dokumentu | `"id_card"` |
| **documentNumber** | WARUNKOWO* | Numer dokumentu | `"JET449773"` |
| **documentIssueCountry** | WARUNKOWO* | Kraj wydania | `"PL"` |
| **documentExpirationDate** | WARUNKOWO* | Data ważności | `"2026-05-15"` |
| **withoutExpirationDate** | NIE | Dokument bezterminowy? | `true`/`false` |

**Wymagalność:**
- `*` Wymagane gdy podano `documentType`

### Uprawnienia beneficjenta

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **ownedSharesAmount** | NIE | Liczba udziałów | `"45"` |
| **ownedSharesUnit** | NIE | Jednostka | `"%"` lub `"PLN"` |
| **directRights** | NIE | Bezpośrednie uprawnienia | `"Wspólnik spółki"` |
| **directRightsPrivilegeType** | NIE | Rodzaj uprzywilejowania | `"brak"` |
| **directRightsPrivilegeDescription** | NIE | Opis uprzywilejowania | `"brak"` |
| **indirectRights** | NIE | Pośrednie uprawnienia | `"brak"` |
| **otherRights** | NIE | Inne uprawnienia | `"brak"` |
| **otherRightsDescription** | NIE | Opis innych uprawnień | `"brak"` |
| **additionalInformation** | WARUNKOWO* | Dodatkowe informacje | `"brak"` |

**Wymagalność:**
- `*` Wymagane gdy wszystkie powyższe uprawnienia są puste

### Dane PEP beneficjenta

| Parametr | Wymagane | Wartości | Opis |
|:----------:|:----------:|:----------:|:------:|
| **politicallyExposed** | TAK | `yes`, `no` | Czy beneficjent jest PEP? |
| **politicallyExposedFamily** | TAK | `yes`, `no` | Czy jest rodziną PEP? |
| **politicallyExposedCoworker** | TAK | `yes`, `no` | Czy jest współpracownikiem PEP? |

### Adres beneficjenta

| Parametr | Wymagane | Opis |
|:----------:|:----------:|:------:|
| **accommodationAddress** | NIE | Obiekt z adresem zamieszkania |

**Struktura adresu:**
```json
{
  "country": "PL",
  "city": "Warszawa",
  "street": "Nowa",
  "houseNumber": "1",
  "flatNumber": "10",
  "postalCode": "00-001"
}
```

### Przykład beneficjenta z PESEL

```json
{
  "firstName": "Jan",
  "lastName": "Kowalski",
  "personalIdentityNumber": "64091098920",
  "documentType": "id_card",
  "documentNumber": "JET449773",
  "documentIssueCountry": "PL",
  "withoutExpirationDate": true,
  "citizenship": "PL",
  "birthCountry": "PL",
  "birthCity": "Warszawa",
  "ownedSharesAmount": "45",
  "ownedSharesUnit": "%",
  "directRights": "Wspólnik spółki",
  "directRightsPrivilegeType": "brak",
  "politicallyExposed": "no",
  "politicallyExposedFamily": "no",
  "politicallyExposedCoworker": "no",
  "accommodationAddress": {
    "country": "PL",
    "city": "Warszawa",
    "street": "Nowa",
    "houseNumber": "1",
    "postalCode": "00-001"
  }
}
```

### Przykład beneficjenta bez PESEL

```json
{
  "firstName": "Hans",
  "lastName": "Podolski",
  "birthDate": "2002-10-01",
  "birthCountry": "DE",
  "birthCity": "Berlin",
  "citizenship": "DE",
  "documentType": "passport",
  "documentNumber": "aze423",
  "ownedSharesAmount": "15",
  "ownedSharesUnit": "%",
  "directRights": "Udziałowiec",
  "politicallyExposed": "no",
  "politicallyExposedFamily": "no",
  "politicallyExposedCoworker": "no",
  "withoutExpirationDate": true
}
```

---

## Reprezentanci (zarząd)

**Dodawane w tablicy `boardMembers`**

Reprezentanci to osoby upoważnione do działania w imieniu firmy:
- Prezesi, wiceprezesi zarządu
- Członkowie zarządu
- Prokurenci
- Inne osoby z pełnomocnictwami

### Parametry podstawowe reprezentanta

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **firstName** | TAK | Imię | `"Jan"` |
| **lastName** | TAK | Nazwisko | `"Kowalski"` |
| **personalIdentityNumber** | WARUNKOWO* | PESEL | `"31111161119"` |
| **birthDate** | WARUNKOWO** | Data urodzenia | `"2001-01-01"` |
| **birthCountry** | WARUNKOWO** | Kraj urodzenia | `"PL"` |
| **birthCity** | NIE | Miejsce urodzenia | `"Warszawa"` |
| **citizenship** | NIE | Obywatelstwo | `"PL"` |

**Wymagalność:**
- `*` Jedno z dwóch jest wymagane: `personalIdentityNumber`
- `**` Wymagane gdy brak PESEL

### Dokument tożsamości reprezentanta

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **documentType** | TAK | Typ dokumentu | `"id_card"` |
| **documentNumber** | TAK | Numer dokumentu | `"GLD358884"` |
| **documentIssueCountry** | TAK | Kraj wydania | `"PL"` |
| **documentExpirationDate** | TAK | Data ważności | `"2026-05-15"` |
| **withoutExpirationDate** | NIE | Dokument bezterminowy? | `true`/`false` |

### Rola reprezentanta

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **roleType** | TAK | Typ roli | `"president"` |
| **description** | WARUNKOWO* | Opis roli | `"Prezes zarządu"` |

**Wymagalność:**
- `*` Wymagane gdy `roleType: "other"`

#### Typy ról (roleType)

| Kod | Nazwa |
|:-----:|:-------:|
| `president` | Prezes |
| `board_member` | Członek zarządu |
| `proxy` | Prokurent |
| `other` | Inna rola |

### Dane PEP reprezentanta

| Parametr | Wymagane | Wartości | Opis |
|:----------:|:----------:|:----------:|:------:|
| **politicallyExposed** | TAK | `yes`, `no` | Czy reprezentant jest PEP? |
| **politicallyExposedFamily** | TAK | `yes`, `no` | Czy jest rodziną PEP? |
| **politicallyExposedCoworker** | TAK | `yes`, `no` | Czy jest współpracownikiem PEP? |

### Przykład reprezentanta z PESEL

```json
{
  "firstName": "Jan",
  "lastName": "Kowalski",
  "personalIdentityNumber": "31111161119",
  "documentType": "id_card",
  "documentNumber": "GLD358884",
  "documentIssueCountry": "PL",
  "citizenship": "PL",
  "birthDate": "2001-01-01",
  "birthCountry": "PL",
  "birthCity": "Warszawa",
  "roleType": "president",
  "description": "Prezes zarządu",
  "politicallyExposed": "no",
  "politicallyExposedFamily": "no",
  "politicallyExposedCoworker": "no",
  "withoutExpirationDate": true
}
```

### Przykład reprezentanta bez PESEL

```json
{
  "firstName": "Adam",
  "lastName": "Nowak",
  "birthDate": "2001-01-01",
  "birthCountry": "PL",
  "birthCity": "Warszawa",
  "citizenship": "PL",
  "documentType": "id_card",
  "documentNumber": "OBG470534",
  "roleType": "other",
  "description": "Wiceprezes",
  "politicallyExposed": "yes",
  "politicallyExposedFamily": "no",
  "politicallyExposedCoworker": "no",
  "withoutExpirationDate": true
}
```

---

## Dane kontaktowe i adresy firmy

### Typy adresów

| Parametr | Opis | Wymagane |
|:----------:|:------:|:----------:|
| **businessAddress** | Adres prowadzenia działalności | NIE |

### Struktura adresu

| Parametr | Wymagane | Opis |
|:----------:|:----------:|:------:|
| **country** | NIE | Kraj (kod ISO) |
| **region** | NIE | Region/Województwo |
| **city** | NIE | Miasto |
| **street** | NIE | Ulica |
| **houseNumber** | NIE | Numer domu |
| **flatNumber** | NIE | Numer mieszkania |
| **postalCode** | NIE | Kod pocztowy |

**Przykład:**
```json
{
  "country": "PL",
  "city": "Warszawa",
  "street": "Sienna",
  "houseNumber": "86",
  "flatNumber": "47",
  "postalCode": "00-131"
}
```

### Typy kontaktów

| Parametr | Opis | Wymagane |
|:----------:|:------:|:----------:|
| **companyContact** | Kontakt firmowy | NIE |

### Struktura kontaktu

| Parametr | Wymagane | Opis |
|:----------:|:----------:|:------:|
| **emailAdress** | NIE | Adres email |
| **phoneCountry** | NIE | Prefix kraju |
| **phoneNumber** | NIE | Numer telefonu |

**Przykład:**
```json
{
  "emailAdress": "info@fiberpay.pl",
  "phoneCountry": "48",
  "phoneNumber": "222302622"
}
```

---

## Przykład - Kompletna firma

```json
{
  "type": "company",
  "status": "active",
  "registrationCountry": "PL",
  "companyName": "FiberPay Sp. z o.o.",
  "tradeNames": ["FiberPay", "SystemAML"],
  "taxIdNumber": "7010634566",
  "nationalBusinessRegistryNumber": "147302566",
  "nationalCourtRegistryNumber": "0000512707",
  "businessActivityForm": "limited_liability_company",
  "listedOnStock": "no",
  "website": "fiberpay.pl",
  "economicRelationStartDate": "2023-11-14",
  "references": "qwerty",
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
      "firstName": "Jan",
      "lastName": "Kowalski",
      "personalIdentityNumber": "64091098920",
      "documentType": "id_card",
      "documentNumber": "JET449773",
      "documentIssueCountry": "PL",
      "citizenship": "PL",
      "birthCountry": "PL",
      "birthCity": "Warszawa",
      "ownedSharesAmount": "45",
      "ownedSharesUnit": "%",
      "directRights": "Wspólnik spółki",
      "directRightsPrivilegeType": "brak",
      "politicallyExposed": "no",
      "politicallyExposedFamily": "no",
      "politicallyExposedCoworker": "no",
      "withoutExpirationDate": true,
      "accommodationAddress": {
        "country": "PL",
        "city": "Warszawa",
        "street": "Nowa",
        "houseNumber": "1",
        "postalCode": "00-001"
      }
    },
    {
      "firstName": "Hans",
      "lastName": "Podolski",
      "birthDate": "2002-10-01",
      "birthCountry": "DE",
      "birthCity": "Berlin",
      "citizenship": "DE",
      "documentType": "passport",
      "documentNumber": "aze423",
      "ownedSharesAmount": "15",
      "ownedSharesUnit": "%",
      "directRights": "Udziałowiec",
      "politicallyExposed": "no",
      "politicallyExposedFamily": "no",
      "politicallyExposedCoworker": "no",
      "withoutExpirationDate": true
    }
  ],
  "boardMembers": [
    {
      "firstName": "Jan",
      "lastName": "Kowalski",
      "personalIdentityNumber": "31111161119",
      "documentType": "id_card",
      "documentNumber": "GLD358884",
      "documentIssueCountry": "PL",
      "citizenship": "PL",
      "birthDate": "2001-01-01",
      "birthCountry": "PL",
      "birthCity": "Warszawa",
      "roleType": "president",
      "description": "Prezes zarządu",
      "politicallyExposed": "no",
      "politicallyExposedFamily": "no",
      "politicallyExposedCoworker": "no",
      "withoutExpirationDate": true
    },
    {
      "firstName": "Adam",
      "lastName": "Nowak",
      "birthDate": "2001-01-01",
      "birthCountry": "PL",
      "birthCity": "Warszawa",
      "citizenship": "PL",
      "documentType": "id_card",
      "documentNumber": "OBG470534",
      "roleType": "other",
      "description": "Wiceprezes",
      "politicallyExposed": "yes",
      "politicallyExposedFamily": "no",
      "politicallyExposedCoworker": "no",
      "withoutExpirationDate": true
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
    "emailAdress": "info@fiberpay.pl",
    "phoneCountry": "48",
    "phoneNumber": "222302622"
  }
}
```

---

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
        "street": "Sienna",
        "houseNumber": "86",
        "flatNumber": "47",
        "postalCode": "00-815",
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

#### 2.1.2. GET /parties

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


#### 2.1.3. GET /parties/{code}

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
        "street": "Sienna",
        "houseNumber": "86",
        "flatNumber": "47",
        "postalCode": "00-815",
        "createdAt": "2023-11-20T16:49:44.000000Z"
      },
      {
        "code": "uv63gb9h57wc",
        "type": "business_address",
        "country": "PL",
        "city": "Warszawa",
        "street": "Sienna",
        "houseNumber": "86",
        "flatNumber": "47",
        "postalCode": "00-815",
        "createdAt": "2023-11-20T16:49:44.000000Z"
      },
      {
        "code": "ewrjvy2b17f9",
        "type": "accommodation_address",
        "country": "PL",
        "city": "Warszawa",
        "street": "Sienna",
        "houseNumber": "86",
        "flatNumber": "47",
        "postalCode": "00-815",
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

#### 2.1.4. POST /parties/{code}/status

Zmiana statusu podmiotu.

Endpoint umożliwia aktualizację statusu podmiotu w systemie. Status podmiotu określa jego aktualny stan w relacji z systemem AML.

#### Parametry URL

| Parametr | Wymagane | Opis |
|:----------:|:----------:|:------:|
| **code** | TAK | Unikalny kod identyfikujący podmiot |

#### Parametry body (JSON)

| Parametr | Typ | Wymagane | Wartości | Opis |
|:----------:|:-----:|:----------:|:----------:|:------:|
| **newStatus** | string | TAK | `draft`, `active`, `occasional`, `inactive`, `in_acceptance` | Nowy status podmiotu |

#### Dozwolone statusy

| Status | Opis |
|:--------:|:------:|
| `draft` | Szkic - podmiot w trakcie tworzenia |
| `active` | Aktywny - podmiot w pełnej współpracy |
| `occasional` | Okazjonalny - podmiot występujący incydentalnie |
| `inactive` | Nieaktywny - podmiot zakończył współpracę |
| `in_acceptance` | W akceptacji - podmiot oczekuje na weryfikację |

#### Zachowanie systemu

Zmiana statusu podmiotu powoduje:
- **Utworzenie zdarzenia systemowego** w historii podmiotu
- **Wysłanie webhooka** `party_status_change` (jeśli skonfigurowany)
- **Aktualizację kolejki screeningu** list sankcyjnych (dla statusu `active`)
- **Walidację unikalności** identyfikatorów (PESEL/NIP)

> **Uwaga:** Jeśli nowy status jest taki sam jak obecny, system zwraca podmiot bez zmian.

#### Przykładowe dane do zmiany statusu

```json
{
  "newStatus": "active"
}
```

#### Przykładowa odpowiedź serwera

**STATUS 200 OK**

```json
{
  "data": {
    "code": "abc123xyz456",
    "type": "individual",
    "status": "active",
    "riskStatus": "pending",
    "firstName": "Jan",
    "lastName": "Kowalski",
    "personalIdentityNumber": "09271573233",
    "economicRelationStartDate": "2023-11-14T00:00:00.000000Z"
  }
}
```

#### Możliwe błędy

**STATUS 400 Bad Request**
```json
{
  "status": "ERROR",
  "error": "The new status field is required."
}
```

**STATUS 404 Not Found**
```json
{
  "status": "ERROR",
  "error": "Party not found"
}
```

**STATUS 422 Unprocessable Entity**
```json
{
  "status": "ERROR",
  "error": "The selected new status is invalid."
}
```

---

#### 2.1.5. DELETE /parties/{code}

Usunięcie podmiotu wskazanego kodem identyfikującym.

---

### 2.2. Beneficjenci

#### 2.2.1. POST /parties/{code}/beneficiaries

Dodaje beneficjenta rzeczywistego do podmiotu typu `company`.

> **Uwaga:** Endpoint dostępny tylko dla podmiotów typu `company` (osoba prawna).

---

#### Parametry podstawowe beneficjenta

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **firstName** | TAK | Imię beneficjenta | `"Jan"` |
| **lastName** | TAK | Nazwisko beneficjenta | `"Bożek"` |
| **personalIdentityNumber** | WARUNKOWO* | PESEL | `"65122666817"` |
| **birthDate** | WARUNKOWO* | Data urodzenia | `"1985-06-15"` |
| **birthCountry** | NIE | Kraj urodzenia | `"FR"` |
| **birthCity** | NIE | Miejsce urodzenia | `"Paryż"` |
| **citizenship** | NIE | Obywatelstwo | `"PL"` |

**Wymagalność:**
- `*` PESEL lub `birthDate` - jedno z nich jest wymagane

---

#### Dokument tożsamości beneficjenta

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **documentType** | NIE | Typ dokumentu | `"id_card"` |
| **documentNumber** | WARUNKOWO* | Numer dokumentu | `"PVL852925"` |
| **documentIssueCountry** | WARUNKOWO* | Kraj wydania | `"PL"` |
| **documentExpirationDate** | WARUNKOWO* | Data ważności | `"2026-05-15"` |
| **withoutExpirationDate** | NIE | Dokument bezterminowy? | `true`/`false` |

**Wymagalność:**
- `*` Wymagane gdy podano `documentType`

---

#### Uprawnienia beneficjenta

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **ownedSharesAmount** | NIE | Liczba udziałów | `"5"` |
| **ownedSharesUnit** | NIE | Jednostka | `"%"` lub `"PLN"` |
| **directRights** | NIE | Bezpośrednie uprawnienia | `"Wspólnik spółki"` |
| **directRightsPrivilegeType** | NIE | Rodzaj uprzywilejowania | `"brak"` |
| **directRightsPrivilegeDescription** | NIE | Opis uprzywilejowania | `"brak"` |
| **indirectRights** | NIE | Pośrednie uprawnienia | `"brak"` |
| **otherRights** | NIE | Inne uprawnienia | `"brak"` |
| **otherRightsDescription** | NIE | Opis innych uprawnień | `"brak"` |
| **additionalInformation** | WARUNKOWO* | Dodatkowe informacje | `"brak"` |

**Wymagalność:**
- `*` Wymagane gdy wszystkie powyższe uprawnienia są puste

---

#### Dane PEP beneficjenta

| Parametr | Wymagane | Wartości | Opis |
|:----------:|:----------:|:----------:|:------:|
| **politicallyExposed** | TAK | `yes`, `no` | Czy beneficjent jest PEP? |
| **politicallyExposedFamily** | TAK | `yes`, `no` | Czy jest rodziną PEP? |
| **politicallyExposedCoworker** | TAK | `yes`, `no` | Czy jest współpracownikiem PEP? |

---

#### Adres beneficjenta

| Parametr | Wymagane | Opis |
|:----------:|:----------:|:------:|
| **accommodationAddress** | NIE | Obiekt z adresem zamieszkania |

**Struktura adresu:**
```json
{
  "country": "PL",
  "city": "Krakow",
  "street": "Kazimierza",
  "houseNumber": "41",
  "flatNumber": "10",
  "postalCode": "20-131"
}
```

---

### Przykład - Dodanie beneficjenta z PESEL

```json
{
  "birthCountry": "FR",
  "directRights": "Wspólnik spółki",
  "birthCity": "Paryż",
  "citizenship": "PL",
  "documentNumber": "PVL852925",
  "documentType": "id_card",
  "documentIssueCountry": "PL",
  "withoutExpirationDate": true,
  "firstName": "Jan",
  "lastName": "Bożek",
  "ownedSharesAmount": "5",
  "ownedSharesUnit": "%",
  "personalIdentityNumber": "65122666817",
  "politicallyExposed": "no",
  "politicallyExposedCoworker": "no",
  "politicallyExposedFamily": "no",
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

### Przykład - Dodanie beneficjenta bez PESEL

```json
{
  "firstName": "Hans",
  "lastName": "Schmidt",
  "birthDate": "1985-06-15",
  "birthCountry": "DE",
  "birthCity": "Berlin",
  "citizenship": "DE",
  "documentType": "passport",
  "documentNumber": "C01X00T47",
  "documentIssueCountry": "DE",
  "withoutExpirationDate": true,
  "ownedSharesAmount": "15",
  "ownedSharesUnit": "%",
  "directRights": "Udziałowiec",
  "politicallyExposed": "no",
  "politicallyExposedFamily": "no",
  "politicallyExposedCoworker": "no",
  "withoutExpirationDate": true
}
```

---

### Odpowiedź API

**STATUS 201 Created**

```json
{
  "data": {
    "code": "wn6hmp7e2x98",
    "ownedSharesAmount": "5.00",
    "ownedSharesUnit": "%",
    "description": null,
    "directRights": "Wspólnik spółki",
    "directRightsPrivilegeType": null,
    "directRightsPrivilegeDescription": null,
    "indirectRights": null,
    "otherRights": null,
    "otherRightsDescription": null,
    "additionalInformation": null,
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
        "politicallyExposedCoworker": "no",
        "politicallyExposedFamily": "no",
        "createdAt": "2023-08-24T15:51:27.000000Z",
        "birthDate": null
      },
      "accommodationAddress": {
        "code": "abc123xyz",
        "country": "PL",
        "city": "Krakow",
        "street": "Kazimierza",
        "houseNumber": "41",
        "flatNumber": "10",
        "postalCode": "20-131"
      }
    },
    "company": {
      "legalEntity": {
        "code": "cga7z8hf3j6r",
        "companyName": "FiberPay",
        "tradeNames": ["FiberPay"],
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
          "pkdName": "POZOSTAŁA FINANSOWA DZIAŁALNOŚĆ USŁUGOWA",
          "mainPkd": true
        }
      },
      "addresses": [
        {
          "code": "9fn2jukbtv7q",
          "type": "business_address",
          "country": "PL",
          "city": "Warszawa",
          "street": "Sienna",
          "houseNumber": "86",
          "flatNumber": "47",
          "postalCode": "00-815",
          "createdAt": "2023-08-24T15:48:19.000000Z"
        }
      ],
      "contacts": [
        {
          "code": "5b1gk4jpe9fn",
          "type": "company",
          "emailAdress": "info@fiberpay.pl",
          "phoneCountry": "48",
          "phoneNumber": "222302622",
          "createdAt": "2023-08-24T15:48:19.000000Z"
        }
      ]
    }
  }
}
```

#### 2.2.2. GET /parties/{code}/beneficiaries

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

#### 2.2.3. DELETE /beneficiaries/{code}

Usunięcie beneficjenta rzeczywistego wskazanego kodem identyfikującym.

---

### 2.3. Reprezentanci

#### 2.3.1. POST /parties/{code}/boardmembers

Dodaje reprezentanta (członka zarządu) do podmiotu typu `company`.

> **Uwaga:** Endpoint dostępny tylko dla podmiotów typu `company` (osoba prawna).

---

#### Parametry podstawowe reprezentanta

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **firstName** | TAK | Imię reprezentanta | `"Jan"` |
| **lastName** | TAK | Nazwisko reprezentanta | `"Nowak"` |
| **personalIdentityNumber** | WARUNKOWO* | PESEL | `"97120824889"` |
| **birthDate** | WARUNKOWO** | Data urodzenia | `"2002-01-01"` |
| **birthCountry** | WARUNKOWO** | Kraj urodzenia | `"PL"` |
| **birthCity** | NIE | Miejsce urodzenia | `"Warszawa"` |
| **citizenship** | NIE | Obywatelstwo | `"PL"` |

**Wymagalność:**
- `*` Jedno z dwóch jest wymagane: `personalIdentityNumber`
- `**` Wymagane gdy brak PESEL

---

#### Dokument tożsamości reprezentanta

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **documentType** | TAK | Typ dokumentu | `"id_card"` |
| **documentNumber** | WARUNKOWO* | Numer dokumentu | `"XIK941595"` |
| **documentIssueCountry** | WARUNKOWO* | Kraj wydania | `"PL"` |
| **documentExpirationDate** | WARUNKOWO* | Data ważności | `"2026-05-15"` |
| **withoutExpirationDate** | NIE | Dokument bezterminowy? | `true`/`false` |

**Wymagalność:**
- `*` Wymagane gdy podano `documentType`

---

#### Rola reprezentanta

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **roleType** | TAK | Typ roli | `"president"` |
| **description** | WARUNKOWO* | Opis roli | `"Prezes zarządu"` |

**Wymagalność:**
- `*` Wymagane gdy `roleType: "other"`

#### Typy ról (roleType)

| Kod | Nazwa |
|:-----:|:-------:|
| `president` | Prezes |
| `board_member` | Członek zarządu |
| `proxy` | Prokurent |
| `other` | Inna rola |

---

#### Dane PEP reprezentanta

| Parametr | Wymagane | Wartości | Opis |
|:----------:|:----------:|:----------:|:------:|
| **politicallyExposed** | TAK | `yes`, `no` | Czy reprezentant jest PEP? |
| **politicallyExposedFamily** | TAK | `yes`, `no` | Czy jest rodziną PEP? |
| **politicallyExposedCoworker** | TAK | `yes`, `no` | Czy jest współpracownikiem PEP? |

---

#### Dodatkowe parametry

| Parametr | Wymagane | Opis |
|:----------:|:----------:|:------:|
| **references** | NIE | Referencje własne |

---

### Przykład - Dodanie reprezentanta z PESEL

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
  "politicallyExposedCoworker": "no",
  "politicallyExposedFamily": "no",
  "roleType": "proxy",
  "withoutExpirationDate": true
}
```

### Przykład - Dodanie reprezentanta bez PESEL

```json
{
  "firstName": "Hans",
  "lastName": "Schmidt",
  "birthDate": "1985-06-15",
  "birthCountry": "DE",
  "birthCity": "Berlin",
  "citizenship": "DE",
  "documentType": "passport",
  "documentNumber": "C01X00T47",
  "documentIssueCountry": "DE",
  "roleType": "board_member",
  "description": "Członek zarządu",
  "politicallyExposed": "no",
  "politicallyExposedFamily": "no",
  "politicallyExposedCoworker": "no",
  "withoutExpirationDate": true
}
```

---

### Odpowiedź API

**STATUS 201 Created**

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
      "politicallyExposedCoworker": "no",
      "politicallyExposedFamily": "no",
      "createdAt": "2023-11-20T16:42:39.000000Z",
      "birthDate": null
    },
    "company": {
      "legalEntity": {
        "code": "jx86zfnrhks9",
        "companyName": "FiberPay",
        "tradeNames": ["FiberPay"],
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
          "pkdName": "POZOSTAŁA FINANSOWA DZIAŁALNOŚĆ USŁUGOWA",
          "mainPkd": true
        }
      },
      "addresses": [
        {
          "code": "38z5gseqyp6t",
          "type": "business_address",
          "country": "PL",
          "city": "Warszawa",
          "street": "Sienna",
          "houseNumber": "86",
          "flatNumber": "47",
          "postalCode": "00-815",
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

#### 2.3.2. GET /parties/{code}/boardmembers

Pobranie reprezentantów wskazanego kodem podmiotu typu company.

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

#### 2.3.3. DELETE /boardmembers/{code}

Usunięcie reprezentanta wskazanego kodem identyfikującym.

## 3. Transakcje

### 3.1. POST /transactions

Tworzy nową transakcję w systemie.

---

### Parametry podstawowe transakcji

| Parametr | Wymagane | Wartości | Opis |
|:----------:|:----------:|:----------:|:------:|
| **type** | TAK | `buyer`, `seller`, `transfer`, `other`, `seller_crypto`, `buyer_crypto`, `exchange_fiat` | Typ transakcji |
| **status** | TAK | `draft`, `in_acceptance`, `accepted`, `cancelled` | Status transakcji |
| **occasionalTransaction** | TAK | `true`, `false` | Czy transakcja jest okazjonalna? |

---

### Parametry dla transakcji okazjonalnych

**Wymagane gdy `occasionalTransaction: true`**

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **amount** | TAK | Kwota transakcji | `"100.00"` |
| **currency** | TAK | Waluta (kod ISO) | `"PLN"` |
| **bookedAt** | TAK | Data zaksięgowania | `"2024-05-07T13:03:48.000000Z"` |
| **paymentMethod** | TAK | Sposób płatności | `"cash"` |
| **paymentMethodOther** | WARUNKOWO* | Opis innego sposobu płatności | - |
| **title** | TAK | Tytuł transakcji | `"Zakup usług"` |
| **location** | NIE | Kraj przeprowadzenia | `"PL"` |
| **description** | NIE | Opis transakcji | - |
| **references** | NIE | Referencje własne | - |
| **createdByName** | NIE | Osoba wprowadzająca | - |
| **entities** | NIE | Tablica stron transakcji | Zobacz [Strony transakcji](#strony-transakcji) |

**Wymagalność:**
- `*` Wymagane gdy `paymentMethod: "other"`

---

### Typy transakcji i wymagane strony

| Typ transakcji | Wymagane strony | Opis |
|:----------------:|:-----------------:|:------:|
| `buyer` | `seller` | Zakup od kontrahenta |
| `seller` | `buyer` | Sprzedaż kontrahentowi |
| `transfer` | `payer`, `receiver` | Transfer środków |
| `seller_crypto` | `buyer_crypto` | Sprzedaż kryptowalut |
| `buyer_crypto` | `seller_crypto` | Zakup kryptowalut |
| `exchange_fiat` | `exchange_fiat_client` | Wymiana walut fiat |
| `other` | `receiver`, `seller`, `buyer` lub `payer` | Inna transakcja |

---

## Strony transakcji

**Dodawane w tablicy `entities`**

### Parametry podstawowe strony

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **type** | TAK | Typ strony | `"seller"` |
| **typeOther** | WARUNKOWO* | Opis innego typu | - |
| **partyCode** | NIE | Kod podmiotu z systemu | `"abc123xyz"` |
| **firstName** | WARUNKOWO** | Imię | `"Jan"` |
| **lastName** | WARUNKOWO** | Nazwisko | `"Kowalski"` |
| **companyName** | WARUNKOWO** | Nazwa firmy | `"FiberPay Sp. z o.o."` |
| **description** | NIE | Opis strony | - |

**Wymagalność:**
- `*` Wymagane gdy `type: "other"`
- `**` Wymagane gdy brak `partyCode`

#### Typy stron transakcji (type)

| Kod | Nazwa | Użycie |
|:-----:|:-------:|:--------:|
| `receiver` | Odbiorca | Transfer |
| `seller` | Sprzedawca | Zakup |
| `buyer` | Kupujący | Sprzedaż |
| `payer` | Płatnik | Transfer |
| `buyer_crypto` | Kupujący krypto | Sprzedaż kryptowalut |
| `seller_crypto` | Sprzedawca krypto | Zakup kryptowalut |
| `exchange_fiat_client` | Klient kantoru | Wymiana walut |
| `other` | Inna rola | Niestandardowa transakcja |

---

### Parametry dodatkowe strony

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **amount** | NIE | Kwota dla tej strony | `"100.00"` |
| **currency** | NIE | Waluta | `"PLN"` |
| **iban** | WARUNKOWO* | Numer IBAN | `"PL61109010140000071219812874"` |
| **ip** | NIE | Adres IP | `"192.168.1.1"` |

**Wymagalność:**
- `*` Wymagane gdy `paymentMethod: "bank_transfer"`

---

## Transakcje kryptowalutowe

**Dodatkowe parametry dla typów: `buyer_crypto`, `seller_crypto`**

### Parametry kryptowalutowe strony

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **currencyCustom** | NIE | Czy waluta niestandardowa? | `true`/`false` |
| **currencyOther** | NIE | Nazwa niestandardowej waluty | `"DOGE"` |
| **currencyType** | NIE | Typ waluty krypto | `"cryptocurrency"` |
| **txId** | NIE | ID transakcji blockchain | `"0x123..."` |
| **cryptoAddress** | NIE | Adres portfela | `"1A1zP1eP5QGefi..."` |
| **isEntityAWalletOwner** | TAK | Czy podmiot jest właścicielem portfela? | `"yes"`, `"no"` |
| **addressDataFromRelatedParty** | WARUNKOWO* | Czy dane adresowe takie same jak podmiotu? | `"yes"`, `"no"` |

**Wymagalność:**
- `*` Wymagane gdy podano `partyCode`

---

### Dane VASP (Virtual Asset Service Provider)

**Parametry dla portfeli hostowanych przez VASP**

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **isVASPEntity** | NIE | Czy portfel hostowany przez VASP? | `"yes"`, `"no"` |
| **vaspName** | WARUNKOWO* | Nazwa VASP | `"Binance"` |
| **vaspLeix** | WARUNKOWO** | VASP LEIX | - |
| **vaspRaid** | WARUNKOWO** | VASP RAID | - |
| **vaspTxid** | WARUNKOWO** | VASP TXID | - |
| **vaspMisc** | WARUNKOWO** | VASP MISC | - |
| **vaspCustomerId** | WARUNKOWO** | ID klienta VASP | - |

**Wymagalność:**
- `*` Wymagane gdy `isVASPEntity: "yes"`
- `**` Co najmniej jedno z pól jest wymagane gdy `isVASPEntity: "yes"`

---

### Dane właściciela portfela

**Obiekt `walletOwnerData`**

**Wymagane gdy `isEntityAWalletOwner: "no"`**

#### Parametry podstawowe

| Parametr | Wymagane | Opis | Przykład |
|:----------:|:----------:|:------:|:----------:|
| **partyType** | TAK | Typ właściciela | `"individual"`, `"sole_proprietorship"`, `"company"` |
| **firstName** | WARUNKOWO* | Imię | `"Jan"` |
| **lastName** | WARUNKOWO* | Nazwisko | `"Kowalski"` |
| **companyName** | WARUNKOWO** | Nazwa firmy | `"FiberPay Sp. z o.o."` |

**Wymagalność:**
- `*` Wymagane gdy `partyType: "individual"` lub `"sole_proprietorship"`
- `**` Wymagane gdy `partyType: "company"` lub `"sole_proprietorship"`

#### Adres właściciela

**Wymagane gdy `isEntityAWalletOwner: "no"` lub `addressDataFromRelatedParty: "no"`**

| Parametr | Wymagane | Opis |
|:----------:|:----------:|:------:|
| **country** | TAK | Kraj (kod ISO) |
| **region** | NIE | Region/Województwo |
| **city** | TAK | Miasto |
| **street** | TAK | Ulica |
| **houseNumber** | TAK | Numer domu |
| **flatNumber** | NIE | Numer mieszkania |
| **postalCode** | TAK | Kod pocztowy |

#### Identyfikacja podatkowa

**Wymagane dla firm gdy `isEntityAWalletOwner: "no"` lub `addressDataFromRelatedParty: "no"`**

| Parametr | Wymagane | Opis |
|:----------:|:----------:|:------:|
| **taxIdNumber** | WARUNKOWO* | NIP | `"7010634566"` |
| **taxIdCountry** | WARUNKOWO** | Kraj NIP | `"PL"` |

**Wymagalność:**
- `*` Wymagane gdy `partyType: "company"` lub `"sole_proprietorship"`
- `**` Wymagane gdy podano `taxIdNumber`

---

## Przykłady transakcji

### Przykład 1: Transakcja standardowa (zakup)

```json
{
  "type": "buyer",
  "status": "accepted",
  "amount": "100.00",
  "currency": "PLN",
  "paymentMethod": "cash",
  "location": "PL",
  "bookedAt": "2024-05-07T13:03:48.000000Z",
  "title": "Zakup usług konsultingowych",
  "occasionalTransaction": false,
  "entities": [
    {
      "type": "seller",
      "firstName": "Adam",
      "lastName": "Kowalski"
    }
  ]
}
```

### Przykład 2: Transakcja kryptowalutowa

```json
{
  "type": "buyer_crypto",
  "status": "accepted",
  "amount": "5000.00",
  "currency": "PLN",
  "paymentMethod": "bank_transfer",
  "location": "PL",
  "bookedAt": "2024-05-07T13:03:48.000000Z",
  "title": "Zakup Bitcoin",
  "occasionalTransaction": false,
  "entities": [
    {
      "type": "seller_crypto",
      "firstName": "Satoshi",
      "lastName": "Nakamoto",
      "amount": "0.15",
      "currency": "BTC",
      "currencyCustom": false,
      "currencyType": "crypto",
      "txId": "0x123abc...",
      "cryptoAddress": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa",
      "isEntityAWalletOwner": "no",
      "isVASPEntity": "no",
      "walletOwnerData": {
        "partyType": "individual",
        "firstName": "Satoshi",
        "lastName": "Nakamoto",
        "country": "JP",
        "city": "Tokyo",
        "street": "Main Street",
        "houseNumber": "123",
        "postalCode": "100-0001"
      }
    }
  ]
}
```

### Przykład 3: Transakcja z VASP

```json
{
  "type": "seller_crypto",
  "status": "accepted",
  "amount": "10000.00",
  "currency": "PLN",
  "paymentMethod": "bank_transfer",
  "bookedAt": "2024-05-07T13:03:48.000000Z",
  "title": "Sprzedaż Ethereum",
  "occasionalTransaction": false,
  "entities": [
    {
      "type": "buyer_crypto",
      "firstName": "Hans",
      "lastName": "Schmidt",
      "amount": "5",
      "currency": "ETH",
      "currencyType": "cryptocurrency",
      "cryptoAddress": "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb",
      "isEntityAWalletOwner": "no",
      "isVASPEntity": "yes",
      "vaspName": "Binance",
      "vaspCustomerId": "USER123456",
      "walletOwnerData": {
        "partyType": "individual",
        "firstName": "Hans",
        "lastName": "Schmidt",
        "country": "DE",
        "city": "Berlin",
        "street": "Unter den Linden",
        "houseNumber": "1",
        "postalCode": "10117"
      }
    }
  ]
}
```

### 3.2. GET /transactions

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
      "type": "seller",
      "status": "accepted",
      "description": null,
      "title": "transakcja z klienta",
      "amount": "780.00",
      "currency": "PLN"
    },
    {
      "code": "td4r9v6weunk",
      "type": "seller",
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
      "type": "seller",
      "status": "accepted",
      "description": null,
      "title": "transakcja z klienta",
      "amount": "780.00",
      "currency": "PLN"
    }
  ]
}
```

### 3.3. GET /transactions/{code}

Pobranie szczegółów danej transakcji.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": {
    "code": "c1ehu5my3s97",
    "type": "seller",
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


### 3.4. POST /transactions/{code}/status

Zmiana statusu transakcji.

Endpoint umożliwia aktualizację statusu transakcji w systemie. Status transakcji określa jej obecny stan procesu weryfikacji i akceptacji.

#### Parametry URL

| Parametr | Wymagane | Opis |
|:----------:|:----------:|:------:|
| **code** | TAK | Unikalny kod identyfikujący transakcję |

#### Parametry body (JSON)

| Parametr | Typ | Wymagane | Wartości | Opis |
|:----------:|:-----:|:----------:|:----------:|:------:|
| **newStatus** | string | TAK | `draft`, `in_acceptance`, `accepted`, `cancelled` | Nowy status transakcji |

#### Dozwolone statusy

| Status | Opis |
|:--------:|:------:|
| `draft` | Szkic - transakcja w trakcie rejestracji |
| `in_acceptance` | W akceptacji - transakcja oczekuje na weryfikację |
| `accepted` | Zaakceptowana - transakcja zatwierdzona |
| `cancelled` | Anulowana - transakcja odrzucona/anulowana |

#### Zachowanie systemu

Zmiana statusu transakcji powoduje:
- **Aktualizację agregatu transakcji** - przeliczenie sum dla powiązanych podmiotów
- **Utworzenie zdarzenia systemowego** w historii transakcji
- **Wysłanie webhooka** `transaction_status_change` (jeśli skonfigurowany)
- **Dodanie podmiotów do kolejki screeningu** (dla statusów `accepted`/`in_acceptance`)
- **Utworzenie zdarzenia rejestracji** (przy pierwszej zmianie statusu)

> **Uwaga:** 
> - Zmiana statusu z `accepted`/`in_acceptance` na `draft`/`cancelled` powoduje **odjęcie kwoty z agregatu**
> - Zmiana statusu z `draft`/`cancelled` na `accepted`/`in_acceptance` powoduje **dodanie kwoty do agregatu**

#### Przykładowe dane do zmiany statusu

```json
{
  "newStatus": "accepted"
}
```

#### Przykładowa odpowiedź serwera

**STATUS 200 OK**

```json
{
  "data": {
    "code": "xyz789abc123",
    "type": "buyer",
    "status": "accepted",
    "riskStatus": "pending",
    "amount": 15000,
    "currency": "PLN",
    "paymentMethod": "transfer",
    "location": "Poland",
    "bookedAt": "2024-01-15T00:00:00.000000Z",
    "description": "Zakup nieruchomości",
    "entities": []
  }
}
```

#### Możliwe błędy

**STATUS 400 Bad Request**
```json
{
  "status": "ERROR",
  "error": "The new status field is required."
}
```

**STATUS 404 Not Found**
```json
{
  "status": "ERROR",
  "error": "Transaction not found"
}
```

**STATUS 422 Unprocessable Entity**
```json
{
  "status": "ERROR",
  "error": "The selected new status is invalid."
}
```

---

### 3.5. DELETE /transactions/{code}

Usunięcie transakcji wskazanej kodem identyfikującym.

---

## 4. Zdarzenia i zadania

### 4.1. Zdarzenia

#### 4.1.1. POST /history-events

Tworzenie nowego zdarzenia w systemie. Parametry żądania:

| Parametr            | Wymagane | Opis                                                          |
|:-------------------:|:--------:|:-------------------------------------------------------------:|
| **description**     | TAK      | Opis zdarzenia                                                |
| **significance**    | TAK      | Ważność zdarzenia. Aktualnie wspierane: info, warning, urgent |
| **party**           | NIE      | Obiekt zawierający kod powiązanego podmiotu                   |
| **transaction**     | NIE      | Obiekt zawierający kod powiązanej transakcji                  |
| **type**            | NIE      | Typ zgłoszenia. Aktualnie wspierane : user, system            |
| **occursAt**        | TAK      | Data wystąpienia zdarzenia                                    |
| **createdByName**   | NIE      | Osoba tworząca zdarzenie                                      |

Struktura obiektu party:

| Parametr        | Wymagane | Opis                              |
|:---------------:|:--------:|:---------------------------------:|
| **code**        | NIE      | Kod powiązanego podmiotu          |


Struktura obiektu transaction:

| Parametr        | Wymagane | Opis                              |
|:---------------:|:--------:|:---------------------------------:|
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

#### 4.1.2. GET /history-events

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

#### 4.1.3. GET /history-events/{code}

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

#### 4.1.4. DELETE /history-events/{code}

Usunięcie zdarzenia wskazanego kodem identyfikującym.

---

### 4.2. Komentarze do zdarzeń

#### 4.2.1. POST /comments

Tworzenie nowego komentarza do zdarzenia w systemie. Parametry żądania:

| Parametr      | Wymagane | Opis                                                           |
|:-------------:|:--------:|:--------------------------------------------------------------:|
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

#### 4.2.2. GET /history-events/{code}/comments

Zwraca komentarze przypisane do zdarzenia.

#### 4.2.3. DELETE /comments/{code}

Usunięcie komentarza wskazanego kodem identyfikującym.

---

### 4.3. Zadania

#### 4.3.1. POST /tasks

Tworzenie nowego zadania w systemie. Parametry żądania:

| Parametr            | Wymagane | Opis                                             |
|:-------------------:|:--------:|:------------------------------------------------:|
| **content**         | TAK      | Treść zadania                                    |
| **expirationDate**  | NIE      | Data przed którą zadanie powinno zostać wykonane |
| **alert**           | NIE      | Obiekt zawierający kod powiązanego alertu        |
| **party**           | NIE      | Obiekt zawierający kod powiązanego podmiotu      |
| **transaction**     | NIE      | Obiekt zawierający kod powiązanej transakcji     |
| **alertCode**       | NIE      | Kod alertu który będzie powiązany z zadaniem     |


Struktura obiektu party:

| Parametr        | Wymagane | Opis                              |
|:---------------:|:--------:|:---------------------------------:|
| **code**        | NIE      | Kod powiązanego podmiotu          |


Struktura obiektu transaction:

| Parametr        | Wymagane | Opis                              |
|:---------------:|:--------:|:---------------------------------:|
| **code**        | NIE      | Kod powiązanej transakcji         |

Struktura obiektu alert:

| Parametr        | Wymagane | Opis                              |
|:---------------:|:--------:|:---------------------------------:|
| **code**        | NIE      | Kod powiązanego alertu            |


#### Przykładowe dane do utworzenia zadania:

```json
{
  "content": "Utworzyć przykładowe zadanie do celów reprezentacyjnych w dokumentacji",
  "expirationDate": "2025-11-06"
}
```

#### Przykładowa odpowiedź serwera:

- **STATUS 201 CREATED**

```json
{
  "data": {
    "code": "dcsur627nf3w",
    "identifier": "Z-1",
    "content": "Utworzyć przykładowe zadanie do celów reprezentacyjnych w dokumentacji",
    "status": null,
    "alert": null,
    "party": null,
    "kycApplicant": null,
    "transaction": null,
    "expirationDate": "2025-11-06",
    "createdAt": "2025-11-05T09:34:56.000000Z",
    "type": null,
    "canEdit": true,
    "createdByName": "Jan Kowalski",
    "redirectTarget": null,
    "commentsCount": 0
  }
}
```

#### 4.3.2. GET /tasks

Pobranie zadań powiązanych z danym użytkownikiem.

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": [
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
        "lastName": "Kowalski"
      },
      "transaction": null,
      "expirationDate": null
    }
  ]
}
```

#### 4.3.3. GET /tasks/{code}

Pobranie szczegółów zadania wskazanego kodem.

#### Przykładowa odpowiedź serwera:

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

#### 4.3.4. DELETE /tasks/{code}

Usunięcie zadania wskazanego kodem identyfikującym.

---

### 4.4. Komentarze do zadań

#### 4.4.1. POST /tasks/{code}/comments

Tworzenie nowego komentarza do zadania w systemie. Parametry żądania:

| Parametr      | Wymagane | Opis                                                           |
|:-------------:|:--------:|:--------------------------------------------------------------:|
| **content**   | TAK      | Treść komentarza                                               |
| **occurredAt**| TAK      | Data wystąpienia komentarza                                    |

#### Przykładowe dane do utworzenia komentarza:

```json
{
  "content": "testowy komentarz",
  "occurredAt": "2025-11-03 15:52:40"
}
```

#### Przykładowa odpowiedź serwera:

- **STATUS 201 CREATED**

```json
{
  "data": {
    "code": "v772btyaydyz",
    "content": "testowy komentarz",
    "authorName": "Jan Kowalski",
    "userId": 101,
    "occurredAt": "2025-11-03T14:52:40.000000Z",
    "createdAt": "2025-11-03T14:52:52.000000Z",
    "updatedAt": "2025-11-03T14:52:52.000000Z"
  }
}
```

#### 4.4.2. GET /tasks/{code}/comments

Zwraca komentarze przypisane do zadania.

#### 4.4.3. DELETE /tasks/{code}/comments/{commentsCode}

Usunięcie komentarza wskazanego kodem identyfikującym oraz kodem identyfikującym zadanie.

#### 4.4.4. PATCH /tasks/{code}/comments/{commentsCode}

Aktualizacja komentarza wskazanego kodem identyfikującym oraz kodem identyfikującym zadanie.

| Parametr      | Wymagane | Opis                                                           |
|:-------------:|:--------:|:--------------------------------------------------------------:|
| **content**   | TAK      | Treść komentarza                                               |
| **occurredAt**| TAK      | Data wystąpienia komentarza                                    |

#### Przykładowe dane do aktualizacji komentarza:

```json
{
  "content": "aktualizacja komentarza",
  "occurredAt": "2025-11-04 15:24:10"
}
```

#### Przykładowa odpowiedź serwera:

- **STATUS 200 OK**

```json
{
  "data": {
    "code": "4hvard7z49eh",
    "content": "aktualizacja komentarza",
    "authorName": "Jan Kowalski",
    "occurredAt": "2025-08-04T13:24:10.000000Z",
    "createdAt": "2025-11-04T14:24:13.000000Z",
    "updatedAt": "2025-11-04T14:27:49.000000Z"
  }
}
```

---

## 5. Alerty

### 5.1. GET /alerts

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

### 5.2. GET /alerts/{code}

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

### 5.3. DELETE /alerts/{code}

Usunięcie alertu wskazanego kodem identyfikującym.

---

## 6. Compliance i weryfikacja

### 6.1. Listy sankcyjne

#### 6.1.1. POST /sanctions-lists/search

Weryfikuje podane dane względem globalnych list sankcyjnych (UE, UK, ONZ).

#### Kiedy używać?

- Przed nawiązaniem relacji biznesowej
- Podczas weryfikacji KYC/AML
- W procesie należytej staranności (due diligence)
- Przy regularnych przeglądach bazy klientów

#### Parametry żądania

| Parametr | Typ | Wymagane | Opis |
|:----------:|:-----:|:----------:|:------:|
| **entityType** | string | TAK | Typ weryfikowanego obiektu. Aktualnie akceptowane: individual, company, any, crypto_address, email, pesel, nip, regon, krs |
| **name** | string | WARUNKOWO | Pełna nazwa (wymagane gdy `entityType: "any"`) |
| **firstName** | string | WARUNKOWO | Imię (wymagane gdy `entityType: "individual"`) |
| **middleName** | string | NIE | Drugie i kolejne imiona |
| **lastName** | string | WARUNKOWO | Nazwisko (wymagane gdy `entityType: "individual"`) |
| **companyName** | string | WARUNKOWO | Nazwa firmy (wymagane gdy `entityType: "company"`) |
| **email** | string | WARUNKOWO | Adres email (wymagane gdy `entityType: "email"`) |
| **cryptoAddress** | string | WARUNKOWO | Adres portfela krypto (wymagane gdy `entityType: "crypto_address"`) |
| **pesel** | string | WARUNKOWO | Pesel (wymagane gdy `entityType: "pesel"`) |
| **nip** | string | WARUNKOWO | Nip (wymagane gdy `entityType: "nip"`) |
| **regon** | string | WARUNKOWO | Regon (wymagane gdy `entityType: "regon"`) |
| **krs** | string | WARUNKOWO | Krs (wymagane gdy `entityType: "krs"`) |

#### Typy weryfikacji (entityType)

| Wartość | Użycie | Wymagane pola |
|:---------:|:--------:|:---------------:|
| `individual` | Osoby fizyczne | `firstName`, `lastName` |
| `company` | Firmy/organizacje | `companyName` |
| `any` | Wyszukiwanie uniwersalne | `name` |
| `email` | Adresy email | `email` |
| `crypto_address` | Portfele kryptowalut | `cryptoAddress` |
| `pesel` | Numer PESEL | `pesel` |
| `nip` | Numer NIP | `nip` |
| `regon` | Numer REGON | `regon` |
| `krs` | Numer KRS | `krs` |

#### Przykłady żądań

**Osoba fizyczna:**
```json
{
  "entityType": "individual",
  "firstName": "Wladimir",
  "lastName": "Putin"
}
```

**Firma:**
```json
{
  "entityType": "company",
  "companyName": "Gazprom"
}
```

**Adres kryptowalutowy:**
```json
{
  "entityType": "crypto_address",
  "cryptoAddress": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa"
}
```

**Wyszukiwanie uniwersalne:**
```json
{
  "entityType": "any",
  "name": "Vladimir Putin"
}
```

#### Odpowiedź - Znaleziono dopasowanie

**STATUS 200 OK**

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
        "Vladimir POUTINE"
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

#### Odpowiedź - Brak dopasowania

**STATUS 200 OK**

```json
{
  "isMatch": false,
  "code": "abc123xyz789",
  "matchedEntities": []
}
```

#### Struktura odpowiedzi

| Pole | Typ | Opis |
|:------:|:-----:|:------:|
| **isMatch** | boolean | `true` - znaleziono dopasowanie<br>`false` - brak dopasowania |
| **code** | string | Unikalny identyfikator wyszukiwania (do pobrania raportu PDF) |
| **matchedEntities** | array | Lista znalezionych dopasowań |

Jeśli dane zostaną odnalezione zmienna **isMatch** przyjmuję wartość true (boolean) w przeciwnym wypadku przyjmuję wartość false (boolean)

#### Struktura obiektu matchedEntity

| Pole | Typ | Opis |
|:------:|:-----:|:------:|
| **listName** | string | Nazwa listy sankcyjnej |
| **name** | string | Główna nazwa wpisu |
| **aliases** | array | Lista aliasów i alternatywnych nazw |
| **recordType** | string | Typ wpisu (`individual`, `company`, `vessel`) |
| **sourceData** | object | Oryginalne dane źródłowe z listy |

#### Listy sankcyjne objęte weryfikacją

| Lista | Zakres | Aktualizacja |
|:-------:|:--------:|:--------------:|
| **EU Financial Sanctions** | Unia Europejska | Dzienna |
| **UK OFSI** | Wielka Brytania | Dzienna |
| **UN Consolidated List** | Organizacja Narodów Zjednoczonych | Dzienna |
| **OFAC SDN** | USA (Treasury) | Dzienna |



#### 6.1.2. GET /sanctions/{code}/pdf

Pobiera raport PDF z wynikami wyszukiwania na listach sankcyjnych.

#### Parametry

| Parametr | Typ | Wymagane | Opis |
|:----------:|:-----:|:----------:|:------:|
| **code** | string | TAK | Kod zwrócony przez endpoint `/sanctions-lists/search` |

#### Zawartość raportu PDF

Raport zawiera:

- Datę i czas wyszukiwania
- Przeszukane dane wejściowe
- Listę znalezionych dopasowań
- Szczegóły każdego dopasowania (lista, aliasy, dane źródłowe)

### 6.2. Proces KYC

#### 6.2.1. POST /parties/applicants

Utworzenie nowego formualarza KYC.

| Parametr      | Wymagane | Opis                                                           |
|:-------------:|:--------:|:--------------------------------------------------------------:|
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

#### 6.2.2. GET /parties/{code}/applicants

Pobranie listy aplikantów powiązanych z danym podmiotem. Parametry żądania:

| Parametr      | Wymagane | Opis                                                           |
|:-------------:|:--------:|:--------------------------------------------------------------:|
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

#### 6.2.3. GET /parties/{code}/applicants/current

Pobranie szczegółów aplikanta powiązanego z danym podmiotem, którego proces weryfikacji nie został zakończony. Parametry żądania:

| Parametr      | Wymagane | Opis                                                           |
|:-------------:|:--------:|:--------------------------------------------------------------:|
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

#### 6.2.4. GET /applicants/{code}

Pobranie szczegółów aplikanta wskazanego kodem. Parametry żądania:

| Parametr      | Wymagane | Opis                                                           |
|:-------------:|:--------:|:--------------------------------------------------------------:|
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


#### 6.2.5. DELETE /applicants/{code}

Usunięcie aplikanta wskazanego kodem.

#### 6.2.6. POST /applicants/{code}/acceptance

Akceptacja deklarowanych danych aplikanta wskazanego kodem.

**Uwaga:** Endpoint obsługuje dwa scenariusze:
- **Akceptacja** (`isAccepted: true`) - wymaga: `partyStatus`, `economicRelationStartDate`; opcjonalnie: `references`
- **Odrzucenie** (`isAccepted: false`) - wymaga: `reason`

Parametry żądania:

| Parametr                      | Typ     | Wymagane          | Opis                                                                               |
|:-----------------------------:|:-------:|:-----------------:|:----------------------------------------------------------------------------------:|
| **isAccepted**                | Boolean | TAK               | Decyzja o akceptacji weryfikacji                                                   |
| **reason**                    | String  | Przy odrzuceniu   | Powód odrzucenia weryfikacji                                                       |
| **partyStatus**               | String  | Przy akceptacji   | Status podmiotu: `draft`, `active`, `inactive`, `in_acceptance`                    |
| **economicRelationStartDate** | Date    | Przy akceptacji   | Data rozpoczęcia stosunków gospodarczych (format: YYYY-MM-DD)                      |
| **references**                | String  | NIE               | Referencje własne podmiotu (tylko przy akceptacji)                                 |

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