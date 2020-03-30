---
title: Rozwiązywanie typowych problemów z indeksatorem wyszukiwania
titleSuffix: Azure Cognitive Search
description: Napraw błędy i typowe problemy z indeksatorami w usłudze Azure Cognitive Search, w tym połączenie ze źródłem danych, zapora i brakujące dokumenty.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190928"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Rozwiązywanie typowych problemów z indeksatorem w usłudze Azure Cognitive Search

Indeksatory można napotkać na wiele problemów podczas indeksowania danych do usługi Azure Cognitive Search. Główne kategorie niepowodzeń obejmują:

* [Łączenie się ze źródłem danych lub innymi zasobami](#connection-errors)
* [Przetwarzanie dokumentów](#document-processing-errors)
* [Pośmianie dokumentu do indeksu](#index-errors)

## <a name="connection-errors"></a>Błędy połączenia

> [!NOTE]
> Indeksatory mają ograniczoną obsługę uzyskiwania dostępu do źródeł danych i innych zasobów, które są zabezpieczone przez mechanizmy zabezpieczeń sieci platformy Azure. Obecnie indeksatory mogą uzyskiwać dostęp tylko do źródeł danych za pośrednictwem odpowiednich mechanizmów ograniczeń zakresu adresów IP lub reguł sieciowej sieciowej sieciowej w stosownych przypadkach. Szczegółowe informacje dotyczące uzyskiwania dostępu do każdego obsługiwanego źródła danych można znaleźć poniżej.
>
> Możesz znaleźć adres IP swojej usługi wyszukiwania, pingując jego w pełni `<your-search-service-name>.search.windows.net`kwalifikowaną nazwę domeny (np., ).
>
> Zakres adresów IP `AzureCognitiveSearch` [tagu usługi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) można znaleźć za pomocą [plików JSON do pobrania](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) lub interfejsu API [odnajdowania tagów usługi.](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview) Zakres adresów IP jest aktualizowany co tydzień.

### <a name="configure-firewall-rules"></a>Konfigurowanie reguł zapory

Usługi Azure Storage, CosmosDB i azure SQL zapewniają konfigurowalną zaporę. Nie ma określonego komunikatu o błędzie, gdy zapora jest włączona. Zazwyczaj błędy zapory są ogólne `The remote server returned an error: (403) Forbidden` i `Credentials provided in the connection string are invalid or have expired`wyglądają jak lub .

Istnieją 2 opcje umożliwiające indeksatorom dostęp do tych zasobów w takim wystąpieniu:

* Wyłącz zaporę, zezwalając na dostęp ze **wszystkich sieci** (jeśli jest to możliwe).
* Alternatywnie można zezwolić na dostęp do adresu IP usługi wyszukiwania `AzureCognitiveSearch` i [tagu](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) zakresu adresów IP usługi w regułach zapory zasobu (ograniczenie zakresu adresów IP).

Szczegóły dotyczące konfigurowania ograniczeń zakresu adresów IP dla każdego typu źródła danych można znaleźć w następujących łączach:

* [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Ograniczenia:** Jak określono w powyższej dokumentacji dla usługi Azure Storage, ograniczenia zakresu adresów IP będą działać tylko wtedy, gdy usługa wyszukiwania i konto magazynu znajdują się w różnych regionach.

Funkcje platformy Azure (które mogą być używane jako [niestandardowa umiejętność interfejsu api sieci Web)](cognitive-search-custom-skill-web-api.md)obsługują również [ograniczenia adresów IP.](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions) Lista adresów IP do skonfigurowania będzie adres IP usługi wyszukiwania i `AzureCognitiveSearch` zakres adresów IP tagu usługi.

Szczegółowe informacje dotyczące uzyskiwania dostępu do danych na serwerze SQL na maszynie Wirtualnej platformy Azure są opisane [tutaj](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Konfigurowanie reguł sieciowej grupy zabezpieczeń

Podczas uzyskiwania dostępu do danych w wystąpieniu zarządzanym SQL lub gdy maszyna wirtualna platformy Azure jest używana jako identyfikator URI usługi sieci web dla [umiejętności niestandardowego interfejsu api sieci Web,](cognitive-search-custom-skill-web-api.md)klienci nie muszą zajmować się określonymi adresami IP.

W takich przypadkach maszyny Wirtualnej platformy Azure lub wystąpienia zarządzanego SQL można skonfigurować do przebywania w sieci wirtualnej. Następnie można skonfigurować grupę zabezpieczeń sieci do filtrowania typu ruchu sieciowego, który może przepływać do i z podsieci sieci wirtualnej i interfejsów sieciowych.

Tag `AzureCognitiveSearch` usługi może być bezpośrednio używany w regułach przychodzącej [sieciowej sieciowej sieciowej bez](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) konieczności wyszukiwania jego zakresu adresów IP.

Więcej szczegółów dotyczących uzyskiwania dostępu do danych w wystąpieniu zarządzanym SQL przedstawiono [tutaj](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>Usługa CosmosDB "Indeksowanie" nie jest włączona

Usługa Azure Cognitive Search ma niejawną zależność od indeksowania usługi Cosmos DB. Jeśli wyłączysz automatyczne indeksowanie w usłudze Cosmos DB, usługa Azure Cognitive Search zwraca pomyślny stan, ale nie może indeksować zawartości kontenera. Aby uzyskać instrukcje dotyczące sprawdzania ustawień i włączania indeksowania, zobacz [Zarządzanie indeksowaniami w usłudze Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Błędy przetwarzania dokumentów

### <a name="unprocessable-or-unsupported-documents"></a>Nieprzetworzalne lub nieobsługiwanych dokumentów

Indeksator obiektów [blob, których formaty dokumentów są jawnie obsługiwane.](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Czasami kontener magazynu obiektów blob zawiera nieobsługiwały dokumenty. Innym razem mogą wystąpić problematyczne dokumenty. Można uniknąć zatrzymywania indeksatora na tych dokumentach, [zmieniając opcje konfiguracji:](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)

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

Indeksator obiektów blob [wyszukuje i wyodrębnia tekst z obiektów blob w kontenerze](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Niektóre problemy z wyodrębnianiem tekstu obejmują:

* Dokument zawiera tylko zeskanowane obrazy. Obiekty BLOB PDF, które mają zawartość nietekstową, taką jak skanowane obrazy (JPG), nie dają wyników w standardowym potoku indeksowania obiektów blob. Jeśli masz zawartość obrazu z elementami tekstowymi, możesz użyć [wyszukiwania poznawczego,](cognitive-search-concept-image-scenarios.md) aby znaleźć i wyodrębnić tekst.
* Indeksator obiektów blob jest skonfigurowany tylko do indeksu metadanych. Aby wyodrębnić zawartość, indeksator obiektów blob musi być skonfigurowany do [wyodrębniania zarówno zawartości, jak i metadanych:](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Błędy indeksu

### <a name="missing-documents"></a>Brakujące dokumenty

Indeksatory znajdują dokumenty ze [źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Czasami dokument ze źródła danych, który powinien być indeksowany wydaje się brakować w indeksie. Istnieje kilka typowych przyczyn, dla których mogą wystąpić następujące błędy:

* Dokument nie został zindeksowany. Sprawdź portal pod kątem pomyślnego uruchomienia indeksatora.
* Dokument został zaktualizowany po uruchomieniu indeksatora. Jeśli indeksator jest zgodnie z [harmonogramem,](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)zostanie on ostatecznie ponownie uruchomić i odebrać dokument.
* [Kwerenda](/rest/api/searchservice/create-data-source) określona w źródle danych wyklucza dokument. Indeksatory nie można indeksować dokumentów, które nie są częścią źródła danych.
* [Mapowania pól](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) lub [wzbogacenie SI](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) zmieniły dokument i wygląda inaczej niż się spodziewasz.
* Użyj [interfejsu API dokumentu odnośny,](https://docs.microsoft.com/rest/api/searchservice/lookup-document) aby znaleźć dokument.
