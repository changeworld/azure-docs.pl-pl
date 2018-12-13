---
title: Ponowne kompilowanie indeksu usługi Azure Search, lub Odśwież zawartość do przeszukiwania — usługa Azure Search
description: Dodawanie nowych elementów, a następnie zaktualizować istniejące elementy lub dokumentów lub usuwanie przestarzałych dokumentów w ponownej pełnej kompilacji lub częściowe Indeksowanie przyrostowe, w celu odświeżenia indeksu usługi Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9c9af69e45af6a70c5327393a1c10385ba2c2aed
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316900"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Jak odbudować indeksu usługi Azure Search

Odbudowywanie indeksu zmiany jego struktury, zmiany ich fizycznej wyrażenie indeksu w usłudze Azure Search. Z drugiej strony odświeżanie indeksu jest aktualizacją tylko zawartość, aby wczytać najnowsze zmiany z powiązanych zewnętrznego źródła danych. Ten artykuł zawiera wskazówki dotyczące jak aktualizowanie indeksów strukturalnie i korzystających.

Uprawnienia odczytu i zapisu na poziomie usługi są wymagane do aktualizacji indeksu. Programowo można wywoływać REST lub interfejsów API platformy .NET dla pełnej rekompilacji lub przyrostowe indeksowanie zawartości, przy użyciu parametrów, określając opcje aktualizacji. 

Ogólnie rzecz biorąc aktualizacje indeksu są na żądanie. Jednak w przypadku indeksów wypełniać przy użyciu specyficznymi dla źródła [indeksatory](search-indexer-overview.md), można użyć wbudowanych harmonogramu. Harmonogram obsługuje odświeżanie dokumentu częstotliwością nawet co 15 minut do dowolnych interwał i wzorzec potrzebujesz. Szybsze częstotliwość odświeżania wymaga wypychania aktualizacji indeksu ręcznie, być może za pomocą podwójnego zapisu w transakcji, jednocześnie Aktualizowanie indeksu usługi Azure Search i zewnętrznym źródle danych.

## <a name="full-rebuilds"></a>Pełne ponowne kompilowanie

Wiele typów aktualizacji, aby uzyskać pełną rekompilację jest wymagana. Pełną rekompilację odnosi się do usuwania indeksu i zarówno dane, jak i metadane, a następnie ponownego uzupełnienia indeksu z zewnętrznych źródeł danych. Programowe [Usuń](https://docs.microsoft.com/rest/api/searchservice/delete-index), [tworzenie](https://docs.microsoft.com/rest/api/searchservice/create-index), i [Załaduj ponownie](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) indeks, aby skompilować go ponownie. 

Po utworzeniu odbudować, należy pamiętać, że jeśli testowali wzorców zapytań i profile oceniania, można oczekiwać, że zmiany w wynikach kwerendy zmiana zawartości źródłowej.

## <a name="when-to-rebuild"></a>Kiedy należy odbudować

Plan w pełni częste, ponownie kompiluje podczas tworzenia active, gdy indeks schematy są w stanie strumienia.

| Modyfikacja | Ponownie skompiluj stanu|
|--------------|---------------|
| Zmień nazwę pola, typ danych, lub jego [atrybutami indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) | Zazwyczaj zmiana definicji pola spowoduje naliczenie opłaty karnej ponownej kompilacji, z wyjątkiem tych [atrybutami indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index): Pobieranie, SearchAnalyzer, SynonymMaps. Atrybuty możliwość pobierania, SearchAnalyzer i SynonymMaps można dodać do istniejącego pola, bez konieczności ponownego kompilowania jego indeksu.|
| Dodaj pole | Ma rygorystyczne wymagania na ponowną kompilację. Istniejących dokumentów indeksowanych podano wartość null dla nowego pola. W przyszłości reindex wartości ze źródła danych zastąpić wartości null, dodawane przez usługę Azure Search. |
| Usuń pole | Nie można bezpośrednio usunąć pole z indeksu usługi Azure Search. Zamiast tego należy zignorować pole "usunięte" w celu uniknięcia korzystania z niego aplikacji. Fizycznie definicję pola i zawartość pozostają w indeksie aż do następnego odbudowanie indeksu przy użyciu schematu, które pomija w danym polu.|

> [!Note]
> Ponownej kompilacji jest również wymagane, jeśli przełącznik warstwy. Jeśli w pewnym momencie podjęciu decyzji o większej pojemności, nie istnieje żadne uaktualnienia w miejscu. Nowa usługa musi zostać utworzona na nowy punkt pojemności, a indeksy muszą zostać skompilowane od podstaw w nowej wersji usługi. 

## <a name="partial-or-incremental-indexing"></a>Indeksowanie częściowego lub przyrostowe

Gdy indeks znajduje się w środowisku produkcyjnym, przyrostowe indeksowanie, zazwyczaj z nie przerwy w świadczeniu usług przyspieszenie otrzymuje fokus. Częściowe lub przyrostowej indeksowanie jest tylko zawartość obciążeniem, dla których synchronizowania zawartości indeksu wyszukiwania, aby odzwierciedlały stan zawartości w źródle danych mającym swój wkład. Dokument dodawane lub usuwane w źródle zostanie dodany lub usunięty z indeksem. W kodzie, wywołania [Add, Update lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) operacji lub równoważnej platformy .NET.

> [!Note]
> Korzystając z indeksatorów, które przeszukują zewnętrznych źródeł danych, mechanizmów śledzenia zmian w systemach źródłowych są używane do indeksowania przyrostowe. Aby uzyskać [usługi Azure Blob storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), `lastModified` pole jest używane. Na [usługi Azure Table storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` pełni tę samą funkcję. Podobnie, zarówno [indeksator usługi Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) i [indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) mają pola Oflagowanie aktualizacji wiersza. Aby uzyskać więcej informacji na temat indeksatorów, zobacz [omówienie indeksatorów](search-indexer-overview.md).


## <a name="see-also"></a>Zobacz także

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Indeks dużych zestawów danych na dużą skalę](search-howto-large-index.md)
+ [Indeksowanie w portalu](search-import-data-portal.md)
+ [Indeksator usługi Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indeksator usługi Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indeksator usługi Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpieczenia w usłudze Azure Search](search-security-overview.md)