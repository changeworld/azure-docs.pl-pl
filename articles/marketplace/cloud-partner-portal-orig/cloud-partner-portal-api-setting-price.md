---
title: Ceny ofert maszyn wirtualnych | Azure Marketplace
description: W tym artykule wyjaśniono trzy metody określania cen ofert maszyn wirtualnych.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 487e66b39bc63363497cb3497d32158efd0c6c8a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255893"
---
<a name="pricing-for-virtual-machine-offers"></a>Cennik ofert maszyn wirtualnych
==================================

> [!NOTE]
> Interfejsy API portalu partnerów w chmurze są zintegrowane z centrum partnerów i będą nadal działać po migracji ofert do Centrum partnerów. Integracja wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [aplikacji Cloud Partner Portal API Reference,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) aby upewnić się, że kod będzie nadal działać po migracji do Centrum partnerów.

Istnieją trzy sposoby określania cen dla ofert maszyn wirtualnych: dostosowane ceny podstawowe, ceny za rdzeń i ceny arkuszy kalkulacyjnych.


<a name="customized-core-pricing"></a>Dostosowane ceny podstawowe
-----------------------

Ceny są specyficzne dla każdego regionu i kombinacji rdzenia. Każdy region na liście sprzedaży musi być określony w **virtualMachinePricing**/**regionuceny** sekcji definicji.  Użyj odpowiednich kodów walut dla każdego [regionu](#regions) w swoim żądaniu.  W poniższym przykładzie przedstawiono następujące wymagania:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Ceny za rdzeń
----------------

W takim przypadku wydawcy określają jedną cenę w USD za jednostkę SKU, a wszystkie inne ceny są generowane automatycznie. Cena za rdzeń jest określona w **pojedynczym** parametrze w żądaniu.

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


<a name="spreadsheet-pricing"></a>Ceny arkuszy kalkulacyjnych
-------------------

Wydawca może również przekazać arkusz kalkulacyjny cen do tymczasowej lokalizacji przechowywania, a następnie uwzględnić identyfikator URI w żądaniu, podobnie jak inne artefakty plików. Arkusz kalkulacyjny jest następnie przesyłany, tłumaczony w celu oceny określonego harmonogramu cen, a na koniec aktualizuje ofertę informacjami o cenach. Kolejne żądania GET dla oferty zwróci identyfikator URI arkusza kalkulacyjnego i ocenione ceny dla regionu.

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

<a name="new-core-sizes-added-on-722019"></a>Dodano nowe rozmiary rdzeni 7/2/2019
---------------------------

Wydawcy maszyn wirtualnych zostali powiadomieni 2 lipca 2019 r. o dodaniu nowych cen nowych rozmiarów maszyn wirtualnych platformy Azure (na podstawie liczby rdzeni).  Nowe ceny są dla podstawowych rozmiarów 10, 44, 48, 60, 120, 208 i 416.  W przypadku istniejących ofert maszyn wirtualnych nowe ceny dla tych rozmiarów rdzeni zostały automatycznie obliczone na podstawie cen bieżących.  Wydawcy mają czas do 1 sierpnia 2019 r., aby przejrzeć dodatkowe ceny i wprowadzić wszelkie pożądane zmiany.  Po tej dacie, jeśli wydawca nie został jeszcze ponownie opublikowany, automatycznie obliczone ceny dla tych nowych rozmiarów rdzenia zostaną zastosowane.


<a name="regions"></a>Regiony
-------

W poniższej tabeli przedstawiono różne regiony, które można określić dla podstawowych cen podstawowych, oraz odpowiadające im kody walut.

| **Region** | **Nazwa**             | **Kod waluty** |
|------------|----------------------|-------------------|
| DZ         | Algieria              | Dzd               |
| AR         | Argentyna            | ARS               |
| AU         | Australia            | AUD               |
| AT         | Austria              | EUR               |
| BH         | Bahrajn              | Bhd               |
| BY         | Białoruś              | RUB               |
| BE         | Belgia              | EUR               |
| BR         | Brazylia               | USD               |
| BG         | Bułgaria             | Bgn               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | Clp               |
| CO         | Kolumbia             | Cop               |
| CR         | Kostaryka           | Crc               |
| HR         | Chorwacja              | Hrk               |
| CY         | Cypr               | EUR               |
| CZ         | Czechy       | Czk               |
| DK         | Dania              | DKK               |
| DO         | Dominikana   | USD               |
| EC         | Ekwador              | USD               |
| EG         | Egipt                | Egp               |
| SV         | Salwador          | USD               |
| EE         | Estonia              | EUR               |
| FI         | Finlandia              | EUR               |
| PW         | Francja               | EUR               |
| DE         | Niemcy              | EUR               |
| GR         | Grecja               | EUR               |
| GT         | Gwatemala            | z o.o.               |
| HK         | SRA Hongkong        | HKD               |
| HU         | Węgry              | Huf               |
| IS         | Islandia              | Isk               |
| IN         | Indie                | INR               |
| ID         | Indonezja            | IDR               |
| IE         | Irlandia              | EUR               |
| IL         | Izrael               | Ils               |
| IT         | Włochy                | EUR               |
| JP         | Japonia                | JPY               |
| JO         | Jordania               | JOD (jod)               |
| KZ         | Kazachstan           | Kzt               |
| KE         | Kenia                | Kes               |
| KR         | Korea                | KRW               |
| KW         | Kuwejt               | Kwd               |
| LV         | Łotwa               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litwa            | EUR               |
| LU         | Luksemburg           | EUR               |
| MK         | Macedonia Północna      | Mkd               |
| MY         | Malezja             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Meksyk               | MXN               |
| ME         | Czarnogóra           | EUR               |
| MA         | Maroko              | Mad               |
| NL         | Holandia          | EUR               |
| NZ         | Nowa Zelandia          | NZD               |
| NG         | Nigeria              | Ngn               |
| NO         | Norwegia               | NOK               |
| OM         | Oman                 | Omr               |
| PK         | Pakistan             | Pkr               |
| PA         | Panama               | USD               |
| PY         | Paragwaj             | Pyg               |
| PE         | Peru                 | Pióro               |
| PH         | Filipiny          | PHP               |
| PL         | Polska               | Pln               |
| PT         | Portugalia             | EUR               |
| PR         | Portoryko          | USD               |
| QA         | Katar                | Qar               |
| RO         | Rumunia              | Ron               |
| RU         | Rosja               | RUB               |
| SA         | Arabia Saudyjska         | SAR               |
| RS         | Serbia               | Rsd               |
| SG         | Singapur            | Stochastyczny spadek gradientu               |
| SK         | Słowacja             | EUR               |
| SI         | Słowenia             | EUR               |
| ZA         | Republika Południowej Afryki         | ZAR               |
| ES         | Hiszpania                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Szwecja               | SEK               |
| CH         | Szwajcaria          | CHF               |
| TW         | Tajwan               | TWD               |
| TH         | Tajlandia             | Thb               |
| TT         | Trynidad i Tobago  | Ttd               |
| TN         | Tunezja              | Tnd               |
| TR         | Turcja               | TRY               |
| UA         | Ukraina              | Uah               |
| AE         | Zjednoczone Emiraty Arabskie | EUR               |
| GB         | Wielka Brytania       | GBP               |
| USA         | Stany Zjednoczone        | USD               |
| UY         | Urugwaj              | Uyu               |
| VE         | Wenezuela            | USD               |
|  |  |  |
