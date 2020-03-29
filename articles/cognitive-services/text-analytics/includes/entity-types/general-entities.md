---
title: Ogólne nazwane jednostki
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77211438"
---
## <a name="general-entity-types"></a>Ogólne typy jednostek:

### <a name="person"></a>Person (Osoba)

Rozpoznawanie nazwisk osób w tekście.

Języki:
* Podgląd `Arabic`publiczny: `Czech`, , `Chinese-Simplified` `Finnish`, `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , `Dutch`, `English`, , , , , , , , , , , , , , , i `Danish``Turkish`

| Nazwa podtypu | Opis                                                      | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------------------|---------------------------------------|
| Nie dotyczy          | Rozpoznane nazwiska osób, na przykład `Bill Gates`,`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>Typ osoby
Typ lub rola zajmowana przez osobę.

Języki:
* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                                                                | Dostępne począwszy od wersji modelu |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Typy zadań, `salesperson` `chef`na `librarian`przykład `civil engineer`, , ,`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Lokalizacja

Naturalne i stworzone przez człowieka punkty orientacyjne, struktury, cechy geograficzne i jednostki geopolityczne.

Języki:

* Podgląd `Arabic`publiczny: `Czech`, , `Chinese-Simplified` `Finnish`, `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , `Dutch`, `English`, , , , , , , , , , , , , , , i `Danish``Turkish`

| Nazwa podtypu              | Opis                                                                              | Dostępne począwszy od wersji modelu |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| Nie dotyczy                       | lokalizacje, na `Atlantic Ocean` `library`przykład `Eiffel Tower`, ,`Statue of Liberty`  | `2019-10-01`                           |
| Podmiot geopolityczny (GPE) — tylko w języku angielskim| Miasta, kraje, państwa, `Pennsylvania` `South Africa`na przykład `Seattle`, ,`Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organizacja  

Uznane organizacje, korporacje, agencje i inne grupy ludzi. Na przykład: firmy, grupy polityczne, zespoły muzyczne, kluby sportowe, organy rządowe i organizacje publiczne. Narodowości i religie nie są uwzględnione w tym typie podmiotu. 

Języki: 

* Podgląd `Arabic`publiczny: `Czech`, , `Chinese-Simplified` `Finnish`, `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , `Dutch`, `English`, , , , , , , , , , , , , , , i `Danish``Turkish`

| Nazwa podtypu | Opis                                                                                             | Dostępne począwszy od wersji modelu |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | organizacje, na `Microsoft` `NASA`przykład `National Oceanic and Atmospheric Administration`, ,`VOA` | `2019-10-01`                           |

### <a name="event"></a>Wydarzenie  

Wydarzenia historyczne, społeczne i naturalne.  

Języki: 

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                                            | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Zdarzenia takie `wedding` `hurricane`jak `car accident` `solar eclipse`, , , ,`American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Product (Produkt)  

Obiekty fizyczne różnych kategorii.  

Języki: 

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                                                        | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Na przykład `Microsoft Surface laptop` `sunglasses`, `motorcycle` `bag`, ,`Xbox` | `2020-02-01`                           |
| Obliczanie    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Umiejętności  

Jednostka opisująca możliwości lub wiedzę fachową.  

Języki: 

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                                                 | Dostępne począwszy od wersji modelu |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Numer telefonu

Numery telefonów (tylko amerykańskie numery telefonów). 

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                    | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Amerykańskie numery telefonów, na przykład`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Adres e-mail

Adres e-mail. 

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                      | Dostępne począwszy od wersji modelu |
|--------------|--------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Adres e-mail, na przykład`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>Adres URL

Internetowe adresy URL.

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                          | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Adresy URL do stron internetowych, na przykład`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>Adres IP

Adres protokołu internetowego

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                              | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------|----------------------------------------|
| Nie dotyczy          | Adres sieciowy na przykład`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Jednostki daty i godziny. 

* Dostępne począwszy od wersji modelu`2019-10-01`

Języki:

* Podgląd `Chinese-Simplified`publiczny: `English` `French`, `German` , , i`Spanish`

| Nazwa podtypu    | Przykłady                     |
|-------------|------------------------------|
| Nie dotyczy         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Data  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| DataRange    | `August 2nd to August 5th`         |
| Układ czasu   | `4-6PM`, `10:00AM to Noon`          |
| Czas trwania | `2.5 minutes`, `one and a half hours`         |
| Set | `every Saturday`         |

###  <a name="quantity"></a>Liczba

Liczby i ilości liczbowe. 

* Dostępne począwszy od wersji modelu`2019-10-01`

Języki:

* Podgląd `Chinese-Simplified`publiczny: `English` `French`, `German` , , i`Spanish`

| Nazwa podtypu    | Przykłady                     |
|-------------|------------------------------|
| Liczba         | `6`, `six`                   |
| Procentowe  | `50%`, `fifty percent`       |
| Porządkowych     | `2nd`, `second`              |
| Wiek         | `90 day old`, `30 years old` |
| Waluta    | `$10.99`, `€30.00`           |
| Wymiar   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
