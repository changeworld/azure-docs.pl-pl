---
title: Harmonogram limitów i ustawień domyślnych
description: Harmonogram limitów i ustawień domyślnych
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4be0695402b66fdb2a027bfbada0e0b26e02d36f
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378724"
---
# <a name="scheduler-limits-and-defaults"></a>Harmonogram limitów i ustawień domyślnych
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Przydziały usługi Scheduler, limity, wartości domyślne i ograniczenia
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>Nagłówek x-ms-request-id
Każde żądanie dotyczące usługi Scheduler zwraca nagłówek odpowiedzi, o nazwie **x-ms-request-id**. Ten nagłówek zawiera nieprzezroczysta wartość, która jednoznacznie identyfikuje żądanie.

Jeśli żądanie stale kończy się niepowodzeniem i upewnieniu się, że wniosek jest poprawnie sformułowany, może używać tę wartość, aby zgłosić błąd do firmy Microsoft. W raporcie obejmują wartość x-ms-request-id, przybliżony czas, który odebrał żądanie, identyfikator subskrypcji, Kolekcja zadań i/lub zadania oraz typ operacji, które próbowało żądania.

## <a name="see-also"></a>Zobacz też
 [Co to jest Scheduler?](scheduler-intro.md)

 [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)

 [Rozpoczynanie pracy z usługą Scheduler w witrynie Azure Portal](scheduler-get-started-portal.md)

 [Plany i rozliczenia w usłudze Azure Scheduler](scheduler-plans-billing.md)

 [Dokumentacja interfejsu API REST usługi Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)

 [Wysoka dostępność i niezawodność usługi Azure Scheduler](scheduler-high-availability-reliability.md)

 [Uwierzytelnianie połączeń wychodzących usługi Azure Scheduler](scheduler-outbound-authentication.md)

