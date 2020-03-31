---
title: Filtry zabezpieczeń dla wyników przycinania
titleSuffix: Azure Cognitive Search
description: Kontrola dostępu do zawartości usługi Azure Cognitive Search przy użyciu filtrów zabezpieczeń i tożsamości użytkowników.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24f168f68a60ebb0408b7f1c367039ea5caea6d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794278"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Filtry zabezpieczeń do przycinania wyników w usłudze Azure Cognitive Search

Filtry zabezpieczeń można zastosować, aby przyciąć wyniki wyszukiwania w usłudze Azure Cognitive Search na podstawie tożsamości użytkownika. To środowisko wyszukiwania zazwyczaj wymaga porównania tożsamości osoby, która żąda wyszukiwania, z polem zawierającym zasady, które mają uprawnienia do dokumentu. Po znalezieniu dopasowania użytkownik lub podmiot zabezpieczeń (na przykład grupa lub rola) ma dostęp do tego dokumentu.

Jednym ze sposobów osiągnięcia filtrowania zabezpieczeń jest skomplikowane rozłączenie wyrażeń równości: na przykład `Id eq 'id1' or Id eq 'id2'`i tak dalej. Takie podejście jest podatne na błędy, trudne do utrzymania, a w przypadkach, gdy lista zawiera setki lub tysiące wartości, spowalnia czas odpowiedzi kwerendy o wiele sekund. 

Prostsze i szybsze `search.in` podejście jest za pośrednictwem funkcji. Jeśli używasz `search.in(Id, 'id1, id2, ...')` zamiast wyrażenia równości, można oczekiwać, że czas odpowiedzi podsekundowej.

W tym artykule pokazano, jak wykonać filtrowanie zabezpieczeń, wykonując następujące kroki:
> [!div class="checklist"]
> * Tworzenie pola zawierającego główne identyfikatory 
> * Wypychanie lub aktualizowanie istniejących dokumentów za pomocą odpowiednich identyfikatorów głównych
> * Wystawianie żądania `search.in` wyszukiwania za pomocą`filter`

>[!NOTE]
> Proces pobierania identyfikatorów głównych nie jest omówiony w tym dokumencie. Powinieneś go otrzymać od dostawcy usług tożsamości.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz [subskrypcję platformy Azure,](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) [usługę Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-create-service-portal)i indeks usługi Azure Cognitive [Search](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Tworzenie pola zabezpieczeń

Dokumenty muszą zawierać pole określające, które grupy mają dostęp. Te informacje stają się kryteriami filtrowania, dla których dokumenty są wybierane lub odrzucane z zestawu wyników zwracanych wystawcy.
Załóżmy, że mamy indeks zabezpieczonych plików, a każdy plik jest dostępny dla innego zestawu użytkowników.
1. Dodaj `group_ids` pole (możesz wybrać dowolną nazwę `Collection(Edm.String)`tutaj) jako . Upewnij się, że `filterable` pole ma `true` atrybut ustawiony tak, aby wyniki wyszukiwania były filtrowane na podstawie dostępu użytkownika. Na przykład jeśli `group_ids` ustawisz `["group_id1, group_id2"]` pole dla `file_name` dokumentu z "secured_file_b", tylko użytkownicy należący do identyfikatorów grup "group_id1" lub "group_id2" mają dostęp do odczytu do pliku.
   Upewnij się, że `retrievable` atrybut pola `false` jest ustawiony tak, aby nie był zwracany w ramach żądania wyszukiwania.
2. Również `file_id` dodać `file_name` i pola dla dobra tego przykładu.  

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
  
Wystawiaj żądanie HTTP POST do punktu końcowego adresu URL indeksu. Treść żądania HTTP jest obiektem JSON zawierającym dokumenty, które mają zostać dodane:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

W treści żądania określ zawartość dokumentów:

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

Jeśli chcesz zaktualizować istniejący dokument o listę grup, `merge` możesz `mergeOrUpload` użyć akcji lub:

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

Aby uzyskać szczegółowe informacje na temat dodawania lub aktualizowania dokumentów, można przeczytać [Edytuj dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Stosowanie filtru zabezpieczeń

Aby przyciąć dokumenty `group_ids` na podstawie dostępu, należy wystawić zapytanie wyszukiwania z filtrem, `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` w którym "group_id1, group_id2,..." są grupami, do których należy wystawca żądań wyszukiwania.
Ten filtr jest zgodny `group_ids` ze wszystkimi dokumentami, dla których pole zawiera jeden z podanych identyfikatorów.
Aby uzyskać szczegółowe informacje na temat wyszukiwania dokumentów przy użyciu usługi Azure Cognitive Search, można przeczytać [w programie Wyszukiwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Należy zauważyć, że w tym przykładzie pokazano, jak wyszukiwać dokumenty przy użyciu żądania POST.

Wystawiaj żądanie HTTP POST:

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

Należy odzyskać dokumenty, `group_ids` gdzie zawiera "group_id1" lub "group_id2". Innymi słowy, otrzymasz dokumenty, do których wystawca żądania ma dostęp do odczytu.

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

W ten sposób można filtrować wyniki na `search.in()` podstawie tożsamości użytkownika i funkcji Usługi Azure Cognitive Search. Za pomocą tej funkcji można przekazać w zasadzie identyfikatory dla użytkownika żądającego, aby dopasować do identyfikatorów głównych skojarzonych z każdego dokumentu docelowego. Gdy żądanie wyszukiwania jest obsługiwane, `search.in` funkcja odfiltruje wyniki wyszukiwania, dla których żaden z podmiotów użytkownika ma dostęp do odczytu. Identyfikatory główne mogą reprezentować takie rzeczy jak grupy zabezpieczeń, role, a nawet własną tożsamość użytkownika.
 
## <a name="see-also"></a>Zobacz też

+ [Kontrola dostępu oparta na tożsamości usłudze Active Directory przy użyciu filtrów usługi Azure Cognitive Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry w usłudze Azure Cognitive Search](search-filters.md)
+ [Bezpieczeństwo i kontrola dostępu do danych w operacjach usługi Azure Cognitive Search](search-security-overview.md)