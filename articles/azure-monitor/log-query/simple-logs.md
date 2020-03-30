---
title: Proste dzienniki w usłudze Azure Monitor (wersja zapoznawcza) | Dokumenty firmy Microsoft
description: Środowisko simple logs umożliwia tworzenie podstawowych zapytań w usłudze Azure Monitor bez bezpośredniej interakcji z KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660261"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Proste dzienniki w usłudze Azure Monitor (wersja zapoznawcza)
Usługa Azure Monitor zapewnia [bogate środowisko](get-started-portal.md) do tworzenia [zapytań dziennika](log-query-overview.md) przy użyciu języka KQL. Nie może być jednak wymagana pełna moc KQL i preferuj uproszczone środowisko dla podstawowych wymagań dotyczących zapytań. Środowisko Simple Logs umożliwia tworzenie podstawowych zapytań bez bezpośredniej interakcji z KQL. Można również użyć dzienników prostych jako narzędzia szkoleniowego dla KQL, ponieważ potrzebujesz bardziej zaawansowanych zapytań.

> [!NOTE]
> Proste dzienniki są obecnie implementowane jako test tylko dla usługi Cosmos DB i Magazyny kluczy. Podziel się swoim doświadczeniem z firmą Microsoft za pośrednictwem [user voice,](https://feedback.azure.com/forums/913690-azure-monitor) aby pomóc nam ustalić, czy rozszerzymy i udostępnimy tę funkcję.


## <a name="scope"></a>Zakres
Środowisko dzienników prostych pobiera dane z *tabeli AzureDiagnostics*, *AzureMetrics*i *AzureActivity* dla wybranego zasobu. 

## <a name="using-simple-logs"></a>Korzystanie z prostych dzienników
Przejdź do dowolnej usługi Cosmos DB lub Key Vault w ramach subskrypcji platformy Azure z [ustawieniami diagnostycznymi skonfigurowanym do zbierania dzienników w obszarze roboczym usługi Log Analytics.](../platform/resource-logs-collect-storage.md) Kliknij **pozycję Dzienniki** w menu **Monitorowanie,** aby otworzyć środowisko Dzienniki proste.

![Menu](media/simple-logs/menu.png)

Wybierz **pole** i **operator** i określ **wartość** do porównania. Kliknij **+** i określ **i/lub,** aby dodać dodatkowe kryteria.

![Kryteria](media/simple-logs/criteria.png)

Kliknij **przycisk Uruchom,** aby wyświetlić wyniki kwerendy.

## <a name="view-and-edit-kql"></a>Wyświetlanie i edytowanie KQL
Wybierz **edytor zapytań,** aby otworzyć KQL generowane przez kwerendę dzienników prostych w pełnym interfejsie usługi Log Analytics. 

![Edytor zapytań](media/simple-logs/query-editor.png)

Możesz bezpośrednio edytować KQL i korzystać z innych funkcji w usłudze Log Analytics, takich jak filtry, aby jeszcze bardziej uściślić wyniki.

![Edycja KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Następne kroki

- Ukończ samouczek dotyczący [korzystania z usługi Log Analytics w witrynie Azure portal](get-started-portal.md).
- Ukończ samouczek dotyczący [pisania zapytań dziennika](get-started-portal.md).
