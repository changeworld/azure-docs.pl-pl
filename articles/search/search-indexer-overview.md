---
title: Indeksatory do indeksowania danych podczas importu
titleSuffix: Azure Cognitive Search
description: Indeksowanie bazy danych SQL platformy Azure, usługi Azure Cosmos DB lub magazynu platformy Azure w celu wyodrębnienia danych zbywalnych i wypełnienia indeksu usługi Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282993"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indeksatory w usłudze Azure Cognitive Search

*Indeksator* w usłudze Azure Cognitive Search to robot, który wyodrębnia dane i metadane z przeszukiwania danych z zewnętrznego źródła danych platformy Azure i wypełnia indeks oparty na mapowaniach pola do pola między indeksem a źródłem danych. Takie podejście jest czasami określane jako "model ściągania", ponieważ usługa pobiera dane bez konieczności pisania kodu, który dodaje dane do indeksu.

Indeksatory są oparte na typach lub platformach źródeł danych, z poszczególnymi indeksatorami dla programu SQL Server na platformie Azure, usłudze Cosmos DB, usłudze Azure Table Storage i Blob Storage. Indeksatory magazynu obiektów blob mają dodatkowe właściwości specyficzne dla typów zawartości obiektów blob.

Dane można wprowadzać tylko za pomocą indeksatora lub kombinacji metod obejmujących użycie indeksatora w celu załadowania tylko niektórych pól w indeksie.

Indeksatory można uruchamiać na żądanie lub w harmonogramie odświeżania danych cyklicznych, który działa tak często, jak co pięć minut. Częstsze aktualizacje wymagają modelu wypychanego, który jednocześnie aktualizuje dane zarówno w usłudze Azure Cognitive Search, jak i zewnętrznym źródle danych.

## <a name="approaches-for-creating-and-managing-indexers"></a>Podejścia do tworzenia indeksatorów i zarządzania nimi

Możesz tworzyć indeksatory i zarządzać nimi przy użyciu tych metod:

* [Kreator danych importu > portalu](search-import-data-portal.md)
* [Interfejs API REST usługi](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Początkowo nowy indeksator jest ogłaszany jako funkcja w wersji zapoznawczej. Funkcje w wersji zapoznawczej są wprowadzane w interfejsach API (REST i .NET), a następnie integrowane z portalem po przejściu do poziomu ogólnej dostępności. Jeśli oceniasz nowy indeksator, zaplanuj napisanie kodu.

## <a name="permissions"></a>Uprawnienia

Wszystkie operacje związane z indeksatorami, w tym żądania GET dotyczące stanu lub definicje, wymagają [klucza interfejsu api administratora.](search-security-api-keys.md) 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

Indeksatory indeksować magazyny danych na platformie Azure.

* [Magazyn obiektów blob platformy Azure](search-howto-indexing-azure-blob-storage.md)
* [Usługa Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (w wersji zapoznawczej)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Program SQL Server na maszynach wirtualnych platformy Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Wystąpienia zarządzane sql na platformie Azure](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Podstawowe kroki konfiguracji
Indeksatory oferują funkcje, które są unikatowe dla źródła danych. W związku z tym niektóre aspekty konfiguracji indeksatora lub źródła danych różnią się w zależności od typu indeksatora. Wszystkie indeksatory korzystają jednak z takich samych kompozycji i wymagań. Kroki, które są wspólne dla wszystkich indeksatorów, znajdują się poniżej.

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych
Indeksator uzyskuje połączenie źródła danych z obiektu *źródła danych.* Definicja źródła danych zawiera parametry połączenia i ewentualnie poświadczenia. Wywołanie interfejsu API REST lub DataSource create the resource wywołaj go w [usłudze Create Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API lub [DataSource.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource)

Źródła danych są konfigurowane i zarządzane niezależnie od indeksatorów, które z nich korzystają. Oznacza to, że jedno źródło może być używane przez wiele indeksatorów w celu jednoczesnego ładowania kilku indeksów.

### <a name="step-2-create-an-index"></a>Krok 2. Tworzenie indeksu
Indeksator automatyzuje niektóre zadania związane z pozyskiwaniem danych, ale tworzenie indeksu na ogół nie należy do tych zadań. Jako warunek wstępny należy posiadać wstępnie zdefiniowany indeks z polami, które odpowiadają polom w zewnętrznym źródle danych. Pola muszą być zgodne według nazwy i typu danych. Aby uzyskać więcej informacji na temat struktury indeksu, zobacz [Tworzenie indeksu (interfejsu API REST usługi Azure Cognitive Search)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) lub [klasy Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Aby uzyskać pomoc dotyczącą skojarzeń pól, zobacz [Mapowania pól w indeksatorach usługi Azure Cognitive Search.](search-indexer-field-mappings.md)

> [!Tip]
> Chociaż indeksatory nie generują automatycznie indeksu, kreator **Importowanie danych** w portalu może pomóc w wykonaniu tego zadania. W większości przypadków kreator może rozpoznać schemat indeksu na podstawie istniejących metadanych w źródle i przedstawić wstępny schemat indeksu, który można edytować przy użyciu funkcji wbudowanych w tym kreatorze. Po utworzeniu indeksu w usłudze możliwość dalszej edycji w portalu jest w większości ograniczona do dodawania nowych pól. Należy rozważyć użycie kreatora do tworzenia indeksu, ale nie do jego poprawiania. Aby nauczyć się wykonywania tych zadań w praktyce, skorzystaj z [przewodnika po portalu](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Krok 3. Tworzenie indeksatora i ustawianie jego harmonogramu
Definicja indeksatora jest konstrukcją, która łączy wszystkie elementy związane z pozyskiwania danych. Wymagane elementy obejmują źródło danych i indeks. Elementy opcjonalne obejmują harmonogram i mapowania pól. Mapowanie pól jest opcjonalne tylko wtedy, gdy pola źródłowe i pola indeksu wyraźnie odpowiadają. Aby uzyskać więcej informacji na temat strukturyzowania indeksatora, zobacz [Tworzenie indeksatora (interfejsu API REST usługi Azure Cognitive Search).](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Uruchamianie indeksatorów na żądanie

Chociaż jest to wspólne zaplanować indeksowanie, indeksator może być również wywoływana na żądanie za pomocą [polecenia Uruchom:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Po pomyślnym pomyślnym powrocie uruchomienia interfejsu API wywołanie indeksatora zostało zaplanowane, ale rzeczywiste przetwarzanie odbywa się asynchronicznie. 

Stan indeksatora można monitorować w portalu lub za pomocą interfejsu API stanu indeksatora Get. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Uzyskaj stan indeksatora

Historię stanu i wykonywania indeksatora można pobrać za pomocą [polecenia Pobierz stan indeksatora:](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Odpowiedź zawiera ogólny stan indeksatora, ostatnie wywołanie indeksatora (lub w toku) oraz historię ostatnich wywołań indeksatora.

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

Historia wykonywania zawiera do 50 ostatnich zakończonych wykonań, które są sortowane w odwrotnej kolejności chronologicznej (więc najnowsze wykonanie jest pierwsze w odpowiedzi).

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz już podstawowe informacje, następnym krokiem jest przegląd wymagań i zadań specyficznych dla poszczególnych typów źródeł danych.

* [Usługa Azure SQL Database lub program SQL Server na maszynie wirtualnej platformy Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Magazyn obiektów blob platformy Azure](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Indeksowanie obiektów BLOB przy użyciu indeksatora obiektów blob usługi Azure Cognitive Search](search-howto-index-csv-blobs.md)
* [Indeksowanie obiektów blob JSON za pomocą indeksatora obiektów blob usługi Azure Cognitive Search](search-howto-index-json-blobs.md)
