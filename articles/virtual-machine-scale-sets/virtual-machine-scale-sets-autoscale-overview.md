---
title: Przegląd automatycznie skalowana za pomocą zestawów skali maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o różnych sposobach może automatycznie skalować skali maszyny wirtualnej platformy Azure, wybrani na wydajność lub zgodnie z ustalonym harmonogramem
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 03053f8427fbd20b0a7288d930dca258ee3070b6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Ustawia Omówienie automatycznego skalowania o skali maszyny wirtualnej platformy Azure
Zestaw skali maszyny wirtualnej platformy Azure automatycznie można zwiększyć lub zmniejszyć liczbę wystąpień maszyn wirtualnych, których uruchamiana jest aplikacja. To zachowanie automatyczne i elastyczny zmniejsza obciążenie związane z zarządzania do monitorowania i zoptymalizować wydajność aplikacji. Możesz utworzyć reguły określające minimalny akceptowalną wydajność środowisko dodatnią klienta. Po spełnieniu tych progów zdefiniowanych reguł skalowania automatycznego reakcję pojemności sieci zestawu skali. Można również zaplanować zdarzeń, aby automatycznie zwiększyć lub zmniejszyć pojemność zestawu skalowania w stałej razy. Ten artykuł zawiera omówienie metryki wydajności, które są dostępne i skalowania automatycznego jakie akcje mogą wykonywać.


## <a name="benefits-of-autoscale"></a>Korzyści wynikające z automatycznego skalowania
Jeśli Twoje żądanie aplikacji zwiększa, obciążenie wystąpień maszyn wirtualnych w skali sieci ustawić zwiększa. Jeśli ta zwiększone obciążenie jest spójne, a nie tylko krótki żądanie, można skonfigurować reguł skalowania automatycznego, aby zwiększyć liczbę wystąpień maszyny Wirtualnej w zestawie skalowania.

Gdy te wystąpień maszyn wirtualnych są tworzone i wdrożonych aplikacji, zestaw skali rozpoczyna Dystrybuuj ruch do nich za pośrednictwem usługi równoważenia obciążenia. Można określić, jakie metryk do monitorowania, takie jak procesor CPU lub pamięci, jak długo obciążenia aplikacji muszą spełniać podany próg i ustaw liczbę wystąpień maszyny Wirtualnej, aby dodać do skali.

Na wieczorem lub w weekendy Twoje żądanie aplikacji mogą się zmniejszyć. Jeśli to zmniejszyć obciążenie jest spójne w danym okresie czasu, można skonfigurować reguły automatycznego skalowania, aby zmniejszyć liczbę wystąpień maszyny Wirtualnej w zestawie skalowania. Ta akcja skali zmniejsza koszt do uruchomienia na skalę ustawione jako tylko Uruchom liczba wystąpień wymaganych do bieżącego zapotrzebowania.


## <a name="use-host-based-metrics"></a>Metryki oparta na hoście
Można utworzyć reguły automatycznego skalowania tego dostępne metryki wbudowanych hosta z wystąpień maszyn wirtualnych. Metryki hosta zapewniają wgląd w działanie wystąpień maszyn wirtualnych w skali, bez potrzeby zainstalować lub skonfigurować dodatkowe agentów i zbierania danych. Reguły automatycznego skalowania, korzystających z tych metryk można skalować, out lub liczby wystąpień maszyn wirtualnych w odpowiedzi na użycie procesora CPU, pamięci żądanie lub dostępu do dysku.

Reguły automatycznego skalowania, które korzystają z metryk opartych na hoście, można utworzyć za pomocą jednego z następujących narzędzi:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Interfejs wiersza polecenia platformy Azure 2.0](tutorial-autoscale-cli.md)
- [Szablonu Azure](tutorial-autoscale-template.md)

Aby tworzyć reguły automatycznego skalowania bardziej szczegółowe metryki wydajności, można [zainstalować i skonfigurować rozszerzenia diagnostyki Azure](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) na wystąpień maszyny Wirtualnej lub [skonfigurować użytkowania aplikacji usługi App Insights](#application-level-metrics-with-app-insights).

Reguły automatycznego skalowania, które metryki oparta na hoście, metryki maszyny Wirtualnej gościa z rozszerzenie diagnostyki Azure i usługi App Insights można użyć następujących ustawień konfiguracji.

### <a name="metric-sources"></a>Metryki źródeł
Reguły automatycznego skalowania może metryki z jednego z następujących źródeł:

| Źródło metryki        | Przypadek użycia                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Bieżący zestaw skali    | Dla metryki oparta na hoście, który nie wymaga dodatkowych agentów, które mają być zainstalowane lub skonfigurowane.                                  |
| Konto magazynu      | Rozszerzenie diagnostyki Azure zapisuje metryki wydajności do magazynu Azure, który zostaje następnie użyty do wyzwolenia reguły automatycznego skalowania. |
| Kolejka usługi Service Bus    | Aplikacja ani innych składników może przesyłać komunikaty w kolejce usługi Azure Service Bus reguł wyzwalacza.                   |
| Application Insights | Pakiet Instrumentacji zainstalowane w aplikacji, która strumieni metryki bezpośrednio z aplikacji.                         |


### <a name="autoscale-rule-criteria"></a>Kryteria reguły automatycznego skalowania
Następujące metryki oparta na hoście są dostępne do użycia podczas tworzenia reguł automatycznego skalowania. Jeśli używasz rozszerzenia diagnostycznych platformy Azure lub usługi App Insights, należy zdefiniować metryk, które do monitorowania i używać przy użyciu reguł automatycznego skalowania.

| Nazwa metryki               |
|---------------------------|
| Procentowe użycie procesora CPU            |
| Sieć — wejście                |
| Sieć — wyjście               |
| Bajty odczytane z dysku           |
| Bajty zapisane na dysku          |
| Operacje odczytu dysku/s  |
| Operacje zapisu dysku/s |
| Pozostałe środki na procesory CPU     |
| Wykorzystane środki na procesory CPU      |

Podczas tworzenia reguły automatycznego skalowania do monitorowania danej metryki reguły przyjrzeć się jeden z następujących akcji agregacji metryki:

| Typ agregacji |
|------------------|
| Średnia          |
| Minimalne          |
| Maksimum          |
| Łącznie            |
| Ostatnia             |
| Licznik            |

Reguły automatycznego skalowania następnie są wyzwalane, gdy metryki są porównywane z określoną wartość progową z jednym z następujących operatorów:

| Operator                 |
|--------------------------|
| Więcej niż             |
| Większe niż lub równe |
| Mniej niż                |
| Mniejsze niż lub równe    |
| Równe                 |
| Różne od             |


### <a name="actions-when-rules-trigger"></a>Akcje podczas wyzwolenia reguły
Gdy wyzwalacze reguły automatycznego skalowania, zestaw skali może automatycznie skalować w jednym z następujących sposobów:

| Operacja skalowania     | Przypadek użycia                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Zwiększ liczbę o   | Stałej liczby wystąpień maszyn wirtualnych do utworzenia. Przydatne w zestawy skalowania o mniejszej liczby maszyn wirtualnych.                                           |
| Zwiększ wartość procentową o | Zwiększ procentowych wystąpień maszyn wirtualnych. Dobra na większą skalę Ustawia, gdzie stały wzrost może nie znacznie zwiększyć wydajność. |
| Zwiększ liczbę do   | Utworzyć wiele wystąpień maszyn wirtualnych są wymagane do uzyskania żądanej maksymalną ilość.                                                            |
| Zmniejsz liczbę do   | Stałej liczby wystąpień maszyny Wirtualnej do usunięcia. Przydatne w zestawy skalowania o mniejszej liczby maszyn wirtualnych.                                           |
| Zmniejsz wartość procentową o | Zmniejszanie procentowych wystąpień maszyn wirtualnych. Dobra na większą skalę Ustawia, gdzie stały wzrost nie mogą znacznie obniżyć zużycia zasobów i kosztów. |
| Zmniejsz liczbę do   | Usunięcie wielu wystąpień maszyny Wirtualnej są wymagane do uzyskania wymaganej ilości minimalnej.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Metryki maszyny Wirtualnej gościa z rozszerzeniem Diagnostyka Azure
Rozszerzenie diagnostyki Azure to agent uruchamiający wewnątrz wystąpienia maszyny Wirtualnej. Agent monitoruje i zapisuje metryki wydajności do magazynu Azure. Te metryki wydajności zawierają bardziej szczegółowe informacje dotyczące stanu maszyny wirtualnej, takie jak *AverageReadTime* dysków lub *PercentIdleTime* dla procesora CPU. Można utworzyć reguły automatycznego skalowania oparte na bardziej szczegółowe świadomości wydajność maszyny Wirtualnej, a nie tylko procent zużycia pamięci lub użycia procesora CPU.

Próbę użycia rozszerzenia diagnostycznych platformy Azure, należy utworzyć konta magazynu platformy Azure dla wystąpień maszyn wirtualnych, zainstaluj agenta platformy Azure diagnostics, a następnie skonfigurować maszyny wirtualne do liczników wydajności konkretnego strumienia z kontem magazynu.

Aby uzyskać więcej informacji, zobacz artykuły o tym, jak włączyć rozszerzenie diagnostyki platformy Azure na [maszynie wirtualnej z systemem Linux](../virtual-machines/linux/diagnostic-extension.md) lub [maszynie wirtualnej z systemem Windows](../virtual-machines/windows/ps-extensions-diagnostics.md).


## <a name="application-level-metrics-with-app-insights"></a>Metryki poziomu aplikacji z informacjami dotyczącymi aplikacji
Aby uzyskać lepszą widoczność w celu wydajności aplikacji, można użyć usługi Application Insights. Zainstalowania pakietu małych Instrumentacji w aplikacji, który monitoruje aplikację, a następnie wysyła dane telemetryczne do platformy Azure. Można monitorować metryki, takie jak czas odpowiedzi aplikacji, wydajność obciążenia strony, oraz liczby sesji. Te metryki aplikacji może służyć do tworzenia reguł skalowania automatycznego na poziomie szczegółowym i osadzone, jak wyzwolenia reguły oparte na przydatnych wyników analiz, które mogą mieć wpływ na wrażenia użytkowników.

Aby uzyskać więcej informacji o usłudze Application Insights, zobacz [Co to jest usługa Application Insights](../application-insights/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Zaplanowane skalowania automatycznego
Można również tworzyć reguły automatycznego skalowania, oparte na harmonogramów. Te zasady oparte na harmonogramie umożliwiają automatycznie skali liczba wystąpień maszyn wirtualnych na stałych razy. Na podstawie reguł w aplikacji przed wyzwalacza reguły automatycznego skalowania może być negatywny wpływ na wydajność i obsługi administracyjnej nowych wystąpień maszyny Wirtualnej. Jeśli przewidujesz takie żądanie, dodatkowe wystąpienia maszyny Wirtualnej są udostępnione i gotowe do dodatkowych wykorzystania i zastosowania popyt.

Poniższe przykłady przedstawiono scenariusze, które mogą korzystać stosowania reguły oparte na harmonogramie skalowania automatycznego:

- Automatyczne skalowanie w poziomie liczba wystąpień maszyn wirtualnych na początku dnia roboczego, gdy żądanie klienta zwiększa. Na koniec dnia roboczego automatycznie skalować liczbę wystąpień maszyny Wirtualnej, aby zminimalizować koszty zasobu noc, gdy brakuje użycia aplikacji.
- Jeśli dział używa aplikacji intensywnie w niektórych części miesiąc lub obrachunkowym cykl, automatycznie skalować liczbę wystąpień maszyny Wirtualnej, aby zmieścił się w ich dodatkowe wymagania.
- W przypadku zdarzeń marketing, podwyższanie poziomu lub sprzedaż dni wolnych program może automatycznie skalować liczbę wystąpień maszyn wirtualnych przed przewidywanego popyt. 


## <a name="next-steps"></a>Kolejne kroki
Można utworzyć reguły automatycznego skalowania, które metryki oparta na hoście z jednej z następujących narzędzi:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Interfejs wiersza polecenia platformy Azure 2.0](tutorial-autoscale-cli.md)
- [Szablonu Azure](tutorial-autoscale-template.md)

To omówienie szczegółowe na temat skalowania w poziomie i zwiększyć lub zmniejszyć za pomocą reguł skalowania automatycznego *numer* wystąpień maszyn wirtualnych w skali sieci ustawiony. Możliwe jest także skalowanie w pionie Aby zwiększyć lub zmniejszyć wystąpienia maszyny Wirtualnej *rozmiar*. Aby uzyskać więcej informacji, zobacz [pionowy automatycznie skalowana za pomocą zestawów skali maszyny wirtualnej](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Aby uzyskać informacje na temat zarządzania wystąpień maszyn wirtualnych, zobacz [ustawia Zarządzaj skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Aby dowiedzieć się, jak generować alerty, kiedy Twoje skalowania automatycznego zasady wyzwalacza, zobacz [użyć akcji skalowania automatycznego do wysyłania wiadomości e-mail i elementu webhook powiadomień o alertach w monitorze Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Możesz również [dzienników inspekcji używany do wysyłania wiadomości e-mail i elementu webhook powiadomień o alertach w monitorze Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).