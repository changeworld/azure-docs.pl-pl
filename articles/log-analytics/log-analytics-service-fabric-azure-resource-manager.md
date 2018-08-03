---
title: Oceny aplikacji usługi Service Fabric za pomocą usługi Log Analytics przy użyciu witryny Azure portal | Dokumentacja firmy Microsoft
description: Rozwiązania usługi Service Fabric można użyć w usłudze Log Analytics przy użyciu portalu Azure w celu oceny ryzyka i kondycji aplikacji usługi Service Fabric, mikrousługi, węzły i klastry.
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
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: nini
ms.component: na
ms.openlocfilehash: 9bc1f7d9eab73a086e664dcc520ecf26befbbf0e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432895"
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Ocena aplikacji usługi Service Fabric i mikrousług za pomocą witryny Azure portal

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [Program PowerShell](log-analytics-service-fabric.md)
>
>

![Symbol usługi Service Fabric](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

W tym artykule opisano, jak używać rozwiązania usługi Service Fabric w usłudze Log Analytics do identyfikacji i rozwiązuj problemy w klastrze usługi Service Fabric.

Rozwiązanie usługi Service Fabric używa danych diagnostycznych platformy Azure z maszynami wirtualnymi usługi Service Fabric, przez zbieranie tych danych z tabel Azure WAD. Usługa log Analytics następnie odczytuje zdarzenia framework usługi Service Fabric, w tym **zdarzenia interfejsu Reliable Service**, **zdarzenia aktora**, **zdarzenia operacyjne**, i **niestandardowe Zdarzenia ETW**. Za pomocą pulpitu nawigacyjnego rozwiązania jesteś w stanie wyświetlić problemy godne uwagi i istotne zdarzenia w danym środowisku usługi Service Fabric.

Aby rozpocząć pracę z rozwiązaniem, należy połączyć z klastra usługi Service Fabric do obszaru roboczego usługi Log Analytics. Poniżej przedstawiono trzy scenariusze, należy wziąć pod uwagę:

1. Jeśli nie wdrożono klaster usługi Service Fabric, wykonaj kroki w ***Wdróż klaster usługi Service Fabric połączone z obszarem roboczym usługi Log Analytics*** do wdrożenia nowego klastra i jest skonfigurowany pod kątem raportowania do usługi Log Analytics.
1. Jeśli zachodzi potrzeba liczniki wydajności są zbierane z hostów na korzystanie z rozwiązania do zarządzania, takie jak zabezpieczenia klastra usługi Service Fabric, wykonaj kroki opisane w ***Wdróż klaster usługi Service Fabric połączone z obszarem roboczym usługi Log Analytics, za pomocą rozszerzenia maszyny Wirtualnej zainstalowane.***
1. Jeśli masz już wdrożone usługi klastra usługi Service Fabric i chcesz, aby podłączyć ją do usługi Log Analytics, wykonaj kroki opisane w ***Dodawanie istniejącego konta magazynu do usługi Log Analytics.***

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Wdrażanie klastra usługi Service Fabric, połączone z obszarem roboczym usługi Log Analytics.
Ten szablon wykonuje następujące czynności:

1. Służy do wdrażania klastra usługi Azure Service Fabric już połączona z obszarem roboczym usługi Log Analytics. Masz opcję, aby utworzyć nowy obszar roboczy podczas wdrażania szablonu, lub wprowadź nazwę istniejącego obszaru roboczego usługi Log Analytics.
1. Dodaje konto magazynu diagnostyki do obszaru roboczego usługi Log Analytics.
1. Włącza rozwiązanie usługi Service Fabric, w obszarze roboczym usługi Log Analytics.

[![Wdrażanie na platformie Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

Gdy wybierzesz przycisk "deploy" powyżej, witryny Azure portal zostanie otwarty z parametrami do edycji. Pamiętaj utworzyć nową grupę zasobów, jeśli należy wprowadzić nazwę nowego obszaru roboczego usługi Log Analytics:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Zaakceptuj postanowienia prawne i kliknij przycisk **Utwórz** rozpocząć wdrażanie. Po zakończeniu wdrożenia powinien zostać wyświetlony nowy obszar roboczy i klastra utworzone i WADServiceFabric * zdarzenie, WADWindowsEventLogs i WADETWEvent tabele dodane:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Wdrażanie klastra usługi Service Fabric, połączone z obszarem roboczym usługi Log Analytics za pomocą zainstalowane rozszerzenie maszyny Wirtualnej.

Ten szablon wykonuje następujące czynności:

1. Służy do wdrażania klastra usługi Azure Service Fabric już połączona z obszarem roboczym usługi Log Analytics. Można utworzyć nowy obszar roboczy lub użyć istniejącego.
1. Dodaje konta magazynu diagnostyki do obszaru roboczego usługi Log Analytics.
1. Włącza rozwiązanie usługi Service Fabric, w obszarze roboczym usługi Log Analytics.
1. Instaluje rozszerzenie agenta MMA w każdym zestawie skalowania maszyn wirtualnych w klastrze usługi Service Fabric. Z zainstalowanym agentem programu MMA jest możliwe wyświetlić metryki wydajności dotyczące węzłów.

[![Wdrażanie na platformie Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Po tej samej powyższe kroki niezbędne parametry wejściowe i uruchamiał wdrożenia. Powinny zostać wyświetlone ponownie nowy obszar roboczy, klaster i wszystkie utworzone tabele WAD:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Wyświetlanie danych wydajności

Aby wyświetlić dane wydajności z węzłów:


[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Uruchom obszar roboczy usługi Log Analytics w witrynie Azure portal.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- Przejdź do strony Ustawienia w okienku po lewej stronie i wybierz dane >> liczniki wydajności Windows >> "Dodaj wybrane liczniki wydajności": ![usługi Service Fabric](./media/log-analytics-service-fabric/7.png)
- Podczas wyszukiwania dziennika należy użyć następujących zapytań do delve do kluczowych metryk dotyczących węzłów:

    a. Porównaj średnie wykorzystanie procesora CPU między wszystkie węzły w ciągu ostatniej godziny, aby wyświetlić węzły, które występują problemy, a w odstępach czasu, jaki węzeł miał największy wzrost:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Wyświetl podobne wykresy liniowe dla dostępnej pamięci w każdym węźle przy użyciu tego zapytania:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Aby wyświetlić listę wszystkich węzłów, przedstawiający dokładna wartość średnia dostępna pamięć (MB) dla każdego węzła, skorzystaj z tej kwerendy:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. W przypadku, gdy chcesz, aby przejść do określonego węzła, sprawdzając godzinowe średnią, minimalną, maksymalną i percentyl 75 użycie procesora CPU możesz to zrobić przy użyciu tego zapytania (Zastąp pole komputera):

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Przeczytaj więcej informacji na temat metryk wydajności w usłudze Log Analytics w [Blog dotyczący pakietu Operations Management Suite](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Dodawanie istniejącego konta magazynu do usługi Log Analytics

Ten szablon dodaje po prostu istniejących kont magazynu do nowego lub istniejącego obszaru roboczego usługi Log Analytics.

[![Wdrażanie na platformie Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Wybierając grupę zasobów, jeśli pracujesz już istniejący obszar roboczy usługi Log Analytics wybierz opcję "Użyj istniejącej" i wyszukaj grupę zasobów zawierającą obszaru roboczego usługi Log Analytics. Utwórz nowy Jeśli jeden inny sposób.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

Po wdrożeniu tego szablonu można wyświetlić konto magazynu, połączone z obszarem roboczym usługi Log Analytics. W tym wypadku jedno konto magazynu więcej po dodaniu do obszaru roboczego programu Exchange, utworzony powyżej.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Wyświetl zdarzenia dotyczące usługi Service Fabric

Po zakończeniu wdrożenia i rozwiązania usługi Service Fabric zostało włączone w obszarze roboczym, wybierz **usługi Service Fabric** kafelka w portalu usługi Log Analytics, aby uruchomić Pulpit nawigacyjny usługi Service Fabric. Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera listę 10 najważniejszych zdarzeń według liczby odpowiadającego kryteriom tej kolumny dla określonego zakresu czasu. Można uruchomić wyszukiwanie w dzienniku, który zawiera całą listę, klikając pozycję **holograficznych** w prawej dolnej każdej kolumny lub przez kliknięcie nagłówka kolumny.

| **Zdarzenia usługi Service Fabric** | **Opis elementu** |
| --- | --- |
| Problemy godne uwagi |Wyświetlanie problemów, takich jak RunAsyncFailures RunAsynCancellations i szczegółu węzła. |
| Zdarzenia operacyjne |Istotne zdarzenia operacyjne takich jak uaktualnianie aplikacji i wdrożenia. |
| Zdarzenia usługi Reliable Service |Zdarzenia istotnych usługi reliable service takich Runasyncinvocations. |
| Zdarzenia aktora |Zdarzenia istotnych aktora, generowane przez operacje micro usługi, takie jak wyjątki zgłaszane przez metodę aktora, aktywacji aktora i deactivations i tak dalej. |
| Zdarzenia aplikacji |Wszystkie niestandardowe zdarzenia ETW wygenerowane przy użyciu aplikacji. |

![Pulpit nawigacyjny usługi Service Fabric](./media/log-analytics-service-fabric/sf3.png)

![Pulpit nawigacyjny usługi Service Fabric](./media/log-analytics-service-fabric/sf4.png)

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegóły dotyczące sposobu dane są zbierane dla usługi Service Fabric.

| Platforma | Agent bezpośredni | Agent programu Operations Manager | Azure Storage | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 minut |

> [!NOTE]
> Zakres tych zdarzeń w rozwiązaniu usługi Service Fabric można zmienić, klikając **dane oparte na ostatnich 7 dni** w górnej części pulpitu nawigacyjnego. Można również pokazać zdarzenia wygenerowane w ciągu ostatnich siedmiu dni, jeden dzień lub sześć godzin. Lub możesz wybrać **niestandardowe** określić niestandardowy zakres dat.
>
>

## <a name="next-steps"></a>Kolejne kroki

* Użyj [wyszukiwań w dziennikach w usłudze Log Analytics](log-analytics-log-searches.md) Aby wyświetlić szczegółowe dane zdarzeń usługi Service Fabric.
