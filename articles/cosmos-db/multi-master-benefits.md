---
title: Azure Cosmos DB korzyści z wielu wzorców
description: Poznaj zalety korzystania z wielu wzorców w Azure Cosmos DB, porównując wymagania dotyczące opóźnień i umów SLA w jedną i wiele lokalizacji zapisu.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872013"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Poznaj zalety wielu wzorców w Azure Cosmos DB

Operatory konta Cosmos DB powinny wybrać odpowiednią globalną konfigurację dystrybucji w celu zapewnienia opóźnienia, dostępności i RTO wymagań dla swoich aplikacji. Konta usługi Azure Cosmos skonfigurowane z wieloma lokalizacjami zapisu oferują znaczne korzyści w stosunku do kont z jedną lokalizacją zapisu, w tym < umowy SLA dotyczącej dostępności zapisu w systemie 99,999

## <a name="comparison-of-features"></a>Porównanie funkcji

|Wymaganie aplikacji|Wiele lokalizacji zapisu|Pojedyncza lokalizacja zapisu|Uwaga|
|---|---|---|---|
|Umowa SLA dotycząca opóźnień zapisu < 10 ms w poziomie P99|**Tak**|Nie|Konta z jedną lokalizacją zapisu wiążą się z dodatkowymi opóźnieniami sieci między regionami dla każdego zapisu.|
|Zapoznaj się z umową SLA opóźnienia < 10 ms w poziomie P99|**Tak**|Tak| |
|Zastąp zapis SLA 99,999%|**Tak**|Nie|Konta z jedną lokalizacją zapisu gwarantują umowę SLA na 99,99%|
|RTO = 0|**Tak**|Nie|Zero w dół w przypadku operacji zapisu w przypadku regionalnych awarii. Konta z jedną lokalizacją zapisu mają RTO 15 minut.|

## <a name="next-steps"></a>Następne kroki

Jeśli nadal chcesz wyłączyć EnableMultipleWriteLocations na koncie usługi Azure Cosmos, [Otwórz bilet pomocy technicznej](https://azure.microsoft.com/support/create-ticket/).
