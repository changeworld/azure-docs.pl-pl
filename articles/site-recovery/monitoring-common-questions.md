---
title: Typowe pytania dotyczące monitorowania usługi Azure Site Recovery
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące monitorowania usługi Azure Site Recovery przy użyciu wbudowanego monitorowania i usługi Azure Monitor (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c1d30a9cdd2cd6ca288edd609a2e2e7bee9174d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68718264"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Typowe pytania dotyczące monitorowania odzyskiwania witryny

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące monitorowania usługi Azure [Site Recovery](site-recovery-overview.md), przy użyciu wbudowanego monitorowania odzyskiwania witryny i usługi Azure Monitor (log analytics).

## <a name="general"></a>Ogólne

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Czym rejestrowana jest wartość punktu odzyskiwania od najnowszego dostępnego punktu odzyskiwania?

Usługa Site Recovery używa wieloetapowego, asynchroniowego procesu do replikowania maszyn na platformę Azure.

- W przedostatnim kroku replikacji ostatnie zmiany na komputerze wraz z metadanymi są kopiowane do konta magazynu dziennika/pamięci podręcznej.
- Te zmiany, wraz z tagiem identyfikującym punkt odzyskiwalny, są zapisywane na koncie magazynu/dysku zarządzanym w regionie docelowym.
- Odzyskiwanie lokacji może teraz wygenerować punkt odzyskiwalny dla komputera.
- W tym momencie RPO został spełniony w przypadku zmian przesłanych do tej pory na konto magazynu. Innymi słowy, rpo maszyny w tym momencie jest równa czas, który upłynął od sygnatury czasowej odpowiadającej punktowi odzyskiwania.
- Teraz usługa Site Recovery wybiera przekazane dane z konta magazynu i stosuje go do dysków replik utworzonych dla komputera.
- Odzyskiwanie lokacji następnie generuje punkt odzyskiwania i udostępnia ten punkt do odzyskiwania po przemijazie awaryjnym.
- W związku z tym ostatni dostępny punkt odzyskiwania wskazuje sygnaturę czasową odpowiadającą ostatniemu punktowi odzyskiwania, który został już przetworzony i zastosowany do dysków repliki.


Niepoprawny czas systemowy na komputerze źródłowym replikacji lub na serwerach infrastruktury lokalnej spowoduje wypaczenie obliczonej wartości RPO. Aby uzyskać dokładne raportowanie RPO, upewnij się, że zegar systemowy jest dokładny na wszystkich serwerach i komputerach.



## <a name="inbuilt-site-recovery-logging"></a>Wbudowane rejestrowanie odzyskiwania witryny


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Dlaczego liczba maszyn wirtualnych w widoku infrastruktury przechowalni różni się od łącznej liczby wyświetlanej w elementów replikowanych?

Widok infrastruktury przechowalni jest objęty zakresem według scenariuszy replikacji. Tylko maszyny w aktualnie wybranym scenariuszu replikacji są uwzględniane w liczbie dla widoku. Ponadto liczymy tylko maszyny wirtualne, które są skonfigurowane do replikacji na platformie Azure. Maszyny w razie pracy awaryjnej lub maszyny replikujące się z powrotem do lokacji lokalnej nie są liczone w widoku.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Dlaczego liczba zreplikowanych elementów w programie Essentials różni się od całkowitej liczby zreplikowanych elementów na pulpicie nawigacyjnym?

Tylko maszyny, dla których replikacja początkowa została zakończona, są uwzględniane w liczbie pokazanej w pliku Essentials. Suma zreplikowanych elementów obejmuje wszystkie maszyny w przechowalni, w tym te, dla których replikacja początkowa jest obecnie w toku.

## <a name="azure-monitor-logging"></a>Rejestrowanie w usłudze Azure Monitor


### <a name="how-often-does-site-recovery-send-diagnostic-logs-to-azure-monitor-log"></a>Jak często usługa Site Recovery wysyła dzienniki diagnostyczne do dziennika usługi Azure Monitor? 

- Usługa AzureSiteRecoveryReplicationStats i AzureSiteRecoveryRecoveryPoints są wysyłane co 15 minut.  
- Usługa AzureSiteRecoveryReplicationDataUploadRate i AzureSiteRecoveryProtectedDiskDataChurn są wysyłane co pięć minut. 
- Usługa AzureSiteRecoveryJobs jest wysyłana w wyzwalaczu i zakończeniu zadania.
- Usługa AzureSiteRecoveryEvents jest wysyłana za każdym razem, gdy generowane jest zdarzenie. 
- AzureSiteRecoveryReplicatedItems jest wysyłany za każdym razem, gdy nastąpi zmiana środowiska. Zazwyczaj czas odświeżania danych wynosi 15 minut po zmianie. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Jak długo dane są przechowywane w dziennikach usługi Azure Monitor? 

Domyślnie przechowywanie jest przez 31 dni. Okres można wydłużyć w sekcji **Użycie i Koszt szacowany** w obszarze roboczym Usługa Log Analytics. Kliknij na **Przechowywanie danych**i wybierz zakres.

### <a name="whats-the-size-of-the-diagnostic-logs"></a>Jaki jest rozmiar dzienników diagnostycznych? 

Zazwyczaj rozmiar dziennika wynosi 15-20 KB. 


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak monitorować za pomocą [wbudowanego monitorowania usługi Site Recovery](site-recovery-monitor-and-troubleshoot.md)lub usługi Azure [Monitor.](monitor-log-analytics.md)


