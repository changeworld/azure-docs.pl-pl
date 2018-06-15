---
title: Limity harmonogramu oraz wartości domyślnych
description: Limity harmonogramu oraz wartości domyślnych
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
ms.openlocfilehash: db6b1c196cb468f41c7a7ce34758de346b522abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23867815"
---
# <a name="scheduler-limits-and-defaults"></a>Limity harmonogramu oraz wartości domyślnych
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Przydziały harmonogramu, limity ustawień domyślnych i limity
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>Nagłówek x-ms-request-id
Każde żądanie dotyczące usługi harmonogramu zwraca nagłówka odpowiedzi o nazwie**x-ms-request-id**. Ten nagłówek zawiera wartości nieprzezroczystej, który unikatowo identyfikuje żądania.

Jeśli żądanie jest zawsze kończy się niepowodzeniem i upewnieniu się, że żądanie jest poprawnie sformułowany, można użyć tej wartości na raport o błędzie do firmy Microsoft. W raporcie zawiera wartość x-ms-request-id, przybliżony czas, który odebrał żądanie, identyfikator subskrypcji, kolekcji zadań i/lub zadania i typ operacji, które próbowało żądania.

## <a name="see-also"></a>Zobacz też
 [Co to jest Scheduler?](scheduler-intro.md)

 [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)

 [Rozpoczynanie pracy z usługą Scheduler w witrynie Azure Portal](scheduler-get-started-portal.md)

 [Plany i rozliczenia w usłudze Azure Scheduler](scheduler-plans-billing.md)

 [Dokumentacja interfejsu API REST usługi Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)

 [Wysoka dostępność i niezawodność usługi Azure Scheduler](scheduler-high-availability-reliability.md)

 [Uwierzytelnianie połączeń wychodzących usługi Azure Scheduler](scheduler-outbound-authentication.md)

