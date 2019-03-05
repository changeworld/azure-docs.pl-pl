---
title: Dodaj sugestory do indeksu usługi Azure Search
description: Umożliwia pól dla zapytania z podpowiedziami pojawiającymi działań, gdzie sugerowane zapytania składają się z tekstem z pól w indeksie usługi Azure Search.
ms.date: 02/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: fd4b29134fd45ed2888fbc81ded413ecf7286959
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308656"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>Dodaj sugestory do indeksu usługi Azure Search

A **Sugestora** jest konstrukcja usługi Azure Search, obsługa "search jako — możesz type" [sugestie](https://docs.microsoft.com/rest/api/searchservice/suggestions) funkcji i [Autouzupełniania (wersja zapoznawcza)](search-autocomplete-tutorial.md) funkcji. Przed wywołaniem interfejsu API sugestie, należy zdefiniować **sugestora** do indeksu, aby umożliwić sugestie dotyczące określonych pól.

Mimo że **sugestora** ma kilka właściwości, jest głównie to zbiór pól, dla których włączono [sugestie dotyczące interfejsów API](https://docs.microsoft.com/rest/api/searchservice/suggestions). Na przykład aplikacji turystycznej chcieć umożliwić wyszukiwanie typeahead miejsc docelowych, miasta i atrakcje. W efekcie wszystkie trzy pola musieli przejść w kolekcji pól.

Może mieć tylko jeden **sugestora** zasobów dla każdego indeksu (w szczególności jeden **sugestora** w **sugestory** kolekcji).

## <a name="creating-a-suggester"></a>Tworzenie sugestora

Możesz utworzyć **sugestora** w dowolnym momencie, ale wpływ na indeks w zależności pól.

+ Nowe pola dodane do sugestora w ramach tej samej aktualizacji są najmniej atrakcyjnych, w tym odbudowywanie indeksu, nie jest wymagane.
+ Istniejące pola dodane do sugestora, jednak zmienia definicję pola, wymagających pełnej rekompilacji indeksu.

**Sugestory** działają najlepiej, gdy jest używana do sugerowania określonych dokumentów, a nie utracić wprowadzone terminów ani fraz. Najważniejsze pola Release candidate są tytułów, nazwy i innych względnie krótkich fraz, które można zidentyfikować element. Mniej skuteczne powtarzających się pola, takie jak kategorie i tagi, lub są bardzo długie pola, takie jak pola opisów i komentarzy.

Po utworzeniu sugestora Dodaj [sugestie dotyczące interfejsów API](https://docs.microsoft.com/rest/api/searchservice/suggestions) w logice zapytania, aby wywołać tę funkcję.

Właściwości, które definiują **sugestora** obejmują następujące elementy:

|Właściwość|Opis|
|--------------|-----------------|
|`name`|Nazwa **sugestora**. Użyj nazwy **sugestora** podczas wywoływania [sugestie &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions).|
|`searchMode`|Strategia używana do wyszukiwania fraz kandydujących. To jedyny obecnie obsługiwany tryb `analyzingInfixMatching`, który przeprowadza elastyczne dopasowywanie fraz na początku lub w środku zdań.|
|`sourceFields`|Lista jednego lub więcej pól, które są źródłem zawartości sugestie. Tylko pola typu `Edm.String` i `Collection(Edm.String)` może być źródeł dla sugestie. Można tylko pola, które nie mają niestandardowego analizatora języków zestawu. |

## <a name="suggester-example"></a>Przykład sugestora
A **sugestora** jest częścią definicji indeksu. Tylko jeden **sugestora** może znajdować się w **sugestory** kolekcji w bieżącej wersji obok **pola** kolekcji i **scoringProfiles**.

```
{
  "name": "hotels",
  "fields": [
    . . .
  ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ],
  "scoringProfiles": [
    . . .
  ]
}

```

## <a name="see-also"></a>Zobacz także
[Tworzenie indeksu &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
[Aktualizowanie indeksu &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/update-index)  
[Sugestie dotyczące &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)  
[Operacje indeksowania &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/index-operations)  
[REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/)  
[Zestaw SDK platformy .NET usługi Azure Search](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)
