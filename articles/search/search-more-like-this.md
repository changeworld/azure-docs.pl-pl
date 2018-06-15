---
title: moreLikeThis w usłudze Azure Search (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Wstępne dokumentacji dla funkcji moreLikeThis (wersja zapoznawcza) w interfejsu API REST usługi Azure Search.
authors: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: nateko
ms.openlocfilehash: 29d9a478ca2e91e658d7d0f52e7a193ba694bc16
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790733"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis w usłudze Azure Search (wersja zapoznawcza)

`moreLikeThis=[key]` to parametr zapytania w [interfejsu API Search](https://docs.microsoft.com/rest/api/searchservice/search-documents). Określając `moreLikeThis` parametru w kwerendzie wyszukiwania, możesz znaleźć dokumenty, które są podobne do dokumentu, określony przez klucz dokumentu. Po wysłaniu żądania wyszukiwania z `moreLikeThis`, zapytanie jest generowany z warunkami wyszukiwania wyodrębnione z danego dokumentu, najlepiej opisujące tego dokumentu. Wygenerowane zapytanie jest następnie używany do przesyłania żądania wyszukiwania. Domyślnie zawartość wszystkich `searchable` pola są traktowane jako, chyba że `searchFields` parametr jest używany do ograniczania pola. `moreLikeThis` Parametru nie można używać z parametrem wyszukiwania `search=[string]`.

## <a name="examples"></a>Przykłady 

Poniżej znajduje się przykład moreLikeThis zapytania. Zapytanie znajdzie dokumentów, których opis pola są najbardziej zbliżone do pola dokumentu źródłowego określony przez `moreLikeThis` parametru.

```  
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```  

```  
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {  
      "moreLikeThis": "1002",  
      "searchFields": "description"  
    }  
```  

## <a name="feature-availability"></a>Dostępność funkcji

Funkcja moreLikeThis jest obecnie w wersji zapoznawczej i obsługiwane tylko w wersji interfejsu api w wersji zapoznawczej, `2015-02-28-Preview` i `2016-09-01-Preview`. Ponieważ w żądaniu jest określona wersja interfejsu API, jest możliwe łączenie ogólnie dostępna (GA) i Podgląd interfejsów API w tej samej aplikacji. Jednak Podgląd interfejsów API nie są w ramach umowy dotyczącej poziomu usług i funkcji mogą ulec zmianie, dlatego nie zaleca się stosowanie ich w aplikacjach produkcyjnych.