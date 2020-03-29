---
title: Uaktualnienie aplikacji sieci szkieletowej usług
description: Ten artykuł zawiera wprowadzenie do uaktualniania aplikacji sieci szkieletowej usług, w tym wybieranie trybów uaktualniania i przeprowadzanie kontroli kondycji.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 2dc484b49c5250510e5f018cbbc2da107573d452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259047"
---
# <a name="service-fabric-application-upgrade"></a>Uaktualnienie aplikacji sieci szkieletowej usług
Aplikacja sieci szkieletowej usług Azure jest zbiorem usług. Podczas uaktualniania sieci szkieletowej usług porównuje nowy [manifest aplikacji](service-fabric-application-and-service-manifests.md) z poprzednią wersją i określa, które usługi w aplikacji wymagają aktualizacji. Usługa Sieci szkieletowej porównuje numery wersji w manifestach usługi z numerami wersji w poprzedniej wersji. Jeśli usługa nie uległa zmianie, ta usługa nie zostanie uaktualniona.

## <a name="rolling-upgrades-overview"></a>Omówienie uaktualnień stopniowych
W uaktualnieniu aplikacji stopniowej uaktualnienie jest wykonywane etapami. Na każdym etapie uaktualnienie jest stosowane do podzbioru węzłów w klastrze, zwanego domeną aktualizacji. W rezultacie aplikacja pozostaje dostępna przez cały okres uaktualniania. Podczas uaktualniania klaster może zawierać kombinację starych i nowych wersji.

Z tego powodu obie wersje muszą być zgodne z przodu i do tyłu. Jeśli nie są one zgodne, administrator aplikacji jest odpowiedzialny za przemieszczania uaktualnienia wieloetapowego w celu utrzymania dostępności. W uaktualnieniu wielofazowym pierwszym krokiem jest uaktualnienie do wersji pośredniej aplikacji, która jest zgodna z poprzednią wersją. Drugim krokiem jest uaktualnienie ostatecznej wersji, która przerywa zgodność z wersją przed aktualizacją, ale jest zgodna z wersją pośrednią.

Aktualizuj domeny są określone w manifeście klastra podczas konfigurowania klastra. Domeny aktualizacji nie otrzymują aktualizacji w określonej kolejności. Domena aktualizacji jest logiczną jednostką wdrożenia aplikacji. Domeny aktualizacji umożliwiają usługi pozostają na wysokim poziomie dostępności podczas uaktualniania.

Uaktualnienia niekcyjne są możliwe, jeśli uaktualnienie jest stosowane do wszystkich węzłów w klastrze, co ma miejsce, gdy aplikacja ma tylko jedną domenę aktualizacji. Takie podejście nie jest zalecane, ponieważ usługa ustępuje i nie jest dostępna w momencie uaktualniania. Ponadto platforma Azure nie zapewnia żadnych gwarancji, gdy klaster jest skonfigurowany tylko z jedną domeną aktualizacji.

Po zakończeniu uaktualniania wszystkie usługi i repliki (wystąpienia) pozostaną w tej samej wersji, czyli jeśli uaktualnienie zakończy się pomyślnie, zostaną zaktualizowane do nowej wersji; jeśli uaktualnienie nie powiedzie się i zostanie wycofane, zostaną one wycofane do starej wersji.

## <a name="health-checks-during-upgrades"></a>Kontrole kondycji podczas modernizacji
W przypadku uaktualnienia należy ustawić zasady kondycji (lub można użyć wartości domyślnych). Uaktualnienie jest określane jako pomyślne, gdy wszystkie domeny aktualizacji są uaktualniane w określonych limitach czasu i gdy wszystkie domeny aktualizacji są uważane za zdrowe.  Domena aktualizacji w dobrej kondycji oznacza, że domena aktualizacji przeszła wszystkie kontrole kondycji określone w zasadach kondycji. Na przykład zasady kondycji może zalecić, że wszystkie usługi w wystąpieniu aplikacji musi być *w dobrej kondycji,* jak kondycja jest zdefiniowana przez sieci szkieletowej usług.

Zasady kondycji i kontrole podczas uaktualniania przez sieci szkieletowe usług są niezależną od usług i aplikacji. Oznacza to, że nie są wykonywane żadne testy specyficzne dla usługi.  Na przykład usługa może mieć wymagania dotyczące przepływności, ale sieci szkieletowej usług nie ma informacji do sprawdzenia przepływności. Zapoznaj się z [artykułami dotyczące kondycji](service-fabric-health-introduction.md) dla kontroli, które są wykonywane. Kontrole, które mają miejsce podczas uaktualniania obejmują testy, czy pakiet aplikacji został poprawnie skopiowany, czy wystąpienie zostało uruchomione i tak dalej.

Kondycja aplikacji jest agregacją jednostek podrzędnych aplikacji. W skrócie sieci szkieletowej usług ocenia kondycję aplikacji za pośrednictwem kondycji, który jest zgłaszany w aplikacji. Ocenia również kondycję wszystkich usług dla aplikacji w ten sposób. Sieci szkieletowej usług dalej ocenia kondycję usług aplikacji przez agregowanie kondycji ich ków podrzędnych, takich jak replika usługi. Po spełnieniu zasad kondycji aplikacji można kontynuować uaktualnienie. Jeśli zasady kondycji są naruszane, uaktualnienie aplikacji kończy się niepowodzeniem.

## <a name="upgrade-modes"></a>Tryby uaktualnienia
Trybem zalecanym do uaktualnienia aplikacji jest tryb monitorowany, który jest powszechnie używanym trybem. Tryb monitorowany wykonuje uaktualnienie w jednej domenie aktualizacji, a jeśli wszystkie testy kondycji przebiegają (zgodnie z określonymi zasadami), automatycznie przechodzi do następnej domeny aktualizacji.  Jeśli testy kondycji nie powiedzie się i/lub zostaną osiągnięte limity czasu, uaktualnienie zostanie wycofane dla domeny aktualizacji lub tryb zostanie zmieniony na niemonitorowany podręcznik. Uaktualnienie można skonfigurować tak, aby wybrało jeden z tych dwóch trybów dla uaktualnień, które nie powiodły się. 

Niemonitorowany tryb ręczny wymaga ręcznej interwencji po każdym uaktualnieniu w domenie aktualizacji, aby rozpocząć uaktualnienie w następnej domenie aktualizacji. Nie są wykonywane żadne kontrole kondycji sieci szkieletowej usług. Administrator wykonuje sprawdzanie kondycji lub stanu przed rozpoczęciem uaktualniania w następnej domenie aktualizacji.

## <a name="upgrade-default-services"></a>Uaktualnianie usług domyślnych
Niektóre domyślne parametry usługi zdefiniowane w [manifeście aplikacji](service-fabric-application-and-service-manifests.md) można również uaktualnić jako część uaktualnienia aplikacji. Tylko parametry usługi, które obsługują są zmieniane za pośrednictwem [Update-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) można zmienić w ramach uaktualnienia. Zachowanie zmiany usług domyślnych podczas uaktualniania aplikacji jest następujące:

1. Tworzone są usługi domyślne w nowym manifeście aplikacji, które jeszcze nie istnieją w klastrze.
2. Domyślne usługi, które istnieją w manifestach poprzedniej i nowej aplikacji są aktualizowane. Parametry usługi domyślnej w nowym manifeście aplikacji zastępują parametry istniejącej usługi. Uaktualnienie aplikacji zostanie wycofane automatycznie, jeśli aktualizacja usługi domyślnej zakończy się niepowodzeniem.
3. Domyślne usługi, które nie istnieją w nowym manifeście aplikacji są usuwane, jeśli istnieją w klastrze. **Należy zauważyć, że usunięcie usługi domyślnej spowoduje usunięcie całego stanu tej usługi i nie można cofnąć.**

Po przywróceniu uaktualnienia aplikacji domyślne parametry usługi są przywracane do starych wartości przed rozpoczęciem uaktualniania, ale nie można ponownie utworzyć usuniętych usług przy stanie starego.

> [!TIP]
> Ustawienie konfiguracji klastra [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) musi być *prawdziwe,* aby włączyć reguły 2) i 3) powyżej (domyślna aktualizacja i usunięcie usługi). Ta funkcja jest obsługiwana począwszy od sieci szkieletowej usług w wersji 5.5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Uaktualnianie wielu aplikacji z punktami końcowymi HTTPS
Należy uważać, aby nie używać **tego samego portu** dla różnych wystąpień tej samej aplikacji podczas korzystania z protokołu HTTP**S**. Powodem jest to, że sieci szkieletowej usług nie będzie można uaktualnić certyfikat dla jednego z wystąpień aplikacji. Na przykład jeśli aplikacja 1 lub aplikacja 2 zarówno chcesz uaktualnić ich cert 1 do cert 2. Po uaktualnieniu sieci szkieletowej usług może mieć oczyszczone cert 1 rejestracji z http.sys, mimo że inna aplikacja jest nadal przy użyciu go. Aby temu zapobiec, usługa Service Fabric wykrywa, że istnieje już inne wystąpienie aplikacji zarejestrowane na porcie z certyfikatem (z powodu http.sys) i nie powiedzie się działanie operacji.

W związku z tym sieci szkieletowej usług nie obsługuje uaktualniania dwóch różnych usług przy użyciu **tego samego portu** w różnych wystąpieniach aplikacji. Innymi słowy nie można używać tego samego certyfikatu w różnych usługach na tym samym porcie. Jeśli musisz mieć certyfikat udostępniony na tym samym porcie, należy upewnić się, że usługi są umieszczane na różnych komputerach z ograniczeniami umieszczania. Lub należy rozważyć użycie portów dynamicznych sieci szkieletowej usług, jeśli to możliwe dla każdej usługi w każdym wystąpieniu aplikacji. 

Jeśli uaktualnienie nie powiedzie się z https, ostrzeżenie o błędzie "Interfejs API serwera HTTP systemu Windows nie obsługuje wielu certyfikatów dla aplikacji współużytkujących port".

## <a name="application-upgrade-flowchart"></a>Schemat blokowy uaktualniania aplikacji
Schemat blokowy po tym akapicie może pomóc w zrozumieniu procesu uaktualniania aplikacji sieci szkieletowej usług. W szczególności przepływ opisuje, jak limity czasu, w tym *HealthCheckStableDuration*, *HealthCheckRetryTimeout*i *UpgradeHealthCheckInterval*, pomagają kontrolować, gdy uaktualnienie w jednej domenie aktualizacji jest uważane za powodzenie lub niepowodzenie.

![Proces uaktualniania aplikacji sieci szkieletowej usług][image]

## <a name="next-steps"></a>Następne kroki
[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu Visual Studio.

[Uaktualnianie aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu PowerShell.

Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).

Upewnij się, że uaktualnienia aplikacji są zgodne, ucząc się, jak korzystać z [serializacji danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [tematów zaawansowanych](service-fabric-application-upgrade-advanced.md).

Rozwiąż typowe problemy w uaktualnieniach aplikacji, odwołując się do kroków [rozwiązywania problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
