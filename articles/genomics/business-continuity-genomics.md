---
title: Ciągłość działania — Genomics Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak Microsoft Genomics obsługuje ciągłość prowadzenia działalności biznesowej
keywords: ciągłość prowadzenia działalności biznesowej, odzyskiwania po awarii
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: cb3825cb89aff386c4f7c3f3b0d771d73fe637b1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Omówienie ciągłości działalności biznesowej z Genomics firmy Microsoft
Ten przegląd zawiera opis możliwości, które Genomics firmy Microsoft zapewnia ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii. Więcej informacji na temat opcji odzyskiwania z destrukcyjne zdarzenia, takie jak awarią region platformy Azure, która może spowodować utratę danych. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics funkcje tego ciągłość obsługi 
W sporadycznych przypadkach, centrum danych platformy Azure mogą mieć awarii, co może spowodować zakłócenia biznesowych, które może trwać kilka minut do kilku godzin. Po awarii, wszystkie zadania uruchomione w centrum danych zakończy się niepowodzeniem i usługi Microsoft Genomics zostanie automatycznie ponownie przesłać zadania w regionie pomocniczym. 

* Jedną z opcji jest czekać na centrum danych do trybu online po awarii centrum danych znajduje się nad. W przypadku krótkich awarii usługi Microsoft Genomics automatycznie wykryje zadania zakończone niepowodzeniem i przepływ pracy zostanie automatycznie uruchomiony ponownie.

* Innym rozwiązaniem jest aktywnie przesłania przepływu pracy w innym regionie danych. Microsoft Genomics wdraża wystąpień w kilku [regiony platformy Azure](https://azure.microsoft.com/regions/services/), a każde wystąpienie określonego regionu jest niezależna. Jeśli jedno wystąpienie Genomics Microsoft wystąpi awaria lokalnego regionu, innych regionów uruchomione wystąpienia programu Microsoft Genomics będzie przetwarzania zadań. To przeniesienie do alternatywnego regionu jest pod kontrolą użytkownika i dostępne w dowolnym momencie.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Ręcznie trybu failover Genomics Microsoft przepływy pracy do innego regionu
W przypadku regionalnej awarii centrum danych mogą zdecydować się na przesyłanie przepływów pracy Genomics firmy Microsoft w regionie pomocniczym, opartych na danych poszczególnych wymagań ciągłości suwerenności i business. Aby ręcznie przepływów pracy Genomics Microsoft trybu failover należy użyć innego określonego regionu. Genomika konta i przesłać zadanie z odpowiednią genomika określonego regionu i poświadczeń konta magazynu.

W szczególności należy:
* Utwórz konto genomiki w regionie pomocniczym, przy użyciu portalu Azure. 
* Migrowanie danych wejściowych na konto magazynu w regionie pomocniczym i skonfiguruj folderu wyjściowego w regionie pomocniczym.
* Przedstawia przepływ pracy w regionie pomocniczym.

Po przywróceniu oryginalnego region usługi Microsoft Genomics nie migrację danych z regionu pomocniczego oryginalnego regionu. Może zdecydować przenieść dane wejściowe i dane wyjściowe z regionu pomocniczego z powrotem na oryginalny regionu.  Jeśli postanowisz przenieść swoje dane, jest to poza usługi genomika i wszelkich opłat związanych z przepływu danych będzie z odpowiedzialności. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Przygotowywanie do możliwe awarii określonego regionu
Jeśli masz obawy szybsze odzyskiwanie w przypadku awarii centrum danych, istnieje kilka czynności, które można wykonać, aby skrócić czas, jaki zajmuje należy ręcznie ponownie przesłać Genomics Microsoft przepływy pracy w regionie pomocniczym:

* Określenie odpowiedniego regionu pomocniczego i aktywnie wykraczającego Utwórz konto genomiki w tym regionie
* Zduplikowane dane w regionie podstawowych i pomocniczych tak, aby Twoje dane są natychmiast dostępne w regionie pomocniczym. Może to być gotowe ręcznie lub używając [magazynu geograficznie nadmiarowego](https://docs.microsoft.com/azure/storage/common/storage-redundancy) funkcja dostępna w magazynie Azure. 

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono informacji na temat opcji ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii podczas korzystania z usługi Microsoft Genomics. Aby uzyskać więcej informacji o ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii w obrębie platformy Azure w ogólności, zobacz [wskazówki techniczne odporności platformy Azure.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 