---
title: Skalowalność usług sieci szkieletowej usług
description: Dowiedz się więcej o skalowaniu w sieci szkieletowej usług Azure i różnych technikach używanych do skalowania aplikacji.
author: masnider
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: masnider
ms.openlocfilehash: 17827342b67d37d9fbeb56654824e004367823ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282564"
---
# <a name="scaling-in-service-fabric"></a>Skalowanie w sieci szkieletowej usług
Usługa Azure Service Fabric ułatwia tworzenie skalowalnych aplikacji, zarządzając usługami, partycjami i replikami w węzłach klastra. Uruchamianie wielu obciążeń na tym samym sprzęcie umożliwia maksymalne wykorzystanie zasobów, ale zapewnia również elastyczność w zakresie sposobu skalowania obciążeń. W tym klipie wideo kanału 9 opisano, jak można tworzyć skalowalne aplikacje mikrousług:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Skalowanie w sieci szkieletowej usług odbywa się na kilka różnych sposobów:

1. Skalowanie przez tworzenie lub usuwanie wystąpień usługi bezstanowej
2. Skalowanie przez tworzenie lub usuwanie nowych nazwanych usług
3. Skalowanie przez tworzenie lub usuwanie nowych nazwanych wystąpień aplikacji
4. Skalowanie przy użyciu usług podzielonych na partycje
5. Skalowanie przez dodawanie i usuwanie węzłów z klastra 
6. Skalowanie przy użyciu metryk Menedżera zasobów klastra

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Skalowanie przez tworzenie lub usuwanie wystąpień usługi bezstanowej
Jednym z najprostszych sposobów skalowania w sieci szkieletowej usług działa z usług bezstanowych. Podczas tworzenia usługi bezstanowej, masz szansę, `InstanceCount`aby zdefiniować . `InstanceCount`określa, ile uruchomionych kopii kodu tej usługi jest tworzonych podczas uruchamiania usługi. Załóżmy na przykład, że w klastrze znajduje się 100 węzłów. Załóżmy również, że usługa jest `InstanceCount` tworzona z 10. W czasie wykonywania te 10 uruchomionych kopii kodu może stać się zbyt zajęty (lub może nie być wystarczająco zajęty). Jednym ze sposobów skalowania tego obciążenia jest zmiana liczby wystąpień. Na przykład niektóre fragment monitorowania lub kodu zarządzania można zmienić istniejącą liczbę wystąpień do 50 lub 5, w zależności od tego, czy obciążenie musi skalować lub na podstawie obciążenia. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Korzystanie z liczby wystąpień dynamicznych
W szczególności dla usług bezstanowych sieci szkieletowej usług oferuje automatyczny sposób, aby zmienić liczbę wystąpień. Dzięki temu usługa do dynamicznego skalowania z liczbą węzłów, które są dostępne. Sposobem na wybranie tego zachowania jest ustawienie liczby wystąpień = -1. InstanceCount = -1 jest instrukcją do sieci szkieletowej usług, który mówi "Uruchom tę usługę bezstanową w każdym węźle." Jeśli liczba węzłów ulegnie zmianie, sieci szkieletowej usług automatycznie zmienia liczbę wystąpień, aby dopasować, zapewniając, że usługa jest uruchomiona we wszystkich prawidłowych węzłów. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Skalowanie przez tworzenie lub usuwanie nowych nazwanych usług
Wystąpienie usługi o nazwie jest określonym wystąpieniem typu usługi (zobacz [cykl życia aplikacji sieci szkieletowej usług)](service-fabric-application-lifecycle.md)w niektórych wystąpieniach aplikacji o nazwie w klastrze. 

Nowe wystąpienia usługi o nazwie mogą być tworzone (lub usuwane), jak usługi stają się mniej lub bardziej zajęty. Dzięki temu żądania mają być rozłożone na więcej wystąpień usługi, zwykle umożliwiając obciążenie istniejących usług, aby zmniejszyć. Podczas tworzenia usług Menedżer zasobów klastra sieci szkieletowej usług umieszcza usługi w klastrze w sposób rozproszony. Dokładne decyzje są regulowane przez [metryki](service-fabric-cluster-resource-manager-metrics.md) w klastrze i inne reguły umieszczania. Usługi można tworzyć na kilka różnych sposobów, ale najczęściej są [`New-ServiceFabricService`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)to akcje administracyjne, takie jak wywoływanie przez kogoś lub wywoływanie [`CreateServiceAsync`](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet)kodu. `CreateServiceAsync`można nawet wywołać z poziomu innych usług uruchomionych w klastrze.

Dynamiczne tworzenie usług może być używane w różnego rodzaju scenariuszach i jest typowym wzorcem. Rozważmy na przykład usługi stanowej, która reprezentuje określonego przepływu pracy. Wywołania reprezentujące pracę będą wyświetlane w tej usłudze, a ta usługa będzie wykonywać kroki do tego przepływu pracy i rejestrować postęp. 

Jak można zrobić tę skalę usług? Usługa może być wielodostępna w jakiejś formie i akceptować wywołania i rozpoczynać kroki dla wielu różnych wystąpień tego samego przepływu pracy naraz. Jednak to może uczynić kod bardziej złożony, ponieważ teraz musi martwić się o wiele różnych wystąpień tego samego przepływu pracy, wszystkie na różnych etapach i od różnych klientów. Ponadto obsługa wielu przepływów pracy w tym samym czasie nie rozwiązuje problemu skalowania. Dzieje się tak, ponieważ w pewnym momencie ta usługa zużywa zbyt wiele zasobów, aby zmieścić się na określonym komputerze. Wiele usług nie zbudowany dla tego wzorca w pierwszej kolejności również wystąpić trudności z powodu pewnego nieodłączne wąskie gardło lub spowolnienie w kodzie. Tego typu problemy powodują, że usługa nie działa tak dobrze, gdy liczba równoczesnych przepływów pracy, które śledzi, staje się większa.  

Rozwiązaniem jest utworzenie wystąpienia tej usługi dla każdego wystąpienia przepływu pracy, który chcesz śledzić. Jest to wielki wzorzec i działa, czy usługa jest bezstanowy lub stanowy. Aby ten wzorzec działał, zwykle istnieje inna usługa, która działa jako "Usługa Menedżera obciążenia". Zadaniem tej usługi jest odbieranie żądań i kierowanie tych żądań do innych usług. Menedżer może dynamicznie utworzyć wystąpienie usługi obciążenia po odebraniu wiadomości, a następnie przekazać żądania do tych usług. Usługa menedżera może również odbierać wywołania zwrotne, gdy dana usługa przepływu pracy zakończy swoje zadanie. Gdy menedżer odbiera te wywołania zwrotne może usunąć to wystąpienie usługi przepływu pracy lub pozostawić go, jeśli oczekuje się więcej wywołań. 

Zaawansowane wersje tego typu menedżera można nawet tworzyć pule usług, którymi zarządza. Pula pomaga upewnić się, że gdy pojawi się nowe żądanie, nie trzeba czekać na rozkręcenie usługi. Zamiast tego menedżer można po prostu wybrać usługę przepływu pracy, która nie jest obecnie zajęty z puli lub trasy losowo. Utrzymywanie puli dostępnych usług sprawia, że obsługa nowych żądań szybciej, ponieważ jest mniej prawdopodobne, że żądanie musi czekać na nową usługę, która ma zostać spun up. Tworzenie nowych usług jest szybkie, ale nie bezpłatne lub natychmiastowe. Pula pomaga zminimalizować czas, przez który żądanie musi czekać przed serwisem. Często zobaczysz ten menedżer i wzorzec puli, gdy czasy odpowiedzi mają największe znaczenie. Kolejkowanie żądania i tworzenie usługi w tle, a _następnie_ przekazywanie go jest również popularnym wzorcem menedżera, podobnie jak tworzenie i usuwanie usług na podstawie niektórych śledzenia ilości pracy, którą usługa aktualnie oczekuje. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Skalowanie przez tworzenie lub usuwanie nowych nazwanych wystąpień aplikacji
Tworzenie i usuwanie całych wystąpień aplikacji jest podobny do wzorca tworzenia i usuwania usług. Dla tego wzorca istnieje niektóre usługi menedżera, który podejmuje decyzję na podstawie żądań, które jest widząc i informacje, które otrzymuje od innych usług wewnątrz klastra. 

Kiedy należy utworzyć nowe wystąpienie aplikacji o nazwie powinny być używane zamiast tworzenia nowych wystąpień usługi o nazwie w niektórych już istniejących aplikacji? Jest kilka przypadków:

  * Nowe wystąpienie aplikacji jest dla klienta, którego kod musi działać w niektórych określonych ustawień tożsamości lub zabezpieczeń.
    * Usługa Sieci szkieletowej umożliwia definiowanie różnych pakietów kodu do uruchomienia w ramach określonych tożsamości. Aby uruchomić ten sam pakiet kodu w różnych tożsamości, aktywacje muszą wystąpić w różnych wystąpieniach aplikacji. Należy wziąć pod uwagę przypadek, w którym masz istniejące obciążenia klienta wdrożone. Mogą one być uruchomione w ramach określonej tożsamości, dzięki czemu można monitorować i kontrolować ich dostęp do innych zasobów, takich jak zdalne bazy danych lub innych systemów. W takim przypadku, gdy nowy klient zarejestruje się, prawdopodobnie nie chcesz aktywować ich kod w tym samym kontekście (przestrzeni procesu). Chociaż można to utrudnia kod usługi do działania w kontekście określonej tożsamości. Zazwyczaj musi mieć więcej zabezpieczeń, izolacji i kodu zarządzania tożsamościami. Zamiast używać różnych wystąpień usługi o nazwie w tym samym wystąpieniu aplikacji, a tym samym przestrzeni procesu, można użyć różnych wystąpień aplikacji sieci szkieletowej usługi o nazwie. Ułatwia to definiowanie różnych kontekstów tożsamości.
  * Nowe wystąpienie aplikacji służy również jako środek konfiguracji
    * Domyślnie wszystkie wystąpienia usługi o nazwie określonego typu usługi w wystąpieniu aplikacji będą uruchamiane w tym samym procesie w danym węźle. Oznacza to, że chociaż można skonfigurować każde wystąpienie usługi inaczej, to jest to skomplikowane. Usługi muszą mieć jakiś token, którego używają do wyszukiwania ich konfiguracji w pakiecie konfiguracyjnym. Zazwyczaj jest to tylko nazwa usługi. To działa poprawnie, ale łączy konfigurację z nazwami poszczególnych wystąpień usługi o nazwie w tym wystąpieniu aplikacji. Może to być mylące i trudne do zarządzania, ponieważ konfiguracja jest zwykle artefakt czasu projektowania z wartości specyficznych dla wystąpienia aplikacji. Tworzenie większej liczby usług zawsze oznacza więcej uaktualnień aplikacji, aby zmienić informacje w pakietach konfiguracyjnych lub wdrożyć nowe, aby nowe usługi mogły wyszukać ich konkretne informacje. Często łatwiej jest utworzyć zupełnie nowe wystąpienie aplikacji o nazwie. Następnie można użyć parametrów aplikacji, aby ustawić dowolną konfigurację jest niezbędną dla usług. W ten sposób wszystkie usługi, które są tworzone w ramach tego wystąpienia aplikacji o nazwie można dziedziczyć określone ustawienia konfiguracji. Na przykład zamiast jednego pliku konfiguracyjnego z ustawieniami i dostosowaniami dla każdego klienta, takimi jak wpisy tajne lub limity zasobów dla klienta, zamiast tego masz inne wystąpienie aplikacji dla każdego klienta z tymi ustawieniami Zastąpiona. 
  * Nowa aplikacja służy jako granica uaktualnienia
    * W sieci szkieletowej usług różne wystąpienia nazwanych aplikacji służą jako granice do uaktualnienia. Uaktualnienie jednego wystąpienia aplikacji o nazwie nie wpłynie na kod, który jest uruchomiony innego wystąpienia aplikacji o nazwie. Różne aplikacje będą działać różne wersje tego samego kodu w tych samych węzłach. Może to być czynnikiem, gdy trzeba podjąć decyzję skalowania, ponieważ można wybrać, czy nowy kod powinien wykonać te same uaktualnienia jako innej usługi, czy nie. Na przykład załóż, że wywołanie dociera do usługi menedżera, która jest odpowiedzialna za skalowanie obciążeń określonego klienta przez dynamiczne tworzenie i usuwanie usług. W takim przypadku jednak wywołanie dotyczy obciążenia skojarzonego z _nowym_ klientem. Większość klientów lubi być odizolowane od siebie nie tylko ze względów bezpieczeństwa i konfiguracji wymienionych wcześniej, ale dlatego, że zapewnia większą elastyczność w zakresie uruchamiania określonych wersji oprogramowania i wyboru, kiedy są uaktualniane. Można również utworzyć nowe wystąpienie aplikacji i utworzyć usługę tam po prostu do dalszej partycji ilość usług, które dotknie jednego uaktualnienia. Oddzielne wystąpienia aplikacji zapewniają większą szczegółowość podczas wykonywania uaktualnień aplikacji, a także umożliwiają testowanie A/B i wdrożenia blue/green. 
  * Istniejące wystąpienie aplikacji jest pełne
    * W sieci szkieletowej [usług pojemność aplikacji](service-fabric-cluster-resource-manager-application-groups.md) jest pojęciem, którego można użyć do kontrolowania ilości zasobów dostępnych dla określonych wystąpień aplikacji. Na przykład można zdecydować, że dana usługa musi mieć inne wystąpienie utworzone w celu skalowania. Jednak to wystąpienie aplikacji jest poza pojemnością dla określonej metryki. Jeśli ten konkretny klient lub obciążenie powinno nadal mieć więcej zasobów, można zwiększyć istniejącą pojemność dla tej aplikacji lub utworzyć nową aplikację. 

## <a name="scaling-at-the-partition-level"></a>Skalowanie na poziomie partycji
Usługa Fabric obsługuje partycjonowanie. Partycjonowanie dzieli usługę na kilka sekcji logicznych i fizycznych, z których każda działa niezależnie. Jest to przydatne w przypadku usług stanowych, ponieważ żaden zestaw replik nie musi obsługiwać wszystkich wywołań i manipulować całym stanem jednocześnie. [Omówienie partycjonowania](service-fabric-concepts-partitioning.md) zawiera informacje na temat typów schematów partycjonowania, które są obsługiwane. Repliki każdej partycji są rozłożone na węzły w klastrze, rozdzielając obciążenie tej usługi i zapewniając, że ani usługa jako całość, ani żadna partycja nie ma jednego punktu awarii. 

Należy wziąć pod uwagę usługę, która używa schematu partycjonowania dystansowego z niskim kluczem 0, wysokim kluczem 99 i liczbą partycji 4. W klastrze z trzema węzłami usługa może być rozmieszczona z czterema replikami, które współużytkują zasoby w każdym węźle, jak pokazano poniżej:

<center>

![Układ partycji z trzema węzłami](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Jeśli zwiększysz liczbę węzłów, sieci szkieletowej usług przeniesie niektóre z istniejących replik tam. Załóżmy na przykład, że liczba węzłów wzrasta do czterech, a repliki są redystrybuowane. Teraz usługa ma teraz trzy repliki uruchomione na każdym węźle, z których każda należy do różnych partycji. Pozwala to na lepsze wykorzystanie zasobów, ponieważ nowy węzeł nie jest zimny. Zazwyczaj zwiększa również wydajność, ponieważ każda usługa ma więcej zasobów dostępnych dla niego.

<center>

![Układ partycji z czterema węzłami](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Skalowanie przy użyciu Menedżera zasobów klastra sieci szkieletowej usług i metryki
[Metryki](service-fabric-cluster-resource-manager-metrics.md) są jak usługi wyrazić zużycie zasobów do sieci szkieletowej usług. Korzystanie z metryk daje Menedżerowi zasobów klastra możliwość reorganizacji i optymalizacji układu klastra. Na przykład może istnieć wiele zasobów w klastrze, ale nie mogą być przydzielane do usług, które obecnie wykonują pracę. Korzystanie z metryk umożliwia Menedżerowi zasobów klastra reorganizowanie klastra w celu zapewnienia, że usługi mają dostęp do dostępnych zasobów. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Skalowanie przez dodawanie i usuwanie węzłów z klastra 
Inną opcją skalowania za pomocą sieci szkieletowej usług jest zmiana rozmiaru klastra. Zmiana rozmiaru klastra oznacza dodawanie lub usuwanie węzłów dla jednego lub więcej typów węzłów w klastrze. Rozważmy na przykład przypadek, w którym wszystkie węzły w klastrze są gorące. Oznacza to, że zasoby klastra są prawie wszystkie używane. W takim przypadku dodanie większej liczby węzłów do klastra jest najlepszym sposobem skalowania. Gdy nowe węzły dołączą do klastra, Menedżer zasobów klastra sieci szkieletowej usług przenosi do nich usługi, co powoduje mniejsze całkowite obciążenie istniejących węzłów. W przypadku usług bezstanowych z liczbą wystąpień = -1 automatycznie tworzone są kolejne wystąpienia usługi. Dzięki temu niektóre wywołania, aby przejść z istniejących węzłów do nowych węzłów. 

Aby uzyskać więcej informacji, zobacz [skalowanie klastra](service-fabric-cluster-scaling.md).

## <a name="choosing-a-platform"></a>Wybór platformy

Ze względu na różnice w implementacji między systemami operacyjnymi, wybranie opcji korzystania z sieci szkieletowej usług z systemem Windows lub Linux może być istotną częścią skalowania aplikacji. Jedną z potencjalnych barier jest sposób rejestrowania etapowego. Sieci szkieletowej usług w systemie Windows używa sterownika jądra dla dziennika jeden na komputer, współużytkowane przez repliki usługi stanowej. Ten dziennik waży około 8 GB. Z kolei system Linux używa dziennika przejściowego o rozmiarze 256 MB dla każdej repliki, co czyni go mniej idealnym rozwiązaniem dla aplikacji, które chcą zmaksymalizować liczbę lekkich replik usługi uruchomionych w danym węźle. Te różnice w wymaganiach dotyczących magazynu tymczasowego może potencjalnie poinformować żądaną platformę do wdrażania klastra sieci szkieletowej usług.

## <a name="putting-it-all-together"></a>Zebranie wszystkich elementów
Weźmy wszystkie pomysły, które tutaj omówiliśmy i porozmawiajmy na przykładzie. Rozważmy następującą usługę: próbujesz zbudować usługę, która działa jako książka adresowa, trzymając się nazw i informacji kontaktowych. 

Od razu z góry masz kilka pytań związanych ze skalą: Ilu użytkowników będziesz miał? Ile kontaktów będzie przechowywać każdy użytkownik? Próbuje dowiedzieć się tego wszystkiego, gdy stoisz swoje usługi po raz pierwszy jest trudne. Załóżmy, że zamierzasz iść z pojedynczą usługą statyczną z określoną liczbą partycji. Konsekwencje pobrania niewłaściwej liczby partycji może spowodować problemy skalowania później. Podobnie, nawet jeśli wybierzesz właściwą liczbę, możesz nie mieć wszystkich potrzebnych informacji. Na przykład należy również zdecydować rozmiar klastra z góry, zarówno pod względem liczby węzłów i ich rozmiary. Zazwyczaj trudno jest przewidzieć, ile zasobów usługa będzie zużywać w ciągu swojego okresu istnienia. Może być również trudno wiedzieć z wyprzedzeniem wzorzec ruchu, który usługa faktycznie widzi. Na przykład, może ludzie dodają i usuwają swoje kontakty tylko pierwszą rzeczą rano, a może jest rozłożona równomiernie w ciągu dnia. Na tej podstawie może być konieczne skalowanie w poziomie i dynamicznie. Być może nauczysz się przewidywać, kiedy będziesz musiał skalować w poziomie i w, ale tak czy inaczej prawdopodobnie będziesz musiał zareagować na zmianę zużycia zasobów przez usługę. Może to obejmować zmianę rozmiaru klastra w celu zapewnienia większej ilości zasobów podczas reorganizacji wykorzystania istniejących zasobów nie wystarczy. 

Ale dlaczego nawet spróbować wybrać jeden schemat partycji dla wszystkich użytkowników? Po co ograniczać się do jednej usługi i jednego klastra statycznego? Rzeczywista sytuacja jest zwykle bardziej dynamiczna. 

Podczas tworzenia skali należy wziąć pod uwagę następujący wzorzec dynamiczny. Może być konieczne dostosowanie go do swojej sytuacji:

1. Zamiast próbować wybrać schemat partycjonowania dla wszystkich z góry, zbudować "usługę menedżera".
2. Zadaniem usługi menedżera jest przeglądanie informacji o klientach, gdy zapisują się do usługi. Następnie, w zależności od tych informacji, usługa menedżera tworzy wystąpienie _rzeczywistej_ usługi przechowywania kontaktów _tylko dla tego klienta._ Jeśli wymagają one określonej konfiguracji, izolacji lub uaktualnień, można również zdecydować się na rozkręcenie wystąpienia aplikacji dla tego klienta. 

Ten dynamiczny wzorzec tworzenia wiele korzyści:

  - Nie próbujesz odgadnąć poprawnej liczby partycji dla wszystkich użytkowników z góry lub zbudować jedną usługę, która jest nieskończenie skalowalna. 
  - Różni użytkownicy nie muszą mieć tej samej liczby partycji, liczby replik, ograniczeń umieszczania, metryk, domyślnych obciążeń, nazw usług, ustawień dns ani żadnej z innych właściwości określonych na poziomie usługi lub aplikacji. 
  - Zyskujesz dodatkową segmentację danych. Każdy klient ma własną kopię usługi
    - Każda obsługa klienta można skonfigurować inaczej i przyznać więcej lub mniej zasobów, z więcej lub mniej partycji lub replik w razie potrzeby na podstawie ich oczekiwanej skali.
      - Załóżmy na przykład, że klient zapłacił za warstwę "Gold" — mogą uzyskać więcej replik lub większą liczbę partycji i potencjalnie zasoby dedykowane ich usługom za pośrednictwem metryk i pojemności aplikacji.
      - Albo powiedzmy, że podali informacje wskazujące, że liczba kontaktów, których potrzebują, była "Mała" - otrzymają tylko kilka partycji, a nawet mogą zostać umieszczone w puli usług udostępnionych innym klientom.
  - Nie używasz kilku wystąpień lub replik usług podczas oczekiwania na wyświetlenie się klientów
  - Jeśli klient kiedykolwiek opuści, usunięcie jego informacji z usługi jest tak proste, jak usunięcie przez menedżera tej utworzonej usługi lub aplikacji.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat koncepcji sieci szkieletowej usług, zobacz następujące artykuły:

* [Dostępność usług sieci szkieletowej usług](service-fabric-availability-services.md)
* [Usługi partycjonowania usługi sieci szkieletowej](service-fabric-concepts-partitioning.md)
