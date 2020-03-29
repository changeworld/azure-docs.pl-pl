---
title: Przegląd ciągłości działania
titleSuffix: Microsoft Genomics
description: W tym omówieniu opisano możliwości, które program Microsoft Genomics zapewnia ciągłość działania i odzyskiwanie po awarii.
keywords: ciągłość działania, odzyskiwanie po awarii
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 28a4a53851155c56e8d34981862bf52a3a2cf15b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72249175"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Omówienie ciągłości działania dzięki microsoft genomics
W tym omówieniu opisano możliwości, które program Microsoft Genomics zapewnia ciągłość działania i odzyskiwanie po awarii. Dowiedz się więcej o opcjach odzyskiwania po zdarzeniach powodujących zakłócenia działania, takich jak awaria regionu platformy Azure, które mogą spowodować utratę danych. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics funkcje, które obsługują ciągłość działania 
Mimo że rzadko centrum danych platformy Azure może mieć awarię, która może spowodować zakłócenia w działalności, które mogą trwać od kilku minut do kilku godzin. W przypadku awarii wszystkie zadania aktualnie uruchomione w centrum danych zakończy się niepowodzeniem, a usługa Microsoft Genomics nie będzie automatycznie ponownie przesyłać zadań do regionu pomocniczego. 

* Jedną z opcji jest oczekiwanie na powrót centrum danych do trybu online po zakończeniu awarii centrum danych. Jeśli awaria jest krótka, usługa Microsoft Genomics automatycznie wykryje nieudane zadania, a przepływ pracy zostanie automatycznie uruchomiony ponownie.

* Inną opcją jest proaktywne przesyłanie przepływu pracy w innym regionie danych. Microsoft Genomics wdraża wystąpienia w kilku [regionach platformy Azure,](https://azure.microsoft.com/regions/services/)a każde wystąpienie specyficzne dla regionu jest niezależne. Jeśli jedno z wystąpień microsoft genomics wystąpi błąd lokalny regionu, inne regiony z uruchomionymi wystąpieniami microsoft genomics będą nadal przetwarzać zadania. Ten transfer do alternatywnego regionu jest pod kontrolą użytkownika i jest dostępny w dowolnym momencie.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Ręczne przepływy pracy microsoft genomics w trybie failover do innego regionu
W przypadku wystąpienia awarii regionalnego centrum danych można przesłać przepływy pracy microsoft genomics w regionie pomocniczym na podstawie indywidualnych wymagań dotyczących suwerenności danych i ciągłości biznesowej. Aby ręcznie pracować awaryjnie obiegów pracy microsoft genomics, należy użyć innego regionu specyficzne dla regionu. Konto genomiki i przesyłanie zadania z odpowiednimi poświadczeniami genomiki i konta magazynu specyficznymi dla regionu.

W szczególności, trzeba będzie:
* Utwórz konto genomiki w regionie pomocniczym, korzystając z witryny Azure portal. 
* Migrowanie danych wejściowych do konta magazynu w regionie pomocniczym i konfigurowanie folderu wyjściowego w regionie pomocniczym.
* Prześlij przepływ pracy w regionie pomocniczym.

Po przywróceniu oryginalnego regionu usługa Microsoft Genomics nie migruje danych z regionu pomocniczego z powrotem do regionu oryginalnego. Można wybrać przeniesienie plików wejściowych i wyjściowych z regionu pomocniczego z powrotem do regionu oryginalnego.  Jeśli zdecydujesz się przenieść swoje dane, jest to poza usługą Genomics i wszystkie opłaty związane z przenoszeniem danych będą twoim obowiązkiem. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Przygotowanie do ewentualnej awarii specyficznej dla regionu
Jeśli obawiasz się szybszego odzyskiwania w przypadku awarii centrum danych, możesz wykonać kilka kroków, aby skrócić czas ręcznego ponownego przesłania przepływów pracy microsoft genomics do regionu pomocniczego:

* Identyfikowanie odpowiedniego regionu pomocniczego i aktywne tworzenie konta genomiki w tym regionie
* Duplikuj dane w regionie podstawowym i pomocniczym, aby dane były natychmiast dostępne w regionie pomocniczym. Można to zrobić ręcznie lub przy użyciu funkcji [magazynu geograficznie nadmiarowego](https://docs.microsoft.com/azure/storage/common/storage-redundancy) dostępne w magazynie platformy Azure. 

## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się o opcjach ciągłości biznesowej i odzyskiwania po awarii podczas korzystania z usługi Microsoft Genomics. Aby uzyskać więcej informacji na temat ciągłości działania i odzyskiwania po awarii na platformie Azure w ogóle, zobacz [wskazówki techniczne dotyczące odporności platformy Azure.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 