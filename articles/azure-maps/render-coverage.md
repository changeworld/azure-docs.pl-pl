---
title: Renderowanie pokrycia w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Więcej informacji na temat renderowania pokrycia w usługi Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7feea92d48a826000fd8aa878b95658b79d6d908
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869992"
---
# <a name="azure-maps-render-coverage"></a>Pokrycie renderowaniem usługi Azure Maps

Usługi Azure Maps używa rastrowych kafelków i wektorowych kafelków w celu utworzenia mapy. W jego najniższej rozdzielczości cały świat pasuje do pojedynczego kafelka. Jego największą rozdzielczością pojedynczego kafelka reprezentuje 38 metrów kwadratowych. Jak można powiększyć mapę, w związku z tym, widać coraz więcej szczegółów na temat kontynentach, regiony, miasta i poszczególnych Streets (ulice). Aby uzyskać więcej informacji, zobacz [poziomy powiększenia i siatka kafelków](zoom-levels-and-tile-grid.md).

Jednak mapy nie ma takiego samego poziomu informacji i dokładność we wszystkich regionach. Poniższe tabele zawierają informacje o poziom szczegółowości renderowanych można oczekiwać, że w każdym regionie.

## <a name="legend"></a>Legenda

| Symbol | Znaczenie |
|--------|---------|
| ✓ | Region jest przedstawiana za pomocą szczegółowych danych.   |
| Ø | Region jest przedstawiana za pomocą danych. |


## <a name="africa"></a>Afryka 


| Kraj/region | Kafelki rastrowych Unified | Wektorowych kafelków Unified |
| ------ | :------------------: | :------------------: |
| Algieria                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botswana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Wyspy Zielonego Przylądka                       | ✓ | ✓ |
| Kamerun                         | ✓ | ✓ |
| Republika Środkowoafrykańska         | ✓ | Ø |
| Czad                             | ✓ | Ø |
| Komory                          | ✓ | Ø |
| Demokratyczna Republika Konga | ✓ | ✓ |
| Côte d’Ivoire                    | ✓ | Ø |
| Dżibuti                         | ✓ | Ø |
| Egipt                            | ✓ | ✓ |
| Gwinea Równikowa                | ✓ | Ø |
| Erytrea                          | ✓ | Ø |
| Etiopia                         | ✓ | Ø |
| Gabon                            | ✓ | ✓ |
| Gambia                           | ✓ | Ø |
| Ghana                            | ✓ | ✓ |
| Gwinea                           | ✓ | Ø |
| Gwinea Bissau                    | ✓ | Ø |
| Kenia                            | ✓ | ✓ |
| Lesotho                          | ✓ | ✓ |
| Liberia                          | ✓ | Ø |
| Libia                            | ✓ | Ø |
| Madagaskar                       | ✓ | Ø |
| Malawi                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauretania                       | ✓ | ✓ |
| Mauritius                        | ✓ | ✓ |
| Wyspa Majotta                          | ✓ | ✓ |
| Maroko                          | ✓ | ✓ |
| Mozambik                       | ✓ | ✓ |
| Namibia                          | ✓ | ✓ |
| Niger                            | ✓ | ✓ |
| Nigeria                          | ✓ | ✓ |
| Réunion                          | ✓ | ✓ |
| Rwanda                           | ✓ | ✓ |
| Wyspa Świętej Heleny, Wyspa Wniebowstąpienia i Tristan da Cunha | ✓ | Ø |
| Wyspy Świętego Tomasza i Książęca            | ✓ | Ø |
| Senegal                          | ✓ | ✓ |
| Sierra Leone                     | ✓ | ✓ |
| Somalia                          | ✓ | ✓ |
| Republika Południowej Afryki                     | ✓ | ✓ |
| Sudan Południowy                      | ✓ | ✓ |
| Sudan                            | ✓ | ✓ |
| Suazi                        | ✓ | ✓ |
| Zjednoczona Republika Tanzanii      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunezja                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zambia                           | ✓ | ✓ |
| Zimbabwe                         | ✓ | ✓ |

## <a name="americas"></a>Ameryki

| Kraj/region | Kafelki rastrowych Unified | Wektorowych kafelków Unified |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antigua i Barbuda       | ✓ | ✓ |
| Argentyna                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamy                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermudy                   | ✓ | ✓ |
| Wielonarodowe Państwo Boliwii | ✓ | ✓ |
| Bonaire, Sint Eustatius i Saba | ✓ | ✓ |
| Brazylia                    | ✓ | ✓ |
| Kanada                    | ✓ | ✓ |
| Kajmany            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| Kolumbia                  | ✓ | ✓ |
| Kostaryka                | ✓ | ✓ |
| Kuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominika                  | ✓ | ✓ |
| Republika Dominikańska        | ✓ | ✓ |
| Ekwador                   | ✓ | ✓ |
| Falklandy | ✓ | ✓ |
| Gujana Francuska             | ✓ | ✓ |
| Grenlandia                 | ✓ | Ø |
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
| Saint Pierre i Miquelon | ✓ | ✓ |
| Saint Vincent i Grenadyny | ✓ | ✓ |
| Sint Maarten (holenderski)      | ✓ | ✓ |
| Georgia Południowa i Sandwich Południowy | ✓ | ✓ |
| Surinam                  | ✓ | ✓ |
| Trynidad i Tobago       | ✓ | ✓ |
| Wyspy Turks i Caicos  | ✓ | ✓ |
| Stany Zjednoczone             | ✓ | ✓ |
| Urugwaj                   | ✓ | ✓ |
| Wenezuela                 | ✓ | ✓ |
| Brytyjskie Wyspy Dziewicze   | ✓ | ✓ |
| Wyspy Dziewicze Stanów Zjednoczonych      | ✓ | ✓ |

## <a name="asia"></a>Azja 

| Kraj/region | Kafelki rastrowych Unified | Wektorowych kafelków Unified |
| ------ | :------------------: | :------------------: |
| Afganistan               |   | Ø |
| Bahrajn                   | ✓ | ✓ |
| Bangladesz                |   | Ø |
| Bhutan                    |   | Ø |
| Brytyjskie Terytorium Oceanu Indyjskiego |   | Ø |
| Brunei                    | ✓ | ✓ |
| Kambodża                  |   | Ø |
| Chiny                     |   | Ø |
| Wyspy Kokosowe (Keelinga)   |   | Ø |
| Koreańska Republika Ludowo-Demokratyczna |   | Ø |
| Dokdo/Takeshima       |   | Ø |
| SRA Hongkong             | ✓ | ✓ |
| Indie                     | Ø | ✓ | 
| Indonezja                 | ✓ | ✓ |
| Iran                      |   | Ø |
| Irak                      | ✓ | ✓ |
| Izrael                    |   | ✓ |
| Japonia                     |   | Ø |
| Jordania                    | ✓ | ✓ |
| Kazachstan                |   | ✓ |
| Kuwejt                    | ✓ | ✓ |
| Kirgistan                |   | Ø |
| Laotańska Republika Ludowo-Demokratyczna |   | Ø |
| Liban                   | ✓ | ✓ |
| SRA Makau                 | ✓ | ✓ |
| Malezja                  | ✓ | ✓ |
| Malediwy                  |   | Ø |
| Mongolia                  |   | Ø |
| Myanmar                   |   | Ø |
| Nepal                     |   | Ø |
| Oman                      | ✓ | ✓ |
| Pakistan                  |   | Ø |
| Filipiny               | ✓ | ✓ |
| Katar                     | ✓ | ✓ |
| Republika Korei         | ✓ | Ø |
| Arabia Saudyjska              | ✓ | ✓ |
| Wyspy Senkaku/Diaoyutai           |   | ✓ |
| Singapur                 | ✓ | ✓|
| Sri Lanka                 |   | Ø |
| Arabska Republika Syryjska      |   | Ø |
| Tajwan                    | ✓ | ✓ |
| Tadżykistan                |   | Ø |
| Tajlandia                  | ✓ | ✓ |
| Timor-Leste               |   | Ø |
| Turkmenistan              |   | Ø |
| Zjednoczone Emiraty Arabskie      | ✓ | ✓ |
| Odległe Mniejsze Wyspy Stanów Zjednoczonych |   | Ø |
| Uzbekistan                |   | Ø |
| Wietnam                   | ✓ | ✓ |
| Jemen                     | ✓ | ✓ |

## <a name="oceania"></a>Oceania

| Kraj/region | Kafelki rastrowych Unified | Wektorowych kafelków Unified |
| ------ | :------------------: | :------------------: |
| Samoa Amerykańskie            |   | ✓ |
| Australia                 | ✓ | ✓ |
| Wyspy Cooka              |   | Ø |
| Fidżi                      |   | Ø |
| Polinezja Francuska          |   | Ø |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | Ø |
| Wyspy Marshalla          |   | Ø |
| Mikronezja                |   | Ø |
| Nauru                     |   | Ø |
| Nowa Kaledonia             |   | Ø |
| Nowa Zelandia               | ✓ | ✓ |
| Niue                      |   | Ø |
| Wyspa Norfolk            |   | Ø |
| Palau                     |   | Ø |
| Papua Nowa Gwinea          |   | Ø |
| Pitcairn                  |   | Ø |
| Samoa                     |   | Ø |
| Wyspy Salomona           |   | Ø|
| Tokelau                   |   | Ø |
| Tonga                     |   | Ø |
| Tuvalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis i Futuna         |   | Ø |


## <a name="europe"></a>Europa

| Kraj/region | Kafelki rastrowych Unified | Wektorowych kafelków Unified |
| ------ | :------------------: | :------------------: |
| Albania                   | ✓ | ✓ |
| Andora                   | ✓ | ✓ |
| Armenia                   | ✓ | Ø |
| Austria                   | ✓ | ✓ |
| Azerbejdżan                | ✓ | Ø |
| Białoruś                   | Ø | ✓ |
| Belgia                   | ✓ | ✓ |
| Bosnia-Herzegovina        | ✓ | ✓ |
| Bułgaria                  | ✓ | ✓ |
| Chorwacja                   | ✓ | ✓ |
| Cypr                    | ✓ | ✓ |
| Czechy            | ✓ | ✓ |
| Dania                   | ✓ | ✓ |
| Estonia                   | ✓ | ✓ |
| Wyspy Owcze             | ✓ | Ø |
| Finlandia                   | ✓ | ✓ |
| Francja                    | ✓ | ✓ |
| Gruzja                   | ✓ | Ø |
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

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat renderowania usługi Azure Maps, zobacz [poziomy powiększenia i siatka kafelków](zoom-levels-and-tile-grid.md).

Dowiedz się więcej o [obszary pokrycia dla map, usługa routingu](routing-coverage.md). 