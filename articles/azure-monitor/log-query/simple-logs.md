---
title: Proste środowisko dzienników w Azure Monitor (wersja zapoznawcza) | Microsoft Docs
description: Proste środowisko logowania umożliwia tworzenie podstawowych zapytań w Azure Monitor bez bezpośredniego działania z KQL.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: bwren
ms.openlocfilehash: 323267dd47735ca54b84e47e6a55d1f2d14a0b06
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262173"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Proste środowisko dzienników w Azure Monitor (wersja zapoznawcza)
Azure Monitor oferuje [bogate środowisko](get-started-portal.md) tworzenia [zapytań dzienników](log-query-overview.md) przy użyciu języka KQL. W przypadku podstawowych wymagań związanych z zapytaniami nie można wymagać pełnej mocy KQL. Proste środowisko dzienników umożliwia tworzenie podstawowych zapytań bez bezpośredniego działania z KQL. Możesz również użyć prostych dzienników jako narzędzia edukacyjnego dla KQL, ponieważ potrzebne są bardziej zaawansowane zapytania.

> [!NOTE]
> Dzienniki proste są obecnie zaimplementowane jako testy wyłącznie dla Cosmos DB i magazynów kluczy. Podziel się z firmą Microsoft za pomocą funkcji [User Voice](https://feedback.azure.com/forums/913690-azure-monitor) , aby pomóc nam w ustaleniu, czy ta funkcja zostanie rozszerzona.


## <a name="scope"></a>Scope
W ramach prostych dzienników środowisko to umożliwia pobieranie danych z tabeli *AzureDiagnostics*, *AzureMetrics*i *platformy Azure* dla wybranego zasobu. 

## <a name="using-simple-logs"></a>Korzystanie z prostych dzienników
Przejdź do dowolnego Cosmos DB lub Key Vault w subskrypcji platformy Azure z [ustawieniami diagnostycznymi skonfigurowanymi do zbierania dzienników w obszarze roboczym log Analytics](../platform/resource-logs-collect-storage.md). Kliknij pozycję **dzienniki** w menu **monitorowanie** , aby otworzyć proste środowisko dzienników.

![Menu](media/simple-logs/menu.png)

Wybierz **pole** i **operator** i określ **wartość** do porównania. Kliknij **+** i określ **i/lub,** aby dodać dodatkowe kryteria.

![Kryteria](media/simple-logs/criteria.png)

Kliknij przycisk **Uruchom** , aby wyświetlić wyniki zapytania.

## <a name="view-and-edit-kql"></a>Wyświetlanie i edytowanie KQL
Wybierz **Edytor zapytań** , aby otworzyć KQL wygenerowanego przez proste zapytanie dzienników w pełnym log Analytics. 

![Edytor zapytań](media/simple-logs/query-editor.png)

Możesz bezpośrednio edytować KQL i korzystać z innych funkcji w Log Analytics, takich jak filtry, aby udoskonalić wyniki.

![Edytuj zapytanie KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Następne kroki

- Ukończ samouczek dotyczący [korzystania z log Analytics w Azure Portal](get-started-portal.md).
- Ukończ samouczek dotyczący [pisania zapytań dzienników](get-started-portal.md).
