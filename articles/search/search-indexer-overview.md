---
title: Indeksatory przeszukiwania źródeł danych podczas indeksowania — Azure wyszukiwania
description: Bazę danych Azure SQL Database i usługi Azure Cosmos DB oraz Azure Storage można przeszukiwać w celu wyodrębniania danych z możliwością wyszukiwania i wypełnienia indeksu usługi Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 87e35573eea836fc8a88c7515409c070ec63aa3b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024898"
---
# <a name="indexers-in-azure-search"></a>Indeksatory w usłudze Azure Search

*Indeksatora* w usłudze Azure Search jest przeszukiwarką, która wyodrębnia dane z możliwością wyszukiwania i metadane ze źródła zewnętrznego danych platformy Azure i wypełnienie indeksu na podstawie mapowania pól do pól między indeksem a źródłem danych. Ta metoda jest czasami określane jako "model ściągania", ponieważ usługa pobiera dane bez konieczności pisania kodu, który dodaje dane do indeksu.

Indeksatory są oparte na typach źródeł danych lub platformach — obejmują indeksatory programu SQL Server na platformie Azure, Cosmos DB, Azure Table Storage i Blob Storage. Indeksatory magazynu obiektów blob mają dodatkowe właściwości specyficzne dla typów zawartości obiektu blob.

Dane można wprowadzać tylko za pomocą indeksatora lub kombinacji metod obejmujących użycie indeksatora w celu załadowania tylko niektórych pól w indeksie.

Indeksatory można uruchamiać na żądanie lub według cyklicznego harmonogramu odświeżania danych uruchamianego co 15 minut. Częstsze aktualizacje wymagają modelu wypychania, który pozwala jednocześnie aktualizować dane w usłudze Azure Search i zewnętrznym źródle danych.

## <a name="approaches-for-creating-and-managing-indexers"></a>Podejścia do tworzenia indeksatorów i zarządzania nimi

Możesz tworzyć indeksatory i zarządzać nimi przy użyciu tych metod:

* [Portal > Kreator importowania danych](search-import-data-portal.md)
* [Interfejs API REST usługi](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Początkowo nowy indeksator jest ogłaszany jako funkcja w wersji zapoznawczej. Funkcje w wersji zapoznawczej są wprowadzane w interfejsach API (REST i .NET), a następnie integrowane z portalem po przejściu do poziomu ogólnej dostępności. Jeśli oceniasz nowy indeksator, zaplanuj napisanie kodu.

## <a name="permissions"></a>Uprawnienia

Wszystkie operacje związane z indeksatorów, w tym żądaniach GET dotyczących stanu lub definicji, wymagają [klucz api-key administratora](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

Indeksatory można przeszukiwać magazynów danych na platformie Azure.

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md) 

> [!Note]
> Usługa Azure Table Storage nie jest obsługiwana dla [wyszukiwania kognitywnego](cognitive-search-concept-intro.md).
>

## <a name="basic-configuration-steps"></a>Podstawowe kroki konfiguracji
Indeksatory oferują funkcje, które są unikatowe dla źródła danych. W związku z tym niektóre aspekty konfiguracji indeksatora lub źródła danych różnią się w zależności od typu indeksatora. Wszystkie indeksatory korzystają jednak z takich samych kompozycji i wymagań. Kroki, które są wspólne dla wszystkich indeksatorów, znajdują się poniżej.

### <a name="step-1-create-a-data-source"></a>Krok 1: Tworzenie źródła danych
Indeksator uzyskuje połączenie ze źródłem danych z *źródła danych* obiektu. Definicję źródła danych zawiera parametry połączenia i ewentualnie poświadczeń. Wywołaj [Utwórz źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) interfejsu API REST lub [klasy źródła danych](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) chcesz utworzyć zasób.

Źródła danych są konfigurowane i zarządzane niezależnie od indeksatorów, które z nich korzystają. Oznacza to, że jedno źródło może być używane przez wiele indeksatorów w celu jednoczesnego ładowania kilku indeksów.

### <a name="step-2-create-an-index"></a>Krok 2: Tworzenie indeksu
Indeksator automatyzuje niektóre zadania związane z pozyskiwaniem danych, ale tworzenie indeksu na ogół nie należy do tych zadań. Jako warunek wstępny należy posiadać wstępnie zdefiniowany indeks z polami, które odpowiadają polom w zewnętrznym źródle danych. Pola muszą odpowiadać przez nazwę i typ danych. Aby uzyskać więcej informacji dotyczących tworzenia struktury indeksu, zobacz [Tworzenie indeksu (interfejs API REST usługi Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) lub [Index, klasa](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Aby uzyskać pomoc dotyczącą skojarzeń pól, zobacz [Mapowania pól w indeksatorach usługi Azure Search](search-indexer-field-mappings.md).

> [!Tip]
> Chociaż indeksatory nie generują automatycznie indeksu, kreator **Importowanie danych** w portalu może pomóc w wykonaniu tego zadania. W większości przypadków kreator może rozpoznać schemat indeksu na podstawie istniejących metadanych w źródle i przedstawić wstępny schemat indeksu, który można edytować przy użyciu funkcji wbudowanych w tym kreatorze. Po utworzeniu indeksu w usłudze możliwość dalszej edycji w portalu jest w większości ograniczona do dodawania nowych pól. Należy rozważyć użycie kreatora do tworzenia indeksu, ale nie do jego poprawiania. Aby nauczyć się wykonywania tych zadań w praktyce, skorzystaj z [przewodnika po portalu](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Krok 3: Tworzenie indeksatora i harmonogramu
Definicja indeksatora to konstrukcja, który łączy w sobie wszystkie elementy związane z pozyskiwaniem danych. Wymagane elementy obejmują źródło danych i indeksu. Opcjonalne elementy obejmują mapowania harmonogram i pola. Mapowanie pola są tylko opcjonalne, jeśli wyraźnie odpowiadają pola źródłowego i pola indeksu. Indeksator może odwoływać się do źródła danych z innej usługi pod warunkiem, że źródło danych jest z tej samej subskrypcji. Aby uzyskać więcej informacji dotyczących tworzenia struktury indeksatora, zobacz [Create Indexer (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) (Tworzenie indeksatora — interfejs API REST usługi Azure Search).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Uruchamianie indeksatory na żądanie

Choć często zaplanować indeksowania, indeksator może być wywołana na żądanie przy użyciu [Uruchom polecenie](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Uruchom interfejs API zwraca pomyślnie, wywołanie indeksatora zostało zaplanowane, ale rzeczywisty przetwarzanie odbywa się asynchronicznie. 

Możesz monitorować stan indeksatora w portalu lub za pośrednictwem uzyskać stan interfejsu API indeksatora. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Pobierz stan indeksatora

Można pobrać historii stanu i wykonywanie indeksatora za pośrednictwem [polecenie pobierania stanu indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Odpowiedź zawiera ogólny stan indeksatora, wywołanie indeksatora ostatniego (lub w toku) i historię ostatnich wywołania indeksatora.

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

Historia wykonywania zawiera maksymalnie 50 ostatnich wykonań ukończone, które są sortowane w kolejności chronologicznej odwrotnej (tak, aby najnowsze wykonywania wykorzystasz w odpowiedzi).

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy masz już podstawowe informacje, następnym krokiem jest przegląd wymagań i zadań specyficznych dla poszczególnych typów źródeł danych.

* [Usługa Azure SQL Database lub program SQL Server na maszynie wirtualnej platformy Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Indeksowanie obiektów blob plików CSV za pomocą indeksatora obiektów blob usługi Azure Search](search-howto-index-csv-blobs.md)
* [Indeksowanie obiektów blob plików JSON za pomocą indeksatora obiektów blob usługi Azure Search](search-howto-index-json-blobs.md)
