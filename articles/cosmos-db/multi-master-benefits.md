---
title: Korzyści z platformy Azure Cosmos DB wielu wzorców
description: Poznaj zalety Multi-Master w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789295"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Zrozumienie wielu wzorców korzyści w usłudze Azure Cosmos DB

Operatorzy kont usługi cosmos DB, należy wybrać konfigurację odpowiednią dystrybucji globalnej, aby upewnić się, opóźnienia, dostępności i wymaganiami cel czasu odzyskiwania dla poszczególnych aplikacji. Konta usługi Cosmos Azure skonfigurowano wiele lokalizacji zapisu oferują znaczące korzyści związane za pośrednictwem konta z tym lokalizacji zapisu jednego, 99,999% zapisu umowa SLA dotycząca dostępności < 10 ms opóźnienie zapisu umowy SLA w 99. percentylu i cel czasu odzyskiwania = 0 w przypadku regionalnej awarii.

## <a name="comparison-of-features"></a>Porównanie funkcji

|Wymagania aplikacji|Wiele lokalizacji zapisu|Lokalizacja zapisu jednego|Uwaga|
|---|---|---|---|
|Umowy SLA opóźnienie zapisu < 10 ms na poziomie P99|**Tak**|Nie|Konta z jednej lokalizacji zapisu pociągnąć za sobą dodatkowe opóźnienie między regionami przy każdym zapisie.|
|Umowy SLA opóźnienie odczytu < 10 ms na poziomie P99|**Tak**|Tak| |
|Umowa SLA na poziomie 99,999% zapisu|**Tak**|Nie|Konta z jednej lokalizacji zapisu gwarantuje umowę SLA na poziomie 99,99%|
|CEL CZASU ODZYSKIWANIA = 0|**Tak**|Nie|Zero, przerw do zapisu w przypadku regionalnej awarii. Konta z lokalizacją zapisu pojedynczej mają RTO 15 minut.|

## <a name="next-steps"></a>Następne kroki

Jeśli nadal chcesz wyłączyć EnableMultipleWriteLocations na Twoim koncie usługi Azure Cosmos, [Otwórz bilet pomocy technicznej](https://azure.microsoft.com/support/create-ticket/).
