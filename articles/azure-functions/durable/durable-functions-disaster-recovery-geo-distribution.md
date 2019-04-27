---
title: Odzyskiwanie po awarii i dystrybucja geograficzna w funkcje trwałe - Azure
description: Więcej informacji na temat odzyskiwania po awarii i dystrybucja geograficzna w funkcje trwałe.
services: functions
author: MS-Santi
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 1363dd3c620789b9f3c8ce1dbe0892ee61d66051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741382"
---
# <a name="disaster-recovery-and-geo-distribution"></a>Odzyskiwanie po awarii i dystrybucja geograficzna

## <a name="overview"></a>Omówienie

W przypadku funkcji trwałych wszystkie stany są utrwalane w usłudze Azure Storage. A [Centrum zadań](durable-functions-task-hubs.md) to kontener logiczny dla zasobów usługi Azure Storage, które są używane do aranżacji. Funkcje programu orchestrator i działanie tylko można ze sobą współdziałać, jeśli należą do tego samego Centrum zadania.
Opisane scenariusze zaproponować opcjami wdrożenia w celu zwiększenia dostępności i skrócić czas przestoju podczas działania odzyskiwania po awarii.

Należy zauważyć, że te scenariusze są oparte na konfiguracji aktywne-pasywne, ponieważ są one sterowana przez użycie usługi Azure Storage. Ten wzorzec polega na wdrożeniu aplikacji funkcji wykonywania kopii zapasowej (pasywnego) w innym regionie. Usługi Traffic Manager będzie monitorować aplikację podstawową funkcją (aktywny) dla dostępności. Zakończy się niepowodzeniem przez aplikację funkcji tworzenia kopii zapasowej w przypadku niepowodzenia podstawowego. Aby uzyskać więcej informacji, zobacz [usługi Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)firmy [metody rozsyłania ruchu priorytetu.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

>[!NOTE]
>
> - Proponowane konfiguracji aktywne-pasywne gwarantuje, czy klient zawsze jest w stanie do wyzwolenia nowych mechanizmów za pośrednictwem protokołu HTTP. Jednak dzięki dwóm aplikacjom funkcji, udostępnianie tego samego magazynu, w wyniku przetwarzania w tle będą dystrybuowane między obie z nich, rywalizując o wiadomości w tej samej kolejki. Ta konfiguracja jest naliczana opłata w kosztach dodano ruchu wychodzącego dla aplikacji funkcji pomocniczej.
> - Podstawowe Centrum konta i zadania magazynu są tworzone w regionie podstawowym, a są współużytkowane przez obie aplikacje funkcji.
> - Wszystkie aplikacje funkcji, które są wdrażane nadmiarowo, muszą współużytkować tych samych kluczy dostępu do funkcji w przypadku aktywowania za pośrednictwem protokołu HTTP. Środowisko uruchomieniowe usługi Functions udostępnia [interfejsu API zarządzania](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) , który umożliwia klientom programowe Dodawanie, usuwanie i aktualizowanie klawiszy funkcyjnych.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenariusz 1 — obliczeniowe przy użyciu magazynu udostępnionego ze zrównoważonym obciążeniem

W przypadku awarii infrastruktury obliczeniowej na platformie Azure aplikacji funkcji mogą stać się niedostępne. Aby zminimalizować możliwości taki Przestój, w tym scenariuszu dwie aplikacje funkcji wdrożone w różnych regionach.
Usługa Traffic Manager jest skonfigurowana do wykrywania problemów w aplikacji funkcji podstawowej i automatyczne przekierowywanie ruchu do aplikacji funkcji w regionie pomocniczym. Tej aplikacji funkcji udostępnia Centrum zadań i tego samego konta usługi Azure Storage. W związku z tym stan aplikacji funkcji nie zostanie utracony i zwykle wznowić pracę. Po przywróceniu kondycji do regionu podstawowego usługi Azure Traffic Manager zostanie automatycznie uruchomiony kierowania żądań do tej aplikacji funkcji.

![Diagram przedstawiający Scenariusz 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Istnieje kilka korzyści, korzystając z tego scenariusza wdrażania:

- W przypadku niepowodzenia infrastrukturę obliczeniową pracy można wznowić w pracy nad regionu bez utraty stanu.
- Usługa Traffic Manager zajmuje się automatycznie z automatycznej pracy awaryjnej do aplikacji funkcji w dobrej kondycji.
- Usługa Traffic Manager automatycznie ponownie nawiązuje ruch do aplikacji głównej funkcji po awarii została poprawiona.

Jednak w tym scenariuszu należy wziąć pod uwagę:

- Jeśli aplikacja funkcji jest wdrażany za pomocą dedykowanego planu usługi App Service, replikowanie infrastrukturę obliczeniową w pracy za pośrednictwem centrum danych zwiększa koszty.
- Ten scenariusz obejmuje awarii na infrastrukturę obliczeniową, ale konto magazynu w dalszym ciągu być pojedynczym punktem awarii dla aplikacji funkcji. W przypadku awarii magazynu aplikacji wystąpi Przestój.
- Jeśli aplikacja funkcji jest w trybie Failover, powoduje to zwiększenie opóźnienia, ponieważ system będzie dostępu do jego konta magazynu w regionach.
- Uzyskiwanie dostępu do usługi storage w innym regionie, gdzie jest zlokalizowany spowoduje naliczenie wyższych kosztów ze względu na ruch wychodzący w sieci.
- W tym scenariuszu zależy od tego w usłudze Traffic Manager. Biorąc pod uwagę [jak działa usługa Traffic Manager](../../traffic-manager/traffic-manager-how-it-works.md), być może trochę czasu, aż aplikacja kliencka, która wykorzystuje funkcję trwałego musi ponownie zapytanie adres aplikacji funkcji z usługi Traffic Manager.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenariusz 2 — obliczeniowe przy użyciu magazynu regionalnego ze zrównoważonym obciążeniem

Poprzedni scenariusz dotyczy tylko w przypadku awarii w infrastrukturze obliczeniowej. W przypadku awarii usługi magazynu spowoduje awarię aplikacji funkcji.
W celu zapewnienia nieprzerwanego działania funkcje trwałe, w tym scenariuszu użyto konta lokalnego magazynu w każdym regionie, do której są wdrażane aplikacje funkcji.

![Diagram przedstawiający Scenariusz 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Takie podejście wprowadza ulepszenia w poprzednim scenariuszu:

- Jeśli aplikacja funkcji nie powiedzie się, usługa Traffic Manager dba o przechodzenie w tryb failover do regionu pomocniczego. Jednakże ponieważ aplikacja funkcji opiera się na swoje własne konta magazynu, trwałe funkcje kontynuować pracę.
- Podczas pracy awaryjnej istnieje nie dodatkowe opóźnienie w pracy w regionie, ponieważ aplikacja funkcji i konta magazynu mają wspólną lokalizację.
- Błąd warstwy magazynu spowoduje błędy w funkcje trwałe, które z kolei spowoduje wyzwolenie przekierowanie do pracy nad regionu. Ponownie ponieważ aplikacja funkcji i magazynu są izolowane na regiony, funkcje trwałe będą nadal działać.

Istotne zagadnienia dotyczące tego scenariusza:

- Jeśli aplikacja funkcji jest wdrażany za pomocą dedykowanego planu usługi App Service, replikowanie infrastrukturę obliczeniową w pracy za pośrednictwem centrum danych zwiększa koszty.
- Bieżący stan nie jest w trybie Failover, co oznacza, że wykonywanie i utworzono punkt kontrolny funkcji zakończy się niepowodzeniem. To Ty aplikacji klienckiej ponawiania/ponowne uruchomienie pracy.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenariusz 3 - obliczeniowe przy użyciu magazynu GRS udostępnionych ze zrównoważonym obciążeniem

Ten scenariusz jest modyfikacja za pośrednictwem pierwszego scenariusza, wykonania na koncie magazynu udostępnionego. Główną różnicą, że konto magazynu jest tworzone z włączoną replikacją geograficzną.
Funkcjonalnie w tym scenariuszu zapewnia te same zalety co scenariusza 1, ale umożliwia korzyści odzyskiwania dodatkowe dane:

- Magazyn geograficznie nadmiarowy (GRS) i GRS dostęp do odczytu (RA-GRS) maksymalizację dostępności dla konta magazynu.
- W przypadku awarii region, usługi magazynu jest jedną z możliwości, operacji w centrum danych określić, że magazyn należy przełączone w tryb failover w regionie pomocniczym. W tym przypadku dostępu do konta magazynu zostanie przekierowany przezroczyste replikacją geograficzną kopię na koncie magazynu bez interwencji użytkownika.
- W takim przypadku stan funkcje trwałe zostaną zachowane aż ostatniej replikacji konta magazynu, co kilka minut.

Podobnie jak w innych scenariuszach, istnieją ważne kwestie:

- Tryb failover do repliki jest wykonywane przez operatorów centrów danych i może upłynąć trochę czasu. Do tego czasu aplikacja funkcji będzie niekorzystnie wpłynąć na awarię.
- Istnieje zwiększona koszt używania kont magazynu z replikacją geograficzną.
- GRS występuje asynchronicznie. Niektóre z najnowszych transakcji mogą zostać utracone z powodu opóźnienia proces replikacji.

![Diagram przedstawiający Scenariusz 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj więcej o [projektowanie wysoko dostępnych aplikacji przy użyciu RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
