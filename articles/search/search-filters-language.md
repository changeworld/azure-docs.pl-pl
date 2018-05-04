---
title: Filtry języka w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Filtrowanie kryteria tożsamości zabezpieczeń użytkownika, języka, lokalizacji geograficznej lub wartości liczbowe, aby ograniczyć wyniki wyszukiwania na zapytania w usłudze Azure Search, Usługa wyszukiwania w chmurze hostowanej w systemie Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.workload: search
ms.date: 10/23/2017
ms.author: heidist
ms.openlocfilehash: 6d7fa7ab6db1fe9f8e2d1530c2917f4716a38079
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Jak filtrować według języków w usłudze Azure Search 

Decydujące znaczenie w aplikacji wielojęzycznych wyszukiwania jest możliwość przeszukiwać i pobrać wyniki w języku przez użytkownika. W usłudze Azure Search jednym ze sposobów spełnia wymagania językowe aplikacji wielojęzycznych ma tworzyć serie pól służących do przechowywania ciągów w określonym języku, a następnie ograniczyć wyszukiwanie pełnotekstowe do pól tylko podczas przeszukiwania.

Parametry zapytania w żądaniu służą do zarówno zakresu operacji wyszukiwania, a następnie przyciąć wyniki wszystkich pól, które nie udostępniają zawartości jest zgodny z wyników wyszukiwania, które mają zostać dostarczone.

| Parametry | Przeznaczenie |
|-----------|--------------|
| **searchFields** | Limity pełnej wyszukiwania tekstu do listy pól nazwanego. |
| **$select** | Przycina odpowiedzi zawiera wyłącznie dane, które określisz. Domyślnie zwracane są wszystkie pola pobieranie. **$Select** parametru pozwala wybrać te, które do zwrócenia. |

Powodzenie tej techniki zawiasy na integralność zawartość pola. Usługa Azure Search nie translacji ciągów lub wykonać wykrywania języka. Jest należy się upewnić, że pola zawierają ciągi oczekiwane.

## <a name="define-fields-for-content-in-different-languages"></a>Definiowanie pól dla zawartości w różnych językach

W usłudze Azure Search zapytania docelowe jeden indeks. Deweloperów, którzy chcą zapewnienie ciągów specyficzne dla języka w jednym przeszukiwanie zazwyczaj Definiowanie pól dedykowane do przechowywania wartości: ciągi jedno pole w języku angielskim, jeden dla francuski i tak dalej. 

W naszym próbek, w tym [próbki nieruchomości](search-get-started-portal.md) pokazano poniżej, może być przejrzane definicje pól podobny do następującego zrzutu ekranu. Zwróć uwagę, jak ten przykład przedstawia język analizatora przypisania dla pola do indeksu. Pola, które zawierają ciągi działać lepiej wyszukiwania pełnotekstowego podczas parowania z analizatora zaprojektowane tak, aby obsłużyć językowe reguły języka docelowego.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Przykłady kodu przedstawiający definicje pól z analizatorów języków, zobacz [definiowanie indeksu (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet#define-your-azure-search-index) i [definiowanie indeksu (REST)](https://docs.microsoft.com/azure/search/search-create-index-rest-api#define-your-azure-search-index-using-well-formed-json).

## <a name="build-and-load-an-index"></a>Tworzenie i załadować indeks

Etap pośredni (i prawdopodobnie oczywiste) to, czy masz do [kompilacji i wypełnić indeks](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) przed formułowanie zapytania. Firma Microsoft wspomina ten krok tutaj aby informacje były kompletne. Jednym ze sposobów ustalenia, czy dostępne jest indeks jest sprawdzanie listy indeksów w [portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Ogranicz zapytania i przycinania wyników

Parametry zapytania są używane do Ogranicz wyszukiwanie do określonych pól, a następnie przyciąć wyniki wszystkich pól, które nie są przydatne do danego scenariusza. Biorąc pod uwagę celem Ograniczanie wyszukiwania do pól zawierającego ciągi francuskim, należy użyć **searchFields** pod kątem zapytania na pola zawierające ciągi w tym języku. 

Domyślnie wszystkie pola, które są oznaczone jako pobieranie funkcja wyszukiwania. Tak można wykluczyć pola, które nie są zgodne z wyników wyszukiwania specyficzny dla języka, który ma być dostępny. W szczególności jeśli ograniczone wyszukiwania do pola z ciągami francuskim, prawdopodobnie chcesz wykluczyć pola z ciągami angielskiej wersji językowej z wyników. Przy użyciu **$select** zapytania daje parametru kontrolę nad pola, które są zwracane do aplikacji wywołującej.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Ma argumentu no $filter w zapytaniu, ale ten przypadek użycia jest silnie powiązane z pojęcia filtru, możemy przedstawić jako filtrowania scenariusza.

## <a name="see-also"></a>Zobacz także

+ [Filtry w usłudze Azure Search](search-filters.md)
+ [Analizatory języków](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Ile wyszukiwanie pełnotekstowe działa w usłudze Azure Search](search-lucene-query-architecture.md)
+ [Wyszukiwanie w dokumentach interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents)

