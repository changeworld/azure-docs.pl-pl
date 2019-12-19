---
title: Pokrycie geokodowania w Azure Maps | Microsoft Docs
description: Informacje o zapotrzebowaniu geokodowania w Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 999e545d49b11eef84d1a03401eb28cde36e82af
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991363"
---
# <a name="azure-maps-geocoding-coverage"></a>Pokrycie geokodowania Azure Maps

Podczas wyszukiwania lokalizacji z Azure Maps, usługa wyszukiwania przyjmuje terminy wyszukiwania i zwraca współrzędne geograficzne i długości geograficznej, proces o nazwie geokodowania. Mapy nie mają jednak tego samego poziomu informacji i dokładności dla wszystkich regionów i krajów. Skorzystaj z tego artykułu, aby określić, jakiego rodzaju lokalizacje można niezawodnie wyszukiwać w poszczególnych regionach. 

Możliwość geokodowania w kraju/regionie jest zależna od pokrycia danych drogowych oraz dokładności geokodowanej usługi geokodowania. Poniższe kategoryzacje służą do określania poziomu obsługi geokodowania w poszczególnych krajach/regionach.
* **Punkty adresów** — dane dotyczące adresów można rozpoznać na współrzędnej szerokości/długości geograficznej w ramach paczki adresowej (granica właściwości). Czasami określana jako "Rooftop". Jest to najwyższy poziom dokładności dostępny dla adresów. 
* **Numery domów** — adresy są interpolowane na współrzędnej szerokości geograficznej na ulicy.
* Adresy **poziomu ulic** są rozpoznawane jako Współrzędna szerokości/długości geograficznej ulicy, która zawiera adres. Nie można przetworzyć numeru domu.
* Nazwy miejsc miasta na **poziomie miejscowości** są obsługiwane.

## <a name="americas"></a>Ameryki

| Kraj/region                                       | Punkty adresów | Numery domów | Poziom ulicy | Poziom miasta | Punkty orientacyjne |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarktyda                                          |                 |                |              |      ✓     |          ✓         |
| Antigua i Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentyna                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamy                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermudy                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Boliwia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Sint Eustatius i Saba                   |                 |                |              |      ✓     |          ✓         |
| Brazylia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kanada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kajmany                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Kolumbia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kostaryka                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Kuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Mini                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominikana                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ekwador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Salwador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Falklandy                                    |                 |                |              |      ✓     |          ✓         |
| Gujana Francuska                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Gwadelupa                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gwatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Gujana                                              |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamajka                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martynika                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Meksyk                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nikaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paragwaj                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Portoryko                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Kitts i Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Lucia                                         |                 |                |              |      ✓     |          ✓         |
| Saint-Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint-Pierre i Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Vincent i Grenadyny                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Georgia Południowa i Sandwich Południowy        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trynidad i Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Odległe Mniejsze Wyspy Stanów Zjednoczonych                |                 |                |              |      ✓     |          ✓         |
| Stany Zjednoczone Ameryki                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Urugwaj                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wenezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Brytyjskie Wyspy Dziewicze                              |                 |                |              |      ✓     |          ✓         |
| Wyspy Dziewicze Stanów Zjednoczonych                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Azja i Pacyfik

| Kraj/region                                      | Punkty adresów |Numery domów | Poziom ulicy | Poziom miasta | Punkty orientacyjne |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Samoa Amerykańskie                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Australia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesz                                          |                 |                |              |      ✓     |          ✓         |
| Bhutan                                              |                 |                |              |      ✓     |          ✓         |
| Brytyjskie Terytorium Oceanu Indyjskiego                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kambodża                                            |                 |                |              |      ✓     |          ✓         |
| Chiny                                               |                 |                |              |      ✓     |          ✓         |
| Wyspa Bożego Narodzenia                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Wyspy Kokosowe (Keelinga)                             |                 |                |              |      ✓     |          ✓         |
| Komory                                             |                 |                |              |      ✓     |          ✓         |
| Wyspy Cooka                                        |                 |                |              |      ✓     |          ✓         |
| Fidżi                                                |                  |                |              |      ✓     |          ✓        |
| Polinezja Francuska                                    |                 |                |              |      ✓     |          ✓         |
| Wyspy Heard i McDonald                   |                 |                |              |      ✓     |          ✓         |
| Hongkong SAR                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonezja                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indie                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japonia                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Korea Południowa                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| Makau SAR                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malezja                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mikronezja                                          |                 |                |              |      ✓     |          ✓         |
| Mongolia                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepal                                               |                 |                |              |      ✓     |          ✓         |
| Nowa Kaledonia                                       |                 |                |              |      ✓     |          ✓         |
| Nowa Zelandia                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Wyspa Norfolk                                      |                 |                |              |      ✓     |          ✓         |
| Korea Północna                                         |                 |                |              |      ✓     |          ✓         |
| Mariany Północne                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papua Nowa Gwinea                                    |                 |                |              |      ✓     |          ✓         |
| Filipiny                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Zachodniego                                               |                 |                |              |      ✓     |          ✓         |
| Wyspy Senkaku/Diaoyutai                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapur                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wyspy Salomona                                     |                 |                |              |      ✓     |          ✓         |
| Kurils południowy                                     |        ✓        |                |              |      ✓     |          ✓         |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Tajwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tajlandia                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turks i Caicos                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Wietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis i Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europa

| Kraj/region                                      | Punkty adresów |Numery domów | Poziom ulicy | Poziom miasta | Punkty orientacyjne |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albania                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andora                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Armenia                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Austria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbejdżan                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Belgia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bośnia i Hercegowina                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bułgaria                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Białoruś                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Chorwacja                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cypr                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Republika Czeska                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dania                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estonia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wyspy Owcze                                       |                 |                |              |      ✓     |          ✓         |
| Finlandia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Francja                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gruzja                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Niemcy                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grecja                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Grenlandia                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Węgry                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Islandia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlandia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wyspa Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Włochy                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazachstan                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosowo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirgistan                                          |                 |                |              |      ✓     |          ✓         |
| Łotwa                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Litwa                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Luksemburg                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macedonia Północna                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mołdawia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Monako                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Czarnogóra                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Holandia                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norwegia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polska                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugalia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| \+ Azory i Maderę                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Rumunia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Federacja Rosyjska                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Serbia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Słowacja                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Słowenia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Hiszpania                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Szwecja                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Szwajcaria                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tadżykistan                                          |                 |                |              |      ✓     |          ✓         |
| Turcja                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkmenistan                                        |                 |                |              |      ✓     |          ✓         |
| Ukraina                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Zjednoczone Królestwo                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbekistan                                          |                 |                |              |      ✓     |          ✓         |
| Watykan                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Bliski Wschód i Afryka

| Kraj/region                                      | Punkty adresów |Numery domów | Poziom ulicy | Poziom miasta | Punkty orientacyjne |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afganistan                                         |                 |                |              |      ✓     |          ✓         |
| Algieria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrajn                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspa Bouveta                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Kamerun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspy Zielonego Przylądka                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Republika Środkowoafrykańska                            |                 |                |       ✓      |      ✓     |          ✓         |
| Czad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Konga                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d’Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Demokratyczna Republika Konga                    |                 |                |       ✓      |      ✓     |          ✓         |
| Dżibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egipt                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gwinea Równikowa, Republika                      |                 |                |       ✓      |      ✓     |          ✓         |
| Erytrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiopia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Francuskie Terytoria Południowe|                        |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Przybrzeżn                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gwinea                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Gwinea Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Iran                                                |                 |                |              |      ✓     |          ✓         |
| Irak                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Izrael                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Jordania                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Kenia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuwejt                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Liban                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagaskar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Malediwy                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspy Marshalla                                    |                 |                |              |      ✓     |          ✓         |
| Mauretania                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauritius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Majotta                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Maroko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambik                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigeria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Oman                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Katar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Reunion                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Rwanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspa Świętej Heleny                                        |                 |                |              |      ✓     |          ✓         |
| Arabia Saudyjska                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seszele                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somalia                                             |                 |                |              |      ✓     |          ✓         |
| Republika Południowej Afryki                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sudan południowy                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudan                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Suazi                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Syria                                               |                 |                |              |      ✓     |          ✓         |
| Wyspy Świętego Tomasza i Książęca                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzania                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunezja                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zjednoczone Emiraty Arabskie                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Maps geokodowania, zobacz strony referencyjne [wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) .

Dowiedz się więcej o [obszarach pokrycia usługi Traffic Maps](traffic-coverage.md). 

