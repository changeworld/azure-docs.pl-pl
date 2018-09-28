---
title: Globalna dystrybucja danych za pomocą usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Więcej informacji na temat skalowana w skali replikacji geograficznej, Multi-Master, trybu failover oraz odzyskiwanie danych przy użyciu globalne bazy danych z usługi Azure Cosmos DB, to usługa globalnie dystrybuowanej, wielomodelowej bazy danych.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 227c243d82665dc533e3bfa6a1fe3e9bb775a262
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408899"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Dane globalne dystrybucji za pomocą usługi Azure Cosmos DB

Platforma Azure jest powszechnie stosowana — jej zasięgu globalnym w regionach geograficznych ponad 50 i stale powiększający się. Obecność i obsługę wielu wzorców jednym z zróżnicowane możliwości, których platforma Azure oferuje deweloperom jest możliwość tworzenie, wdrażanie i łatwe zarządzanie globalnie rozproszonych aplikacji.

[Azure Cosmos DB](../cosmos-db/introduction.md) to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft do aplikacji o krytycznym znaczeniu. Usługa Azure Cosmos DB udostępnia gotowa do użycia funkcja dystrybucji globalnej, [elastyczne skalowanie przepływności i przestrzeni dyskowej](../cosmos-db/partition-data.md) na całym świecie, oznaczona jedną cyfrą odczytu i zapisu milisekundowe opóźnienie w 99. percentylu, [odpowiednio zdefiniowane ustawienia spójności modele](consistency-levels.md)oraz gwarantowaną wysoką dostępność, wspierane przez [wiodące w branży kompleksowe umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Usługa Azure Cosmos DB [automatycznie indeksuje wszystkie dane](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) bez konieczności zarządzania schematami lub indeksami.

## <a name="global-distribution-with-multi-master"></a>Globalnej dystrybucji przy użyciu Multi-Master

Jako usługa w chmurze usługi Azure Cosmos DB starannie zaprojektowany pod kątem obsługę wielu dzierżawców, globalną dystrybucję i Multi-Master dokumentów, pary klucz wartość, wykres i modeli danych rodzin kolumn.

![Kontener usługi Azure Cosmos DB na partycje i rozproszonych w trzech regionach](./media/distribute-data-globally/global-apps.png)

**Jeden kontener usługi Azure Cosmos DB na partycje i rozproszone w wielu regionach platformy Azure**

Jak nauczyliśmy się podczas tworzenia usługi Azure Cosmos DB, dodawanie globalnej dystrybucji nie może być ekskluzywnym. Nie może być "skręcania jednokrotnego" na jego podstawie system bazy danych "w jednej lokacji". Span możliwości oferowane przez globalnie rozproszonej bazy danych poza funkcje odzyskiwania po awarii na geograficzne tradycyjnych (Geo-DR), oferowane przez bazy danych "w jednej lokacji". Pojedyncza lokacja bazy danych oferuje możliwość Geo-DR to podzbiór globalnie rozproszonych baz danych.

Przy użyciu gotowej do użycia dystrybucję globalną usługi Azure Cosmos DB deweloperzy nie trzeba tworzyć własne szkieletu replikacji przez zastosowanie wzorca Lambda za pośrednictwem dziennika bazy danych lub wykonywania operacji "double zapisu" w wielu regionach. Robimy *nie* zaleca się takiego podejścia, ponieważ nie jest możliwe sprawdzić ich poprawność takiego podejścia i podaj dźwięku umowy SLA.

## <a id="Next Steps"></a>Następne kroki

* [Jak usługa Azure Cosmos DB umożliwia kompleksowa dystrybucja globalna](distribute-data-globally-turnkey.md)

* [Usługa Azure Cosmos DB globalną dystrybucję kluczowe korzyści](distribute-data-globally-benefits.md)

* [Jak skonfigurować usługę Azure Cosmos DB globalna Replikacja bazy danych](tutorial-global-distribution-sql-api.md)

* [Jak włączyć Multi-Master dla kont usługi Azure Cosmos DB](enable-multi-master.md)

* [Sposób działania automatycznego i ręcznego trybu failover w usłudze Azure Cosmos DB](regional-failover.md)

* [Opis rozwiązywania konfliktów w usłudze Azure Cosmos DB](multi-master-conflict-resolution.md)

* [Używanie Multi-Master z bazy danych NoSQL typu open source](multi-master-oss-nosql.md)
