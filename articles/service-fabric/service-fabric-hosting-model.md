---
title: Usługa Azure Service Fabric modelu hostingu | Dokumentacja firmy Microsoft
description: W tym artykule opisano relację między repliki (lub wystąpieniami) wdrożonej usługi Service Fabric i procesu hosta usługi.
services: service-fabric
documentationcenter: .net
author: harahma
manager: chackdan
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: d2d958a89bff40483e1cd473538f7d1a6971d266
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60483633"
---
# <a name="azure-service-fabric-hosting-model"></a>Usługa Azure Service Fabric modelu hostingu
Ten artykuł zawiera omówienie modele udostępniane przez usługi Azure Service Fabric do hostowania aplikacji i w tym artykule opisano różnice między **udostępnionego procesu** i **wyłączne procesu** modeli. Opisuje, jak wygląda wdrożonej aplikacji na węzła usługi Service Fabric i relacji między repliki (lub wystąpieniami), usługi i procesu hosta usługi.

Przed kontynuacją upewnij się, aby zapoznać się z pojęciami różnych i szczegółowo opisane relacje [modelu aplikacji w usłudze Service Fabric][a1]. 

> [!NOTE]
> W tym artykule chyba że wyraźnie wymienione jako oznacza coś innego:
>
> - *Replika* odwołuje się do zarówno repliki, usługi stanowe i wystąpienie usługi bezstanowej.
> - *CodePackage* jest traktowane jako równoważne *ServiceHost* procesu, który rejestruje *ServiceType*i hosty repliki usług tego *ServiceType*.
>

Aby poznać modelu hostingu, Przejdźmy przykładem. Załóżmy, że mamy *ApplicationType* "MyAppType", która ma *ServiceType* "MyServiceType". "MyServiceType" są dostarczane przez *pakiet ServicePackage* "MyServicePackage", która ma *CodePackage* "MyCodePackage". Rejestruje "MyCodePackage" *ServiceType* "MyServiceType", po jego uruchomieniu.

Załóżmy, że mamy klaster z trzema węzłami, możemy utworzyć *aplikacji* **Service fabric: / App1** typu "MyAppType". W tej aplikacji **Service fabric: / App1**, możemy utworzyć usługę **Service fabric: / App1/ServiceA** typu "MyServiceType". Ta usługa ma dwie partycje (na przykład **P1** i **P2**), a trzy repliki na partycję. Na poniższym diagramie przedstawiono widok tej aplikacji, ponieważ kończy się wdrożonej w węźle.


![Diagram przedstawiający widok węzła wdrożonej aplikacji][node-view-one]


Usługa Service Fabric aktywowana "MyServicePackage", która została "MyCodePackage", który jest hostem repliki z obu partycji. Wszystkie węzły w klastrze mają tego samego widoku, ponieważ została wybrana liczba replik na partycję, aby być taka sama jak liczba węzłów w klastrze. Utwórzmy innej usługi, **Service fabric: / App1/ServiceB**, w aplikacji **Service fabric: / App1**. Ta usługa ma jedną partycję (na przykład **P3**), a trzy repliki na partycję. Na poniższym diagramie przedstawiono nowy widok w węźle:


![Diagram przedstawiający widok węzła wdrożonej aplikacji][node-view-two]


Usługa Service Fabric umieszczone nowej repliki dla partycji **P3** usługi **Service fabric: / App1/ServiceB** w istniejących aktywacji "MyServicePackage". Teraz. Utwórz inną aplikację **Service fabric: / App2** typu "MyAppType". Wewnątrz **Service fabric: / App2**, utworzyć usługę **Service fabric: / App2/ServiceA**. Ta usługa ma dwie partycje (**P4** i **P5**), a trzy repliki na partycję. Na poniższym diagramie przedstawiono nowy widok węzła:


![Diagram przedstawiający widok węzła wdrożonej aplikacji][node-view-three]


Usługa Service Fabric aktywuje nową kopię "MyServicePackage", który uruchamia nową kopię "MyCodePackage". Repliki z obu partycji usługi **Service fabric: / App2/ServiceA** (**P4** i **P5**) są umieszczane w tej nowej kopii "MyCodePackage".

## <a name="shared-process-model"></a>Udostępnione model procesów
W poprzedniej sekcji opisano domyślny model dostarczane przez usługę Service Fabric, określane jako model udostępnionego procesu hostingu. W tym modelu dla danej aplikacji, tylko jedna kopia danego *pakiet ServicePackage* została aktywowana w węźle (co spowoduje włączenie wszystkich *CodePackages* zawarte w nim). Wszystkie repliki wszystkich usług z danego *ServiceType* są umieszczane w *CodePackage* , który rejestruje *ServiceType*. Innymi słowy, wszystkich replik wszystkich usług w węźle danego *ServiceType* udostępnianie tego samego procesu.

## <a name="exclusive-process-model"></a>Wyłączne model procesów
Inne modelu hostingu udostępnianego przez usługę Service Fabric jest model procesu wyłączności. W tym modelu w danym węźle każda replika znajduje się w swoim własnym procesie dedykowanych. Usługa Service Fabric aktywuje nową kopię *pakiet ServicePackage* (co spowoduje włączenie wszystkich *CodePackages* zawarte w nim). Repliki są umieszczane w *CodePackage* zarejestrowanego *ServiceType* usługi, do której należy replika. 

Jeśli używasz usługi Service Fabric w wersji 5.6 lub później, można wybrać model procesu wyłączności w czasie tworzenia usługi (za pomocą [PowerShell][p1], [REST] [ r1], lub [FabricClient][c1]). Określ **ServicePackageActivationMode** jako "ExclusiveProcess".

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

Jeśli masz usługi domyślnej w manifeście aplikacji, można wybrać model procesu wyłączny, określając **ServicePackageActivationMode** atrybutu:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Teraz Utwórzmy innej usługi, **Service fabric: / App1/ServiceC**, w aplikacji **Service fabric: / App1**. Ta usługa ma dwie partycje (na przykład **P6** i **P7**) i trzech replik dla każdej partycji. Możesz ustawić **ServicePackageActivationMode** do "ExclusiveProcess". Na poniższym diagramie przedstawiono nowy widok w węźle:


![Diagram przedstawiający widok węzła wdrożonej aplikacji][node-view-four]


Jak widać, Usługa Service Fabric aktywowane dwóch nowych kopii "MyServicePackage" (jeden dla każdej repliki z partycji **P6** i **P7**). Usługi Service Fabric umieszczone każdej repliki w jego dedykowanych kopię *CodePackage*. Korzystając z modelem procesu wyłączny dla danej aplikacji, wiele kopii danej *pakiet ServicePackage* może być aktywny w węźle. W powyższym przykładzie trzy kopie "MyServicePackage" są aktywne dla **Service fabric: / App1**. Każda z tych kopii aktywnej "MyServicePackage" ma **ServicePackageActivationId** skojarzonych z nim. Ten identyfikator identyfikuje kopii aplikacji **Service fabric: / App1**.

Korzystając z modelem procesu udostępnione dla aplikacji, jest tylko jedna kopia active *pakiet ServicePackage* w węźle. **ServicePackageActivationId** to aktywacji *pakiet ServicePackage* jest ciągiem pustym. Jest to przypadek, na przykład za pomocą **Service fabric: / App2**.

> [!NOTE]
>- Proces udostępnione odnosi się do modelu hostowania **ServicePackageActivationMode** jest równa **SharedProcess**. Jest to domyślny model, hostingu i **ServicePackageActivationMode** nie muszą być określane podczas tworzenia usługi.
>
>- Proces wyłączne odnosi się do modelu hostowania **ServicePackageActivationMode** jest równa **ExclusiveProcess**. Aby użyć tego ustawienia, należy określić ją jawnie w czasie tworzenia usługi. 
>
>- Aby wyświetlić modelu hostingu usługi, należy zbadać [opis usługi][p2]i przyjrzyj się wartość **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Praca z pakietem wdrożonej usługi
Aktywna kopia *pakiet ServicePackage* w węźle jest określany jako [wdrożony pakiet usługi][p3]. Korzystając z modelu procesu wyłączności do tworzenia usług dla danej aplikacji, może być wiele pakietów wdrożonej usługi dla tego samego *pakiet ServicePackage*. W przypadku przeprowadzania operacji specyficzne dla pakietu wdrożonej usługi, należy podać **ServicePackageActivationId** do identyfikowania pakiet określonych wdrożonej usługi. Na przykład można podać identyfikator przypadku [raportowania kondycji wdrożony pakiet usługi] [ p4] lub [ponowne uruchomienie pakietu kodu wdrożony pakiet usługi] [p5].

Możesz dowiedzieć się **ServicePackageActivationId** pakietu wdrożonej usługi, badając listy [wdrożonych pakietów usługi] [ p3] w węźle. Gdy jest wykonywane zapytanie dla [wdrożone typów usług][p6], [wdrożone replik][p7], i [wdrożone pakiety kodu ] [ p8] w węźle zawiera także wynik kwerendy **ServicePackageActivationId** pakiet z wdrożonej przez element nadrzędny.

> [!NOTE]
>- W ramach modelu udostępnionego procesu hostingu w danym węźle dla danej aplikacji, tylko jedna kopia *pakiet ServicePackage* została aktywowana. Ma ona **ServicePackageActivationId** równa *pusty ciąg*i nie muszą być określane podczas wykonywania operacji związanych z pakietu wdrożonej usługi. 
>
> - W obszarze wyłączne procesu hostingu modelu w danym węźle dla danej aplikacji, jednego lub większej liczby kopii *pakiet ServicePackage* może być aktywne. Każda aktywacja ma *niepuste* **ServicePackageActivationId**określone podczas wykonywania operacji związanych z pakietu wdrożonej usługi. 
>
> - Jeśli **ServicePackageActivationId** jest pominięty, jego wartość domyślna to *pusty ciąg*. Jeśli pakiet wdrożonej usługi, który został aktywowany w ramach modelu udostępnionego procesu jest obecny, będzie można wykonać operacji na nim. W przeciwnym razie operacja zakończy się niepowodzeniem.
>
> - Nie wykonać kwerendy raz i pamięci podręcznej **ServicePackageActivationId**. Identyfikator jest generowana dynamicznie i mogą być zmieniane dla różnych powodów. Przed przystąpieniem do wykonywania operacji, która wymaga **ServicePackageActivationId**, należy najpierw kwerendy listy [wdrożonych pakietów usługi] [ p3] w węźle. Następnie użyj **ServicePackageActivationId** od wyniku kwerendy do wykonywania operacji pierwotnej.
>
>

## <a name="guest-executable-and-container-applications"></a>Gość plik wykonywalny i aplikacji kontenera
Usługa Service Fabric traktuje [pliku wykonywalnego gościa] [ a2] i [kontenera] [ a3] aplikacji jako bezstanowej usługi, które są niezależne. Istnieje nie środowisko uruchomieniowe usługi Service Fabric w *ServiceHost* (procesu lub kontenera). Ponieważ te usługi są niezależne, liczba replik na *ServiceHost* nie ma zastosowania do tych usług. Najbardziej typowe konfiguracji, które są używane z tych usług jest jedną partycją, za pomocą [InstanceCount] [ c2] równy-1 (jedną kopię kodu usługi uruchomione w każdym węźle klastra). 

Wartość domyślna **ServicePackageActivationMode** dla tych usług jest **SharedProcess**, w którym to przypadku usługi Service Fabric aktywuje tylko jedną kopię *pakiet ServicePackage* w węźle dla danej aplikacji.  Oznacza to, że tylko jedna kopia kod obsługi zostanie uruchomiony węzeł. Jeśli chcesz, aby wiele kopii kodu usługi do uruchamiania w węźle, należy określić **ServicePackageActivationMode** jako **ExclusiveProcess** w czasie tworzenia usługi. Na przykład, można to zrobić podczas tworzenia wielu usług (*Service1* do *ServiceN*) z *ServiceType* (określony w *ServiceManifest*), lub gdy usługa jest podzielona na partycje wielu. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Zmiana modelu hostingu istniejącej usługi
W chwili obecnej nie można zmienić modelu hostingu istniejącej usługi z udostępnionego procesu do wyłącznego procesu (lub odwrotnie).

## <a name="choose-between-the-hosting-models"></a>Wybieranie między modelach hostingu
Należy ocenić hostingu model, który najlepiej spełnia Twoje wymagania. Modelu udostępnionego procesu używa zasobów systemu operacyjnego lepiej, ponieważ mniejszej liczby procesów jest zduplikowany, a wiele replik w tym samym procesie można udostępniać portów. Jednak jeśli jednej z replik ma błąd, w którym należy obniżyć hosta usługi, wpłynie ona na wszystkie repliki w tym samym procesie.

 Model procesów wyłączne zapewnia lepsze izolację, ze wszystkimi replikami we własnym procesie. Jeśli jednej z replik ma błąd, nie ma wpływu na pozostałe repliki. Ten model jest przydatne w sytuacjach, w którym współużytkowania portów nie jest obsługiwana przez protokół komunikacyjny. Ułatwia ono możliwość stosowania nadzór nad zasobami na poziomie repliki. Jednak wyłączne proces zużywa więcej zasobów systemu operacyjnego, zgodnie z jej spowoduje utworzenie jednego procesu dla każdej repliki w węźle.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Wyłączne modelu procesu i aplikacji modelu zagadnienia
W przypadku większości aplikacji może modelować aplikację w usłudze Service Fabric, zapewniając jeden *ServiceType* na *pakiet ServicePackage*. 

W niektórych przypadkach usługa Service Fabric umożliwia również więcej niż jeden *ServiceType* na *pakiet ServicePackage* (jeden *CodePackage* może zarejestrować więcej niż jeden  *Typ ServiceType*). Poniżej przedstawiono niektóre scenariusze, w którym te konfiguracje mogą być przydatne:

- Chcesz optymalizować wykorzystanie zasobów podczas duplikowania mniejszą liczbę procesów, a także o zwiększenie gęstości repliki na proces.
- Repliki za pośrednictwem różnych *ServiceTypes* potrzebne do udostępniania niektórych wspólnych danych o wysokiej inicjowania lub pamięci kosztów.
- Masz bezpłatnej oferty usługi i chcesz umieścić limit na wykorzystanie zasobów przez umieszczenie wszystkich replik usługi w tym samym procesie.

Wyłączne proces model hostingu nie jest spójny z modelem aplikacji, posiadanie wielu *ServiceTypes* na *pakiet ServicePackage*. Jest to spowodowane wieloma *ServiceTypes* na *pakiet ServicePackage* zaprojektowano w celu osiągnięcia wyższej zasobów współużytkowanie danych przez repliki i umożliwia zwiększenie gęstości repliki na proces. Model procesów wyłączne zaprojektowano w celu osiągnięcia różne wyniki.

Należy wziąć pod uwagę w przypadku wielu *ServiceTypes* na *pakiet ServicePackage*, z innym *CodePackage* zarejestrowanie każdego *ServiceType*. Załóżmy, że mamy *pakiet ServicePackage* "MultiTypeServicePackage", który ma dwa *CodePackages*:

- "MyCodePackageA", która rejestruje *ServiceType* "MyServiceTypeA".
- "MyCodePackageB", która rejestruje *ServiceType* "MyServiceTypeB".

Teraz załóżmy, że możemy utworzyć aplikację, **Service fabric: / SpecialApp**. Wewnątrz **Service fabric: / SpecialApp**, możemy utworzyć następujące dwie usługi z modelem procesu wyłączne:

- Usługa **Service fabric: / SpecialApp/ServiceA** typu "MyServiceTypeA" za pomocą dwóch partycji (na przykład **P1** i **P2**) i trzech replik dla każdej partycji.
- Usługa **Service fabric: / SpecialApp/ServiceB** typu "MyServiceTypeB" za pomocą dwóch partycji (**P3** i **P4**) i trzech replik dla każdej partycji.

W danym węźle usług mają dwie repliki. Ponieważ użyliśmy model procesu wyłączne trzeba utworzyć usługi Service Fabric aktywuje nową kopię "MyServicePackage" dla każdej repliki. Każda aktywacja "MultiTypeServicePackage" rozpoczyna się kopię "MyCodePackageA" i "MyCodePackageB". Jednak tylko jeden z "MyCodePackageA" lub "MyCodePackageB" obsługuje replikę, dla którego została aktywowana "MultiTypeServicePackage". Na poniższym diagramie przedstawiono widok węzła:


![Diagram przedstawiający widok węzła wdrożonej aplikacji][node-view-five]


W oknie Aktywacja systemu "MultiTypeServicePackage" dla repliki partycji **P1** usługi **Service fabric: / SpecialApp/ServiceA**, "MyCodePackageA" jest hostem repliki. "MyCodePackageB" jest uruchomiona. Podobnie, w przypadku aktywacji "MultiTypeServicePackage" dla repliki partycji **P3** usługi **Service fabric: / SpecialApp/ServiceB**, "MyCodePackageB" jest hostem repliki. "MyCodePackageA" jest uruchomiona. W związku z tym większa liczba *CodePackages* (rejestrowanie różnych *ServiceTypes*) na *pakiet ServicePackage*, tym większe obciążenie zasobów nadmiarowe. 
 
 Jednak jeśli utworzymy usług **Service fabric: / SpecialApp/ServiceA** i **Service fabric: / SpecialApp/ServiceB** z modelem procesu udostępnionych usługi Service Fabric aktywuje tylko jedna kopia " MultiTypeServicePackage "dla aplikacji **Service fabric: / SpecialApp**. "MyCodePackageA" obsługuje wszystkie repliki usługi **Service fabric: / SpecialApp/ServiceA**. "MyCodePackageB" obsługuje wszystkie repliki usługi **Service fabric: / SpecialApp/ServiceB**. Na poniższym diagramie przedstawiono widok węzła, w tym ustawieniu: 


![Diagram przedstawiający widok węzła wdrożonej aplikacji][node-view-six]


W poprzednim przykładzie, może się wydawać, że jeśli rejestruje "MyCodePackageA", "MyServiceTypeA" i "MyServiceTypeB" i nie ma żadnych MyCodePackageB, a następnie jest nie nadmiarowe *CodePackage* uruchomione. Mimo że jest poprawny, ten model aplikacji nie jest zgodny z procesu wyłączne model hostingu. Jeśli celem jest umieszczenie każdej repliki w swoim własnym procesie dedykowanym, nie trzeba zarejestrować zarówno *ServiceTypes* z samej *CodePackage*. Zamiast tego możesz po prostu umieść każdy *ServiceType* we własnym *pakiet ServicePackage*.

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie pakietu aplikacji] [ a4] i przygotowanie do wdrożenia.

[Wdrażanie i usuwać aplikacje][a5]. W tym artykule opisano sposób używania programu PowerShell do zarządzania wystąpieniami aplikacji.

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

[p1]: https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
