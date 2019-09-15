---
title: Co to jest interfejs API wyszukiwania lokalnych firm Bing?
titleSuffix: Azure Cognitive Services
description: Interfejs API wyszukiwania lokalnych firm Bing jest usługą RESTful, która pozwala aplikacjom znajdować informacje o lokalnych miejscach i firmach na podstawie zapytań wyszukiwania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: bdada613ef46881bbf051144efded541ac3d0974
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996686"
---
# <a name="what-is-bing-local-business-search"></a>Co to jest lokalne wyszukiwanie w biznesie Bing?
Lokalny interfejs API wyszukiwania biznesowego Bing to usługa RESTful, która umożliwia aplikacjom Znajdowanie informacji o lokalnych firmach na podstawie zapytań wyszukiwania. Na przykład `q=<business-name> in Redmond, Washington`, lub `q=Italian restaurants near me`. 

## <a name="features"></a>Funkcje
| Cecha | Opis |  
| -- | -- | 
| [Znajdowanie lokalnych firm i lokalizacji](quickstarts/local-quickstart.md) | Interfejs API wyszukiwania lokalnego firmy Bing umożliwia zlokalizowanie wyników zapytania. Wyniki obejmują adres URL witryny internetowej firmy oraz wyświetla tekst, numer telefonu i lokalizację geograficzną, w tym: Współrzędne GPS, miasto, ulica |  
| [Filtrowanie wyników lokalnych ze granicami geograficznymi](specify-geographic-search.md) | Dodaj współrzędne jako parametry wyszukiwania, aby ograniczyć wyniki do określonego obszaru geograficznego, określonego przez okrągły obszar lub kwadratowe pole ograniczające. | 
| [Filtrowanie lokalnych wyników firmy według kategorii](local-categories.md) | Wyszukaj lokalne wyniki biznesowe według kategorii. Ta opcja używa odwrotnej lokalizacji IP lub współrzędnej GPS obiektu wywołującego, aby zwracać zlokalizowane wyniki w różnych kategoriach firmy.|

## <a name="workflow"></a>Przepływ pracy
Wywołaj interfejs API wyszukiwania w usłudze Bing Local Business Search z dowolnego języka programowania, który może wykonywać żądania HTTP i analizować odpowiedzi JSON. Ta usługa jest dostępna przy użyciu interfejsu API REST.
 
1. Utwórz [konto interfejsu API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z dostępem do interfejsy API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. Kodowanie w adresie URL wyszukiwanych terminów dla `q=""` parametru zapytania. Na przykład `q=nearby+restaurant` lub `q=nearby%20restaurant`. W razie konieczności Ustaw również stronicowanie. 
3. Wyślij [żądanie do interfejsu API wyszukiwania lokalnego usługi Bing](quickstarts/local-quickstart.md) 
4. Analizowanie odpowiedzi w formacie JSON 

> [!NOTE]
> Obecnie lokalne wyszukiwanie biznesowe obsługuje tylko `en-US` rynek. 
> [!NOTE]
> Obecnie lokalne wyszukiwanie biznesowe nie obsługuje automatycznego sugerowania. 

## <a name="next-steps"></a>Następne kroki
- [Zapytanie i odpowiedź](local-search-query-response.md)
- [Lokalne wyszukiwanie biznesowe — Szybki Start](quickstarts/local-quickstart.md)
- [Dokumentacja interfejsu API wyszukiwania lokalnego w firmie](local-search-reference.md)
- [Use and display requirements (Wymagania dotyczące użycia i wyświetlania)](use-display-requirements.md)
