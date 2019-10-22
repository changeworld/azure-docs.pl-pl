---
title: Dodawanie wyszukiwania pełnotekstowego do usługi Azure Blob Storage
titleSuffix: Azure Search
description: Wyodrębnij zawartość i Dodaj strukturę do obiektów blob platformy Azure podczas kompilowania indeksu wyszukiwania pełnotekstowego w Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 6d8ad27766e85568cc759af84cc586476ad3ef9a
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678436"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Dodawanie wyszukiwania pełnotekstowego do danych obiektów blob platformy Azure przy użyciu Azure Search

Wyszukiwanie w różnych typach zawartości przechowywanych w usłudze Azure Blob Storage może być trudnym problemem do rozwiązania. Można jednak indeksować i przeszukiwać zawartość obiektów BLOB za pomocą zaledwie kilku kliknięć przy użyciu [Azure Search](search-what-is-azure-search.md). Azure Search ma wbudowaną integrację do indeksowania poza magazynem obiektów BLOB za pomocą [*indeksatora obiektów BLOB*](search-howto-indexing-azure-blob-storage.md) , który dodaje możliwości rozpoznawania źródła danych do indeksowania.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Co oznacza Dodawanie wyszukiwania pełnotekstowego do danych obiektów BLOB

Azure Search to usługa wyszukiwania w chmurze, która udostępnia aparaty indeksowania i zapytań, które działają przez zdefiniowane przez użytkownika indeksy hostowane w usłudze wyszukiwania. Wspólne lokalizowanie zawartości z możliwością wyszukiwania za pomocą aparatu zapytań w chmurze jest niezbędne w celu uzyskania wydajności, zwracając wyniki z szybkością szukania przez użytkowników.

Azure Search integruje się z usługą Azure Blob Storage w warstwie indeksowania, importując zawartość obiektu BLOB jako dokumenty wyszukiwania indeksowane w *odwróconych indeksach* i innych strukturach zapytań, które obsługują kwerendy tekstowe w postaci nieodpłatnej i wyrażenia filtru. Ponieważ zawartość obiektu BLOB jest indeksowana do indeksu wyszukiwania, dostęp do zawartości obiektu BLOB może korzystać z pełnego zakresu funkcji zapytania w Azure Search.

Po utworzeniu i wypełnieniu indeksu istnieje on niezależnie od kontenera obiektów blob, ale można ponownie uruchomić operacje indeksowania, aby odświeżyć indeks z uwzględnieniem zmian w źródłowym kontenerze. Informacje o znacznikach czasu dla poszczególnych obiektów BLOB są używane na potrzeby wykrywania zmian. Można wybrać opcję zaplanowanego wykonania lub indeksowania na żądanie jako mechanizmu odświeżania.

Dane wejściowe są obiektami BLOB w jednym kontenerze w usłudze Azure Blob Storage. Obiekty blob mogą być niemal dowolnym rodzajem danych tekstowych. Jeśli obiekty blob zawierają obrazy, można dodać [wzbogacanie AI do indeksowania obiektów BLOB](search-blob-ai-integration.md) w celu utworzenia i wyodrębnienia tekstu z obrazów.

Dane wyjściowe są zawsze indeksem Azure Search używanym do szybkiego wyszukiwania tekstu, pobierania i eksploracji w aplikacjach klienckich. W obszarze między jest architekturą potoku indeksowania. Potok jest oparty na funkcji *indeksatora* omówionej w dalszej sekcji tego artykułu.

## <a name="start-with-services"></a>Zacznij od usług

Potrzebujesz Azure Search i usługi Azure Blob Storage. W magazynie obiektów BLOB wymagany jest kontener, który udostępnia zawartość źródłową.

Możesz rozpocząć bezpośrednio na stronie portalu konta magazynu. Na lewej stronie nawigacyjnej w obszarze **BLOB Service** kliknij pozycję **Dodaj Azure Search** , aby utworzyć nową usługę, lub wybierz istniejącą. 

Po dodaniu Azure Search do konta magazynu można wykonać standardowy proces, aby zindeksować dane obiektów BLOB. Zaleca się, aby Kreator **importu danych** w Azure Search na potrzeby szybkiego wprowadzenia, lub wywołać interfejsy API REST za pomocą narzędzia takiego jak Poster. Ten samouczek przeprowadzi Cię przez kroki wywoływania interfejsu API REST w programie Poster: [indeksowanie i wyszukiwanie danych częściowo strukturalnych (obiektów BLOB JSON) w Azure Search](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Używanie indeksatora obiektów BLOB

*Indeksator* to podusługa z obsługą źródła danych, która jest wyposażona w wewnętrzną logikę do próbkowania danych, odczytywanie danych metadanych, pobieranie danych i Serializowanie danych z formatów natywnych do dokumentów JSON w celu późniejszego zaimportowania. 

Obiekty blob w usłudze Azure Storage są indeksowane przy użyciu [indeksatora magazynu obiektów blob Azure Search](search-howto-indexing-azure-blob-storage.md). Można wywołać ten indeksator przy użyciu kreatora **importu danych** , interfejsu API REST lub zestawu .NET SDK. W kodzie, używasz tego indeksatora, ustawiając typ i dostarczając informacje o połączeniu, które obejmują konto usługi Azure Storage wraz z kontenerem obiektów BLOB. Obiekty blob można podzbiórować przez utworzenie katalogu wirtualnego, który można następnie przekazać jako parametr lub przez filtrowanie według rozszerzenia typu pliku.

Indeksator wykonuje "pęknięcia dokumentu", otwierając obiekt BLOB w celu przeprowadzenia inspekcji zawartości. Po nawiązaniu połączenia ze źródłem danych jest to pierwszy krok w potoku. W przypadku danych obiektów BLOB są wykrywane pliki PDF, Office docs i inne typy zawartości. Łamanie dokumentów przy użyciu wyodrębniania tekstu nie jest naliczane. Jeśli obiekty blob zawierają zawartość obrazu, obrazy są ignorowane, chyba że [dodasz wzbogacanie AI](search-blob-ai-integration.md). Standardowe indeksowanie dotyczy tylko zawartości tekstowej.

Indeksator obiektów BLOB zawiera parametry konfiguracji i obsługuje śledzenie zmian, jeśli dane podstawowe zawierają wystarczające informacje. Więcej informacji na temat podstawowych funkcji można znaleźć w [Azure Search indeksator magazynu obiektów BLOB](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Obsługiwane typy zawartości

Uruchamiając indeksator obiektów BLOB w kontenerze, można wyodrębnić tekst i metadane z następujących typów zawartości za pomocą pojedynczego zapytania:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indeksowanie metadanych obiektu BLOB

Typowym scenariuszem, który ułatwia sortowanie za pomocą obiektów BLOB dowolnego typu zawartości, jest indeksowanie zarówno niestandardowych metadanych, jak i właściwości systemu dla każdego obiektu BLOB. W ten sposób informacje o wszystkich obiektach Blob są indeksowane niezależnie od typu dokumentu, przechowywane w indeksie na Azure Search. Korzystając z nowego indeksu, można następnie posortować, filtrować i aspektować zawartość całej zawartości magazynu obiektów BLOB.

### <a name="indexing-json-blobs"></a>Indeksowanie obiektów BLOB JSON
Azure Search można skonfigurować do wyodrębniania zawartości strukturalnej znalezionej w obiektach Blob, które zawierają kod JSON. Azure Search może odczytywać obiekty blob JSON i analizować zawartość strukturalną do odpowiednich pól Azure Search dokumentu. Azure Search może również przyjmować obiekty blob, które zawierają tablicę obiektów JSON i mapują każdy element do oddzielnego Azure Search dokumentu. Można ustawić tryb analizowania, który ma wpływ na typ obiektu JSON utworzonego przez indeksator.

## <a name="search-blob-content-in-a-search-index"></a>Wyszukaj zawartość obiektu BLOB w indeksie wyszukiwania 

Dane wyjściowe indeksowania to indeks wyszukiwania używany do interaktywnej eksploracji przy użyciu dowolnego tekstu i filtrowanych zapytań w aplikacji klienckiej. W przypadku początkowej eksploracji i weryfikacji zawartości zalecamy rozpoczęcie pracy z [Eksploratorem wyszukiwania](search-explorer.md) w portalu, aby sprawdzić strukturę dokumentu. Możesz użyć [prostej składni zapytania](query-simple-syntax.md), [pełnej składni zapytania](query-lucene-syntax.md)i [składni wyrażenia filtru](query-odata-filter-orderby-syntax.md) w Eksploratorze wyszukiwania.

Bardziej trwałe rozwiązanie polega na zebraniu danych wejściowych zapytania i zaprezentowaniu odpowiedzi jako wyników wyszukiwania w aplikacji klienckiej. W poniższym C# samouczku opisano sposób tworzenia aplikacji wyszukiwania: [Tworzenie pierwszej aplikacji w Azure Search](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Następne kroki

+ [Przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą Azure Portal (Azure Blob Storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Konfigurowanie indeksatora obiektów BLOB (Azure Search)](search-howto-indexing-azure-blob-storage.md) 
