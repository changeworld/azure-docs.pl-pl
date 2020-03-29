---
title: Rozwiązywanie problemów z uaktualnieniami aplikacji
description: W tym artykule opisano niektóre typowe problemy dotyczące uaktualniania aplikacji sieci szkieletowej usług i sposobu ich rozwiązania.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377926"
---
# <a name="troubleshoot-application-upgrades"></a>Rozwiązywanie problemów z uaktualnieniami aplikacji

W tym artykule opisano niektóre typowe problemy dotyczące uaktualniania aplikacji sieci szkieletowej usług Azure i sposobu ich rozwiązania.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Rozwiązywanie problemów z nieudanym uaktualnieniem aplikacji

Gdy uaktualnienie nie powiedzie się, dane **wyjściowe polecenia Get-ServiceFabricApplicationUpligrade** zawiera dodatkowe informacje dotyczące debugowania błędu.  Poniższa lista określa, w jaki sposób można wykorzystać dodatkowe informacje:

1. Zidentyfikuj typ błędu.
2. Zidentyfikuj przyczynę awarii.
3. Wyizolować jeden lub więcej składników, które nie mają więcej informacji, w celu dalszego zbadania.

Te informacje są dostępne, gdy sieci szkieletowej usług wykryje błąd, niezależnie od tego, czy **FailureAction** jest wycofać lub zawiesić uaktualnienie.

### <a name="identify-the-failure-type"></a>Identyfikowanie typu błędu

W danych wyjściowych **Get-ServiceFabricApplicationUpgrade,** **FailureTimestampUtc** identyfikuje sygnaturę czasową (w czasie UTC), w którym została wykryta awaria uaktualnienia przez sieci szkieletowej usług i **failureAction** został wyzwolony. **FailureReason** identyfikuje jedną z trzech potencjalnych przyczyn wysokiego poziomu awarii:

1. UpgradeDomainTimeout — wskazuje, że określonej domeny uaktualnienia trwało zbyt długo, aby zakończyć i **UpgradeDomainTimeout** wygasła.
2. Ogólna aktualizacjaTimeout — wskazuje, że ogólne uaktualnienie trwało zbyt długo, aby zakończyć i **UpgradeTimeout** wygasł.
3. HealthCheck - Wskazuje, że po uaktualnieniu domeny aktualizacji, aplikacja pozostała w złej kondycji zgodnie z określonymi zasadami kondycji i **HealthCheckRetryTimeout** wygasła.

Te wpisy są wyświetlane tylko w danych wyjściowych, gdy uaktualnienie kończy się niepowodzeniem i rozpoczyna wycofywanie. Dalsze informacje są wyświetlane w zależności od typu błędu.

### <a name="investigate-upgrade-timeouts"></a>Badanie limitów czasu uaktualnienia

Błędy limitu czasu uaktualnienia są najczęściej spowodowane problemami z dostępnością usługi. Dane wyjściowe następujące po tym akapicie jest typowe dla uaktualnień, gdzie repliki usługi lub wystąpień nie można uruchomić w nowej wersji kodu. **Pole UpgradeDomainProgressAtFailure** przechwytuje migawkę wszelkich oczekujących prac uaktualnianych w momencie awarii.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
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

W tym przykładzie uaktualnienie nie powiodło się w domenie uaktualnienia *MYUD1* i dwie partycje (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* i *4b43f4d8-b26b-424e-9307-7a7a62e797500*) zostały zablokowane. Partycje utknęły, ponieważ środowisko wykonawcze nie może umieścić replik podstawowych (*WaitForPrimaryPlacement*) w węzłach docelowych *Node1* i *Node4*.

Polecenie **Get-ServiceFabricNode** może służyć do sprawdzenia, czy te dwa węzły znajdują się w domenie uaktualnienia *MYUD1*. *UpgradePhase* mówi *PostUpgradeSafetyCheck*, co oznacza, że te kontrole bezpieczeństwa występują po zakończeniu uaktualniania wszystkich węzłów w domenie uaktualnienia. Wszystkie te informacje wskazują na potencjalny problem z nową wersją kodu aplikacji. Najczęstsze problemy to błędy usługi w otwartych lub promocji do podstawowych ścieżek kodu.

*UpgradePhase* *preupgradeSafetyCheck* oznacza, że wystąpiły problemy z przygotowaniem domeny uaktualnienia przed jej wykonaniem. Najczęstsze problemy w tym przypadku są błędy usługi w zamknięciu lub degradacji z podstawowych ścieżek kodu.

Bieżący **UpgradeState** jest *RollingBackCompleted*, więc oryginalne uaktualnienie musi być wykonane z **wycofywania FailureAction**, który automatycznie wycofać uaktualnienia po awarii. Jeśli oryginalna aktualizacja została wykonana z ręcznego **FailureAction**, a następnie uaktualnienie będzie zamiast tego w stanie wstrzymania, aby umożliwić debugowanie na żywo aplikacji.

W rzadkich przypadkach **pole UpgradeDomainProgressAtFailure** może być puste, jeśli całkowity czas uaktualnienia, tak jak system zakończy całą pracę dla bieżącej domeny uaktualnienia. Jeśli tak się stanie, spróbuj zwiększyć **upgradetimeout** i **UpgradeDomainTimeout** wartości parametrów uaktualnienia i ponów próbę uaktualnienia.

### <a name="investigate-health-check-failures"></a>Badanie błędów sprawdzania kondycji

Błędy sprawdzania kondycji mogą być wyzwalane przez różne problemy, które mogą się zdarzyć po zakończeniu uaktualnienia wszystkich węzłów w domenie uaktualnienia i przekazywania wszystkich kontroli bezpieczeństwa. Dane wyjściowe następujące po tym akapicie jest typowe dla niepowodzenia uaktualnienia z powodu nieudanych kontroli kondycji. W polu **Niezdrowe wartości** przechwytuje migawkę kontroli kondycji, które nie powiodły się w momencie uaktualnienia zgodnie z określonymi [zasadami kondycji.](service-fabric-health-introduction.md)

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
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

Badanie błędów sprawdzania kondycji najpierw wymaga zrozumienia modelu kondycji sieci szkieletowej usług. Ale nawet bez tak dogłębnego zrozumienia, widzimy, że dwie usługi są niezdrowe: *fabric:/DemoApp/Svc3* i *fabric:/DemoApp/Svc2*, wraz z raportami o kondycji błędu ("InjectedFault" w tym przypadku). W tym przykładzie dwie z czterech usług są niezdrowe, która jest poniżej domyślnego celu 0% w złej kondycji (*MaxPercentUnhealthyServices*).

Uaktualnienie zostało zawieszone po niepowodzeniu, określając **FailureAction** ręcznego podczas uruchamiania uaktualnienia. Ten tryb pozwala nam zbadać system na żywo w stanie awarii przed podjęciem jakichkolwiek dalszych działań.

### <a name="recover-from-a-suspended-upgrade"></a>Odzyskiwanie po zawieszeniu uaktualnienia

Z **wycofywania FailureAction**, nie ma potrzeby odzyskiwania, ponieważ uaktualnienie automatycznie wycofuje się po awarii. W przypadku ręcznego **działania FailureAction**istnieje kilka opcji odzyskiwania:

1.  wyzwalanie wycofywania
2. Kontynuuj dalszą część uaktualnienia ręcznie
3. Wznowienie monitorowane uaktualnienia

Polecenie **Start-ServiceFabricApplicationRollback** może być używane w dowolnym momencie, aby rozpocząć wycofywanie aplikacji. Gdy polecenie zwraca pomyślnie, żądanie wycofywania zostało zarejestrowane w systemie i rozpoczyna się wkrótce po tym.

Polecenie **Resume-ServiceFabricApplicationUpgrade** może służyć do ręcznego przechodzenia przez pozostałą część uaktualnienia, po jednej domenie uaktualnienia naraz. W tym trybie system przeprowadza tylko kontrole bezpieczeństwa. Nie przeprowadza się już kontroli kondycji. To polecenie może być używane tylko wtedy, gdy *UpgradeState* pokazuje *RollingForwardPending*, co oznacza, że bieżąca domena uaktualnienia zakończyła uaktualnianie, ale następna nie została uruchomiona (oczekująca).

Polecenie **Update-ServiceFabricApplicationUpgrade** może służyć do wznowienia monitorowanego uaktualnienia z przeprowadzanym zarówno kontrolami bezpieczeństwa, jak i kondycji.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
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
```

Uaktualnienie jest kontynuowane z domeny uaktualnienia, w której została ostatnio zawieszona i używa tych samych parametrów uaktualniania i zasad kondycji, jak poprzednio. W razie potrzeby można zmienić dowolne parametry uaktualnienia i zasady kondycji pokazane w poprzednim wyniku wyjściowym, które można zmienić w tym samym poleceniu po wznowieniu uaktualnienia. W tym przykładzie uaktualnienie zostało wznowione w trybie monitorowanym, z parametrami i zasadami kondycji bez zmian.

## <a name="further-troubleshooting"></a>Dalsze rozwiązywanie problemów

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Sieci szkieletowej usług nie jest zgodne z określonymi zasadami kondycji

Możliwa przyczyna 1:

Sieć szkieletowa usług przekłada wszystkie wartości procentowe na rzeczywistą liczbę jednostek (na przykład repliki, partycje i usługi) do oceny kondycji i zawsze zaokrągla do całych jednostek. Na przykład jeśli *maksymalna maksymalna wartość MaxPercentUnhealthyReplicasPerPartition* wynosi 21% i istnieje pięć replik, wówczas usługa Service Fabric zezwala na maksymalnie dwie repliki w złej kondycji (czyli`Math.Ceiling (5*0.21)`). W związku z tym należy odpowiednio ustalić politykę zdrowotną.

Możliwa przyczyna 2:

Zasady kondycji są określone w procentach całkowitej liczby usług, a nie określonych wystąpień usługi. Na przykład przed uaktualnieniem, jeśli aplikacja ma cztery wystąpienia usługi A, B, C i D, gdzie usługa D jest w złej kondycji, ale z niewielkim wpływem na aplikację. Chcemy zignorować znaną usługę w złej kondycji D podczas uaktualniania i ustawić parametr *MaxPercentUnhealthyServices* na 25%, przy założeniu, że tylko A, B i C muszą być w dobrej kondycji.

Jednak podczas uaktualniania, D może stać się zdrowy, podczas gdy C staje się niezdrowe. Uaktualnienie będzie nadal sukces, ponieważ tylko 25% usług są niezdrowe. Jednak może to spowodować nieoczekiwane błędy z powodu C jest nieoczekiwanie w złej kondycji zamiast D. W tej sytuacji D powinny być modelowane jako inny typ usługi z A, B i C. Ponieważ zasady kondycji są określone dla typu usługi, różne progi procentowe w złej kondycji mogą być stosowane do różnych usług. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Nie określiłem zasad kondycji dla uaktualnienia aplikacji, ale uaktualnienie nadal kończy się niepowodzeniem dla niektórych limitów czasu, które nigdy nie określiłem

Gdy zasady kondycji nie są dostarczane do żądania uaktualnienia, są one pobierane z *ApplicationManifest.xml* bieżącej wersji aplikacji. Na przykład w przypadku uaktualniania aplikacji X z wersji 1.0 do wersji 2.0 używane są zasady dotyczące kondycji aplikacji określone w wersji 1.0. Jeśli różne zasady kondycji powinny być używane do uaktualniania, a następnie zasady muszą być określone jako część wywołania interfejsu API uaktualnienia aplikacji. Zasady określone jako część wywołania interfejsu API mają zastosowanie tylko podczas uaktualniania. Po zakończeniu uaktualniania używane są zasady określone w *pliku ApplicationManifest.xml.*

### <a name="incorrect-time-outs-are-specified"></a>Określono nieprawidłowe limity czasu

Być może zastanawiałeś się, co się dzieje, gdy limity czasu są ustawione niespójnie. Na przykład może mieć *UpgradeTimeout,* który jest mniejszy niż *UpgradeDomainTimeout*. Odpowiedź jest, że zwracany jest błąd. Błędy są zwracane, jeśli *UpgradeDomainTimeout* jest mniejsza niż suma *HealthCheckWaitDuration* i *HealthCheckRetryTimeout*, lub jeśli *UpgradeDomainTimeout* jest mniejsza niż suma *HealthCheckWaitDuration* i *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Moje aktualizacje trwają zbyt długo

Czas ukończenia uaktualnienia zależy od określonych kontroli kondycji i limitów czasu. Sprawdzanie kondycji i limity czasu zależą od czasu potrzebnym do kopiowania, wdrażania i stabilizacji aplikacji. Zbyt agresywne z limitów czasu może oznaczać więcej nieudanych uaktualnień, więc zalecamy rozpoczęcie zachowawczo z dłuższym limitem czasu.

Oto szybkie odświeżenie sposobu interakcji limitów czasu z czasem uaktualnienia:

Uaktualnienia dla domeny uaktualnienia nie może zakończyć się szybciej niż *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Błąd uaktualnienia nie może wystąpić szybciej niż *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Czas uaktualnienia domeny uaktualnienia jest ograniczony przez *UpgradeDomainTimeout*.  Jeśli *HealthCheckRetryTimeout* i *HealthCheckStableDuration* są zarówno non-zero i kondycji aplikacji utrzymuje przełączania tam iz powrotem, a następnie uaktualnić ostatecznie limit czasu na *UpgradeDomainTimeout*. *UpgradeDomainTimeout* rozpoczyna odliczanie po rozpoczęciu uaktualnienia dla bieżącej domeny uaktualnienia.

## <a name="next-steps"></a>Następne kroki

[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu Visual Studio.

[Uaktualnianie aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu PowerShell.

Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).

Upewnij się, że uaktualnienia aplikacji są zgodne, ucząc się, jak korzystać z [serializacji danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [tematów zaawansowanych](service-fabric-application-upgrade-advanced.md).