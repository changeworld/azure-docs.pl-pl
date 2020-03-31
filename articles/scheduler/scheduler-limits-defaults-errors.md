---
title: Limity, przydziały i progi w harmonogramie platformy Azure
description: Dowiedz się więcej o limitach, przydziałach, wartościach domyślnych i progach przepustnicy dla harmonogramu platformy Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898523"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limity, przydziały i progi przepustnicy w harmonogramie platformy Azure

> [!IMPORTANT]
> [Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje harmonogram platformy Azure, który jest [wycofywany.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Aby kontynuować pracę z zadaniami skonfigurowane w harmonogramie, należy jak [najszybciej przeprowadzić migrację do usługi Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Harmonogram nie jest już dostępny w witrynie Azure portal, ale [interfejsy CMDLET interfejsu REST](/rest/api/scheduler) i [narzędzia cmdlet programu Azure Scheduler PowerShell](scheduler-powershell-reference.md) pozostają dostępne w tej chwili, dzięki czemu można zarządzać zadaniami i kolekcjami zadań.

## <a name="limits-quotas-and-thresholds"></a>Limity, przydziały i progi

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>nagłówek x-ms-request-id

Każde żądanie wykonane z usługą Harmonogram zwraca nagłówek odpowiedzi o nazwie **x-ms-request-id**. Ten nagłówek zawiera nieprzezroczystą wartość, która jednoznacznie identyfikuje żądanie. Tak więc jeśli żądanie konsekwentnie nie powiedzie się i potwierdzono, że żądanie jest poprawnie sformatowane, można zgłosić błąd do firmy Microsoft, podając wartość nagłówka odpowiedzi **x-ms-request-id** i dołączając następujące szczegóły: 

* Wartość **x-ms-request-id**
* Przybliżony czas złożenia wniosku 
* Identyfikatory subskrypcji platformy Azure, zbierania zadań i zadania 
* Typ operacji, którą próbowano wykonać w żądaniu

## <a name="next-steps"></a>Następne kroki

* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Plany i rozliczenia dla harmonogramu platformy Azure](scheduler-plans-billing.md)
* [Dokumentacja interfejsu API REST usługi Azure Scheduler](/rest/api/scheduler)
* [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)