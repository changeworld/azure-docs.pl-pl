---
title: Eksplorator wyszukiwania do wykonywania zapytań indeksów w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Eksploratora wyszukiwania dla zapytania indeksów w usłudze Azure Search.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: 520d9e7b1899c54d922ff6fb77e0901f9609b029
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004137"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>Jak używać Eksploratora wyszukiwania zapytania indeksów w usłudze Azure Search 

W tym artykule dowiesz się, jak wykonywać zapytania dla istniejącej usługi Azure Search indeksu przy użyciu **Eksploratora wyszukiwania** w witrynie Azure portal. Eksploratora wyszukiwania można użyć do przesyłania prostych lub pełnych ciągów zapytań Lucene do dowolnego istniejącego indeksu w usłudze.

## <a name="open-the-service-dashboard"></a>Otwórz pulpit nawigacyjny usługi
1. Na pasku dostępu po lewej stronie w witrynie [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) kliknij opcję **Wszystkie zasoby**.
2. Wybierz swoją usługę Azure Search.

## <a name="select-an-index"></a>Wybierz indeks

Z kafelka **Indeksy** wybierz indeks, który chcesz przeszukać.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Otwórz Eksplorator wyszukiwania

Kliknij Kafelek Eksplorator wyszukiwania, aby wysunąć pasek wyszukiwania i okienko wyników.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>Rozpocznij wyszukiwanie

Podczas używania Eksploratora wyszukiwania możesz określić [parametry zapytania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) aby sformułować zapytanie.

1. W polu **Ciąg zapytania** wpisz zapytanie, a następnie naciśnij klawisz **Wyszukaj**. 

   Ciąg zapytania jest automatycznie przekształcany do odpowiedniego adresu URL żądania w celu przesłania żądania HTTP do interfejsu API REST usługi Azure Search.   
   
   Do utworzenia żądania możesz użyć dowolnej poprawnej prostej lub pełnej składni zapytań Lucene. Znak `*` jest odpowiednikiem pustego lub nieokreślonego wyszukiwania, które zwraca wszystkie dokumenty w losowej kolejności.

2. Wyniki zapytania są prezentowane w obszarze **Wyniki** w niesformatowanej strukturze JSON, w taki sam sposób, jak ładunek zwracany w treści odpowiedzi HTTP podczas programowego wystawiania żądań.

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>Kolejne kroki

W następujących zasobach udostępniono dodatkowe informacje o składni oraz przykłady.

 + [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Przykłady składni zapytań Lucene](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [Składnia wyrażeń filtru OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 