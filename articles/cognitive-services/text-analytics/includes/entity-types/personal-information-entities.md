---
title: Nazwane jednostki dla informacji osobistych
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77086908"
---
## <a name="personal-information-entity-types"></a>Typy jednostek informacji osobistych:

### <a name="person"></a>Person (Osoba)
Rozpoznawanie nazwisk osób w tekście.

Języki:
* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                               | Dostępne począwszy od wersji modelu |
|--------------|-----------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Rozpoznane nazwy, `Bill Gates`na przykład ,`Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organizacja  

Uznaj organizacje, korporacje, agencje, firmy, kluby i inne grupy ludzi.

Języki: 

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                                                                       | Dostępne począwszy od wersji modelu|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| Nie dotyczy          | organizacje, na `Microsoft` `NASA`przykład ,`National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Numer telefonu

Numery telefonów (tylko amerykańskie numery telefonów). 

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                    | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Amerykańskie numery telefonów, na przykład`(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>Adres e-mail

Adres e-mail. 

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                      | Dostępne począwszy od wersji modelu |
|--------------|--------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Adres e-mail, na przykład`support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>Adres URL

Internetowe adresy URL.

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                          | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Adresy URL do stron internetowych, na przykład`https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>Adres IP

Adres protokołu internetowego

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                              | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------|----------------------------------------|
| Nie dotyczy          | Adres sieciowy na przykład`10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Liczba 

Ilości liczbowe

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                   | Dostępne począwszy od wersji modelu |
|--------------|-------------------------------|----------------------------------------|
| Wiek          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>DateTime

Jednostki daty i godziny

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                   | Dostępne począwszy od wersji modelu |
|--------------|-------------------------------|----------------------------------------|
| Data         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>Współrzędne GPS UE

 Współrzędne GPS dla lokalizacji na terenie Unii Europejskiej. 

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                               | Dostępne począwszy od wersji modelu |
|--------------|-------------------------------------------|----------------------------------------|
| Nie dotyczy          | Współrzędne GPS w Unii Europejskiej | `2019-10-01`                           |

### <a name="azure-information"></a>Informacje o platformie Azure

Informacje platformy Azure umożliwiające identyfikację, w tym informacje o uwierzytelnianiu i parametry połączenia. 

* Dostępne począwszy `2019-10-01`od wersji modelu .

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu                          | Opis                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Klucz Auth usługi Azure DocumentDB             | Klucz autoryzacji dla serwera usługi Azure DocumentDB.                           |
| Parametry połączenia bazy danych usługi IAAS platformy Azure | Parametry połączenia dla bazy danych infrastruktury platformy Azure jako usługi (IaaS). |
| Ciąg połączenia SQL platformy Azure           | Parametry połączenia dla bazy danych SQL platformy Azure.                                |
| Ciąg połączenia IoT platformy Azure           | Parametry połączenia dla usługi Azure Internet of things(IoT).                        |
| Hasło ustawień publikowania platformy Azure        | Hasło do ustawień publikowania platformy Azure.                                        |
| Ciąg połączenia pamięci podręcznej usługi Azure Redis   | Parametry połączenia dla pamięci podręcznej platformy Azure dla redis.                             |
| Azure SAS                             | Parametry połączenia dla oprogramowania platformy Azure jako usługi (SAS).                     |
| Ciąg połączenia usługi Azure Service Bus   | Parametry połączenia dla magistrali usług Platformy Azure.                                |
| Klucz konta usługi Azure Storage             | Klucz konta dla konta magazynu platformy Azure.                                   |
| Klucz konta usługi Azure Storage (ogólny)   | Ogólny klucz konta dla konta magazynu platformy Azure.                           |
| Parametry połączenia programu SQL Server          | Parametry połączenia dla serwera SQL.                                         |

### <a name="identification"></a>Identyfikacja

* Dostępne począwszy `2019-10-01`od wersji modelu .

Języki:

* Publiczna wersja zapoznawcza:`English`

#### <a name="financial-account-identification"></a>Identyfikacja rachunku finansowego

| Nazwa podtypu               | Opis                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Numery routingu ABA        | American Banker Association (ABA) numery routingu tranzytowego.                  |
| Kod SWIFT                 | kody SWIFT do informacji o instrukcjach płatności.                           |
| Karta kredytowa                | Numery kart kredytowych.                                                       |
| Kod IBAN                  | kody IBAN dla informacji o instrukcjach płatności.                            |

#### <a name="government-and-country-specific-identification"></a>Identyfikacja dla instytucji rządowych i samorządowych

Poniższe jednostki są pogrupowane i wymienione według kraju:

Argentyna
* Numer tożsamości narodowej (DNI)

Australia
* Numer pliku podatku 
* Identyfikator prawa jazdy
* Identyfikator paszportu
* Numer konta medycznego
* numery kont bankowych (na przykład sprawdzanie, oszczędności i rachunki debetowe)

Belgia
* Numer krajowy

Brazylia
* Numer podmiotu prawnego (CNPJ)
* Numer CPF
* Krajowy dowód osobisty (RG)

Kanada
* Identyfikator paszportu
* Identyfikator prawa jazdy
* Numer ubezpieczenia zdrowotnego
* Osobisty numer do okazania obrażeń zdrowia (PHIN)
* Numer ubezpieczenia społecznego
* numery kont bankowych (na przykład sprawdzanie, oszczędności i rachunki debetowe)

Chile
* Numer dowodu osobistego 

Chiny
* Numer dowodu osobistego
* Numer dowodu osobistego rezydenta (ChRL)

Chorwacja
* Numer dowodu osobistego
* Numer osobistego identyfikatora (OIB)

Czechy
* Numer dowodu osobistego

Dania
* Osobisty numer do rzeczy

Unia Europejska (UE)
* Krajowy numer do nip
* Identyfikator paszportu
* Identyfikator prawa jazdy
* Numer ubezpieczenia społecznego (SSN) lub równoważny identyfikator
* Unijny numer identyfikacji podatkowej (NIP)
* Numer karty debetowej UE

Finlandia
* Krajowy numer do nip
* Identyfikator paszportu

Francja
* Krajowy dowód osobisty (CNI)
* Numer ubezpieczenia społecznego (INSEE)
* Identyfikator paszportu
* Identyfikator prawa jazdy

Niemcy
* Numer dowodu osobistego
* Identyfikator paszportu
* Identyfikator prawa jazdy

Grecja 
* Numer dowodu osobistego

Hongkong
* Numer dowodu osobistego (HKID)

Indie
* Numer konta stałego (PAN)
* Unikatowy numer ID (Aadhaar)

Indonezja
* Numer dowodu osobistego (KTP)

Irlandia
* Numer osobistej służby publicznej (PPS)

Izrael
* Identyfikator krajowy
* numery kont bankowych (na przykład sprawdzanie, oszczędności i rachunki debetowe)

Włochy
* Identyfikator prawa jazdy

Japonia
* Numer rejestracyjny rezydenta
* Numer karty pobytu
* Identyfikator prawa jazdy
* Numer ubezpieczenia społecznego (SIN)
* Identyfikator paszportu
* numery kont bankowych (na przykład sprawdzanie, oszczędności i rachunki debetowe)

Malezja
* Numer dowodu osobistego

Holandia
* Numer służby obywatelskiej (BSN)

Nowa Zelandia
* Numer Ministerstwa Zdrowia

Norwegia
* Numer dowodu osobistego

Filipiny
* Ujednolicony wielozadaniowy numer identyfikatora

Polska
* Numer dowodu osobistego
* Identyfikator krajowy (PESEL)
* Identyfikator paszportu

Portugalia 
* Numer karty obywatela

Arabia Saudyjska
* Identyfikator krajowy

Singapur
* Numer krajowego dowodu rejestracyjnego (NRIC)

Republika Południowej Afryki
* Numer ID
* Numer rejestracyjny rezydenta

Korea Południowa
* Numer rejestracyjny rezydenta

Hiszpania 
* Numer ubezpieczenia społecznego (SSN)

Szwecja
* Identyfikator krajowy
* Identyfikator paszportu

Tajwan 
* Identyfikator krajowy
* Numer certyfikatu rezydenta (ARC/TARC)
* Identyfikator paszportu

Tajlandia
* Kod identyfikacji populacji

Wielka Brytania
* Identyfikator paszportu
* Identyfikator prawa jazdy
* Krajowy numer ubezpieczenia (NINO)
* Numer Krajowej Służby Zdrowia (NHS)

Stany Zjednoczone
* Numer ubezpieczenia społecznego (SSN)
* Identyfikator prawa jazdy
* Identyfikator paszportu
* Numer spisu wyborców
* Indywidualny numer identyfikacji podatkowej (ITIN)
* Numer Agencji Ścigania Narkotyków (DEA)
* numery kont bankowych (na przykład sprawdzanie, oszczędności i rachunki debetowe)
