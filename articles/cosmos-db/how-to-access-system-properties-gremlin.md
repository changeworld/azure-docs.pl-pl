---
title: Uzyskiwanie dostępu do właściwości dokumentu systemowego za pomocą grafu Azure Cosmos DB
description: Dowiedz się, jak odczytywać i zapisywać Cosmos DB właściwości dokumentu systemu za pośrednictwem interfejsu API Gremlin
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 4ed7e67ae0ef027b260d0e0f0407e4e05ed5a8f4
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898302"
---
# <a name="system-document-properties"></a>Właściwości dokumentu systemowego

Azure Cosmos DB ma [Właściwości systemu](https://docs.microsoft.com/rest/api/cosmos-db/databases) , takie jak ```_ts```, ```_self```, ```_attachments```, ```_rid```i ```_etag``` na każdym dokumencie. Ponadto aparat Gremlin dodaje właściwości ```inVPartition``` i ```outVPartition``` dla krawędzi. Domyślnie te właściwości są dostępne do przechodzenia. Można jednak uwzględnić określone właściwości lub wszystkie z nich podczas przechodzenia Gremlin.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>Tag E-

Ta właściwość jest używana do kontrolowania optymistycznej współbieżności. Jeśli aplikacja wymaga przerwania operacji do kilku oddzielnych przechodzenia, może użyć właściwości eTag, aby uniknąć utraty danych podczas jednoczesnego zapisu.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Czas wygaśnięcia (TTL)

Jeśli dla kolekcji włączono funkcję wygasania dokumentów, a dokumenty mają ustawioną ```ttl``` właściwość, wówczas ta właściwość będzie dostępna w przechodzeniu Gremlin jako zwykła Właściwość wierzchołka lub krawędzi. ```ProjectionStrategy``` nie jest konieczne, aby włączyć ekspozycję właściwości czasu wygaśnięcia.

Wierzchołek utworzony za pomocą poniższej operacji przechodzenia zostanie automatycznie usunięty za **123 sekundy**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Następne kroki
* [Optymistyczna współbieżność usługi Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Czas wygaśnięcia (TTL)](time-to-live.md) w Azure Cosmos DB
