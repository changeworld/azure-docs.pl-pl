---
title: Geokodowanie pokrycia w lokalizacji platformy Azure na podstawie usług | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o Geokodowanie usługi na podstawie zapotrzebowania w lokalizacji platformy Azure
services: location-based-services
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: article
ms.service: location-based-services
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: b2ae0f2cb8eba6ca42b3c82458d1fadf4ea93cdf
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
---
# <a name="azure-location-based-services---geocoding-coverage"></a>Lokalizacja platformy Azure na podstawie usług - Geokodowanie pokrycia
Azure lokalizacji na podstawie usług (kg) zawiera informacje szczegółowe geokodowanie do wyszukiwania adresów, miejsca i inne jednostki geograficznej na świecie. Jest to ważne w przypadku klientów, którzy chcieli wiedzieć, gdzie można i nie może poszukiwać różnych poziomach wierności podczas wyszukiwania dla adresów w usłudze wyszukiwania. Podczas wyszukiwania regionów z pokryciem niskiej rozdzielczości, może spodziewać się niższym wyniki wyszukiwania zaufania. Poniższa tabela zawiera informacje o pokryciu w celu wykonania Geokodowania wyszukiwania kg Azure.

| Region             | Adres punktów<sup>1</sup>|Zawierają numery<sup>2</sup>| Ulica poziom | Poziom Miasto | Ważne |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| **Americas**                                            |                 |                |              |            |                    |
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarktyda                                          |                 |                |              |      ✓     |          ✓         |
| Antigua i Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentyna                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamy                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermudy                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Boliwia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Sint Eustatius i Saba|                 |                |              |      ✓     |          ✓         |
| Brazylia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kanada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kajmany                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Wyspy Clipperton                                   |                 |                |              |      ✓     |                    |
| Kolumbia                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kostaryka                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Kuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominika                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ekwador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Salwador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Falklandy                                    |                 |                |              |      ✓     |          ✓         |
| Gujana Francuska                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Gwadelupa|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gwatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamajka                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martynika                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Meksyk                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nikaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paragwaj                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Portoryko                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint-Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Kitts i Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Lucia                                         |                 |                |              |      ✓     |          ✓         |
| Saint-Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Pierre i Miquelon|                 |                |       ✓      |      ✓     |          ✓         |
| Saint Vincent i Grenadyny                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Georgia Południowa i Sandwich Południowy        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trynidad i Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Odległe Mniejsze Wyspy Stanów Zjednoczonych                |                 |                |              |      ✓     |          ✓         |
| Stany Zjednoczone Ameryki                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Urugwaj                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Wenezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspy Dziewicze - Brytyjskie                            |                 |                |              |      ✓     |          ✓         |
| Wyspy Dziewicze — Stany Zjednoczone                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| |                 |                |              |            |                    |
| **Azja i Pacyfik**                                        |                 |                |              |            |                    |
| Samoa Amerykańskie                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Australia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesz                                          |                 |                |              |      ✓     |          ✓         |
| Bhutan                                              |                 |                |              |      ✓     |          ✓         |
| Brytyjskie Terytorium Oceanu Indyjskiego                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kambodża                                            |                 |                |              |      ✓     |          ✓         |
| Chiny |                 |                |              |      ✓     |          ✓         |
| Wyspa Bożego Narodzenia                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Wyspy Kokosowe (Keelinga)|                 |                |              |      ✓     |          ✓         |
| Komory|                 |                |              |      ✓     |          ✓         |
| Wyspy Cooka                                        |                 |                |              |      ✓     |          ✓         |
| Fidżi |                 |                |              |      ✓     |          ✓         |
| Polinezja Francuska                                    |                 |                |              |      ✓     |          ✓         |
| Wyspy Heard i McDonalda                   |                 |                |              |      ✓     |          ✓         |
| Hongkong                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonezja                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Japonia                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| SRA Makau                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malezja                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mikronezja |                 |                |              |      ✓     |          ✓         |
| Mongolia                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Napal|                 |                |              |      ✓     |          ✓         |
| Nowa Kaledonia                                       |                 |                |              |      ✓     |          ✓         |
| Nowa Zelandia                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Wyspy Norfolk                                      |                 |                |              |      ✓     |          ✓         |
| Korea Północna                                         |                 |                |              |      ✓     |          ✓         |
| Mariany Północne                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau |                 |                |              |      ✓     |          ✓         |
| Papua Nowa Gwinea                                    |                 |                |              |      ✓     |          ✓         |
| Wyspy Paracel                                     |                 |                |              |      ✓     |                    |
| Filipiny                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Wyspy Senkaku/Diaoyutai                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapur                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wyspy Salomona                                     |                 |                |              |      ✓     |          ✓         |
| Korea Południowa                                         |                 |                |              |      ✓     |          ✓         |
| Kurils Terytoria Południowe                                     |        ✓        |                |              |      ✓     |          ✓         |
| Wyspy Spratly                                      |                 |                |              |      ✓     |                    |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Tajwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tajlandia                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turks i Caicos                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Wietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis i Futuna|                 |                |              |      ✓     |          ✓         |
| |                 |                |              |            |                    |
| **Europa**                                              |                 |                |              |            |                    |
| Albania                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andora                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Armenia                                             |                 |                |              |      ✓     |          ✓         |
| Austria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbejdżan                                          |                 |                |              |      ✓     |          ✓         |
| Belgia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bośnia i Hercegowina                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bułgaria                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Belerus|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Chorwacja                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cypr                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Republika Czeska                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dania                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estonia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wyspy Owcze                                       |                 |                |              |      ✓     |          ✓         |
| Finlandia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Francja                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gruzja                                             |                 |                |              |      ✓     |          ✓         |
| Niemcy                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibralter                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grecja                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grenlandia                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Węgry                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Islandia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlandii (Republika)                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wyspa Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Włochy                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazachstan                                          |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kosowo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirgistan                                          |                 |                |              |      ✓     |          ✓         |
| Łotwa                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Litwa                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Luksemburg                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macedonia                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mołdawia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Monako                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Czarnogóra                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Holandia                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norwegia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polska                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugalia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Rumunia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Federacja Rosyjska                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Serbia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Słowacja                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Słowenia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Hiszpania                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Szwecja                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Szwajcaria                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tadżykistan                                          |                 |                |              |      ✓     |          ✓         |
| Turcja                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkmenistan                                        |                 |                |              |      ✓     |          ✓         |
| Ukraina                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Wielka Brytania                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbekistan                                          |                 |                |              |      ✓     |          ✓         |
| Watykan                                        |                 |                |       ✓      |      ✓     |          ✓         |
| |                 |                |              |            |                    |
| **Bliski Wschód Afryka**                                  |                 |                |              |            |                    |
| Afganistan                                         |                 |                |              |      ✓     |          ✓         |
| Algieria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrajn                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspa Bouveta                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Kamerun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspy Zielonego Przylądka|                 |                |       ✓      |      ✓     |          ✓         |
| Republika Środkowoafrykańska                            |                 |                |       ✓      |      ✓     |          ✓         |
| Czad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Kongo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d’Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Demokratyczna Republika Konga                        |                 |                |       ✓      |      ✓     |          ✓         |
| Dżibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egipt                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gwinea Gwinea, Republika                      |                 |                |       ✓      |      ✓     |          ✓         |
| Erytrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiopia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Francuskie Terytoria Południowe i Antarktyczne|                 |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambia                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gwinea                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Gwinea Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Iran                                                |                 |                |              |      ✓     |          ✓         |
| Irak                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Izrael                                              |                 |                |              |      ✓     |          ✓         |
| Jordania                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kenia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuwejt                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Liban                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libia|                 |                |       ✓      |      ✓     |          ✓         |
| Madagaskar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Malediwy |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspy Marshalla |                 |                |              |      ✓     |          ✓         |
| Mauretania                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauritius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspa Majotta                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Maroko                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambik                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigeria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Oman                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Katar                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Réunion|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Rwanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Święta Helena                                        |                 |                |              |      ✓     |          ✓         |
| Arabia Saudyjska                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seszele                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somalia                                             |                 |                |              |      ✓     |          ✓         |
| Republika Południowej Afryki                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sudan Południowy                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudan                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Suazi                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Syria                                               |                 |                |              |      ✓     |          ✓         |
| Wyspy Świętego Tomasza i Książęca, Demokratyczna Republika       |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzania                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunezja                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zjednoczone Emiraty Arabskie                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bank Zachodnia                                           |                 |                |              |      ✓     |          ✓         |
| Jemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |


<br>
<br>




|Warunki|Definicje|
|--- |--- |
|**<sup>1</sup> — adres punktu**|Pochodzące z indeksem adres punktu w wyszukiwania w trybie Online. To pole jest obsługiwana tylko podczas wyszukiwania i kodowanie geograficzne jest obsługiwany.|
|**<sup>2</sup> -house numer**|Pochodzące z indeksu interpolacji adres w polu wyszukiwania w trybie Online. To pole jest obsługiwana tylko podczas wyszukiwania i kodowanie geograficzne jest obsługiwany.|
|**<sup>3</sup> -kod przypisany użytkownik**|Online wyszukiwanie określonych kod, nie oficjalnego ISO 3166-1.|
