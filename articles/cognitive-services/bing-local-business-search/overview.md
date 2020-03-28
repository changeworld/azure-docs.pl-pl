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
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 4e08596e8cf71bbb0e88abdc51f5d8e69972464d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74665259"
---
# <a name="what-is-bing-local-business-search"></a>Co to jest wyszukiwanie lokalne firmy Bing?
Interfejs API wyszukiwania lokalnego firmy Bing to usługa RESTful, która umożliwia aplikacjom znajdowanie informacji o lokalnych firmach na podstawie zapytań wyszukiwania. Na przykład `q=<business-name> in Redmond, Washington`, `q=Italian restaurants near me`lub . 

## <a name="features"></a>Funkcje
| Funkcja | Opis |  
| -- | -- | 
| [Znajdź lokalne firmy i lokalizacje](quickstarts/local-quickstart.md) | Interfejs API wyszukiwania lokalnego firmy Bing pobiera zlokalizowane wyniki z kwerendy. Wyniki obejmują adres URL witryny firmy oraz tekst, numer telefonu i lokalizację geograficzną, w tym: współrzędne GPS, miasto, adres ulicy |  
| [Filtrowanie wyników lokalnych z obwiedniami geograficznymi](specify-geographic-search.md) | Dodaj współrzędne jako parametry wyszukiwania, aby ograniczyć wyniki do określonego obszaru geograficznego, określonego przez okrągły obszar lub kwadratowe obwiedni. | 
| [Filtrowanie wyników lokalnej firmy według kategorii](local-categories.md) | Wyszukaj wyniki lokalnej firmy według kategorii. Ta opcja używa odwrotnej lokalizacji IP lub współrzędnych GPS rozmówcy, aby zwrócić zlokalizowane wyniki w różnych kategoriach biznesowych.|

## <a name="workflow"></a>Przepływ pracy
Wywołanie interfejsu API wyszukiwania lokalnego firmy Bing z dowolnego języka programowania, który może tworzyć żądania HTTP i analizować odpowiedzi JSON. Ta usługa jest dostępna przy użyciu interfejsu API REST.
 
1. Utwórz [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z dostępem do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. Adres URL koduje wyszukiwane terminy dla parametru `q=""` zapytania. Na przykład: `q=nearby+restaurant` lub `q=nearby%20restaurant`. W razie potrzeby ustaw również podział na strony. 
3. Wysyłanie [żądania do interfejsu API wyszukiwania lokalnego firmy Bing](quickstarts/local-quickstart.md) 
4. Analizowanie odpowiedzi w formacie JSON 

> [!NOTE]
> Obecnie wyszukiwarka lokalna firmy: 
> * Obsługuje tylko `en-US` rynek. 
> * Nie obsługuje automatycznego przełączania Bing. 

## <a name="next-steps"></a>Następne kroki
- [Zapytanie i odpowiedź](local-search-query-response.md)
- [Szybki start wyszukiwania lokalnych firm](quickstarts/local-quickstart.md)
- [Dokumentacja interfejsu API wyszukiwania lokalnych firm Bing](local-search-reference.md)
- [Wymagania dotyczące użytkowania i wyświetlania](use-display-requirements.md)
