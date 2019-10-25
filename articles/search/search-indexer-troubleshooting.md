---
title: Rozwiązywanie typowych problemów dotyczących indeksatora wyszukiwania
titleSuffix: Azure Cognitive Search
description: Rozwiązywanie błędów i typowych problemów dotyczących indeksatorów na platformie Azure Wyszukiwanie poznawcze, w tym połączenia ze źródłem danych, zapory i brakujących dokumentów.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c5a16d957f1e0414f92d0cc03442d88d438e4c92
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793625"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Rozwiązywanie typowych problemów indeksatora na platformie Azure Wyszukiwanie poznawcze

Indeksatory mogą być uruchamiane w ramach wielu problemów podczas indeksowania danych na platformie Azure Wyszukiwanie poznawcze. Główne kategorie niepowodzeń obejmują:

* [Nawiązywanie połączenia ze źródłem danych](#data-source-connection-errors)
* [Przetwarzanie dokumentu](#document-processing-errors)
* [Pozyskiwanie dokumentów do indeksu](#index-errors)

## <a name="data-source-connection-errors"></a>Błędy połączenia ze źródłem danych

### <a name="blob-storage"></a>Blob Storage

#### <a name="storage-account-firewall"></a>Zapora konta magazynu

Usługa Azure Storage udostępnia konfigurowalną zaporę. Zapora jest domyślnie wyłączona, ponieważ usługa Azure Wyszukiwanie poznawcze może nawiązać połączenie z kontem magazynu.

Jeśli Zapora jest włączona, nie ma określonego komunikatu o błędzie. Zwykle błędy zapory wyglądają jak `The remote server returned an error: (403) Forbidden`.

Możesz sprawdzić, czy Zapora jest włączona w [portalu](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). Jedynym obsługiwanym obejściem jest wyłączenie zapory poprzez wybranie opcji Zezwalaj na dostęp ze [wszystkich sieci](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Jeśli indeksator nie ma dołączonego zestawu umiejętności, _możesz spróbować_ [dodać wyjątek](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) dla adresów IP usługi wyszukiwania. Jednak ten scenariusz nie jest obsługiwany i nie jest gwarantowany jego działania.

Adres IP usługi wyszukiwania można sprawdzić, uruchamiając polecenie ping według nazwy FQDN (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>Indeksowanie nie jest włączone

Usługa Azure Wyszukiwanie poznawcze ma niejawną zależność od indeksowania Cosmos DB. Jeśli wyłączysz automatyczne indeksowanie w Cosmos DB, usługa Azure Wyszukiwanie poznawcze zwróci stan pomyślne, ale nie będzie można indeksować zawartości kontenera. Aby uzyskać instrukcje dotyczące sprawdzania ustawień i włączania indeksowania, zobacz [Zarządzanie indeksowanie w Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Błędy przetwarzania dokumentu

### <a name="unprocessable-or-unsupported-documents"></a>Nieprzetworzone lub nieobsługiwane dokumenty

Indeksator obiektów BLOB umożliwia dokumentowanie [formatów dokumentów, które są jawnie obsługiwane.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) Czasami kontener magazynu obiektów BLOB zawiera nieobsługiwane dokumenty. Inne przypadki mogą wystąpić problemy z dokumentami. Możesz uniknąć zatrzymywania indeksatora w tych dokumentach, [zmieniając opcje konfiguracji](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

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
* Dokument został zaktualizowany po uruchomieniu indeksatora. Jeśli indeksator jest zgodnie z [harmonogramem](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), zostanie on ostatecznie uruchomiony i zostanie pobrany do dokumentu.
* [Zapytanie](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) określone w źródle danych wyklucza dokument. Indeksatory nie mogą indeksować dokumentów, które nie są częścią źródła danych.
* [Mapowania pól](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) lub [wzbogacania AI](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) zmieniły dokument i wyglądają inaczej niż oczekiwano.
* Użyj [interfejsu API dokumentu wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/lookup-document) , aby znaleźć dokument.
