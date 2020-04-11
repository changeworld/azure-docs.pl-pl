---
title: Model hostingu sieci szkieletowej usługi Azure
description: Opisuje relację między replikami (lub wystąpieniami) wdrożonej usługi sieci szkieletowej usług a procesem hosta usługi.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: 82bc5068be651b05eb24efa3b05e46c1e7c1e24d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115040"
---
# <a name="azure-service-fabric-hosting-model"></a>Model hostingu sieci szkieletowej usługi Azure
W tym artykule przedstawiono omówienie modeli hostingu aplikacji dostarczonych przez usługę Azure Service Fabric i opisano różnice między modelami **procesu udostępnionego** i **procesu wyłącznego.** Opisano w nim, jak wdrożona aplikacja wygląda w węźle sieci szkieletowej usług oraz relację między replikami (lub wystąpieniami) usługi a procesem usługi-hosta.

Przed kontynuowaniem, upewnij się, że rozumiesz różne pojęcia i relacje wyjaśnione w [Model aplikacji w sieci szkieletowej usług][a1]. 

> [!NOTE]
> W tym artykule, chyba że wyraźnie wymienione jako znaczenie coś innego:
>
> - *Replika* odnosi się zarówno do repliki usługi stanowej, jak i do wystąpienia usługi bezstanowej.
> - *CodePackage* jest traktowany jako odpowiednik procesu *ServiceHost,* który rejestruje *ServiceType*i obsługuje repliki usług tego *ServiceType*.
>

Aby zrozumieć model hostingu, przejdźmy przez przykład. Załóżmy, że mamy *ApplicationType* "MyAppType", który ma *ServiceType* "MyServiceType". "MyServiceType" jest dostarczany przez *ServicePackage* "MyServicePackage", który ma *CodePackage* "MyCodePackage". 'MyCodePackage' rejestruje *ServiceType* "MyServiceType" po uruchomieniu.

Załóżmy, że mamy klaster z trzema węzłami i tworzymy **sieć szkieletową aplikacji:/App1** typu "MyAppType". *application* Wewnątrz tej **sieci szkieletowej aplikacji:/App1**tworzymy **sieć szkieletową usługi:/App1/ServiceA** typu "MyServiceType". Ta usługa ma dwie partycje (na przykład **P1** i **P2**) i trzy repliki na partycję. Na poniższym diagramie przedstawiono widok tej aplikacji, ponieważ kończy się ona wdrożona w węźle.


![Diagram widoku węzła wdrożonej aplikacji][node-view-one]


Usługa Fabric aktywowana "MyServicePackage", który rozpoczął "MyCodePackage", który jest hosting replik z obu partycji. Wszystkie węzły w klastrze mają ten sam widok, ponieważ wybraliśmy liczbę replik na partycję, aby była równa liczbie węzłów w klastrze. Utwórzmy inną usługę, **fabric:/App1/ServiceB**, w **sieci szkieletowej aplikacji:/App1**. Ta usługa ma jedną partycję (na przykład **P3**) i trzy repliki na partycję. Na poniższym diagramie przedstawiono nowy widok w węźle:


![Diagram widoku węzła wdrożonej aplikacji][node-view-two]


Usługa Fabric umieszczone nowej repliki dla partycji **P3** **sieci szkieletowej usług:/App1/ServiceB** w istniejącej aktywacji "MyServicePackage". Nwo. utwórzmy inną **sieć szkieletową aplikacji:/App2** typu "MyAppType". Wewnątrz **sieci szkieletowej:/App2**, utwórz **sieć szkieletową usług:/App2/ServiceA**. Ta usługa ma dwie partycje (**P4** i **P5**) i trzy repliki na partycję. Na poniższym diagramie przedstawiono nowy widok węzła:


![Diagram widoku węzła wdrożonej aplikacji][node-view-three]


Usługa Service Fabric aktywuje nową kopię "MyServicePackage", która uruchamia nową kopię "MyCodePackage". Repliki z obu partycji **sieci szkieletowej usług:/App2/ServiceA** (**P4** i **P5**) są umieszczane w tej nowej kopii "MyCodePackage".

## <a name="shared-process-model"></a>Model procesu udostępnionego
W poprzedniej sekcji opisano domyślny model hostingu dostarczony przez sieci szkieletowej usług, dalej modelu procesu udostępnionego. W tym modelu dla danej aplikacji tylko jedna kopia danego *ServicePackage* jest aktywowana w węźle (który uruchamia wszystkie *CodePackages* zawarte w nim). Wszystkie repliki wszystkich usług danego *typu usługi* są umieszczane w pakiecie *kodu,* który rejestruje ten *servicetype*. Innymi słowy wszystkie repliki wszystkich usług w węźle danego *ServiceType* współużytkują ten sam proces.

## <a name="exclusive-process-model"></a>Ekskluzywny model procesu
Inny model hostingu dostarczony przez usługę Fabric jest model procesu wyłącznego. W tym modelu, w danym węźle, każda replika żyje w swoim własnym dedykowanym procesie. Usługa Sieci szkieletowej aktywuje nową kopię *ServicePackage* (który uruchamia wszystkie *CodePackages* zawarte w nim). Repliki są umieszczane w *CodePackage,* który zarejestrował *ServiceType* usługi, do której należy replika. 

Jeśli używasz sieci szkieletowej usług w wersji 5.6 lub nowszej, możesz wybrać model procesu wyłącznego w momencie tworzenia usługi (przy użyciu [programu PowerShell,][p1] [REST][r1]lub [FabricClient).][c1] Określ **ServicePackageActivationMode** jako "ExclusiveProcess".

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

Jeśli masz domyślną usługę w manifeście aplikacji, możesz wybrać model procesu wyłącznego, określając atrybut **ServicePackageActivationMode:**

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Teraz utwórzmy inną usługę, **fabric:/App1/ServiceC**, w **sieci szkieletowej aplikacji:/App1**. Ta usługa ma dwie partycje (na przykład **P6** i **P7**) i trzy repliki na partycję. Możesz ustawić **ServicePackageActivationMode** na "ExclusiveProcess". Na poniższym diagramie przedstawiono nowy widok w węźle:


![Diagram widoku węzła wdrożonej aplikacji][node-view-four]


Jak widać, Service Fabric aktywował dwie nowe kopie "MyServicePackage" (po jednej dla każdej repliki z partycji **P6** i **P7**). Sieć szkieletowa usług umieściła każdą replikę w dedykowanej kopii *CodePackage*. Korzystając z modelu procesu wyłącznego dla danej aplikacji, wiele kopii danego *ServicePackage* może być aktywny w węźle. W poprzednim przykładzie trzy kopie "MyServicePackage" są aktywne dla **fabric:/App1**. Każda z tych aktywnych kopii "MyServicePackage" ma **ServicePackageActivationId** skojarzone z nim. Ten identyfikator identyfikuje tę kopię w **sieci szkieletowej aplikacji:/App1**.

Jeśli używasz tylko modelu procesu udostępnionego dla aplikacji, istnieje tylko jedna aktywna kopia *ServicePackage* w węźle. **ServicePackageActivationId** dla tej aktywacji *ServicePackage* jest pusty ciąg. Dotyczy to na przykład **sieci szkieletowej:/App2.**

> [!NOTE]
>- Model hostingu procesu udostępnionego odpowiada **ServicePackageActivationMode** równa **się SharedProcess**. Jest to domyślny model hostingu i **ServicePackageActivationMode** nie muszą być określone w czasie tworzenia usługi.
>
>- Model hostingu proces wyłączności odpowiada **ServicePackageActivationMode** równa **się ExclusiveProcess**. Aby użyć tego ustawienia, należy określić go jawnie w momencie tworzenia usługi. 
>
>- Aby wyświetlić model hostingu usługi, należy zbadać [opis usługi][p2]i sprawdzić wartość **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Praca z wdrożonym pakietem usług
Aktywna kopia *ServicePackage* w węźle jest określana jako [wdrożony pakiet usług.][p3] W przypadku korzystania z modelu procesu wyłącznego do tworzenia usług dla danej aplikacji może istnieć wiele wdrożonych pakietów usług dla tego samego *pakietu ServicePackage.* Jeśli wykonujesz operacje specyficzne dla wdrożonego pakietu usług, należy podać **ServicePackageActivationId** do identyfikowania określonego wdrożonego pakietu usług. Na przykład podaj identyfikator, jeśli [zgłaszasz kondycję wdrożonego pakietu usług][p4] lub [ponownie uruchamiasz pakiet kodu wdrożonego pakietu usług][p5].

Identyfikator **usługi Wdrożony** pakiet usług można znaleźć, odpytując listę [wdrożonych pakietów usług][p3] w węźle. Podczas wykonywania zapytań dotyczących [wdrożonych typów usług,][p6] [wdrożonych replik][p7]i [wdrożonych pakietów kodu][p8] w węźle wynik kwerendy zawiera również **identyfikator ServicePackageActivationId** wdrożonego pakietu usługi nadrzędnego.

> [!NOTE]
>- W modelu hostingu procesu udostępnionego w danym węźle dla danej aplikacji aktywowana jest tylko jedna kopia *pakietu ServicePackage.* Ma **ServicePackageActivationId** równy *pustegład*, i nie muszą być określone podczas wykonywania operacji związanych z wdrożonym pakietem usługi. 
>
> - W modelu hostingu proces wyłączności, w danym węźle, dla danej aplikacji, jeden lub więcej kopii *ServicePackage* może być aktywny. Każda aktywacja ma *niepuste* **ServicePackageActivationId**, określone podczas wykonywania operacji związanych z wdrożonym pakietem usługi. 
>
> - Jeśli **ServicePackageActivationId** zostanie pominięty, domyślnie *pusty ciąg*. Jeśli wdrożony pakiet usługi, który został aktywowany w modelu procesu udostępnionego jest obecny, operacja zostanie wykonana na nim. W przeciwnym razie operacja zakończy się niepowodzeniem.
>
> - Nie wysyłaj kwerend ani razu i nie buforuj **ServicePackageActivationId**. Identyfikator jest generowany dynamicznie i może ulec zmianie z różnych powodów. Przed wykonaniem operacji, która wymaga **ServicePackageActivationId**, należy najpierw kwerendy listy [wdrożonych pakietów usług][p3] w węźle. Następnie użyj **ServicePackageActivationId** z wyniku kwerendy, aby wykonać oryginalną operację.
>
>

## <a name="guest-executable-and-container-applications"></a>Aplikacje wykonywalne i kontenerowe gościa
Sieć szkieletowa usług traktuje aplikacje [wykonywalne][a2] i [kontenerowe][a3] gościa jako usługi bezstanowe, które są samodzielne. Nie ma środowiska uruchomieniowego sieci szkieletowej usług w *ServiceHost* (proces lub kontener). Ponieważ te usługi są samodzielne, liczba replik na *ServiceHost* nie ma zastosowania do tych usług. Najbardziej typową konfiguracją używaną z tymi usługami jest pojedyncza partycja, z [InstanceCount][c2] równa -1 (jedna kopia kodu usługi uruchomionego w każdym węźle klastra). 

Domyślnym **ServicePackageActivationMode** dla tych usług jest **SharedProcess**, w którym to przypadku sieci szkieletowej usług aktywuje tylko jedną kopię *ServicePackage* w węźle dla danej aplikacji.  Oznacza to, że tylko jedna kopia kodu usługi uruchomi węzeł. Jeśli chcesz, aby wiele kopii kodu usługi działało w węźle, określ **ServicePackageActivationMode** jako **ExclusiveProcess** w momencie tworzenia usługi. Na przykład można to zrobić podczas tworzenia wielu usług *(Service1* *do ServiceN*) *z ServiceType* (określone w *ServiceManifest*), lub gdy usługa jest wielopodzielnie. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Zmienianie modelu hostingu istniejącej usługi
W chwili obecnej nie można zmienić modelu hostingu istniejącej usługi z procesu współużytkowania na proces wyłączny (lub odwrotnie).

## <a name="choose-between-the-hosting-models"></a>Wybierz między modelami hostingu
Powinieneś ocenić, który model hostingu najlepiej pasuje do Twoich wymagań. Model procesu udostępnionego lepiej używa zasobów systemu operacyjnego, ponieważ pojawia się mniej procesów, a wiele replik w tym samym procesie może współużytkować porty. Jednak jeśli jedna z replik ma błąd, gdzie musi obniżyć hosta usługi, ma wpływ na wszystkie inne repliki w tym samym procesie.

 Model procesu wyłącznego zapewnia lepszą izolację, z każdej repliki w swoim własnym procesie. Jeśli jedna z replik ma błąd, nie ma wpływu na inne repliki. Ten model jest przydatny w przypadkach, gdy udostępnianie portów nie jest obsługiwane przez protokół komunikacyjny. Ułatwia możliwość stosowania zarządzania zasobami na poziomie repliki. Jednak proces wyłączności zużywa więcej zasobów systemu operacyjnego, ponieważ tworzy jeden proces dla każdej repliki w węźle.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Wyłączne zagadnienia dotyczące modelu procesu i modelu aplikacji
W przypadku większości aplikacji można modelować aplikację w sieci szkieletowej usług, zachowując jeden *typ usługi* na *pakiet ServicePackage*. 

W niektórych przypadkach sieci szkieletowej usług pozwala również więcej niż jeden *ServiceType* na *ServicePackage* (i jeden *CodePackage* można zarejestrować więcej niż jeden *ServiceType).* Oto niektóre ze scenariuszy, w których te konfiguracje mogą być przydatne:

- Chcesz zoptymalizować wykorzystanie zasobów przez tarło mniej procesów i o większej gęstości repliki na proces.
- Repliki z różnych *ServiceTypes* muszą udostępniać niektóre typowe dane, które ma wysoki koszt inicjowania lub pamięci.
- Masz bezpłatną ofertę usług i chcesz umieścić limit wykorzystania zasobów, umieszczając wszystkie repliki usługi w tym samym procesie.

Model hostingu procesów wyłącznych nie jest spójny z modelem aplikacji o wielu *typach usług* na *pakiet ServicePackage.* Dzieje się tak, ponieważ wiele *servicetypes* na *ServicePackage* są przeznaczone do osiągnięcia większego udostępniania zasobów między replikami i umożliwia większą gęstość replik na proces. Model Procesu wyłącznego został zaprojektowany w celu osiągnięcia różnych wyników.

Należy wziąć pod uwagę przypadek wielu *ServiceTypes* na *ServicePackage*, z innym *CodePackage* rejestracji każdego *ServiceType*. Powiedzmy, że mamy *ServicePackage* "MultiTypeServicePackage", który ma dwa *CodePackages:*

- "MyCodePackageA", który rejestruje *ServiceType* "MyServiceTypeA".
- "MyCodePackageB", który rejestruje *ServiceType* "MyServiceTypeB".

Teraz załóżmy, że tworzymy aplikację, **fabric:/SpecialApp**. Wewnątrz **tkaniny:/SpecialApp,** tworzymy następujące dwie usługi z modelem procesu wyłącznego:

- Sieci **szkieletowej usługi:/SpecialApp/ServiceA** typu "MyServiceTypeA", z dwóch partycji (na przykład **P1** i **P2)** i trzy repliki na partycję.
- Szkielet **usługi:/SpecialApp/ServiceB** typu "MyServiceTypeB", z dwiema partycjami **(P3** i **P4)** i trzema replikami na partycję.

W danym węźle obie usługi mają dwie repliki. Ponieważ użyliśmy modelu procesu wyłącznego do utworzenia usług, sieć szkieletowa usług aktywuje nową kopię "MyServicePackage" dla każdej repliki. Każda aktywacja "MultiTypeServicePackage" rozpoczyna kopię "MyCodePackageA" i "MyCodePackageB". Jednak tylko jeden z "MyCodePackageA" lub "MyCodePackageB" hostuje replikę, dla której aktywowano "MultiTypeServicePackage". Na poniższym diagramie przedstawiono widok węzła:


![Diagram widoku węzła wdrożonej aplikacji][node-view-five]


W aktywacji "MultiTypeServicePackage" dla repliki partycji **P1** **sieci szkieletowej usługi:/SpecialApp/ServiceA**, "MyCodePackageA" jest hosting repliki. "MyCodePackageB" jest uruchomiony. Podobnie w aktywacji "MultiTypeServicePackage" dla repliki partycji **P3** **sieci szkieletowej usługi:/SpecialApp/ServiceB**, "MyCodePackageB" jest gospodarzem repliki. "MyCodePackageA" jest uruchomiony. W związku z tym im większa liczba *Pakietów Kodu* (rejestrowanie różnych *ServiceTypes)* na *ServicePackage,* tym wyższe użycie nadmiarowego zasobu. 
 
 Jeśli jednak utworzymy **sieć szkieletową usług:/SpecialApp/ServiceA** i **fabric:/SpecialApp/ServiceB** z modelem procesu udostępnionego, sieć szkieletowa usług aktywuje tylko jedną kopię "MultiTypeServicePackage" dla **sieci szkieletowej aplikacji:/SpecialApp**. 'MyCodePackageA' obsługuje wszystkie repliki dla **sieci szkieletowej usługi:/SpecialApp/ServiceA**. 'MyCodePackageB' obsługuje wszystkie repliki dla **sieci szkieletowej usługi:/SpecialApp/ServiceB**. Na poniższym diagramie przedstawiono widok węzła w tym ustawieniu: 


![Diagram widoku węzła wdrożonej aplikacji][node-view-six]


W poprzednim przykładzie można pomyśleć, że jeśli "MyCodePackageA" rejestruje zarówno "MyServiceTypeA" i "MyServiceTypeB", a nie ma "MyCodePackageB", a następnie nie ma nadmiarowych *CodePackage* uruchomiony. Mimo że jest to poprawne, ten model aplikacji nie jest zgodny z modelu hostingu proces wyłączny. Jeśli celem jest umieszczenie każdej repliki w swoim własnym dedykowanym procesie, nie trzeba rejestrować zarówno *ServiceTypes* z tego samego *CodePackage*. Zamiast tego wystarczy umieścić każdy *ServiceType* we własnym *ServicePackage*.

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Niezawodne usługi i podprocesy rozwidlenia aktora

Sieci szkieletowej usług nie obsługuje niezawodnych usług, a następnie niezawodne podmioty rozwidlenia podprocesów. Przykładem dlaczego jego nie jest obsługiwany jest [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) nie może służyć do rejestracji nieobsługiconych podprocesów i tokeny anulowania są wysyłane tylko do zarejestrowanych procesów; powoduje różnego rodzaju problemy, takie jak błędy uaktualniania, gdy podprocesy nie zamykają się po odebraniu tokenu anulowania przez proces nadrzędny.

## <a name="next-steps"></a>Następne kroki
[Zapakuj aplikację][a4] i przygotuj ją do wdrożenia.

[Wdrażanie i usuwanie aplikacji][a5]. W tym artykule opisano sposób używania programu PowerShell do zarządzania wystąpieniami aplikacji.

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
