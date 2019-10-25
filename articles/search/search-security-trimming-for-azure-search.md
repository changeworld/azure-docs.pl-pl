---
title: Filtry zabezpieczeń do przycinania wyników
titleSuffix: Azure Cognitive Search
description: Kontrola dostępu do zawartości platformy Azure Wyszukiwanie poznawcze przy użyciu filtrów zabezpieczeń i tożsamości użytkowników.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24f168f68a60ebb0408b7f1c367039ea5caea6d1
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794278"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Filtry zabezpieczeń do przycinania wyników na platformie Azure Wyszukiwanie poznawcze

Filtry zabezpieczeń można stosować do przycinania wyników wyszukiwania w usłudze Azure Wyszukiwanie poznawcze w oparciu o tożsamość użytkownika. To środowisko wyszukiwania zwykle wymaga porównania tożsamości osoby, która chce przeszukać w odniesieniu do pola zawierającego zasady, które mają uprawnienia do dokumentu. Po znalezieniu dopasowania użytkownik lub podmiot zabezpieczeń (na przykład grupa lub rola) ma dostęp do tego dokumentu.

Jednym ze sposobów osiągnięcia filtrowania zabezpieczeń jest skomplikowane rozłączenie wyrażeń równości: na przykład `Id eq 'id1' or Id eq 'id2'`i tak dalej. Takie podejście jest podatne na błędy, trudne do utrzymania i w przypadkach, gdy lista zawiera setki lub tysiące wartości, spowalnia czas odpowiedzi kwerendy o wiele sekund. 

Prostszym i szybszym podejściem jest użycie funkcji `search.in`. Jeśli używasz `search.in(Id, 'id1, id2, ...')` zamiast wyrażenia równości, możesz oczekiwać, że czasy odpowiedzi są podsekundami.

W tym artykule pokazano, jak wykonać filtrowanie zabezpieczeń, wykonując następujące czynności:
> [!div class="checklist"]
> * Utwórz pole, które zawiera identyfikatory podmiotu zabezpieczeń 
> * Wypchnij lub zaktualizuj istniejące dokumenty z odpowiednimi identyfikatorami podmiotu zabezpieczeń
> * Wydaj żądanie Search z `search.in` `filter`

>[!NOTE]
> Proces pobierania identyfikatorów podmiotu zabezpieczeń nie został uwzględniony w tym dokumencie. Należy uzyskać od dostawcy usługi tożsamości.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz [subskrypcję platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [usługę Azure Wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-create-service-portal)i [indeks wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Utwórz pole zabezpieczeń

Dokumenty muszą zawierać pole określające, które grupy mają dostęp. Te informacje staną się kryteriami filtrowania, względem których dokumenty są wybierane lub odrzucane z zestawu wyników zwróconego do wystawcy.
Załóżmy, że mamy indeks zabezpieczonych plików, a każdy plik jest dostępny dla innego zestawu użytkowników.
1. Dodaj `group_ids` pola (możesz tutaj wybrać dowolną nazwę) jako `Collection(Edm.String)`. Upewnij się, że pole ma atrybut `filterable` ustawiony na `true`, aby wyniki wyszukiwania zostały odfiltrowane na podstawie dostępu użytkownika. Na przykład jeśli ustawisz pole `group_ids`, aby `["group_id1, group_id2"]` dla dokumentu z `file_name` "secured_file_b", tylko użytkownicy należący do identyfikatorów grup "group_id1" lub "group_id2" mają dostęp do odczytu do tego pliku.
   Upewnij się, że atrybut `retrievable` pola jest ustawiony na `false`, aby nie został zwrócony jako część żądania wyszukiwania.
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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Wypychanie danych do indeksu przy użyciu interfejsu API REST
  
Wydaj żądanie HTTP POST do punktu końcowego adresu URL Twojego indeksu. Treść żądania HTTP jest obiektem JSON zawierającym dokumenty, które mają zostać dodane:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
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

Jeśli musisz zaktualizować istniejący dokument z listą grup, możesz użyć akcji `merge` lub `mergeOrUpload`:

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

Aby uzyskać szczegółowe informacje na temat dodawania lub aktualizowania dokumentów, można odczytać [Edytowanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Zastosuj filtr zabezpieczeń

Aby można było przyciąć dokumenty na podstawie `group_ids` dostępu, należy wydać zapytanie wyszukiwania z filtrem `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))`, gdzie "group_id1, group_id2,..." są grupami, do których należy wystawca żądania wyszukiwania.
Ten filtr dopasowuje wszystkie dokumenty, dla których pole `group_ids` zawiera jeden z podanego identyfikatorów.
Aby uzyskać szczegółowe informacje na temat wyszukiwania dokumentów przy użyciu usługi Azure Wyszukiwanie poznawcze, można odczytywać [dokumenty wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Należy zauważyć, że w tym przykładzie pokazano, jak przeszukiwać dokumenty przy użyciu żądania POST.

Wydaj żądanie HTTP POST:

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

Należy pobrać dokumenty z powrotem, gdzie `group_ids` zawiera "group_id1" lub "group_id2". Innymi słowy, uzyskasz dokumenty, do których wystawcy żądania ma dostęp do odczytu.

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

W ten sposób można filtrować wyniki w oparciu o tożsamość użytkownika i funkcję `search.in()` Wyszukiwanie poznawcze platformy Azure. Za pomocą tej funkcji można przekazać identyfikatory zasad dla użytkownika żądającego, aby dopasować je do identyfikatorów podmiotu zabezpieczeń skojarzonych z każdym dokumentem docelowym. Gdy żądanie wyszukiwania jest obsługiwane, funkcja `search.in` filtruje wyniki wyszukiwania, dla których żaden z podmiotów zabezpieczeń użytkownika nie ma dostępu do odczytu. Identyfikatory podmiotów mogą reprezentować elementy, takie jak grupy zabezpieczeń, role, a nawet własna tożsamość użytkownika.
 
## <a name="see-also"></a>Zobacz także

+ [Active Directory kontroli dostępu opartej na tożsamościach przy użyciu filtrów Wyszukiwanie poznawcze platformy Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
+ [Zabezpieczenia danych i kontrola dostępu w operacjach usługi Azure Wyszukiwanie poznawcze](search-security-overview.md)