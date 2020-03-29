---
title: Właściwości dokumentu systemu dostępu za pośrednictwem programu Azure Cosmos DB Graph
description: Dowiedz się, jak odczytywać i zapisywać właściwości dokumentu systemu Usługi Cosmos DB za pośrednictwem interfejsu API Gremlin
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 4ed7e67ae0ef027b260d0e0f0407e4e05ed5a8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898302"
---
# <a name="system-document-properties"></a>Właściwości dokumentu systemowego

Usługa Azure Cosmos DB ma ```_ts``` ```_self```właściwości ```_attachments``` ```_rid``` [systemu,](https://docs.microsoft.com/rest/api/cosmos-db/databases) takie jak , , i ```_etag``` na każdym dokumencie. Ponadto aparat Gremlin dodaje właściwości ```inVPartition``` i ```outVPartition``` dla krawędzi. Domyślnie te właściwości są dostępne dla przechodzenia. Jednak jest możliwe, aby uwzględnić określone właściwości, lub wszystkie z nich, w Gremlin traversal.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

Ta właściwość jest używana do kontrolowania optymistycznej współbieżności. Jeśli aplikacja musi podzielić operację na kilka oddzielnych traversals, można użyć eTag właściwości, aby uniknąć utraty danych w zapisach równoczesnych.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Czas wygaśnięcia (TTL)

Jeśli kolekcja ma włączone wygasanie ```ttl``` dokumentu i dokumenty mają ustawione właściwości na nich, a następnie ta właściwość będzie dostępna w Gremlin traversal jako regularny wierzchołek lub edge właściwości. ```ProjectionStrategy```nie jest konieczne, aby włączyć czas do żywo ekspozycji nieruchomości.

Wierzchołek utworzony za pomocą poniższej operacji przechodzenia zostanie automatycznie usunięty za **123 sekundy**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Następne kroki
* [Optymistyczna współbieżność usługi Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Czas wygaśnięcia (TTL)](time-to-live.md) w usłudze Azure Cosmos DB
