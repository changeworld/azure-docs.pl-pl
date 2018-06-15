---
title: Dodawanie do magazynu obiektów Blob Azure Search | Dokumentacja firmy Microsoft
description: Tworzenie indeksu za pomocą kodu przy użyciu interfejsu API REST protokołu HTTP usługi Azure Search
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/04/2017
author: chaosrealm
manager: jlembicz
ms.author: eugenesh
ms.openlocfilehash: 71e43920f0e6a64beb7cdb28d0707dd30502bf05
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790784"
---
# <a name="searching-blob-storage-with-azure-search"></a>Przeszukiwanie magazynu obiektów blob za pomocą usługi Azure Search

Wyszukiwanie w różnych typów zawartości, przechowywane w magazynie obiektów Blob platformy Azure może być trudne problem do rozwiązania. Można jednak indeksu i wyszukiwanie zawartości obiektów blob za pomocą kilku kliknięć za pomocą usługi Azure Search. Wyszukiwanie w magazynie obiektów Blob wymaga inicjowania obsługi usługi Azure Search. Różne ograniczenia usługi i warstw cenowych usługi Azure Search znajduje się na [cennikiem](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Co to jest usługa Azure Search?
[Usługa Azure Search](https://aka.ms/whatisazsearch) jest usługą wyszukiwania, która ułatwia deweloperom Dodaj niezawodne wyszukiwania pełnotekstowego napotyka sieci web i aplikacji dla urządzeń przenośnych. Usługa Azure Search eliminuje potrzebę zarządzania infrastrukturą żadnych wyszukiwania podczas oferty [dostępności 99,9% SLA](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Formaty dokumentu enterprise indeksu i wyszukiwania
Z obsługą [dokumentu wyodrębniania](https://aka.ms/azsblobindexer) w magazynie obiektów Blob platformy Azure może indeksować następującą zawartość:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Wyodrębnianie tekstu i metadanych z tych typów plików, można przeszukać wiele formatów plików z pojedynczego zapytania. 

## <a name="search-through-your-blob-metadata"></a>Przeszukaj metadane obiektu blob
Typowy scenariusz, który ułatwia sortowanie za pośrednictwem obiektów blob typu zawartości jest indeksu zarówno niestandardowych metadanych i właściwości dla każdego obiektu blob. W ten sposób informacje dla wszystkich obiektów blob jest indeksowany niezależnie od tego typu dokumentu. Możesz następnie przejść do sortowania, filtrować i aspekt przez całą zawartość magazynu obiektów Blob.

[Dowiedz się więcej na temat indeksowania metadane obiektu blob.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Obraz wyszukiwania
Wyszukiwanie pełnotekstowe wyszukiwanie Azure, nawigacji aspektowej i funkcje sortowania teraz mogą być stosowane do metadanych obrazy przechowywane w obiektach blob.

Jeśli te obrazy są wstępnie przetwarzane przy użyciu [komputera wizji API](https://www.microsoft.com/cognitive-services/computer-vision-api) z kognitywnych usług firmy Microsoft, jest możliwość indeksu visual zawartości każdego obrazu, w tym Rozpoznawania i pisma ręcznego. Pracujemy nad dodaniem Rozpoznawania i innych możliwości przetwarzania obrazu bezpośrednio do usługi Azure Search, jeśli planuje się tych funkcji, Prześlij żądanie na naszych [UserVoice](https://aka.ms/azsuv) lub [Napisz do nas](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Indeks i wyszukiwania za pomocą obiektów blob JSON
Usługa wyszukiwanie Azure można skonfigurować do wyodrębniania zawartości strukturalnych znaleziono w obiektach blob, które zawierają JSON. Usługa Azure Search może odczytywać obiekty BLOB JSON i przeanalizować uporządkowana zawartość w odpowiednich polach dokument usługi Azure Search. Usługa wyszukiwanie Azure można również wykonać obiektów blob, które zawiera tablicę obiektów JSON i mapowanie każdy element na oddzielnych dokumentu usługi Azure Search.

Analiza kodu JSON nie jest obecnie można konfigurować za pośrednictwem portalu. [Dowiedz się więcej na temat analizowania JSON w usłudze Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Szybki start
Usługa wyszukiwanie Azure można dodać do obiektów blob bezpośrednio ze strony portalu magazynu obiektów Blob.

![](./media/search-blob-storage-integration/blob-blade.png)

Kliknij przycisk **Dodaj usługi Azure Search** można uruchomić przepływu, w którym można wybrać istniejącą usługę Azure Search lub Utwórz nową usługę. Jeśli utworzysz nową usługę, są przesłane możliwości portalu konta magazynu. Można przejść z powrotem do strony portalu magazynu i wybierz ponownie **Dodaj usługi Azure Search** opcja, w którym można wybrać istniejącą usługę.

### <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o indeksatora obiektów Blob Azure wyszukiwania z pełnym [dokumentacji](https://aka.ms/azsblobindexer).
