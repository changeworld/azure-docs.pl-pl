---
title: Filtry zabezpieczeń dla wyników przycinania w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Kontrolę dostępu do zawartości usługi Azure Search przy użyciu filtrów zabezpieczeń i tożsamości użytkowników.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 08/07/2017
author: revitalbarletz
ms.author: revitalb
manager: jlembicz
ms.openlocfilehash: dd26676b74431566b3631b8a79cd06bcf3022518
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792804"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Filtry zabezpieczeń dla wyników przycinania w usłudze Azure Search

Możesz zastosować filtry zabezpieczeń, aby przyciąć wyniki wyszukiwania w usłudze Azure Search na podstawie tożsamości użytkownika. Ta wersja wyszukiwania wymaga zwykle porównanie tożsamości kto żądania wyszukiwania względem pola zawierającego zasady, którzy mają uprawnienia do dokumentu. Po znalezieniu dopasowania użytkownika lub podmiot zabezpieczeń (np. grupy lub roli) ma dostęp do tego dokumentu.

Jednym ze sposobów osiągnięcia zabezpieczeń filtrowania odbywa się za pośrednictwem skomplikowane rozłączenia wyrażeń równości: na przykład `Id eq 'id1' or Id eq 'id2'`, itd. Ta metoda jest podatne na błędy, trudne w utrzymaniu i w przypadkach, gdy lista zawiera setek lub tysięcy wartości spowalnia czas odpowiedzi na zapytanie o liczbę sekund. 

Podejście łatwiejsze i szybsze odbywa się za pośrednictwem `search.in` funkcji. Jeśli używasz `search.in(Id, 'id1, id2, ...')` zamiast wyrażenia równości, może spodziewać się odpowiedzi podrzędne sekundę razy.

W tym artykule przedstawiono sposób wykonania filtrowanie zabezpieczeń, wykonując następujące czynności:
> [!div class="checklist"]
> * Utwórz pole, które zawiera identyfikatory główna 
> * Push lub zaktualizuj istniejących dokumentów z odpowiednimi identyfikatorami główna
> * Wysłać żądania wyszukiwania z `search.in` `filter`

>[!NOTE]
> Proces pobierania główna identyfikatory nie opisano w tym dokumencie. Należy pobrać go z dostawcą usług tożsamości.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, masz [subskrypcji platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [usługi Azure Search](https://docs.microsoft.com/azure/search/search-create-service-portal), i [indeksu usługi Azure Search](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Utwórz pole zabezpieczeń

Dokumenty musi zawierać pole określania, które grupy mają dostęp. Te informacje będą kryteria filtrowania, względem których dokumenty są wybrane lub odrzucone z zestaw wyników zwrócony do wystawcy.
Załóżmy, że mamy indeksu zabezpieczonych plików, a każdy plik jest dostępny przez inny zestaw użytkowników.
1. Dodaj pole `group_ids` (możesz wybrać dowolną nazwę tutaj) jako `Collection(Edm.String)`. Upewnij się, że pole ma `filterable` ustawić atrybutu `true` celu wyniki wyszukiwania są filtrowane na podstawie użytkownik ma dostęp. Na przykład jeśli ustawisz `group_ids` do `["group_id1, group_id2"]` dla dokumentu z `file_name` "secured_file_b", tylko użytkownicy należący do grupy identyfikatorów "group_id1" lub "group_id2" mają dostęp do odczytu pliku.
   Upewnij się, że w polu `retrievable` atrybut ma ustawioną `false` tak, aby nie są zwracane jako część żądania wyszukiwania.
2. Dodaj również `file_id` i `file_name` pól dla tego przykładu.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Przekazywanie danych do indeksu przy użyciu interfejsu API REST
  
Wysłać żądanie HTTP POST do punktu końcowego adresu URL Twojego indeksu. Treść żądania HTTP jest obiektem JSON zawierającego dokumenty przeznaczone do dodania:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=[api-version]  
Content-Type: application/json
api-key: [admin key]
```

W treści żądania Określ zawartość dokumentów:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Jeśli musisz zaktualizować istniejący dokument z listy grup, można użyć `merge` lub `mergeOrUpload` akcji:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Aby uzyskać szczegółowe informacje dotyczące dodawania lub aktualizowania dokumentów, możesz przeczytać [edycji dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Zastosuj filtr zabezpieczeń

Aby przyciąć dokumentów na podstawie `group_ids` dostępu, powinien wystawiać zapytania wyszukiwania z `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` filtru, gdzie "group_id1 group_id2..." grup, do których należy wystawcy żądania wyszukiwania.
Ten filtr dopasowuje wszystkie dokumenty dla którego `group_ids` pole zawiera jeden z danym identyfikatorów.
Aby uzyskać szczegółowe informacje na wyszukiwanie dokumentów za pomocą usługi Azure Search, możesz przeczytać [dokumenty wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Należy pamiętać, że w tym przykładzie przedstawiono sposób wyszukiwania dokumentów za pomocą żądania POST.

Problem żądanie HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=[api-version]  
Content-Type: application/json  
api-key: [admin or query key]
```

Określ filtr w treści żądania:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Należy pobrać je ponownie, gdy `group_ids` zawiera "group_id1" lub "group_id2". Innymi słowy należy pobrać dokumentów, do których wystawcy żądania ma dostęp do odczytu.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Podsumowanie

Jest to, jak można filtrować wyniki na podstawie tożsamości użytkownika i usługi Azure Search `search.in()` funkcji. Ta funkcja służy do przekazania w identyfikatorach podmiotu zabezpieczeń dla użytkownika do dopasowania do głównej identyfikatorów skojarzonych z każdym dokumencie docelowym. Podczas obsługi żądania wyszukiwania `search.in` funkcja odfiltrowuje wyniki wyszukiwania, dla których żaden z podmiotów zabezpieczeń użytkownika nie ma dostępu do odczytu. Identyfikatory główna może reprezentować np. grupy zabezpieczeń, ról lub nawet tożsamości przez użytkownika.
 
## <a name="see-also"></a>Zobacz także

+ [Kontrola dostępu na podstawie tożsamości katalogu Active za pomocą usługi Azure Search filtrów](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry w usłudze Azure Search](search-filters.md)
+ [Dane zabezpieczeń i kontroli dostępu w ramach operacji usługi Azure Search](search-security-overview.md)