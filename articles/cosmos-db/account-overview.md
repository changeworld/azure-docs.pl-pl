---
title: Praca z kontami usługi Azure Cosmos DB
description: W tym artykule opisano sposób tworzenia i używania kont usługi Azure Cosmos. Pokazuje również hierarchię elementów na koncie usługi Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: d29ed68b2945b2473b33aa88176e6f5d832a0fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246905"
---
# <a name="work-with-azure-cosmos-account"></a>Praca z kontem usługi Azure Cosmos

Usługa Azure Cosmos DB to w pełni zarządzana platforma jako usługa (PaaS). Aby rozpocząć korzystanie z usługi Azure Cosmos DB, należy początkowo utworzyć konto usługi Azure Cosmos w ramach subskrypcji platformy Azure. Twoje konto usługi Azure Cosmos zawiera unikatową nazwę DNS i można zarządzać kontem przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub przy użyciu różnych zestawów SDK specyficznych dla języka. Aby uzyskać więcej informacji, zobacz [jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md).

Konto usługi Azure Cosmos jest podstawową jednostką dystrybucji globalnej i wysokiej dostępności. Do globalnej dystrybucji danych i przepływności w wielu regionach platformy Azure można dodać i usunąć regiony platformy Azure do konta usługi Azure Cosmos w dowolnym momencie. Konto usługi Azure Cosmos można skonfigurować tak, aby miało jeden lub wiele regionów zapisu. Aby uzyskać więcej informacji, zobacz [jak dodać i usunąć regiony platformy Azure do konta usługi Azure Cosmos](how-to-manage-database-account.md). Można skonfigurować [domyślny](consistency-levels.md) poziom spójności na koncie usługi Azure Cosmos. Usługa Azure Cosmos DB zapewnia kompleksowe łasce sla obejmujące przepływność, opóźnienie na 99 percentyl, spójność i wysoką dostępność. Aby uzyskać więcej informacji, zobacz [100 000 000 000 000 000 000 000 000 000](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)

Aby bezpiecznie zarządzać dostępem do wszystkich danych w ramach konta usługi Azure Cosmos, można użyć [kluczy głównych skojarzonych](secure-access-to-data.md) z kontem. Aby dodatkowo zabezpieczyć dostęp do danych, można skonfigurować [punkt końcowy usługi sieci wirtualnej](vnet-service-endpoint.md) i [zaporę IP](firewall-support.md) na koncie usługi Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elementy na koncie usługi Azure Cosmos

Kontener usługi Azure Cosmos jest podstawową jednostką skalowalności. Praktycznie można mieć nieograniczoną aprowizowaną przepływność (RU/s) i magazyn w kontenerze. Usługa Azure Cosmos DB w sposób przezroczysty partycjonuje kontener przy użyciu klucza partycji logicznej, który określisz w celu elastycznego skalowania aprowizowanej przepływności i magazynu. Aby uzyskać więcej informacji, zobacz [praca z kontenerami i elementami usługi Azure Cosmos](databases-containers-items.md).

Obecnie można utworzyć maksymalnie 100 kont usługi Azure Cosmos w ramach subskrypcji platformy Azure. Jedno konto usługi Azure Cosmos może praktycznie zarządzać nieograniczoną ilością danych i aprowizowaną przepływnością. Aby zarządzać danymi i aprowizowaną przepływnością, można utworzyć jedną lub więcej baz danych usługi Azure Cosmos w ramach konta i w tej bazie danych, można utworzyć jeden lub więcej kontenerów. Na poniższej ilustracji przedstawiono hierarchię elementów na koncie usługi Azure Cosmos:

![Hierarchia konta usługi Azure Cosmos](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać kontem usługi Azure Cosmos i innymi pojęciami:

* [Jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Dystrybucja globalna](distribute-data-globally.md)
* [Poziomy spójności](consistency-levels.md)
* [Praca z kontenerami i elementami usługi Azure Cosmos](databases-containers-items.md)
* [Punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos](vnet-service-endpoint.md)
* [Zapora IP dla twojego konta usługi Azure Cosmos](firewall-support.md)
* [Instrukcje dodawania i usuwania regionów platformy Azure do konta usługi Azure Cosmos](how-to-manage-database-account.md)
* [Łas 10 100 000 000 000 000 00](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
