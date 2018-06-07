---
title: Administrowanie pamięć podręczna Redis Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonywać zadania administracyjne, takie jak ponowne uruchomienie i harmonogram aktualizacji dla pamięci podręcznej Redis Azure
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 07/05/2017
ms.author: wesmc
ms.openlocfilehash: 3b62b41fb7b9d7ff6f40191c48d00c1f0a941e48
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639456"
---
# <a name="how-to-administer-azure-redis-cache"></a>Administrowanie pamięć podręczna Redis Azure
W tym temacie opisano sposób wykonywania zadań administracyjnych, takich jak [ponowny rozruch](#reboot) i [Planowanie aktualizacji](#schedule-updates) dla swoich wystąpień w pamięci podręcznej Redis Azure.

## <a name="reboot"></a>Ponowne uruchamianie
**Ponowny rozruch** blok służy do ponownego uruchamiania co najmniej jeden węzeł z pamięci podręcznej. Ta możliwość ponownego uruchomienia pozwala testować aplikację odporności w przypadku awarii węzła pamięci podręcznej.

![Ponowne uruchamianie](./media/cache-administration/redis-cache-administration-reboot.png)

Wybierz węzeł, aby ponownie uruchomić system, a następnie kliknij przycisk **ponowny rozruch**.

![Ponowne uruchamianie](./media/cache-administration/redis-cache-reboot.png)

Jeśli pamięć podręczna premium z włączoną funkcją klastrowania, można wybrać, które odłamków pamięci podręcznej, aby ponownie uruchomić system.

![Ponowne uruchamianie](./media/cache-administration/redis-cache-reboot-cluster.png)

Ponownie uruchomić co najmniej jeden węzeł z pamięci podręcznej, wybierz żądany węzeł i kliknij przycisk **ponowny rozruch**. Jeśli masz usługi pamięć podręczna premium z włączoną funkcją klastrowania, wybierz żądany odłamków ponowny rozruch, a następnie kliknij przycisk **ponowny rozruch**. Po kilku minutach ponowne uruchomienie wybranych węzłów i można je do trybu online później za kilka minut.

Wpływ na aplikacje klienckie różni się w zależności od węzły, które, ponowne uruchomienie.

* **Wzorzec** — węzła głównego umieszczeniu kończy się niepowodzeniem po ponownym rozruchu, Azure pamięć podręczna Redis do węzła repliki i zamienia ją do wzorca. Podczas tego trybu failover może być przez krótki czas, w którym połączenia może zakończyć się niepowodzeniem w pamięci podręcznej.
* **Podrzędny** — po ponownym uruchomieniu węzła podrzędny nie zwykle ma to wpływu na klientach pamięci podręcznej.
* **Zarówno i podrzędna** — zarówno węzły pamięci podręcznej są ponownie uruchamiane, wszystkie dane zostaną utracone w pamięci podręcznej po awarii połączeń do pamięci podręcznej aż do węzła podstawowego wraca do trybu online. Jeśli skonfigurowano [trwałości danych](cache-how-to-premium-persistence.md), najbardziej ostatniej kopii zapasowej jest przywracany pamięci podręcznej wraca do trybu online, ale wszystkie zapisy pamięci podręcznej, które wystąpiły po utworzeniu ostatniej kopii zapasowej zostaną utracone.
* **Węzły w warstwie premium pamięci podręcznej z włączoną funkcją klastrowania** — po ponownym uruchomieniu co najmniej jeden węzeł pamięci podręcznej premium z usługą klastrowania włączone, zachowanie dla wybranych węzłów jest taka sama jak po ponownym uruchomieniu odpowiedniego węzła lub węzłów pamięci podręcznej niedziałającego w klastrze.

> [!IMPORTANT]
> Ponowne uruchomienie jest teraz dostępna dla wszystkich warstw cenowych.
> 
> 

## <a name="reboot-faq"></a>Ponowny rozruch — często zadawane pytania
* [Węzły ponownie do testowania Moja aplikacja](#which-node-should-i-reboot-to-test-my-application)
* [Czy można ponownie pamięci podręcznej, aby wyczyścić połączeń klienta?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Spowoduje utratę danych z mojej pamięci podręcznej, jeśli I wykonaj ponowny rozruch?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Czy można ponownie moje pamięci podręcznej przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Warstw cenowych, jakie można korzystać z funkcji ponowny rozruch?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Węzły ponownie do testowania Moja aplikacja
Aby przetestować odporność aplikacji przed awarią węzła podstawowego pamięć podręczną, uruchom ponownie **wzorca** węzła. Aby przetestować odporność aplikacji przed awarią węzła pomocniczego, ponowny rozruch **podrzędna** węzła. Aby przetestować odporność aplikacji przed całkowitą awarią pamięci podręcznej, ponowny rozruch **zarówno** węzłów.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Czy można ponownie pamięci podręcznej, aby wyczyścić połączeń klienta?
Tak, jeśli ponowne uruchomienie z pamięci podręcznej są usuwane wszystkich połączeń klientów. Ponowne uruchomienie może być przydatne w przypadku wszystkich połączeń klientów są ze względu na błąd logiczny lub błędem w aplikacji klienta. Każda warstwa cenowa ma inną [limitów połączeń klienta](cache-configure.md#default-redis-server-configuration) dla różnych rozmiarach i raz te limity osiągnięciu nie więcej połączeń klienta są akceptowane. Ponowny rozruch pamięci podręcznej umożliwia wyczyszczenie wszystkich połączeń klientów.

> [!IMPORTANT]
> Jeśli ponowne uruchomienie pamięć podręczną, aby wyczyścić połączeń klienckich programie StackExchange.Redis automatycznie ponownie nawiąże połączenie po powrocie do trybu online w węźle Redis. Jeśli problem nie zostanie rozwiązany, połączeń klientów może nadal być wykorzystane.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Spowoduje utratę danych z mojej pamięci podręcznej, jeśli I wykonaj ponowny rozruch?
Jeśli ponowne uruchomienie zarówno **wzorca** i **podrzędna** węzłów, wszystkie dane w pamięci podręcznej (lub w tym niezależnych korzystania z usługi pamięć podręczna premium z włączoną funkcją klastrowania) zostaną utracone. Jeśli skonfigurowano [trwałości danych](cache-how-to-premium-persistence.md), najbardziej ostatniej kopii zapasowej zostaną przywrócone, gdy pamięci podręcznej wraca do trybu online, ale wszystkie zapisy pamięci podręcznej, które wystąpiły po utworzenia kopii zapasowej zostaną utracone.

Jeśli ponowne uruchomienie tylko jeden z węzłów, dane nie są zwykle utracone, ale nadal może być. Na przykład jeśli ponownym uruchomieniu węzła głównego i pamięć podręczna zapisu jest w toku, dane z pamięci podręcznej zapisu zostaną utracone. Inny scenariusz utraty danych będzie, jeśli ponowne uruchomienie jeden węzeł i innym węźle stanie się przestaną działać z powodu błędu w tym samym czasie. Aby uzyskać więcej informacji na temat możliwych przyczyn utraty danych, zobacz [co się stało z danych w pamięci podręcznej Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Czy można ponownie moje pamięci podręcznej przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?
Tak, dla środowiska PowerShell instrukcje można znaleźć [ponownego uruchomienia pamięci podręcznej Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Warstw cenowych, jakie można korzystać z funkcji ponowny rozruch?
Ponowne uruchomienie jest dostępna dla wszystkich warstw cenowych.

## <a name="schedule-updates"></a>Aktualizacje harmonogramu
**Zaplanuj aktualizacje** bloku umożliwia wyznaczenie okno obsługi pamięci podręcznej warstwy Premium. Okno obsługi zostanie określona, wszystkie aktualizacje serwera Redis są wprowadzane podczas tego okna. 

> [!NOTE] 
> Okna obsługi dotyczą tylko aktualizacje serwera Redis i nie do dowolnego Azure aktualizacji lub aktualizuje systemowi operacyjnemu maszyn wirtualnych, które hostują pamięci podręcznej.
> 
> 

![Aktualizacje harmonogramu](./media/cache-administration/redis-schedule-updates.png)

Aby określić okna obsługi, sprawdź odpowiednią dni i określ godzina rozpoczęcia okna konserwacji codziennie i kliknij **OK**. Należy pamiętać, że czas okna obsługi w formacie UTC. 

Domyślną i minimum, okna obsługi aktualizacji jest pięć godzin. Ta wartość nie jest konfigurowalne z portalu Azure, ale można go skonfigurować za pomocą programu PowerShell `MaintenanceWindow` parametr [AzureRmRedisCacheScheduleEntry nowy](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry) polecenia cmdlet. Aby uzyskać więcej informacji, zobacz [zaplanowanych aktualizacji przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania można zarządzać?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)


## <a name="schedule-updates-faq"></a>Zaplanuj aktualizacje — często zadawane pytania
* [Gdy aktualizacje wystąpić, jeśli funkcja Aktualizacje harmonogramu nie jest używany?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Jakiego rodzaju aktualizacje są wprowadzane podczas czasu zaplanowanego okna obsługi?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Zaplanowane aktualizacje przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania można zarządzać?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Warstw cenowych, jakie można korzystać z funkcji aktualizacji harmonogram?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Gdy aktualizacje wystąpić, jeśli funkcja Aktualizacje harmonogramu nie jest używany?
Jeśli nie określisz okna obsługi, aktualizacje mogą być dokonywane w dowolnym momencie.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Jakiego rodzaju aktualizacje są wprowadzane podczas czasu zaplanowanego okna obsługi?
Tylko pamięci podręcznej Redis serwera wprowadzone aktualizacji podczas czasu zaplanowanego okna obsługi. Okna obsługi nie ma zastosowania do aktualizacji platformy Azure lub aktualizacje systemu operacyjnego maszyny Wirtualnej.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Czy mogę zarządzanego, zaplanowanych aktualizacji przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?
Tak, można zarządzać zaplanowane aktualizacje za pomocą następujących poleceń cmdlet programu PowerShell:

* [Get-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/get-azurermrediscachepatchschedule)
* [New-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/new-azurermrediscachepatchschedule)
* [New-AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry)
* [Remove-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/remove-azurermrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Warstw cenowych, jakie można korzystać z funkcji aktualizacji harmonogram?
**Zaplanuj aktualizacje** funkcja jest dostępna tylko w warstwie cenowej premium.

## <a name="next-steps"></a>Kolejne kroki
* Poznaj więcej [warstwy premium usługi pamięć podręczna Redis Azure](cache-premium-tier-intro.md) funkcji.

