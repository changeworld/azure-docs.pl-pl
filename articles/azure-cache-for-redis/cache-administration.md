---
title: Administrowanie pamięć podręczna systemu Azure dla usługi Redis | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonywać zadania administracyjne, takie jak ponowne uruchomienie komputera oraz planowanie aktualizacji dla usługi Azure Cache dla usługi Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 81ef669b62c822e10d8bf5c45e58dd769c5dbeb9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60233034"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Administrowanie pamięć podręczna systemu Azure dla usługi Redis
W tym temacie opisano sposób wykonywania zadań administracyjnych, takich jak [ponowny rozruch](#reboot) i [Planowanie aktualizacji](#schedule-updates) dla pamięci podręcznej Azure dla wystąpienia usługi Redis.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Ponowne uruchamianie
**Ponowny rozruch** bloku pozwala na ponowne uruchomienie co najmniej jeden węzeł pamięci podręcznej. Ta możliwość ponownego uruchomienia umożliwia testowanie aplikacji pod kątem odporności, jeśli wystąpi awaria węzła pamięci podręcznej.

![Ponowne uruchamianie](./media/cache-administration/redis-cache-administration-reboot.png)

Zaznacz węzły, aby ponownie uruchomić, a następnie kliknij przycisk **ponowny rozruch**.

![Ponowne uruchamianie](./media/cache-administration/redis-cache-reboot.png)

Jeśli masz cache w warstwie premium przy włączonym klastrowaniu można wybrać które fragmentu pamięci podręcznej, ponowne uruchomienie.

![Ponowne uruchamianie](./media/cache-administration/redis-cache-reboot-cluster.png)

Aby ponownie uruchomić co najmniej jeden węzeł pamięci podręcznej, wybierz żądaną węzłów i kliknij **ponowny rozruch**. Jeśli masz cache w warstwie premium przy włączonym klastrowaniu, wybierz żądane fragmentami w celu ponownego rozruchu, a następnie kliknij przycisk **ponowny rozruch**. Po kilku minutach ponowne uruchomienie wybranych węzłów oraz że do trybu online później za kilka minut.

Wpływ na aplikacje klienckie różni się zależnie od węzły, które, ponowne uruchomienie.

* **Wzorzec** — w przypadku ponownego rozruchu węzła głównego pamięci podręcznej Redis Azure awaryjnie do węzła repliki i Awansuje ją do wzorca. Podczas pracy w trybie failover może być krótki czas, w którym połączenia może zakończyć się niepowodzeniem w pamięci podręcznej.
* **Element podrzędny** — w przypadku ponownego rozruchu węzła podrzędnego zwykle to żadnego wpływu na klientów pamięci podręcznej.
* **Zarówno węzła głównego i podrzędnego** — gdy węzłów pamięci podręcznej są ponownie uruchamiane, wszystkie dane zostaną utracone w pamięci podręcznej i połączenia z pamięcią podręczną niepowodzeniem aż do węzła podstawowego powróci do trybu online. Jeśli skonfigurowano [funkcji trwałości danych](cache-how-to-premium-persistence.md), najbardziej ostatnie kopia zapasowa zostanie przywrócona, gdy pamięć podręczna powróci do trybu online, ale wszelkie operacje zapisu pamięci podręcznej, które wystąpiły po najnowszej kopii zapasowej zostaną utracone.
* **Węzły w warstwie premium w pamięci podręcznej z włączonym klastrowaniu** — po ponownym uruchomieniu co najmniej jeden węzeł pamięci podręcznej w warstwie premium z klastrowaniem włączone, zachowanie dla wybranych węzłów jest taka sama jak po ponownym uruchomieniu odpowiedniego węzła lub węzłów nieklastrowanych pamięci podręcznej.

> [!IMPORTANT]
> Ponowne uruchomienie jest teraz dostępna dla wszystkich warstw cenowych.
> 
> 

## <a name="reboot-faq"></a>Ponowne uruchomienie — często zadawane pytania
* [Który węzeł reboot do testuję moją aplikację](#which-node-should-i-reboot-to-test-my-application)
* [Czy może uruchomić pamięci podręcznej, aby wyczyścić połączeń klientów?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Spowoduje utratę danych z przepełnieniu pamięci podręcznej Jeśli teraz uruchomię ponowny rozruch?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Czy może uruchomić przepełnieniu pamięci podręcznej przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Jakie ceny warstwy mogą korzystać z funkcji ponowny rozruch?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Który węzeł reboot do testuję moją aplikację
Aby testować odporność aplikacji przed awariami węzła podstawowego pamięci podręcznej, należy ponownie uruchomić **wzorzec** węzła. Aby testować odporność aplikacji przed awariami węzła pomocniczego, uruchom ponownie **podrzędny** węzła. Aby testować odporność aplikacji przed awariami całkowitej pamięci podręcznej, należy ponownie uruchomić **zarówno** węzłów.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Czy może uruchomić pamięci podręcznej, aby wyczyścić połączeń klientów?
Tak, jeśli ponownym uruchomieniu pamięci podręcznej zostaną wyczyszczone wszystkie połączenia klienta. Ponowne uruchamianie może być przydatne w przypadku, gdy wszystkie połączenia klienta zostały zużyte ze względu na błąd logiczny lub usterkę w aplikacji klienckiej. Każda warstwa cenowa ma inną [limitów połączeń dla klienta](cache-configure.md#default-redis-server-configuration) dla różnych rozmiarów i jeden raz te limity zostaną osiągnięte, żadnych połączeń klienta są akceptowane. Ponowne uruchamianie pamięci podręcznej umożliwia wyczyszczenie wszystkich połączeń klientów.

> [!IMPORTANT]
> Jeśli ponowne uruchomienie pamięć podręczną, aby wyczyścić połączeń klienta StackExchange.Redis automatycznie połączy się ponownie po powrocie do trybu online węzła Redis. Podstawowy problem nie zostanie rozwiązany, może nadal być zużyte połączeń klientów.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Spowoduje utratę danych z przepełnieniu pamięci podręcznej Jeśli teraz uruchomię ponowny rozruch?
Jeśli ponowne uruchomienie obu **wzorzec** i **podrzędny** węzłów, wszystkich danych w pamięci podręcznej (lub w tym fragmencie, jeśli używasz cache w warstwie premium przy włączonym klastrowaniu) mogą zostać utracone, ale nie jest to gwarantowane albo. Jeśli skonfigurowano [funkcji trwałości danych](cache-how-to-premium-persistence.md), najbardziej ostatniej kopii zapasowej zostaną przywrócone, gdy pamięć podręczna powróci do trybu online, ale wszelkie operacje zapisu pamięci podręcznej, które wystąpiły po utworzenia kopii zapasowej zostaną utracone.

Jeśli ponowne uruchomienie tylko jeden z węzłów, dane nie są zwykle utracone, ale nadal może być. Na przykład, jeśli ponownego uruchomienia węzła głównego i zapis w pamięci podręcznej jest w toku, dane z pamięci podręcznej zapisu jest utracone. Inny scenariusz utraty danych będzie, jeśli ponowne uruchomienie jednego węzła, a inny węzeł stanie się przestaną działać z powodu błędu w tym samym czasie. Aby uzyskać więcej informacji na temat możliwych przyczyn utraty danych, zobacz [co się stało z moimi danymi w usłudze Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Czy może uruchomić przepełnieniu pamięci podręcznej przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?
Tak, środowiska PowerShell uzyskać instrukcje, zobacz [ponowne uruchomienie pamięć podręczna systemu Azure dla usługi Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Jakie ceny warstwy mogą korzystać z funkcji ponowny rozruch?
Ponowny rozruch jest dostępny dla wszystkich warstw cenowych.

## <a name="schedule-updates"></a>Aktualizacje harmonogramu
**Lizacje** bloku pozwala na określenie okno obsługi pamięci podręcznej warstwy Premium. Jeśli okna obsługi jest określony, wszystkie aktualizacje serwera Redis są wprowadzane podczas tego okna. 

> [!NOTE] 
> Okna obsługi dotyczą tylko aktualizacje serwera Redis i nie Azure aktualizacji lub aktualizacje systemu operacyjnego maszyn wirtualnych, które hostują pamięci podręcznej.
> 
> 

![Aktualizacje harmonogramu](./media/cache-administration/redis-schedule-updates.png)

Aby określić okna obsługi, sprawdź odpowiednią dni i określ godzina rozpoczęcia okna konserwacji za każdy dzień i kliknij **OK**. Należy pamiętać, że czas okna obsługi jest w formacie UTC. 

Domyślny i minimum, okno konserwacji dla aktualizacji jest pięć godzin. Ta wartość nie jest można skonfigurować w witrynie Azure portal, ale można ją skonfigurować przy użyciu programu PowerShell `MaintenanceWindow` parametru [New AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) polecenia cmdlet. Aby uzyskać więcej informacji zobacz zaplanowanych aktualizacji przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania można zarządzać?


## <a name="schedule-updates-faq"></a>Zaplanuj aktualizacje — często zadawane pytania
* [Gdy aktualizacje wystąpić, jeśli funkcja Aktualizacje harmonogramu nie jest używany?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Jakiego rodzaju aktualizacje są wprowadzane podczas okna zaplanowanej konserwacji?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Zaplanowane aktualizacje przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania można zarządzać?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Jakie ceny warstwy można użyć funkcji Aktualizacje harmonogramu?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Gdy aktualizacje wystąpić, jeśli funkcja Aktualizacje harmonogramu nie jest używany?
Jeśli nie określisz okna obsługi aktualizacji może się w dowolnym momencie.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Jakiego rodzaju aktualizacje są wprowadzane podczas okna zaplanowanej konserwacji?
Tylko usługa Redis serwera, które aktualizacje są wprowadzane podczas okna zaplanowanej konserwacji. Okna obsługi nie ma zastosowania do aktualizacji platformy Azure lub aktualizacje systemu operacyjnego maszyny Wirtualnej.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Czy można zarządzać aktualizacjami zaplanowane przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?
Tak, można zarządzać zaplanowane aktualizacje za pomocą następujących poleceń cmdlet programu PowerShell:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Jakie ceny warstwy można użyć funkcji Aktualizacje harmonogramu?
**Lizacje** funkcja jest dostępna tylko w warstwy cenowej premium.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej [pamięci podręcznej Azure redis Cache w warstwie premium](cache-premium-tier-intro.md) funkcji.

