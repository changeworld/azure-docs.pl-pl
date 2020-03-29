---
title: Zalety wielu wzorców usługi Azure Cosmos DB
description: Poznaj zalety wielu wzorców w usłudze Azure Cosmos DB, porównanie opóźnień i wymagań umowy SLA w pojedynczych i wielu lokalizacjach zapisu.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872013"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Poznaj zalety wielu wzorców w usłudze Azure Cosmos DB

Operatorzy kont usługi Cosmos DB powinni wybrać odpowiednią konfigurację dystrybucji globalnej, aby zapewnić opóźnienie, dostępność i wymagania dotyczące obiektów RTO dla ich aplikacji. Konta usługi Azure Cosmos skonfigurowane z wieloma lokalizacjami zapisu oferują znaczące korzyści za pośrednictwem kont z pojedynczą lokalizacją zapisu, w tym 99,999% umowy SLA dostępności zapisu, <10 ms sla opóźnienia zapisu w 99 percentylu i RTO = 0 w regionalnej awarii.

## <a name="comparison-of-features"></a>Porównanie funkcji

|Wymaganie zastosowania|Wiele lokalizacji zapisu|Pojedyncza lokalizacja zapisu|Uwaga|
|---|---|---|---|
|SLA z opóźnieniem zapisu <10ms przy P99|**Tak**|Nie|Konta z pojedynczą lokalizacją zapisu naliczają dodatkowe opóźnienie sieci między regionami dla każdego zapisu.|
|Odczyt umowy SLA z opóźnieniem <10ms w P99|**Tak**|Tak| |
|Zapis SLA z 99.999%|**Tak**|Nie|Umowy SLA z gwarancją pojedynczego zapisu w wysokości 99,99%|
|RTO = 0|**Tak**|Nie|Zero czasu w dół dla zapisów w przypadku klęsk regionalnych. Konta z pojedynczą lokalizacją zapisu mają RTO 15 min.|

## <a name="next-steps"></a>Następne kroki

Jeśli nadal chcesz wyłączyć EnableMultipleWriteLocations na swoim koncie usługi Azure Cosmos, [otwórz bilet pomocy technicznej.](https://azure.microsoft.com/support/create-ticket/)
