---
title: Przegląd alertów klasycznych na platformie Microsoft Azure i usługi Azure Monitor
description: Alerty umożliwiają monitorowanie zdarzeń, dzienniki lub metryk zasobów platformy Azure i otrzymywać powiadomienia, gdy spełniony jest warunek, który określisz.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: a0abcbdaa7e998413efb717be6e0addc5607ec5c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114015"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Co to są alertów klasycznych na platformie Microsoft Azure?

> [!NOTE]
> W tym artykule opisano sposób tworzenia starsze, klasyczne alertów dotyczących metryk. Platforma Azure obsługuje teraz Monitor [alertów dotyczących metryk czasu nowsze, prawie rzeczywistym, a nowe środowisko alertów](monitoring-overview-unified-alerts.md). 
>

Za pomocą alertów, można skonfigurować warunki danych i otrzymaj powiadomienie, gdy warunki pasują do monitorowania najnowszych danych.


## <a name="alerts-on-azure-monitor-data"></a>Alerty dotyczące danych w usłudze Azure Monitor
Istnieją dwa typy alertów klasycznych: alertów dotyczących metryk i alertów dziennika aktywności.

* **Klasyczne alertów dotyczących metryk**: uruchamia ten alert, gdy wartość określonej metryki przekracza wartość progową, który przypiszesz. Alert generuje powiadomienie, gdy it na "aktywny" (po przekroczeniu progu, i jest spełniony warunek alertu). Generuje również alert, kiedy zostanie on "rozwiązany" (po przekroczeniu progu ponownie i warunek przestaje być spełniany). 

* **Alerty dziennika aktywności klasycznego**: tym przesyłania strumieniowego alertu dziennika wyzwalane podczas generowania zdarzenia dziennika aktywności, dopasowania filtrowania kryteria, które zostały przypisane. Te alerty mają tylko jeden stan "aktywowano", ponieważ aparat alertów po prostu dotyczy kryteria filtrowania wszelkie nowe zdarzenie. Te alerty można otrzymywać powiadomienia, gdy występuje nowego zdarzenia usługi Service Health lub po użytkownik lub aplikacja wykonuje operację w ramach subskrypcji takich jak "Usuń maszynę wirtualną."

Aby odbierać danych dzienników diagnostycznych dostępnych dostępnych za pośrednictwem usługi Azure Monitor, kierować dane do usługi Log Analytics (dawniej pakiet Operations Management Suite) i użyj alertu zapytań usługi Log Analytics. Zaloguj korzysta teraz Analytics [nowe alerty metoda](monitoring-overview-unified-alerts.md). 

Poniższy diagram zawiera podsumowanie źródeł danych w usłudze Azure Monitor i sugeruje się, jak może generować alerty wyłączyć te dane.

![Wyjaśniono alertów](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Taksonomia usługi Azure Monitor alerty (klasyczne)
Platforma Azure stosuje następujące warunki do opisania alertów klasycznych i ich funkcje:
* **Alert**: definicja kryteria (jednego lub więcej reguł, jak i warunki), która staje się aktywowany, gdy spełnione.
* **Aktywne**: stan, który występuje po spełnieniu kryteriów, które jest definiowany przez klasyczny alert.
* **Rozwiązane**: stan, który występuje, gdy kryteria, które jest definiowany przez klasyczny alert nie jest już spełniany po wcześniej spełnione.
* **Powiadomienie**: akcję, która jest wykonywana na podstawie, gdy klasyczny alert stanie się aktywny.
* **Akcja**: wywołań, które są wysyłane do odbiorcy powiadomienia (na przykład wiadomości e-mail lub post na adres URL elementu webhook). Powiadomienia zwykle może wyzwolić wiele akcji.

## <a name="how-do-i-receive-notifications-from-an-azure-monitor-classic-alert"></a>Jak otrzymywać powiadomienia z poziomu alertu klasycznej usługi Azure Monitor?
W przeszłości alertów platformy Azure z różnymi usługami używać własnych metod wbudowanych powiadomień. 

Teraz usługi Azure Monitor udostępnia grupowanie wielokrotnego użytku powiadomienia o nazwie *grup akcji*. Grupy akcji określić zbiór odbiorców dla powiadomienia. Po aktywowaniu alertu, który odwołuje się do grupy akcji, wszystkich odbiorniki odbierania powiadomienia. Ta funkcja pozwala na wielokrotne używanie Grupa odbiorców (na przykład listy odtwarzania na wywołanie) w odniesieniu do obiektów wiele alertów. Grupy akcji powiadomienia są obsługiwane za pomocą różnych metod. Metody te mogą obejmować publikowanie do adresu URL elementu webhook, wysyłanie wiadomości e-mail, wiadomości SMS i wiele innych działań. Aby uzyskać więcej informacji, zobacz [tworzenie grup akcji w witrynie Azure portal i zarządzanie nimi](monitoring-action-groups.md). 

Starsze alertów dziennika aktywności klasycznego korzystanie z grup akcji.

Jednak nie należy używać starszych alertów metryk grupy akcji. Zamiast tego można skonfigurować następujące akcje: 
* Wysyłanie powiadomień e-mail do administratora usługi współadministratorów, lub adresy e-mail dodatkowych, które określisz.
* Wywołaj element webhook, co pozwala na uruchamianie automatyzacji dodatkowej akcji.

Elementy Webhook Włącz automatyzację i korygowania, na przykład przy użyciu następujących usług:
- Element Runbook usługi Azure Automation
- Azure Functions
- Aplikacji logiki platformy Azure
- Usługa innej firmy

## <a name="next-steps"></a>Kolejne kroki
Uzyskać informacje na temat reguł alertów oraz jak je skonfigurować przy użyciu poniższej dokumentacji:

* Dowiedz się więcej o [metryki](monitoring-overview-metrics.md)
* Konfigurowanie [metryki alertów klasycznych przy użyciu witryny Azure portal](insights-alerts-portal.md)
* Konfigurowanie [metryki alertów klasycznych przy użyciu programu PowerShell](insights-alerts-powershell.md)
* Konfigurowanie [metryki alertów klasycznych przy użyciu wiersza polecenia platformy Azure](insights-alerts-command-line-interface.md)
* Konfigurowanie [metryki alertów klasycznych przy użyciu interfejsu API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Dowiedz się więcej o [dzienników aktywności](monitoring-overview-activity-logs.md)
* Konfigurowanie [alertów dziennika aktywności przy użyciu witryny Azure portal](monitoring-activity-log-alerts.md)
* Konfigurowanie [alertów dziennika aktywności przy użyciu usługi Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Przegląd [schemat elementów webhook alertu dziennika aktywności](monitoring-activity-log-alerts-webhook.md)
* Dowiedz się więcej o [grupy akcji](monitoring-action-groups.md)
* Konfigurowanie [nowszych alertów](monitor-alerts-unified-usage.md)
