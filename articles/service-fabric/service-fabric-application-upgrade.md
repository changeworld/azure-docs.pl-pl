---
title: Uaktualnianie aplikacji usługi Service Fabric | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wprowadzenie do uaktualniania aplikacji usługi Service Fabric, w tym wybierając tryby uaktualnienia i przeprowadzanie kontroli kondycji.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: e2b407733bcab7bc854e8e3703e53eb474f3425b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615074"
---
# <a name="service-fabric-application-upgrade"></a>Uaktualnianie aplikacji usługi Service Fabric
Aplikacja usługi Azure Service Fabric jest kolekcja usług. Podczas uaktualniania usługi Service Fabric porównuje nowy [manifest aplikacji](service-fabric-application-and-service-manifests.md) za pomocą poprzedniej wersji i ustala, usługi w aplikacji wymagają aktualizacji. Usługa Service Fabric porównuje wersji liczb w usłudze manifesty z numerami wersji w poprzedniej wersji. Jeśli usługa nie została zmieniona, czy usługa nie jest uaktualniany.

## <a name="rolling-upgrades-overview"></a>Omówienie uaktualnienia stopniowego
W stopniowego uaktualnienia aplikacji uaktualnienia odbywa się etapami. Na każdym etapie uaktualnienia jest stosowany do podzbioru węzłów w klastrze o nazwie domeny aktualizacji. W rezultacie aplikacja pozostaje dostępna całego procesu uaktualniania. Podczas uaktualniania klastra może zawierać kombinację starej i nowej wersji.

Z tego powodu dwie wersje musi być w przód i Wstecz zgodne. Jeśli nie są zgodne, administrator aplikacji jest odpowiedzialny za przemieszczania uaktualnienia fazy w celu zapewnienia dostępności. W przypadku uaktualniania fazy pierwszym krokiem jest uaktualniany do pośredniego wersję aplikacji, która jest zgodna z poprzednią wersją. Drugim krokiem jest uaktualnienie wersji ostatecznej, dzieli zgodności z wersją przed aktualizacją, ale jest zgodna z wersją pośrednich.

Domeny aktualizacji są określone w manifeście klastra, po skonfigurowaniu klastra. Domeny aktualizacji nie otrzymywać aktualizacje w określonej kolejności. Domena aktualizacji to jednostka logiczna wdrażania aplikacji. Domeny aktualizacji umożliwiają usług, które mają pozostać w wysokiej dostępności, podczas uaktualniania.

Uaktualnienia stopniowego nie są możliwe, jeśli uaktualnianie jest stosowany do wszystkich węzłów w klastrze, co jest wymagane, gdy aplikacja ma tylko jedną domenę aktualizacji. To podejście nie jest zalecane, ponieważ usługa ulegnie awarii, a nie jest dostępna w czasie uaktualniania. Ponadto platforma Azure nie zapewnia żadnej gwarancji, gdy klaster jest skonfigurowany przy użyciu tylko jedna domena aktualizacji.

Po zakończeniu uaktualniania, wszystkie usługi i replicas(instances) może pozostać w tej samej wersji — czyli Jeśli uaktualnienie zakończy się powodzeniem, zostaną one zaktualizowane do nowej wersji; Jeśli uaktualnienie nie powiedzie się i jest wycofana, ich będzie z powrotem obniżyć do starej wersji.

## <a name="health-checks-during-upgrades"></a>Kontrole kondycji podczas uaktualniania
W przypadku uaktualnienia zasady dotyczące kondycji, które muszą być ustawione lub mogą zostać użyte wartości domyślne. Uaktualnienie jest określane jako pomyślne, po uaktualnieniu wszystkich domen aktualizacji w ramach określonego limitu czasu i wszystkich domen aktualizacji uważa, że działa prawidłowo.  Domeny aktualizacji w dobrej kondycji oznacza o tym, że domena aktualizacji jest przekazywany kontrole kondycji określonym w zasadach kondycji. Na przykład zasad kondycji może uzasadniają, że wszystkie usługi w ramach wystąpienia aplikacji musi być *dobrej kondycji*, zdefiniowaną kondycji przez usługę Service Fabric.

Zasady dotyczące kondycji i kontrole podczas uaktualniania przez usługę Service Fabric są niezależne od usług i aplikacji. Oznacza to, że są wykonywane żadne testy specyficzne dla usługi.  Na przykład usługa może być wymagane przepływności, ale usługi Service Fabric nie ma informacji, aby sprawdzić przepływności. Zapoznaj się [artykuły kondycji](service-fabric-health-introduction.md) dla testów, które są wykonywane. Kontrole wykonywane podczas testów uaktualniania dołączenia do tego, czy pakiet aplikacji zostały skopiowane poprawnie, czy wystąpienie zostało uruchomione i tak dalej.

Kondycja aplikacji jest agregacją jednostki podrzędne aplikacji. Krótko mówiąc Usługa Service Fabric ocenia kondycję aplikacji za pomocą kondycji, który jest zgłaszany w aplikacji. Oblicza również kondycję wszystkich usług dla aplikacji w ten sposób. Dodatkowo usługi Service Fabric ocenia kondycję usług aplikacji przez agregowanie ich elementy podrzędne, takie jak repliki usługi kondycji. Po spełnieniu zasad dotyczących kondycji aplikacji, można kontynuować uaktualniania. Naruszenia zasad dotyczących kondycji uaktualnienia aplikacji nie powiedzie się.

## <a name="upgrade-modes"></a>Tryby uaktualnienia
Tryb, w którym firma Microsoft zaleca dla uaktualnienie aplikacji jest monitorowane trybu, który jest powszechnie używany tryb. Tryb monitorowanych przeprowadza uaktualnienie w jedna domena aktualizacji, a jeśli kontroli kondycji wszystkich — dostęp próbny (zgodnie z zasadami określone), przechodzi do następnej domeny aktualizacji automatycznie.  Kontrole kondycji zakończyć się niepowodzeniem i/lub Osiągnięto limity czasu, uaktualnienia albo zostanie wycofana w domenie aktualizacji, czy tryb jest zmieniany na niemonitorowanej ręcznie. Można skonfigurować uaktualnienia można wybrać jedną z tych dwóch trybów uaktualnienia nie powiodło się. 

Niemonitorowane ręcznego trybu wymaga ręcznej interwencji po każdym uaktualnieniu w domenie usługi aktualizacji, aby rozpocząć uaktualnianie na następnej domeny aktualizacji. Są wykonywane żadne testy kondycji usługi Service Fabric. Administrator wykonuje sprawdzanie kondycji lub stanu przed rozpoczęciem uaktualniania w następnej domeny aktualizacji.

## <a name="upgrade-default-services"></a>Uaktualnianie usług domyślnych
Niektóre domyślne parametry usługi zdefiniowane w [manifest aplikacji](service-fabric-application-and-service-manifests.md) także zostaną uaktualnione w ramach uaktualniania aplikacji. Parametry usługi, obsługujące zmieniany przy użyciu [ServiceFabricService aktualizacji](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) można zmienić w ramach uaktualnienia. Zachowanie zmiana domyślnych usług podczas uaktualniania aplikacji jest następujący:

1. Domyślne usługi w nowy manifest aplikacji, które jeszcze nie istnieje w klastrze są tworzone.
2. Domyślne usługi, które istnieją w manifestach poprzedniej i nowej aplikacji zostaną zaktualizowane. Parametry domyślnej usługi w nowy manifest aplikacji zastąpić parametry istniejącej usługi. Uaktualnianie aplikacji spowoduje wycofanie automatycznie, jeśli aktualizacja usługi domyślnej nie powiedzie się.
3. Domyślne usługi, które nie istnieją w nowy manifest aplikacji są usuwane, jeśli nie istnieją w klastrze. **Należy pamiętać, że usunięcie domyślnej usługi spowoduje usunięcie wszystkie te usługi w stan i nie można cofnąć.**

Podczas uaktualniania aplikacji zostanie wycofana, domyślne parametry usługi są przywracane do jego stare wartości przed Uaktualnianie zostało rozpoczęte, ale usunięto usługi nie może być ponownie utworzony przy użyciu ich poprzedni stan.

> [!TIP]
> [Parametr EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) ustawienia konfiguracji klastra musi być *true* Aby włączyć reguły 2) i 3) powyżej (aktualizacja usługi domyślnej i usuwanie). Ta funkcja jest obsługiwana w usłudze Service Fabric w wersji 5.5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Uaktualnianie wieloma aplikacjami za pomocą punktów końcowych HTTPS
Należy zachować ostrożność nie należy używać **tego samego portu** dotyczy to różnych wystąpień tej samej aplikacji przy użyciu protokołu HTTP**S**. Przyczyną jest to, że usługi Service Fabric nie będzie można uaktualnić certyfikatu dla jednego z wystąpień aplikacji. Na przykład, jeśli chcesz, aby aplikacja 1 lub aplikacji 2 zarówno uaktualnić ich cert 1 do 2 certyfikatów. W przypadku uaktualniania usługi Service Fabric może wyczyściliśmy rejestracji certyfikatu 1 w pliku http.sys nawet, jeśli inna aplikacja nadal jest używany. Aby tego uniknąć, Usługa Service Fabric wykrywa, że nie jest już inne wystąpienie aplikacji zarejestrowanych na tym porcie za pomocą certyfikatu (z powodu sterownik http.sys), a nie operacji.

Dlatego usługi Service Fabric nie obsługuje uaktualniania w dwóch różnych usług za pomocą **tego samego portu** w różne wystąpienia aplikacji. Innymi słowy nie można użyć tego samego certyfikatu na różne usługi, w tym samym porcie. Musisz mieć certyfikat udostępnione w tym samym porcie, musisz upewnić się, że usługi są umieszczane na różnych maszynach z ograniczeniami dotyczącymi umieszczania. Lub należy wziąć pod uwagę przy użyciu dynamicznych portów usługi Service Fabric, jeśli jest to możliwe dla każdej usługi w każdym wystąpieniu aplikacji. 

Jeśli widzisz uaktualnienia kończyć się niepowodzeniem przy użyciu protokołu https, wystąpił błąd, ostrzeżenie z informacją "Interfejsu API serwera HTTP Windows nie obsługuje wielu certyfikatów dla aplikacji, które współdzielą port".

## <a name="application-upgrade-flowchart"></a>Schemat blokowy uaktualniania aplikacji
Schemat blokowy poniżej tego akapitu pomóc Ci zrozumieć proces uaktualniania aplikacji usługi Service Fabric. W szczególności przepływu w tym artykule opisano sposób limity czasu, w tym *HealthCheckStableDuration*, *HealthCheckRetryTimeout*, i *UpgradeHealthCheckInterval*, pomoc Kontrolka podczas uaktualniania w jedna domena aktualizacji jest uznawany za sukces lub niepowodzenie.

![Proces uaktualniania aplikacji usługi Service Fabric][image]

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnienie z aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu Visual Studio.

[Uaktualnienie z aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu PowerShell.

Kontrolować, jak uaktualnić aplikację przy użyciu [parametry uaktualniania](service-fabric-application-upgrade-parameters.md).

Uzyskania uaktualnień aplikacji zgodnych poznanie sposobu używania [serializacja danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).

Rozwiązywanie typowych problemów podczas uaktualniania aplikacji korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
