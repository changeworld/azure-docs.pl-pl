---
title: Rozwiązywanie problemów z uaktualnieniami aplikacji | Dokumentacja firmy Microsoft
description: W tym artykule omówiono niektóre typowe problemy dotyczące uaktualniania aplikacji usługi Service Fabric i ich rozwiązania.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 9e4989f61741d317e78a613c8c8fac312d1568c2
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666957"
---
# <a name="troubleshoot-application-upgrades"></a>Rozwiązywanie problemów z uaktualnieniami aplikacji
W tym artykule omówiono niektóre typowe problemy dotyczące uaktualniania aplikacji usługi Azure Service Fabric i ich rozwiązania.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Rozwiązywanie problemów z uaktualnienia aplikacji nie powiodło się
Jeśli uaktualnienie nie powiedzie się, dane wyjściowe **Get ServiceFabricApplicationUpgrade** polecenia zawiera dodatkowe informacje dotyczące debugowania błędu.  Poniższa lista określa, jak można uzyskać dodatkowe informacje:

1. Określ typ awarii.
2. Zidentyfikuj przyczynę błędu.
3. Izolowanie co najmniej jednego składnika Niepowodzenie w celu bliższego zbadania problemu.

Te informacje są dostępne w przypadku, gdy Usługa Service Fabric wykrywa błąd, niezależnie od tego, czy **FailureAction** wycofać lub zawiesić uaktualnienia.

### <a name="identify-the-failure-type"></a>Identyfikator typu błędu
W danych wyjściowych **Get ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identyfikuje znacznik czasu (w formacie UTC) wykryto Niepowodzenie uaktualniania przez usługę Service Fabric i  **FailureAction** zostało wyzwolone. **FailureReason** identyfikuje jeden z trzech możliwych przyczyn wysokiego poziomu błędu:

1. UpgradeDomainTimeout — wskazuje, czy określonej domeny uaktualnienia trwało zbyt długo i **UpgradeDomainTimeout** wygasł.
2. OverallUpgradeTimeout — wskazuje, czy ogólnej uaktualnienia trwało zbyt długo, aby zakończyć i **UpgradeTimeout** wygasł.
3. Test kondycji — wskazuje, że po uaktualnieniu domena aktualizacji aplikacji pozostaje złej kondycji, zgodnie z zasadami określonej kondycji i **HealthCheckRetryTimeout** wygasł.

Te wpisy tylko wyświetlana w danych wyjściowych podczas uaktualniania nie powiedzie się i rozpoczyna się wycofywanie. Dalsze informacje są wyświetlane w zależności od typu błędu.

### <a name="investigate-upgrade-timeouts"></a>Badanie uaktualnienia przekroczeń limitu czasu
Limit czasu, które błędy są najczęściej spowodowane przez problemy z dostępnością usługi uaktualniania. Dane wyjściowe poniżej tego akapitu jest typowy dla uaktualnień, gdzie usługa replik lub wystąpień nie można uruchomić w nowej wersji kodu. **UpgradeDomainProgressAtFailure** pola przechwytuje migawkę żadnych oczekujących działań uaktualnienia w chwili awarii.

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

W tym przykładzie uaktualnienie nie powiodło się w domenie uaktualnienia *MYUD1* i dwie partycje (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* i *4b43f4d8-b26b-424e-9307-7a7a62e79750*) zostały zatrzymane. Partycje zostały zablokowane, ponieważ nie można umieścić replikami podstawowymi środowiska uruchomieniowego (*WaitForPrimaryPlacement*) na węzłach docelowych *Węzeł1* i *Węzeł4*.

**Get-ServiceFabricNode** polecenie służy do Sprawdź, że te dwa węzły znajdują się w domenie uaktualnienia *MYUD1*. *UpgradePhase* mówi *PostUpgradeSafetyCheck*, co oznacza, że kontrole bezpieczeństwa występują po wszystkich węzłów w domenie uaktualnień została zakończona, uaktualnianie. Te informacje wskazuje na potencjalny problem z nową wersją kodu aplikacji. Najbardziej typowe problemy z błędów usługi open lub przenoszenia do ścieżek kodu podstawowego.

*UpgradePhase* z *PreUpgradeSafetyCheck* oznacza, że wystąpiły problemy, przygotowanie domeny uaktualnień, przed zostało ono wykonane. Najczęściej spotykanych problemów w tym przypadku są błędy usługi Zamknij lub obniżania poziomu od ścieżki głównej kodu.

Bieżący **UpgradeState** jest *RollingBackCompleted*, dzięki czemu oryginalne uaktualniania należy wykonać przy użyciu wycofywania **FailureAction**, który automatycznie walcowane Utwórz kopię uaktualnienia w przypadku awarii. W przypadku oryginalnej uaktualniania ręcznego **FailureAction**, a następnie uaktualnienie będzie zamiast tego jest w stanie wstrzymania, aby zezwolić na żywo debugowania aplikacji.

W rzadkich przypadkach **UpgradeDomainProgressAtFailure** pole może być puste, jeśli uaktualnienie ogólną upłynie limit czasu tak samo, jak system zakończy wszystkie prace dla bieżąca domena uaktualnienia. W takim przypadku spróbuj zwiększyć **UpgradeTimeout** i **UpgradeDomainTimeout** uaktualnienia wartości parametrów i ponów uaktualnienie.

### <a name="investigate-health-check-failures"></a>Zbadaj błędy sprawdzania kondycji
Błędy sprawdzania kondycji mogą być wywoływane przez różne problemy, które mogą wystąpić po zakończeniu wszystkich węzłów w domenie uaktualnienia, uaktualniania i przekazywania wszystkich kontroli bezpieczeństwa. Dane wyjściowe poniżej tego akapitu jest typowy dla uaktualnienie niepowodzenia kontroli kondycji nie powiodło się. **UnhealthyEvaluations** pola przechwytuje migawkę kontrole kondycji, które nie powiodły się w czasie uaktualniania zgodnie z określonym [zasad dotyczących kondycji](service-fabric-health-introduction.md).

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

Pierwsze badanie błędy sprawdzania kondycji wymaga zrozumienia model kondycji usługi Service Fabric. Jednak takie szczegółowe informacje, nawet jeśli nie widać, że dwie usługi są w złej kondycji: *Service fabric: / DemoApp/Svc3* i *Service fabric: / DemoApp/Svc2*, wraz z raportami kondycji błędu ("InjectedFault" w tym przypadku). W tym przykładzie dwie z czterech usługi jest w złej kondycji, która jest poniżej wartości docelowej domyślnej 0% złej kondycji (*MaxPercentUnhealthyServices*).

Uaktualnianie została zawieszona z chwilą niepowodzenie, określając **FailureAction** podręcznika podczas uruchamiania uaktualniania. Ten tryb pozwala nam badanie system na żywo w stanie niepowodzenia, przed podjęciem dalszych działań.

### <a name="recover-from-a-suspended-upgrade"></a>Odzyskiwanie z wstrzymane uaktualnienia
Przy użyciu wycofywania **FailureAction**, nie jest wymagane, ponieważ uaktualnienie automatyczne wycofanie na niepowodzenie. Za pomocą ręcznego **FailureAction**, dostępnych jest kilka opcji odzyskiwania:

1.  wyzwalacz wycofywania
2. Przejdź przez pozostałą część uaktualnienia ręcznie
3. Wznów monitorowanych uaktualnienia

**Start ServiceFabricApplicationRollback** polecenia można w dowolnym momencie można uruchomić, wycofywanie aplikacji. Gdy polecenie jest zwracane pomyślnie, żądanie rollback został zarejestrowany w systemie i uruchamia dostępna wkrótce.

**ServiceFabricApplicationUpgrade Wznów** polecenie może służyć do przejdź przez pozostałą część uaktualnienia ręcznie, jedną domenę uaktualnienia w danym momencie. W tym trybie bezpieczeństwa tylko testy są wykonywane przez system. Nie ma więcej kontroli kondycji są wykonywane. To polecenie może być tylko używane podczas *UpgradeState* pokazuje *RollingForwardPending*, co oznacza, że bieżąca domena uaktualnienia zakończył uaktualnianie, ale następny nie została uruchomiona (oczekiwanie).

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

Uaktualnianie będzie kontynuowane z domeny uaktualnień, w której ostatnio została zawieszona i użycia, Uaktualnij takie same parametry i zasady dotyczące kondycji jako przed. Jeśli to konieczne, wszystkie parametry uaktualniania oraz zasady dotyczące kondycji pokazano w powyższym danych wyjściowych można zmienić w tym samym poleceniu po wznowieniu działania uaktualnienia. W tym przykładzie uaktualnienie zostało wznowione w trybie monitorowania za pomocą parametrów i zasady dotyczące kondycji, bez zmian.

## <a name="further-troubleshooting"></a>Dodatkowe informacje o rozwiązywaniu
### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Usługi Service Fabric nie jest następujące zasady dotyczące kondycji określony
Możliwe przyczyny 1:

Usługa Service Fabric wykonuje translację wszystkich wartości procentowych na rzeczywistą liczbę jednostek (na przykład replik, partycji i usługi) dla oceny kondycji i zawsze zaokrągla w górę do całej jednostki. Na przykład jeśli maksymalną *MaxPercentUnhealthyReplicasPerPartition* wynosi 21% wiąże się z pięciu replik, a następnie zezwala na maksymalnie dwie repliki w złej kondycji usługi Service Fabric (czyli`Math.Ceiling (5*0.21)`). Zatem zasady dotyczące kondycji należy ustawić odpowiednio.

Możliwe przyczyny 2:

Zasady dotyczące kondycji są określane w przeliczeniu na wartości procentowe łączna liczba usług oraz wystąpień usługi nie odnoszą się. Na przykład, przed uaktualnieniem, jeśli aplikacja ma cztery usługi wystąpień, A, B, C i D, w przypadku, gdy usługa D jest w złej kondycji, ale z większego wpływu na aplikację. Chcemy zignorować znane usługi w złej kondycji D podczas uaktualniania i ustaw dla parametru *MaxPercentUnhealthyServices* do 25%, zakładając, że tylko A, B i C muszą być w dobrej kondycji.

Jednak podczas uaktualniania, D może stać się dobrej kondycji podczas C staje się nieprawidłowy. Uaktualnienie będzie nadal się powieść, ponieważ tylko 25% usługi jest w złej kondycji. Jednak może to spowodować nieprzewidziane błędy z powodu C jest nieoczekiwanie złej kondycji, zamiast D. W takiej sytuacji D powinny być modelowane jako typu innej usługi, a, B i C. Ponieważ zasady dotyczące kondycji są określone dla typów usług, można zastosować progi różną wartość procentową w złej kondycji z różnymi usługami. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Nie określono zasadę dla uaktualnienie aplikacji, ale nadal nieudanego uaktualnienia dla niektóre błędy przekroczenia limitu czasu, które nigdy nie została określona
Gdy zasady dotyczące kondycji nie są podane na żądanie uaktualnienia są pobierane z *ApplicationManifest.xml* bieżącej wersji aplikacji. Na przykład jeśli aplikacja X wykonujesz uaktualnienie z wersji 1.0 do wersji 2.0, zasady dotyczące kondycji aplikacji określona dla w wersji 1.0 są używane. Jeśli zasady kondycję różnych powinien być używany do uaktualnienia, zasad musi być określona jako część wywołania interfejsu API uaktualniania aplikacji. Zasady, określony jako część wywołania interfejsu API są stosowane tylko wtedy podczas uaktualniania. Po zakończeniu uaktualniania zasady określone w *ApplicationManifest.xml* są używane.

### <a name="incorrect-time-outs-are-specified"></a>Podano niepoprawny przekroczeń limitu czasu
Może mieć zastanawiasz się informacji na temat efektów niespójnie ustawione limity czasu. Na przykład masz *UpgradeTimeout* to mniej niż *UpgradeDomainTimeout*. Odpowiedzią jest zwracany jest błąd. Błędy są zwracane, jeśli *UpgradeDomainTimeout* jest mniejsza niż suma *HealthCheckWaitDuration* i *HealthCheckRetryTimeout*, lub jeśli  *UpgradeDomainTimeout* jest mniejsza niż suma *HealthCheckWaitDuration* i *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Moje uaktualnień trwa zbyt długo
Czas dla uaktualnienie ukończyć zależy od tego, kontroli kondycji i określić limity czasu. Kontrole kondycji oraz przekroczeń limitu czasu są zależą od tego, jak długo trwa kopiowanie, wdrażanie i stabilizacja aplikacji. Trwa zbyt agresywne z przekroczeń limitu czasu może oznaczać bardziej uaktualnienia nie powiodło się, tak więc zaleca się konserwatywnie począwszy od dłuższego limitu czasu.

Poniżej przedstawiono szybki odświeżacz interakcję limitów czasu w czasie uaktualniania:

Uaktualnienia dla domeny uaktualnienia nie można ukończyć szybciej niż *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Niepowodzenia uaktualnienia nie może być szybsza niż *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Podczas uaktualniania do domeny uaktualnienia jest ograniczona przez *UpgradeDomainTimeout*.  Jeśli *HealthCheckRetryTimeout* i *HealthCheckStableDuration* są różna od zera i kondycję aplikacji utrzymuje przełączając się, a następnie Uaktualnianie końcu przekroczy limit czasu na *UpgradeDomainTimeout*. *UpgradeDomainTimeout* odlicza dół uaktualnienia dla rozpoczyna się w bieżącej domenie uaktualnienia.

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnienie z aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu Visual Studio.

[Uaktualnienie z aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu PowerShell.

Kontrolować, jak uaktualnić aplikację przy użyciu [parametry uaktualniania](service-fabric-application-upgrade-parameters.md).

Uzyskania uaktualnień aplikacji zgodnych poznanie sposobu używania [serializacja danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).
