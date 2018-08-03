---
title: Odłączanie konta usługi Azure Automation od usługi Log Analytics
description: Ten artykuł zawiera omówienie sposobów odłączyć konto usługi Azure Automation z obszarem roboczym usługi Log Analytics.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1328ce8c306188c32bce5385f58f118a63c08deb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426537"
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Jak odłączyć konto usługi Automation z obszarem roboczym usługi Log Analytics

Usługa Azure Automation jest zintegrowana z usługą Log Analytics, umożliwiają nie tylko monitorowania zadań elementów runbook dla wszystkich kont usługi Automation, ale jest również wymagane podczas importowania następujących rozwiązań, które są zależne od usługi Log Analytics:

* [Zarządzanie aktualizacjami](../operations-management-suite/oms-solution-update-management.md)
* [Śledzenie zmian](../log-analytics/log-analytics-change-tracking.md)
* [Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu](automation-solution-vm-management.md)

Jeśli zdecydujesz, że już nie chcesz zintegrować konta usługi Automation z usługą Log Analytics, możesz odłączyć konta bezpośrednio w witrynie Azure portal.  Przed kontynuowaniem należy najpierw usunąć rozwiązania, o których wspomniano wcześniej, w przeciwnym razie ten proces nie będzie mógł kontynuować. Zapoznaj się z artykułem dla danego rozwiązania, które zostały zaimportowane, aby zrozumieć kroki wymagane do usunięcia go.

Po usunięciu tych rozwiązań, można wykonać poniższe kroki, aby odłączyć konto usługi Automation.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania do monitorowania usługi Azure SQL zostały utworzone zasoby usługi automation i może również muszą zostać usunięte przed odłączanie obszaru roboczego.

## <a name="unlink-workspace"></a>Odłączanie obszaru roboczego

1. W witrynie Azure Portal otwórz konto usługi Automation, a na automatyzację konta wybierz stronę **połączony obszar roboczy** sekcji **powiązane zasoby** po lewej stronie.

1. Na stronie odłączania obszaru roboczego kliknij **odłączanie obszaru roboczego**.

   ![Odłącz strona obszaru roboczego](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png).

   Zostanie wyświetlony monit sprawdzający, czy chcesz kontynuować.

1. Gdy usługa Azure Automation usiłuje odłączyć konto obszaru roboczego usługi Log Analytics, możesz śledzić postęp w obszarze **powiadomienia** z menu.

Jeśli używane jest rozwiązanie do zarządzania aktualizacjami, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Zaplanowanie aktualizacji — będzie mieć nazwy zgodne utworzonych wdrożeń aktualizacji)

* Grupy hybrydowych procesów roboczych utworzone dla rozwiązania — każda będzie miała podobnie do maszyna1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Jeśli uruchamianie/zatrzymywanie maszyn wirtualnych jest używana podczas szczytu rozwiązania, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywanie maszyn wirtualnych elementu runbook harmonogramy
* Uruchamianie i zatrzymywanie elementów runbook maszyny Wirtualnej
* Zmienne

## <a name="next-steps"></a>Kolejne kroki

Aby zmienić konfigurację konta usługi Automation w celu integracji z usługą Log Analytics, zobacz [przekazywać strumienie zadania i stan zadania z usługi Automation do usługi Log Analytics](automation-manage-send-joblogs-log-analytics.md).