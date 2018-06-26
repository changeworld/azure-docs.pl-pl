---
title: Rozszerzona alerty (Kopiuj) Analiza dzienników Azure alerty — omówienie
description: Omówienie procesu, aby skopiować alerty z Log Analytics w portalu OMS do alertów Azure z zawiera szczegóły dotyczące adresowania typowych problemów klienta.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 6484142eafa8388117c1e96ab31eefeab188e488
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750276"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Rozszerzenie alerty usługi Analiza dzienników Azure alertów
Niedawna Azure Log Analytics uwzględnione własną funkcję alertów, która może proaktywnie powiadamia użytkownika o warunków na podstawie danych analizy dzienników. Możesz zarządzać reguły alertów w portalu programu Microsoft Operations Management Suite. Nowe środowisko alertów ma teraz zintegrowane alertów w różnych usługach w Microsoft Azure. To ustawienie jest dostępne jako **alerty** w obszarze Monitor Azure w portalu Azure i obsługuje alerty z działania dzienniki, metryki oraz Dzienniki z analizy dzienników i Azure Application Insights. 

## <a name="benefits-of-extending-your-alerts"></a>Korzyści z rozszerzenia alerty
Istnieje kilka zalet tworzenie i Zarządzanie alertami w portalu Azure, takich jak:

- W odróżnieniu od w portalu usługi Operations Management Suite, gdzie tylko 250 alerty można można tworzyć i wyświetlać, alerty Azure ma bez tych ograniczeń.
- Azure alertów można zarządzać, wyliczyć i wyświetlić wszystkie typy alertów. Wcześniej można tylko tym alertów analizy dzienników.
- Można ograniczyć dostęp do użytkowników tylko monitorowanie i alerty, za pomocą [roli Azure Monitor](monitoring-roles-permissions-security.md).
- Azure alertów, można użyć [grupy akcji](monitoring-action-groups.md). Dzięki temu można mieć więcej niż jedną akcję dla każdego alertu. Można programu SMS, wysyłania połączeń głosowych, wywołaj element runbook usługi Automatyzacja Azure wywołania elementu webhook i skonfigurować łącznik zarządzania usługi IT (zarządzanie, usługami IT —). 

## <a name="process-of-extending-your-alerts"></a>Proces rozszerzania alerty
Proces przechodzenia alerty z analizy dzienników do Azure alertów nie wymaga zmian w definicji alertu, w zapytania lub w konfiguracji w dowolny sposób. Zmiana jest wprowadzana wyłącznie wymagane na platformie Azure, wykonanie wszystkich akcji przy użyciu grupy akcji. Jeśli akcja grup są już skojarzone z alertu, zostaną one dołączone podczas rozszerzania na platformie Azure.

> [!NOTE]
> Microsoft spowoduje automatyczne przedłużenie alertach tworzonych w Analiza dzienników Azure alerty, począwszy od na 14 maja 2018 cykl aż do zakończenia. Jeśli masz problemy z tworzeniem [grupy akcji](monitoring-action-groups.md), użyj [następujące kroki korygujące](monitoring-alerts-extend-tool.md#troubleshooting) można pobrać grupy akcji utworzone automatycznie. Te kroki służą do 5 lipca 2018. 
> 

Podczas planowania alerty w obszarze roboczym analizy dzienników na platformie Azure, użytkownik nadal będzie działać, a nie w się, że wszelkie naruszenia sposób konfiguracji. Podczas planowania alerty może tymczasowo niedostępna do modyfikacji, ale można kontynuować tworzenia nowych alertów Azure w tym czasie. Jeśli próba edycji lub tworzenie alertów przy użyciu portalu usługi Operations Management Suite, masz opcję, aby kontynuować tworzenie je z obszaru roboczego analizy dzienników. Można również utworzyć je z alertów Azure w portalu Azure.

 ![Zrzut ekranu przedstawiający opcja do tworzenia alertów z usługi Analiza dzienników Azure alertów](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Rozszerzanie alertów z usługi Analiza dzienników Azure nie wiąże się z opłatami do Twojego konta. Korzystanie z alertów Azure dla analizy dzienników alerty na podstawie zapytania nie jest rozliczany używanego w ramach limitów i warunki określone w [Monitor Azure cennik zasad](https://azure.microsoft.com/pricing/details/monitor/).  


### <a name="how-to-extend-your-alerts-voluntarily"></a>Jak rozszerzyć dobrowolnie alerty
Rozszerzenie alerty Alerty Azure, można użyć Kreatora dostępne w portalu usługi Operations Management Suite, lub należy więc programowo przy użyciu interfejsu API. Aby uzyskać więcej informacji, zobacz [rozszerzanie alerty na platformie Azure przy użyciu portalu usługi Operations Management Suite i interfejsu API](monitoring-alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>Występuje po rozszerzeniu alerty
Alerty zostały rozszerzone do alertów Azure, nadal będą znajdować się w portalu zarządzania usługi Operations Management Suite nie inaczej niż wcześniej.

![Zrzut ekranu Operations Management Suite portal, za pomocą alertów](./media/monitor-alerts-extend/PostExtendList.png)

Podczas próby edycji istniejącego alertu lub Utwórz nowy alert w portalu usługi Operations Management Suite, automatycznie są przekierowywane do alertów Azure.  

> [!NOTE]
> Upewnij się, że uprawnienia przypisane do osób, które należy dodać lub edytować alerty są poprawnie przypisane na platformie Azure. Aby zrozumieć, jakie należy przyznać uprawnienia, zobacz [uprawnienia za pomocą monitora Azure i alertów](monitoring-roles-permissions-security.md).  
> 

Możesz tworzyć alerty z [API analizy dziennika](../log-analytics/log-analytics-api-alerts.md) i [dziennika analizy zasobów szablonu](../monitoring/monitoring-solutions-resources-searches-alerts.md). Należy uwzględnić grupy akcji po wykonaniu.

## <a name="next-steps"></a>Kolejne kroki

* Więcej informacji na temat narzędzia do [inicjować rozszerzanie alerty z analizy dzienników na platformie Azure](monitoring-alerts-extend-tool.md).
* Dowiedz się więcej o [zgłaszać alerty Azure](monitoring-overview-unified-alerts.md).
* Dowiedz się, jak utworzyć [rejestrowania alertów w alertach Azure](monitor-alerts-unified-log.md).
