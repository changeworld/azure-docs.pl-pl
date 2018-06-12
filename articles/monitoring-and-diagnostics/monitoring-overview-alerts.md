---
title: Omówienie klasycznego alertów w monitorze Azure i Microsoft Azure
description: Alerty umożliwiają monitorowanie metryki zasobów platformy Azure, zdarzeń i dzienniki i otrzymać powiadomienie, gdy spełniony jest warunek, który określisz.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 89c3975a1212aa991e42e0cce4fe5521b53bd373
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263674"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Co to są klasycznego alerty na platformie Microsoft Azure?

> [!NOTE]
> W tym artykule opisano sposób tworzenia starsze klasycznego alerty metryki. Azure obsługuje teraz Monitor [nowszej alerty metryki czasie niemal rzeczywistym i nowego środowiska alerty](monitoring-overview-unified-alerts.md). 
>

Alerty pozwalają na konfigurowanie warunków nad danymi i stają się powiadomienie, gdy warunki pasują do monitorowania najnowszych danych.


## <a name="alerts-on-azure-monitor-data"></a>Alerty dotyczące danych monitora Azure
Istnieją dwa typy klasycznego alertów niedostępny — alerty metryki i alertów dotyczących działań w dzienniku.

* **Klasycznym alerty metryki** — ten alert jest wyzwalane po wartości progowej, którą należy przypisać przecina wartość określonej metryki. Ten alert generuje powiadomienie, gdy alert jest "aktywny" (po przekroczeniu progu i spełnieniu warunku alertu), a także gdy "Problemu" (po przekroczeniu progu ponownie i nie jest spełniony warunek). Dla nowszej metryki alertów są wymienione poniżej.

* **Alerty dziennika aktywności klasycznego** -przesyłania strumieniowego alertu dziennika, który uaktywnia jest generowane zdarzenie dziennika aktywności, że dopasowań filtrować kryteria, które zostały przypisane. Te alerty mają tylko jeden stan "Aktywować", ponieważ aparat alertów po prostu dotyczy kryteria filtrowania wszelkie nowe zdarzenie. Te alerty umożliwiają stają się powiadomienie po wystąpieniu nowego zdarzenia kondycji usługi lub gdy użytkownik lub aplikacja wykonuje operację w ramach subskrypcji, na przykład "Usuń maszynę wirtualną".

W przypadku dzienników diagnostycznych danych dostępne za pośrednictwem Monitora Azure Sugerujemy routingu danych do analizy dzienników (OMS wcześniej) i przy użyciu alertu zapytania analizy dzienników. Dziennika używa teraz Analytics [nowe alerty — metoda](monitoring-overview-unified-alerts.md) 

Poniższy diagram przedstawia źródeł danych w Azure monitora i, koncepcyjnym, jak może generować alerty znajdujące się na nich danych.

![Opis alertów](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Taksonomii alerty monitora Azure (klasyczne)
Azure używa następujących elementów do opisywania klasycznego alertów i ich funkcje:
* **Alert** -definicję kryteria (jeden lub więcej reguł lub warunków), która zostanie aktywowany, gdy spełnione.
* **Aktywne** — stan, gdy są spełnione kryteria zdefiniowane przez klasycznego alertu.
* **Rozwiązane** — stan, gdy kryteria zdefiniowane przez klasycznego alertu nie jest już spełniany po wcześniej spełnione.
* **Powiadomienie** — akcję wykonywaną na podstawie wylogowuje klasycznego alertu, aktywację.
* **Akcja** -wywołań wysyłane do odbiorcy powiadomienia (na przykład wysyłanie wiadomości e-mail adres lub publikowanie do adresu URL elementu webhook). Powiadomienia zwykle można wyzwolić wiele akcji.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Sposób otrzymywania powiadomień z poziomu klasycznego alertu Azure Monitor?
W przeszłości Azure alertów z różnych usług używać metod wbudowanych powiadomień. 

Teraz Azure Monitor oferuje powiadomienie wielokrotnego użytku, grupowanie wywołane *grupy akcji*. Grupy akcji określ zestaw odbiorcy powiadomienia o uprawnia do zawsze, gdy alert jest aktywny odwołujących się do grupy akcji wszystkich odbiorcy powiadomienia. Dzięki temu można ponownie użyć Grupa odbiorców (na przykład na wywołania odtwarzania listy) przez wiele obiektów alertu. Grupy akcji powiadomienia są obsługiwane przez publikowanie do adresu URL elementu webhook oprócz adresów e-mail, numerów programu SMS i wiele innych akcji.  Aby uzyskać więcej informacji, zobacz [grupy akcji](monitoring-action-groups.md). 

Starsze klasycznego alerty dziennik aktywności przy użyciu grup działań.

Jednak starsza alerty metryki nie należy używać grup akcji. Zamiast tego można skonfigurować następujące akcje: 
* Wysyłania powiadomień e-mail do administratora usługi, współadministratorów lub adresy e-mail dodatkowych, które określisz.
* Wywołaj element webhook, co pozwala na uruchamianie automatyzacji dodatkowe akcje.

Elementów Webhook umożliwia automatyzację i korygowania, na przykład przy użyciu:
    - Element Runbook usługi Azure Automation
    - Funkcja platformy Azure
    - Aplikacja logiki platformy Azure
    - usługi innej firmy

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
* Dowiedz się więcej o [grupy akcji](monitoring-action-groups.md)
* Skonfiguruj [nowszej alertów](monitor-alerts-unified-usage.md)
