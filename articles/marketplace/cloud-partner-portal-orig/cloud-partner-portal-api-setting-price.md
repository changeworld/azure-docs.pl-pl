---
title: Cennik oferty maszyny wirtualnej | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono trzy metody określania cen oferty maszyny wirtualnej.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c78a54d5002972339994d9590c0a3e23b5c69bd9
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810530"
---
<a name="pricing-for-virtual-machine-offers"></a>Cennik oferty maszyny wirtualnej
==================================

Istnieją trzy sposoby, aby określić, cennik oferty maszyny wirtualnej: dostosowane cen core, ceny za Rdzeń oraz ceny arkusza kalkulacyjnego.


<a name="customized-core-pricing"></a>Podstawowe dostosowany cennik
-----------------------

Ceny są specyficzne dla każdej kombinacji region i core. Musi być określona co regionu na liście sprzedaży w **virtualMachinePricing**/**regionPrices** sekcja definicji.  Użyj kody walut poprawne dla każdego [region](#regions) w żądaniu.  W poniższym przykładzie pokazano te wymagania:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 2,
                    "1core": 2,
                    "2core": 3,
                    "4core": 4,
                    "6core": 5,
                    "8core": 2,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 4,
                    "36core": 4,
                    "40core": 4,
                    "64core": 4,
                    "128core": 4
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Ceny za rdzeń
----------------

W tym przypadku wydawców określ jedną cenę w USD w ramach ich jednostki SKU, a wszystkie inne ceny są generowane automatycznie. Cena za rdzeń jest określona w **pojedynczego** parametru w żądaniu.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Arkusz cen
-------------------

Wydawcy mogą również przekazywanie ich arkusza cen do tymczasowej lokalizacji przechowywania, a następnie zawierać identyfikator URI żądania, podobnie jak inne artefakty pliku. Arkusz kalkulacyjny następnie przekazaniu przetłumaczone do oceny, harmonogram określoną cenę i aktualizuje oferty z informacje o cenach. Kolejne żądania GET do oferty spowoduje zwrócenie identyfikatora URI w arkusza kalkulacyjnego i szacowane ceny dla regionu.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="regions"></a>Regiony
-------

W poniższej tabeli przedstawiono różnych regionach, że można określić dostosowanego podstawowe ceny i odpowiadające im kody walut.

| **Region** | **Nazwa**             | **Kod waluty** |
|------------|----------------------|-------------------|
| DZ         | Algieria              | DZD               |
| AR         | Argentyna            | ARS               |
| AU         | Australia            | AUD               |
| AT         | Austria              | EUR               |
| BH         | Bahrajn              | BHD               |
| PRZEZ         | Białoruś              | RUB               |
| MOŻNA         | Belgia              | EUR               |
| BRAZYLIA         | Brazylia               | USD               |
| BG         | Bułgaria             | BGN               |
| Urząd certyfikacji         | Kanada               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Kolumbia             | COP               |
| CR         | Kostaryka           | CRC               |
| Kadry         | Chorwacja              | HRK               |
| CY         | Cypr               | EUR               |
| CZECHY         | Republika Czeska       | CZK               |
| DK         | Dania              | DKK               |
| CZY         | Dominikana   | USD               |
| WE         | Ekwador              | USD               |
| NA PRZYKŁAD         | Egipt                | EGP               |
| SV         | Salwador          | USD               |
| EE         | Estonia              | EUR               |
| FI         | Finlandia              | EUR               |
| PW         | Francja               | EUR               |
| DE         | Niemcy              | EUR               |
| GR         | Grecja               | EUR               |
| GT         | Gwatemala            | GTQ               |
| HK         | SRA Hongkong        | HKD               |
| HU         | Węgry              | HUF               |
| JEST         | Islandia              | ISK               |
| INDIE         | Indie                | INR               |
| ID         | Indonezja            | IDR               |
| PROGRAMU INTERNET EXPLORER         | Irlandia              | EUR               |
| IL         | Izrael               | USŁUGI ILS               |
| IT         | Włochy                | EUR               |
| JP         | Japonia                | JPY               |
| JO         | Jordania               | JOD               |
| KZ         | Kazachstan           | KZT               |
| KE         | Kenia                | KES               |
| KR         | Korea                | KRW               |
| KW         | Kuwejt               | KWD               |
| LV         | Łotwa               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litwa            | EUR               |
| JEDNOSTKA LOGICZNA         | Luksemburg           | EUR               |
| MK         | Macedonia Północna       | MKD               |
| MOJE         | Malezja             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Meksyk               | MXN               |
| MNIE         | Czarnogóra           | EUR               |
| MA         | Maroko              | MAD               |
| HOLANDIA         | Holandia          | EUR               |
| NZ         | Nowa Zelandia          | NZD               |
| NG         | Nigeria              | NGN               |
| NIE         | Norwegia               | NOK               |
| OM         | Oman                 | OMA               |
| KLUCZ PODSTAWOWY         | Pakistan             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paragwaj             | PYG               |
| PE         | Peru                 | PIÓRA               |
| PH         | Filipiny          | PHP               |
| PL         | Polska               | PLN               |
| (CZAS PACYFICZNY)         | Portugalia             | EUR               |
| ŻĄDANIA ŚCIĄGNIĘCIA         | Portoryko          | USD               |
| ODPOWIEDZI NA PYTANIA         | Katar                | QAR               |
| RO         | Rumunia              | PIOTR               |
| JEDNOSTKA ŻĄDANIA         | Rosja               | RUB               |
| AMERYKA POŁUDNIOWA         | Arabia Saudyjska         | SAR               |
| RS         | Serbia               | RSD               |
| GRUPY ZABEZPIECZEŃ         | Singapur            | SGD               |
| SK         | Słowacja             | EUR               |
| SI         | Słowenia             | EUR               |
| ZA         | Republika Południowej Afryki         | ZAR               |
| ES         | Hiszpania                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Szwecja               | SEK               |
| CH         | Szwajcaria          | CHF               |
| TW         | Tajwan               | TWD               |
| TH         | Tajlandia             | THB               |
| TT         | Trynidad i Tobago  | TTD               |
| TN         | Tunezja              | TND               |
| TR         | Turcja               | TRY               |
| AGENT UŻYTKOWNIKA         | Ukraina              | UAH               |
| AE         | Zjednoczone Emiraty Arabskie | EUR               |
| GB         | Wielka Brytania       | GBP               |
| USA         | Stany Zjednoczone        | USD               |
| UY         | Urugwaj              | UYU               |
| VE         | Wenezuela            | USD               |
|  |  |  |
