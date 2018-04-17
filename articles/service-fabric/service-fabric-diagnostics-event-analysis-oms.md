---
title: Analiza zdarzeń sieci szkieletowej usług Azure z analizy dzienników | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wizualizacji i analizowania zdarzeń za pomocą analizy dzienników dla monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 290b1d594cc1f874bcfdd0cef728fc78af96f702
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Analiza zdarzeń i wizualizacji z analizy dzienników

Rozwiązania do zarządzania platformy Azure to zbiór usług zarządzania, które pomagają w monitorowania i diagnostyki dla aplikacji i usług hostowanych w chmurze. Aby uzyskać bardziej szczegółowe omówienie analizy dzienników, jakie oferuje, przeczytaj [co to jest analiza dziennika?](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics

Analiza dzienników zbiera dane z zarządzanych zasobów, włącznie z tabeli magazynu systemu Azure lub agenta i przechowuje ją w centralnym repozytorium. Dane można następnie używana do analizy, alerty i wizualizacji lub dodatkowe eksportowanie. Analiza dzienników obsługuje zdarzeń, danych wydajności lub innych danych niestandardowych.

Po skonfigurowaniu analizy dzienników mają dostęp do określonego *obszaru roboczego analizy dzienników*, z którym danych można wykonać zapytania lub wizualizowane w pulpitach nawigacyjnych.

Po odebraniu danych przez analizy dzienników, platforma Azure ma kilka *rozwiązań do zarządzania* , które są opakowaniach jednostkowych rozwiązań do przychodzących danych, dostosować, tak aby kilka scenariuszy monitorowania. Obejmują one *usługi sieć szkieletowa Analytics* rozwiązania i *kontenery* rozwiązania, które są najbardziej odpowiednie dwóch te Diagnostyka i monitorowanie w przypadku używania klastrów sieci szkieletowej usług. Istnieje kilka innych oraz które warto eksploracji i analizy dzienników umożliwia również tworzenie rozwiązań niestandardowych, które można uzyskać więcej informacji [tutaj](../operations-management-suite/operations-management-suite-solutions.md). W tym samym obszarze roboczym analizy dzienników, obok analizy dzienników można skonfigurować każdego rozwiązania, które chcesz użyć dla klastra. Obszary robocze umożliwiają dla niestandardowych pulpitów nawigacyjnych i wizualizacja danych i modyfikacji danych, które chcesz gromadzenie i przetwarzanie i analizowanie danych.

## <a name="setting-up-a-log-analytics-workspace-with-the-service-fabric-analytics-solution"></a>Konfigurowanie obszaru roboczego analizy dzienników z rozwiązania analizy sieci szkieletowej usług
Zaleca się, że obejmują rozwiązania usługi sieci szkieletowej w obszarze roboczym analizy dzienników — zawiera pulpit nawigacyjny, który zawiera różne kanały dzienników przychodzące z poziomu platformy i aplikacji, a także możliwość wykonywania kwerend platformy Service Fabric dzienniki. W tym miejscu jest stosunkowo proste rozwiązania sieci szkieletowej usługi wygląd, z jednej aplikacji wdrożonych w klastrze:

![Rozwiązanie rz OMS](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Zobacz [Konfigurowanie analizy dzienników](service-fabric-diagnostics-oms-setup.md) rozpocząć pracę z tym dla klastra.

## <a name="using-the-oms-agent"></a>Za pomocą agenta pakietu OMS

Jest zalecane, aby użyć EventFlow i WAD jako rozwiązania agregacji, ponieważ pozwalają one na bardziej podejściu Diagnostyka i monitorowanie. Na przykład jeśli chcesz zmienić Twoje dane wyjściowe z EventFlow wymaga nie zmieniono Twojego rzeczywiste Instrumentacji tylko prostą modyfikację do pliku konfiguracji. Jeśli jednak użytkownik zdecyduje się inwestycji w przy użyciu analizy dzienników, należy skonfigurować [agent pakietu OMS](../log-analytics/log-analytics-windows-agent.md). Należy też używać agent pakietu OMS podczas wdrażania kontenerów do klastra, zgodnie z opisem poniżej. 

HEAD za pośrednictwem do [dodać do klastra Agent pakietu OMS](service-fabric-diagnostics-oms-agent.md) dotyczące kroków związanych z tym.

Zalety tego są następujące:

* Bardziej rozbudowane dane po stronie liczników i metryki wydajności
* Łatwa w konfigurowaniu metryki zbieranych z klastra i bez konieczności aktualizacji konfiguracji sieci klastra. Zmiany ustawień agenta może odbywać się w portalu OMS i do dopasowania ustawień konfiguracji niezbędnych do automatycznego uruchomienia agenta. Aby skonfigurować agenta pakietu OMS do pobrania konkretnych licznikach wydajności, przejdź do obszaru roboczego **strona główna > Ustawienia > danych > liczników wydajności systemu Windows** i wybierz dane, które chcesz wyświetlić zebrane
* Dane zostaną wyświetlone szybciej niż musi być przechowywany przed odbierany przez analizy dzienników
* Monitorowanie kontenery jest znacznie łatwiejsze, ponieważ jego mogą odbierać dzienniki docker (stdout, stderr) oraz statystyka (metryki wydajności na poziomach kontenera i węzła)

Główne w tym miejscu to, ponieważ agent zostanie wdrożony w klastrze z wszystkich aplikacji, może wystąpić pewne wpływ na wydajność aplikacji w klastrze.

## <a name="monitoring-containers"></a>Kontenery monitorowania

Podczas wdrażania kontenerów do klastra usługi sieć szkieletowa, zaleca się, że klaster został skonfigurowany z agentem pakietu OMS i czy rozwiązania kontenery został dodany do obszaru roboczego analizy dzienników, aby włączyć monitorowanie i diagnostyki. Oto, jak wygląda rozwiązania kontenery w obszarze roboczym:

![Pulpit nawigacyjny podstawowe OMS](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agent umożliwia zbieranie kilka dzienników specyficzne dla kontenera, które można wykonać zapytania w analizy dzienników, lub używany do wizualizowanego wskaźników. Typy dziennika, które są zbierane są:

* ContainerInventory: zawiera informacje o lokalizacji kontenera, nazwy i obrazów
* ContainerImageInventory: informacje o wdrożonej obrazów, w tym identyfikatory lub rozmiary
* ContainerLog: dzienniki błędu, dzienniki docker (stdout itp.) i innych pozycji
* ContainerServiceLog: docker demon poleceń, które zostały uruchomione
* O wydajności: liczniki wydajności w tym kontenerze procesora cpu, pamięć, ruch sieciowy na dysku We/Wy i metryki niestandardowe z maszyn hosta

[Monitorowanie kontenery z analizy dzienników](service-fabric-diagnostics-oms-containers.md) opisano kroki wymagane do skonfigurowania kontenera monitorowania dla klastra. Aby dowiedzieć się więcej o rozwiązaniu kontenery analizy dzienników, zapoznaj się ich [dokumentacji](../log-analytics/log-analytics-containers.md).

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z następujących narzędzi analizy dzienników i opcji, aby dostosować obszar roboczy do potrzeb:

* W przypadku klastrów lokalnymi analizy dzienników oferuje bramy (do przodu serwer Proxy HTTP), która może służyć do wysyłania danych do analizy dzienników. Dowiedz się więcej o tym, że w [łączenie komputerów bez dostępu do Internetu z analizy dzienników przy użyciu bramy OMS](../log-analytics/log-analytics-oms-gateway.md)
* Konfigurowanie analizy dzienników, aby skonfigurować [automatycznego alerty](../log-analytics/log-analytics-alerts.md) do pomocy w wykrywaniu i Diagnostyka
* Pobierz zapoznaniu się z [wyszukiwania i badania dziennika](../log-analytics/log-analytics-log-searches.md) funkcje dostępne w ramach analizy dzienników