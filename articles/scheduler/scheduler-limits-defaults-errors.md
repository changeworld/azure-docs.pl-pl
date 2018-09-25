---
title: Limity, limity przydziału i progi w usłudze Azure Scheduler
description: Dowiedz się o limity, limity przydziału, wartości domyślne i ograniczać progi dla usługi Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 0c1e704a3bdec239c87d879ae1ef95e6e76d27fc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966915"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limity, limity przydziału i ograniczenia progi w usłudze Azure Scheduler

> [!IMPORTANT]
> [Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługi Azure Scheduler, która zostanie wycofana. Aby zaplanować prace, [zamiast tego spróbuj wykonać usługi Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="limits-quotas-and-thresholds"></a>Limity, limity przydziału i progi

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>Nagłówek x-ms-request-id

Każde żądanie dotyczące usługi Scheduler zwraca nagłówek odpowiedzi, o nazwie **x-ms-request-id**. Ten nagłówek zawiera nieprzezroczysta wartość, która jednoznacznie identyfikuje żądanie. Tak, jeśli żądanie stale kończy się niepowodzeniem i potwierdzony, żądanie jest poprawnie sformatowana, możesz zgłosić błąd do firmy Microsoft, zapewniając **x-ms-request-id** wartości nagłówka odpowiedzi i szczegółowe informacje w tym: 

* **X-ms-request-id** wartość
* Przybliżony czas, gdy wniosek został złożony 
* Identyfikatory subskrypcji platformy Azure, Kolekcja zadań i zadań 
* Typ operacji, które próbowało żądania

## <a name="see-also"></a>Zobacz także

* [Co to jest usługa Azure Scheduler?](scheduler-intro.md)
* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
