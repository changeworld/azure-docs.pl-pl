---
title: 'Uaktualnianie aplikacji: parametry uaktualniania | Dokumentacja firmy Microsoft'
description: W tym artykule opisano parametry uaktualniania aplikacji usługi Service Fabric, w tym kontrole kondycji do wykonania i zasady, aby automatycznie Cofnij uaktualnienia.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: subramar
ms.openlocfilehash: 9a93c0993ee45e72b11b023982dfbbe8c6528272
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614394"
---
# <a name="application-upgrade-parameters"></a>Parametry uaktualniania aplikacji
W tym artykule opisano różne parametry, które są stosowane podczas uaktualniania aplikacji usługi Azure Service Fabric. Parametry uaktualniania aplikacji kontrolować limity czasu i kontroli kondycji, które są stosowane podczas uaktualniania, a określają zasady, które można zastosować, uaktualnienie nie powiedzie się. Parametry aplikacji stosuje się do uaktualnienia przy użyciu:
- PowerShell
- Visual Studio
- SFCTL
- [REST](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Uaktualnienia aplikacji są inicjowane przy użyciu jednej z trzech trybów uaktualnienia wybierane przez użytkownika. Każdego trybu ma swój własny zestaw parametrów aplikacji:
- Monitorowane
- Niemonitorowane automatycznie
- Niemonitorowane ręczne

Zastosowanie wymaganych i opcjonalnych parametrów są opisane w każdej sekcji.

## <a name="visual-studio-and-powershell-parameters"></a>Parametry programu Visual Studio i programu PowerShell

Uaktualnianie aplikacji usługi Service Fabric przy użyciu programu PowerShell [Start ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) polecenia. Tryb uaktualniania jest zaznaczone, przekazując albo **monitorowanej**, **UnmonitoredAuto**, lub **UnmonitoredManual** parametr [ Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Visual Studio usługi Service Fabric aplikacji uaktualnienia parametrów są ustawiane za pomocą okna dialogowego uaktualnienia ustawienia programu Visual Studio. Tryb uaktualniania programu Visual Studio jest zaznaczone, przy użyciu **tryb uaktualniania** pole listy rozwijanej, aby albo **monitorowanej**, **UnmonitoredAuto**, lub **UnmonitoredManual** . Aby uzyskać więcej informacji, zobacz [Konfigurowanie uaktualniania aplikacji usługi Service Fabric w programie Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Wymagane parametry
(PS=PowerShell, VS=Visual Studio)

| Parametr | Dotyczy | Opis |
| --- | --- | --- |
ApplicationName |PS| Nazwa aplikacji, która jest uaktualniana. Przykłady: Service fabric: / VisualObjects, Service fabric: / ClusterMonitor. |
ApplicationTypeVersion|PS|Typ wersji aplikacji, które cele uaktualniania. |
FailureAction |PS, VS|Dozwolone wartości to **wycofywania**, **ręczne**, i **nieprawidłowy**. Wyrównującej akcję do wykonania, kiedy *monitorowanej* uaktualnienia napotka monitorowania zasad lub kondycji naruszenia zasad. <br>**Wycofywanie** Określa, czy uaktualnienie będzie automatycznie konieczne przywrócenie początkowej wersji sprzed uaktualnienia. <br>**Ręczne** wskazuje, że uaktualnienie przełączą się do *UnmonitoredManual* tryb uaktualniania. <br>**Nieprawidłowy** wskazuje, że akcja błędu jest nieprawidłowa.|
Monitorowane |PS|Wskazuje, czy tryb uaktualniania jest monitorowany. Po zakończeniu pracy uaktualnienie domeny uaktualnienia, polecenia cmdlet, jeśli kondycję domeny uaktualnienia klastra, spełniają zasady dotyczące kondycji, które definiujesz, Usługa Service Fabric uaktualnia następnej domeny uaktualnienia. Jeśli domena uaktualnienia lub klastra nie spełnia zasad dotyczących kondycji, uaktualnienie nie powiedzie się i usługi Service Fabric powoduje wycofanie uaktualnienie dla domeny uaktualnień lub powraca do trybu ręcznego zgodnie z zasadami określony. To jest zalecanym trybem uaktualnień aplikacji w środowisku produkcyjnym. |
Element UpgradeMode | VS | Dozwolone wartości to **monitorowanej** (ustawienie domyślne), **UnmonitoredAuto**, lub **UnmonitoredManual**. Zobacz parametry programu PowerShell dla każdego trybu, w tym artykule, aby uzyskać szczegółowe informacje. |
UnmonitoredAuto | PS | Wskazuje, że tryb uaktualniania jest automatycznie monitorowany. Po uaktualnieniu domeny uaktualnienia usługi Service Fabric Service Fabric uaktualnia następnej domeny uaktualnienia, niezależnie od tego, stan kondycji aplikacji. Ten tryb nie jest zalecane w środowisku produkcyjnym i jest przydatna tylko podczas tworzenia aplikacji. |
UnmonitoredManual | PS | Wskazuje, że tryb uaktualniania jest monitorowany ręczne. Po uaktualnieniu domeny uaktualnienia usługi Service Fabric czeka do uaktualnienia następnej domeny uaktualnienia przy użyciu *ServiceFabricApplicationUpgrade Wznów* polecenia cmdlet. |

### <a name="optional-parameters"></a>Parametry opcjonalne

Parametry oceny kondycji są opcjonalne. Jeśli nie zostały określone kryteria oceny kondycji, po rozpoczęciu uaktualnienia, Usługa Service Fabric używa zasady dotyczące kondycji aplikacji, które są określone w ApplicationManifest.xml wystąpienia aplikacji.

Użyj poziomy pasek przewijania w dolnej części tabeli, aby wyświetlić pełen opis pola.

(PS=PowerShell, VS=Visual Studio)

| Parametr | Dotyczy | Opis |
| --- | --- | --- |
| ApplicationParameter |PS, VS| Określa przesłonięcia dla parametry aplikacji.<br>Parametry aplikacji dla programu PowerShell są określane jako tablica skrótów par nazwa/wartość. Na przykład @{"VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1"}.<br>Parametry aplikacji w usłudze Visual Studio można określić w oknie dialogowym Publikowanie aplikacji usługi Service Fabric w **plik parametrów aplikacji** pola.
| Potwierdź |PS| Dozwolone wartości to **True** i **False**. Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet. |
| ConsiderWarningAsError |PS, VS |Dozwolone wartości to **True** i **False**. Wartość domyślna to **False**. Zdarzenia kondycji ostrzeżenia dla aplikacji należy traktować jako błędy podczas oceny kondycji aplikacji podczas uaktualniania. Domyślnie Usługa Service Fabric nie może oszacować zdarzenia kondycji ostrzeżeń jako błędów (błędy), aby kontynuować uaktualnianie nawet jeśli występują ostrzeżenia. |
| DefaultServiceTypeHealthPolicy | PS, VS |Określa zasady kondycji domyślny typ usługi do użycia podczas uaktualniania monitorowanych w formacie MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Na przykład 5,10,15 wskazuje następujące wartości: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
| Wymuś | PS, VS | Dozwolone wartości to **True** i **False**. Wskazuje, że proces uaktualniania pomija komunikat ostrzegawczy i wymuszenie uaktualnienia, nawet wtedy, gdy jest to numer wersji nie zmienił. To jest przydatne w przypadku lokalnego testowania, ale nie jest zalecane do użytku w środowisku produkcyjnym, ponieważ wymaga usunięcia istniejącego wdrożenia, co powoduje, że czas przestoju i potencjalnych utracie. |
| ForceRestart |PS, VS |Po aktualizacji konfiguracji lub danych pakietu bez aktualizowania kodu usługi tylko wtedy, gdy właściwość ForceRestart jest ustawiona na ponownym uruchomieniu usługi **True**. Po zakończeniu aktualizacji usługi Service Fabric powiadamia usługę nowy pakiet konfiguracji lub pakiet danych jest dostępna. Usługa jest odpowiedzialna za zastosowania zmian. Jeśli to konieczne, usługa może uruchamia się ponownie. |
| HealthCheckRetryTimeoutSec |PS, VS |Czas trwania (w sekundach) tej usługi Service Fabric w dalszym ciągu wykonywać ocenę kondycji przed zadeklarowaniem uaktualnienia, ponieważ nie powiodło się. Wartość domyślna to 600 sekund. Ten czas trwania rozpoczyna się po *HealthCheckWaitDurationSec* zostanie osiągnięty. W ramach tej *HealthCheckRetryTimeout*, Usługa Service Fabric może działać wiele kontrole kondycji kondycji aplikacji. Wartość domyślna to 10 minut i powinien zostać dostosowany odpowiednio do aplikacji. |
| HealthCheckStableDurationSec |PS, VS |Czas trwania (w sekundach) aby sprawdzić, czy aplikacja jest stabilna przed przeniesieniem do następnej domeny uaktualnienia lub ukończeniu procesu uaktualniania. Ten czas oczekiwania używany w celu zapobiegania niewykrytym zmianom kondycji bezpośrednio po sprawdzeniu kondycji. Wartość domyślna to 120 sekund i powinien zostać dostosowany odpowiednio do aplikacji. |
| HealthCheckWaitDurationSec |PS, VS | Czas oczekiwania (w sekundach), po zakończeniu uaktualniania w domenie uaktualnień przed usługi Service Fabric ocenia kondycję aplikacji. Ten czas można również uważana za czas, który aplikacja powinna działać, zanim mogą być uznane za dobrej kondycji. Jeśli sprawdzenie kondycji zakończy się pomyślnie, proces uaktualniania przechodzi do następnej domeny uaktualnienia.  W przypadku niepowodzenia sprawdzania kondycji usługi Service Fabric czeka [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) przed ponowną próbą kondycji Sprawdź ponownie do czasu *HealthCheckRetryTimeoutSec* zostanie osiągnięty. Domyślna i zalecana wartość wynosi 0 sekund. |
| MaxPercentUnhealthyDeployedApplications|PS, VS |Domyślnej i zalecanej wartości to 0. Określ maksymalną liczbę wdrożonych aplikacji (zobacz [sekcji kondycji](service-fabric-health-introduction.md)), które można złej kondycji przed aplikacja jest uznawana za złą i niepowodzenia uaktualnienia. Ten parametr określa kondycji aplikacji w węźle, co ułatwia wykrywanie problemów podczas uaktualniania. Zazwyczaj replik ją pobrać ze zrównoważonym obciążeniem na inny węzeł, który umożliwia aplikacji są wyświetlane w dobrej kondycji, umożliwiając w ten sposób uaktualnienia kontynuować. Określając strict *MaxPercentUnhealthyDeployedApplications* kondycji usługi Service Fabric można szybko wykryć problem z pakietem aplikacji i utworzyć awarii szybkie uaktualnienia. |
| MaxPercentUnhealthyServices |PS, VS |Parametr *DefaultServiceTypeHealthPolicy* i *ServiceTypeHealthPolicyMap*. Domyślnej i zalecanej wartości to 0. Określ maksymalną liczbę usług w wystąpienie aplikacji, które może być nieprawidłowy, zanim aplikacja jest uznawana za złą i niepowodzenia uaktualnienia. |
| MaxPercentUnhealthyPartitionsPerService|PS, VS |Parametr *DefaultServiceTypeHealthPolicy* i *ServiceTypeHealthPolicyMap*. Domyślnej i zalecanej wartości to 0. Określ maksymalną liczbę partycji w usłudze, która może być nieprawidłowy, zanim usługa jest uznawana za złą. |
| MaxPercentUnhealthyReplicasPerPartition|PS, VS |Parametr *DefaultServiceTypeHealthPolicy* i *ServiceTypeHealthPolicyMap*. Domyślnej i zalecanej wartości to 0. Określ maksymalną liczbę replik partycji, który może być nieprawidłowy, zanim partycji jest określana jako zła. |
| ServiceTypeHealthPolicyMap | PS, VS | Reprezentuje zasad dotyczących kondycji używane do oceny kondycji usług należące do typu usługi. Przyjmuje dane wejściowe z tabeli skrótu w następującym formacie: @ {"ServiceTypeName": "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} For example: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
| TimeoutSec | PS, VS | Określa wartość limitu czasu w sekundach dla tej operacji. |
| UpgradeDomainTimeoutSec |PS, VS |Maksymalny czas (w sekundach) dla uaktualnienie pojedynczej domeny uaktualnienia. Po osiągnięciu tego limitu czasu uaktualnienia zatrzymuje i jest przeprowadzane zgodnie z ustawieniem dla *FailureAction*. Wartość domyślna to nigdy nie (nieograniczony) i należy odpowiednio dostosować dla aplikacji. |
| UpgradeReplicaSetCheckTimeoutSec |PS, VS |Mierzony w sekundach.<br>**Usługa bezstanowa**— w jednej domenie uaktualnienia program próbuje upewnij się, że dostępne są dodatkowe wystąpienia usługi Service Fabric. Jeśli liczba wystąpień docelowego jest więcej niż jeden, Usługa Service Fabric czeka na więcej niż jedno wystąpienie, które mają być dostępne, maksymalnie maksymalną wartość limitu czasu. Ten limit czasu jest określony za pomocą *UpgradeReplicaSetCheckTimeoutSec* właściwości. Przekroczeniu limitu czasu usługi Service Fabric będzie kontynuowana po uaktualnieniu, bez względu na liczbę wystąpień usługi. Jeśli docelowa liczba wystąpień, usługi Service Fabric czeka i natychmiast kontynuacją uaktualnienia przez.<br><br>**Usługa stanowa**— w pojedynczej domenie uaktualnienia usługi Service Fabric próbuje upewnij się, że zestawu replik ma kworum. Usługa Service Fabric czeka kworum udostępnienie maksymalnie maksymalną wartość limitu czasu (określony przez *UpgradeReplicaSetCheckTimeoutSec* właściwości). Przekroczeniu limitu czasu usługi Service Fabric będzie kontynuowana po uaktualnieniu, niezależnie od kworum. To ustawienie jest ustawiony jako nigdy nie (z bez ograniczeń czasowych), podczas wdrażania do przodu, 1200 sekund podczas wycofywania. |
| UpgradeTimeoutSec |PS, VS |Czasu (w sekundach), która ma zastosowanie do całej uaktualnienia. Po osiągnięciu tego limitu czasu zatrzymuje uaktualnienia i *FailureAction* zostanie wywołany. Wartość domyślna to nigdy nie (nieograniczony) i należy odpowiednio dostosować dla aplikacji. |
| WhatIf | PS | Dozwolone wartości to **True** i **False**. Pokazuje, co się stanie po uruchomieniu polecenia cmdlet. Polecenie cmdlet nie jest uruchomione. |

*MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService*, i *MaxPercentUnhealthyReplicasPerPartition* kryteria może być określona dla Typ usługi dla wystąpienia aplikacji. Ustawienia te parametry za daną usługę umożliwia aplikacji zawierają typy różnych usług za pomocą różnych oceny zasad. Na przykład można mieć typ usługi bramy o bezstanowa *MaxPercentUnhealthyPartitionsPerService* różni się od typu usługi stanowej aparatu w przypadku wystąpienie aplikacji.

## <a name="sfctl-parameters"></a>Interfejs SFCTL parametrów

Uaktualnianie aplikacji usługi Service Fabric przy użyciu interfejsu wiersza polecenia usługi Service Fabric [uaktualnienia aplikacji sfctl](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) polecenia wraz z następujących wymaganych i opcjonalnych parametrów.

### <a name="required-parameters"></a>Wymagane parametry

| Parametr | Opis |
| --- | --- |
| Identyfikator aplikacji  |Identyfikator aplikacji, która jest uaktualniana. <br> Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric:" Schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric: / myapp/app1", tożsamość aplikacji będzie "myapp\~app1" 6.0 + i "myapp app1" w poprzednich wersjach.|
Wersja aplikacji |Typ wersji aplikacji, które cele uaktualniania.|
parameters  |Zastępuje kodowany w formacie JSON listę parametrów aplikacji mają być stosowane podczas uaktualniania aplikacji.|

### <a name="optional-parameters"></a>Parametry opcjonalne

| Parametr | Opis |
| --- | --- |
service kondycji — zasady domyślne | [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) zakodowane specyfikacji zasad dotyczących kondycji używany domyślnie do oceny kondycji typu usługi. Mapa jest domyślnie puste. |
Akcja błędu | Dozwolone wartości to **wycofywania**, **ręczne**, i **nieprawidłowy**. Wyrównującej akcję do wykonania, kiedy *monitorowanej* uaktualnienia napotka monitorowania zasad lub kondycji naruszenia zasad. <br>**Wycofywanie** Określa, czy uaktualnienie będzie automatycznie konieczne przywrócenie początkowej wersji sprzed uaktualnienia. <br>**Ręczne** wskazuje, że uaktualnienie przełączą się do *UnmonitoredManual* tryb uaktualniania. <br>**Nieprawidłowy** wskazuje, że akcja błędu jest nieprawidłowa.|
force-restart | Po aktualizacji konfiguracji lub danych pakietu bez aktualizowania kodu usługi tylko wtedy, gdy właściwość ForceRestart jest ustawiona na ponownym uruchomieniu usługi **True**. Po zakończeniu aktualizacji usługi Service Fabric powiadamia usługę nowy pakiet konfiguracji lub pakiet danych jest dostępna. Usługa jest odpowiedzialna za zastosowania zmian. Jeśli to konieczne, usługa może uruchamia się ponownie. |
health-check-retry-timeout | Ilość czasu, aby ponowić próbę wykonania oceny kondycji aplikacji lub klaster jest w złej kondycji przed *FailureAction* jest wykonywany. Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. Wartość domyślna: PT0H10M0S. |
health-check-stable-duration | Ilość czasu, aplikacji lub klastra musi pozostać dobrej kondycji przed uaktualnienia przechodzi do następnej domeny uaktualnienia. Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. Wartość domyślna: PT0H2M0S. |
health-check-wait-duration | Ilość czasu oczekiwania po wykonaniu uaktualnienia domeny przed zastosowaniem zasady dotyczące kondycji. Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. Wartość domyślna: 0.|
MAX — zła — aplikacje | Domyślnej i zalecanej wartości to 0. Określ maksymalną liczbę wdrożonych aplikacji (zobacz [sekcji kondycji](service-fabric-health-introduction.md)), które można złej kondycji przed aplikacja jest uznawana za złą i niepowodzenia uaktualnienia. Ten parametr określa kondycji aplikacji w węźle, co ułatwia wykrywanie problemów podczas uaktualniania. Zazwyczaj replik ją pobrać ze zrównoważonym obciążeniem na inny węzeł, który umożliwia aplikacji są wyświetlane w dobrej kondycji, umożliwiając w ten sposób uaktualnienia kontynuować. Określając strict *max-złej kondycji apps* kondycji usługi Service Fabric można szybko wykryć problem z pakietem aplikacji i utworzyć awarii szybkie uaktualnienia. Przedstawiona jako liczba od 0 do 100. |
mode | Dozwolone wartości to **monitorowanej**, **element UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**. Wartość domyślna to **UnmonitoredAuto**. Zapoznaj się z programu Visual Studio i PowerShell *wymagane parametry* sekcji, aby uzyskać opis tych wartości.|
replica-set-check-timeout |Mierzony w sekundach. <br>**Usługa bezstanowa**— w jednej domenie uaktualnienia program próbuje upewnij się, że dostępne są dodatkowe wystąpienia usługi Service Fabric. Jeśli liczba wystąpień docelowego jest więcej niż jeden, Usługa Service Fabric czeka na więcej niż jedno wystąpienie, które mają być dostępne, maksymalnie maksymalną wartość limitu czasu. Ten limit czasu jest określony za pomocą *repliki set wyboru timeout* właściwości. Przekroczeniu limitu czasu usługi Service Fabric będzie kontynuowana po uaktualnieniu, bez względu na liczbę wystąpień usługi. Jeśli docelowa liczba wystąpień, usługi Service Fabric czeka i natychmiast kontynuacją uaktualnienia przez.<br><br>**Usługa stanowa**— w pojedynczej domenie uaktualnienia usługi Service Fabric próbuje upewnij się, że zestawu replik ma kworum. Usługa Service Fabric czeka kworum udostępnienie maksymalnie maksymalną wartość limitu czasu (określony przez *repliki set wyboru timeout* właściwości). Przekroczeniu limitu czasu usługi Service Fabric będzie kontynuowana po uaktualnieniu, niezależnie od kworum. To ustawienie jest ustawiony jako nigdy nie (z bez ograniczeń czasowych), podczas wdrażania do przodu, 1200 sekund podczas wycofywania. |
Usługa kondycji zasad | Mapy za pomocą zasad kondycji typ usługi dla usługi, nazwa typu zakodowane JSON. Mapa jest pusta domyślnie. [Parametr formatu JSON. ](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). Zawiera plik JSON do części "Value" **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService**, i **MaxPercentUnhealthyReplicasPerPartition**. Sekcja programu Visual Studio i PowerShell następujące parametry opcjonalne opisy tych parametrów.
timeout | Określa wartość limitu czasu w sekundach dla tej operacji. Wartość domyślna: 60. |
upgrade-domain-timeout | Czas każdej z domen musi ukończyć przed *FailureAction* jest wykonywany. Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. Wartość domyślna to nigdy nie (nieograniczony) i należy odpowiednio dostosować dla aplikacji. Wartość domyślna: P10675199DT02H48M05.4775807S. |
limit czasu uaktualniania | Czas każdej z domen musi ukończyć przed *FailureAction* jest wykonywany. Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. Wartość domyślna to nigdy nie (nieograniczony) i należy odpowiednio dostosować dla aplikacji. Wartość domyślna: P10675199DT02H48M05.4775807S.|
warning-as-error | Dozwolone wartości to **True** i **False**. Wartość domyślna to **False**. Jako flagi, może być przekazywany w. Zdarzenia kondycji ostrzeżenia dla aplikacji należy traktować jako błędy podczas oceny kondycji aplikacji podczas uaktualniania. Domyślnie Usługa Service Fabric nie może oszacować zdarzenia kondycji ostrzeżeń jako błędów (błędy), aby kontynuować uaktualnianie nawet jeśli występują ostrzeżenia. |

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnienie z aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu Visual Studio.

[Uaktualnienie z aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu PowerShell.

[Uaktualnianie aplikacji przy użyciu interfejsu wiersza polecenia usługi Service Fabric w systemie Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) przeprowadzi uaktualnienie aplikacji przy użyciu interfejsu wiersza polecenia usługi Service Fabric.

[Uaktualnianie aplikacji przy użyciu wtyczki środowiska Eclipse usługi Service Fabric](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Uzyskania uaktualnień aplikacji zgodnych poznanie sposobu używania [serializacja danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).

Rozwiązywanie typowych problemów podczas uaktualniania aplikacji korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).
