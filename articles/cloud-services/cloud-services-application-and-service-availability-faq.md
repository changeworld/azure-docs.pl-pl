---
title: Aplikacji i problemów dotyczących dostępności usługi dla Microsoft Azure Cloud Services często zadawane pytania | Dokumentacja firmy Microsoft
description: W tym artykule wymieniono często zadawane pytania dotyczące aplikacji i dostępności usługi dla usługi w chmurze Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 49576aa99f6cd505648e33348b89e502bea9d5c4
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Aplikacji i problemów dotyczących dostępności usługi dla usług Azure Cloud Services: często zadawane pytania (FAQ)

Ten artykuł zawiera często zadawane pytania dotyczące aplikacji i problemów dotyczących dostępności usługi dla [usługi w chmurze Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Można również znaleźć [strony rozmiar maszyny Wirtualnej usługi w chmurze](cloud-services-sizes-specs.md) dla informacji o rozmiarze.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Moje roli został ponownie przetworzony. Były aktualizację wprowadzanie dla usługi w chmurze?
Około raz w miesiącu, firma Microsoft udostępnia nową wersję systemu operacyjnego gościa dla maszyn wirtualnych systemu Windows Azure PaaS. System operacyjny gościa jest tylko jeden taki aktualizacji w potoku. Inne czynniki mogą mieć wpływ zlecenia. Ponadto Azure działa na setki tysięcy komputerów. W związku z tym jest niemożliwe do prognozowania określenie dokładnej daty i czasu, gdy zostanie ponownie uruchomiony poszczególnych ról. Możemy zaktualizować gościa systemu operacyjnego zaktualizować źródła danych RSS o najnowsze informacje, które zostały, ale należy rozważyć, które zostały zgłoszone czas jako wartość przybliżoną. Firma Microsoft wiedzą, że jest to powodować problemy dla klientów i pracy w planie limit lub dokładnie czas ponownego uruchomienia.

Aby uzyskać szczegółowe informacje o najnowsze aktualizacje systemu operacyjnego gościa, zobacz [wersji systemu operacyjnego gościa Azure i zgodność pakietu SDK](cloud-services-guestos-update-matrix.md).

Pomocne informacje dotyczące ponownego uruchomienia i wskaźniki do szczegóły techniczne dotyczące systemu operacyjnego hosta i gościa aktualizacji, zapoznaj się z subskrypcją MSDN blogu [roli wystąpienia uruchamia ponownie z powodu uaktualnienia systemu operacyjnego](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Dlaczego pierwsze żądanie do mojego usługi w chmurze po pewnym czasie usługi trwa dłużej niż zwykle?
Gdy serwer sieci Web otrzymuje pierwsze żądanie, najpierw rekompiluje kod, a następnie je przetwarza żądanie. Dlatego to pierwsze żądanie dłużej, niż pozostałe. Domyślnie pula aplikacji pobiera Zamknij w przypadku braku aktywności użytkownika. Pula aplikacji zostanie również Odtwórz domyślnie co 1,740 minut (29 godzin).

Awarie aplikacji usług Internet Information Services (IIS), pule mogą być okresowo przetworzony ponownie, aby uniknąć niestabilny stanów, które mogą prowadzić do aplikacji, zawiesza się, lub przecieków pamięci.

Poniższe dokumenty ułatwią zrozumienie i ograniczenia tego problemu:
* [Ustalania powolnego ładowania początkowego dla usług IIS](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [Pierwsze żądanie aplikacji sieci web usług IIS 7.5 po bardzo wolno odtwarzania puli aplikacji](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Jeśli chcesz zmienić domyślne zachowanie usług IIS, konieczne będzie użycie uruchamiania zadań, ponieważ ręcznie zastosować zmiany wystąpień roli sieci Web, zmiany po pewnym czasie zostaną utracone.

Aby uzyskać więcej informacji, zobacz [sposób konfigurowania i uruchamiania zadań uruchamiania dla usługi w chmurze](cloud-services-startup-tasks.md).
