---
title: Filtrowanie według języka w indeksie wyszukiwania
titleSuffix: Azure Cognitive Search
description: Filtruj kryteria do obsługi wyszukiwania w wielu językach, określanie zakresu wykonywania zapytań do pól specyficznych dla języka.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5dbf32610e54df4ff009d4cb0a0b080babb4ec73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112064"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Jak filtrować według języka w usłudze Azure Cognitive Search 

Kluczowym wymaganiem w wielojęzycznej aplikacji wyszukiwania jest możliwość wyszukiwania i pobierania wyników w języku użytkownika. W usłudze Azure Cognitive Search jednym ze sposobów spełnienia wymagań językowych aplikacji wielojęzycznej jest utworzenie serii pól poświęconych przechowywaniu ciągów w określonym języku, a następnie ograniczenie wyszukiwania pełnego tekstu tylko do tych pól w czasie kwerendy.

Parametry kwerendy w żądaniu są używane zarówno do zakresu operacji wyszukiwania, a następnie przyciąć wyniki wszystkich pól, które nie zapewniają zawartości zgodnej z środowiska wyszukiwania, które chcesz dostarczyć.

| Parametry | Przeznaczenie |
|-----------|--------------|
| **pola wyszukiwania** | Ogranicza wyszukiwanie pełnotekstowe do listy nazwanych pól. |
| **$select** | Przycina odpowiedź, aby uwzględnić tylko określone pola. Domyślnie zwracane są wszystkie pola, które można pobrać. Parametr **$select** umożliwia wybranie tych, które mają być zwracane. |

Sukces tej techniki zależy od integralności zawartości pola. Usługa Azure Cognitive Search nie tłumaczy ciągów ani nie wykrywa języka. To do Ciebie, aby upewnić się, że pola zawierają ciągi, których oczekujesz.

## <a name="define-fields-for-content-in-different-languages"></a>Definiowanie pól zawartości w różnych językach

W usłudze Azure Cognitive Search kwerendy są kierowane na pojedynczy indeks. Deweloperzy, którzy chcą zapewnić ciągi specyficzne dla języka w jednym środowiska wyszukiwania zazwyczaj definiują dedykowane pola do przechowywania wartości: jedno pole dla ciągów w języku angielskim, jedno dla języka francuskiego i tak dalej. 

W naszych przykładach, w tym [w przykładzie nieruchomości pokazanym](search-get-started-portal.md) poniżej, być może widzieliście definicje pól podobne do poniższego zrzutu ekranu. Zwróć uwagę, jak w tym przykładzie pokazano przypisania analizatora języka dla pól w tym indeksie. Pola zawierające ciągi działają lepiej w wyszukiwaniu pełnotekstowym po sparowaniu z analizatorem zaprojektowanym do obsługi reguł językowych języka docelowego.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Aby zapoznać się z przykładami kodów przedstawiającymi definicje pól z analizatorami języków, zobacz [Definiowanie indeksu (NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) i [Definiowanie indeksu (REST)](search-create-index-rest-api.md).

## <a name="build-and-load-an-index"></a>Tworzenie i ładowanie indeksu

Pośredni (i być może oczywiste) krok jest, że trzeba [skompilować i wypełnić indeks](https://docs.microsoft.com/azure/search/search-create-index-dotnet) przed sformułowaniem kwerendy. Wspominamy o tym kroku tutaj dla kompletności. Jednym ze sposobów określenia, czy indeks jest dostępny, jest sprawdzenie listy indeksów w [portalu](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Ograniczanie wyników kwerendy i przycinania

Parametry w kwerendzie są używane do ograniczania wyszukiwania do określonych pól, a następnie przycinania wyników pól, które nie są przydatne dla danego scenariusza. Biorąc pod uwagę cel ograniczenia wyszukiwania do pól zawierających ciągi francuskie, należy użyć **searchFields** do kierowania zapytania w polach zawierających ciągi w tym języku. 

Domyślnie wyszukiwanie zwraca wszystkie pola, które są oznaczone jako możliwe do pobrania. W związku z tym można wykluczyć pola, które nie są zgodne z środowisko wyszukiwania specyficzne dla języka, które chcesz zapewnić. W szczególności, jeśli ograniczone wyszukiwanie do pola z francuskimi ciągami, prawdopodobnie chcesz wykluczyć pola z angielskimi ciągami ciągów z wyników. Za pomocą **$select** query parametr daje kontrolę nad tym, które pola są zwracane do aplikacji wywołującej.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Chociaż nie ma argumentu $filter w kwerendzie, ten przypadek użycia jest silnie powiązany z pojęciami filtru, więc przedstawiamy go jako scenariusz filtrowania.

## <a name="see-also"></a>Zobacz też

+ [Filtry w usłudze Azure Cognitive Search](search-filters.md)
+ [Analizatory języków](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Interfejs API REST wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents)

