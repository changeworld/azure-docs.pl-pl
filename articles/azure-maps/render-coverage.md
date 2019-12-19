---
title: Pokrycie renderowania w Azure Maps | Microsoft Docs
description: Dowiedz się więcej na temat pokrycia renderowania w Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5affc9952cfe13ebfeede126051f2236499d1343
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934294"
---
# <a name="azure-maps-render-coverage"></a>Pokrycie Azure Maps renderowania

Azure Maps używa zarówno kafelków rastrowych, jak i kafelków wektorowych do tworzenia map. W najniższym rozwiązaniu cały świat jest dopasowany do jednego kafelka. Przy najwyższej rozdzielczości pojedynczy kafelek reprezentuje 38 metrów kwadratowych. W miarę powiększania mapy można zobaczyć bardziej szczegółowe informacje dotyczące kontynentów, regionów, miast i indywidualnych ulic. Aby uzyskać więcej informacji, zobacz [poziomy powiększenia i siatka kafelków](zoom-levels-and-tile-grid.md).

Mapy nie mają jednak tego samego poziomu informacji i dokładności dla wszystkich regionów. W poniższych tabelach przedstawiono informacje o tym, jaki poziom renderowanych szczegółów można oczekiwać z każdego regionu.

## <a name="legend"></a>Legendy

| Symboliczn | Znaczenie |
|--------|---------|
| ✓ | Region jest reprezentowany ze szczegółowymi danymi.   |
| O | Region jest reprezentowany przez uproszczone dane. |


## <a name="africa"></a>Afryka 


| Kraj/region | Zunifikowane kafelki rastrowe | Zunifikowane kafelki wektorowe |
| ------ | :------------------: | :------------------: |
| Algieria                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botswana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Wyspy Zielonego Przylądka                       | ✓ | ✓ |
| Kamerun                         | ✓ | ✓ |
| Republika Środkowoafrykańska         | ✓ | O |
| Czad                             | ✓ | O |
| Komory                          | ✓ | O |
| Demokratyczna Republika Konga | ✓ | ✓ |
| Côte d’Ivoire                    | ✓ | O |
| Dżibuti                         | ✓ | O |
| Egipt                            | ✓ | ✓ |
| Gwinea Równikowa                | ✓ | O |
| Erytrea                          | ✓ | O |
| Etiopia                         | ✓ | O |
| Gabon                            | ✓ | ✓ |
| Przybrzeżn                           | ✓ | O |
| Ghana                            | ✓ | ✓ |
| Gwinea                           | ✓ | O |
| Gwinea Bissau                    | ✓ | O |
| Kenia                            | ✓ | ✓ |
| Lesotho                          | ✓ | ✓ |
| Liberia                          | ✓ | O |
| Libia                            | ✓ | O |
| Madagaskar                       | ✓ | O |
| Malawi                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauretania                       | ✓ | ✓ |
| Mauritius                        | ✓ | ✓ |
| Majotta                          | ✓ | ✓ |
| Maroko                          | ✓ | ✓ |
| Mozambik                       | ✓ | ✓ |
| Namibia                          | ✓ | ✓ |
| Niger                            | ✓ | ✓ |
| Nigeria                          | ✓ | ✓ |
| Reunion                          | ✓ | ✓ |
| Rwanda                           | ✓ | ✓ |
| Wyspa Świętej Heleny, Wyspa Wniebowstąpienia i Tristan da Cunha | ✓ | O |
| Wyspy Świętego Tomasza i Książęca            | ✓ | O |
| Senegal                          | ✓ | ✓ |
| Sierra Leone                     | ✓ | ✓ |
| Somalia                          | ✓ | ✓ |
| Republika Południowej Afryki                     | ✓ | ✓ |
| Sudan południowy                      | ✓ | ✓ |
| Sudan                            | ✓ | ✓ |
| Suazi                        | ✓ | ✓ |
| Zjednoczone Republiki Tanzanii      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunezja                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zambia                           | ✓ | ✓ |
| Zimbabwe                         | ✓ | ✓ |

## <a name="americas"></a>Ameryki

| Kraj/region | Zunifikowane kafelki rastrowe | Zunifikowane kafelki wektorowe |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antigua i Barbuda       | ✓ | ✓ |
| Argentyna                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamy                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermudy                   | ✓ | ✓ |
| Stan Plurinational Boliwii | ✓ | ✓ |
| Bonaire, Sint Eustatius i Saba | ✓ | ✓ |
| Brazylia                    | ✓ | ✓ |
| Kanada                    | ✓ | ✓ |
| Kajmany            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| Kolumbia                  | ✓ | ✓ |
| Kostaryka                | ✓ | ✓ |
| Kuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Mini                  | ✓ | ✓ |
| Dominikana        | ✓ | ✓ |
| Ekwador                   | ✓ | ✓ |
| Falklandy (Malwiny) | ✓ | ✓ |
| Gujana Francuska             | ✓ | ✓ |
| Grenlandia                 | ✓ | O |
| Grenada                   | ✓ | ✓ |
| Gwadelupa                | ✓ | ✓ |
| Gwatemala                 | ✓ | ✓ |
| Gujana                    | ✓ | ✓ |
| Haiti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamajka                   | ✓ | ✓ |
| Martynika                | ✓ | ✓ |
| Meksyk                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nikaragua                 | ✓ | ✓ |
| Mariany Północne  | ✓ | ✓ |
| Panama                    | ✓ | ✓ | 
| Paragwaj                  | ✓ | ✓ |
| Peru                      | ✓ | ✓ |
| Portoryko               | ✓ | ✓ |
| Quebec (Kanada)           | ✓ | ✓ |
| Saint Barthélemy          | ✓ | ✓ |
| Saint Kitts i Nevis     | ✓ | ✓ |
| Saint Lucia               | ✓ | ✓ |
| Saint-Martin (francuski)     | ✓ | ✓ |
| Saint-Pierre i Miquelon | ✓ | ✓ |
| Saint Vincent i Grenadyny | ✓ | ✓ |
| Sint Maarten (holenderski)      | ✓ | ✓ |
| Georgia Południowa i Sandwich Południowy | ✓ | ✓ |
| Surinam                  | ✓ | ✓ |
| Trynidad i Tobago       | ✓ | ✓ |
| Turks i Caicos  | ✓ | ✓ |
| Stany Zjednoczone             | ✓ | ✓ |
| Urugwaj                   | ✓ | ✓ |
| Wenezuela                 | ✓ | ✓ |
| Brytyjskie Wyspy Dziewicze   | ✓ | ✓ |
| Wyspy Dziewicze Stanów Zjednoczonych      | ✓ | ✓ |

## <a name="asia"></a>Azja 

| Kraj/region | Zunifikowane kafelki rastrowe | Zunifikowane kafelki wektorowe |
| ------ | :------------------: | :------------------: |
| Afganistan               |   | O |
| Bahrajn                   | ✓ | ✓ |
| Bangladesz                |   | O |
| Bhutan                    |   | O |
| Brytyjskie Terytorium Oceanu Indyjskiego |   | O |
| Brunei                    | ✓ | ✓ |
| Kambodża                  |   | O |
| Chiny                     |   | O |
| Wyspy Kokosowe (Keelinga)   |   | O |
| Koreańska Republika Ludowo-Demokratyczna |   | O |
| Hongkong SAR             | ✓ | ✓ |
| Indie                     | O | ✓ | 
| Indonezja                 | ✓ | ✓ |
| Iran                      |   | O |
| Irak                      | ✓ | ✓ |
| Izrael                    |   | ✓ |
| Japonia                     |   | O |
| Jordania                    | ✓ | ✓ |
| Kazachstan                |   | ✓ |
| Kuwejt                    | ✓ | ✓ |
| Kirgistan                |   | O |
| Laotańska Republika Ludowo-Demokratyczna |   | O |
| Liban                   | ✓ | ✓ |
| Makau SAR                 | ✓ | ✓ |
| Malezja                  | ✓ | ✓ |
| Malediwy                  |   | O |
| Mongolia                  |   | O |
| Myanmar                   |   | O |
| Nepal                     |   | O |
| Oman                      | ✓ | ✓ |
| Pakistan                  |   | O |
| Filipiny               | ✓ | ✓ |
| Katar                     | ✓ | ✓ |
| Republika Korei         | ✓ | O |
| Arabia Saudyjska              | ✓ | ✓ |
| Wyspy Senkaku/Diaoyutai           |   | ✓ |
| Singapur                 | ✓ | ✓|
| Sri Lanka                 |   | O |
| Arabska Republika Syryjska      |   | O |
| Tajwan                    | ✓ | ✓ |
| Tadżykistan                |   | O |
| Tajlandia                  | ✓ | ✓ |
| Timor — Wschodni               |   | O |
| Turkmenistan              |   | O |
| Zjednoczone Emiraty Arabskie      | ✓ | ✓ |
| Odległe Mniejsze Wyspy Stanów Zjednoczonych |   | O |
| Uzbekistan                |   | O |
| Wietnam                   | ✓ | ✓ |
| Jemen                     | ✓ | ✓ |

## <a name="oceania"></a>Oceanii

| Kraj/region | Zunifikowane kafelki rastrowe | Zunifikowane kafelki wektorowe |
| ------ | :------------------: | :------------------: |
| Samoa Amerykańskie            |   | ✓ |
| Australia                 | ✓ | ✓ |
| Wyspy Cooka              |   | O |
| Fidżi                      |   | O |
| Polinezja Francuska          |   | O |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | O |
| Wyspy Marshalla          |   | O |
| Mikronezja                |   | O |
| Nauru                     |   | O |
| Nowa Kaledonia             |   | O |
| Nowa Zelandia               | ✓ | ✓ |
| Niue                      |   | O |
| Wyspa Norfolk            |   | O |
| Palau                     |   | O |
| Papua Nowa Gwinea          |   | O |
| Pitcairn                  |   | O |
| Zachodniego                     |   | O |
| Wyspy Salomona           |   | O|
| Tokelau                   |   | O |
| Tonga                     |   | O |
| Tuvalu                    |   | O |
| Vanuatu                   |   | O |
| Wallis i Futuna         |   | O |


## <a name="europe"></a>Europa

| Kraj/region | Zunifikowane kafelki rastrowe | Zunifikowane kafelki wektorowe |
| ------ | :------------------: | :------------------: |
| Albania                   | ✓ | ✓ |
| Andora                   | ✓ | ✓ |
| Armenia                   | ✓ | O |
| Austria                   | ✓ | ✓ |
| Azerbejdżan                | ✓ | O |
| Białoruś                   | O | ✓ |
| Belgia                   | ✓ | ✓ |
| Bośnia i Hercegowina        | ✓ | ✓ |
| Bułgaria                  | ✓ | ✓ |
| Chorwacja                   | ✓ | ✓ |
| Cypr                    | ✓ | ✓ |
| Republika Czeska            | ✓ | ✓ |
| Dania                   | ✓ | ✓ |
| Estonia                   | ✓ | ✓ |
| Wyspy Owcze             | ✓ | O |
| Finlandia                   | ✓ | ✓ |
| Francja                    | ✓ | ✓ |
| Gruzja                   | ✓ | O |
| Niemcy                   | ✓ | ✓ |
| Gibraltar                 | ✓ | ✓ |
| Grecja                    | ✓ | ✓ |
| Guernsey                  | ✓ | ✓ |
| Węgry                   | ✓ | ✓ |
| Islandia                   | ✓ | ✓ |
| Irlandia                   | ✓ | ✓ |
| Wyspa Man               | ✓ | ✓ |
| Włochy                     | ✓ | ✓ |
| Jan Mayen                 | ✓ | ✓ |
| Jersey                    | ✓ | ✓ |
| Łotwa                    | ✓ | ✓ |
| Liechtenstein             | ✓ | ✓ |
| Litwa                 | ✓ | ✓ |
| Luksemburg                | ✓ | ✓ |
| Macedonia Północna           | ✓ | ✓ |
| Malta                     | ✓ | ✓ |
| Mołdawia                   | ✓ | ✓ |
| Monako                    | ✓ | ✓ |
| Czarnogóra                | ✓ | ✓ |
| Holandia               | ✓ | ✓ |
| Norwegia                    | ✓ | ✓ |
| Polska                    | ✓ | ✓ |
| Portugalia                  | ✓ | ✓ |
| Rumunia                   | ✓ | ✓ |
| Federacja Rosyjska        | ✓ | ✓ |
| San Marino                | ✓ | ✓ |
| Serbia                    | ✓ | ✓ |
| Słowacja                  | ✓ | ✓ |
| Słowenia                  | ✓ | ✓ |
| Kurils południowy           | ✓ | ✓ |
| Hiszpania                     | ✓ | ✓ |
| Svalbard                  | ✓ | ✓ |
| Szwecja                    | ✓ | ✓ |
| Szwajcaria               | ✓ | ✓ |
| Turcja                    | ✓ | ✓ |
| Ukraina                   | ✓ | ✓ |
| Zjednoczone Królestwo            | ✓ | ✓ |
| Watykan              | ✓ | ✓ |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat renderowania Azure Maps, zobacz [poziomy powiększenia i siatka kafelków](zoom-levels-and-tile-grid.md).

Dowiedz się więcej o [obszarach pokrycia dla usługi routingu Maps](routing-coverage.md). 