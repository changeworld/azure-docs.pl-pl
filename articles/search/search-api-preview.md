---
title: Funkcje podglądu w interfejsie REST API
titleSuffix: Azure Cognitive Search
description: Usługa Azure Cognitive Search REST API w wersji 2019-05-06-Preview zawiera funkcje eksperymentalne, takie jak magazyn wiedzy i buforowanie indeksatora w celu przyrostowego wzbogacenia.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162280"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funkcje w wersji zapoznawczej w usłudze Azure Cognitive Search

W tym artykule wymieniono funkcje aktualnie w wersji zapoznawczej. Funkcje, które przechodzą z wersji zapoznawczej do ogólnej dostępności są usuwane z tej listy. Możesz sprawdzić [aktualizacje usługi](https://azure.microsoft.com/updates/?product=search) lub [Co nowego w](whats-new.md) przypadku ogłoszeń dotyczących ogólnej dostępności.

Niektóre funkcje w wersji zapoznawczej mogą być dostępne w portalu i w pliku .NET SDK, ale interfejs API REST zawsze ma funkcje w wersji zapoznawczej.

+ Dla operacji [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) wyszukiwania, jest bieżącą wersją wersji zapoznawczej.
+ W przypadku [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) operacji zarządzania jest bieżącą wersją wersji zapoznawczej.

> [!IMPORTANT]
> Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Funkcje wzbogacania SI

Zapoznaj się z najnowszymi ulepszeniami wzbogacania si wzbogacenia za pośrednictwem [interfejsu API wyszukiwania w wersji zapoznawczej.](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)

|||
|-|-|
| [Niestandardowa umiejętność wyszukiwania encji (wersja zapoznawcza)](cognitive-search-skill-custom-entity-lookup.md ) | Umiejętność poznawcza, która wyszukuje tekst z niestandardowej, zdefiniowanej przez użytkownika listy słów i fraz. Korzystając z tej listy, etykietuje wszystkie dokumenty z dowolnymi pasującymi encjami. Umiejętność obsługuje również stopień rozmytego dopasowania, które można zastosować, aby znaleźć dopasowania, które są podobne, ale nie do końca dokładne. | 
| [Umiejętność wykrywania pii (wersja zapoznawcza)](cognitive-search-skill-pii-detection.md) | Umiejętności poznawcze używane podczas indeksowania, który wyodrębnia dane osobowe z tekstu wejściowego i daje możliwość maskowania go z tego tekstu na różne sposoby.| 
| [Wzbogacenie przyrostowe (wersja zapoznawcza)](cognitive-search-incremental-indexing-conceptual.md) | Dodaje buforowanie do potoku wzbogacania, co pozwala na ponowne użycie istniejących danych wyjściowych, jeśli ukierunkowane modyfikacji, takie jak aktualizacja do skillset lub innego obiektu, nie zmienia zawartości. Buforowanie dotyczy tylko wzbogaconych dokumentów opracowanych przez zestaw umiejętności.| 
| [Magazyn wiedzy (wersja zapoznawcza)](knowledge-store-concept-intro.md) | Nowe miejsce docelowe rurociągu wzbogacania opartego na SI. Struktura danych fizycznych istnieje w magazynie obiektów Blob platformy Azure i usługi Azure Table storage i jest tworzona i wypełniana po uruchomieniu indeksatora, który ma dołączony zestaw umiejętności poznawczych. Definicja samego magazynu wiedzy jest określona w definicji zestawu umiejętności. W definicji magazynu wiedzy można kontrolować struktury fizyczne danych za pomocą elementów *rzutowania,* które określają, jak dane są kształtowane, czy dane są przechowywane w magazynie tabel lub magazynu obiektów blob i czy istnieje wiele widoków.| 

## <a name="indexing-and-query-features"></a>Funkcje indeksowania i kwerendy

Funkcje podglądu indeksatora są dostępne w interfejsie API wyszukiwania w wersji zapoznawczej. 

|||
|-|-|
| [Indeksator usługi Cosmos DB](search-howto-index-cosmosdb.md) | Obsługa typów interfejsów API MongoDB (wersja zapoznawcza), interfejsu API Gremlin (wersja zapoznawcza) i interfejsu API Cassandra (wersja zapoznawcza). | 
|  [Indeksator usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza)](search-howto-index-azure-data-lake-storage.md) | Indeks zawartości i metadanych z usługi Data Lake Storage Gen2.| 
| [moreLikeThis query parametr (podgląd)](search-more-like-this.md) | Znajduje dokumenty, które są istotne dla określonego dokumentu. Ta funkcja została we wcześniejszych wersjach zapoznawców. | 

## <a name="management-features"></a>Funkcje zarządzania

|||
|-|-|
| [Prywatna obsługa punktów końcowych](service-create-private-endpoint.md) | Można utworzyć sieć wirtualną z bezpiecznym klientem (na przykład maszyną wirtualną), a następnie utworzyć usługę wyszukiwania, która używa prywatnego punktu końcowego. |
| Ograniczenie dostępu do ip | Za [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) pomocą interfejsu API REST zarządzania, można utworzyć usługę, która ma ograniczenia, na których adresy IP są dozwolone dostęp. |

## <a name="earlier-preview-features"></a>Wcześniejsze funkcje podglądu

Funkcje ogłoszone we wcześniejszych wersjach zapoznawców, jeśli nie zostały przeniesione do ogólnej dostępności, są nadal w publicznej wersji zapoznawczej. Jeśli wywołujesz interfejs API z wcześniejszą wersją interfejsu API w wersji `2019-05-06-Preview` zapoznawczej, możesz nadal używać tej wersji lub przełączać się do niej bez żadnych zmian w oczekiwanym zachowaniu.

## <a name="how-to-call-a-preview-api"></a>Jak wywołać interfejs API w wersji zapoznawczej

Starsze wersje zapoznawcza są nadal operacyjne, ale z czasem stają się przestarzałe. Jeśli wywołasz `api-version=2016-09-01-Preview` `api-version=2017-11-11-Preview`kod lub , te wywołania są nadal ważne. Jednak tylko najnowsza wersja zapoznawcza jest odświeżana z ulepszeniami. 

Poniższa przykładowa składnia ilustruje wywołanie wersji interfejsu API w wersji zapoznawczej.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Usługa Azure Cognitive Search jest dostępna w wielu wersjach. Aby uzyskać więcej informacji, zobacz [wersje interfejsu API](search-api-versions.md).

## <a name="next-steps"></a>Następne kroki

Przejrzyj dokumentację referencyjną interfejsu API interfejsu API wyszukiwania. Jeśli napotkasz problemy, poproś nas o pomoc w [StackOverflow](https://stackoverflow.com/) lub [skontaktuj się z pomocą techniczną.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [Odwołanie do interfejsu API REST usługi wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/)