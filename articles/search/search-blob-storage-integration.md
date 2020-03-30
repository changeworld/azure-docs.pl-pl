---
title: Dodawanie wyszukiwania pełnotekstowym do usługi Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Wyodrębnij zawartość i dodaj strukturę do obiektów blob platformy Azure podczas tworzenia indeksu wyszukiwania pełnotekstowego w uchu usługi Azure Cognitive.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af7d04bd74ada296b9f0e0f7c149c2a781cec579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496465"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Dodawanie wyszukiwania pełnotekstowym do danych obiektów blob platformy Azure przy użyciu usługi Azure Cognitive Search

Przeszukiwanie różnych typów zawartości przechowywanych w magazynie obiektów Blob platformy Azure może być trudnym problemem do rozwiązania. Jednak za pomocą usługi Azure Cognitive Search można indeksować i przeszukiwać zawartość obiektów blob za pomocą funkcji [Azure Cognitive Search.](search-what-is-azure-search.md) Usługa Azure Cognitive Search ma wbudowaną integrację do indeksowania z magazynu obiektów Blob za pośrednictwem [*indeksatora obiektów Blob,*](search-howto-indexing-azure-blob-storage.md) który dodaje możliwości obsługujące źródło danych do indeksowania.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Co to znaczy dodać wyszukiwanie pełnotekstowe do danych obiektów blob

Usługa Azure Cognitive Search to usługa wyszukiwania w chmurze, która udostępnia aparaty indeksowania i zapytań, które działają za pomocą indeksów zdefiniowanych przez użytkownika hostowanych w usłudze wyszukiwania. Współlokowanie zawartości z możliwością wyszukiwania z wyszukiwarką zapytań w chmurze jest niezbędne do uzyskania wydajności, a wyniki są zwracane z szybkością, jaką użytkownicy oczekują od zapytań wyszukiwania.

Usługa Azure Cognitive Search integruje się z magazynem obiektów Blob platformy Azure w warstwie indeksowania, importując zawartość obiektu blob jako dokumenty wyszukiwania indeksowane *do odwróconych indeksów* i innych struktur zapytań obsługujących zapytania tekstowe w postaci swobodnej i wyrażenia filtru. Ponieważ zawartość obiektu blob jest indeksowana do indeksu wyszukiwania, dostęp do zawartości obiektu blob może korzystać z pełnego zakresu funkcji zapytań w usłudze Azure Cognitive Search.

Po utworzeniu i wypełnieniu indeksu istnieje niezależnie od kontenera obiektów blob, ale można ponownie uruchomić operacje indeksowania, aby odświeżyć indeks ze zmianami w kontenerze źródłowym. Informacje o sygnaturach czasowych poszczególnych obiektów blob są używane do wykrywania zmian. Można wybrać zaplanowane wykonanie lub indeksowanie na żądanie jako mechanizm odświeżania.

Dane wejściowe są obiekty blobs, w jednym kontenerze, w magazynie obiektów Blob platformy Azure. Obiekty BLOB mogą być prawie każdym rodzajem danych tekstowych. Jeśli obiekty blob zawierają obrazy, można dodać [wzbogacenie AI do indeksowania obiektów blob,](search-blob-ai-integration.md) aby utworzyć i wyodrębnić tekst z obrazów.

Dane wyjściowe to zawsze indeks usługi Azure Cognitive Search, używany do szybkiego wyszukiwania tekstu, pobierania i eksploracji w aplikacjach klienckich. W międzyczasie jest sama architektura potoku indeksowania. Potok jest oparty na funkcji *indeksatora,* omówione dalej w tym artykule.

## <a name="start-with-services"></a>Zacznij od usług

Potrzebujesz usługi Azure Cognitive Search i usługi Azure Blob storage. W magazynie obiektów Blob potrzebny jest kontener, który udostępnia zawartość źródłową.

Możesz rozpocząć bezpośrednio na stronie portalu konta magazynu. Na lewej stronie nawigacji w obszarze **Usługa obiektów Blob** kliknij pozycję **Dodaj usługę Azure Cognitive Search,** aby utworzyć nową usługę lub wybierz istniejącą. 

Po dodaniu usługi Azure Cognitive Search do konta magazynu, można wykonać standardowy proces indeksowania danych obiektów blob. Zalecamy **kreatora importu danych** w usłudze Azure Cognitive Search w celu łatwego początkowego wprowadzenia lub wywołanie interfejsów API REST przy użyciu narzędzia, takiego jak Postman. W tym samouczku przejdziesz przez kroki wywoływania interfejsu API REST w postman: [Indeks i wyszukiwanie danych półstrukturatywnych (JSON blob) w usłudze Azure Cognitive Search](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Używanie indeksatora obiektów blob

*Indeksator* jest podusługą obsługującą źródła danych wyposażoną w wewnętrzną logikę pobierania próbek danych, odczytywania danych metadanych, pobierania danych i serializacji danych z formatów natywnych do dokumentów JSON w celu późniejszego importu. 

Obiekty BLOB w usłudze Azure Storage są indeksowane przy użyciu [indeksatora magazynu obiektów Blob usługi Azure Cognitive Search.](search-howto-indexing-azure-blob-storage.md) Ten indeksator można wywołać za pomocą **kreatora importu danych,** interfejsu API REST lub sdk .NET. W kodzie używasz tego indeksatora, ustawiając typ i udostępniając informacje o połączeniu, które zawiera konto usługi Azure Storage wraz z kontenerem obiektów blob. Obiekty blob można podzesetować, tworząc katalog wirtualny, który można następnie przekazać jako parametr, lub filtrując rozszerzenie typu pliku.

Indeksator wykonuje "pękanie dokumentów", otwierając obiekt blob do inspekcji zawartości. Po nawiązaniu połączenia ze źródłem danych jest to pierwszy krok w potoku. W przypadku danych obiektów blob jest to miejsce, w którym są wykrywane pliki PDF, dokumenty pakietu Office i inne typy zawartości. Pękanie dokumentów z wyodrębnieniem tekstu jest bez opłat. Jeśli obiekty blob zawierają zawartość obrazu, obrazy są ignorowane, chyba że [dodasz wzbogacenie SI](search-blob-ai-integration.md). Standardowe indeksowanie dotyczy tylko zawartości tekstowej.

Indeksator obiektów blob jest wyposażony w parametry konfiguracji i obsługuje śledzenie zmian, jeśli dane źródłowe zawiera wystarczające informacje. Więcej informacji na temat podstawowych funkcji w [indeksatorze magazynu obiektów Blob usługi Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Obsługiwane typy zawartości

Uruchamiając indeksator obiektów blob za pomocą kontenera, można wyodrębnić tekst i metadane z następujących typów zawartości za pomocą jednej kwerendy:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indeksowanie metadanych obiektów blob

Typowy scenariusz, który ułatwia sortowanie obiektów blob dowolnego typu zawartości jest indeksowanie zarówno metadanych niestandardowych i właściwości systemu dla każdego obiektu blob. W ten sposób informacje dla wszystkich obiektów blob są indeksowane niezależnie od typu dokumentu, przechowywane w indeksie w usłudze wyszukiwania. Za pomocą nowego indeksu, można następnie przystąpić do sortowania, filtrowania i aspektu we wszystkich zawartości magazynu obiektów Blob.

### <a name="indexing-json-blobs"></a>Indeksowanie obiektów blob JSON
Indeksatory można skonfigurować do wyodrębniania zawartości strukturalnej znalezionej w obiektach blob zawierających JSON. Indeksator może odczytywać obiekty blob JSON i analizować zawartość strukturalną w odpowiednich polach dokumentu wyszukiwania. Indeksatory można również wziąć obiekty blob, które zawierają tablicę obiektów JSON i mapować każdy element do oddzielnego dokumentu wyszukiwania. Można ustawić tryb analizy, aby mieć wpływ na typ obiektu JSON utworzonego przez indeksatora.

## <a name="search-blob-content-in-a-search-index"></a>Wyszukiwanie zawartości obiektu blob w indeksie wyszukiwania 

Dane wyjściowe indeksowania jest indeks wyszukiwania, używane do interaktywnej eksploracji przy użyciu tekstu wolnego i filtrowane zapytania w aplikacji klienckiej. W celu wstępnej eksploracji i weryfikacji zawartości zalecamy rozpoczęcie od [Eksploratora wyszukiwania](search-explorer.md) w portalu w celu zbadania struktury dokumentu. W Eksploratorze wyszukiwania można użyć [składni kwerendy](query-simple-syntax.md) [prostej, pełnej składni kwerendy](query-lucene-syntax.md)i [składni wyrażenia filtru.](query-odata-filter-orderby-syntax.md)

Bardziej trwałe rozwiązanie jest zbieranie danych wejściowych kwerendy i przedstawić odpowiedź jako wyniki wyszukiwania w aplikacji klienckiej. Poniższy samouczek języka C# wyjaśnia, jak utworzyć aplikację wyszukiwania: [Tworzenie pierwszej aplikacji w usłudze Azure Cognitive Search.](tutorial-csharp-create-first-app.md)

## <a name="next-steps"></a>Następne kroki

+ [Przekazywanie, pobieranie i listy obiektów blob za pomocą witryny Azure portal (Azure Blob storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Konfigurowanie indeksatora obiektów blob (usługi Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
