---
title: Azure Service Fabric modelu hostingu | Dokumentacja firmy Microsoft
description: Opisuje relację między repliki (lub wystąpień) wdrożonej usługi Service Fabric i procesu hosta usługi.
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: b2ba019f21256ee98276ef30847c43709b9b3462
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="azure-service-fabric-hosting-model"></a>Azure modelu hostingu sieci szkieletowej usług
Ten artykuł zawiera omówienie modeli dostarczonych przez sieć szkieletowa usług Azure hosting aplikacji i opisano różnice między **udostępnionego procesu** i **wyłącznego procesu** modeli. Opisuje wygląd wdrożonej aplikacji na węzeł sieci szkieletowej usług i relacji między repliki (lub wystąpień), usługi i procesu hosta usługi.

Przed kontynuacją, upewnij się, że różne pojęciami i relacje wyjaśniono w [modelu aplikacji w sieci szkieletowej usług][a1]. 

> [!NOTE]
> W tym artykule chyba że jawnie wymieniony jako inny oznacza:
>
> - *Replika* odwołuje się do obu repliki usługi stanowej i wystąpienie usługi bezstanowej.
> - *Elementu CodePackage* jest traktowane jako równoważne *ServiceHost* procesu, który rejestruje *ServiceType*i replik hostów usług tego *ServiceType*.
>

Aby poznać hostingu modelu, Przejdźmy przykładem. Załóżmy, że mamy *atrybutów ApplicationType* "MyAppType", który ma *ServiceType* "MyServiceType". "MyServiceType" są dostarczane przez *ServicePackage* "MyServicePackage", który ma *elementu CodePackage* "MyCodePackage". Rejestruje "MyCodePackage" *ServiceType* "MyServiceType" po uruchomieniu.

Załóżmy, że mamy istnieją trzy węzły klastra, utworzymy *aplikacji* **fabric: / App1** typu "MyAppType". Wewnątrz tej aplikacji **fabric: / App1**, możemy utworzyć usługę **sieci szkieletowej: / App1/ServiceA** typu "MyServiceType". Ta usługa ma dwie partycje (na przykład **P1** i **P2**) i trzy replik jednej partycji. Na poniższym diagramie przedstawiono widoku tej aplikacji, w jakiej kończy się wdrożonej w węźle.


![Diagram widoku węzła wdrożonej aplikacji][node-view-one]


Sieć szkieletowa usług aktywować "MyServicePackage", który rozpoczął "MyCodePackage", który jest hostem repliki z obu partycji. Wszystkie węzły w klastrze mają tego samego widoku, ponieważ wybrano liczba replik jednej partycji do być taka sama jak liczba węzłów w klastrze. Utwórz inną usługę **sieci szkieletowej: / App1/ServiceB**, w aplikacji **fabric: / App1**. Ta usługa ma jedną partycję (na przykład **P3**) i trzy replik jednej partycji. Na poniższym diagramie przedstawiono nowy widok w węźle:


![Diagram widoku węzła wdrożonej aplikacji][node-view-two]


Sieć szkieletowa usług umieszczone nowej repliki dla partycji **P3** usługi **sieci szkieletowej: / App1/ServiceB** w istniejących aktywacji "MyServicePackage". Teraz. Utwórz inną aplikację **fabric: / App2** typu "MyAppType". Wewnątrz **fabric: / App2**, Utwórz usługę **fabric: / App2/ServiceA**. Ta usługa ma dwie partycje (**P4** i **5**) i trzy replik jednej partycji. Na poniższym diagramie przedstawiono nowy widok węzła:


![Diagram widoku węzła wdrożonej aplikacji][node-view-three]


Usługa Service Fabric aktywuje nową kopię "MyServicePackage", która uruchamia nową kopię "MyCodePackage". Repliki z obu partycji usługi **fabric: / App2/ServiceA** (**P4** i **5**) są umieszczane w tej nowej kopii "MyCodePackage".

## <a name="shared-process-model"></a>Udostępnione modelu procesów
Poprzedniej sekcji opisano domyślny model udostępniane przez usługi Service Fabric, określany jako model procesu udostępnionych hostowania. W tym modelu dla danej aplikacji, tylko jedna kopia danego *ServicePackage* została aktywowana w węźle (co spowoduje włączenie wszystkich *CodePackages* zawarte w niej). Wszystkie repliki wszystkich usług danego *ServiceType* są umieszczane w *elementu CodePackage* który który rejestruje *ServiceType*. Innymi słowy, wszystkich replik wszystkich usług w węźle danego *ServiceType* udostępniać ten sam proces.

## <a name="exclusive-process-model"></a>Wyłączny modelu procesów
Inne model hostingu udostępniane przez usługi sieć szkieletowa jest model procesu wyłącznego. W tym modelu w danym węźle każda replika znajduje się w dedykowanym procesie. Usługa Service Fabric aktywuje nową kopię *ServicePackage* (co spowoduje włączenie wszystkich *CodePackages* zawarte w niej). Repliki są umieszczane w *elementu CodePackage* zarejestrowanego *ServiceType* usługi, do której należy replika. 

Jeśli używasz usługi sieć szkieletowa wersja 5.6 lub później, możesz wybrać model procesu wyłączności w czasie tworzenia usługi (za pomocą [PowerShell][p1], [REST] [ r1], lub [klienta fabricclient z rolą][c1]). Określ **ServicePackageActivationMode** jako "ExclusiveProcess".

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Jeśli masz usługi domyślne w manifeście aplikacji, określając można wybrać model procesu wyłącznego **ServicePackageActivationMode** atrybutu:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Teraz Utwórzmy innej usługi, **fabric: / App1/ServiceC**, w aplikacji **fabric: / App1**. Ta usługa ma dwie partycje (na przykład **P6** i **P7**) i trzy replik jednej partycji. Możesz ustawić **ServicePackageActivationMode** do "ExclusiveProcess". Na poniższym diagramie przedstawiono nowy widok w węźle:


![Diagram widoku węzła wdrożonej aplikacji][node-view-four]


Jak widać, Service Fabric aktywowany dwóch nowych kopii "MyServicePackage" (po jednej dla każdej repliki z partycji **P6** i **P7**). Sieć szkieletowa usług umieszczone każdej repliki w jego dedykowanych kopii *elementu CodePackage*. Korzystając z modelem procesu wyłącznego dla danej aplikacji, wielu kopii danej *ServicePackage* może być aktywny w węźle. W powyższym przykładzie trzy kopie "MyServicePackage" są aktywne dla **fabric: / App1**. Każdy z tych active kopii "MyServicePackage" ma **ServicePackageActivationId** skojarzonych z nim. Ten identyfikator identyfikuje ją w aplikacji **fabric: / App1**.

Korzystając z modelu procesu udostępnione dla aplikacji, jest tylko jedna kopia active *ServicePackage* w węźle. **ServicePackageActivationId** w przypadku tego aktywacji *ServicePackage* jest pustym ciągiem. Jest to, na przykład z **fabric: / App2**.

> [!NOTE]
>- Udostępnione procesu hostingu modelu odpowiada **ServicePackageActivationMode** jest równe **SharedProcess**. Jest to domyślny model hostowania, i **ServicePackageActivationMode** nie muszą być określane podczas tworzenia usługi.
>
>- Wyłączny procesu hostingu modelu odpowiada **ServicePackageActivationMode** jest równe **ExclusiveProcess**. Aby użyć tego ustawienia, należy określić go jawnie w czasie tworzenia usługi. 
>
>- Aby wyświetlić hostingu modelu usługi, zapytania [opis usługi][p2]i przyjrzyj się wartość **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Praca z pakietem wdrożonej usługi
Aktywne kopię *ServicePackage* w węźle jest określany jako [wdrożony pakiet usługi][p3]. Podczas korzystania z modelu procesu wyłączności do tworzenia usług dla danej aplikacji, może być wiele pakietów wdrożonej usługi dla tego samego *ServicePackage*. Jeśli są wykonywane operacje specyficzne dla pakietu wdrożonej usługi, należy podać **ServicePackageActivationId** do identyfikowania pakietu określonych wdrożonej usługi. Na przykład można podać identyfikator, jeśli jesteś [raportowania kondycji pakietu wdrożonej usługi] [ p4] lub [ponownego uruchamiania pakietu kodu pakietu wdrożonej usługi] [p5].

Można sprawdzić **ServicePackageActivationId** pakietu wdrożonej usługi, badając listę [wdrożonych pakietów usługi] [ p3] w węźle. Gdy są wykonywania zapytania dotyczącego [wdrożone typów usług][p6], [wdrożone replik][p7], i [wdrożone pakiety kodu ] [ p8] w węźle, zawiera także wynik zapytania **ServicePackageActivationId** pakiet z wdrożonej przez element nadrzędny.

> [!NOTE]
>- W modelu udostępnionego procesu hostingu w danym węźle dla danej aplikacji, tylko jedną kopię *ServicePackage* jest aktywny. Ma ona **ServicePackageActivationId** równa *pusty ciąg*i nie muszą być określane podczas wykonywania operacji związanych z pakietem wdrożonej usługi. 
>
> - W obszarze wyłącznego procesu hostingu modelu w danym węźle dla danej aplikacji, jednego lub większej liczby kopii *ServicePackage* mogą być aktywne. Każda aktywacja ma *niepustym* **ServicePackageActivationId**określone podczas wykonywania operacji związanych z pakietem wdrożonej usługi. 
>
> - Jeśli **ServicePackageActivationId** jest pominięty, domyślnie *pusty ciąg*. Jeśli pakiet wdrożonej usługi, który został aktywowany zgodnie z modelem procesu udostępnione, będzie można wykonać operacji na nim. W przeciwnym razie operacja zakończy się niepowodzeniem.
>
> - Nie wykonać kwerendy raz i pamięci podręcznej **ServicePackageActivationId**. Identyfikator jest generowane dynamicznie i zmieniać z różnych przyczyn. Przed wykonaniem operacji, które należy **ServicePackageActivationId**, należy najpierw sprawdzić listę [wdrożonych pakietów usługi] [ p3] w węźle. Następnie użyj **ServicePackageActivationId** od wyniku kwerendy, aby wykonać operację.
>
>

## <a name="guest-executable-and-container-applications"></a>Gość plik wykonywalny i kontenera aplikacji
Sieć szkieletowa usług traktuje [pliku wykonywalnego gościa] [ a2] i [kontenera] [ a3] aplikacji jako usługi bezstanowej, które są niezależne. Brak nie środowiska uruchomieniowego platformy Service Fabric w *ServiceHost* (proces lub kontenera). Ponieważ te usługi są niezależne, liczba replik na *ServiceHost* nie ma zastosowania do tych usług. Konfiguracja najczęściej używane z tymi usługami jest jednej partycji, z [InstanceCount] [ c2] równy -1 (jedną kopię kodu usługi uruchomione w każdym węźle klastra). 

Wartość domyślna **ServicePackageActivationMode** dla tych usług jest **SharedProcess**, w którym to przypadku usługi Service Fabric aktywuje tylko jedną kopię *ServicePackage* w węźle dla danej aplikacji.  Oznacza to, że tylko jedna kopia kodu usługi zostaną uruchomione węzła. Jeśli wiele kopii kodu usługi do uruchamiania w węźle, należy określić **ServicePackageActivationMode** jako **ExclusiveProcess** w czasie tworzenia usługi. Na przykład można to zrobić po utworzeniu wielu usług (*Service1* do *ServiceN*) z *ServiceType* (określony w *ServiceManifest*), lub gdy usługa jest podzielona na partycje wielu. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Zmień model hostingu istniejącej usługi
Obecnie nie można zmienić model hostingu istniejącą usługę z udostępnionego procesu wyłącznego procesu (lub odwrotnie).

## <a name="choose-between-the-hosting-models"></a>Wybierz między modelami hostingu
Należy ocenić hostingu model, który najlepiej spełnia wymagania. Model procesu udostępnionych używa zasobów systemu operacyjnego lepsze, ponieważ procesy mniej jest zduplikowany, a wiele replik w tym samym procesie można udostępniać portów. Jednak jeśli jednej z replik zawiera błąd, w którym należy go zamknąć hosta usługi, dotyczy wszystkich replik w tym samym procesie.

 Model procesu wyłącznego zapewnia izolację lepsze, z każdej repliki w swoim własnym procesie. Jeśli jeden z repliki zawiera błąd, nie ma ona wpływu innych replik. Ten model jest przydatna w sytuacjach, w których udostępnianie portów nie jest obsługiwana przez protokół komunikacji. Ułatwia on możliwość stosowania ładu zasobów na poziomie repliki. Jednak proces wyłącznego wykorzystuje więcej zasobów systemu operacyjnego, zgodnie z jego spowoduje utworzenie jednego procesu dla każdej repliki na węźle.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Wyłączny modelu procesu i aplikacji zagadnienia dotyczące modelu
W przypadku większości aplikacji można modelu aplikacji w sieci szkieletowej usług przy zachowaniu jedną *ServiceType* na *ServicePackage*. 

W niektórych przypadkach usługi sieć szkieletowa umożliwia również więcej niż jeden *ServiceType* na *ServicePackage* (jeden *elementu CodePackage* można zarejestrować więcej niż jeden  *Typ ServiceType*). Poniżej przedstawiono niektóre scenariusze, w których te konfiguracje mogą być przydatne:

- Chcesz optymalizować wykorzystanie zasobów przez dochodzi do uruchamiania procesów mniej i po zwiększeniu repliki na proces.
- Repliki z różnych *Serivcetype* wymagają udostępniania niektórych typowych danych o wysokiej inicjowania lub pamięci kosztów.
- Masz darmowa oferty, i chcesz zawiesić limit wykorzystania zasobów przez umieszczenie wszystkich replik usługi, w tym samym procesie.

Wyłączny proces obsługujący modelu nie jest spójne z istnieniem wielu modelu aplikacji *Serivcetype* na *ServicePackage*. Jest to spowodowane wielu *Serivcetype* na *ServicePackage* zaprojektowano w celu osiągnięcia wyższej zasobów, udostępnianie między replikami i umożliwia zwiększeniu repliki na proces. Model procesu wyłącznego zaprojektowano w celu osiągnięcia różnych wyników.

Należy wziąć pod uwagę w przypadku wielu *Serivcetype* na *ServicePackage*, z innym *elementu CodePackage* zarejestrowanie każdego *ServiceType*. Załóżmy, że mamy *ServicePackage* "MultiTypeServicePackge", która ma dwa *CodePackages*:

- "MyCodePackageA", która rejestruje *ServiceType* "MyServiceTypeA".
- "MyCodePackageB", która rejestruje *ServiceType* "MyServiceTypeB".

Teraz załóżmy, że utworzymy aplikację, **fabric: / SpecialApp**. Wewnątrz **fabric: / SpecialApp**, utworzymy następujące dwie usługi z modelem procesu wyłącznego:

- Usługi **fabric: / SpecialApp/ServiceA** typu "MyServiceTypeA" z dwóch partycji (na przykład **P1** i **P2**) i trzy replik jednej partycji.
- Usługi **fabric: / SpecialApp/ServiceB** typu "MyServiceTypeB" z dwóch partycji (**P3** i **P4**) i trzy replik jednej partycji.

W danym węźle usług mają dwie repliki. Ponieważ użyliśmy model procesu wyłączności do tworzenia usług platformy Service Fabric aktywuje nową kopię "MyServicePackage" dla każdej repliki. Każda aktywacja "MultiTypeServicePackge" uruchamia kopię "MyCodePackageA" i "MyCodePackageB". Jednak tylko jeden "MyCodePackageA" lub "MyCodePackageB" hosta repliki dla którego "MultiTypeServicePackge" został aktywowany. Na poniższym diagramie przedstawiono widok węzła:


![Diagram widoku węzła wdrożonej aplikacji][node-view-five]


Podczas aktywacji "MultiTypeServicePackge" dla replik partycji **P1** usługi **fabric: / SpecialApp/ServiceA**, "MyCodePackageA" jest hostem repliki. "MyCodePackageB" jest uruchomiona. Podobnie w przypadku aktywacji "MultiTypeServicePackge" dla replik partycji **P3** usługi **fabric: / SpecialApp/ServiceB**, "MyCodePackageB" jest hostem repliki. "MyCodePackageA" jest uruchomiona. W związku z tym im większa liczba *CodePackages* (rejestrowanie różnych *Serivcetype*) na *ServicePackage*, większego użycia zasobów nadmiarowe. 
 
 Jednak jeśli utworzymy usługami **fabric: / SpecialApp/ServiceA** i **sieci szkieletowej: / SpecialApp/ServiceB** z modelem procesu udostępnione usługi Service Fabric aktywuje tylko jedną kopię " MultiTypeServicePackge "dla aplikacji **fabric: / SpecialApp**. "MyCodePackageA" obsługuje wszystkie repliki usługi **fabric: / SpecialApp/ServiceA**. "MyCodePackageB" obsługuje wszystkie repliki usługi **fabric: / SpecialApp/ServiceB**. Na poniższym diagramie przedstawiono widok węzła w tym ustawieniu: 


![Diagram widoku węzła wdrożonej aplikacji][node-view-six]


W poprzednim przykładzie, może uważasz, że jeśli "MyCodePackageA" rejestruje zarówno "MyServiceTypeA" i "MyServiceTypeB" i "MyCodePackageB" nie istnieje, a następnie jest nadmiarowy nie *elementu CodePackage* uruchomiona. Mimo że jest poprawny, model tej aplikacji nie jest wyrównana z wyłącznych proces obsługujący modelu. Jeśli celem jest umieszczenie każdej repliki w dedykowanym procesie, nie trzeba zarejestrować obie *Serivcetype* z samej *elementu CodePackage*. Zamiast tego możesz po prostu zaznaczyć każdego *ServiceType* we własnym *ServicePackage*.

## <a name="next-steps"></a>Kolejne kroki
[Pakiet aplikacji] [ a4] i przygotowania do wdrożenia.

[Wdrażanie i usunąć aplikacje][a5]. W tym artykule opisano sposób zarządzania wystąpień aplikacji przy użyciu programu PowerShell.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/servicefabric/vlatest/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/servicefabric/vlatest/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedcodepackage
