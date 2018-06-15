---
title: Rozwiązywanie problemów z uaktualnieniami aplikacji | Dokumentacja firmy Microsoft
description: W tym artykule opisano kilka typowych problemów wokół uaktualnianie aplikacji usługi Service Fabric i sposobu ich rozwiązania.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: c6ba61354bf7466819e34a0d619a5a1820dd7b90
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212742"
---
# <a name="troubleshoot-application-upgrades"></a>Rozwiązywanie problemów z uaktualnieniami aplikacji
W tym artykule omówiono niektóre typowe problemy dotyczące uaktualniania aplikacji sieci szkieletowej usług Azure i sposobu ich rozwiązania.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Rozwiązywanie problemów z uaktualniania aplikacji nie powiodło się
Jeśli uaktualnienie nie powiedzie się, dane wyjściowe **Get-ServiceFabricApplicationUpgrade** polecenia zawiera dodatkowe informacje dotyczące debugowania błędu.  Poniższa lista określa, jak można uzyskać dodatkowe informacje:

1. Określ typ awarii.
2. Określ przyczynę niepowodzenia.
3. Określ co najmniej jednego składnika Niepowodzenie w celu dokładniejszego zbadania.

Te informacje są dostępne, gdy usługa sieć szkieletowa wykrywa błąd niezależnie od tego, czy **FailureAction** wycofać lub zawiesić uaktualnienia.

### <a name="identify-the-failure-type"></a>Określ typ awarii
W danych wyjściowych **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identyfikuje znacznik czasu (w formacie UTC) jaką błąd uaktualnienia został wykryty przez sieć szkieletowa usług i  **FailureAction** zostało wyzwolone. **Przyczyna błędu** identyfikuje jeden z trzech potencjalnych wysokiego poziomu przyczyny awarii:

1. UpgradeDomainTimeout — wskazuje, że konkretnej domeny uaktualnienia trwało zbyt długo i **UpgradeDomainTimeout** wygasł.
2. OverallUpgradeTimeout — wskazuje, czy ogólną uaktualnienia trwało zbyt długo, aby zakończyć i **UpgradeTimeout** wygasł.
3. Test kondycji — wskazuje, że po uaktualnieniu domeny aktualizacji pozostała zła zgodnie z zasad dotyczących kondycji określonej aplikacji i **HealthCheckRetryTimeout** wygasł.

Te wpisy tylko widoczne w danych wyjściowych podczas uaktualniania nie powiedzie się i uruchamia wycofywanie. Dalsze informacje są wyświetlane w zależności od typu błędu.

### <a name="investigate-upgrade-timeouts"></a>Zbadaj uaktualnienia limitów czasu
Uaktualnij limit błędów są najczęściej spowodowane problemów dotyczących dostępności usługi. Dane wyjściowe dalej w tym temacie są typowe dla uaktualnienia, jeżeli repliki usługi lub wystąpienia nie można uruchomić w nowej wersji kodu. **UpgradeDomainProgressAtFailure** pola przechwytuje migawki wszelkie oczekujące pracy uaktualniania w chwili awarii.

```
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

W tym przykładzie, uaktualnienie nie powiodło się w domenie uaktualnienia *MYUD1* i dwóch partycji (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* i *4b43f4d8-b26b-424e-9307-7a7a62e79750*) zostały zatrzymane. Partycje zostały zatrzymane, ponieważ środowisko uruchomieniowe nie mógł umieścić replik podstawowych (*WaitForPrimaryPlacement*) na węzłach docelowych *Node1* i *Węzeł4*.

**Get-ServiceFabricNode** polecenia można sprawdzić, czy oba te węzły są w domenie uaktualnienia *MYUD1*. *UpgradePhase* mówi *PostUpgradeSafetyCheck*, co oznacza, że występuje kontrole bezpieczeństwa po we wszystkich węzłach w domenie uaktualnienia zakończeniu uaktualniania. Te informacje wskazuje potencjalny problem z nową wersją kodu aplikacji. Najbardziej typowe problemy są błędy usługi open lub podwyższeniu ścieżki podstawowego kodu.

*UpgradePhase* z *PreUpgradeSafetyCheck* oznacza, że wystąpiły problemy przygotowanie domeny uaktualnień, przed jego została wykonana. Najbardziej typowe problemy, w tym przypadku są błędy usługi zamknięcia lub obniżania poziomu od ścieżki głównej kodu.

Bieżący **UpgradeState** jest *RollingBackCompleted*, więc oryginalnego uaktualniania należy wykonać o wycofaniu **FailureAction**, która automatycznie walcowane Tworzenie kopii przypadku niepowodzenia uaktualnienia. W przypadku uaktualniania oryginalnego ręcznego **FailureAction**, a następnie Uaktualnianie czy zamiast tego można w stanie wstrzymanym, aby zezwolić na żywo debugowania aplikacji.

W rzadkich przypadkach **UpgradeDomainProgressAtFailure** pole może być puste, jeśli uaktualnienie ogólną upłynie limit czasu tak samo, jak system ukończeniu pracy wszystkie bieżąca domena uaktualnienia. W takim przypadku spróbuj zwiększyć **UpgradeTimeout** i **UpgradeDomainTimeout** uaktualnienia wartości parametrów i ponów uaktualnienie.

### <a name="investigate-health-check-failures"></a>Zbadaj błędy sprawdzania kondycji
Błędy sprawdzania kondycji mogą być wyzwalane przez różne problemy, które mogą wystąpić po zakończeniu wszystkich węzłów w domenie uaktualnienia, uaktualniania i przekazywania wszystkich kontroli bezpieczeństwa. Dane wyjściowe dalej w tym temacie są typowe dla niepowodzenia uaktualnienia z powodu kontroli kondycji nie powiodło się. **UnhealthyEvaluations** pola przechwytuje migawki kontroli kondycji, których nie powiodła się podczas uaktualnienia zgodnie z określonym [zasad dotyczących kondycji](service-fabric-health-introduction.md).

```
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Najpierw niepowodzeń sprawdzenia kondycji do badania wymaga zrozumienia model kondycji sieci szkieletowej usług. Ale nawet bez takich dobrej znajomości, możemy stwierdzić, czy dwie usługi są w złej kondycji: *sieci szkieletowej: / DemoApp/Svc3* i *fabric: / DemoApp/Svc2*, wraz z raportów o błędach kondycji ("InjectedFault" w tym przypadku). W tym przykładzie dwóch spośród czterech usługi jest w złej kondycji, która jest poniżej wartości docelowej domyślnej 0% złej kondycji (*MaxPercentUnhealthyServices*).

Uaktualnianie zostało zawieszone po niepowodzeniu określając **FailureAction** ręczne uaktualnienie do rozpoczęcia. Ten tryb umożliwia firmie Microsoft w celu zbadania systemu na żywo w stanie niepowodzenia przed podjęciem dalszych działań.

### <a name="recover-from-a-suspended-upgrade"></a>Odzyskiwanie z wstrzymane uaktualnienia
O wycofaniu **FailureAction**, jest odzyskanie nie jest wymagane, ponieważ uaktualnienie automatycznie przywraca po niepowodzeniu. Ręcznego **FailureAction**, dostępnych jest kilka opcji odzyskiwania:

1.  wyzwalacz wycofywania
2. Postępuj zgodnie z instrukcjami w pozostałej części uaktualnianie ręcznie
3. Wznawianie uaktualniania monitorowanych

**Start ServiceFabricApplicationRollback** polecenia można w dowolnym momencie uruchomić wycofywanie aplikacji. Po polecenie zwraca pomyślnie, żądanie wycofania został zarejestrowany w systemie i rozpoczyna się w krótkim czasie.

**Polecenie Resume-ServiceFabricApplicationUpgrade** polecenia można ręcznie, kontynuuj pracę pozostałą część uaktualnienia domeny uaktualnienia pojedynczo. W tym trybie bezpieczeństwa tylko testy są wykonywane przez system. Brak kondycji są sprawdzane. To polecenie może być tylko używane podczas *UpgradeState* pokazuje *RollingForwardPending*, co oznacza, że bieżąca domena uaktualnienia zakończył uaktualnianie, ale kolejnego nie została uruchomiona (oczekiwanie).

**ServiceFabricApplicationUpgrade aktualizacji** polecenia można wznowić monitorowanych uaktualnienia z obu bezpieczeństwa i kontrole kondycji wykonywane.

```
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
```

Uaktualnianie będzie kontynuowane z domeny uaktualnień, których ostatnio został wstrzymany i użycia uaktualnienia takie same parametry i zasady dotyczące kondycji jako przed. W razie potrzeby, parametry uaktualniania i zasady dotyczące kondycji wyświetlany w powyższych danych wyjściowych można zmienić w tym samym poleceniu po wznowieniu pracy uaktualniania. W tym przykładzie uaktualnienie zostało wznowione w trybie monitorowana, parametry i zasady dotyczące kondycji bez zmian.

## <a name="further-troubleshooting"></a>Dodatkowe informacje o rozwiązywaniu
### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Sieć szkieletowa usług nie jest następujące zasady określonej kondycji
Możliwa przyczyna 1:

Sieć szkieletowa usług tłumaczy wszystkie wartości procentowe na rzeczywistą liczbę jednostek (na przykład replik, partycji i usług) do oceny kondycji i zawsze zaokrągla w górę do całej jednostki. Na przykład jeśli maksymalna *MaxPercentUnhealthyReplicasPerPartition* % 21 i istnieją pięciu replik, a następnie do dwóch replik zła umożliwia sieci szkieletowej usług (czyli`Math.Ceiling (5*0.21)`). W związku z tym zasady dotyczące kondycji powinien być odpowiednio ustawiony.

Możliwe przyczyny 2:

Zasady dotyczące kondycji są określane w przeliczeniu na wartości procentowe całkowita usługi i wystąpień nie określonej usługi. Na przykład przed uaktualnieniem, jeśli aplikacja ma cztery usługi wystąpień A "," B "," C "i" D, w przypadku, gdy usługa D jest zła, ale mały wpływ na aplikację. Chcemy zignorować znanej usługi zła D podczas uaktualniania i ustaw dla parametru *MaxPercentUnhealthyServices* 25%, zakładając, że tylko A, B i C muszą być dobrej kondycji.

Jednak podczas uaktualniania D mogą stać się dobra podczas C staje się nieprawidłowy. Uaktualnianie może nadal się powieść, ponieważ tylko 25% usługi jest w złej kondycji. Jednak może to spowodować nieprzewidziane błędy z powodu C jest nieoczekiwanie niezdrowa zamiast D. W takiej sytuacji należy należy modelować D jako typ innej usługi, a, B i C. Ponieważ zasady dotyczące kondycji są określone dla typów usług, progi różnych Zła wartość procentowa może odnosić się do różnych usług. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Nie określono zasadę dla aplikacji, ale nadal nieudanego uaktualnienia dla niektóre błędy przekroczenia limitu czasu, które nigdy nie została określona
Gdy zasady dotyczące kondycji nie są dostarczane na żądanie uaktualnienia są pobierane z *ApplicationManifest.xml* bieżącej wersji aplikacji. Na przykład Jeśli uaktualniasz X aplikacji w wersji 1.0 w wersji 2.0, zasady dotyczące kondycji aplikacji określone dla w wersji 1.0 są używane. Jeśli zasadę różnych powinno być używane do uaktualnienia, zasad musi być określony jako część wywołania interfejsu API uaktualniania aplikacji. Zasady określony jako część wywołania interfejsu API są stosowane tylko podczas uaktualniania. Po ukończeniu uaktualniania zasady określone w *ApplicationManifest.xml* są używane.

### <a name="incorrect-time-outs-are-specified"></a>Podano niepoprawny limity czasu
Może mieć zastanawiasz się o co się dzieje, gdy niespójnie przekroczeń limitu czasu. Na przykład może być *UpgradeTimeout* to jest mniejsza niż *UpgradeDomainTimeout*. Odpowiedzią jest zwracany jest błąd. Błędy są zwracane, jeśli *UpgradeDomainTimeout* jest mniejsza niż suma *HealthCheckWaitDuration* i *HealthCheckRetryTimeout*, lub jeśli  *UpgradeDomainTimeout* jest mniejsza niż suma *HealthCheckWaitDuration* i *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Moje uaktualnień trwa zbyt długo
Czas na zakończenie uaktualniania zależy od sprawdzania kondycji i określić limity czasu. Sprawdzanie kondycji i limity czasu są zależne od czas potrzebny do skopiowania, wdrażanie i ustabilizowania aplikacji. Trwa zbyt agresywnie z limitów czasu może to oznaczać więcej uaktualnienia nie powiodło się, dlatego zalecamy konserwatywnie począwszy od dłuższego limitu czasu.

Poniżej przedstawiono szybki odświeżacz na interakcje limitów czasu w czasie uaktualniania:

Uaktualnia dla domeny uaktualnienia nie można ukończyć szybciej niż *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Niepowodzenie uaktualniania nie może być szybsza niż *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Czas uaktualniania dla domeny uaktualnienia jest ograniczona przez *UpgradeDomainTimeout*.  Jeśli *HealthCheckRetryTimeout* i *HealthCheckStableDuration* są zarówno niezerowy i kondycji aplikacji przechowuje przełączania i z powrotem, a następnie Uaktualnianie ostatecznie upłynie limit czasu na *UpgradeDomainTimeout*. *UpgradeDomainTimeout* odlicza w dół po uaktualnieniu dla rozpoczyna się w bieżącej domenie uaktualnienia.

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnianie aplikacji za pomocą Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu programu Visual Studio.

[Uaktualnienie z aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu programu PowerShell.

Kontrolowanie sposobu uaktualnienia aplikacji przy użyciu [uaktualnienia parametrów](service-fabric-application-upgrade-parameters.md).

Uzyskania uaktualnień aplikacji zgodnych przez poznanie [szeregowanie danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).
