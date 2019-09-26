---
title: Limity, przydziały i progi w usłudze Azure Scheduler
description: Dowiedz się więcej na temat limitów, przydziałów, wartości domyślnych i progów ograniczania dla usługi Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 293cd956f8270a4863fcc657f58c970096cec1e3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300920"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limity, przydziały i progi ograniczania w usłudze Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która jest [wycofywana](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Aby kontynuować pracę z zadaniami skonfigurowanymi w usłudze Scheduler, [Przeprowadź migrację do Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) najszybciej, jak to możliwe.

## <a name="limits-quotas-and-thresholds"></a>Limity, przydziały i progi

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>Nagłówek x-MS-Request-ID

Każde żądanie wykonywane względem usługi Scheduler zwraca nagłówek odpowiedzi o nazwie **x-MS-Request-ID**. Ten nagłówek zawiera nieprzezroczystą wartość, która jednoznacznie identyfikuje żądanie. Dlatego jeśli żądanie nie powiedzie się, a żądanie jest prawidłowo sformatowane, możesz zgłosić błąd do firmy Microsoft, podając wartość nagłówka odpowiedzi **x-MS-Request-ID** i uwzględniając następujące szczegóły: 

* Wartość **x-MS-Request-ID**
* Przybliżony czas wprowadzenia żądania 
* Identyfikatory dla subskrypcji platformy Azure, kolekcji zadań i zadania 
* Typ operacji, którą próbowano wykonać żądanie

## <a name="see-also"></a>Zobacz także

* [Czym jest Azure Scheduler?](scheduler-intro.md)
* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
