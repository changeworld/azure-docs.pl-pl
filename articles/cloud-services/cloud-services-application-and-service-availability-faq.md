---
title: Aplikacja i problemów dotyczących dostępności usługi dla często zadawane pytania dotyczące systemu Microsoft Azure Cloud Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę często zadawane pytania dotyczące dostępności aplikacji i usług dla usług Microsoft Azure Cloud Services.
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: fb4b5dde63d8c7c75419d3202d9848cd6fde8b8a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337548"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Aplikacja i problemów dotyczących dostępności usługi dla usług Azure Cloud Services: Często zadawane pytania (FAQ)

Ten artykuł zawiera często zadawane pytania dotyczące aplikacji i problemów dotyczących dostępności usługi dla [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Można także zapoznać się [strony rozmiar maszyny Wirtualnej usługi w chmurze](cloud-services-sizes-specs.md) Aby uzyskać informacje o rozmiarze.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Moja rola może to być odtworzona. Czy były dowolne aktualizacje wprowadzane dla usługi w chmurze?
Około raz w miesiącu, firma Microsoft publikuje nową wersję systemu operacyjnego gościa dla maszyn wirtualnych z Windows Azure PaaS. System operacyjny gościa jest tylko jeden taki aktualizacji w potoku. Wydanie mogą mieć wpływ na inne czynniki. Ponadto platforma Azure działa w setkach tysięcy maszyn. Dlatego jest niemożliwe do prognozowania dokładną datę i godzinę, gdy role nastąpi ponowne uruchomienie komputera. Firma Microsoft aktualizuje gościa systemu operacyjnego zaktualizować źródło danych RSS z najnowszymi informacjami, które mamy, ale należy rozważyć, zgłaszany było przybliżona wartość. Mamy świadomość, że jest to kłopotliwy dla klientów i działają w ramach planu limit lub dokładnie czasu ponownego uruchomienia.

Aby uzyskać szczegółowe informacje o najnowszych aktualizacjach systemu operacyjnego gościa, zobacz [wersji systemu operacyjnego gościa platformy Azure i zgodności zestawów SDK](cloud-services-guestos-update-matrix.md).

Aby uzyskać przydatne informacje na temat ponownego uruchamiania i wskaźników do szczegóły techniczne dotyczące aktualizacji gościa i systemu operacyjnego hosta, zobacz wpis na blogu MSDN [roli wystąpienie powoduje ponowne uruchomienie z powodu uaktualnienia systemu operacyjnego](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Dlaczego to pierwsze żądanie do mojej usłudze w chmurze po pewnym czasie usługa trwać dłużej niż zwykle?
Gdy serwer sieci Web otrzymuje pierwszego żądania, najpierw następuje rekompilacja kod, a następnie przetwarza żądanie. Dlatego pierwsze żądanie trwa dłużej niż inne. Domyślnie pula aplikacji pobiera zamknięty w przypadkach braku aktywności użytkownika. Pula aplikacji zostanie również Odtwórz domyślnie co 1,740 minut (29 godzin).

Aplikacja Internet Information Services (IIS), pule mogą być okresowo odtwarzania, aby uniknąć niestabilne stanów, które mogą prowadzić do aplikacji ulegnie awarii, zawieszenia lub przecieki pamięci.

Następujące dokumenty pomoże Ci zrozumieć i rozwiązać ten problem:
* [Naprawianie wolnego ładowania początkowego dla usług IIS](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [Pierwsze żądanie aplikacji sieci web usług IIS 7.5 po bardzo wolno odtwarzanie puli aplikacji](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Jeśli chcesz zmienić domyślne zachowanie usług IIS, należy użyć zadań uruchamiania, ponieważ ręcznie zastosować zmiany do wystąpień roli internetowej, zmiany po pewnym czasie zostaną utracone.

Aby uzyskać więcej informacji, zobacz [sposób konfigurowania i uruchamiania zadania uruchamiania dla usługi w chmurze](cloud-services-startup-tasks.md).
