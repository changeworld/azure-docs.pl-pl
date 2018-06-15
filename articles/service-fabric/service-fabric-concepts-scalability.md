---
title: Skalowalność usługi sieć szkieletowa usług | Dokumentacja firmy Microsoft
description: Opis zmiany skali usługi sieci szkieletowej usług
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 88c563876940da14f75d7ab30aa3f79a8f6dd870
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209376"
---
# <a name="scaling-in-service-fabric"></a>Skalowanie w sieci szkieletowej usług
Sieć szkieletowa usług Azure ułatwia tworzenie skalowalnych aplikacji dzięki zarządzaniu usług, partycji i replik w węzłach klastra. Uruchamianie wielu obciążeń na tym samym sprzęcie umożliwia wykorzystanie zasobów maksymalna, ale również zapewnia elastyczność pod względem sposobu wybranego skalowania obciążeń. To wideo z witryny Channel 9 opisano, jak w przypadku tworzenia aplikacji skalowalnych mikrousług:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Skalowanie w sieci szkieletowej usług odbywa się na kilka różnych sposobów:

1. Skalowanie przez tworzenie lub usuwanie wystąpienia usługi bezstanowej
2. Skalowanie przez tworzenia lub usuwania nowych o nazwie usługi
3. Skalowanie przez tworzenia lub usuwania nowych nazwane wystąpienia aplikacji
4. Skalowanie za pomocą usług podzielonym na partycje
5. Skalowanie przez dodawanie i usuwanie węzłów z klastra 
6. Skalowanie przy użyciu metryk Menedżera zasobów klastra

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Skalowanie przez tworzenie lub usuwanie wystąpienia usługi bezstanowej
Jedną z najprostszych sposobów skalować w sieci szkieletowej usług współpracuje z usług bezstanowych. Podczas tworzenia usługi bezstanowej, możesz uzyskać możliwość definiowania `InstanceCount`. `InstanceCount` Określa, ile działających kopii tej usługi kodu są tworzone po uruchomieniu usługi. Załóżmy na przykład, czy 100 węzłów w klastrze. Również Załóżmy, że usługa jest tworzony z `InstanceCount` 10. W czasie wykonywania te 10 uruchomionej kopii kodu może wszystkie stać się zbyt zajęty (lub może nie być wystarczająco zajęty). Jednym ze sposobów skalować, aby obciążenie jest liczba wystąpień. Na przykład niektóre fragment kodu, monitorowania i zarządzania można zmienić istniejące liczbę wystąpień 50 lub 5, w zależności od tego, czy obciążenie powinna skalować lub na podstawie obciążenia. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Środowiska PowerShell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Przy użyciu dynamicznych liczba wystąpień
W szczególności w przypadku usług bezstanowych sieci szkieletowej usług oferuje automatyczny sposób, aby zmienić liczbę wystąpień. Dzięki temu usługa dynamicznie skalować liczbę węzłów, które są dostępne. Sposobem uczestnictwa w to zachowanie jest ustawiona liczba wystąpień = -1. Wartość InstanceCount = -1 jest instrukcją sieci szkieletowej usług informacją "Uruchamianie tej usługi bezstanowej na każdym węźle." W przypadku zmiany liczby węzłów, sieci szkieletowej usług automatycznie zmienia liczbę wystąpień do dopasowania, zapewnienie, że usługa jest uruchomiona na wszystkich węzłach prawidłowe. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Środowiska PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Skalowanie przez tworzenia lub usuwania nowych o nazwie usługi
Wystąpienie usługi o nazwie jest określonego wystąpienia typu usługi (zobacz [cyklu życia aplikacji w sieci szkieletowej usług](service-fabric-application-lifecycle.md)) w ramach niektóre wystąpienia nazwanego aplikacji w klastrze. 

Nowe wystąpienia usługi o nazwie można utworzyć (lub usunięte) jako usługi staną się bardziej lub mniej zajęty. Dzięki temu żądań było ich rozmieszczenie więcej wystąpień usługi, zwykle zwiększając istniejących usług na zmniejszenie obciążenia. Podczas tworzenia usługi, Menedżer zasobów klastra sieci szkieletowej usług umieszcza usługi klastra w sposób rozproszonych. Dokładne decyzje są regulowane przez [metryki](service-fabric-cluster-resource-manager-metrics.md) w klastrze i inne zasady umieszczania. Usługi mogą być tworzone na kilka różnych sposobów, ale są najczęściej używane przez działania administracyjne, takie jak ktoś wywoływania [ `New-ServiceFabricService` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps), lub przez wywołanie kodu [ `CreateServiceAsync` ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet). `CreateServiceAsync` Można nawet można wywoływać z wewnątrz innych usług uruchomionych w klastrze.

Dynamicznie tworzenie usług mogą być używane w szerokiej gamy scenariuszy i jest wspólnym wzorcem. Rozważmy na przykład usługi stanowej, która reprezentuje określonego przepływu pracy. Połączenia reprezentującą pracy mają być wyświetlane do tej usługi, a ta usługa ma wykonać kroki tego przepływu pracy i rejestrowanie postępu. 

Jak można utworzyć tej skali określonej usługi? Usługa może być wielodostępne w pewnej formy i akceptować połączeń i zaczną działać jednocześnie poza kroki dla wielu różnych wystąpień tego samego przepływu pracy. Jednak który wprowadzić kod bardziej złożonej, ponieważ aktualnie martwić się o wiele różnych wystąpień tego samego przepływu pracy, wszystko na różnych etapach oraz od innych klientów. Ponadto obsługa wielu przepływów pracy w tym samym czasie nie rozwiąże problemu skali. Jest to spowodowane w pewnym momencie tej usługi zużywa zbyt wiele zasobów, aby pomieścić się na określonym komputerze. Wiele usług nie jest skompilowany dla tego wzorca w pierwszej kolejności również wystąpić problemy z powodu niektóre związanego z używaniem "wąskie gardło" lub spowolnienie ich kodu. Tego rodzaju problemy spowodować, że usługa nie będą działać również w przypadku pobiera większą liczbę jednoczesnych przepływów pracy, który go służy do śledzenia.  

Rozwiązaniem jest utworzenie wystąpienia tej usługi, dla każdego innego wystąpienia przepływu pracy, które mają być śledzone. To jest doskonałym wzorca i działa, czy usługa jest bezstanowych i stanowych. Ten wzorzec do pracy jest zazwyczaj innej usługi, która działa jako "Usługa Menedżera obciążenia". Zadania tej usługi jest do odbierania żądań i kierować te żądania do innych usług. Menedżer można dynamicznie utworzyć wystąpienia usługi Obciążenie, gdy odbierze komunikat, a następnie przekazać w odpowiedzi na żądania do tych usług. Usługa Menedżera również otrzymać wywołania zwrotne usługi danego przepływu pracy wykonuje swojego zadania. Odebrania Menedżera tych wywołań zwrotnych go można usunąć to wystąpienie usługi przepływu pracy, lub pozostaw Jeśli oczekiwano kolejnych wywołań. 

Zaawansowane wersje tego typu manager można również utworzyć pule usług, którymi zarządza. Puli gwarantuje, że gdy nowe żądanie nie ma czekać, aż usługi. Zamiast tego menedżera można po prostu wybierz usługi przepływu pracy, który nie jest aktualnie zajęty z puli, lub losowo trasy. Utrzymywanie puli usług dostępnych przyspiesza nowych żądań obsługi, ponieważ jest mniej prawdopodobne, że żądanie ma czekać na nową usługę do przejścia można. Tworzenie nowych usług szybki, ale nie jest wolnego lub natychmiastowe. Pula minimalizuje ilość czasu, żądanie musi odczekać są obsługiwane. Często zobaczysz ten menedżer i wzorzec puli gdy czas reakcji niezależnie od tego, najczęściej. Kolejkowanie żądania i tworzenie usługi w tle i _następnie_ przekazanie jej jest również wzorzec popularnych Menedżera jako jest tworzenie i usuwanie usług w oparciu o niektórych śledzenie ilość pracy usługi aktualnie ma oczekujące. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Skalowanie przez tworzenia lub usuwania nowych nazwane wystąpienia aplikacji
Tworzenie i usuwanie instancji całej aplikacji jest podobny do wzorca tworzenia i usuwania usług. Ten wzorzec jest niektóre usługi Menedżera, która jest podejmowania decyzji na podstawie żądań, które go ma do czynienia i informacje, że odbiera z innymi usługami wewnątrz klastra. 

Podczas tworzenia nowego wystąpienia nazwanego aplikacji stosuje się zamiast tworzyć nowe wystąpienia usługi o nazwie w istniejącej aplikacji? Istnieje kilka przykładów:

  * Nowe wystąpienie aplikacji jest dla danego klienta, którego kod musi być uruchamiane w ramach niektórych określonej tożsamości lub ustawienia zabezpieczeń.
    * Sieć szkieletowa usług umożliwia definiowanie pakietów inny kod do uruchamiania w określonej tożsamości. Aby uruchomić tego samego pakietu kodu w ramach tożsamości innego, aktywacji przez muszą zostać wykonane w innej aplikacji wystąpień. Rozważmy, gdzie masz istniejący klient obciążeń wdrożonych. Te może być uruchomiony w ramach określonej tożsamości, dzięki czemu może monitorować i kontrolowanie dostępu do innych zasobów, takie jak zdalne bazy danych lub innych systemów. W takim przypadku po zarejestrowaniu nowego klienta, prawdopodobnie nie chcesz aktywować swój kod w tym samym kontekście (proces miejsca). Gdy użytkownik może utrudnia dla kodu usługi do działania w ramach określonej tożsamości. Zazwyczaj należy więcej zabezpieczeń, izolacji i kod zarządzania tożsamościami. Zamiast różnych nazwane wystąpienia usługi w ramach tego samego wystąpienia aplikacji i dlatego tego samego procesu miejsca, można użyć różnych nazwane wystąpienia aplikacji sieci szkieletowej usług. Ułatwia określenie tożsamości różnych kontekstach.
  * Nowe wystąpienie aplikacji służy również jako środek konfiguracji
    * Domyślnie wszystkich wystąpień usługi o nazwie typu określonej usługi, w ramach wystąpienia aplikacji zostanie uruchomiony w ten sam proces danego węzła. Co to oznacza to, że podczas każdego wystąpienia usługi można skonfigurować inaczej, jest to skomplikowane. Usługi muszą mieć niektóre token, używanych do wyszukiwania ich konfiguracji w ramach pakietu konfiguracji. Zazwyczaj jest to tylko nazwę usługi. To działa prawidłowo, ale jego couples konfiguracji do nazw wystąpień poszczególnych usługi o nazwie w ramach danego wystąpienia aplikacji. Może to być skomplikowane i trudne do zarządzania, ponieważ konfiguracja jest zwykle artefaktem czasu projektowania, z określonymi wartościami wystąpienie aplikacji. Utworzenie większej liczby usług zawsze powoduje więcej uaktualnień aplikacji w celu zmiany informacji zawartych w pakietach konfiguracji lub wdrożyć nowe, tak aby nowych usług można wyszukiwać ich określone informacje. Często łatwiej jest utworzyć całkiem nowe wystąpienie aplikacji o nazwie. Następnie można parametry aplikacji ustawić niezależnie od konfiguracji jest niezbędne dla usług. W ten sposób wszystkich usług, które są tworzone w ramach których nazwane wystąpienie aplikacji może dziedziczyć ustawień określonej konfiguracji. Na przykład zamiast pojedynczym pliku konfiguracji z ustawień i dostosowań dla każdego klienta, takich jak tajemnice lub na ograniczenia zasobów klienta, zamiast tego trzeba inną aplikację dla poszczególnych klientów przy użyciu tych ustawień przesłonięcia. 
  * Nowa aplikacja służy jako granica uaktualnienia
    * W ramach usługi Service Fabric inną aplikację nazwanego wystąpienia służyć jako granic w celu uaktualnienia. Uaktualnienie jednego wystąpienia nazwanego aplikacji nie ma wpływu na kod, który jest uruchomione inne wystąpienie nazwane aplikacji. Różnych aplikacji zakończą się z różnymi wersjami tego samego kodu na tych samych węzłów. Może to być czynnikiem, gdy trzeba podjąć decyzję skalowania, ponieważ można wybrać, czy nowy kod należy stosować tej samej uaktualnień jako inną usługę lub nie. Załóżmy, że wywołanie dociera usługę menedżera, która jest odpowiedzialny za skalowania obciążeń określonego klientów przez tworzenie i usuwanie usług dynamicznie. W takim przypadku jednak wywołanie jest dla obciążenia związane z _nowe_ klienta. Większość klientów tak, jak są odizolowane od siebie nie tylko z powodów bezpieczeństwa i konfiguracji wymienionymi wcześniej, ale ponieważ zapewnia większą elastyczność pod względem określonych wersji oprogramowania i wybierania, gdy zostać uaktualniony. Może także Utwórz nowe wystąpienie aplikacji oraz tworzenie usługi po prostu do dalszego partycji ilość Twojej usługi, które będzie touch żadnych jedno uaktualnienie. Aplikacji osobnych wystąpień zapewniają większy poziom szczegółowości podczas wykonywania uaktualnienia aplikacji, a także włączyć A / B wdrożeń testowych i niebieski/zielony. 
  * Istniejące wystąpienie aplikacji jest pełny
    * W sieci szkieletowej usług [aplikacji, zdolność](service-fabric-cluster-resource-manager-application-groups.md) to pojęcie można użyć do kontrolowania ilości zasobów dostępnych dla wystąpień określonej aplikacji. Na przykład może zdecydować, że dana usługa musi mieć innego wystąpienia utworzone w celu skalowania. Jednak to wystąpienie aplikacji jest poza pojemność dla niektórych metryki. Jeśli tego konkretnego klienta lub obciążenie może nadal być przyznany więcej zasobów, następnie należy można zwiększyć pojemność istniejących dla tej aplikacji albo utwórz nową aplikację. 

## <a name="scaling-at-the-partition-level"></a>Skalowanie w poziomie partycji
Sieć szkieletowa usług obsługuje partycjonowanie. Partycjonowanie dzieli usługi na kilku sekcji logicznej i fizycznej, z których każdy działa niezależnie. To jest przydatne w przypadku usługi stanowej, ponieważ nikt zestawu replik musi obsługiwać wszystkie wywołania i manipulowania wszystkie stanu na raz. [Partycjonowania omówienie](service-fabric-concepts-partitioning.md) zawiera informacje na temat typów partycjonowania systemów, które są obsługiwane. Replik każdej partycji są rozkładane między węzłami w klastrze dystrybucji obciążenia tej usługi i zapewnienie, że żadna usługa jako całość lub dowolnej partycji ma pojedynczy punkt awarii. 

Należy wziąć pod uwagę usługę korzystającą z ranged schemat partycjonowania niska wartość klucza o wartości 0, wysoka wartość klucza 99 i liczba partycji, 4. W trzech węzłów klastra usługi może być rozmieszczona z czterech repliki, które mają zasoby w każdym węźle, jak pokazano poniżej:

<center>
![Układ partycji z trzech węzłów](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Zwiększenie liczby węzłów sieci szkieletowej usług przeniesie niektórych istniejących replik istnieje. Na przykład załóżmy powiedz liczba wzrasta węzłów do czterech i replik uzyskać redystrybucji. Obecnie usługa ma teraz replik trzy uruchomione na każdym węźle każdego należących do różnych partycji. Umożliwia to lepsze wykorzystanie zasobów, ponieważ nowy węzeł nie jest zimnych. Zazwyczaj również zwiększa wydajność, ponieważ każda usługa ma więcej dostępnych zasobów.

<center>
![Układ partycji z czterech węzłów](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Skalowanie za pomocą Menedżera zasobów klastra sieci szkieletowej usług i metryki
[Metryki](service-fabric-cluster-resource-manager-metrics.md) są jak usługi express ich wykorzystania zasobów sieci szkieletowej usług. Przy użyciu metryk daje możliwość reorganizować i zoptymalizować układ klastra Menedżera zasobów klastra. Przykładowo może istnieć wiele zasobów w klastrze, ale nie może zostać przydzielony do usług, które są aktualnie podczas pracy. Przy użyciu metryk umożliwia Menedżera zasobów klastra, aby uporządkować klaster, aby upewnić się, że usługi mają dostęp do dostępnych zasobów. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Skalowanie przez dodawanie i usuwanie węzłów z klastra 
Inną opcją w przypadku skalowania z sieci szkieletowej usług jest zmiana rozmiaru klastra. Zmiana rozmiaru klastra oznacza dodawania lub usuwania węzłów z co najmniej jednego z typów węzłów w klastrze. Rozważmy na przykład przypadek, gdzie są wszystkie węzły w klastrze dynamicznej. Oznacza to, czy zasoby klastra są prawie wszystkie używane. W przypadku dodawania więcej węzłów do klastra jest najlepszym sposobem skalowania. Po nowe węzły dołączą do klastra Menedżera zasobów klastra sieci szkieletowej usług przenosi usług, co mniej całkowita obciążenie istniejących węzłów. Dla usług bezstanowych z liczbą wystąpień = -1, usługa więcej wystąpień są tworzone automatycznie. Dzięki temu niektóre połączenia przenieść z istniejących węzłów do nowych węzłów. 

Aby uzyskać więcej informacji, zobacz [skalowanie klastra](service-fabric-cluster-scaling.md).

## <a name="putting-it-all-together"></a>Składanie wszystkiego razem
Rozwiąż wszystkie pomysły, które firma Microsoft zostały omówione w tym miejscu i komunikują się za pośrednictwem przykładem. Należy wziąć pod uwagę następujące usługi: próbujesz kompilacji usługi, który działa jako książkę adresową, zawierający nazwy i informacje kontaktowe. 

Prawo góry ma licznych zagadnień dotyczących skalowania: ilu użytkowników będą mieć? Ile kontaktów zapisze każdego użytkownika? Próby rysunek to wszystkie limit kiedy są stały się usługi po raz pierwszy jest trudne. Załóżmy, że ma przejść z jedną usługą statycznych wraz z liczbą określonej partycji. Konsekwencje pobrania liczba partycji niewłaściwy może spowodować później mają problemy z skali. Podobnie nawet jeśli pobranie wszystkich informacji prawo licznik, nie może być konieczne. Na przykład masz również zdecydować rozmiar klastra na początku w zakresie liczby węzłów i ich rozmiary. Jest to zwykle trudna do przewidzenia liczby zasobów, usługa ma używać swojego okresu istnienia. Można go również wiadomo wcześniejsze wzorzec ruchu, który faktycznie widzi usługi. Na przykład osób może dodawać i usuwać kontakty ich w tylko po pierwsze w nocy lub może on jest równomiernie w ciągu dnia. Na podstawie tych potrzebnych do skalowania i dynamicznie. Może być nauczysz prognozowania, gdy użytkownik chce musi przebiegać proces skalowania Wyloguj się i zaloguj, ale w obu przypadkach prawdopodobnie będzie konieczne reagować na zmieniające się zużycia zasobów przez usługę. Może to obejmować zmieniania rozmiaru klastra w celu zapewnienia większej ilości zasobów podczas reorganizacja korzystanie z istniejących zasobów nie jest wystarczająca. 

Ale Dlaczego nawet próby wybrania schemat jednej partycji dla wszystkich użytkowników? Dlaczego ograniczać się do jednej usługi i jeden klaster statycznych? Rzeczywisty stan jest zazwyczaj bardziej dynamiczne. 

Podczas budowania skali, należy wziąć pod uwagę następujące dynamiczne wzorca. Może być konieczne dostosowanie go do sytuacji:

1. Zamiast w trakcie wybierz schemat partycjonowania dla wszystkich użytkowników na początku kompilacji "manager service".
2. Zadania programu manager service jest przyjrzeć się informacje o kliencie rejestracji usługi. A następnie w zależności od tego, że informacje tworzenia wystąpienia usługi Menedżera sieci _rzeczywiste_ skontaktuj się z magazynu usługi _tylko dla tego klienta_. Jeśli wymagają określonej konfiguracji, izolacji lub uaktualnień, można również określić pokrętła wystąpienia aplikacji dla tego klienta. 

To tworzenie dynamicznych wzorca wiele korzyści:

  - Użytkownik nie próbuje odgadnąć liczby partycji poprawne dla wszystkich użytkowników na początku lub utworzyć pojedynczą usługą, która jest nieskończenie skalowalny na jego własnej. 
  - Różni użytkownicy nie muszą mieć tej samej liczby partycji, liczby replik, umieszczania ograniczeń, metryki, domyślne obciążeń, nazw usług, ustawienia dns lub innych właściwości określonego na poziomie aplikacji lub usługi. 
  - Możesz uzyskać segmentacji dodatkowe dane. Każdy klient ma swoje własne kopie usługi
    - Można konfigurować inaczej i przyznane więcej lub mniej zasobów, przy więcej lub mniej partycji i replik w razie potrzeby, w oparciu o ich oczekiwana Skala każdej usługi klienta.
      - Na przykład powiedzieć klienta płatnej dla warstwy "Złota" - ich można pobrać więcej repliki lub większa liczba partycji i potencjalnie zasobów dedykowanych do usług za pośrednictwem możliwości metryki i aplikacji.
      - Lub że one pod warunkiem, że informacje o wskazującą liczbę kontaktów zachodzi potrzeba były "Mała" — jak tylko kilka partycje lub nawet mogą być wprowadzane w puli usług udostępnionych z innymi klientami.
  - Nie używasz licznych wystąpienie usługi lub replik w czasie oczekiwania dla klientów wyświetlani
  - Jeśli kiedykolwiek wyjdzie klienta, następnie usuwania ich informacji z usługi jest tak proste, jak o manager Usuń tę usługę lub aplikację, utworzony.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji dotyczących pojęć sieci szkieletowej usług zobacz następujące artykuły:

* [Dostępność usług sieci szkieletowej usług](service-fabric-availability-services.md)
* [Partycjonowanie usług sieci szkieletowej usług](service-fabric-concepts-partitioning.md)
