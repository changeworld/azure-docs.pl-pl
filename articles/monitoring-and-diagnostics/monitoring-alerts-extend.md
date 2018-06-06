---
title: Rozszerzona alerty (Kopiuj) Analiza dzienników Azure alerty — omówienie | Dokumentacja firmy Microsoft
description: Omówienie procesu, aby skopiować alerty z Log Analytics w portalu OMS do alertów Azure z zawiera szczegóły dotyczące adresowania typowych problemów klienta.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: vinagara
ms.openlocfilehash: 296bdc6939ef8ab67656e5805cd603b50bce211e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763157"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Rozszerzenie alerty usługi Analiza dzienników Azure alertów
Niedawna Azure Log Analytics uwzględnione własną funkcję alertów, która może proaktywnie powiadamia użytkownika o warunków na podstawie danych analizy dzienników.  Wykonano zarządzania regułami alertów w [portal Microsoft operacji Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Nowe środowisko alertów ma teraz zintegrowane środowisko alertów w różnych usługach w Microsoft Azure. Nowe środowisko jest dostępna jako **alerty** w obszarze Monitor Azure w portalu Azure i obsługuje alerty z działania dzienniki, metryki oraz Dzienniki z usługi Application Insights i analizy dzienników. 

## <a name="benefits-of-extending-your-alerts"></a>Korzyści z rozszerzenia alerty
Istnieje kilka zalet tworzenie i Zarządzanie alertami w porcie Azure, takich jak:

- W odróżnieniu od w portalu OMS, gdzie można utworzyć i wyświetlić; tylko 250 alerty w alertach Azure to ograniczenie nie jest obecny
- Alertów Azure wszystkie typy alertów mogą być zarządzane, wyliczyć i wyświetlane; nie tylko analizy dzienników alertów, jak w przypadku wcześniej
- Kontrola dostępu umożliwia użytkownikom tylko monitorowanie i alerty, przy użyciu [roli Azure Monitor](monitoring-roles-permissions-security.md)
- Korzystanie z alertów Azure [grupy akcji](monitoring-action-groups.md), która może istnieć więcej niż jedną akcję dla każdego alertu programu SMS, w tym wysyłania połączeń głosowych, wywołaj element Runbook usługi Automatyzacja, wywołania elementu Webhook, skonfiguruj łącznik Zarządzanie usługami IT — i inne. 

## <a name="process-of-extending-your-alerts"></a>Proces rozszerzania alerty
Proces przechodzenia alerty z analizy dzienników do alertów Azure nie **nie** wymaga zmian w definicji alertu, w zapytania lub w konfiguracji w dowolny sposób. Tylko zmiany wymagane jest, że w Azure, wszystkie akcje, takie jak powiadomienia e-mail, wywołanie elementu webhook uruchomiony element runbook usługi Automatyzacja lub nawiązywania połączenia z narzędzia Zarządzanie usługami IT — są wykonywane przy użyciu grupy akcji. Jeśli grupy akcji są już skojarzone z alertu — będą uwzględniane podczas rozszerzania na platformie Azure.

> [!NOTE]
> Firma Microsoft automatycznie rozszerzy alerty utworzone w Analiza dzienników Azure alertów, uruchamianie na **14 maja 2018** w cykl aż do zakończenia. Jeśli występują problemy przy tworzeniu [grupy akcji](monitoring-action-groups.md), użytkownik może użyć [korygowania kroki](monitoring-alerts-extend-tool.md#troubleshooting) dopóki **5 lipca 2018** można pobrać grupy akcji utworzone automatycznie. 
> 

Podczas planowania alerty w obszarze roboczym analizy dzienników dla rozszerzenia na platformie Azure, użytkownik będzie nadal działać i zostanie **nie** w żaden sposób naruszyć konfiguracji. Podczas planowania alerty mogą być niedostępne do edycji/modyfikacji tymczasowo; ale nowe alerty Azure mogą w dalszym ciągu można utworzyć w tym czasie. Próba edycji lub tworzenie alertów przy użyciu portalu OMS, konieczne będzie kontynuować tworzenie ich z obszaru roboczego analizy dzienników lub alerty Azure w portalu Azure.

 ![Podczas okresu zaplanowanego Akcja użytkownika na alerty przekierowanie do usługi Azure](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Rozszerzanie alertów z usługi Analiza dzienników Azure nie wiąże się z opłatami do konta i użycia Azure alerty na podstawie zapytania analizy dzienników alerty będą nie naliczane używanego w ramach limitów i warunki określone w [Monitor Azure cennik zasad](https://azure.microsoft.com/pricing/details/monitor/)  

Można korzystać z zalet rozszerzania alerty przed tą datą przez dobrowolnie rezygnacja, aby przenieść je do platformy Azure alerty.

### <a name="how-to-voluntarily-extend-your-alerts"></a>Jak rozszerzyć dobrowolnie alerty
Rozszerzenie alerty Alerty Azure, firma Microsoft uwzględniła dwie metody do zakończenia tego zadania w obszarze roboczym.  Użytkownik może wykonywać za pomocą kreatora, które są dostępne w portalu OMS lub programowo przy użyciu nowego interfejsu API.  Aby uzyskać więcej informacji, zobacz [rozszerzanie alerty na platformie Azure przy użyciu portalu OMS i interfejsu API](monitoring-alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>Występuje po rozszerzeniu alerty
Alerty zostały rozszerzone do alertów Azure, będzie nadal będą znajdować się w portalu OMS zarządzania nie inaczej niż wcześniej.<br><br> ![Wyświetlanie alertów po rozszerzana Azure portalu OMS](./media/monitor-alerts-extend/PostExtendList.png)

Podczas próby edytowania istniejącego alertu lub Utwórz nowy alert w portalu OMS automatycznie są przekierowywane do alertów Azure.  

> [!NOTE]
> Należy upewnić się, że uprawnienia przypisane do osób, które należy dodać lub edytować alerty są prawidłowo przypisany w usłudze Azure.  Przegląd, [uprawnienia za pomocą monitora Azure i alertów](monitoring-roles-permissions-security.md) Aby zrozumieć, jakie uprawnienia należy przyznać.  
> 

Alert tworzenia będą nadal działały z [API analizy dziennika](../log-analytics/log-analytics-api-alerts.md) i [dziennika analizy zasobów szablonu](../monitoring/monitoring-solutions-resources-searches-alerts.md), z niewielkimi tylko jeden zmiany można zastosować, wymagane — grup akcji muszą być uwzględnione.

## <a name="next-steps"></a>Kolejne kroki

* Więcej informacji na temat narzędzia do [inicjować rozszerzanie alerty z analizy dzienników na platformie Azure](monitoring-alerts-extend-tool.md)
* Dowiedz się więcej o nowe [zgłaszać alerty Azure](monitoring-overview-unified-alerts.md).
* Dowiedz się, jak utworzyć [rejestrowania alertów w alertach Azure](monitor-alerts-unified-log.md).
