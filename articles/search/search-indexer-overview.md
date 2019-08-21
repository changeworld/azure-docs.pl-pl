---
title: Indeksatory do przeszukiwania źródeł danych podczas indeksowania — Azure Search
description: Bazę danych Azure SQL Database i usługi Azure Cosmos DB oraz Azure Storage można przeszukiwać w celu wyodrębniania danych z możliwością wyszukiwania i wypełnienia indeksu usługi Azure Search.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: af01b6127a8a3e20edfac19ce3b54cecb9d561d1
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640583"
---
# <a name="indexers-in-azure-search"></a>Indeksatory w usłudze Azure Search

*Indeksator* w Azure Search to przeszukiwarka, która wyodrębnia dane z możliwością wyszukiwania i metadane z zewnętrznego źródła danych platformy Azure i wypełnia indeks na podstawie mapowań pola do pola między indeksem a źródłem danych. Takie podejście jest czasami nazywane "modelem ściągania", ponieważ usługa ściąga dane w programie bez konieczności pisania kodu, który dodaje dane do indeksu.

Indeksatory są oparte na typach źródeł danych lub na platformach z poszczególnymi indeksatorami dla SQL Server na platformie Azure, Cosmos DB, Table Storage platformy Azure i Blob Storage. Indeksatory magazynu obiektów BLOB mają dodatkowe właściwości specyficzne dla typów zawartości obiektów BLOB.

Dane można wprowadzać tylko za pomocą indeksatora lub kombinacji metod obejmujących użycie indeksatora w celu załadowania tylko niektórych pól w indeksie.

Indeksatory można uruchamiać na żądanie lub według cyklicznego harmonogramu odświeżania danych uruchamianego co pięć minut. Częstsze aktualizacje wymagają modelu wypychania, który pozwala jednocześnie aktualizować dane w usłudze Azure Search i zewnętrznym źródle danych.

## <a name="approaches-for-creating-and-managing-indexers"></a>Podejścia do tworzenia indeksatorów i zarządzania nimi

Możesz tworzyć indeksatory i zarządzać nimi przy użyciu tych metod:

* [Kreator importu danych > portalu](search-import-data-portal.md)
* [Interfejs API REST usługi](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Początkowo nowy indeksator jest ogłaszany jako funkcja w wersji zapoznawczej. Funkcje w wersji zapoznawczej są wprowadzane w interfejsach API (REST i .NET), a następnie integrowane z portalem po przejściu do poziomu ogólnej dostępności. Jeśli oceniasz nowy indeksator, zaplanuj napisanie kodu.

## <a name="permissions"></a>Uprawnienia

Wszystkie operacje związane z indeksatorami, w tym żądania pobrania dla stanu lub definicji, wymagają [klucza API-Key administratora](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

Indeksatory przeszukują magazyny danych na platformie Azure.

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md) 

> [!Note]
> Usługa Azure Table Storage nie jest obsługiwana w przypadku [wyszukiwania poznawczego](cognitive-search-concept-intro.md).
>

## <a name="basic-configuration-steps"></a>Podstawowe kroki konfiguracji
Indeksatory oferują funkcje, które są unikatowe dla źródła danych. W związku z tym niektóre aspekty konfiguracji indeksatora lub źródła danych różnią się w zależności od typu indeksatora. Wszystkie indeksatory korzystają jednak z takich samych kompozycji i wymagań. Kroki, które są wspólne dla wszystkich indeksatorów, znajdują się poniżej.

### <a name="step-1-create-a-data-source"></a>Krok 1: Utwórz źródło danych
Indeksator uzyskuje połączenie ze źródłem danych z obiektu *źródła danych* . Definicja źródła danych zawiera parametry połączenia i prawdopodobnie poświadczenia. Wywołaj interfejs API REST [tworzenia źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) lub [klasę DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) , aby utworzyć zasób.

Źródła danych są konfigurowane i zarządzane niezależnie od indeksatorów, które z nich korzystają. Oznacza to, że jedno źródło może być używane przez wiele indeksatorów w celu jednoczesnego ładowania kilku indeksów.

### <a name="step-2-create-an-index"></a>Krok 2: Tworzenie indeksu
Indeksator automatyzuje niektóre zadania związane z pozyskiwaniem danych, ale tworzenie indeksu na ogół nie należy do tych zadań. Jako warunek wstępny należy posiadać wstępnie zdefiniowany indeks z polami, które odpowiadają polom w zewnętrznym źródle danych. Pola muszą być zgodne według nazwy i typu danych. Aby uzyskać więcej informacji o tworzeniu struktury indeksu, zobacz [Tworzenie indeksu (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) lub [klasy index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Aby uzyskać pomoc dotyczącą skojarzeń pól, zobacz [Mapowania pól w indeksatorach usługi Azure Search](search-indexer-field-mappings.md).

> [!Tip]
> Chociaż indeksatory nie generują automatycznie indeksu, kreator **Importowanie danych** w portalu może pomóc w wykonaniu tego zadania. W większości przypadków kreator może rozpoznać schemat indeksu na podstawie istniejących metadanych w źródle i przedstawić wstępny schemat indeksu, który można edytować przy użyciu funkcji wbudowanych w tym kreatorze. Po utworzeniu indeksu w usłudze możliwość dalszej edycji w portalu jest w większości ograniczona do dodawania nowych pól. Należy rozważyć użycie kreatora do tworzenia indeksu, ale nie do jego poprawiania. Aby nauczyć się wykonywania tych zadań w praktyce, skorzystaj z [przewodnika po portalu](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Krok 3: Tworzenie i planowanie indeksatora
Definicja indeksatora to konstrukcja, która łączy wszystkie elementy związane z pozyskiwaniem danych. Wymagane elementy obejmują źródło i indeks danych. Opcjonalne elementy obejmują mapowania harmonogramu i pola. Mapowanie pól jest opcjonalne tylko wtedy, gdy pola źródłowe i pola indeksu są wyraźnie zgodne. Indeksator może odwoływać się do źródła danych z innej usługi pod warunkiem, że źródło danych jest z tej samej subskrypcji. Aby uzyskać więcej informacji dotyczących tworzenia struktury indeksatora, zobacz [Create Indexer (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) (Tworzenie indeksatora — interfejs API REST usługi Azure Search).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Uruchamianie indeksatorów na żądanie

Chociaż często jest planowane planowanie indeksowania, indeksator może być również wywoływany na żądanie przy użyciu [polecenia Run](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Po pomyślnym uruchomieniu interfejsu API wywołanie indeksatora zostało zaplanowane, ale rzeczywiste przetwarzanie odbywa się asynchronicznie. 

Stan indeksatora można monitorować w portalu lub za pomocą interfejsu API pobierania stanu indeksatora. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Pobierz stan indeksatora

Można pobrać stan i historię wykonywania indeksatora za pomocą [polecenia Pobierz indeksator stanu](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Odpowiedź zawiera ogólny stan indeksatora, ostatnie (lub w toku) wywołanie indeksatora i historię najnowszych wywołań indeksatora.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Historia wykonywania zawiera do 50 najnowszych zakończonych wykonań, które są sortowane w odwrotnej kolejności chronologicznej (w związku z czym ostatnie wykonanie jest najpierw w odpowiedzi).

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz już podstawowe informacje, następnym krokiem jest przegląd wymagań i zadań specyficznych dla poszczególnych typów źródeł danych.

* [Usługa Azure SQL Database lub program SQL Server na maszynie wirtualnej platformy Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Indeksowanie obiektów blob plików CSV za pomocą indeksatora obiektów blob usługi Azure Search](search-howto-index-csv-blobs.md)
* [Indeksowanie obiektów blob plików JSON za pomocą indeksatora obiektów blob usługi Azure Search](search-howto-index-json-blobs.md)
