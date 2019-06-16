---
title: Filtry zabezpieczeń do przycinania wyniki — usługa Azure Search
description: Kontrolę dostępu do zawartości usługi Azure Search przy użyciu filtrów zabezpieczeń i tożsamości użytkowników.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 05/02/2019
author: brjohnstmsft
ms.author: brjohnst
manager: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: a222b9e506988929c25a560361611b8f78142053
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024365"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Filtry zabezpieczeń dla wyników przycinania w usłudze Azure Search

Można stosować filtry zabezpieczeń można przycięcia wyniki wyszukiwania w usłudze Azure Search na podstawie tożsamości użytkownika. To środowisko wyszukiwania wymaga zwykle porównanie tożsamości spojrzenia żądania wyszukiwania względem pola zawierającego zasady, którzy mają uprawnienia do dokumentu. Gdy zostanie znalezione dopasowanie, użytkownik lub ta jednostka (np. grupy lub roli) ma dostęp do tego dokumentu.

Jednym ze sposobów osiągnięcia bezpieczeństwa filtrowanie opiera się skomplikowane rozłączenia wyrażeń równości: na przykład `Id eq 'id1' or Id eq 'id2'`, i tak dalej. To podejście jest podatne na błędy, trudne w utrzymaniu oraz w przypadkach, gdy lista zawiera setek lub tysięcy wartości spowalnia przez czas w sekundach czas odpowiedzi na zapytanie. 

Podejście prostszy i szybszy jest za pośrednictwem `search.in` funkcji. Jeśli używasz `search.in(Id, 'id1, id2, ...')` zamiast wyrażenie równości, można oczekiwać, że reakcji poniżej sekundy czasu.

W tym artykule pokazano, jak wykonać filtrowanie zabezpieczeń wykonując następujące czynności:
> [!div class="checklist"]
> * Tworzenie pola, które zawiera identyfikatory jednostki 
> * Wypychanie lub zaktualizować istniejące dokumenty przy użyciu odpowiednich identyfikatorów jednostki
> * Żądania wyszukiwania za pomocą `search.in` `filter`

>[!NOTE]
> Proces pobierania identyfikatorów jednostki nie pasuje do tego dokumentu. Należy pobrać go z dostawcą tożsamości usługi.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, masz [subskrypcji platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [usługi Azure Search](https://docs.microsoft.com/azure/search/search-create-service-portal), i [indeksu wyszukiwania platformy Azure](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Utwórz pole zabezpieczeń

Dokumenty mogą zawierać pole określania, które grupy mają dostęp. Te informacje będą kryteria filtrowania, wobec których dokumenty zostaną wybrane lub odrzucone z zestaw wyników zwrócony z wystawcą.
Załóżmy, że mamy indeksu zabezpieczonych plików, a każdy plik jest dostępny za pomocą innego zestawu użytkowników.
1. Dodaj pole `group_ids` (można wybrać dowolną nazwę, w tym miejscu) jako `Collection(Edm.String)`. Upewnij się, że pole ma `filterable` ustawioną wartość atrybutu `true` tak, aby wyniki wyszukiwania są filtrowane na podstawie uprawnień użytkownika. Na przykład jeśli ustawisz `group_ids` pole `["group_id1, group_id2"]` dokumentu o `file_name` "secured_file_b", tylko użytkownicy, którzy należą do grupy identyfikatorów "group_id1" lub "group_id2" po ich przeczytaniu dostępu do pliku.
   Upewnij się, w polu `retrievable` ma ustawioną wartość atrybutu `false` tak, aby nie jest zwracana jako część żądania wyszukiwania.
2. Również dodać `file_id` i `file_name` pól do celów tego przykładu.  

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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Wypychanie danych do indeksu przy użyciu interfejsu API REST
  
Wyślij żądanie HTTP POST do punktu końcowego adresu URL Twojego indeksu. Treść żądania HTTP jest obiektem JSON zawierającym dokumenty do dodania:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

W treści żądania należy określić zawartość dokumentów:

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

Jeśli musisz zaktualizować istniejący dokument z listy grup, możesz użyć `merge` lub `mergeOrUpload` akcji:

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

Aby uzyskać szczegółowe informacje o Dodawanie lub aktualizowanie dokumentów, możesz przeczytać [edycji dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Zastosuj filtr zabezpieczeń

Aby przyciąć dokumentów na podstawie `group_ids` dostępu, należy wydać zapytania wyszukiwania z `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` filtr, gdzie "group_id1 group_id2..." to grupy, do których należy wydawcy żądania wyszukiwania.
Ten filtr dopasowuje wszystkie dokumenty, dla którego `group_ids` pole zawiera jeden z identyfikatorów danego.
Aby uzyskać szczegółowe informacje na wyszukiwanie dokumentów za pomocą usługi Azure Search, możesz przeczytać [wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Należy pamiętać, że w tym przykładzie przedstawiono sposób wyszukiwania dokumentów za pomocą żądania POST.

Wysłać żądanie HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Określ filtr w treści żądania:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Pobieraj dokumenty Wstecz, gdzie `group_ids` zawiera "group_id1" lub "group_id2". Innymi słowy możesz uzyskać dokumentów, do których wydawcy żądania ma dostęp do odczytu.

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

Jest to, jak możesz filtrować wyniki na podstawie tożsamości użytkownika i usługa Azure Search `search.in()` funkcji. Funkcja ta jest przydatna do przekazania w identyfikatorach zasady dla użytkownika do dopasowywania identyfikatorów jednostki skojarzone z każdym dokumencie docelowym. Podczas obsługi żądania wyszukiwania `search.in` funkcja filtruje wyniki wyszukiwania, dla których żadna z podmiotami zabezpieczeń użytkownika nie ma dostępu do odczytu. Identyfikatory jednostki może reprezentować elementów, takich jak grupy zabezpieczeń, ról lub nawet tożsamości przez użytkownika.
 
## <a name="see-also"></a>Zobacz także

+ [Aktywna kontrola dostępu na podstawie tożsamości katalogu przy użyciu filtrów usługi Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry w usłudze Azure Search](search-filters.md)
+ [Danych zabezpieczeń i kontroli dostępu w ramach operacji usługi Azure Search](search-security-overview.md)