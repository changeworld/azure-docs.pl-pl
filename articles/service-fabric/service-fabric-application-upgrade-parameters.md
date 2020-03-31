---
title: 'Uaktualnienie aplikacji: parametry uaktualnienia'
description: W tym artykule opisano parametry związane z uaktualnieniem aplikacji sieci szkieletowej usług, w tym kontrole kondycji do wykonania i zasady, aby automatycznie cofnąć uaktualnienie.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 42b5c52181cfb006ae57e43c183b96a059a9c63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377977"
---
# <a name="application-upgrade-parameters"></a>Parametry uaktualniania aplikacji
W tym artykule opisano różne parametry, które mają zastosowanie podczas uaktualniania aplikacji sieci szkieletowej usługi Azure. Parametry uaktualniania aplikacji kontrolują limity czasu i testy kondycji, które są stosowane podczas uaktualniania, i określają zasady, które muszą być stosowane, gdy uaktualnienie nie powiedzie się. Parametry aplikacji mają zastosowanie do uaktualnień przy użyciu:
- PowerShell
- Visual Studio
- SFCTL (SFCTL)
- [Reszta](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Uaktualnienia aplikacji są inicjowane za pomocą jednego z trzech trybów uaktualniania, które można wybrać przez użytkownika. Każdy tryb ma swój własny zestaw parametrów aplikacji:
- Monitorowane
- Niemonitorowana automatyczna
- Niemonitorowana instrukcja

Odpowiednie parametry wymagane i opcjonalne są opisane w każdej sekcji w następujący sposób.

## <a name="visual-studio-and-powershell-parameters"></a>Parametry programu Visual Studio i Programu PowerShell

Uaktualnienia aplikacji sieci szkieletowej usług przy użyciu programu PowerShell używają polecenia [Start-ServiceFabricApplicationUpligrade.](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) Tryb uaktualniania jest wybierany przez przekazanie parametru **Monitored**, **UnmonitoredAuto**lub **UnmonitoredManual** do [parametru Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Parametry uaktualnienia aplikacji usługi Visual Studio Fabric są ustawiane za pomocą okna dialogowego Ustawienia uaktualnienia programu Visual Studio. Tryb uaktualniania programu Visual Studio jest wybierany za pomocą pola rozwijanego **Tryb uaktualnienia** do **monitorowanego**, **NiemonitorowanegoAuto**lub **NiemonitorowanegoManału**. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uaktualnienia aplikacji sieci szkieletowej usług w programie Visual Studio.](service-fabric-visualstudio-configure-upgrade.md)

### <a name="required-parameters"></a>Wymagane parametry
(PS=PowerShell, VS=Visual Studio)

| Parametr | Dotyczy: | Opis |
| --- | --- | --- |
ApplicationName |PS| Nazwa aplikacji, która jest uaktualniany. Przykłady: tkanina:/VisualObjects, fabric:/ClusterMonitor. |
ApplicationTypeVersion (Wersja typu aplikacji)|PS|Wersja typu aplikacji, która jest przeznaczona dla uaktualnienia. |
AwariaAkcja |PS, VS|Dozwolone wartości to **Wycofywanie,** **Ręczne**i **Nieprawidłowe**. Akcja kompensacjąca do wykonania, gdy *monitorowane* uaktualnienie napotka naruszenia zasad monitorowania lub zasad kondycji. <br>**Wycofywanie** określa, że uaktualnienie zostanie automatycznie wycofane do wersji przed uaktualnieniem. <br>**Podręcznik** wskazuje, że uaktualnienie przełączy się do trybu uaktualniania *UnmonitoredManual.* <br>**Nieprawidłowy** oznacza, że akcja błędu jest nieprawidłowa.|
Monitorowane |PS|Wskazuje, że tryb uaktualniania jest monitorowany. Po zakończeniu polecenia cmdlet uaktualnienia dla domeny uaktualnienia, jeśli kondycja domeny uaktualnienia i klastra spełniają zasady kondycji, które definiujesz, sieci szkieletowej usług uaktualnia następną domenę uaktualnienia. Jeśli domena uaktualnienia lub klaster nie spełnia zasad kondycji, uaktualnienie kończy się niepowodzeniem, a sieci szkieletowej usług wycofuje uaktualnienie dla domeny uaktualnienia lub powraca do trybu ręcznego według określonych zasad. Jest to zalecany tryb uaktualniania aplikacji w środowisku produkcyjnym. |
Tryb uaktualnienia | VS | Dozwolone wartości to **Monitorowane** (domyślnie), **NiemonitorowaneAuto**lub **NiemonitorowaneManualne**. Zobacz parametry programu PowerShell dla każdego trybu w tym artykule, aby uzyskać szczegółowe informacje. |
NiemonitorowaneAuto | PS | Wskazuje, że tryb uaktualniania jest niemonitorowany automatycznie. Po uaktualnieniu domeny uaktualnienia sieci szkieletowej usługi uaktualnia domenę następnego uaktualnienia niezależnie od stanu kondycji aplikacji. Ten tryb nie jest zalecany do produkcji i jest przydatny tylko podczas tworzenia aplikacji. |
NiemonitorowaneManualne | PS | Wskazuje, że tryb uaktualniania jest niemonitorowany ręcznie. Po uaktualnieniu domeny uaktualnienia przez usługę Service Fabric, czeka na uaktualnienie następnej domeny uaktualnienia przy użyciu polecenia cmdlet *Resume-ServiceFabricApplicationUplication.* |

### <a name="optional-parameters"></a>Parametry opcjonalne

Parametry oceny kondycji są opcjonalne. Jeśli kryteria oceny kondycji nie są określone po uruchomieniu uaktualnienia, sieć szkieletowa usług używa zasad kondycji aplikacji określonych w applicationManifest.xml wystąpienia aplikacji.

> [!div class="mx-tdBreakAll"]
> | Parametr | Dotyczy: | Opis |
> | --- | --- | --- |
> | AplikacjaParametr |PS, VS| Określa zastąpienia parametrów aplikacji.<br>Parametry aplikacji programu PowerShell są określane jako pary nazw/wartości. Na przykład @{ "VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1" }.<br>Parametry aplikacji programu Visual Studio można określić w oknie dialogowym Publikowanie aplikacji sieci szkieletowej usługi w polu **Plik parametrów aplikacji.**
> | Confirm |PS| Dozwolone wartości to **Prawda** i **Fałsz**. Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet. |
> | RozważwarningAsError |PS, VS |Dozwolone wartości to **Prawda** i **Fałsz**. Wartość domyślna to **False**. Potraktuj zdarzenia kondycji ostrzeżenia dla aplikacji jako błędy podczas oceny kondycji aplikacji podczas uaktualniania. Domyślnie sieć szkieletowa usług nie ocenia zdarzeń kondycji ostrzeżenia za błędy (błędy), więc uaktualnienie można kontynuować, nawet jeśli występują zdarzenia ostrzegawcze. |
> | DefaultServiceTypeHealthPolicy | PS, VS |Określa zasady kondycji dla domyślnego typu usługi do użycia dla monitorowane uaktualnienie w formacie MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyService. Na przykład 5,10,15 wskazuje następujące wartości: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Dozwolone wartości to **Prawda** i **Fałsz**. Wskazuje, że proces uaktualniania pomija komunikat ostrzegawczy i wymusza uaktualnienie nawet wtedy, gdy numer wersji nie uległ zmianie. Jest to przydatne w przypadku testowania lokalnego, ale nie jest zalecane do użycia w środowisku produkcyjnym, ponieważ wymaga usunięcia istniejącego wdrożenia, co powoduje przestoje i potencjalną utratę danych. |
> | ForceRestart (Początek siły) |PS, VS |W przypadku aktualizacji konfiguracji lub pakietu danych bez aktualizowania kodu usługi usługa jest uruchamiana ponownie tylko wtedy, gdy właściwość ForceRestart jest **ustawiona**na True . Po zakończeniu aktualizacji sieci szkieletowej usług powiadamia usługę, że nowy pakiet konfiguracji lub pakiet danych jest dostępny. Usługa jest odpowiedzialna za zastosowanie zmian. W razie potrzeby usługa może zostać ponownie uruchomiona. |
> | HealthCheckRetryTimeoutSec |PS, VS |Czas trwania (w sekundach), że sieć szkieletowa usług kontynuuje wykonywanie oceny kondycji przed zadeklarowaniem uaktualnienia jako nie powiodło się. Wartość domyślna to 600 sekund. Ten czas trwania rozpoczyna się po *healthcheckWaitDurationSec* zostanie osiągnięty. W ramach tego *HealthCheckRetryTimeout*sieci szkieletowej usługi może wykonać wiele kontroli kondycji aplikacji. Wartość domyślna wynosi 10 minut i powinna być dostosowana odpowiednio do aplikacji. |
> | HealthCheckStableDurationSec |PS, VS |Czas trwania (w sekundach), aby sprawdzić, czy aplikacja jest stabilna przed przejściem do następnej domeny uaktualnienia lub ukończeniem uaktualnienia. Ten czas oczekiwania jest używany, aby zapobiec niewykrytych zmian kondycji zaraz po przeprowadzeniu kontroli kondycji. Wartość domyślna wynosi 120 sekund i powinna być dostosowana odpowiednio do aplikacji. |
> | HealthCheckWaitDurationSec |PS, VS | Czas oczekiwania (w sekundach) po zakończeniu uaktualniania w domenie uaktualnienia, zanim sieć szkieletowa usług oceni kondycję aplikacji. Ten czas trwania można również uznać za czas aplikacji powinny być uruchomione, zanim można uznać za zdrowe. Jeśli sprawdzanie kondycji przejdzie, proces uaktualniania przechodzi do następnej domeny uaktualnienia.  Jeśli sprawdzanie kondycji nie powiedzie się, sieci szkieletowej usług czeka na [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) przed ponowną próbą sprawdzenia kondycji ponownie, aż *HealthCheckRetryTimeoutSec* zostanie osiągnięty. Wartość domyślna i zalecana to 0 sekund. |
> | MaxPercentUnhealthyDeployedApplications MaxPercentUnhealthyDeployedApplications MaxPercentUnhealthyDeployedApplications MaxPer|PS, VS |Domyślna i zalecana wartość to 0. Określ maksymalną liczbę wdrożonych aplikacji (zobacz [sekcję Zdrowie),](service-fabric-health-introduction.md)które mogą być w złej kondycji, zanim aplikacja zostanie uznana za złą w złej kondycji i nie powiedzie się uaktualnienie. Ten parametr definiuje kondycję aplikacji w węźle i pomaga wykrywać problemy podczas uaktualniania. Zazwyczaj repliki aplikacji uzyskać równoważenie obciążenia do drugiego węzła, co pozwala aplikacji w dobrej kondycji, umożliwiając w ten sposób uaktualnienie, aby kontynuować. Określając ścisły *MaxPercentUnhealthyDeployedApplications* kondycji, sieci szkieletowej usług można szybko wykryć problem z pakietem aplikacji i pomóc w produkcji nie szybkie uaktualnienie. |
> | MaxPercentNiezdroweusługi |PS, VS |Parametr *DefaultServiceTypeHealthPolicy* i *ServiceTypeHealthPolicyMap*. Domyślna i zalecana wartość to 0. Określ maksymalną liczbę usług w wystąpieniu aplikacji, które mogą być w złej kondycji, zanim aplikacja zostanie uznana za złą w złej kondycji i nie powiedzie się uaktualnienie. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |Parametr *DefaultServiceTypeHealthPolicy* i *ServiceTypeHealthPolicyMap*. Domyślna i zalecana wartość to 0. Określ maksymalną liczbę partycji w usłudze, które mogą być w złej kondycji, zanim usługa zostanie uznana za złą w złej kondycji. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |Parametr *DefaultServiceTypeHealthPolicy* i *ServiceTypeHealthPolicyMap*. Domyślna i zalecana wartość to 0. Określ maksymalną liczbę replik na partycji, które mogą być w złej kondycji, zanim partycja zostanie uznana za złą w złej kondycji. |
> | ServiceTypeHealthPolicyMap | PS, VS | Reprezentuje zasady kondycji używane do oceny kondycji usług należących do typu usługi. Wykonuje dane wejściowe tabeli mieszania w następującym formacie: @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} Na przykład: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
> | Przełękń czasu | PS, VS | Określa limit czasu w sekundach dla operacji. |
> | UpgradeDomainTimeoutSec |PS, VS |Maksymalny czas (w sekundach) na uaktualnienie domeny pojedynczego uaktualnienia. Jeśli ten limit czasu zostanie osiągnięty, uaktualnienie zatrzymuje się i przebiega na podstawie ustawienia *FailureAction*. Wartość domyślna nigdy nie jest (Infinite) i powinny być dostosowane odpowiednio do aplikacji. |
> | UpgradeReplicaSetCheckTimeoutSec |PS, VS |Mierzona w sekundach.<br>**Usługa bezstanowa**--W ramach jednej domeny uaktualnienia sieci szkieletowej usług próbuje upewnić się, że dodatkowe wystąpienia usługi są dostępne. Jeśli liczba wystąpień docelowych jest więcej niż jeden, sieci szkieletowej usług czeka na więcej niż jedno wystąpienie, aby być dostępne, maksymalnie maksymalną wartość przekroczenia czasu. Ten limit czasu jest określony przy użyciu *UpgradeReplicaSetCheckTimeoutSec* właściwości. Jeśli limit czasu wygaśnie, usługa Service Fabric kontynuuje uaktualnienie, niezależnie od liczby wystąpień usługi. Jeśli liczba wystąpień docelowych jest jedna, sieci szkieletowej usług nie czeka i natychmiast przechodzi z uaktualnienia.<br><br>**Usługa stanowa**--W ramach jednej domeny uaktualnienia sieci szkieletowej usług próbuje upewnić się, że zestaw replik ma kworum. Usługa Sieci szkieletowej czeka na kworum, aby być dostępne, maksymalnie maksymalna wartość limitu czasu (określona przez *UpgradeReplicaSetCheckTimeoutSec* właściwości). Jeśli limit czasu wygaśnie, usługa Service Fabric kontynuuje uaktualnienie, niezależnie od kworum. To ustawienie jest ustawione jako nigdy (nieskończone) podczas toczenia do przodu i 1200 sekund podczas wycofywania. |
> | UpgradeTimeoutSec |PS, VS |Limit czasu (w sekundach), który ma zastosowanie do całego uaktualnienia. Jeśli ten limit czasu zostanie osiągnięty, uaktualnienie zatrzymuje się i *FailureAction* jest wyzwalany. Wartość domyślna nigdy nie jest (Infinite) i powinny być dostosowane odpowiednio do aplikacji. |
> | Instrukcja WhatIf | PS | Dozwolone wartości to **Prawda** i **Fałsz**. Pokazuje, co się stanie po uruchomieniu polecenia cmdlet. Polecenie cmdlet nie zostało uruchomione. |

*Kryteria MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService*i *MaxPercentUnhealthyReplicasPerPartition* można określić dla typu usługi dla wystąpienia aplikacji. Ustawienie tych parametrów na usługę umożliwia aplikacji zawiera różne typy usług z różnych zasad oceny. Na przykład typu usługi bramy bezstanowej może mieć *MaxPercentUnhealthyPartitionsPerService,* który różni się od typu usługi aparatu stanowego dla wystąpienia określonej aplikacji.

## <a name="sfctl-parameters"></a>Parametry SFCTL

Uaktualnienia aplikacji sieci szkieletowej usług przy użyciu interfejsu wiersza polecenia sieci szkieletowej usług używają polecenia [uaktualniania aplikacji sfctl](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) wraz z następującymi wymaganymi i opcjonalnymi parametrami.

### <a name="required-parameters"></a>Wymagane parametry

| Parametr | Opis |
| --- | --- |
| identyfikator aplikacji  |Identyfikator uaktualnianej aplikacji. <br> Zazwyczaj jest to pełna nazwa aplikacji bez schematu URI 'fabric:'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem ' . Jeśli na przykład nazwa aplikacji to "fabric:/myapp/app1", tożsamość aplikacji\~będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach.|
wersja aplikacji |Wersja typu aplikacji, która jest przeznaczona dla uaktualnienia.|
parameters  |A JSON zakodowana lista zastępów parametrów aplikacji, które mają być stosowane podczas uaktualniania aplikacji.|

### <a name="optional-parameters"></a>Parametry opcjonalne

| Parametr | Opis |
| --- | --- |
domyślna polityka zdrowotna usługi | [Specyfikacja](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) zakodowana w usłudze JSON zasad dotyczących kondycji używana domyślnie do oceny kondycji typu usługi. Mapa jest domyślnie pusta. |
niepowodzenie-działanie | Dozwolone wartości to **Wycofywanie,** **Ręczne**i **Nieprawidłowe**. Akcja kompensacjąca do wykonania, gdy *monitorowane* uaktualnienie napotka naruszenia zasad monitorowania lub zasad kondycji. <br>**Wycofywanie** określa, że uaktualnienie zostanie automatycznie wycofane do wersji przed uaktualnieniem. <br>**Podręcznik** wskazuje, że uaktualnienie przełączy się do trybu uaktualniania *UnmonitoredManual.* <br>**Nieprawidłowy** oznacza, że akcja błędu jest nieprawidłowa.|
force-restart | W przypadku aktualizacji konfiguracji lub pakietu danych bez aktualizowania kodu usługi usługa jest uruchamiana ponownie tylko wtedy, gdy właściwość ForceRestart jest **ustawiona**na True . Po zakończeniu aktualizacji sieci szkieletowej usług powiadamia usługę, że nowy pakiet konfiguracji lub pakiet danych jest dostępny. Usługa jest odpowiedzialna za zastosowanie zmian. W razie potrzeby usługa może zostać ponownie uruchomiona. |
health-check-retry-timeout | Czas ponowić próbę oceny kondycji, gdy aplikacja lub klaster jest w złej kondycji przed *FailureAction* jest wykonywany. Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. Domyślnie: PT0H10M0S. |
stan zdrowia-check-stabilny czas trwania | Czas, przez który aplikacja lub klaster musi pozostać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia. Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. Domyślnie: PT0H2M0S. |
czas trwania oczekiwania na sprawdzenie kondycji | Czas oczekiwania po ukończeniu domeny uaktualnienia przed zastosowaniem zasad kondycji. Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. Domyślnie: 0.|
max-unhealthy-apps | Domyślna i zalecana wartość to 0. Określ maksymalną liczbę wdrożonych aplikacji (zobacz [sekcję Zdrowie),](service-fabric-health-introduction.md)które mogą być w złej kondycji, zanim aplikacja zostanie uznana za złą w złej kondycji i nie powiedzie się uaktualnienie. Ten parametr definiuje kondycję aplikacji w węźle i pomaga wykrywać problemy podczas uaktualniania. Zazwyczaj repliki aplikacji uzyskać równoważenie obciążenia do drugiego węzła, co pozwala aplikacji w dobrej kondycji, umożliwiając w ten sposób uaktualnienie, aby kontynuować. Określając ścisłą *kondycję aplikacji w złej kondycji,* usługa Service Fabric może szybko wykryć problem z pakietem aplikacji i pomóc w szybkim uaktualnieniu. Reprezentowana jako liczba od 0 do 100. |
tryb | Dozwolone wartości to **Monitorowane**, **UpgradeMode**, **UnmonitoredAuto**, **Unmonitormanual**. Domyślnie jest **To UnmonitoredAuto**. Opisy tych wartości można znaleźć w sekcji Wymagane *parametry* programu Visual Studio i Programu PowerShell.|
limit czasu sprawdzania repliki |Mierzona w sekundach. <br>**Usługa bezstanowa**--W ramach jednej domeny uaktualnienia sieci szkieletowej usług próbuje upewnić się, że dodatkowe wystąpienia usługi są dostępne. Jeśli liczba wystąpień docelowych jest więcej niż jeden, sieci szkieletowej usług czeka na więcej niż jedno wystąpienie, aby być dostępne, maksymalnie maksymalną wartość przekroczenia czasu. Ten limit czasu jest określony przy użyciu *właściwości limit czasu repliki-set-check-timeout.* Jeśli limit czasu wygaśnie, usługa Service Fabric kontynuuje uaktualnienie, niezależnie od liczby wystąpień usługi. Jeśli liczba wystąpień docelowych jest jedna, sieci szkieletowej usług nie czeka i natychmiast przechodzi z uaktualnienia.<br><br>**Usługa stanowa**--W ramach jednej domeny uaktualnienia sieci szkieletowej usług próbuje upewnić się, że zestaw replik ma kworum. Usługa Sieci szkieletowej czeka na kworum, aby być dostępne, maksymalnie maksymalna wartość limitu czasu (określona przez właściwość limit czasu sprawdzania repliki.Service Fabric waits for a kwrum to be available, up to a maximum time-out value (specified by the *replica-set-check-timeout* property). Jeśli limit czasu wygaśnie, usługa Service Fabric kontynuuje uaktualnienie, niezależnie od kworum. To ustawienie jest ustawione jako nigdy (nieskończone) podczas toczenia do przodu i 1200 sekund podczas wycofywania. |
polityka usług zdrowotnych | Mapa zakodowana w Usłudze JSON z zasadami kondycji typu usługi dla nazwy typu usługi. Mapa jest pusta domyślnie. [Parametr JSON format.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). Część JSON dla "Wartość" zawiera **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService**i **MaxPercentUnhealthyReplicasPerPartition**. Opisy tych parametrów można znaleźć w sekcji Parametry opcjonalne programu Visual Studio i Programu PowerShell.
timeout | Określa limit czasu w sekundach dla operacji. Domyślnie: 60. |
limit czasu uaktualnienia domeny | Czas, przez który każda domena uaktualnienia musi zostać ukończona przed *wykonaniem FailureAction.* Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. Wartość domyślna nigdy nie jest (Infinite) i powinny być dostosowane odpowiednio do aplikacji. Domyślnie: P10675199DT02H48M05.4775807S. |
limit czasu uaktualnienia | Czas, przez który każda domena uaktualnienia musi zostać ukończona przed *wykonaniem FailureAction.* Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. Wartość domyślna nigdy nie jest (Infinite) i powinny być dostosowane odpowiednio do aplikacji. Domyślnie: P10675199DT02H48M05.4775807S.|
ostrzeżenie jako błąd | Dozwolone wartości to **Prawda** i **Fałsz**. Wartość domyślna to **False**. Może być przekazywana jako flaga. Potraktuj zdarzenia kondycji ostrzeżenia dla aplikacji jako błędy podczas oceny kondycji aplikacji podczas uaktualniania. Domyślnie sieć szkieletowa usług nie ocenia zdarzeń kondycji ostrzeżenia za błędy (błędy), więc uaktualnienie można kontynuować, nawet jeśli występują zdarzenia ostrzegawcze. |

## <a name="next-steps"></a>Następne kroki
[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu Visual Studio.

[Uaktualnianie aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu PowerShell.

[Uaktualnianie aplikacji przy użyciu interfejsu wiersza polecenia sieci szkieletowej usług w systemie Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu interfejsu wiersza polecenia sieci szkieletowej usług.

[Uaktualnianie aplikacji przy użyciu wtyczki Eclipse usługi Fabric](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Upewnij się, że uaktualnienia aplikacji są zgodne, ucząc się, jak korzystać z [serializacji danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [tematów zaawansowanych](service-fabric-application-upgrade-advanced.md).

Rozwiąż typowe problemy w uaktualnieniach aplikacji, odwołując się do kroków [rozwiązywania problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).
