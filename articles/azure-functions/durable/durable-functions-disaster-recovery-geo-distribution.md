---
title: Odzyskiwanie po awarii i geodezyjne funkcje trwałe platformy Azure
description: Dowiedz się więcej o odzyskiwaniu po awarii i dystrybucji geograficznej w funkcji trwałych.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 7951f216143bef0d48a6b751beff3f8f4316b9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433333"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Odzyskiwanie po awarii i dystrybucja geograficzna w usłudze Azure Durable Functions

W funkcji trwałe wszystkie stany jest utrwalony w usłudze Azure Storage. [Centrum zadań](durable-functions-task-hubs.md) jest kontenerem logicznym dla zasobów usługi Azure Storage, które są używane do aranżacji. Funkcje koordynatora i działania mogą wchodzić ze sobą w interakcje tylko wtedy, gdy należą do tego samego centrum zadań.
Opisane scenariusze proponują opcje wdrażania, aby zwiększyć dostępność i zminimalizować przestoje podczas działań odzyskiwania po awarii.

Należy zauważyć, że te scenariusze są oparte na konfiguracjach aktywnych pasywnych, ponieważ są one prowadzone przez użycie usługi Azure Storage. Ten wzorzec polega na wdrażaniu aplikacji funkcji kopii zapasowej (pasywnej) w innym regionie. Usługa Traffic Manager będzie monitorować dostępność aplikacji funkcji podstawowej (aktywnej). Zostanie przejęty awaryjnie do aplikacji funkcji tworzenia kopii zapasowych, jeśli podstawowy nie powiedzie się. Aby uzyskać więcej informacji, zobacz [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)'s [Priority Traffic-Routing Metody.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

>[!NOTE]
>
> - Proponowana konfiguracja active-pasywna zapewnia, że klient jest zawsze w stanie wyzwolić nowe aranżacji za pośrednictwem protokołu HTTP. Jednak w wyniku posiadania dwóch aplikacji funkcji współużytkujących ten sam magazyn, przetwarzanie w tle będzie dystrybuowane między nimi, konkurując o wiadomości w tych samych kolejkach. Ta konfiguracja wiąże się z dodatkowymi kosztami wychodzącymi dla aplikacji funkcji pomocniczej.
> - Podstawowe konto magazynu i centrum zadań są tworzone w regionie podstawowym i są współużytkowane przez obie aplikacje funkcji.
> - Wszystkie aplikacje funkcji, które są nadmiarowo wdrożone, muszą współużytkują te same klucze dostępu do funkcji w przypadku aktywacji za pomocą protokołu HTTP. Środowisko wykonawcze funkcji udostępnia [interfejs API zarządzania,](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) który umożliwia konsumentom programowo dodawanie, usuwanie i aktualizowanie kluczy funkcyjnych.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenariusz 1 — obliczenie z równoważenia obciążenia z magazynem udostępnionym

Jeśli infrastruktura obliczeniowa na platformie Azure ulegnie awarii, aplikacja funkcji może stać się niedostępna. Aby zminimalizować możliwość takiego przestoju, w tym scenariuszu używa dwóch aplikacji funkcji wdrożonych w różnych regionach.
Usługa Traffic Manager jest skonfigurowana do wykrywania problemów w aplikacji funkcji podstawowej i automatycznego przekierowywania ruchu do aplikacji funkcji w regionie pomocniczym. Ta aplikacja funkcji współudziała to samo konto usługi Azure Storage i Centrum zadań. W związku z tym stan aplikacji funkcji nie jest tracona i praca może zostać wznowiona normalnie. Po przywróceniu kondycji do regionu podstawowego usługa Azure Traffic Manager automatycznie rozpocznie routing żądań do tej aplikacji funkcji.

![Diagram przedstawiający scenariusz 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Istnieje kilka korzyści podczas korzystania z tego scenariusza wdrażania:

- Jeśli infrastruktura obliczeniowa ulegnie awarii, praca może zostać wznowiona w regionie po awarii bez utraty stanu.
- Usługa Traffic Manager automatycznie zajmuje się automatycznym trybem awaryjnym w aplikacji funkcji w dobrej kondycji.
- Usługa Traffic Manager automatycznie ponownie ustanawia ruch do aplikacji funkcji podstawowej po poprawie awarii.

Jednak przy użyciu tego scenariusza należy wziąć pod uwagę:

- Jeśli aplikacja funkcji jest wdrażana przy użyciu dedykowanego planu usługi aplikacji, replikowanie infrastruktury obliczeniowej w centrum danych w trybie fail over zwiększa koszty.
- Ten scenariusz obejmuje awarie w infrastrukturze obliczeniowej, ale konto magazynu nadal jest pojedynczym punktem awarii dla aplikacji funkcji. W przypadku awarii magazynu aplikacja cierpi na przestoje.
- Jeśli aplikacja funkcji jest w pracy awaryjnej, będzie zwiększone opóźnienie, ponieważ będzie ona uzyskać dostęp do swojego konta magazynu w różnych regionach.
- Dostęp do usługi magazynu z innego regionu, w którym znajduje się, wiąże się z wyższymi kosztami z powodu ruchu wychodzącego z sieci.
- Ten scenariusz zależy od menedżera ruchu. Biorąc pod [uwagę, jak działa usługa Traffic Manager,](../../traffic-manager/traffic-manager-how-it-works.md)może ugięć się trochę czasu, dopóki aplikacja kliencka, która zużywa funkcję trwałe, musi ponownie zbadać adres aplikacji funkcji z usługi Traffic Manager.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenariusz 2 — obliczenie z równoważenia obciążenia z pamięcią masową regionalną

Poprzedni scenariusz obejmuje tylko przypadek awarii w infrastrukturze obliczeniowej. Jeśli usługa magazynu nie powiedzie się, spowoduje to awarię aplikacji funkcji.
Aby zapewnić ciągłą pracę funkcji trwałe, w tym scenariuszu używa konta magazynu lokalnego w każdym regionie, do którego są wdrażane aplikacje funkcji.

![Diagram przedstawiający scenariusz 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Takie podejście dodaje ulepszenia w poprzednim scenariuszu:

- Jeśli aplikacja funkcji nie powiedzie się, Usługa Traffic Manager zajmuje się w pełni w przypadku awarii do regionu pomocniczego. Jednak ponieważ aplikacja funkcji opiera się na własnym koncie magazynu, trwałe funkcje nadal działają.
- Podczas pracy awaryjnej nie ma żadnych dodatkowych opóźnień w regionie trybu fail over, ponieważ aplikacja funkcji i konto magazynu są zlokalizowane.
- Awaria warstwy magazynu spowoduje błędy w trwałych funkcji, które z kolei wyzwoli przekierowanie do regionu trybu fail over. Ponownie, ponieważ aplikacja funkcji i magazynu są izolowane na region, trwałe funkcje będą nadal działać.

Ważne zagadnienia dotyczące tego scenariusza:

- Jeśli aplikacja funkcji jest wdrażany przy użyciu dedykowanego planu AppService, replikowanie infrastruktury obliczeniowej w centrum danych w trybie fail over zwiększa koszty.
- Bieżący stan nie jest awaryjnie, co oznacza, że wykonywanie i checkpointed funkcje zakończy się niepowodzeniem. To do aplikacji klienckiej, aby ponowić próbę/ponownie uruchomić pracę.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenariusz 3 — obliczenie z równoważenia obciążenia z magazynem współdzielonym GRS

Ten scenariusz jest modyfikacją w pierwszym scenariuszu, implementując konto magazynu udostępnionego. Główna różnica, że konto magazynu jest tworzony z włączonym replikacji geograficznej.
Funkcjonalnie ten scenariusz zapewnia takie same zalety jak scenariusz 1, ale umożliwia dodatkowe korzyści odzyskiwania danych:

- Magazyn geograficznie nadmiarowy (GRS) i GRS dostępu do odczytu (RA-GRS) maksymalizują dostępność konta magazynu.
- Jeśli występuje awaria regionu usługi magazynu, jedną z możliwości jest to, że operacje centrum danych określają, że magazyn musi być awaryjnie w regionie pomocniczym. W takim przypadku dostęp do konta magazynu zostanie przekierowany w sposób przezroczysty do replikowanej geograficznie kopii konta magazynu, bez interwencji użytkownika.
- W takim przypadku stan trwałych funkcji zostaną zachowane do ostatniej replikacji konta magazynu, który występuje co kilka minut.

Podobnie jak w przypadku innych scenariuszy, istnieją ważne kwestie:

- Przeładuj awaryjnie do repliki jest wykonywana przez operatorów centrów danych i może upłynąć trochę czasu. Do tego czasu aplikacja funkcji będzie cierpieć awarii.
- Istnieje zwiększony koszt korzystania z kont magazynu replikowanego geograficznie.
- GRS występuje asynchronicznie. Niektóre z najnowszych transakcji mogą zostać utracone z powodu opóźnienia procesu replikacji.

![Diagram przedstawiający scenariusz 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat [projektowania aplikacji o wysokiej dostępności za pomocą RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
