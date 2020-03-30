---
title: Często zadawane pytania dotyczące dostępności aplikacji i usług
titleSuffix: Azure Cloud Services
description: W tym artykule wymieniono często zadawane pytania dotyczące dostępności aplikacji i usług dla usług w chmurze platformy Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c294d4583ba2690e1f4952441ffb43bff1459059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386922"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemy z dostępnością aplikacji i usług dla usług w chmurze Azure: często zadawane pytania (często zadawane pytania)

Ten artykuł zawiera często zadawane pytania dotyczące problemów z dostępnością aplikacji i usług dla [usług w chmurze platformy Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Informacje o rozmiarach można również zapoznać się ze [stroną Rozmiar maszyny Wirtualnej usług w chmurze.](cloud-services-sizes-specs.md)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Moja rola została poddana recyklingowi. Czy została wdrożona aktualizacja dla mojej usługi w chmurze?
Mniej więcej raz w miesiącu firma Microsoft wydaje nową wersję systemu operacyjnego gościa dla maszyn wirtualnych paas platformy Windows azure.System operacyjny gościa jest tylko jedną taką aktualizację w potoku. Na wydanie może mieć wpływ wiele innych czynników. Ponadto platforma Azure działa na setkach tysięcy maszyn. W związku z tym nie można przewidzieć dokładną datę i godzinę, kiedy role zostaną ponownie uruchomione. Aktualizujemy kanał RSS aktualizacji systemu operacyjnego gościa o najnowsze informacje, które mamy, ale należy wziąć pod uwagę, że zgłoszony czas jest przybliżoną wartością. Zdajemy sobie sprawę, że jest to problematyczne dla klientów i pracujemy nad planem ograniczenia lub precyzyjnego ponownego uruchomienia czasu.

Aby uzyskać pełne informacje na temat ostatnich aktualizacji systemu operacyjnego gościa, zobacz [Wersje systemu operacyjnego gościa platformy Azure i macierz zgodności SDK.](cloud-services-guestos-update-matrix.md)

Aby uzyskać przydatne informacje na temat ponownego uruchamiania i wskaźników do szczegółów technicznych aktualizacji systemu operacyjnego gościa i hosta, zobacz wpis msdn [wystąpienie roli blogu restartuje się z powodu uaktualnień systemu operacyjnego](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Dlaczego pierwsze żądanie do mojej usługi w chmurze po zakończeniu usługi jest bezczynne przez pewien czas dłużej niż zwykle?
Gdy serwer sieci Web odbiera pierwsze żądanie, najpierw ponownie kompiluje kod, a następnie przetwarza żądanie. Dlatego pierwsze żądanie trwa dłużej niż inne. Domyślnie pula aplikacji zostanie zamknięta w przypadku braku aktywności użytkownika. Pula aplikacji będzie również domyślnie przetwarzać co 1740 minut (29 godzin).

Pule aplikacji internetowych usług informacyjnych (IIS) mogą być okresowo poddawanym recyklingowi, aby uniknąć niestabilnych stanów, które mogą prowadzić do awarii aplikacji, zawieszenia lub przecieków pamięci.

Poniższe dokumenty pomogą Ci zrozumieć i złagodzić ten problem:
* [Mocowanie powolnego obciążenia początkowego dla iis](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [Pierwsze żądanie aplikacji internetowej usług IIS 7.5 po bardzo powolnym recyklingu w puli aplikacji](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Jeśli chcesz zmienić domyślne zachowanie usług IIS, należy użyć zadań uruchamiania, ponieważ jeśli ręcznie zastosujesz zmiany do wystąpień roli sieci Web, zmiany zostaną ostatecznie utracone.

Aby uzyskać więcej informacji, zobacz [Jak skonfigurować i uruchomić zadania uruchamiania usługi w chmurze](cloud-services-startup-tasks.md).
