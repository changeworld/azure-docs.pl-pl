---
title: 'Uaktualnianie aplikacji: parametry uaktualnienia | Dokumentacja firmy Microsoft'
description: W tym artykule opisano parametry dotyczące uaktualniania aplikacji usługi Service Fabric, w tym kontroli kondycji do wykonania i zasad, aby automatycznie cofania operacji uaktualniania.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: eb319b0f4e910163572ee62d8bdee735f27be592
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="application-upgrade-parameters"></a>Parametry uaktualniania aplikacji
W tym artykule opisano różne parametry, które są stosowane podczas uaktualniania aplikacji sieci szkieletowej usług Azure. Parametry zawierają nazwę i wersję aplikacji. Są one pokrętła określające limity czasu i kontroli kondycji, które są stosowane podczas uaktualniania, a określają zasady, które należy zastosować w przypadku niepowodzenia uaktualnienia.

<br>

| Parametr | Opis |
| --- | --- |
| ApplicationName |Nazwa aplikacji, który jest uaktualniany. Przykłady: fabric: / VisualObjects, fabric: / ClusterMonitor |
| TargetApplicationTypeVersion |Typ wersji aplikacji, które cele uaktualniania. |
| FailureAction |Akcję wykonywaną przez sieć szkieletowa usług w przypadku nieudanego uaktualnienia. Aplikacja może zostać przywrócona do wersji poprzedzającej wersję update (wycofywania) lub uaktualnienia może być zatrzymana w bieżącej domenie uaktualnienia. W drugim przypadku tryb uaktualniania jest również zmienić ręcznie. Dozwolone wartości to wycofywania i ręczne. |
| HealthCheckWaitDurationSec |Czas oczekiwania (w sekundach), po zakończeniu uaktualnienia w domenie uaktualnienia przed sieci szkieletowej usług ocenia kondycję aplikacji. Ten czas trwania również można uznać za czas, które aplikacja powinna być uruchomiona, zanim mogą zostać uwzględnione dobrej kondycji. Jeśli sprawdzenie kondycji zakończy się pomyślnie, proces uaktualniania przechodzi do następnej domeny uaktualnienia.  W przypadku niepowodzenia sprawdzania kondycji sieci szkieletowej usług czeka na interwał (UpgradeHealthCheckInterval) przed ponowną próbą wykonania sprawdzenia kondycji ponownie aż do osiągnięcia HealthCheckRetryTimeout. Domyślną i zalecaną wartością jest 0 sekund. |
| HealthCheckRetryTimeoutSec |Czas trwania (w sekundach) tej sieci szkieletowej usług kontynuuje wykonywanie oceny kondycji przed zadeklarowaniem uaktualniania, ponieważ nie powiodło się. Wartość domyślna to 600 sekund. Ten czas trwania rozpoczyna się po osiągnięciu HealthCheckWaitDuration. W tym HealthCheckRetryTimeout sieci szkieletowej usług może być sprawdzana jest wiele kondycji kondycji aplikacji. Wartość domyślna to 10 minut i należy odpowiednio dostosować aplikacji. |
| HealthCheckStableDurationSec |Czas trwania (w sekundach), aby sprawdzić, czy aplikacja jest stabilna przed przejście do następnej domeny uaktualnienia lub zakończeniu uaktualniania. Ten czas trwania oczekiwania jest używany do chronienia wykryte zmiany kondycji prawej, po wykonaniu sprawdzania kondycji. Wartość domyślna to 120 sekund i należy odpowiednio dostosować aplikacji. |
| UpgradeDomainTimeoutSec |Maksymalny czas (w sekundach) dla uaktualnienia pojedynczej domeny uaktualnienia. Po osiągnięciu tego limitu czasu uaktualnienia zatrzymuje i jest przeprowadzane zgodnie z ustawieniem dla UpgradeFailureAction. Wartość domyślna to nigdy nie (Infinite) i należy odpowiednio dostosować aplikacji. |
| UpgradeTimeout |Limit (w sekundach), która ma zastosowanie do całego uaktualnienia. Po osiągnięciu tego limitu czasu uaktualnienia przerywa działanie i UpgradeFailureAction zostanie wywołany. Wartość domyślna to nigdy nie (Infinite) i należy odpowiednio dostosować aplikacji. |
| UpgradeHealthCheckInterval |Częstotliwość, że odbywa się sprawdzenie stanu kondycji. Ten parametr jest określony w sekcji ClusterManager *klastra* *manifestu*i nie jest określony jako część polecenia cmdlet uaktualnienia. Wartość domyślna to 60 sekund. |

<br>

## <a name="service-health-evaluation-during-application-upgrade"></a>Oceny kondycji usługi podczas uaktualniania aplikacji
<br>
Kryteria oceny kondycji są opcjonalne. Jeśli po uruchomieniu uaktualniania nie określono kryteriów oceny kondycji, sieci szkieletowej usług używa zasady dotyczące kondycji aplikacji, które są określone w pliku ApplicationManifest.xml wystąpienia aplikacji.

<br>

| Parametr | Opis |
| --- | --- |
| Elementów ConsiderWarningAsError |Wartość domyślna to False. Traktuj ostrzeżenia kondycji aplikacji jako błędy podczas oceny kondycji aplikacji podczas uaktualniania. Domyślnie usługa sieci szkieletowej nie może oszacować kondycji ostrzeżenia, aby uaktualnianie mógł kontynuować, nawet jeśli istnieją ostrzeżenia, należy błędów (błędy). |
| MaxPercentUnhealthyDeployedApplications |Domyślną i zalecaną wartością jest 0. Określ maksymalną liczbę wdrożonych aplikacji (zobacz [sekcji kondycji](service-fabric-health-introduction.md)) które może być nieprawidłowy, przed aplikacja jest określana jako zła i niepowodzenia uaktualnienia. Ten parametr określa kondycji aplikacji w węźle, co ułatwia wykrywanie problemów podczas uaktualniania. Zazwyczaj replik aplikacji Pobierz równoważeniem obciążenia na inny węzeł, który umożliwia aplikacji są wyświetlane w dobrej kondycji, dzięki czemu Uaktualnij, aby przejść. Określając strict kondycji MaxPercentUnhealthyDeployedApplications usługi sieć szkieletowa można wykrycie problem z pakietem aplikacji szybko i utworzyć błędu szybkiego uaktualnienia. |
| MaxPercentUnhealthyServices |Domyślną i zalecaną wartością jest 0. Określ maksymalną liczbę usług w wystąpienia aplikacji, które mogą być zła, zanim aplikacja jest określana jako zła i niepowodzenia uaktualnienia. |
| MaxPercentUnhealthyPartitionsPerService |Domyślną i zalecaną wartością jest 0. Określ maksymalną liczbę partycji usługi, który może być nieprawidłowy, zanim usługa jest określana jako zła. |
| MaxPercentUnhealthyReplicasPerPartition |Domyślną i zalecaną wartością jest 0. Określ maksymalną liczbę replik partycji, który może być nieprawidłowy, zanim partycji jest określana jako zła. |
| UpgradeReplicaSetCheckTimeout |<p>**Usługi bezstanowej**— w ramach pojedynczej domeny uaktualnienia, próbuje upewnij się, że dostępne są dodatkowe wystąpienia usługi sieć szkieletowa usług. Jeśli liczba wystąpień docelowych jest więcej niż jeden, Service Fabric czeka na więcej niż jedno wystąpienie, które mają być dostępne do wartości maksymalnej limitu czasu. Ten limit czasu został określony przy użyciu właściwości UpgradeReplicaSetCheckTimeout. Po przekroczeniu limitu czasu, usługa sieć szkieletowa kontynuuje uaktualnienia, niezależnie od liczby wystąpień usługi. Jeśli liczba wystąpień docelowych, usługi Service Fabric nie oczekuje i natychmiast będzie kontynuowana po uaktualnieniu.</p><p>**Usługi stanowej**— w ramach pojedynczej domeny uaktualnienia sieci szkieletowej usług próbuje upewnij się, że zestawu replik ma kworum. Sieć szkieletowa usług czeka kworum mają być dostępne do wartości maksymalnej limitu czasu (określoną przez właściwość UpgradeReplicaSetCheckTimeout). Po przekroczeniu limitu czasu, usługa sieć szkieletowa kontynuuje uaktualnienia, niezależnie od kworum. To ustawienie jest zestaw jak nigdy (infinite) stopniowych do przodu i 1200 sekund po wycofanie.</p> |
| ForceRestart |Po aktualizacji konfiguracji lub danych pakietu bez aktualizowania kodu usługi ponownym uruchomieniu usługi tylko wtedy, gdy właściwość ForceRestart jest ustawiona na true. Po ukończeniu aktualizacji sieci szkieletowej usług powiadamia usługę pakiet konfiguracji lub pakiet danych jest dostępna. Usługa jest odpowiedzialna za zastosowania zmian. Jeśli to konieczne, usługi można ponownie uruchomić się. |

<br>
<br>
Kryteria MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService i MaxPercentUnhealthyReplicasPerPartition może być określona dla typu usługi dla wystąpienia aplikacji. Umożliwia ustawienie tych parametrów dla usługi do zawiera typy różnych usług z różnych wersji ewaluacyjnej zasadami aplikacji. Na przykład typ usługi bezstanowej bramy może mieć MaxPercentUnhealthyPartitionsPerService, który jest inny niż typ usługi stanowej aparat wystąpienie aplikacji.

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnianie aplikacji za pomocą Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu programu Visual Studio.

[Uaktualnienie z aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu programu PowerShell.

[Uaktualnianie aplikacji w systemie Linux przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa](service-fabric-application-lifecycle-sfctl.md#upgrade-application) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu interfejsu wiersza polecenia usługi sieci szkieletowej.

[Uaktualnianie aplikacji za pomocą wtyczki Eclipse sieci szkieletowej usług](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Uzyskania uaktualnień aplikacji zgodnych przez poznanie [szeregowanie danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).

Rozwiązywania typowych problemów w uaktualnień aplikacji, korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).
