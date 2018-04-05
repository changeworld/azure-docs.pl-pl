---
title: Omówienie klasycznego alertów w monitorze Azure i Microsoft Azure | Dokumentacja firmy Microsoft
description: Alerty umożliwiają monitorowanie metryki zasobów platformy Azure, zdarzeń i dzienniki i otrzymać powiadomienie, gdy spełniony jest warunek, który określisz.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: 06ba05f71cf1f696033099c04448526a0421ad42
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Co to są klasycznego alerty na platformie Microsoft Azure?

> [!NOTE]
> W tym artykule opisano sposób tworzenia starsze klasycznego alerty metryki. Azure obsługuje teraz Monitor [nowszej czasie niemal rzeczywistym metryki alerty.](monitoring-overview-unified-alerts.md)
>

W tym artykule opisano różne źródła alertów na platformie Microsoft Azure, jakie są cele dla alertów, ich zalety oraz sposobu wprowadzenie do korzystania z nich. W szczególności dotyczy alerty klasycznego Azure monitora. Alerty oferują metodą monitorowania na platformie Azure, która pozwala na konfigurowanie warunków nad danymi i stają się powiadomienie, gdy warunki pasują do monitorowania najnowszych danych.


## <a name="taxonomy-of-azure-monitor-classic-alerts"></a>Taksonomii alerty klasycznego Azure monitora
Azure używa następujących elementów do opisywania klasycznego alertów i ich funkcje:
* **Alert** -definicję kryteria (jeden lub więcej reguł lub warunków), która zostanie aktywowany, gdy spełnione.
* **Aktywne** — stan, gdy są spełnione kryteria zdefiniowane za pomocą klasycznego alertu.
* **Rozwiązane** — stan, gdy kryteria zdefiniowane za pomocą klasycznego alertu nie jest już spełniany po wcześniej spełnione.
* **Powiadomienie** — akcję wykonywaną na podstawie wylogowuje alert klasycznego aktywację.
* **Akcja** -wywołań wysyłane do odbiorcy powiadomienia (na przykład wysyłanie wiadomości e-mail adres lub publikowanie do adresu URL elementu webhook). Powiadomienia zwykle można wyzwolić wiele akcji.


## <a name="alerts-on-azure-monitor-data"></a>Alerty dotyczące danych monitora Azure
Istnieją trzy typy alertów znajdujące się na nich danych, dostępnej w sklepie Azure Monitor — alerty metryki, niemal w czasie rzeczywistym metryki alertów i dziennika aktywności alerty.

* **Klasycznym alerty metryki** — ten alert jest wyzwalane po wartości progowej, którą należy przypisać przecina wartość określonej metryki. Ten alert generuje powiadomienie, gdy alert jest "aktywny" (po przekroczeniu progu i spełnieniu warunku alertu), a także gdy "Problemu" (po przekroczeniu progu ponownie i nie jest spełniony warunek). Są to starsze alerty metryki. Dla nowszej metryki alertów są wymienione poniżej.

* **Metryki alertów w czasie rzeczywistym w pobliżu** (nowszej środowisko alertu) — są to nowszej generowania alertów metryki z ulepszonych funkcji w porównaniu do poprzednich metryki alertów. Te alerty można uruchomić z częstotliwością 1 min. One również obsługiwać monitorowanie wielu metryki (obecnie dwa).  Ten alert generuje powiadomienie, gdy alert jest "aktywny" (po przekroczeniu progów dla każdego metryki w tym samym czasie i spełnieniu warunku alertu), a także gdy "Problemu" (gdy co najmniej jedna Metryka przekracza wartość progową ponownie i warunku nie już spełniany).

* **Alerty dziennika aktywności klasycznego** -przesyłania strumieniowego alertu dziennika, który uaktywnia jest generowane zdarzenie dziennika aktywności, że dopasowań filtrować kryteria, które zostały przypisane. Te alerty mają tylko jeden stan "Aktywować", ponieważ aparat alertów po prostu dotyczy kryteria filtrowania wszelkie nowe zdarzenie. Te alerty umożliwiają stają się powiadomienie po wystąpieniu nowego zdarzenia kondycji usługi lub gdy użytkownik lub aplikacja wykonuje operację w ramach subskrypcji, na przykład "Usuń maszynę wirtualną".

W przypadku dzienników diagnostycznych danych dostępne za pośrednictwem Monitora Azure Sugerujemy routingu danych do analizy dziennika i przy użyciu alertu analizy dzienników. Poniższy diagram przedstawia źródeł danych w Azure monitora i, koncepcyjnym, jak może generować alerty znajdujące się na nich danych.

![Opis alertów](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-classic-alert"></a>Jak odbierać powiadomienie w klasycznym alert monitora Azure?
W przeszłości Azure alertów z różnych usług używać metod wbudowanych powiadomień. Idąc dalej, Azure Monitor oferuje grupowanie wielokrotnego użytku powiadomienia o nazwie grupy akcji. Grupy akcji określ zestaw odbiorcy powiadomienia — dowolną liczbę adresów e-mail, numerów (SMS) lub adresów URL elementu webhook — uprawnia do zawsze, gdy alert jest aktywny odwołujących się do grupy akcji wszystkich odbiorcy powiadomienia. Dzięki temu można ponownie użyć Grupa odbiorców (na przykład na wywołania odtwarzania listy) przez wiele obiektów alertu. Obecnie tylko alerty dziennik aktywności wykorzystać grupy akcji, ale pracy przy użyciu grup akcji, jak również kilka inne Azure typy alertów.

Grupy akcji powiadomienia są obsługiwane przez publikowanie do adresu URL elementu webhook oprócz adresy e-mail i SMS cyfry. Dzięki temu automatyzacji i korygowania, na przykład przy użyciu:
    - Element Runbook usługi Azure Automation
    - Azure — funkcja
    - Aplikacja logiki platformy Azure
    - usługi innej firmy

Niemal w czasie rzeczywistym Metryka alertów i dziennika aktywności alerty używać grup akcji.

Starsze alerty metryki dostępnych w ramach alertów (klasyczne) nie należy używać grup akcji. Na poszczególnych metryki alertu można skonfigurować powiadomienia do:
* Wysyłania powiadomień e-mail do administratora usługi, współadministratorów lub adresy e-mail dodatkowych, które określisz.
* Wywołaj element webhook, co pozwala na uruchamianie automatyzacji dodatkowe akcje.

## <a name="next-steps"></a>Kolejne kroki
Uzyskaj informacje o regułach alertów i konfigurowanie ich za pomocą:

* Dowiedz się więcej o [metryk](monitoring-overview-metrics.md)
* Skonfiguruj [klasycznego Metryka alertów za pośrednictwem portalu Azure](insights-alerts-portal.md)
* Skonfiguruj [klasycznego Metryka alerty w programie PowerShell](insights-alerts-powershell.md)
* Skonfiguruj [klasycznego Metryka alerty wiersza polecenia (CLI)](insights-alerts-command-line-interface.md)
* Skonfiguruj [klasycznego Metryka alerty monitora interfejsu API REST Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Dowiedz się więcej o [dziennik aktywności](monitoring-overview-activity-logs.md)
* Skonfiguruj [alerty dziennika aktywności za pośrednictwem portalu Azure](monitoring-activity-log-alerts.md)
* Skonfiguruj [alerty dziennika aktywności za pomocą Menedżera zasobów](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Przegląd [schemat alertu elementu webhook dziennika aktywności](monitoring-activity-log-alerts-webhook.md)
* Dowiedz się więcej o [nowszej Metryka alertów](monitoring-near-real-time-metric-alerts.md)
* Dowiedz się więcej o [powiadomień usługi](monitoring-service-notifications.md)
* Dowiedz się więcej o [grupy akcji](monitoring-action-groups.md)
* Skonfiguruj [alertów](monitor-alerts-unified-usage.md)
