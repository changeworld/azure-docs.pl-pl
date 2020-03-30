---
title: Nowe ogłoszenia o funkcjach
titleSuffix: Azure Cognitive Search
description: Anonse dotyczące nowych i ulepszonych funkcji, w tym nazwy usługi Usługi Azure Search na usługę Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/24/2020
ms.openlocfilehash: 475f89fc5b33948864fd83c39ee8058ab6908cad
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80247201"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co nowego w usłudze Azure Cognitive Search

Dowiedz się, co nowego w usłudze. Zaznacz tę stronę zakładką, aby być na bieżąco z usługą.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nowa nazwa usługi

Usługa Azure Search została zmieniona na **Usługa Azure Cognitive Search,** aby odzwierciedlić rozszerzone (ale opcjonalne) użycie umiejętności poznawczych i przetwarzania sztucznej inteligencji w operacjach podstawowych. Wersje interfejsu API, pakiety NuGet, przestrzenie nazw i punkty końcowe pozostają niezmienione. Zmiana nazwy usługi nie ma wpływu na nowe i istniejące rozwiązania wyszukiwania.

## <a name="feature-announcements"></a>Anonse funkcji

### <a name="march-2020"></a>Marzec 2020 r.

+ [Natywne usuwanie nietrwałe obiektów blob (wersja zapoznawcza)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) oznacza, że indeksator usługi Azure Blob Storage w usłudze Azure Cognitive Search rozpozna obiekty BLOB, które są w stanie nietrwałym usuniętym, i usunie odpowiedni dokument wyszukiwania podczas indeksowania.

+ Nowy stabilny [interfejs API REST zarządzania (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) jest już dostępny. 

### <a name="february-2020"></a>Luty 2020 r.

+ [Wykrywanie pii (wersja zapoznawcza)](cognitive-search-skill-pii-detection.md) to umiejętność poznawcza używana podczas indeksowania, która wyodrębnia informacje umożliwiające identyfikację użytkownika z tekstu wejściowego i daje możliwość maskowania go z tego tekstu na różne sposoby.

+ [Niestandardowe wyszukiwanie encji (wersja zapoznawcza)](cognitive-search-skill-custom-entity-lookup.md ) wyszukuje tekst z niestandardowej, zdefiniowanej przez użytkownika listy słów i fraz. Korzystając z tej listy, etykietuje wszystkie dokumenty z dowolnymi pasującymi encjami. Umiejętność obsługuje również stopień rozmytego dopasowania, które można zastosować, aby znaleźć dopasowania, które są podobne, ale nie do końca dokładne. 

### <a name="january-2020"></a>Styczeń 2020 r.

+ [Klucze szyfrowania zarządzane przez klienta](search-security-manage-encryption-keys.md) są teraz ogólnie dostępne. Jeśli używasz REST, możesz uzyskać `api-version=2019-05-06`dostęp do tej funkcji za pomocą programu . W przypadku kodu zarządzanego poprawny pakiet jest nadal [.NET SDK w wersji 8.0-preview,](search-dotnet-sdk-migration-version-9.md) nawet jeśli funkcja jest poza podglądem. 

+ Prywatny dostęp do usługi wyszukiwania jest dostępny za pośrednictwem dwóch mechanizmów, oba obecnie w wersji zapoznawczej:

  + Dostęp do określonych adresów IP można ograniczyć za pomocą interfejsu API `api-version=2019-10-01-Preview` rest zarządzania do utworzenia usługi. Interfejs API wersji zapoznawczej ma nowe właściwości **IpRule** i **NetworkRuleSet** w [interfejsie API CreateOrUpdate](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Ta funkcja podglądu jest dostępna w wybranych regionach. Aby uzyskać więcej informacji, zobacz [Jak korzystać z interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + Obecnie dostępne w wersji zapoznawczej ograniczonego dostępu, można aprowizować usługę Azure Search, która obsługuje prywatny punkt końcowy platformy Azure dla połączeń z klientami w tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie prywatnego punktu końcowego dla bezpiecznego połączenia](service-create-private-endpoint.md).

### <a name="december-2019"></a>Grudzień 2019 r.

+ [Utwórz aplikację (wersja zapoznawcza)](search-create-app-portal.md) to nowy kreator w portalu, który generuje plik HTML do pobrania. Plik jest dostarczany z osadzonym skryptem, który renderuje operacyjną aplikację sieci web w stylu "localhost", powiązaną z indeksem w usłudze wyszukiwania. Strony można konfigurować w kreatorze i mogą zawierać pasek wyszukiwania, obszar wyników, nawigację po pasku bocznym i obsługę zapytań typu. Kod HTML można zmodyfikować w trybie offline, aby rozszerzyć lub dostosować przepływ pracy lub wygląd.

+ [Utwórz prywatny punkt końcowy dla bezpiecznych połączeń (wersja zapoznawcza)](service-create-private-endpoint.md) wyjaśnia, jak skonfigurować łącze prywatne dla bezpiecznych połączeń z usługą wyszukiwania. Ta funkcja w wersji zapoznawczej jest dostępna na żądanie i używa [usługi Azure Private Link](../private-link/private-link-overview.md) i Azure Virtual [Network](../virtual-network/virtual-networks-overview.md) jako część rozwiązania.

### <a name="november-2019---ignite-conference"></a>Listopad 2019 - Konferencja Ignite

+ [Przyrostowe wzbogacenie (wersja zapoznawcza)](cognitive-search-incremental-indexing-conceptual.md) dodaje buforowanie i statefullness do potoku wzbogacania, dzięki czemu można pracować nad określonymi krokami lub fazami bez utraty zawartości, która jest już przetworzona. Wcześniej wszelkie zmiany w potoku wzbogacania wymaga pełnej przebudowy. Wraz z przyrostowym wzbogaceniem zachowane są wyniki kosztownej analizy, zwłaszcza analizy obrazu.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Wyodrębnianie dokumentów (wersja zapoznawcza)](cognitive-search-skill-document-extraction.md) to umiejętność poznawcza używana podczas indeksowania, która umożliwia wyodrębnienie zawartości pliku z zestawu umiejętności. Wcześniej pękanie dokumentów wystąpiło tylko przed wykonaniem zestawu umiejętności. Z dodatkiem tej umiejętności, można również wykonać tę operację w ramach wykonywania skillset.

+ [Tłumaczenie tekstu](cognitive-search-skill-text-translation.md) jest umiejętnością poznawczą używaną podczas indeksowania, która ocenia tekst i dla każdego rekordu zwraca tekst przetłumaczony na określony język docelowy.

+ [Szablony usługi Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) mogą szybko rozpoczynać wizualizacje i analizę wzbogaconej zawartości w magazynie wiedzy na pulpicie usługi Power BI. Ten szablon jest przeznaczony dla projekcji tabel platformy Azure utworzonych za pomocą [Kreatora importu danych](knowledge-store-create-portal.md).

+ [Usługa Azure Data Lake Storage Gen2 (wersja zapoznawcza)](search-howto-index-azure-data-lake-storage.md), [interfejs API Programu Cosmos DB Gremlin (wersja zapoznawcza)](search-howto-index-cosmosdb.md)i [interfejs API Usługi Cosmos DB Cassandra (wersja zapoznawcza)](search-howto-index-cosmosdb.md) są teraz obsługiwane w indeksatorach. Możesz zarejestrować się za pomocą [tego formularza](https://aka.ms/azure-cognitive-search/indexer-preview). Po zaakceptowaniu do programu zapoznawczego otrzymasz wiadomość e-mail z potwierdzeniem.

### <a name="july-2019"></a>Lipiec 2019 r.

+ Ogólnie dostępne w [usłudze Azure Government Cloud](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Aktualizacje usług

[Anonse aktualizacji usługi](https://azure.microsoft.com/updates/?product=search&status=all) dla usługi Azure Cognitive Search można znaleźć w witrynie sieci Web platformy Azure.