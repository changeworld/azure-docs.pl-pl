---
title: Ciągłość działania — Microsoft Genomics | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: W tym omówieniu opisano możliwości Microsoft Genomics zapewnia ciągłość biznesową i odzyskiwanie po awarii. Poznaj opcje odzyskiwania zdarzeń powodujących zakłócenia, takich jak awaria region platformy Azure, który może powodować utratę danych.
keywords: ciągłość prowadzenia działalności biznesowej, odzyskiwanie po awarii
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: 7a51477dbbf6f4e50959a6d979342961c7e49ad9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60641113"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Omówienie ciągłości biznesowej z usługą Microsoft Genomics
W tym omówieniu opisano możliwości Microsoft Genomics zapewnia ciągłość biznesową i odzyskiwanie po awarii. Poznaj opcje odzyskiwania zdarzeń powodujących zakłócenia, takich jak awaria region platformy Azure, który może powodować utratę danych. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics funkcje ciągłości działania tej pomocy technicznej 
Sporadycznie centrum danych platformy Azure może mieć awarię, co może spowodować zakłócenia działania firmy, która może trwać kilka minut do kilku godzin. Po awarii, wszystkie zadania aktualnie uruchomione w centrum danych zakończy się niepowodzeniem i usługi Microsoft Genomics zostaną automatycznie ponownie przesłać zadania do regionu pomocniczego. 

* Jedną z opcji jest czekać na centrum danych do trybu online, gdy awaria centrum danych za pośrednictwem. Jeśli awaria jest krótki, usługi Microsoft Genomics automatycznie wykryje zadań zakończonych niepowodzeniem i przepływ pracy zostanie automatycznie uruchomiony ponownie.

* Innym rozwiązaniem jest aktywnie przesyłanie przepływu pracy w innym regionie danych. Microsoft Genomics wdraża wystąpień w kilku [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/services/), i każde wystąpienie określonego regionu jest niezależne. Jeśli jednego z wystąpień Microsoft Genomics występuje błąd lokalny region, innych regionach uruchomione wystąpienia programu Microsoft Genomics będzie przetwarzania zadań. To przeniesienie na inny region jest pod kontrolą użytkownika i jest dostępna w dowolnym momencie.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Ręcznie trybu failover Microsoft Genomics przepływy pracy do innego regionu
W przypadku awarii regionalnego centrum danych może zdecydować się na przesyłanie przepływy pracy Microsoft Genomics w regionie pomocniczym, zgodnie z wymaganiami ciągłość działalności biznesowej danych, jak niezależność i biznesowych. Aby ręcznie przepływy pracy Microsoft Genomics trybu failover należy użyć innego określonego regionu. Genomics konta i przesłać zadanie przy użyciu odpowiednich Genomics specyficzne dla regionu i poświadczenia konta magazynu.

W szczególności należy:
* Utwórz konto usługi Genomics w regionie pomocniczym, przy użyciu witryny Azure portal. 
* Migrowanie danych wejściowych do konta magazynu w regionie pomocniczym i skonfiguruj folder wyjściowy w regionie pomocniczym.
* Przesyłanie przepływu pracy w regionie pomocniczym.

Po przywróceniu oryginalnego region usługi Microsoft Genomics nie przeprowadza migracji danych z regionu pomocniczego do regionu oryginalnego. Może zdecydować przenieść dane wejściowe i pliki wyjściowe z regionu pomocniczego do regionu oryginalnego.  Jeśli postanowisz przenieść swoje dane, to znajduje się poza usługi Genomics, a wszystkie opłaty związane z przepływu danych będzie odpowiedzialny za. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Trwa przygotowywanie możliwe awarii specyficzne dla regionu
Jeśli jesteś zajmującym się ochroną szybsze odzyskiwanie w przypadku awarii centrum danych, istnieje kilka kroków, które można wykonać, aby skrócić czas, jaki zajmuje należy ręcznie ponownie przesłać Microsoft Genomics przepływy pracy do regionu pomocniczego:

* Określenie odpowiedniego regionu pomocniczego i aktywnego utworzenie konta usługi Genomics w danym regionie
* Duplikowanie danych w regionie podstawowych i pomocniczych tak, aby Twoje dane są natychmiast dostępne w regionie pomocniczym. Może to być gotowe ręcznie lub przy użyciu [magazyn geograficznie nadmiarowy](https://docs.microsoft.com/azure/storage/common/storage-redundancy) funkcji dostępnych w usłudze Azure storage. 

## <a name="next-steps"></a>Kolejne kroki
W tym artykule opisano opcje ciągłości biznesowej i odzyskiwania po awarii podczas korzystania z usługi Microsoft Genomics. Aby uzyskać więcej informacji o ciągłość biznesową i odzyskiwanie po awarii w ramach platformy Azure ogólnie rzecz biorąc, zobacz [wskazówek technicznych odporność platformy Azure.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 