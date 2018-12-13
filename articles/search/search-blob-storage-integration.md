---
title: Dodaj wyszukiwanie pełnotekstowe w usłudze Azure Blob Storage — usługi Azure Search
description: Zawartość tekstowa przeszukiwanie magazynu obiektów Blob platformy Azure na potrzeby usługi Azure Search indeksowanie, w kodzie przy użyciu interfejsu API REST protokołu HTTP.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: 7bd7bcf66f0d91a87519a5d5bff7df7c73e2603f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310882"
---
# <a name="searching-blob-storage-with-azure-search"></a>Przeszukiwanie magazynu obiektów blob za pomocą usługi Azure Search

Wyszukiwanie różnych typów zawartości, przechowywane w usłudze Azure Blob storage może być trudne problemu do rozwiązania. Można jednak indeksowanie i wyszukiwanie zawartości obiektów blob za pomocą kilku kliknięć za pomocą usługi Azure Search. Wyszukiwanie w magazynie obiektów Blob wymaga inicjowania obsługi usługi Azure Search. Różne limity usług i warstw cenowych usługi Azure Search znajduje się na [stronę z cennikiem](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Co to jest usługa Azure Search?
[Usługa Azure Search](https://aka.ms/whatisazsearch) Usługa wyszukiwania, która ułatwia deweloperom Dodawanie zaawansowanego wyszukiwania pełnotekstowego, środowisk sieci web i aplikacji mobilnych. Jako usługa, usługi Azure Search eliminuje konieczność zarządzania dowolną infrastrukturą wyszukiwania podczas oferty [przez 99,9% czasu SLA](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Indeksowanie i wyszukiwanie formatowania dokumentu przedsiębiorstwa
Dzięki obsłudze [dokumentu wyodrębniania](https://aka.ms/azsblobindexer) w usłudze Azure Blob storage umożliwia indeksowanie następującej zawartości:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Dzięki możliwości wyodrębniania tekstu i metadanych z tych typów plików, możesz dodatkowo przeszukiwać wiele formatów plików za pomocą jednego zapytania. 

## <a name="search-through-your-blob-metadata"></a>Przeszukaj metadane obiektu blob
Typowy scenariusz, który ułatwia przeglądanie obiektów blob, typu zawartości jest indeks zarówno niestandardowych metadanych, jak i właściwości dla każdego obiektu blob. W ten sposób informacje dla wszystkich obiektów blob są indeksowane niezależnie od typu dokumentu. Możesz następnie przejść do sortowania, filtrowania i reguł w całej zawartości z magazynu obiektów Blob.

[Dowiedz się więcej na temat indeksowania metadane obiektu blob.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Wyszukiwanie obrazów
Wyszukiwanie pełnotekstowe, nawigacji aspektowej i sortowania możliwości usługi Azure Search można teraz stosować do metadanych obrazów przechowywanych w obiektach blob.

Jeśli te obrazy są wstępnie przetwarzane przy użyciu [interfejs API przetwarzania obrazów](https://www.microsoft.com/cognitive-services/computer-vision-api) od firmy Microsoft Cognitive Services, możliwe jest indeksowanie zawartości wizualnej znajdującej każdego obrazu, w tym optyczne rozpoznawanie znaków i pisma ręcznego. Pracujemy nad dodaniem optyczne rozpoznawanie znaków i inne możliwości przetwarzania obrazu bezpośrednio do usługi Azure Search, jeśli interesują Cię te możliwości, Prześlij żądanie na naszych [UserVoice](https://aka.ms/azsuv) lub [Wyślij wiadomość e-mail adres](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Indeksowania i wyszukiwania za pomocą obiektów blob JSON
Usługa Azure Search można skonfigurować do wyodrębnienia ze strukturą zawartości znajdującej się w obiektach blob, które zawierają JSON. Usługa Azure Search może odczytywać obiektów blob JSON i przeanalizować zawartość ze strukturą w odpowiednich polach dokumentu usługi Azure Search. Usługa Azure Search możesz skorzystać z obiektów blob, które zawiera tablicę obiektów JSON i mapowanie każdy element na osobny dokument usługi Azure Search.

Analizowanie JSON nie jest obecnie można konfigurować za pośrednictwem portalu. [Dowiedz się więcej na temat analizowanie JSON w usłudze Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Szybki start
Usługa Azure Search można dodać do obiektów blob bezpośrednio na stronie portalu magazynu obiektów Blob.

![](./media/search-blob-storage-integration/blob-blade.png)

Kliknij przycisk **Dodaj usługę Azure Search** można uruchomić przepływu, w którym możesz wybrać istniejącą usługę Azure Search lub Utwórz nową usługę. Jeśli tworzysz nową usługę, są przejście poza środowisko portalu konta usługi Storage. Możesz przejść z powrotem do strony portalu magazynu i wybierz ponownie **Dodaj usługę Azure Search** opcji, w którym można wybrać istniejącą usługę.

### <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o indeksatora obiektów Blob usługi Azure Search, w pełni [dokumentacji](https://aka.ms/azsblobindexer).
