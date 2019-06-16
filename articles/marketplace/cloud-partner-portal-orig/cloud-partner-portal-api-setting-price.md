---
title: Cennik oferty maszyny wirtualnej | Portal Azure Marketplace
description: W tym artykule wyjaśniono trzy metody określania cen oferty maszyny wirtualnej.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: dde3e04dc8f30ea5657139d50dd4456e5dfb57c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935722"
---
<a name="pricing-for-virtual-machine-offers"></a>Cennik ofert maszyn wirtualnych
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
| DZ         | Algeria              | DZD               |
| AR         | Argentyna            | ARS               |
| AU         | Australia            | AUD               |
| AT         | Austria              | EUR               |
| BH         | Bahrajn              | BHD               |
| BY         | Białoruś              | RUB               |
| BE         | Belgia              | EUR               |
| BR         | Brazylia               | USD               |
| BG         | Bułgaria             | BGN               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Kolumbia             | COP               |
| CR         | Kostaryka           | CRC               |
| HR         | Chorwacja              | HRK               |
| CY         | Cypr               | EUR               |
| CZ         | Czechy       | CZK               |
| DK         | Dania              | DKK               |
| DO         | Republika Dominikańska   | USD               |
| EC         | Ekwador              | USD               |
| EG         | Egipt                | EGP               |
| SV         | Salwador          | USD               |
| EE         | Estonia              | EUR               |
| FI         | Finlandia              | EUR               |
| PW         | Francja               | EUR               |
| DE         | Niemcy              | EUR               |
| GR         | Grecja               | EUR               |
| GT         | Gwatemala            | GTQ               |
| HK         | SRA Hongkong        | HKD               |
| HU         | Węgry              | HUF               |
| IS         | Islandia              | ISK               |
| IN         | Indie                | INR               |
| ID         | Indonezja            | IDR               |
| IE         | Irlandia              | EUR               |
| IL         | Izrael               | ILS               |
| IT         | Włochy                | EUR               |
| JP         | Japonia                | JPY               |
| JO         | Jordania               | JOD               |
| KZ         | Kazakhstan           | KZT               |
| KE         | Kenya                | KES               |
| KR         | Korea                | KRW               |
| KW         | Kuwejt               | KWD               |
| LV         | Łotwa               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litwa            | EUR               |
| LU         | Luksemburg           | EUR               |
| MK         | Macedonia Północna      | MKD               |
| MY         | Malezja             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Meksyk               | MXN               |
| ME         | Czarnogóra           | EUR               |
| MA         | Maroko              | MAD               |
| NL         | Holandia          | EUR               |
| NZ         | Nowa Zelandia          | NZD               |
| NG         | Nigeria              | NGN               |
| NO         | Norwegia               | NOK               |
| OM         | Oman                 | OMA               |
| PK         | Pakistan             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paragwaj             | PYG               |
| PE         | Peru                 | PIÓRA               |
| PH         | Filipiny          | PHP               |
| PL         | Polska               | PLN               |
| PT         | Portugalia             | EUR               |
| PR         | Portoryko          | USD               |
| QA         | Katar                | QAR               |
| RO         | Rumunia              | PIOTR               |
| RU         | Rosja               | RUB               |
| SA         | Arabia Saudyjska         | SAR               |
| RS         | Serbia               | RSD               |
| SG         | Singapur            | SGD               |
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
| UA         | Ukraina              | UAH               |
| AE         | Zjednoczone Emiraty Arabskie | EUR               |
| GB         | Zjednoczone Królestwo       | GBP               |
| USA         | Stany Zjednoczone        | USD               |
| UY         | Urugwaj              | UYU               |
| VE         | Wenezuela            | USD               |
|  |  |  |
