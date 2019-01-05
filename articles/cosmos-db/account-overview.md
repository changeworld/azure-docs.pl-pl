---
title: Praca z kontami usługi Azure Cosmos DB
description: W tym artykule opisano sposób tworzenia i używania kont usługi Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 7c4c1a5991445448f015dc0912383baf53f4e38c
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034934"
---
# <a name="working-with-azure-cosmos-account"></a>Praca z kontem usługi Azure Cosmos

Usługa Azure Cosmos DB to w pełni zarządzana platforma as-a-service (PaaS). Aby rozpocząć korzystanie z usługi Azure Cosmos DB, początkowo należy utworzyć konto usługi Azure Cosmos w subskrypcji platformy Azure. Twoje konto usługi Azure Cosmos zawiera unikatową nazwę DNS, a konto można zarządzać za pomocą witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub przy użyciu różnych zestawów SDK specyficzne dla języka. Aby uzyskać więcej informacji, zobacz [jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md).

Konto usługi Azure Cosmos jest podstawową jednostką globalnej dystrybucji i wysokiej dostępności. Globalnie dystrybucji danych i przepływności w wielu regionach platformy Azure, możesz Dodawanie i usuwanie regionów platformy Azure do swojego konta usługi Azure Cosmos, w dowolnym momencie. Można skonfigurować swoje konto usługi Azure Cosmos, aby mieć jedną lub wiele regionów zapisu. Aby uzyskać więcej informacji, zobacz [jak dodawanie i usuwanie regionów platformy Azure do swojego konta usługi Azure Cosmos](how-to-manage-database-account.md). Można skonfigurować [domyślna spójność](consistency-levels.md) poziomu konta usługi Azure Cosmos. Usługa Azure Cosmos DB zapewnia kompleksowe umowy SLA, obejmujący przepustowość, opóźnienie w 99. percentylu, spójności i wysokiej dostępności. Aby uzyskać więcej informacji, zobacz [usługi Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Bezpieczne zarządzanie dostępem do wszystkich danych w ramach konta usługi Azure Cosmos, można użyć kluczy głównych skojarzonych z Twoim kontem. Aby dodatkowo zabezpieczyć dostęp do danych można skonfigurować punkt końcowy usługi sieci Wirtualnej i zapory adresów IP na Twoim koncie usługi Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elementy na koncie usługi Azure Cosmos

Kontener usługi Azure Cosmos DB jest podstawową jednostką skalowalności. Nieograniczona liczba aprowizowaną przepływność (RU/s) i magazynu może mieć praktycznie w kontenerze. Usługa Azure Cosmos DB w sposób niewidoczny dla użytkownika partycje kontenera przy użyciu klucza partycji logicznej, można określić, aby elastycznie Skaluj aprowizowanej przepływności i magazynu. Aby uzyskać więcej informacji, zobacz [Praca z kontenerów w usłudze Azure Cosmos i elementy](databases-containers-items.md).

Obecnie można utworzyć maksymalnie 100 kont usługi Azure Cosmos w ramach subskrypcji platformy Azure. Na jednym koncie usługi Azure Cosmos może zarządzać praktycznie nieograniczonej ilości danych i aprowizowanej przepływności. Do zarządzania danymi i aprowizowanej przepływności, można utworzyć jeden lub więcej baz danych Azure Cosmos w ramach konta usługi i w ramach tej bazy danych, można utworzyć co najmniej jeden kontener. Na poniższej ilustracji przedstawiono hierarchię elementów na koncie usługi Azure Cosmos:

![Hierarchia konta usługi Azure Cosmos](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz można przystąpić do Dowiedz się, jak zarządzać kontem usługi Azure Cosmos lub zobacz inne pojęcia związane z usługą Azure Cosmos DB:

* [Instrukcje Zarządzaj swoim kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Dystrybucja globalna](distribute-data-globally.md)
* [Poziomy spójności](consistency-levels.md)
* [Praca z kontenerów w usłudze Azure Cosmos i elementów](databases-containers-items.md)
* [Punkt końcowy usługi sieci Wirtualnej dla konta usługi Azure Cosmos](vnet-service-endpoint.md)
* [Zapory adresów IP dla konta usługi Azure Cosmos](firewall-support.md)
* [Instrukcje dodawania i usuwania regiony platformy Azure do swojego konta usługi Azure Cosmos](how-to-manage-database-account.md)
* [Umowy SLA usługi Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
