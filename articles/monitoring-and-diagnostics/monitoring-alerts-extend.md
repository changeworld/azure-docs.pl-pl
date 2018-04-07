---
title: Rozszerzanie alerty (Kopiuj) z portalu OMS na platformie Azure — omówienie | Dokumentacja firmy Microsoft
description: Omówienie procesu kopiowania alertów z portalu OMS do alertów Azure, szczegóły dotyczące typowych problemów klienta.
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
ms.date: 04/06/2018
ms.author: vinagara
ms.openlocfilehash: 445adb7f57332a285494c744763f633806d2675e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="extend-copy-alerts-from-oms-portal-into-azure"></a>Rozszerzanie alerty (Kopiuj) z portalu OMS na platformie Azure
Portal Operations Management Suite (OMS) wyświetlane tylko alerty analizy dzienników.  Nowe środowisko alertów ma teraz zintegrowane środowisko alertów w różnych usług i części na platformie Microsoft Azure. Nowe środowisko dostępna jako **alerty** w obszarze Monitor Azure w usłudze Azure portalu zawiera alertów dotyczących działań w dzienniku, metryki alertów i dziennika alerty dla analizy dzienników i usługi Application Insights. 


Ale dla niektórych użytkowników, użyj analizy dzienników i allied funkcji, takich jak alerty, został przez [portal Microsoft operacji Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). I dlatego umożliwia łatwe zarządzanie ich innych zasobów platformy Azure, wraz z ich użycia analizy dzienników - systematycznie Microsoft ma zostały zapewnienie możliwości portalu OMS są również dostępne w portalu Azure. W tym zakresie, alerty Azure już umożliwia użytkownikom zarządzanie alerty na podstawie zapytania dla analizy dzienników, aby uzyskać więcej informacji, zobacz [rejestrowania alertów dotyczących alertów Azure](monitor-alerts-unified-log.md). W alertach w obszarze Azure Monitor alerty utworzone w portalu OMS są już na liście w obszarze roboczym analizy odpowiedniego dziennika. Jednak żadnego edycji lub zmienić takie alerty utworzone w portalu OMS, użytkownik musi zostać pozostaw Azure i użyć portalu OMS; następnie wrócić do platformy Azure, jeśli zachodzi potrzeba do wszelkich innych usług zarządzania. Aby zmniejszyć to trudności, Microsoft teraz jest umożliwienie użytkownikom rozszerzenie alerty z portalu OMS na platformie Azure.

## <a name="benefits-of-extending-your-alerts"></a>Korzyści z rozszerzenia alerty
Oprócz korzyści naliczone w nie ma potrzeby przejść z portalu Azure istnieją inne istotne zalety w rozszerzeniu alerty z portalu OMS do platformy Azure

- W odróżnieniu od w portalu OMS, gdzie można utworzyć i wyświetlić; tylko 250 alerty w alertach Azure to ograniczenie nie jest obecny
- Azure alertów wszystkie typy alertów mogą być zarządzane, wyliczyć i wyświetlane; nie tylko analizy dzienników alertów, jak w przypadku portalu OMS
- Kontrola dostępu umożliwia użytkownikom tylko monitorowanie i alerty, przy użyciu [roli Azure Monitor](monitoring-roles-permissions-security.md)
- Korzystanie z alertów Azure [grupy akcji](monitoring-action-groups.md), które umożliwiają używanie więcej niż jedną akcję dla każdego alertu, w tym programu SMS, wywołać głosu, elementu Runbook automatyzacji, elementu Webhook, zarządzanie usługami IT — łącznika i więcej. 

## <a name="process-of-extending-your-alerts"></a>Proces rozszerzania alerty
Proces rozszerzania alerty z portalu OMS na platformie Azure, nie **nie** wymaga zmian w definicji alertu, w zapytania lub w konfiguracji w dowolny sposób. Tylko zmiany wymagane jest, że na platformie Azure, wszystkie akcje, takie jak powiadomienia e-mail wywołanie elementu webhook i uruchamiania elementu runbook automatyzacji lub nawiązywania Zarządzanie usługami IT — tool są wykonywane za pośrednictwem grupy działań. Dlatego jeśli grupa odpowiednich akcji skojarzonych z alertu - one zostaną stają się rozszerzone na platformie Azure.

Ponieważ proces rozszerzenia jest bezpieczna i nie interruptive, firma Microsoft rozszerzy alerty utworzone w portalu OMS na alerty Azure automatycznie — począwszy od **14 maja 2018**. Od tego dnia Microsoft rozpocznie się zaplanować rozszerzanie alerty na platformie Azure i stopniowo wszystkie alerty w portalu OMS, można zarządzać z portalu Azure. 

Gdy alerty w obszarze roboczym analizy dzienników są planowane do rozszerzania na platformie Azure, użytkownik będzie nadal działać i zostanie **nie** w żaden sposób naruszyć monitorowanie. Podczas planowania alerty mogą być niedostępne do edycji/modyfikacji tymczasowo; ale nowe alerty Azure mogą w dalszym ciągu można tworzyć w tym krótkim czasie. W tym czasie Jeśli wszystkie edytowania lub tworzenia alertu można to zrobić w portalu OMS, użytkownicy będą mieć opcję, aby kontynuować do Azure Log Analytics lub Azure alerty.

 ![Podczas okresu zaplanowanego Akcja użytkownika na alerty przekierowanie do usługi Azure](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Rozszerzanie alerty z portalu OMS na platformie Azure nie jest pobierana i użycia usługi Azure alertów dla zapytania na podstawie analizy dzienników alerty będą nie naliczane, gdy jest używany w ramach limitów i warunki określone w [Monitor Azure cennik zasad](https://azure.microsoft.com/en-us/pricing/details/monitor/)  

Użytkownicy mogą korzystać z zalet rozszerzające alertach przed tą datą. przez dobrowolnie Rezygnacja w celu umożliwienia zarządzania na platformie Azure alerty.

### <a name="how-to-voluntarily-extending-your-alerts"></a>Jak dobrowolnie rozszerzanie alerty
Aby umożliwić użytkownikom OMS łatwe przebieg do platformy Azure alerty, firma Microsoft opracowała narzędzia umożliwiające rozszerzenie alerty. Klienci portalu OMS firmy Microsoft mogą rozszerzać alerty Azure albo za pomocą kreatora w portalu OMS (lub) przez rozwiązania programowego przy użyciu nowego interfejsu API. Aby uzyskać więcej informacji, zobacz [rozszerzanie alerty na platformie Azure przy użyciu portalu OMS i interfejsu API](monitoring-alerts-extend-tool.md).


## <a name="usage-after-extending-your-alerts"></a>Użycie po rozszerzeniu alerty
Jak już wspomniano, alertach tworzonych w pakiet zarządzania operacji Microsoft będzie można rozszerzyć do alertów Azure; Po, która umożliwi zarządzanie nimi z platformy Azure. Alerty będą nadal wyświetlane w portalu OMS - sekcji ustawienie alertów. Jak przedstawiono poniżej:

 ![Wyświetlanie alertów po rozszerzana na platformie Azure portalu OMS](./media/monitor-alerts-extend/PostExtendList.png)

Do żadnej operacji dotyczących alertów, takie jak edytowania lub tworzenia zrobić w portalu OMS użytkowników będzie przezroczysty kierowane do alertów Azure. 

> [!NOTE]
> Gdy użytkownicy niewidocznie wpłynie na platformie Azure, na dodanie lub edytować akcję na alert w OMS — upewnij się, użytkownicy są prawidłowo zamapowane odpowiedni [uprawnienia za pomocą monitora Azure i alerty](monitoring-roles-permissions-security.md)

Alert będzie tworzenia z istniejącego [API analizy dziennika](../log-analytics/log-analytics-api-alerts.md) jak wcześniej, tylko drobne zmiany że po alerty zostały rozszerzone na platformie Azure — grup akcji musiałby być skojarzone w harmonogramie.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, z narzędzi [inicjować rozszerzanie alerty z usługą OMS na platformie Azure](monitoring-alerts-extend-tool.md)
* Dowiedz się więcej o nowe [zgłaszać alerty Azure](monitoring-overview-unified-alerts.md).
* Dowiedz się więcej o [rejestrowania alertów w alertach Azure](monitor-alerts-unified-log.md).
