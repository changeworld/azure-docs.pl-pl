---
title: Tworzenie indeksu w kodzie przy użyciu interfejsu API REST — usługa Azure Search
description: Tworzenie indeksu wyszukiwania pełnotekstowego w kodzie za pomocą żądań HTTP i interfejsu API REST usługi Azure Search.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 98749e0900920d0d3541c78c79598123e4f987df
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313973"
---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>Tworzenie indeksu usługi Azure Search przy użyciu interfejsu API REST
> [!div class="op_single_selector"]
>
> * [Omówienie](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
>
>

Ten artykuł przeprowadzi Cię przez proces tworzenia [indeksu](https://docs.microsoft.com/rest/api/searchservice/Create-Index) usługi Azure Search przy użyciu interfejsu API REST usługi Azure Search.

Przed rozpoczęciem pracy z przewodnikiem oraz przed utworzeniem indeksu powinna zostać [utworzona usługa Azure Search](search-create-service-portal.md).

Aby utworzyć indeks usługi Azure Search przy użyciu interfejsu API REST, należy wysłać pojedyncze żądanie HTTP POST do punktu końcowego adresu URL usługi Azure Search. Definicja indeksu będzie znajdować się w treści żądania jako poprawnie sformułowana zawartość JSON.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identyfikowanie klucza api-key administratora usługi Azure Search
Po aprowizowaniu usługi Azure Search możesz wysyłać żądania HTTP do punktu końcowego adresu URL usługi za pomocą interfejsu API REST. *Wszystkie* żądania interfejsu API muszą zawierać klucz api-key, który został wygenerowany dla aprowizowanej usługi Search. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

1. Aby znaleźć klucze api-key dla usługi, musisz zalogować się w witrynie [Azure Portal](https://portal.azure.com/)
2. Przejdź do bloku usługi Azure Search
3. Kliknij ikonę „Klucze”

Usługa będzie dysponować *kluczami administratora* i *kluczami zapytań*.

* Za pomocą podstawowego i pomocniczego *klucza administratora* przyznawane są pełne prawa do wszystkich operacji, łącznie z możliwością zarządzania usługą oraz tworzenia i usuwania indeksów, indeksatorów i źródeł danych. Dostępne są dwa klucze, dzięki czemu możesz nadal używać klucza pomocniczego, jeśli zdecydujesz się na ponowne wygenerowanie klucza podstawowego, i na odwrót.
* *Klucze zapytań* umożliwiają dostęp tylko do odczytu do indeksów oraz dokumentów i są zazwyczaj dystrybuowane do aplikacji klienckich, które wysyłają żądania wyszukiwania.

Podczas tworzenia indeksu można użyć zarówno podstawowego, jak i pomocniczego klucza administratora.

## <a name="define-your-azure-search-index-using-well-formed-json"></a>Definiowanie indeksu usługi Azure Search przy użyciu poprawnie sformułowanej zawartości JSON
Pojedyncze żądanie HTTP POST do usługi spowoduje utworzenie indeksu. Treść żądania HTTP POST będzie zawierać pojedynczy obiekt JSON, który definiuje indeks usługi Azure Search.

1. Pierwszą właściwością obiektu JSON jest nazwa Twojego indeksu.
2. Drugą właściwością obiektu JSON jest tablica JSON o nazwie `fields`, która zawiera oddzielny obiekt JSON dla każdego pola w indeksie. Każdy z tych obiektów JSON zawiera wiele par nazwa/wartość dla każdego z atrybutów pola, w tym „name”, „type” itd.

Podczas projektowania indeksu należy pamiętać o środowisku wyszukiwania użytkownika oraz o potrzebach biznesowych, ponieważ do każdego pola należy przypisać [odpowiednie atrybuty](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Te atrybuty kontrolują, które funkcje wyszukiwania (filtrowanie, tworzenie aspektów, sortowanie, wyszukiwanie pełnotekstowe itp.) dotyczą określonych pól. Dla każdego atrybutu, który nie zostanie określony, odpowiednia funkcja wyszukiwania zostanie domyślnie włączona.

W naszym przykładzie indeks ma nazwę „hotels”, a pola zostały zdefiniowane w następujący sposób:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Dla każdego pola starannie wybraliśmy atrybuty indeksu, kierując się tym, jak naszym zdaniem będą używane w aplikacji. Na przykład `hotelId` jest kluczem unikatowym, który prawdopodobnie nie będzie znany osobom szukającym hoteli, dlatego wyłączyliśmy wyszukiwanie pełnotekstowe dla tego pola przez ustawienie właściwości `searchable` na wartość `false`, co pozwala na zaoszczędzenie miejsca w indeksie.

Zauważ, że dokładnie jedno pole typu `Edm.String` w Twoim indeksie musi być wyznaczone jako pole klucza „key”.

W powyższej definicji indeksu użyto analizatora języków dla pola `description_fr`, ponieważ jest ono przeznaczone do przechowywania tekstu w języku francuskim. Aby uzyskać więcej informacji o analizatorach języków, zobacz [temat Language support (Obsługa języków)](https://docs.microsoft.com/rest/api/searchservice/Language-support) oraz odpowiadający mu [wpis na blogu](https://azure.microsoft.com/blog/language-support-in-azure-search/).

## <a name="issue-the-http-request"></a>Wysyłanie żądania HTTP
1. Korzystając z definicji indeksu jako treści żądania, wyślij żądanie HTTP POST do punktu końcowego adresu URL usługi Azure Search. Upewnij się, że w adresie URL jako nazwy hosta użyto nazwy usługi oraz że wstawiono właściwą wartość `api-version` jako parametr ciągu zapytania (w chwili opublikowania tego dokumentu `2017-11-11` jest bieżącą wersją interfejsu API).
2. W nagłówkach żądania określ wartość `application/json` dla właściwości `Content-Type`. Należy również podać klucz administratora usługi, który został zidentyfikowany w kroku I, w nagłówku `api-key`.

Aby wysłać poniższe żądanie, konieczne jest podanie własnej nazwy usługi oraz klucza interfejsu API: 

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [api-key]


Jeśli żądanie zakończy się powodzeniem powinien zostać wyświetlony kod stanu 201 (Utworzono). Aby uzyskać więcej informacji na temat tworzenia indeksu za pomocą interfejsu API REST, odwiedź [stronę dokumentacji interfejsu API dostępną tutaj](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Aby uzyskać więcej informacji o innych kodach stanów HTTP, które mogą być zwracane w przypadku niepowodzenia, zobacz [HTTP status codes (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes) (Usługa Azure Search — kody stanów HTTP).

Gdy ukończysz pracę z indeksem i zechcesz go usunąć, po prostu wyślij żądanie HTTP DELETE. Na przykład indeks „hotele” możesz usunąć w następujący sposób:

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11
    api-key: [api-key]


## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu indeksu usługi Azure Search można [przekazać zawartość do indeksu](search-what-is-data-import.md), aby rozpocząć wyszukiwanie danych.
