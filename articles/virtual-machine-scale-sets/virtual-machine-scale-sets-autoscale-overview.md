---
title: Omówienie automatycznego skalowania za pomocą zestawów skalowania maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o różnych sposobach może automatycznie skalować Azure zestawu skalowania maszyn wirtualnych na podstawie wydajności lub zgodnie z ustalonym harmonogramem
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 610f3073594f73f04a68865593be6bfb4188d4f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60883674"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Ustawia Omówienie automatycznego skalowania przy użyciu skali maszyny wirtualnej platformy Azure
Zestaw skalowania maszyn wirtualnych platformy Azure może automatycznie zwiększyć lub zmniejszyć liczbę wystąpień maszyn wirtualnych, na których działa Twoja aplikacja. To zachowanie automatycznych i elastycznych zmniejsza koszty zarządzania do monitorowania i optymalizowania wydajności aplikacji. Możesz utworzyć zasady, które określają akceptowalny poziom wydajności dla pozytywnych komfort. Po spełnieniu tych zdefiniowanych progów, reguły skalowania automatycznego podjąć działania w celu dostosowania pojemność zestawu skalowania. Można także zaplanować zdarzeń, aby automatycznie zwiększać lub zmniejszyć pojemność zestawu skalowania o stałej razy. Ten artykuł zawiera omówienie metryk wydajności, które są dostępne i jakie akcje skalowania automatycznego można wykonywać.


## <a name="benefits-of-autoscale"></a>Korzyści, automatycznego skalowania
Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawie skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania.

Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Możesz kontrolować jakie metryki mają być monitorowane, na przykład procesora CPU lub pamięci, jak długo obciążenie aplikacji musi przekraczać wartość progową i ustaw liczbę wystąpień maszyn wirtualnych, aby dodać do skalowania.

Wieczorami lub w weekendy zapotrzebowanie aplikacji może być mniejsze. Jeśli spadek obciążenia ma cechy stałego trendu w danym okresie, można skonfigurować reguły skalowania automatycznego umożliwiające zmniejszenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Akcja skalowania w pionie ogranicza koszt używania zestawu skalowania, ponieważ jest uruchomionych tylko tyle wystąpień, ile jest wymaganych do zaspokojenia bieżącego zapotrzebowania.


## <a name="use-host-based-metrics"></a>Przy użyciu metryk opartych na hoście
Reguły automatycznego skalowania można utworzyć tej metryki hosta wbudowana dostępna z wystąpień maszyn wirtualnych. Metryki hosta zapewniają wgląd w wydajność wystąpień maszyn wirtualnych w zestawie bez konieczności instalowania lub konfigurowania dodatkowych agentów i zbierania danych skalowania. Reguły skalowania automatycznego, które używają tych metryk można skalować w poziomie lub w liczbie wystąpień maszyn wirtualnych w odpowiedzi na obciążenie procesora CPU, zapotrzebowanie na pamięć lub dostępu do dysku.

Reguły automatycznego skalowania, które korzystają z metryk opartych na hoście, można utworzyć za pomocą jednego z następujących narzędzi:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Interfejs wiersza polecenia platformy Azure](tutorial-autoscale-cli.md)
- [Szablon platformy Azure](tutorial-autoscale-template.md)

Aby utworzyć reguły automatycznego skalowania, które używają bardziej szczegółowe metryki wydajności, możesz [zainstalować i skonfigurować rozszerzenie diagnostyki platformy Azure](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) na wystąpieniach maszyn wirtualnych lub [konfigurowania używania aplikacji usługi App Insights](#application-level-metrics-with-app-insights).

Reguły skalowania automatycznego, korzystających z metryk opartych na hoście metryki maszyny Wirtualnej gościa z rozszerzenie diagnostyki platformy Azure i usługi App Insights można użyć następujących ustawień konfiguracji.

### <a name="metric-sources"></a>Metryki źródeł
Reguły automatycznego skalowania, można użyć metryki z jednego z następujących źródeł:

| Źródło metryki        | Przypadek użycia                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Bieżący zestaw skalowania    | Aby uzyskać metryki oparte na hoście, które nie wymagają dodatkowych agentów, które mają być zainstalowane lub skonfigurowane.                                  |
| Konto magazynu      | Rozszerzenie diagnostyki platformy Azure zapisuje metryki wydajności do usługi Azure storage, która jest następnie używane do wyzwalania reguły automatycznego skalowania. |
| Kolejki usługi Service Bus    | Aplikacja lub inne składniki mogą przesyłać komunikaty w kolejce usługi Azure Service Bus z regułami wyzwalacza.                   |
| Application Insights | Pakiet instrumentacji, zainstalowane w Twojej aplikacji, strumienie metryk bezpośrednio z aplikacji.                         |


### <a name="autoscale-rule-criteria"></a>Kryteria reguły skalowania automatycznego
Następujące metryki oparte na hoście są dostępne do użycia podczas tworzenia reguł automatycznego skalowania. Jeśli używasz rozszerzenie diagnostyki platformy Azure lub usługi App Insights, należy zdefiniować jaką metrykę do monitorowania i za pomocą reguł automatycznego skalowania.

| Nazwa metryki               |
|---------------------------|
| Procentowe użycie procesora CPU            |
| Sieć — wejście                |
| Sieć — wyjście               |
| Bajty odczytane z dysku           |
| Bajty zapisane na dysku          |
| Dysku, operacje odczytu/s  |
| Operacje zapisu dysku/s |
| Pozostałe środki na korzystanie z procesora CPU     |
| Środki na procesory CPU wykorzystany      |

Podczas tworzenia reguł skalowania automatycznego w celu monitorowania danej metryki reguły Przyjrzyj się jedną z następujących czynności agregacji metryk:

| Typ agregacji |
|------------------|
| Średnia          |
| Minimalne          |
| Maksimum          |
| Łącznie            |
| Ostatnia             |
| Licznik            |

Reguły skalowania automatycznego następnie są wyzwalane, gdy metryki są porównywane z określoną wartość progową przy użyciu jednego z następujących operatorów:

| Operator                 |
|--------------------------|
| Większe niż             |
| Większe niż lub równe |
| Mniejsze niż                |
| Mniejsze niż lub równe    |
| Równa się                 |
| Nie równa się             |


### <a name="actions-when-rules-trigger"></a>Akcje w przypadku wyzwolenia reguły
Gdy wyzwolenie reguły skalowania automatycznego, zestaw skalowania może automatycznie skalować w jednym z następujących sposobów:

| Operacja skalowania     | Przypadek użycia                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Zwiększ liczbę o   | Stałej liczby wystąpień maszyn wirtualnych do utworzenia. To przydatne w zestawach skalowania z mniejszej liczby maszyn wirtualnych.                                           |
| Zwiększ wartość procentową o | Na podstawie wzrostu liczby wystąpień maszyn wirtualnych. Dobre dla większych Ustawia, gdzie stały wzrost może nie znacznie poprawić wydajność. |
| Zwiększ liczbę do   | Utworzyć wiele wystąpień maszyn wirtualnych są wymagane do uzyskania żądanej maksymalnej wysokości.                                                            |
| Zmniejsz liczbę o   | Stałej liczby wystąpień maszyn wirtualnych do usunięcia. To przydatne w zestawach skalowania z mniejszej liczby maszyn wirtualnych.                                           |
| Zmniejsz wartość procentową o | Na podstawie spadku wystąpień maszyn wirtualnych. Dobre dla większych Ustawia, gdzie stały wzrost nie mogą znacznie obniżyć koszty i użycia zasobów. |
| Zmniejsz liczbę do   | Usunąć, ponieważ wiele wystąpień maszyn wirtualnych są wymagane do uzyskania wymaganej ilości minimalnej.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Metryki maszyny Wirtualnej gościa za pomocą rozszerzenie diagnostyki platformy Azure
Rozszerzenie diagnostyki platformy Azure jest agenta, który działa wewnątrz wystąpienia maszyny Wirtualnej. Agent monitoruje i zapisuje metryki wydajności do usługi Azure storage. Te metryki wydajności zawierają bardziej szczegółowe informacje o stanie maszyny wirtualnej, takie jak *AverageReadTime* dysków lub *PercentIdleTime* procesora CPU. Można utworzyć reguły skalowania automatycznego na podstawie bardziej szczegółowe rozpoznawania wydajności maszyny Wirtualnej, nie tylko wartości procentowej użycia procesora CPU użycia lub pamięci.

Do użycia rozszerzenie diagnostyki platformy Azure, należy utworzyć konta usługi Azure storage dla wystąpień maszyn wirtualnych, zainstaluj agenta funkcji Diagnostyka Azure, a następnie skonfigurować maszyny wirtualne do strumienia specyficzne liczniki wydajności do konta magazynu.

Aby uzyskać więcej informacji, zobacz artykuły o tym, jak włączyć rozszerzenie diagnostyki platformy Azure na [maszynie wirtualnej z systemem Linux](../virtual-machines/extensions/diagnostics-linux.md) lub [maszynie wirtualnej z systemem Windows](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Metryki poziomu aplikacji za pomocą usługi App Insights
Aby uzyskać więcej wgląd w wydajność aplikacji, można użyć usługi Application Insights. Zainstalowanie mały pakiet Instrumentacji w swojej aplikacji, która monitoruje aplikację i wysyła dane telemetryczne do platformy Azure. Można monitorować metryki, takie jak czas odpowiedzi aplikacji, wydajność ładowania strony, a liczba sesji. Te metryki aplikacji może służyć do tworzenia reguł skalowania automatycznego na poziomie szczegółowym i osadzone, jak wyzwolić reguły oparte na uzyskiwanie przydatnych wyników analiz, które mogą mieć wpływ na wrażenia użytkowników.

Aby uzyskać więcej informacji o usłudze Application Insights, zobacz [Co to jest usługa Application Insights](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Automatyczne skalowanie według harmonogramu
Można również tworzyć reguły automatycznego skalowania, na podstawie zgodnie z harmonogramami. Te reguły na podstawie harmonogramu pozwala automatycznie skalować liczbę wystąpień maszyn wirtualnych w stałej razy. Za pomocą reguł na podstawie wydajności może być negatywny wpływ na wydajność aplikacji przed wyzwalania reguły automatycznego skalowania i udostępnieniu nowych wystąpień maszyn wirtualnych. Jeśli przewidujesz takie żądanie, kolejnych wystąpień maszyn wirtualnych są aprowizowane i gotowy na dodatkowe użycie i aplikacji popyt.

Poniższe przykłady są scenariusze, które można przeznaczyć na korzystanie z automatycznego na podstawie harmonogramu:

- Automatycznie skalować liczbę wystąpień maszyn wirtualnych, na początku dnia roboczego, gdy zwiększa zapotrzebowanie klientów. Na koniec dnia roboczego automatyczne skalowanie liczby wystąpień maszyn wirtualnych, aby zminimalizować koszty zasobów przez noc, gdy użycie aplikacji jest za mało.
- Jeśli dział używa aplikacji intensywnie w niektórych części miesiąca lub obrachunkowy cykl, automatyczne skalowanie liczby wystąpień maszyn wirtualnych, aby uwzględnić jego potrzebom związanym z dodatkowych.
- Jeśli marketingowe zdarzeń, podwyższanie poziomu lub sprzedaży dniem wolnym od pracy, możesz automatycznie skalować liczbę wystąpień maszyn wirtualnych, które wcześniej przewidywany popyt. 


## <a name="next-steps"></a>Kolejne kroki
Można utworzyć reguły automatycznego skalowania, korzystających z metryk opartych na hoście przy użyciu jednego z następujących narzędzi:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Interfejs wiersza polecenia platformy Azure](tutorial-autoscale-cli.md)
- [Szablon platformy Azure](tutorial-autoscale-template.md)

W tym omówieniu szczegółowe na temat reguł automatycznego skalowania umożliwia skalowanie w poziomie i zwiększyć lub zmniejszyć *numer* wystąpień maszyn wirtualnych w zestawie skalowania jest ustawiony. Możesz również skalować w pionie do zwiększania lub zmniejszania wystąpienia maszyny Wirtualnej *rozmiar*. Aby uzyskać więcej informacji, zobacz [pionowe skalowania automatycznego za pomocą zestawów skalowania maszyn wirtualnych](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Aby uzyskać informacje na temat zarządzania wystąpień maszyn wirtualnych, zobacz [zestawów skalowania maszyn wirtualnych zarządzania za pomocą programu Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Aby dowiedzieć się, jak generować alerty, gdy wyzwalacz reguł z automatycznego, zobacz [użyć akcji skalowania automatycznego, aby wysyłać wiadomości e-mail i elementy webhook powiadomienia o alertach w usłudze Azure Monitor](../azure-monitor/platform/autoscale-webhook-email.md). Możesz również [korzystaj z dzienników inspekcji do wysyłania wiadomości e-mail i elementy webhook powiadomień o alertach w usłudze Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
