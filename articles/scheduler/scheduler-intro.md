---
title: Co to jest usługa Azure Scheduler?
description: Tworzenie harmonogramu i uruchamianie zautomatyzowanych zadań, które wywołują usługi na platformie Azure lub poza nią
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e851da0013cf3a9ff6bb1a0fc1c073b5b796c54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898535"
---
# <a name="what-is-azure-scheduler"></a>Co to jest usługa Azure Scheduler?

> [!IMPORTANT]
> [Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje harmonogram platformy Azure, który jest [wycofywany.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Aby kontynuować pracę z zadaniami skonfigurowane w harmonogramie, należy jak [najszybciej przeprowadzić migrację do usługi Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Harmonogram nie jest już dostępny w witrynie Azure portal, ale [interfejsy CMDLET interfejsu REST](/rest/api/scheduler) i [narzędzia cmdlet programu Azure Scheduler PowerShell](scheduler-powershell-reference.md) pozostają dostępne w tej chwili, dzięki czemu można zarządzać zadaniami i kolekcjami zadań.

Usługa [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) pomaga w tworzeniu [zadań](../scheduler/scheduler-concepts-terms.md), które są uruchamiane w chmurze przez deklaratywne opisanie akcji. Usługa następnie automatycznie planuje i uruchamia te akcje. Można np. wywoływać usługi na platformie Azure i poza nią, np. przez wywołanie punktów końcowych HTTP lub HTTPS, jak również publikować komunikaty w kolejkach usługi Azure Storage i kolejkach lub tematach usługi Azure Service Bus. Zadania można uruchamiać natychmiast lub w późniejszym czasie. Usługa Scheduler umożliwia prostą obsługę [złożonych harmonogramów i zaawansowanych cykli](../scheduler/scheduler-advanced-complexity.md). Usługa Scheduler określa, kiedy uruchamiać zadania, przechowuje historię wyników zadania, którą można przeglądać, a następnie w sposób przewidywalny i niezawodny planuje obciążenia do uruchomienia.

Inne funkcje planowania platformy Azure również wykorzystują usługę Scheduler w tle, na przykład usługa [Azure WebJobs](../app-service/webjobs-create.md), która jest funkcją [Web Apps](https://azure.microsoft.com/services/app-service/web/) w usłudze Azure App Service. Komunikację dla tych akcji można zarządzać za pomocą [interfejsu API REST harmonogramu,](https://docs.microsoft.com/rest/api/scheduler/)który ułatwia zarządzanie komunikacją dla tych akcji.

Poniżej przedstawiono kilka scenariuszy, w których może być przydatna usługa Scheduler:

* Uruchamianie cyklicznych akcji aplikacji: na przykład okresowe zbieranie danych z serwisu Twitter do kanału informacyjnego.

* Wykonywanie codziennej konserwacji: na przykład codzienne oczyszczanie dzienników, tworzenie kopii zapasowych i wykonywanie innych zadań konserwacji.

  Na przykład jako administrator możesz tworzyć kopię zapasową bazy danych codziennie o godzinie 1:00 przez następne dziewięć miesięcy.

Chociaż usługi Scheduler można używać do tworzenia, obsługi i uruchamiania zaplanowanych obciążeń, nie hostuje ona obciążeń ani nie uruchamia kodu. Usługa jedynie *wywołuje* kod lub usługi hostowane gdzie indziej, np. na platformie Azure, lokalnie lub przez innego dostawcę. Usługa Scheduler może wywoływać z użyciem protokołu HTTP, HTTPS, kolejki usługi Storage, kolejki usługi Service Bus lub tematu usługi Service Bus.

Aby utworzyć, zaplanować, zarządzać, aktualizować lub usuwać zadania i [kolekcje zadań](../scheduler/scheduler-concepts-terms.md), można użyć kodu, [interfejsu API REST harmonogramu](https://docs.microsoft.com/rest/api/scheduler/)lub [poleceń cmdlet programu Azure Scheduler PowerShell](scheduler-powershell-reference.md).

## <a name="next-steps"></a>Następne kroki

* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Plany i rozliczenia dla harmonogramu platformy Azure](scheduler-plans-billing.md)
* [Tworzenie złożonych harmonogramów i zaawansowanego cyklu za pomocą usługi Azure Scheduler](scheduler-advanced-complexity.md)
* [Dokumentacja interfejsu API REST usługi Azure Scheduler](/rest/api/scheduler)
* [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)
