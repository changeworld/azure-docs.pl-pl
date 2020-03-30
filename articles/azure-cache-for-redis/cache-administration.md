---
title: Jak administrować pamięcią podręczną platformy Azure dla redis
description: Dowiedz się, jak wykonywać zadania administracyjne, takie jak ponowne uruchamianie i planowanie aktualizacji usługi Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 69686cad20bc4ce70bff2a92a216c9430522c301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278846"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Jak administrować pamięcią podręczną platformy Azure dla redis
W tym temacie opisano sposób wykonywania zadań [administracyjnych,](#reboot) takich jak ponowne uruchamianie i [planowanie aktualizacji](#schedule-updates) dla pamięci podręcznej platformy Azure dla wystąpień Redis.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Ponowne uruchamianie
**Blok restartu** umożliwia ponowne uruchomienie jednego lub więcej węzłów pamięci podręcznej. Ta funkcja ponownego uruchamiania umożliwia testowanie aplikacji pod kątem odporności w przypadku awarii węzła pamięci podręcznej.

![Ponowne uruchamianie](./media/cache-administration/redis-cache-administration-reboot.png)

Wybierz węzły do ponownego uruchomienia i kliknij przycisk **Uruchom ponownie**.

![Ponowne uruchamianie](./media/cache-administration/redis-cache-reboot.png)

Jeśli masz pamięć podręczną premium z włączoną klastrowania, można wybrać, które fragmenty pamięci podręcznej do ponownego uruchomienia.

![Ponowne uruchamianie](./media/cache-administration/redis-cache-reboot-cluster.png)

Aby ponownie uruchomić jeden lub więcej węzłów pamięci podręcznej, wybierz żądane węzły i kliknij przycisk **Uruchom ponownie**. Jeśli masz włączoną pamięć podręczną premium z włączonym klastrem, wybierz żądane fragmenty do ponownego uruchomienia, a następnie kliknij przycisk **Uruchom ponownie**. Po kilku minutach wybrane węzły ponownie uruchomią się ponownie i powrócą do trybu online kilka minut później.

Wpływ na aplikacje klienckie różni się w zależności od węzłów, które można ponownie uruchomić.

* **Wzorzec** — po ponownym uruchomieniu węzła głównego usługa Azure Cache for Redis przechodzi w tryb fail to the replica node i promuje go do wzorca. Podczas tej pracy awaryjnej może istnieć krótki interwał, w którym połączenia mogą zakończyć się niepowodzeniem do pamięci podręcznej.
* **Slave** — po ponownym uruchomieniu węzła podrzędnego, zazwyczaj nie ma wpływu na klientów pamięci podręcznej.
* **Zarówno wzorzec, jak i podrzędny** — po ponownym uruchomieniu obu węzłów pamięci podręcznej wszystkie dane zostaną utracone w pamięci podręcznej, a połączenia z pamięcią podręczną nie powiodą się, dopóki węzeł podstawowy nie powróci do trybu online. Jeśli skonfigurowano [trwałość danych,](cache-how-to-premium-persistence.md)najnowsza kopia zapasowa jest przywracana, gdy pamięć podręczna wraca do trybu online, ale każda pamięć podręczna zapisuje, która wystąpiła po utracie ostatniej kopii zapasowej.
* **Węzły pamięci podręcznej premium z włączoną klastrowania** — po ponownym uruchomieniu jednego lub więcej węzłów pamięci podręcznej premium z włączonym klastrem zachowanie dla wybranych węzłów jest takie samo jak po ponownym uruchomieniu odpowiedniego węzła lub węzłów pamięci podręcznej nieklastrowanej.

## <a name="reboot-faq"></a>Często zadawane pytania dotyczące ponownego uruchamiania
* [Który węzeł należy ponownie uruchomić, aby przetestować aplikację?](#which-node-should-i-reboot-to-test-my-application)
* [Czy mogę ponownie uruchomić pamięć podręczną, aby wyczyścić połączenia klientów?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Czy jeśli uruchomię ponownie komputer, utracę dane z pamięci podręcznej?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Czy mogę ponownie uruchomić pamięć podręczną przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Który węzeł należy ponownie uruchomić, aby przetestować aplikację?
Aby przetestować odporność aplikacji na awarię węzła podstawowego pamięci podręcznej, uruchom ponownie węzeł **główny.** Aby przetestować odporność aplikacji na awarię węzła pomocniczego, uruchom ponownie węzeł **podrzędny.** Aby przetestować odporność aplikacji przed całkowitym niepowodzeniem pamięci podręcznej, uruchom ponownie **oba** węzły.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Czy mogę ponownie uruchomić pamięć podręczną, aby wyczyścić połączenia klientów?
Tak, po ponownym uruchomieniu pamięci podręcznej wszystkie połączenia klienta są wyczyszczone. Ponowne uruchomienie może być przydatne w przypadku, gdy wszystkie połączenia klienta są używane z powodu błędu logicznego lub błędu w aplikacji klienckiej. Każda warstwa cenowa ma różne [limity połączeń klienta](cache-configure.md#default-redis-server-configuration) dla różnych rozmiarów, a po osiągnięciu tych limitów nie są akceptowane żadne połączenia klientów. Ponowne uruchomienie pamięci podręcznej umożliwia wyczyszczenie wszystkich połączeń klienta.

> [!IMPORTANT]
> Jeśli ponownie uruchomić pamięć podręczną, aby wyczyścić połączenia klienta, StackExchange.Redis automatycznie łączy się ponownie, gdy węzeł Redis jest z powrotem w trybie online. Jeśli podstawowy problem nie zostanie rozwiązany, połączenia klienta mogą być nadal używane.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Czy jeśli uruchomię ponownie komputer, utracę dane z pamięci podręcznej?
Po ponownym uruchomieniu węzłów **Master** i **Slave** wszystkie dane w pamięci podręcznej (lub w tym fragment, jeśli używasz pamięci podręcznej premium z włączonym klastrem) mogą zostać utracone, ale nie jest to gwarantowane. Jeśli skonfigurowano [trwałość danych,](cache-how-to-premium-persistence.md)najnowsza kopia zapasowa zostanie przywrócona, gdy pamięć podręczna powróci do trybu online, ale wszelkie zapisy pamięci podręcznej, które wystąpiły po wykonaniu kopii zapasowej, zostaną utracone.

Jeśli ponownie uruchomisz ponownie jeden z węzłów, dane zazwyczaj nie zostaną utracone, ale nadal mogą być. Na przykład, jeśli węzeł główny jest ponownie uruchomiony i zapis pamięci podręcznej jest w toku, dane z zapisu pamięci podręcznej są tracone. Innym scenariuszem utraty danych byłoby ponowne uruchomienie jednego węzła, a drugi węzeł z powodu awarii w tym samym czasie. Aby uzyskać więcej informacji na temat możliwych przyczyn utraty danych, zobacz [Co się stało z moimi danymi w Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Czy mogę ponownie uruchomić pamięć podręczną przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?
Tak, w przypadku instrukcji programu PowerShell zobacz [Aby ponownie uruchomić pamięć podręczną platformy Azure dla programu Redis.](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis)

## <a name="schedule-updates"></a>Aktualizacje harmonogramu
**Bloku Aktualizacje harmonogramu** umożliwia wyznaczenie okna konserwacji dla wystąpienia pamięci podręcznej. Po określeniu okna konserwacji wszystkie aktualizacje serwera Redis są dokonywane w tym oknie. 

> [!NOTE] 
> Okno konserwacji dotyczy tylko aktualizacji serwera Redis, a nie żadnych aktualizacji platformy Azure lub aktualizacji systemu operacyjnego maszyn wirtualnych obsługujących pamięć podręczną.
>

![Aktualizacje harmonogramu](./media/cache-administration/redis-schedule-updates.png)

Aby określić okno konserwacji, sprawdź żądane dni i określ godzinę rozpoczęcia okna konserwacji dla każdego dnia, a następnie kliknij przycisk **OK**. Należy zauważyć, że czas okna konserwacji jest w czasie UTC. 

Domyślne i minimalne okno konserwacji aktualizacji to pięć godzin. Ta wartość nie jest konfigurowalna z witryny Azure portal, ale `MaintenanceWindow` można ją skonfigurować w programie PowerShell przy użyciu parametru polecenia cmdlet [New-AzRedisCacheScheduleEntry.](/powershell/module/az.rediscache/new-azrediscachescheduleentry) Aby uzyskać więcej informacji, zobacz Czy mogę zarządzać zaplanowanymi aktualizacjami przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?

## <a name="schedule-updates-faq"></a>Często zadawane pytania dotyczące aktualizacji harmonogramu
* [Kiedy aktualizacje występują, jeśli nie korzystam z funkcji aktualizacji harmonogramu?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Jakiego typu aktualizacje są dokonywane podczas okna zaplanowanej konserwacji?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Czy mogę zarządzać zaplanowanymi aktualizacjami przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Kiedy aktualizacje występują, jeśli nie korzystam z funkcji aktualizacji harmonogramu?
Jeśli nie określisz okna konserwacji, aktualizacje mogą być wykonane w dowolnym momencie.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Jakiego typu aktualizacje są dokonywane podczas okna zaplanowanej konserwacji?
Tylko aktualizacje serwera Redis są dokonywane podczas okna zaplanowanej konserwacji. Okno konserwacji nie ma zastosowania do aktualizacji platformy Azure lub aktualizacji systemu operacyjnego maszyny Wirtualnej.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Czy mogę zarządzać zaplanowanymi aktualizacjami przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?
Tak, zaplanowane aktualizacje można zarządzać przy użyciu następujących poleceń cmdlet programu PowerShell:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [Nowy-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [Nowy-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Usuń-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Następne kroki
* Poznaj więcej [funkcji warstwy premium usługi Azure Cache dla wersji Premium.](cache-premium-tier-intro.md)

