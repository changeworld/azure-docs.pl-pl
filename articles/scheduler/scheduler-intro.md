---
title: Co to jest usługa Azure Scheduler? | Microsoft Docs
description: Dowiedz się, jak tworzyć, planować i uruchamiać zadania automatyczne, które wywołują usługi na platformie Azure i poza nią
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 2f418a78f80d65cbb784685804a4cc6790c28b99
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300903"
---
# <a name="what-is-azure-scheduler"></a>Co to jest usługa Azure Scheduler?

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która jest [wycofywana](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Aby kontynuować pracę z zadaniami skonfigurowanymi w usłudze Scheduler, [Przeprowadź migrację do Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) najszybciej, jak to możliwe.

Usługa [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) pomaga w tworzeniu [zadań](../scheduler/scheduler-concepts-terms.md), które są uruchamiane w chmurze przez deklaratywne opisanie akcji. Usługa następnie automatycznie planuje i uruchamia te akcje. Można np. wywoływać usługi na platformie Azure i poza nią, np. przez wywołanie punktów końcowych HTTP lub HTTPS, jak również publikować komunikaty w kolejkach usługi Azure Storage i kolejkach lub tematach usługi Azure Service Bus. Zadania można uruchamiać natychmiast lub w późniejszym czasie. Usługa Scheduler umożliwia prostą obsługę [złożonych harmonogramów i zaawansowanych cykli](../scheduler/scheduler-advanced-complexity.md). Usługa Scheduler określa, kiedy uruchamiać zadania, przechowuje historię wyników zadania, którą można przeglądać, a następnie w sposób przewidywalny i niezawodny planuje obciążenia do uruchomienia.

Chociaż usługi Scheduler można używać do tworzenia, obsługi i uruchamiania zaplanowanych obciążeń, nie hostuje ona obciążeń ani nie uruchamia kodu. Usługa jedynie *wywołuje* kod lub usługi hostowane gdzie indziej, np. na platformie Azure, lokalnie lub przez innego dostawcę. Usługa Scheduler może wywoływać z użyciem protokołu HTTP, HTTPS, kolejki usługi Storage, kolejki usługi Service Bus lub tematu usługi Service Bus. Do tworzenia i planowania zadań oraz zarządzania nimi można użyć witryny [Azure Portal](../scheduler/scheduler-get-started-portal.md), kodu, [interfejsu API REST usługi Scheduler](https://docs.microsoft.com/rest/api/scheduler/) lub [poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md). Można na przykład programowo tworzyć, wyświetlać, aktualizować lub usuwać zadania i [kolekcje zadań](../scheduler/scheduler-concepts-terms.md) lub nimi zarządzać za pomocą skryptów oraz w witrynie Azure Portal.

Inne funkcje planowania platformy Azure również wykorzystują usługę Scheduler w tle, na przykład usługa [Azure WebJobs](../app-service/webjobs-create.md), która jest funkcją [Web Apps](https://azure.microsoft.com/services/app-service/web/) w usłudze Azure App Service. Komunikacją niezbędną do wykonywania tych akcji można zarządzać przy użyciu [interfejsu API REST usługi Scheduler](https://docs.microsoft.com/rest/api/scheduler/). ułatwia zarządzanie komunikacją niezbędną do wykonywania tych akcji.

Poniżej przedstawiono kilka scenariuszy, w których może być przydatna usługa Scheduler:

* **Uruchamianie cyklicznych akcji aplikacji**: na przykład okresowe zbieranie danych z serwisu Twitter do kanału informacyjnego.

* **Wykonywania codziennej konserwacji**: na przykład codzienne oczyszczanie dzienników, tworzenie kopii zapasowych i wykonywanie innych zadań konserwacji. 

  Na przykład jako administrator możesz tworzyć kopię zapasową bazy danych codziennie o godzinie 1:00 przez następne dziewięć miesięcy.

## <a name="next-steps"></a>Następne kroki

* [Rozpoczynanie pracy z usługą Scheduler w witrynie Azure portal](scheduler-get-started-portal.md)
* Dowiedz się więcej o [planach i rozliczeniach dla usługi Azure Scheduler](scheduler-plans-billing.md)
* Dowiedz się, [jak tworzyć złożone harmonogramy i zaawansowane cykle z użyciem usługi Azure Scheduler](scheduler-advanced-complexity.md)