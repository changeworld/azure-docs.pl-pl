---
title: Powiadomienia o nowych funkcjach
titleSuffix: Azure Cognitive Search
description: Anonse nowych i ulepszonych funkcji, w tym zmiana nazwy usługi Azure Search na platformę Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 2627e4be20d80251e3753e46624c58a0c6244aba
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863091"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co nowego w usłudze Azure Wyszukiwanie poznawcze

Dowiedz się, co nowego w usłudze. Oznacz Tę stronę zakładką, aby zapewnić aktualność usługi.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nowa nazwa usługi dla Azure Search

Azure Search teraz zmieniono nazwę na **platformę Azure wyszukiwanie poznawcze** w celu odzwierciedlenia rozszerzonego użycia umiejętności poznawczych i przetwarzania AI w operacjach podstawowych. Chociaż umiejętności poznawcze dodają nowe możliwości, korzystanie z AI jest absolutnie opcjonalne. Możesz nadal używać platformy Azure Wyszukiwanie poznawcze bez systemu AI do kompilowania rozbudowanych rozwiązań wyszukiwania pełnotekstowego za pośrednictwem prywatnej, heterogenicznychowej zawartości opartej na tekście w indeksie tworzonym i zarządzanym w chmurze. 

Wersje interfejsu API, pakiety NuGet, przestrzenie nazw i punkty końcowe nie są zmieniane. Zmiany nazwy usługi nie wpływają na istniejące rozwiązania wyszukiwania.

## <a name="feature-announcements"></a>Anonse funkcji

### <a name="january-2020"></a>Styczeń 2020

+ [Klucze szyfrowania zarządzane przez klienta](search-security-manage-encryption-keys.md) są teraz ogólnie dostępne. Jeśli używasz usługi REST, możesz uzyskać dostęp do tej funkcji przy użyciu `api-version=2019-05-06`. W przypadku kodu zarządzanego prawidłowy pakiet nadal jest [zestawem SDK .NET w wersji 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , nawet jeśli ta funkcja nie jest dostępna w wersji zapoznawczej. 

+ *Ograniczony dostęp do adresów IP i prywatny punkt końcowy (wersja zapoznawcza)* w punkcie końcowym usługi wyszukiwania jest teraz dostępny w **wersji API-Version = 2019-10 -01-Preview**. Bezpieczny punkt końcowy można skonfigurować przy użyciu nowych właściwości **IpRule** i **NetworkRuleSet** w interfejsie API REST usługi [Create lub Update](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate) Management. Aby uzyskać więcej informacji na temat wersji interfejsu API i regionalnej dostępności, zobacz [jak używać interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="december-2019"></a>Grudzień 2019 r.

+ [Tworzenie aplikacji (wersja zapoznawcza)](search-create-app-portal.md) to nowy Kreator w portalu, który GENERUJE plik HTML do pobrania. Plik zawiera osadzony skrypt, który renderuje operacyjną aplikację sieci Web w stylu "localhost", powiązaną z indeksem w usłudze wyszukiwania. Strony można konfigurować w Kreatorze i zawierać pasek wyszukiwania, obszar wyników, nawigację paska bocznego i obsługę zapytań typeahead. Możesz zmodyfikować kod HTML w trybie offline, aby zwiększyć lub dostosować przepływ pracy lub wygląd.

+ [Utwórz prywatny punkt końcowy dla bezpiecznych połączeń (wersja zapoznawcza)](service-create-private-endpoint.md) zawiera opis sposobu konfigurowania prywatnego linku dla bezpiecznych połączeń do usługi wyszukiwania. Ta funkcja w wersji zapoznawczej jest dostępna na żądanie i używa [prywatnego linku platformy Azure](../private-link/private-link-overview.md) i [usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md) w ramach rozwiązania.

### <a name="november-2019---ignite-conference"></a>Listopad 2019 — Konferencja zapłonowa

+ [Wzbogacanie przyrostowe (wersja zapoznawcza)](cognitive-search-incremental-indexing-conceptual.md) dodaje buforowanie i statefullness do potoku wzbogacania, dzięki czemu można korzystać z określonych etapów lub faz bez utraty zawartości, która została już przetworzona. Wcześniej każda zmiana potoku wzbogacania wymagała pełnej kompilacji. W przypadku przyrostowego wzbogacania dane wyjściowe analizy kosztów, w szczególności analizy obrazów, są zachowywane.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Wyodrębnianie dokumentu (wersja zapoznawcza)](cognitive-search-skill-document-extraction.md) to umiejętność wykorzystywana podczas indeksowania, która pozwala wyodrębnić zawartość pliku z zestawu umiejętności. Wcześniej przed wykonaniem zestawu umiejętności tylko złamanie dokumentu. Po dodaniu tej umiejętności można także wykonać tę operację w ramach wykonywania zestawu umiejętności.

+ [Tłumaczenie tekstu (wersja zapoznawcza)](cognitive-search-skill-text-translation.md) to umiejętność wykorzystywana podczas indeksowania, która oblicza tekst i dla każdego rekordu zwraca tekst przetłumaczony na określony język docelowy.

+ [Szablony Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) mogą szybko Rozpocznij pracę wizualizacje i analizę wzbogaconej zawartości w sklepie z Power BI Desktop. Ten szablon jest przeznaczony dla projekcji tabel platformy Azure utworzonych za pomocą [Kreatora importu danych](knowledge-store-create-portal.md).

+ W indeksatorach są teraz obsługiwane [Azure Data Lake Storage Gen2 (wersja zapoznawcza)](search-howto-index-azure-data-lake-storage.md) [Cosmos DB, interfejs API Gremlin (wersja](search-howto-index-cosmosdb.md)zapoznawcza) i [Cosmos DB interfejs API Cassandra (wersja zapoznawcza)](search-howto-index-cosmosdb.md) . Możesz zarejestrować się, korzystając z [tego formularza](https://aka.ms/azure-cognitive-search/indexer-preview). Po zaakceptowaniu w programie w wersji zapoznawczej otrzymasz wiadomość e-mail z potwierdzeniem.

### <a name="july-2019"></a>Lipiec 2019 r.

+ Ogólnie dostępna w [chmurze Azure Government](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Aktualizacje usług

[Anonse aktualizacji usługi](https://azure.microsoft.com/updates/?product=search&status=all) dla systemu Azure wyszukiwanie poznawcze można znaleźć w witrynie sieci Web systemu Azure.