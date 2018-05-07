---
title: Odzyskiwanie po awarii i dystrybucji geograficznie w funkcjach trwałe - Azure
description: Więcej informacji na temat odzyskiwania po awarii i dystrybucji geograficznie w funkcjach trwałe.
services: functions
author: MS-Santi
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 8eb42a60045304416ec6aa1099a84b1e264c692d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="disaster-recovery-and-geo-distribution"></a>Odzyskiwanie po awarii i dystrybucja geograficzna

## <a name="overview"></a>Przegląd
W środowisku Azure Functions trwałe stan wszystkich są utrwalane w magazynie Azure. A [Centrum zadań](durable-functions-task-hubs.md) jest kontenerem logicznym dla zasobów usługi Azure Storage, które są używane dla orchestrations. Funkcje programu orchestrator i działania tylko mogą współdziałać ze sobą, jeśli należą do tego samego Centrum zadań.
Opisane scenariusze przedstawia opcje wdrażania, aby zwiększyć dostępność i zminimalizować czas przestoju podczas działania odzyskiwania po awarii.
Należy zauważyć, że te scenariusze są oparte na aktywny / pasywny konfiguracji, ponieważ są one narzuconego przez użycie usługi Azure Storage. Ten wzorzec składa się z wdrożeniem aplikacji kopii zapasowej — funkcja (pasywnego) w innym regionie. Menedżer ruchu będzie monitorował aplikacji głównej (aktywny) funkcji dostępności. Go zostaną przełączone awaryjnie do aplikacji funkcji wykonywania kopii zapasowej w przypadku awarii serwera podstawowego. Aby uzyskać więcej informacji, zobacz [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)w [metody routingu ruchu priorytet.](../traffic-manager/traffic-manager-routing-methods.md#a-name--priorityapriority-traffic-routing-method)


>[!NOTE]
>- Proponowana konfiguracja aktywny / pasywny zapewnia, że klient zawsze jest mogli wywoływać nowe orchestrations za pośrednictwem protokołu HTTP. Jednak wyniku o dwóch aplikacji funkcji Udostępnianie tego samego magazynu, przetwarzanie w tle będą dystrybuowane między ich rywalizuje wiadomości w tej samej kolejki. Ta konfiguracja wiąże kosztów wyjście dodany dla aplikacji funkcja dodatkowej.
>- Podstawowej Centrum konta i zadań magazynu są tworzone w regionie podstawowym i są współużytkowane przez obie aplikacje funkcji.
>- Wszystkie aplikacje funkcji, które są wdrażane nadmiarowo, muszą współużytkować tych samych kluczy dostępu do funkcji w przypadku aktywowania za pośrednictwem protokołu HTTP. Udostępnia środowisko uruchomieniowe Functions [interfejsu API zarządzania](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) konsumentów do programowane Dodawanie, usuwanie i aktualizowanie klawiszy funkcyjnych, który umożliwia.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenariusz 1 - o zrównoważonym obciążeniu obliczeń z magazynem udostępnionym
W przypadku niepowodzenia infrastruktury obliczeniowej na platformie Azure aplikacji funkcji mogą stać się niedostępne. Aby ograniczyć możliwość takie przestoju, w tym scenariuszu korzysta z dwóch aplikacji funkcji, wdrożyć w różnych regionach. Menedżer ruchu jest skonfigurowana do wykrywania problemów z podstawowej funkcji aplikacji oraz automatyczne przekierowywanie ruchu do aplikacji funkcji w regionie pomocniczym. Ta aplikacja funkcja udostępnia Centrum zadań i tego samego konta magazynu Azure. W związku z tym stan aplikacji funkcji nie zostanie utracony i zwykle wznowić pracę. Po przywróceniu kondycji w regionie podstawowym usługi Azure Traffic Manager zostanie automatycznie uruchomiony routingu żądań aplikacji w tej funkcji.


![Diagram przedstawiający Scenariusz 1.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Ma wiele zalet, korzystając z tego scenariusza wdrażania:
- W przypadku niepowodzenia infrastruktury obliczeniowej pracy można wznowić w pracy awaryjnej w regionie bez utraty stanu.
- Menedżer ruchu odpowiada on za automatycznej pracy awaryjnej w dobrej kondycji funkcji aplikacji automatycznie.
- Menedżer ruchu automatycznie ponownie nawiązuje ruch do aplikacji głównej funkcji po awarii został rozwiązany.

Jednak przy użyciu tego scenariusza należy wziąć pod uwagę:
- Jeśli funkcja aplikacji jest wdrażany za pomocą dedykowanego planu usługi aplikacji, replikowanie infrastruktury obliczeniowej w pracy w centrum danych powoduje zwiększenie kosztów.
- Ten scenariusz obejmuje awarie w infrastruktury obliczeniowej, ale konto magazynu, nadal jest pojedynczym punktem awarii dla funkcji aplikacji. W przypadku awarii pamięci masowej, aplikacji wystąpi przestoju.
- Jeśli funkcja aplikacji przeszła w tryb failover, będzie większe opóźnienia, ponieważ jego będą uzyskiwać dostęp do swojego konta magazynu w regionach.
- Uzyskiwanie dostępu do usługi magazynu w innym regionie, w którym jest zlokalizowany wiąże się wyższe koszty ze względu na ruch wychodzący ruch w sieci.
- W tym scenariuszu zależy od w usłudze Traffic Manager. Biorąc pod uwagę [działania Menedżera ruchu](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), może być trochę czasu, aż do aplikacji klienckiej, która wykorzystuje funkcję trwałego wymaga kwerendy ponownie adres aplikacji funkcji usługi Traffic Manager. 


## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenariusz 2 - o zrównoważonym obciążeniu obliczeń z magazynem regionalne
Poprzedni scenariusz dotyczy tylko w przypadku awarii w infrastrukturze obliczeń. W przypadku niepowodzenia usługi magazynu spowoduje awarię aplikacji funkcji.
Aby zapewnić ciągłe działanie funkcji trwałe, w tym scenariuszu użyto konta lokalnego magazynu w poszczególnych regionach, do której są wdrażane aplikacje funkcji.

![Diagram przedstawiający Scenariusz 2.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Takie podejście dodano ulepszenia w poprzednim scenariuszu:
- W przypadku niepowodzenia aplikacji funkcji Menedżera ruchu odpowiada on za awarii w regionie pomocniczym. Jednak ponieważ aplikacji funkcja opiera się na jego własnej konta magazynu, trwałe funkcje kontynuować pracę.
- Podczas pracy awaryjnej nie jest żadne dodatkowe opóźnienia w pracy awaryjnej w regionie, ponieważ funkcja aplikacji i konta magazynu są wspólnie.
- Błąd warstwy magazynu spowoduje błędów w trwałe funkcje, które z kolei wyzwoli przekierowanie do pracy nad regionu. Ponownie ponieważ funkcja aplikacji i magazynu są izolowane na region, trwałe funkcje będą nadal działać.
 
Ważne uwagi dotyczące tego scenariusza:
- Jeśli funkcja aplikacji jest wdrażany za pomocą dedykowanego planu usług aplikacji, replikowanie infrastruktury obliczeniowej w pracy w centrum danych powoduje zwiększenie kosztów.
- Tryb nie jest bieżący stan pracy awaryjnej, co oznacza, że wykonywanie i użyciu funkcji zakończy się niepowodzeniem. To aplikacja kliencka do ponawiania lub ponownie uruchomić pracę.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenariusz 3 - o zrównoważonym obciążeniu obliczeń z magazynem udostępnionym grs w warstwie
Ten scenariusz jest modyfikacja za pośrednictwem pierwszego scenariusza, wdrażanie konta magazynu udostępnionego. Główną różnicą, że konto magazynu jest tworzone z włączoną replikacją geograficzną.
Funkcjonalnie w tym scenariuszu zapewnia te same zalety, jak Scenariusz 1, ale umożliwia dodatkowe dane odzyskiwania zalety:
- Magazyn geograficznie nadmiarowy (GRS) i grs w warstwie dostępu do odczytu (RA-GRS) maksymalizację dostępności dla konta magazynu.
- W przypadku awarii region usługi magazynu jest jedną z możliwości operacji centrum danych określić, że magazyn musi można przełączyć w regionie pomocniczym. W takim przypadku dostępu do konta magazynu będzie przekierowywany niewidocznie z replikacją geograficzną kopią konta magazynu, bez interwencji użytkownika.
- W takim przypadku stan trwały funkcje zostaną zachowane do ostatniej replikacji konta magazynu, co kilka minut.
Podobnie jak w przypadku innych scenariuszy, brak istotnych kwestii:
- Tryb failover na serwer repliki jest wykonywane przez operatorów centrów danych i może upłynąć trochę czasu. Do tego czasu aplikacji funkcji spowoduje obniżenie awarii.
- Brak koszt zwiększone użycie kont magazynu z replikacją geograficzną.
- GRS występuje asynchronicznie. Niektórych najnowszej transakcji, które mogą zostać utracone z powodu opóźnień procesu replikacji.

![Diagram przedstawiający Scenariusz 3.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)


## <a name="next-steps"></a>Kolejne kroki

Możesz przeczytać dodatkowe informacje [projektowania wysokiej dostępności aplikacji przy użyciu RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)
