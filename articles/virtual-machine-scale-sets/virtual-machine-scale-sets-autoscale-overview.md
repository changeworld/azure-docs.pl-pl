---
title: Omówienie autoskalowania w zestawach skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się więcej o różnych sposobach automatycznego skalowania zestawu skalowania maszyny wirtualnej platformy Azure na podstawie wydajności lub ustalonego harmonogramu
author: cynthn
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb96be187502afcccfd3fb2c88f709facfbc3b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278140"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Omówienie autoskalowania w zestawach skalowania maszyn wirtualnych platformy Azure
Zestaw skalowania maszyny wirtualnej platformy Azure może automatycznie zwiększać lub zmniejszać liczbę wystąpień maszyn wirtualnych, które uruchamiają aplikację. To zautomatyzowane i elastyczne zachowanie zmniejsza obciążenie związane z zarządzaniem w celu monitorowania i optymalizacji wydajności aplikacji. Tworzenie reguł, które definiują dopuszczalną wydajność dla pozytywnego doświadczenia klienta. Po spełnieniu tych zdefiniowanych progów reguły skalowania automatycznego podejmują działania w celu dostosowania pojemności zestawu skalowania. Można również zaplanować zdarzenia, aby automatycznie zwiększać lub zmniejszać pojemność zestawu skalowania o ustalonych godzinach. Ten artykuł zawiera omówienie, które metryki wydajności są dostępne i jakie akcje można wykonać skalowanie automatyczne.


## <a name="benefits-of-autoscale"></a>Korzyści ze skalowania automatycznego
Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawie skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania.

Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Można kontrolować, jakie metryki do monitorowania, takich jak procesor CPU lub pamięci, jak długo obciążenie aplikacji musi spełniać dany próg i ile wystąpień maszyny Wirtualnej, aby dodać do zestawu skalowania.

Wieczorami lub w weekendy zapotrzebowanie aplikacji może być mniejsze. Jeśli spadek obciążenia ma cechy stałego trendu w danym okresie, można skonfigurować reguły skalowania automatycznego umożliwiające zmniejszenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Akcja skalowania w pionie ogranicza koszt używania zestawu skalowania, ponieważ jest uruchomionych tylko tyle wystąpień, ile jest wymaganych do zaspokojenia bieżącego zapotrzebowania.


## <a name="use-host-based-metrics"></a>Korzystanie z danych opartych na hoście
Można utworzyć reguły skalowania automatycznego, które wbudowane metryki hosta dostępne z wystąpień maszyny Wirtualnej. Metryki hosta zapewniają wgląd w wydajność wystąpień maszyn wirtualnych w zestawie skalowania bez konieczności instalowania lub konfigurowania dodatkowych agentów i kolekcji danych. Reguły skalowania automatycznego, które używają tych metryk można skalować w poziomie lub w liczbie wystąpień maszyn wirtualnych w odpowiedzi na użycie procesora CPU, zapotrzebowanie na pamięć lub dostęp do dysku.

Reguły automatycznego skalowania, które korzystają z metryk opartych na hoście, można utworzyć za pomocą jednego z następujących narzędzi:

- [Portal Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Interfejs wiersza polecenia platformy Azure](tutorial-autoscale-cli.md)
- [Szablon Azure](tutorial-autoscale-template.md)

Aby utworzyć reguły skalowania automatycznego, które używają bardziej szczegółowych metryk wydajności, można [zainstalować i skonfigurować rozszerzenie diagnostyki platformy Azure](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) w wystąpieniach maszyn wirtualnych lub [skonfigurować aplikację przy użyciu usługi App Insights](#application-level-metrics-with-app-insights).

Reguły skalowania automatycznego, które używają metryk opartych na hoście, metryk maszyn wirtualnych gości z rozszerzeniem diagnostycznym platformy Azure i usługi App Insights mogą używać następujących ustawień konfiguracji.

### <a name="metric-sources"></a>Źródła metryki
Reguły skalowania automatycznego mogą używać danych z jednego z następujących źródeł:

| Źródło metryki        | Przypadek użycia                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Bieżący zestaw skalowania    | Dla metryk opartych na hoście, które nie wymagają dodatkowych agentów do zainstalowania lub skonfigurowania.                                  |
| Konto magazynu      | Rozszerzenie diagnostyczne platformy Azure zapisuje metryki wydajności w magazynie platformy Azure, który jest następnie zużywany do wyzwalania reguł skalowania automatycznego. |
| Kolejka usługi Service Bus    | Aplikacja lub inne składniki mogą przesyłać wiadomości w kolejce usługi Azure Service Bus do wyzwalania reguł.                   |
| Application Insights | Pakiet instrumentacji zainstalowany w aplikacji, który strumieniuje metryki bezpośrednio z aplikacji.                         |


### <a name="autoscale-rule-criteria"></a>Kryteria reguły skalowania automatycznego
Następujące metryki oparte na hoście są dostępne do użycia podczas tworzenia reguł skalowania automatycznego. Jeśli używasz rozszerzenia diagnostycznego platformy Azure lub usługi App Insights, można zdefiniować, które metryki do monitorowania i używania z regułami skalowania automatycznego.

| Nazwa metryki               |
|---------------------------|
| Procentowe użycie procesora CPU            |
| Sieć — wejście                |
| Sieć — wyjście               |
| Bajty odczytu dysku           |
| Bajty zapisu dysku          |
| Operacje odczytu dysku/s  |
| Operacje zapisu dysku/s |
| Pozostałe kredyty CPU     |
| Zużyte kredyty CPU      |

Podczas tworzenia reguł skalowania automatycznego w celu monitorowania danej metryki reguły analizują jedną z następujących akcji agregacji metryk:

| Typ agregacji |
|------------------|
| Średnia          |
| Minimalne          |
| Maksimum          |
| Łącznie            |
| Last             |
| Liczba            |

Reguły skalowania automatycznego są następnie wyzwalane, gdy metryki są porównywane ze zdefiniowanym progiem z jednym z następujących operatorów:

| Operator                 |
|--------------------------|
| Większe niż             |
| Większe niż lub równe |
| Mniejsze niż                |
| Mniejsze niż lub równe    |
| Równe                 |
| Różne od             |


### <a name="actions-when-rules-trigger"></a>Akcje wyzwalane przez reguły
Po wyzwoleniu reguły skalowania automatycznego zestaw skalowania może być automatycznie skalowany w jeden z następujących sposobów:

| Operacja skalowania     | Przypadek użycia                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Zwiększ licznik o   | Stała liczba wystąpień maszyn wirtualnych do utworzenia. Przydatne w zestawach skalowania z mniejszą liczbą maszyn wirtualnych.                                           |
| Zwiększ procent o | Procentowy wzrost wystąpień maszyn wirtualnych. Dobre dla większych zestawów skalowania, gdzie stały wzrost może nie znacznie poprawić wydajność. |
| Zwiększ liczbę do   | Utwórz jak najwięcej wystąpień maszyn wirtualnych są wymagane do osiągnięcia żądanej maksymalnej kwoty.                                                            |
| Zmniejsz licznik o   | Stała liczba wystąpień maszyn wirtualnych do usunięcia. Przydatne w zestawach skalowania z mniejszą liczbą maszyn wirtualnych.                                           |
| Zmniejsz procent o | Procentowy spadek wystąpień maszyn wirtualnych. Dobre dla większych zestawów skalowania, gdzie stały wzrost może nie znacznie zmniejszyć zużycie zasobów i koszty. |
| Zmniejsz liczbę do   | Usuń tyle wystąpień maszyn wirtualnych są wymagane do osiągnięcia żądanej minimalnej kwoty.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Metryki maszyny wirtualnej gościa z rozszerzeniem diagnostyki platformy Azure
Rozszerzenie diagnostyki platformy Azure jest agentem, który działa wewnątrz wystąpienia maszyny Wirtualnej. Agent monitoruje i zapisuje metryki wydajności w magazynie platformy Azure. Te metryki wydajności zawierają bardziej szczegółowe informacje o stanie maszyny Wirtualnej, takie jak *AverageReadTime* dla dysków lub *PercentIdleTime* dla procesora CPU. Reguły skalowania automatycznego można tworzyć na podstawie bardziej szczegółowej świadomości wydajności maszyny Wirtualnej, a nie tylko procent użycia procesora CPU lub zużycia pamięci.

Aby użyć rozszerzenia diagnostyki platformy Azure, należy utworzyć konta magazynu platformy Azure dla wystąpień maszyn wirtualnych, zainstalować agenta diagnostyki platformy Azure, a następnie skonfigurować maszyny wirtualne do przesyłania strumieniowego liczników wydajności określonych do konta magazynu.

Aby uzyskać więcej informacji, zobacz artykuły o tym, jak włączyć rozszerzenie diagnostyki platformy Azure na [maszynie wirtualnej z systemem Linux](../virtual-machines/extensions/diagnostics-linux.md) lub [maszynie wirtualnej z systemem Windows](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Metryki na poziomie aplikacji za pomocą usługi App Insights
Aby uzyskać większą widoczność wydajności aplikacji, można użyć usługi Application Insights. Zainstaluj mały pakiet instrumentacji w aplikacji, który monitoruje aplikację i wysyła dane telemetryczne do platformy Azure. Można monitorować metryki, takie jak czasy odpowiedzi aplikacji, wydajność ładowania strony i liczby sesji. Te metryki aplikacji mogą służyć do tworzenia reguł skalowania automatycznego na poziomie szczegółowym i osadzonym podczas wyzwalania reguł na podstawie szczegółowych informacji, które mogą mieć wpływ na środowisko klienta.

Aby uzyskać więcej informacji o usłudze Application Insights, zobacz [Co to jest usługa Application Insights](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Zaplanowana skalowanie automatyczne
Można również tworzyć reguły skalowania automatycznego na podstawie harmonogramów. Te reguły oparte na harmonogramie umożliwiają automatyczne skalowanie liczby wystąpień maszyn wirtualnych o stałych porach. W przypadku reguł opartych na wydajności może mieć wpływ na wydajność aplikacji, zanim wyzwolić reguły skalowania automatycznego i nowe wystąpienia maszyn wirtualnych. Jeśli można przewidzieć takie zapotrzebowanie, dodatkowe wystąpienia maszyny Wirtualnej są aprowizowane i gotowe do dodatkowego użycia klienta i żądania aplikacji.

Poniższe przykłady to scenariusze, które mogą być korzystne dla korzystania z reguł skalowania automatycznego opartego na harmonogramie:

- Automatyczne skalowanie w poziomie liczby wystąpień maszyn wirtualnych na początku dnia roboczego, gdy wzrasta zapotrzebowanie klientów. Na koniec dnia roboczego automatycznie skaluj liczbę wystąpień maszyn wirtualnych, aby zminimalizować koszty zasobów przez noc, gdy użycie aplikacji jest niskie.
- Jeśli dział używa aplikacji mocno w niektórych częściach miesiąca lub cyklu obrachunkowego, automatycznie skaluj liczbę wystąpień maszyn wirtualnych, aby spełnić ich dodatkowe wymagania.
- W przypadku zdarzenia marketingowego, promocji lub sprzedaży świątecznej można automatycznie skalować liczbę wystąpień maszyn wirtualnych przed przewidywanym zapotrzebowaniem klientów. 


## <a name="next-steps"></a>Następne kroki
Reguły skalowania automatycznego, które używają metryk opartych na hoście, można utworzyć za pomocą jednego z następujących narzędzi:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Interfejs wiersza polecenia platformy Azure](tutorial-autoscale-cli.md)
- [Szablon Azure](tutorial-autoscale-template.md)

W tym omówieniu opisano sposób używania reguł skalowania automatycznego do skalowania w poziomie i zwiększania lub zmniejszania *liczby* wystąpień maszyn wirtualnych w zestawie skalowania. Można również skalować w pionie, aby zwiększyć lub zmniejszyć *rozmiar*wystąpienia maszyny Wirtualnej . Aby uzyskać więcej informacji, zobacz [Pionowa skalowanie automatyczne z zestawami skalowania maszyny wirtualnej](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Aby uzyskać informacje na temat zarządzania wystąpieniami maszyn wirtualnych, zobacz [Zarządzanie zestawami skalowania maszyn wirtualnych za pomocą programu Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Aby dowiedzieć się, jak generować alerty po wyzwoleniu reguł skalowania automatycznego, zobacz [Wysyłanie powiadomień alertów e-mail i webhook w usłudze Azure Monitor za pomocą akcji skalowania automatycznego.](../azure-monitor/platform/autoscale-webhook-email.md) Za pomocą dzienników inspekcji można również [wysyłać powiadomienia o alertach poczty e-mail i elementu webhook w usłudze Azure Monitor.](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
