---
title: Rozwiązywanie typowych problemów z indeksatorem wyszukiwania — Azure Search
description: Rozwiązywanie błędów i typowych problemów dotyczących indeksatorów w Azure Search, w tym połączenia ze źródłem danych, zapory i brakujących dokumentów.
author: mgottein
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: magottei
ms.openlocfilehash: 4692be287e9b38cf116107d2e7c1043f23a6b34b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640598"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>Rozwiązywanie typowych problemów indeksatora w Azure Search

Indeksatory mogą być uruchamiane w wielu problemach podczas indeksowania danych w Azure Search. Główne kategorie niepowodzeń obejmują:

* [Nawiązywanie połączenia ze źródłem danych](#data-source-connection-errors)
* [Przetwarzanie dokumentu](#document-processing-errors)
* [Pozyskiwanie dokumentów do indeksu](#index-errors)

## <a name="data-source-connection-errors"></a>Błędy połączenia ze źródłem danych

### <a name="blob-storage"></a>Blob Storage

#### <a name="storage-account-firewall"></a>Zapora konta magazynu

Usługa Azure Storage udostępnia konfigurowalną zaporę. Zapora jest domyślnie wyłączona, Azure Search może nawiązać połączenie z kontem magazynu.

Jeśli Zapora jest włączona, nie ma określonego komunikatu o błędzie. Zwykle błędy zapory są podobne do `The remote server returned an error: (403) Forbidden`.

Możesz sprawdzić, czy Zapora jest włączona w [portalu](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). Jedynym obsługiwanym obejściem jest wyłączenie zapory poprzez wybranie opcji Zezwalaj na dostęp ze [wszystkich sieci](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Jeśli indeksator nie ma dołączonego zestawu umiejętności, możesz spróbować [dodać wyjątek](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) dla adresów IP usługi wyszukiwania. Jednak ten scenariusz nie jest obsługiwany i nie jest gwarantowany jego działania.

Adres IP usługi wyszukiwania można sprawdzić, uruchamiając polecenie ping według nazwy FQDN (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>Indeksowanie nie jest włączone

Azure Search ma niejawną zależność od indeksowania Cosmos DB. Jeśli wyłączysz automatyczne indeksowanie w Cosmos DB, Azure Search zwróci stan pomyślne, ale nie będzie można indeksować zawartości kontenera. Aby uzyskać instrukcje dotyczące sprawdzania ustawień i włączania indeksowania, zobacz [Zarządzanie indeksowanie w Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Błędy przetwarzania dokumentu

### <a name="unprocessable-or-unsupported-documents"></a>Nieprzetworzone lub nieobsługiwane dokumenty

Indeksator obiektów BLOB umożliwia dokumentowanie [formatów dokumentów, które są jawnie obsługiwane.](search-howto-indexing-azure-blob-storage.md#supported-document-formats) Czasami kontener magazynu obiektów BLOB zawiera nieobsługiwane dokumenty. Inne przypadki mogą wystąpić problemy z dokumentami. Możesz uniknąć zatrzymywania indeksatora w tych dokumentach, [zmieniając opcje konfiguracji](search-howto-indexing-azure-blob-storage.md#dealing-with-errors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Brak zawartości dokumentu

Indeksator obiektów BLOB [odnajduje i wyodrębnia tekst z obiektów BLOB w kontenerze](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Niektóre problemy z wyodrębnianiem tekstu obejmują:

* Dokument zawiera tylko zeskanowane obrazy. Obiekty blob PDF, które zawierają zawartość nietekstową, na przykład zeskanowane obrazy (JPGs), nie generują wyników w standardowym potoku indeksowania obiektów BLOB. Jeśli masz zawartość obrazu z elementami tekstowymi, możesz użyć [wyszukiwania poznawczego](cognitive-search-concept-image-scenarios.md) do znajdowania i wyodrębniania tekstu.
* Indeksator obiektu BLOB jest skonfigurowany tylko do indeksowania metadanych. Aby wyodrębnić zawartość, indeksator obiektu BLOB musi być skonfigurowany do [wyodrębniania zawartości i metadanych](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Błędy indeksowania

### <a name="missing-documents"></a>Brakujące dokumenty

Indeksatory Znajdź dokumenty ze [źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Czasami dokument ze źródła danych, który powinien być indeksowany, prawdopodobnie nie znajduje się w indeksie. Istnieje kilka typowych powodów, dla których mogą wystąpić te błędy:

* Dokument nie został zindeksowany. Sprawdź, czy Portal zakończył działanie indeksatora.
* Dokument został zaktualizowany po uruchomieniu indeksatora. Jeśli indeksator jest zgodnie z harmonogramem [](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), zostanie on ostatecznie uruchomiony i zostanie pobrany do dokumentu.
* [Zapytanie](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) określone w źródle danych wyklucza dokument. Indeksatory nie mogą indeksować dokumentów, które nie są częścią źródła danych.
* [Mapowania pól](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) lub [wyszukiwania poznawczego](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) zmieniły dokument i wyglądają inaczej niż oczekiwano.
* Użyj [interfejsu API dokumentu wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/lookup-document) , aby znaleźć dokument.
