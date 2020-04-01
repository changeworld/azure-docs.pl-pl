---
title: Samouczek dystrybucji globalnej usługi Azure Cosmos DB dla interfejsu API tabel
description: Dowiedz się, jak działa dystrybucja globalna na kontach interfejsu API tabeli usługi Azure Cosmos DB i jak skonfigurować preferowaną listę regionów
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76900185"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Konfigurowanie dystrybucji globalnej usługi Azure Cosmos DB przy użyciu interfejsu API tabel

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu interfejsów [API tabel](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Nawiązywanie połączenia z preferowanym regionem przy użyciu interfejsu API tabel

Aby skorzystać z [dystrybucji globalnej,](distribute-data-globally.md)aplikacje klienckie powinny określić bieżącą lokalizację, w której jest uruchomiona ich aplikacja. Odbywa się to `CosmosExecutorConfiguration.CurrentRegion` przez ustawienie właściwości. Właściwość `CurrentRegion` powinna zawierać jedną lokalizację. Każde wystąpienie klienta można określić swój własny region dla odczytów o małym opóźnieniu. Region musi być nazwany przy użyciu ich [nazw wyświetlanych,](https://msdn.microsoft.com/library/azure/gg441293.aspx) takich jak "Zachodnie stany USA". 

Zestaw SDK interfejsu API tabeli usługi Azure Cosmos DB automatycznie wybiera najlepszy punkt końcowy do komunikowania się na podstawie konfiguracji konta i bieżącej dostępności regionalnej. Nadaje priorytet najbliższemu regionowi, aby zapewnić lepsze opóźnienia dla klientów. Po ustawieniu `CurrentRegion` bieżącej właściwości żądania odczytu i zapisu są kierowane w następujący sposób:

* **Prośby o odczyt:** Wszystkie żądania odczytu są `CurrentRegion`wysyłane do skonfigurowanych . Na podstawie bliskości, SDK automatycznie wybiera rezerwowy region replikowany geograficznie dla wysokiej dostępności.

* **Żądania zapisu:** SDK automatycznie wysyła wszystkie żądania zapisu do bieżącego regionu zapisu. Na koncie multi master bieżący region będzie również obsługiwać żądania zapisu. Na podstawie bliskości, SDK automatycznie wybiera rezerwowy region replikowany geograficznie dla wysokiej dostępności.

Jeśli właściwość nie `CurrentRegion` zostanie określona, sdk używa bieżącego regionu zapisu dla wszystkich operacji.

Na przykład jeśli konto usługi Azure Cosmos znajduje się w regionach "Zachodnie stany USA" i "Wschodnie stany USA". Jeśli "Zachodnie stany USA" jest regionem zapisu, a aplikacja jest obecna w "Wschodnich stanach USA". Jeśli właściwość CurrentRegion nie jest skonfigurowana, wszystkie żądania odczytu i zapisu są zawsze kierowane do regionu "Zachodnie stany USA". Jeśli currentregion właściwość jest skonfigurowana, wszystkie żądania odczytu są obsługiwane z regionu "Wschodnie stany USA".

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu interfejsu API tabel usługi Azure Cosmos DB

