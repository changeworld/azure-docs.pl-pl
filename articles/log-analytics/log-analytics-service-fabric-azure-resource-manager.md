---
title: Ocena aplikacji usługi Service Fabric z analizy dzienników przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: Rozwiązanie usługi sieć szkieletowa można użyć w analizy dzienników przy użyciu portalu Azure w celu oceny ryzyka i kondycji aplikacji usługi Service Fabric, micro-services, węzły i klastry.
services: log-analytics
documentationcenter: ''
author: niniikhena
manager: jochan
editor: ''
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: nini
ms.openlocfilehash: 10c5394935c59b21139c0e141c348ce20a7c0800
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
ms.locfileid: "28932685"
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Oceń aplikacji usługi Service Fabric i micro-services przy użyciu portalu Azure

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [Program PowerShell](log-analytics-service-fabric.md)
>
>

![Symbol sieci szkieletowej usług](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

W tym artykule opisano sposób użycia rozwiązania sieci szkieletowej usług w analizy dzienników do identyfikacji i rozwiązywania problemów w klastrze usługi sieć szkieletowa usług.

Rozwiązanie usługi Service Fabric korzysta z danych diagnostyki Azure z maszyn wirtualnych, sieci szkieletowej usług zbierać dane z tabel Azure WAD. Analizy dzienników następnie odczytuje zdarzenia framework sieci szkieletowej usług, w tym **niezawodnej zdarzeń usługi**, **zdarzenia aktora**, **zdarzenia operacyjne**, i **zdarzenia ETW niestandardowe**. Z pulpitu nawigacyjnego rozwiązania będą mogli wyświetlić godne uwagi problemy i istotne zdarzenia w środowisku sieci szkieletowej usług.

Aby rozpocząć pracę z rozwiązaniem, należy połączyć z klastra usługi sieć szkieletowa usług do obszaru roboczego analizy dzienników. Poniżej przedstawiono trzy scenariusze, które należy uwzględnić:

1. Jeśli klaster sieci szkieletowej usług nie została wdrożona, wykonaj kroki w ***wdrażanie klastra sieci szkieletowej usług podłączony do obszaru roboczego analizy dzienników*** do wdrożenia nowego klastra i została ona skonfigurowana do raportu analizy dzienników.
2. Jeśli potrzebujesz można zebrać liczników wydajności z hostów do użycia w klastrze usługi sieć szkieletowa rozwiązań do zarządzania, takich jak zabezpieczenia, postępuj zgodnie z instrukcjami ***wdrażanie klastra sieci szkieletowej usług podłączony do obszaru roboczego analizy dzienników rozszerzenie maszyny Wirtualnej zainstalowana.***
3. Jeśli zostały już wdrożone z klastra sieci szkieletowej usług i chcesz nawiązać analizy dzienników, postępuj zgodnie z instrukcjami ***Dodawanie istniejącego konta magazynu do analizy dzienników.***

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Wdrażanie klastra sieci szkieletowej usług podłączony do obszaru roboczego analizy dzienników.
Ten szablon wykonuje następujące czynności:

1. Wdraża klastra usługi sieć szkieletowa usług Azure już połączona z obszaru roboczego analizy dzienników. Istnieje możliwość utworzenia nowego obszaru roboczego podczas wdrażania szablonu lub wpisać nazwę już istniejący obszar roboczy analizy dzienników.
2. Dodaje konto magazynu diagnostyki do obszaru roboczego analizy dzienników.
3. Umożliwia rozwiązanie sieci szkieletowej usług w obszarze roboczym analizy dzienników.

[![Wdrażanie na platformie Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

Po wybraniu przycisku Wdróż powyżej portalu Azure zostanie otwarty z parametrami do edycji. Pamiętaj utworzyć nową grupę zasobów, jeśli możesz wpisać nazwę nowego obszaru roboczego analizy dzienników:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Zaakceptuj postanowienia prawne, a następnie kliknij przycisk **Utwórz** do wdrożenia. Po zakończeniu wdrożenia powinien zostać wyświetlony nowy obszar roboczy i klastra utworzone i WADServiceFabric * zdarzeń, WADWindowsEventLogs i WADETWEvent tabele dodać:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Wdrażanie klastra sieci szkieletowej usług podłączony do obszaru roboczego analizy dzienników zainstalowane rozszerzenie maszyny Wirtualnej.

Ten szablon wykonuje następujące czynności:

1. Wdraża klastra usługi sieć szkieletowa usług Azure już połączona z obszaru roboczego analizy dzienników. Można utworzyć nowego obszaru roboczego lub użyć istniejącego.
2. Dodaje do obszaru roboczego analizy dzienników konta magazynu diagnostyki.
3. Umożliwia rozwiązanie sieci szkieletowej usług w obszarze roboczym analizy dzienników.
4. Instaluje rozszerzenia agent MMA w każdym skalowania maszyny wirtualnej w klastrze usługi sieć szkieletowa usług. Z zainstalowanym agentem MMA będą mogli wyświetlić metryki wydajności o węzły.

[![Wdrażanie na platformie Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Po tej samej powyższe kroki niezbędne parametry wejściowe i rozpocząć poza wdrożenia. Ponownie powinien zostać wyświetlony nowy obszar roboczy, klastra i tabele WAD wszystkie utworzone:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Wyświetlanie danych wydajności

Aby wyświetlić dane wydajności z węzłów:


[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Uruchom obszaru roboczego analizy dzienników z portalu Azure.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- Przejdź do strony ustawień w okienku po lewej stronie, a następnie wybierz dane >> liczników wydajności systemu Windows >> "Dodaj wybrane liczniki wydajności": ![sieci szkieletowej usług](./media/log-analytics-service-fabric/7.png)
- W wyszukiwaniu dziennika należy użyć następujących zapytań do delve do kluczowych metryk dotyczących węzły:

    a. Porównanie średnie wykorzystanie procesora CPU przez wszystkie węzły w ciągu ostatniej godziny jeden węzły, które mają problemy, a w odstępach czasu, jaki węzeł ma kolekcji:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Widok podobne wykresy liniowe dla ilość pamięci dostępna na każdym węźle z tej kwerendy:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Aby wyświetlić listę wszystkich węzłów, przedstawiający dokładną wartość średnia dla dostępna pamięć (MB) dla każdego węzła, skorzystaj z tej kwerendy:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. W przypadku, gdy chcesz przejść do określonego węzła, sprawdzając średniej godzinowej, użycie procesora CPU minimalne, maksymalne i 75 percentyl możesz to zrobić przy użyciu tej kwerendy (Zastąp pole komputera):

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Przeczytaj więcej informacji na temat metryki wydajności w usługi Analiza dzienników w [blogu Operations Management Suite](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Dodawanie istniejącego konta magazynu do analizy dzienników

Ten szablon po prostu dodaje istniejących kont magazynu do nowego lub istniejącego obszaru roboczego analizy dzienników.

[![Wdrażanie na platformie Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Wybór grupy zasobów, jeśli pracujesz już istniejący obszar roboczy analizy dzienników wybierz "Użyj istniejącego" i wyszukaj grupę zasobów, zawierającą obszaru roboczego analizy dzienników. Utwórz nowy Jeśli jeden inaczej.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

Po wdrożeniu tego szablonu można zobaczyć magazynu konto podłączone do obszaru roboczego analizy dzienników. W tym wystąpieniu jedno konto magazynu więcej po dodaniu do obszaru roboczego programu Exchange, utworzone powyżej.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Wyświetl zdarzenia dotyczące sieci szkieletowej usług

Po zakończeniu wdrożenia i sieci szkieletowej usług rozwiązania została włączona w obszarze roboczym, wybierz **sieci szkieletowej usług** kafelka w portalu usługi Analiza dzienników do uruchomienia pulpitu nawigacyjnego sieci szkieletowej usług. Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna wymieniono top 10 zdarzenia według liczby spełniających kryteria tej kolumny. dla określonego przedziału czasu. Możesz uruchomić wyszukiwanie dziennika, które zawiera całą listę klikając **zobaczyć wszystkie** w prawej dolnej każdej kolumny lub przez kliknięcie nagłówka kolumny.

| **Zdarzenie sieci szkieletowej usług** | **Opis elementu** |
| --- | --- |
| Problemy godne uwagi |Wyświetlanie problemy, takie jak RunAsyncFailures RunAsynCancellations i rozwijane węzła. |
| Zdarzenia operacyjne |Godne zdarzenia operacyjne takich jak uaktualniania aplikacji i wdrożenia. |
| Zdarzenia niezawodne usługi |Zdarzenia zauważalne niezawodnej usługi takie Runasyncinvocations. |
| Zdarzenia aktora |Godne aktora zdarzenia generowane przez micro usług, takich jak wyjątki wyrzucane przez metodę aktora, aktora aktywacji i deactivations i tak dalej. |
| Zdarzenia aplikacji |Wszystkie niestandardowe ETW zdarzenia generowane przez aplikacje. |

![Pulpit nawigacyjny sieci szkieletowej usług](./media/log-analytics-service-fabric/sf3.png)

![Pulpit nawigacyjny sieci szkieletowej usług](./media/log-analytics-service-fabric/sf4.png)

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegółowe informacje o jak dane są zbierane dla sieci szkieletowej usług.

| Platformy | Bezpośrednie agenta | Agent programu Operations Manager | Azure Storage | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania | Częstotliwość kolekcji |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 minut |

> [!NOTE]
> Zakres tych zdarzeń w rozwiązaniu sieci szkieletowej usług można zmienić, klikając **dane oparte na ostatnich 7 dni** w górnej części pulpitu nawigacyjnego. Można również wyświetlać zdarzenia generowane w ciągu ostatnich siedmiu dni, jeden dzień lub sześciu godzin. Umożliwia wybranie **niestandardowych** Aby określić zakres niestandardowy.
>
>

## <a name="next-steps"></a>Kolejne kroki

* Użyj [wyszukiwania dziennika analizy dzienników](log-analytics-log-searches.md) Aby wyświetlić szczegółowe dane zdarzeń usługi sieć szkieletowa usług.
