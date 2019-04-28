---
title: Skalowalność usługi Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób skalowania usługi Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 14a7389fe562b5f3206b81411d2224257051c636
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60781135"
---
# <a name="scaling-in-service-fabric"></a>Skalowanie w usłudze Service Fabric
Usługa Azure Service Fabric ułatwia tworzenie skalowalnych aplikacji, umożliwiając zarządzanie usług, partycji i replik w węzłach klastra. Uruchamianie wielu obciążeń na tym samym sprzęcie umożliwia wykorzystanie zasobów maksymalna, ale również zapewnia elastyczność pod względem sposobu wybranego obciążenia. W tym wideo Channel 9 opisano, jak tworzyć aplikacje skalowalnych mikrousług:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Skalowanie w usłudze Service Fabric odbywa się na kilka różnych sposobów:

1. Skalowanie przez tworzenie lub usuwanie wystąpień usługi bezstanowej
2. Skalowanie przez utworzenie lub usunięcie nowych o nazwie usługi
3. Skalowanie przez utworzenie lub usunięcie nowych nazwanego wystąpienia aplikacji
4. Skalowanie za pomocą usług podzielonym na partycje
5. Skalowanie przez dodawanie i usuwanie węzłów z klastra 
6. Skalowanie przy użyciu metryk Menedżer zasobów klastra

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Skalowanie przez tworzenie lub usuwanie wystąpień usługi bezstanowej
Jedną z najprostszych sposobów skalowania w ramach usługi Service Fabric sprawdza w przypadku usług bezstanowych. Gdy tworzysz usługę bezstanową, otrzymasz możliwość definiowania `InstanceCount`. `InstanceCount` Określa, ile uruchomionej kopii kodu tej usługi są tworzone podczas uruchamiania usługi. Powiedzmy, na przykład, że istnieją 100 węzłów w klastrze. Ponadto Załóżmy, że usługa jest tworzona przy użyciu `InstanceCount` 10. Podczas wykonywania tych 10 uruchomionej kopii kodu może wszystkie stać się zbyt zajęty (lub może nie być wystarczająco zajęty). Jednym ze sposobów skalowania tego obciążenia jest aby zmienić liczbę wystąpień. Na przykład niektóre części kodu monitorowania lub zarządzania można zmienić istniejące liczby wystąpień, 50 lub 5, w zależności od tego, czy obciążenie konieczne skalowanie w pionie lub poziomie na podstawie obciążenia. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Program PowerShell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Za pomocą dynamicznego liczba wystąpień
Specjalnie dla usługi bezstanowej usługi Service Fabric oferuje automatyczny sposób, aby zmienić liczbę wystąpień. Dzięki temu usługa dynamicznie skalować liczbę węzłów, które są dostępne. Sposobem zdecydować się na to zachowanie jest Ustaw liczbę wystąpień = -1. InstanceCount = -1 jest instrukcję do usługi Service Fabric, który jest wyświetlany komunikat "Uruchom tę usługę bezstanową w każdym węźle." Zmiana liczby węzłów usługi Service Fabric automatycznie zmieni liczbę wystąpień, aby dopasować, zapewnienie, że usługa jest uruchomiona na wszystkich węzłach prawidłowe. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Skalowanie przez utworzenie lub usunięcie nowych o nazwie usługi
Nazwanym wystąpieniu usługi jest wystąpienie określonego typu usługi (zobacz [cyklu życia aplikacji usługi Service Fabric](service-fabric-application-lifecycle.md)) w ramach niektórych wystąpień nazwanych aplikacji w klastrze. 

Nowe wystąpienia usługi o nazwie można utworzyć (lub usunięte) jako usługi staną się bardziej lub mniej zajęty. Dzięki temu żądania do większej liczby wystąpień usługi, zazwyczaj co obciążenia na istniejące usługi, aby zmniejszyć ich rozmieszczenie. Podczas tworzenia usługi, Menedżer zasobów klastra usługi Service Fabric umieszcza usług w klastrze w sposób rozproszonych. Podlegają dokładnie decyzje [metryki](service-fabric-cluster-resource-manager-metrics.md) w klastrze i inne reguły umieszczania. Usługi można utworzyć kilka różnych sposobów, ale są najbardziej typowych, za pomocą działania administracyjne, takiego jak wywołanie [ `New-ServiceFabricService` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps), lub za pośrednictwem wywołania kodu [ `CreateServiceAsync` ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet). `CreateServiceAsync` nawet można wywołać z w ramach innych usług uruchomionych w klastrze.

Tworzenie usługi dynamicznie mogą być używane w szerokiej gamy scenariuszy i jest to typowy wzorzec. Rozważmy na przykład usługi stanowej, która reprezentuje określonego przepływu pracy. Wywołania reprezentujące pracę mają pojawienie się do tej usługi, a ta usługa będzie można wykonać kroki tego przepływu pracy i rejestrowanie postępu. 

Jak można utworzyć tej skali określonej usługi? Usługa może być wielu dzierżawców w pewnej postaci akceptować wywołania i uruchamiał kroki dla wielu różnych wystąpień tego samego przepływu pracy w całości. Jednak, może uczynić kod bardziej skomplikowane, ponieważ teraz ma już martwić się o wiele różnych wystąpień tego samego przepływu pracy na różnych etapach i od innych klientów. Ponadto obsługa wiele przepływów pracy w tym samym czasie nie rozwiąże problemu skali. Jest to spowodowane w pewnym momencie tej usłudze będą używać zbyt wiele zasobów, aby dopasować na danym komputerze. Wiele usług, które nie zostały skompilowane dla tego wzoru w pierwszej kolejności również występować problemy ze względu na pewne nieprzerwaną pracę wąskich gardeł lub spowolnienia w ich kodzie. Tego rodzaju problemy spowodować, że usługa nie działa tak dobrze, podczas gdy większe pobiera liczbę jednoczesnych przepływów pracy, który go służy do śledzenia.  

Jest to rozwiązanie do utworzenia wystąpienia tej usługi dla każdego innego wystąpienia przepływu pracy, który chcesz śledzić. Jest to doskonałe wzorzec i działa, czy usługa jest bezstanowe lub stanowe. Ten wzorzec do pracy jest zazwyczaj innej usługi, która działa jako "Usługa Menedżera obciążenia". Zadania tej usługi jest do odbierania żądań i kierowania tych żądań do innych usług. Menedżera można dynamiczne tworzenie wystąpienia usługi Obciążenie, po odebraniu wiadomości, a następnie przekazać w odpowiedzi na żądania do tych usług. Usługa Menedżera również otrzymać wywołania zwrotne danego przepływu pracy usługi zakończeniu swojego zadania. Kiedy Menedżera otrzymuje te wywołania zwrotne go można usunąć to wystąpienie usługi przepływu pracy, lub pozostaw to pole, jeśli są oczekiwano kolejnych wywołań. 

Zaawansowane wersje tego rodzaju Menedżera można nawet tworzyć pule usługi, którymi zarządza. Pula zapewnia, że gdy nowe żądanie jest oferowana w nie musi czekać do uruchomienia usługi. Zamiast tego menedżera można po prostu wybierz usługi przepływu pracy, który nie jest obecnie zajęta z puli lub losowo trasy. Utrzymywanie puli usług dostępnych sprawia, że nowe żądania obsługi szybciej, ponieważ jest mniej prawdopodobne, że żądanie ma czekać na nową usługę, która jest przetworzyliśmy. Tworzenia nowych usług w szybki, ale nie jest bezpłatne lub natychmiastowe. Pula pomaga zminimalizować ilość czasu, przez który żądanie ma czekać przed są obsługiwane. Często zobaczysz ten wzorzec puli i Menedżera gdy czas reakcji niezależnie od tego najczęściej. Kolejkowanie żądania i tworzenia usługi w tle i _następnie_ przekazanie jej jest również wzorcu Menedżera popularne jest tworzenie i usuwanie usług opartych na niektórych śledzenia dla ilości pracy usługi obecnie ma oczekujące . 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Skalowanie przez utworzenie lub usunięcie nowych nazwanego wystąpienia aplikacji
Tworzenie i usuwanie wystąpień całej aplikacji jest podobny do wzorca, tworzenia i usuwania usług. Ten wzorzec jest niektóre Usługa menedżera, która jest podejmowania decyzji na podstawie żądań, które ma ona do czynienia i informacje, że odbiera od innych usług w klastrze. 

Kiedy należy utworzyć nowe wystąpienie aplikacji o nazwie użyć zamiast tworzyć nowe wystąpienia usługi o nazwie w niektórych już istniejącej aplikacji? Istnieje kilka przypadków:

  * Nowe wystąpienie aplikacji jest dla klienta, którego kod musi zostać uruchomiony w ramach niektóre konkretnej tożsamości lub ustawienia zabezpieczeń.
    * Usługa Service Fabric umożliwia definiowanie pakietów inny kod uruchamiany w kontekście określonej tożsamości. Aby uruchomić ten sam pakiet kodu w ramach różnych tożsamości, aktywacja muszą zostać wykonane w różne wystąpienia aplikacji. Należy rozważyć przypadek, w którym masz istniejący klient obciążenia wdrożone. One może być uruchomiony w kontekście określonej tożsamości, aby można było monitorowanie i kontrolowanie dostępu do innych zasobów, takich jak zdalne bazy danych lub innych systemów. W takim przypadku po zarejestrowaniu nowego klienta, prawdopodobnie nie chcesz aktywować swój kod w tym samym kontekście (przestrzeni procesu). Chociaż można wykonać następujące akcje, to na celu utrudnienie kodu usługi do działania w kontekście określonej tożsamości. Zazwyczaj trzeba więcej zabezpieczeń, izolacji i kod zarządzania tożsamościami. Zamiast korzystać z różnych o nazwie wystąpienia usługi w ramach tego samego wystąpienia aplikacji i dlatego tego samego procesu miejsce, można użyć różnych wystąpień nazwanych aplikacji usługi Service Fabric. Ułatwia do definiowania kontekstów inna tożsamość.
  * Nowe wystąpienie aplikacji służy również jako sposób konfiguracji
    * Domyślnie wszystkich wystąpień usługi o nazwie typu określonej usługi w ramach wystąpienie aplikacji zostanie uruchomiony w ten sam proces danego węzła. Oznacza to, że mimo że możesz skonfigurować inaczej każde wystąpienie usługi jest to skomplikowane. Usługi muszą mieć pewne tokenu, używane w celu wyszukania ich konfiguracji w ramach pakietu konfiguracji. Zazwyczaj jest to po prostu nazwę usługi. Działa to prawidłowo, ale jej couples konfiguracji do nazw wystąpień poszczególnych nazwaną usługę w ciągu tego wystąpienia aplikacji. Może to być mylące i trudny do zarządzania, ponieważ konfiguracja jest zwykle artefaktu czasu projektowania, z określonymi wartościami wystąpienia aplikacji. Tworzenie większej liczby usług zawsze oznacza, że więcej uaktualnień aplikacji, aby zmienić informacji zawartych w pakietach konfiguracji lub wdrożyć nowe nowych usług można wyszukiwać ich szczegółowe informacje. Często łatwiej jest utworzyć zupełnie nowe wystąpienie aplikacji o nazwie. Następnie można użyć parametry aplikacji, można ustawić, niezależnie od konfiguracji jest niezbędne dla usług. Dzięki temu wszystkie usługi, które są tworzone w ramach których nazwane wystąpienie aplikacji może dziedziczyć ustawienia określonej konfiguracji. Na przykład zamiast pojedynczym pliku konfiguracji z ustawień i dostosowań, dla każdego klienta, takie jak hasła lub względem poszczególnych limity zasobów klienta, zamiast tego trzeba wystąpienie aplikacji jest inny dla każdego klienta przy użyciu tych ustawień zastąpione. 
  * Nowa aplikacja służy jako granicę uaktualnienia
    * W ramach usługi Service Fabric różne nazwanego wystąpienia aplikacji pełnić rolę granic dla uaktualnienie. Uaktualnienie jedno wystąpienie aplikacji o nazwie nie ma wpływu na kod, który jest uruchomione inne wystąpienie nazwane aplikacji. Różne aplikacje zostaną uruchomione różne wersje tego samego kodu w tej samej węzłów. Może to być czynnikiem, gdy trzeba podjąć decyzję skalowania, ponieważ można wybrać, czy nowego kodu należy przestrzegać tych samych uaktualnień jako inną usługę lub nie. Na przykład załóżmy, że wywołanie dociera do usługi Menedżera, która jest odpowiedzialna za skalowanie obciążeń określonego klienta, tworzenie i usuwanie usług dynamicznie. W takim jednak wywołanie jest dla obciążenia skojarzone z _nowe_ klienta. Większość klientów tak, pozostając w izolacji od siebie nawzajem nie tylko z powodów bezpieczeństwa i konfiguracji wymienionych powyżej, ale ponieważ zapewnia większą elastyczność w zakresie uruchomioną określoną wersją oprogramowania, a następnie wybierając, gdy ich uaktualniony. Może również utworzyć nowe wystąpienie aplikacji i Utwórz usługę po prostu na kolejne partycje ilość usługi, które będzie w ogóle jakiegokolwiek uaktualnienia jeden. Wystąpienia osobnych aplikacji zapewniają większą szczegółowość podczas wykonywania uaktualnienia aplikacji oraz włączenie A / B wdrożeń testowych i niebieski i zielony. 
  * Istniejące wystąpienie aplikacji jest pełny
    * W usłudze Service Fabric [pojemności aplikacji](service-fabric-cluster-resource-manager-application-groups.md) koncepcja używanej do kontrolowania ilości zasobów dostępnych dla wystąpień określonej aplikacji. Może na przykład można określić, że dana usługa musi mieć innego wystąpienia utworzone w celu przeprowadzenia skalowania. Jednak tego wystąpienia aplikacji jest poza pojemności dla niektórych metryki. Jeśli tego konkretnego klienta lub obciążenia może nadal być przyznany więcej zasobów, następnie użytkownik może zwiększyć pojemność istniejącego dla tej aplikacji lub Utwórz nową aplikację. 

## <a name="scaling-at-the-partition-level"></a>Skalowanie w poziomie partycji
Usługa Service Fabric obsługuje partycjonowanie. Partycjonowanie dzieli usługi na kilka sekcji logiczne i fizyczne, każde z nich działa niezależnie od siebie. To jest przydatne w przypadku usług stanowych, ponieważ nikt zestawu replik musi obsługiwać wszystkie wywołania i manipulowania wszystkimi stanu na raz. [Partycjonowanie Przegląd](service-fabric-concepts-partitioning.md) zawiera informacje na temat typów poszczególne schematy partycjonowania, które są obsługiwane. Replik każdej partycji są dystrybuowane między węzłami w klastrze, dystrybucji obciążenia tę usługę i zapewnienie, że żadna usługa jako całości ani żadnej partycji ma pojedynczy punkt awarii. 

Należy wziąć pod uwagę to usługa, która używa ranged schematu partycjonowania niska wartość klucza 0, wysoka wartość klucza 99 i liczbę partycji wynoszącą 4. W klastrze z trzema węzłami usługa może być rozmieszczony z czterech replik, które współdzielą zasoby w każdym węźle, jak pokazano poniżej:

<center>

![Układ partycji w przypadku użycia trzech węzłów](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Jeśli zwiększysz liczbę węzłów usługi Service Fabric zostanie przesunięty w niektóre z istniejących replik istnieje. Na przykład załóżmy Załóżmy, że liczba węzłów wzrasta do czterech i replikami Pobierz redystrybucji. Teraz usługa ma teraz trzy repliki uruchomione w każdym węźle, każdy należących do różnych partycji. Umożliwia to lepsze wykorzystanie zasobów, ponieważ nowy węzeł nie jest zimno. Zwykle również zwiększa wydajność, ponieważ każda usługa ma więcej dostępnych zasobów.

<center>

![Układ partycji w przypadku czterech węzłów](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Skalowanie za pomocą Menedżera zasobów klastra usługi w sieci szkieletowej i metryki
[Metryki](service-fabric-cluster-resource-manager-metrics.md) są, jak usługi express ich użycie zasobów w usłudze Service Fabric. Przy użyciu metryk daje możliwość reorganizowanie i zoptymalizować układ klastra Menedżer zasobów klastra. Na przykład może istnieć wiele zasobów w klastrze, ale nie może być przydzielonych do usług, które obecnie wykonują pracę. Przy użyciu metryk umożliwia Menedżer zasobów klastra reorganizacja klastra, aby upewnić się, że usługi mają dostęp do dostępnych zasobów. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Skalowanie przez dodawanie i usuwanie węzłów z klastra 
Inną opcją skalowania za pomocą usługi Service Fabric jest aby zmienić rozmiar klastra. Zmiana rozmiaru klastra oznacza dodawanie lub usuwanie węzłów dla jednego lub więcej typów węzłów w klastrze. Na przykład rozważmy przypadek, gdzie są wszystkie węzły w klastrze gorąca. Oznacza to, że zasoby klastra są prawie wszystkie używane. W przypadku dodawania kolejnych węzłów do klastra jest najlepszym sposobem skalowania. Po nowe węzły dołączą do klastra Menedżer zasobów klastra usługi Service Fabric przeniesienie usług, co mniej całkowitego obciążenia na istniejących węzłów. Przypadku usług bezstanowych z liczbą wystąpień = -1, usługa więcej wystąpień są tworzone automatycznie. Dzięki temu niektóre połączenia można przenieść z istniejących węzłów do nowych węzłów. 

Aby uzyskać więcej informacji, zobacz [skalowanie klastra](service-fabric-cluster-scaling.md).

## <a name="putting-it-all-together"></a>Zebranie wszystkich elementów
Weźmy pomysły, które firma Microsoft została omówiona w tym miejscu i komunikować się za pośrednictwem przykładem. Należy wziąć pod uwagę następujące usługi: próbujesz utworzyć usługę, który działa jako książkę adresową utrzymuje nazwy i informacje kontaktowe. 

Bezpośrednio na początku masz wiele pytania dotyczące skalowania: Ilu użytkowników będą mieć? Ile kontakty będą przechowywane w poszczególnych użytkowników Ilustracja to wszystko na zewnątrz po użytkownik stały się z usługą po raz pierwszy jest trudne. Załóżmy, że miało go z jedną usługą statyczne wraz z liczbą określonej partycji. Konsekwencje pobrania liczba partycji problem może spowodować później mieć problemów związanych ze skalowaniem. Podobnie nawet w przypadku wybrania prawo liczby, których możesz nie mieć wszystkie informacje potrzebne. Na przykład również należy określić rozmiar klastra, na początku, zarówno pod względem liczby węzłów i ich rozmiary. Jest to zazwyczaj trudny do prognozowania liczby zasobów, usługa będzie używać przez cały okres ich istnienia. Można go również wiadomo wcześniejsze wzorzec ruchu, który faktycznie widzi usługi. Na przykład osób może być dodawanie i usuwanie kontaktów w tylko po pierwsze, w nocy lub może on jest rozmieszczonych równomiernie w ciągu dnia. Oparte na tym, który może być konieczne skalowanie w poziomie w dynamicznie. Być może można znaleźć umożliwiającej przewidywanie, kiedy użytkownik chce potrzeby skalowania w poziomie i w, ale w obu przypadkach prawdopodobnie będziemy potrzebować reagować na zmieniające się użycie zasobów przez usługę. Może to obejmować zmiany rozmiaru klastra w celu zapewnienia większej ilości zasobów, gdy reorganizacja korzystanie z istniejących zasobów nie jest wystarczająco dużo. 

Ale Dlaczego nawet spróbuj wyszukać schematu jednej partycji dla wszystkich użytkowników? Dlaczego ograniczać się do jednej usługi i jeden klaster statyczne? Rzeczywisty stan jest zazwyczaj bardziej dynamiczne. 

Podczas kompilowania dla skalowania, należy wziąć pod uwagę następujący wzorzec dynamicznych. Może być konieczne dostosowanie jej do swojej sytuacji:

1. Zamiast próbować wybierz schemat partycjonowania dla wszystkich użytkowników na początku, twórz "manager service".
2. Zadania programu manager service jest spojrzeć na informacje o kliencie, po utworzeniu konta usługi. A następnie w zależności od tych informacji usługi Menedżera Utwórz wystąpienie usługi _rzeczywiste_ skontaktuj się z magazynu usługi _tylko dla tego klienta_. Jeśli wymagają konkretnej konfiguracji, izolacji lub uaktualnień, można też uruchomić wystąpienie aplikacji dla danego klienta. 

To dynamiczne tworzenie wzorca wiele korzyści:

  - Nie próby odgadnięcia liczbę właściwą partycję dla wszystkich użytkowników na początku lub tworzenia pojedynczej usługi, która jest nieskończenie skalowalnych wszystko samodzielnie. 
  - Różni użytkownicy nie mają mieć tej samej liczby partycji, liczba replik, ograniczeniami dotyczącymi umieszczania, metryki, ładowania domyślnej, nazwy usługi, ustawienia dns lub dowolne inne właściwości określonego na poziomie aplikacji lub usługi. 
  - Możesz uzyskać segmentacji dodatkowe dane. Każdy klient ma swoje własne kopie usługi
    - Każda usługa klienta można inaczej skonfigurowana i przyznane zwiększenie lub zmniejszenie liczby zasobów, z zwiększenie lub zmniejszenie liczby partycji lub repliki zgodnie z potrzebami, w oparciu o ich oczekiwanej skali.
      - Na przykład powiedz klienta płatnej warstwie "Złota" - one można pobrać więcej replik lub większą liczbę partycji oraz potencjalnie zasobów dedykowanych do swoich usług przy użyciu możliwości metryk i aplikacji.
      - Lub Załóżmy, że udostępniała informacje wskazujące, kontakty, potrzebnych została "Małe" — otrzymamy tylko kilka partycje lub nawet mogą być wprowadzane w pulę usługi udostępnionej przy użyciu innych klientów.
  - Nie Uruchamiamy wiele wystąpień usługi lub replik w czasie oczekiwania dla klientów do wyświetlenia
  - Jeśli klient nigdy nie opuszcza, następnie usuwania ich informacji z usługi jest proste jako posiadające manager Usuń tej usługi lub aplikacji, do którego utworzona.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pojęć usługi Service Fabric zobacz następujące artykuły:

* [Dostępność usługi Service Fabric](service-fabric-availability-services.md)
* [Partycjonowanie usługi Service Fabric](service-fabric-concepts-partitioning.md)
