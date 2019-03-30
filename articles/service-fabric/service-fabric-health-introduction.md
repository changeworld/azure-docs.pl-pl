---
title: Monitorowanie kondycji w usłudze Service Fabric | Dokumentacja firmy Microsoft
description: Wprowadzenie do monitorowania modelu, który oferuje funkcje monitorowania klastra i jego aplikacji i usług kondycji usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: 1d979210-b1eb-4022-be24-799fd9d8e003
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d0ef9f34d6b657a063e50b0f144197c41905e809
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667454"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Wprowadzenie do monitorowania kondycji usługi Service Fabric
Usługa Azure Service Fabric wprowadza modelu kondycji, który umożliwia ocenę kondycji funkcjonalnej, elastyczny i rozszerzalny i raportowania. Model umożliwia monitorowanie stanu klastra i usługi działające w niej niemal w czasie rzeczywistym. Można łatwo uzyskać informacje o kondycji i rozwiązać potencjalne problemy zanim będą oni kaskadowo i spowodować ogromne awarii. W typowym modelu services wysłać raporty oparte na ich lokalnych widokach i że informacje mają charakter zapewnienie ogólną klastra na poziomie widoku.

Składniki usługi Service Fabric umożliwia ten model kondycji sformatowanego raportu wraz z bieżącym stanem. Umożliwia ten sam mechanizm kondycji raportu z poziomu aplikacji. Użytkownik inwestujący w raportowania stanu wysokiej jakości, która przechwytuje swoje warunki niestandardowe, można wykryć i znacznie łatwiejsze Rozwiązywanie problemów dla uruchomionej aplikacji.

> [!NOTE]
> Rozpoczęliśmy podsystemu kondycji spełnić potrzeby monitorowanych uaktualnień. Usługa Service Fabric udostępnia monitorowanych uaktualnień aplikacji i klastra, które zapewniają pełną dostępność, bez przestojów i minimalnym nie interwencji użytkownika. Aby osiągnąć te cele, uaktualnienie sprawdza, czy kondycji na podstawie skonfigurowanych zasad uaktualniania. Można kontynuować uaktualniania, tylko wtedy, gdy kondycja szanuje odpowiednie progi. W przeciwnym razie uaktualnienie jest automatycznie wycofane lub wstrzymana umożliwiają administratorom szansy do rozwiązywania problemów. Aby dowiedzieć się więcej na temat uaktualniania aplikacji, zobacz [w tym artykule](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Magazynu kondycji
Magazynu kondycji przechowuje związane ze zdrowiem informacje na temat jednostek w klastrze na potrzeby łatwego pobierania i oceny. Zaimplementowano zgodnie z usługi Service Fabric utrwalone stanowej usługi w celu zapewnienia wysokiej dostępności i skalowalności. Sklep kondycji jest częścią **Service fabric: / System** aplikacji która jest dostępna, gdy klaster działa i działa.

## <a name="health-entities-and-hierarchy"></a>Kondycja jednostki i hierarchii
Jednostki kondycji są zorganizowane w logicznej hierarchii, która przechwytuje interakcje i zależności między różnymi jednostkami. Magazynu kondycji automatycznie tworzy jednostki kondycji i hierarchii, na podstawie otrzymanych z usługi Service Fabric składników raportów.

Jednostki kondycji dublowanie jednostkami usługi Service Fabric. (Na przykład **kondycji aplikacji jednostki** dopasowuje wystąpienia aplikacji wdrożonych w klastrze, podczas gdy **kondycji węzła jednostki** pasuje do węzła klastra usługi Service Fabric.) Hierarchia kondycji przechwytuje interakcje jednostek systemowych i stanowi on podstawę dla oceny kondycji zaawansowane. Informacje na temat podstawowych pojęć usługi Service Fabric w [omówienie techniczne usługi Service Fabric](service-fabric-technical-overview.md). Aby uzyskać więcej informacji o aplikacji, zobacz [modelu aplikacji usługi Service Fabric](service-fabric-application-model.md).

Kondycja jednostki i hierarchii umożliwiają klastra i aplikacji, które skutecznie zgłoszone, debugować, i monitorowane. Model kondycji zapewnia dokładny *szczegółową* reprezentacja kondycji wielu ruchomych części w klastrze.

![Kondycja jednostki.][1]
Jednostki kondycji są zorganizowane w hierarchii, w oparciu o relacji nadrzędny podrzędny.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Jednostki kondycji są:

* **Klaster**. Reprezentuje kondycji klastra usługi Service Fabric. Raportów o kondycji klastra opis warunków, które wpływają na cały klaster. Te warunki mają wpływ na wiele jednostek w klastrze lub samego klastra. Na podstawie warunku, zgłaszającą nie można ograniczyć wydania do jednego lub więcej podrzędnych złej kondycji. Przykłady obejmują mózg klastra, dzielenia się z powodu problemów z partycjonowania lub komunikacji sieciowej.
* **Węzeł**. Przedstawia kondycję węzła usługi Service Fabric. Raportów o kondycji węzła opis warunków, które wpływają na funkcje węzłów. Zazwyczaj wpływają na wszystkie wdrożone jednostki uruchomić na nim. Przykłady obejmują węzeł mało miejsca na dysku (lub innych właściwości dla komputera, takich jak pamięć, połączenia) i gdy węzeł nie działa. Jednostki węzła jest identyfikowane przez nazwę węzła (ciąg).
* **Aplikacja**. Przedstawia kondycję wystąpień aplikacji uruchomionych w klastrze. Raportów o kondycji aplikacji opisują warunki, które mają wpływ na ogólną kondycję aplikacji. One nie zawężona do poszczególnych elementów podrzędnych (usługi lub wdrożone aplikacje). Przykłady obejmują end-to-end interakcję między różnymi usługami w aplikacji. Jednostka aplikacji jest identyfikowane przez nazwę aplikacji (URI).
* **Usługa**. Reprezentuje kondycję usługi uruchomione w klastrze. Raporty dotyczące kondycji usługi opisują warunki, które mają wpływ na ogólną kondycję usługi. Zgłaszającą nie można zawęzić ten problem, aby partycja w złej kondycji lub replikę. Przykłady konfiguracji usługi (np. port lub udziału plików zewnętrznych), powodujące problemy dla wszystkich partycji. Jednostka usługi jest identyfikowane przez nazwę usługi (URI).
* **Partycja**. Przedstawia kondycję partycji usługi. Raportów o kondycji partycji opis warunków, które wpływają na całym zestawie replik. Do przykładów należą, gdy liczba replik jest poniżej docelowej liczby i gdy partycja jest utraciła kworum. Jednostka partycji jest identyfikowana przez identyfikator (GUID) partycji.
* **Replika**. Przedstawia kondycję repliki usługi stanowej lub wystąpienie usługi bezstanowej. Replika jest najmniejsza jednostka, która watchdogs i składników systemu można sporządzić raport na temat aplikacji. W przypadku usług stanowych przykładami replikę podstawową, która nie może replikować operacje pomocnicze bazy danych i wolna replikacja. Ponadto bezstanowe wystąpienia może zgłaszać zaczyna brakować zasobów, lub ma problemy z łącznością. Jednostki repliki jest identyfikowana przez identyfikator (GUID) partycji i replik lub wystąpień Identyfikatora (long).
* **DeployedApplication**. Reprezentuje kondycję *aplikacji uruchomionej w węźle*. Raportów o kondycji wdrożonej aplikacji opisuje warunki dotyczące tej aplikacji na węzeł, który nie może być zawężona pakietów usługi wdrożone na tym samym węźle. Przykłady obejmują błędy, gdy nie można pobrać pakietu aplikacji, w tym węźle i problemy dotyczące konfigurowania podmiotów zabezpieczeń aplikacji w węźle. Wdrożonej aplikacji jest identyfikowane przez nazwę aplikacji (URI) i nazwę węzła (ciąg).
* **DeployedServicePackage**. Przedstawia kondycję pakietu usług uruchomionych w węźle w klastrze. Opisuje warunki specyficzne dla pakietu usług, które nie ma wpływu na inne pakiety usługi w tym samym węźle, w tej samej aplikacji. Przykłady obejmują pakiet kodu w pakiecie usługi, której nie można uruchomić i pakiet konfiguracji, który nie może zostać odczytany. Wdrożony pakiet usługi jest identyfikowane przez nazwę aplikacji (URI), nazwę węzła (ciąg), nazwy manifestu usługi (ciąg) i usługi pakietu activation ID (ciąg).

Stopień szczegółowości modelu kondycji ułatwia wykrywanie i rozwiązać problemy. Na przykład jeśli usługa nie odpowiada, jest to możliwe do raportu, wystąpienie aplikacji jest w złej kondycji. Raportowanie na, poziom nie jest idealnym rozwiązaniem, jednak, ponieważ ten problem może nie mieć wpływ na wszystkie usługi w ramach tej aplikacji. Raport powinny być stosowane do usługi w złej kondycji lub partycji określonym podrzędnej, jeśli wskazuje więcej informacji o tej partycji. Dane automatycznie powierzchnie za pośrednictwem hierarchii, a partycja w złej kondycji stają się widoczne dla na poziomach usług i aplikacji. Ta agregacja ułatwia identyfikowanie oraz szybciej rozwiązać głównej przyczyny problemu.

Hierarchia health składa się z relacjami nadrzędny podrzędny. Klaster składa się z węzłów i aplikacji. Aplikacje usługi i wdrożonych aplikacji. Wdrożone aplikacje wdrożonych pakietów usługi. Usługi są partycje, a każda partycja zawiera co najmniej jedną replikę. Ma specjalne relacji między węzłami i wdrożone jednostek. Węzeł w złej kondycji zgłoszonej przez jego składnikiem systemu urzędu usługi Menedżer trybu Failover ma wpływ na wdrożone aplikacje, pakiety usługi i replikami wdrożonymi na nim.

Hierarchia kondycji reprezentuje najnowszy stan systemu, w oparciu o najnowszych raportów kondycji, który jest prawie w czasie rzeczywistym informacje.
W tych samych jednostek na podstawie logikę specyficzną dla aplikacji lub niestandardowe warunki monitorowanych zgłosić watchdogs wewnętrznych i zewnętrznych. Raporty użytkownika współistnieć z raportami systemu.

Należy zaplanować i inwestowania w sposób tworzenie raportów i reagować na kondycji podczas projektowania usługi w chmurze dużych. Ta początkowych inwestycji sprawia, że usługa jest łatwiejszy do debugowania, monitorowanie i obsługiwanie.

## <a name="health-states"></a>Stany kondycji
Usługa Service Fabric używa trzech stanów kondycji do opisywania, czy jednostka jest dobrej kondycji: OK, ostrzeżeń i błędów. Każdy raport wysyłany do magazynu kondycji należy określić jedną z tych stanów. Wynik oceny kondycji jest jednym z tych stanów.

Możliwe [stanów kondycji](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) są:

* **OK**. Jednostka jest w dobrej kondycji. Nie istnieją żadne znane problemy zgłoszone on lub jego elementy podrzędne (jeśli ma zastosowanie).
* **Ostrzeżenie**. Jednostka ma kilka problemów, ale mogą nadal działać prawidłowo. Na przykład występują opóźnienia, ale nie powodują problemów funkcjonalnych jeszcze. W niektórych przypadkach stan ostrzeżenia może rozwiązać się bez interwencji zewnętrznych. W takich przypadkach raportów o kondycji zwiększyć świadomość i zapewnić widoczność, co się dzieje. W innych przypadkach stan ostrzeżenia mogą obniżyć poważny problem, bez interwencji użytkownika.
* **Błąd**. Jednostka jest w złej kondycji. Działania powinny zostać podjęte w celu ustalenia stanu jednostki, ponieważ nie będzie działać poprawnie.
* **Nieznany**. Jednostka nie istnieje w magazynie kondycji. Ten wynik można uzyskać z zapytań rozproszonych, które scalania wyników z wielu składników. Na przykład zapytania o listę węzłów get przechodzi do **FailoverManager**, **ClusterManager**, i **HealthManager**; pobieranie aplikacji w zapytaniu o listę przechodzi do  **ClusterManager** i **HealthManager**. Te zapytania scalania wyników z wielu składników systemu. Jeśli inny składnik systemu zwraca jednostki, która nie znajduje się w magazynie danych kondycji, scalonego wyniku ma nieznany stan kondycji. Jednostka nie jest w magazynie, ponieważ nie zostały jeszcze przetworzone Raporty kondycji lub jednostki zostały wyczyszczone po usunięciu.

## <a name="health-policies"></a>Zasady dotyczące kondycji
Magazynu kondycji stosuje zasady dotyczące kondycji, aby ustalić, czy jednostka jest w dobrej kondycji na podstawie jego raporty i jego elementów podrzędnych.

> [!NOTE]
> Zasady dotyczące kondycji można określić w manifeście klastra (w przypadku oceny kondycji klastra i węzła) lub w manifeście aplikacji (w przypadku oceny aplikacji i wszystkich jego elementów podrzędnych). Żądania oceny kondycji można również przekazać w zasadach oceny kondycji niestandardowych, które są używane tylko w przypadku tej oceny.
> 
> 

Domyślnie Usługa Service Fabric stosuje rygorystyczne zasady (wszystko, co musi być dobrej kondycji) hierarchicznych relacji nadrzędny podrzędny. Jeśli jeszcze jeden element podrzędny ma jedno zdarzenie w złej kondycji, element nadrzędny jest uznawana za złą.

### <a name="cluster-health-policy"></a>Zasady dotyczące kondycji klastra
[Klastra zasad dotyczących kondycji](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) jest używany do oceny kondycji klastra i stany kondycji węzłów. Zasady można zdefiniować w manifeście klastra. Jeśli nie jest obecny, domyślne zasady (zero tolerowana awarii) jest używany.
Zawiera zasad dotyczących kondycji klastra:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Określa, czy traktować ostrzeżenia kondycji zgłasza jako błędy podczas oceny kondycji. Domyślnie: false.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Określa maksymalny udział procentowy tolerowana aplikacji, które mogą być złej kondycji, zanim klaster zostanie uznane za błąd.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Określa maksymalny udział procentowy tolerowana węzłów, które mogą być złej kondycji, zanim klaster zostanie uznane za błąd. W dużych klastrach niektóre węzły są zawsze w dół lub w poziomie do naprawy, dlatego ta wartość procentowa, należy skonfigurować tolerowana liczba, która.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Mapa zasad kondycji typ aplikacji może służyć podczas oceny kondycji klastra do opisania typy specjalne aplikacji. Domyślnie wszystkie aplikacje są umieszczane w puli i oceniać za pomocą MaxPercentUnhealthyApplications. W przypadku niektórych typów aplikacji powinny być traktowane inaczej, może zostać pobrany z globalnej puli. Zamiast tego są obliczane względem wartości procentowych skojarzonych z ich nazwa typu aplikacji na mapie. Na przykład w klastrze istnieją tysięcy aplikacji w różnych typów i kilka wystąpień aplikacji kontrolki typu specjalnej aplikacji. Nigdy nie należy kontroli aplikacji w wyniku błędu. Można określić globalne MaxPercentUnhealthyApplications do 20% tolerowana liczba określonych błędów, ale dla typu aplikacji, który "ControlApplicationType" Ustaw MaxPercentUnhealthyApplications na 0. Dzięki temu w przypadku niektórych wiele aplikacji jest w złej kondycji, ale poniżej globalnego Zła wartość procentowa oceniono klastra na ostrzeżenie. Wskazuje ostrzegawczy stan kondycji nie ma wpływu na uaktualniania klastra lub innych monitorowania wyzwolone przez stanu kondycji błędu. Ale nawet kontroli aplikacji w wyniku błędu czyniłyby złej kondycji klastra, który wyzwala wycofywania lub wstrzymuje uaktualniania klastra, w zależności od konfiguracji uaktualnienia.
  Dla typów aplikacji, zdefiniowanych na mapie wszystkie wystąpienia aplikacji są wyjmowane z globalnej puli aplikacji. Są one obliczane na podstawie całkowitej liczby aplikacji typ aplikacji przy użyciu określonych MaxPercentUnhealthyApplications z mapy. Wszystkie pozostałe aplikacje pozostają w puli globalnej i są oceniane przy użyciu MaxPercentUnhealthyApplications.

Poniższy przykład jest fragmencie manifestu klastra. Aby zdefiniować wpisy w mapie typów aplikacji, przed nazwą parametru "ApplicationTypeMaxPercentUnhealthyApplications-", następuje nazwa typu aplikacji.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Zasady kondycji aplikacji
[Zasady kondycji aplikacji](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) w tym artykule opisano, jak ocena zdarzeń oraz Stany podrzędnych agregacji jest wykonywane dla aplikacji i ich elementy podrzędne. Można zdefiniować w manifeście aplikacji **ApplicationManifest.xml**, w pakiecie aplikacji. Jeśli nie określono żadnych zasad, Usługa Service Fabric założono, że jednostka jest w złej kondycji, jeśli ma on raport o kondycji lub element podrzędny na stan kondycji ostrzeżenia lub błędu.
Można skonfigurować zasady są:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Określa, czy traktować ostrzeżenia kondycji zgłasza jako błędy podczas oceny kondycji. Domyślnie: false.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Określa maksymalną wartość procentową tolerowana wdrożone aplikacje, które mogą być złej kondycji, zanim aplikacja zostanie uznane za błąd. Ta wartość procentowa jest obliczana na podstawie dzielenia liczby wdrożone aplikacje w złej kondycji przez liczbę węzłów, które aplikacje są aktualnie wdrożonych w klastrze. Obliczenia zaokrągla w górę do tolerowanie awarii jednego w małej liczby węzłów. Domyślna wartość procentowa: zero.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Określa domyślne usługi typu zasad dotyczących kondycji, która zastępuje domyślne zasady kondycji w przypadku wszystkich typów usług w aplikacji.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Zawiera mapę zasady dotyczące kondycji usługi dla typów usług. Te zasady zastępują domyślne zasady kondycji typ usługi dla każdego określonego typu. Na przykład jeśli aplikacja ma typ usługi bezstanowej bramy i typu usługi stanowej aparatu, należy można skonfigurować zasady dotyczące kondycji ich oceny inaczej. Po określeniu zasad dla typów usług, możesz uzyskać większą kontrolę nad kondycję usługi.

### <a name="service-type-health-policy"></a>Zasady kondycji typ usługi
[Typ zasad dotyczących kondycji usługi](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) określa sposób ocenić i agregacji, usług i elementy podrzędne usługi. Zasady zawierają:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Określa maksymalną wartość procentową tolerowana partycje w złej kondycji, zanim usługa jest uznawana za złą. Domyślna wartość procentowa: zero.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Określa maksymalną wartość procentową tolerowana repliki w złej kondycji, zanim partycji jest określana jako zła. Domyślna wartość procentowa: zero.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Określa maksymalną wartość procentową tolerowana usługi w złej kondycji, zanim aplikacja jest uznawana za złą. Domyślna wartość procentowa: zero.

Poniższy przykład to fragment manifest aplikacji:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Ocenę kondycji
Użytkownicy i automatyczne usługi może służyć do oceny kondycji dla dowolnej jednostki w dowolnym momencie. Do oceny kondycji jednostki, zagregowanych danych z magazynu kondycji kondycji wszystkich raportów w jednostce i ocenia wszystkie jego elementy podrzędne (jeśli ma zastosowanie). Algorytm agregacji kondycji używa zasady dotyczące kondycji, które określają sposób oceny raportów o kondycji i zagregowane stany kondycji podrzędnych (jeśli ma zastosowanie).

### <a name="health-report-aggregation"></a>Agregacja raportów kondycji
Jednej jednostki może mieć wiele raportów o kondycji wysyłane przez różne raporty (składników systemu lub watchdogs) na inne właściwości. Agregacja używa zasad skojarzonej kondycji, w szczególności ConsiderWarningAsError członkiem zasady kondycji aplikacji lub klastra. ConsiderWarningAsError Określa, jak ocenić ostrzeżenia.

Stan kondycji zagregowane jest wyzwalany przez *najgorszy* raportów kondycji w jednostce. W przypadku błędu co najmniej jeden raport o kondycji stanu kondycji zagregowane, występuje błąd.

![Agregacja raportów kondycji z raportu o błędach.][2]

Jednostki kondycji, która ma co najmniej jeden raport kondycji błąd zostanie ocenione jako błąd. Dotyczy to także raport o kondycji wygasłe, niezależnie od jej stan kondycji.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

W przypadku nie raportów o błędach i co najmniej jedno ostrzeżenie, zagregowanej kondycji jest ostrzeżenia lub błędu, w zależności od ConsiderWarningAsError flagi zasad.

![Agregacja raportu kondycji ostrzeżenie raporty i ConsiderWarningAsError false.][3]

Agregacja raportu kondycji z raportem ostrzeżenie i ConsiderWarningAsError ustawiony na wartość false (domyślnie).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregacja kondycji podrzędne
Stan kondycji zagregowanych podmiotu odzwierciedla stanów kondycji podrzędnych (jeśli ma zastosowanie). Algorytm do agregowania stanów kondycji podrzędnych używa zasad kondycji stosowane na podstawie typu jednostki.

![Agregacja kondycji jednostek podrzędnych.][4]

Agregacja podrzędnych na podstawie zasad kondycji.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Po magazynu kondycji oceniła wszystkie elementy podrzędne, agreguje ich stany kondycji oparte na skonfigurowanym maksymalnej wartości procentowej elementów podrzędnych w złej kondycji. Ta wartość procentowa jest pobierana z zasady na podstawie typu jednostki i podrzędnych.

* Jeśli wszystkie elementy podrzędne mają OK stany, podrzędne zagregowane stan kondycji jest OK.
* Jeśli elementy podrzędne mają OK i Stany ostrzeżenie podrzędnych zagregowane stan kondycji jest ostrzeżenie.
* Jeśli istnieją elementy podrzędne o stanów błędów, które nie respektują maksymalną dozwoloną wartość procentowa podrzędne o złej kondycji, stan kondycji zagregowane nadrzędnego, występuje błąd.
* Jeśli elementy podrzędne o stany błędu respektować dozwoloną maksymalną wartość procentową podrzędne o złej kondycji, zagregowane nadrzędnego stan kondycji jest ostrzeżenie.

## <a name="health-reporting"></a>Raportowanie stanu
Składniki systemu, aplikacji Service Fabric w systemie i wewnętrznej/zewnętrznej watchdogs może zgłaszać z jednostkami usługi Service Fabric. Raporty upewnij *lokalnego* oznaczeń kondycję monitorowanych jednostki, na podstawie warunków, ich monitorowanie. Nie należy przyjrzeć się stan globalny ani zagregowanych danych. Żądane zachowanie jest proste raporty, a nie złożonych organizmy, które należy przyjrzeć się wiele elementów wywnioskowania informacji do wysyłania.

Aby wysłać dane dotyczące kondycji w magazynie kondycji, reportera musi zidentyfikować dotyczy jednostki i utworzyć raport o kondycji. Aby wysłać raport, należy użyć [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) interfejsu API raportów kondycji interfejsów API narażonych na `Partition` lub `CodePackageActivationContext` obiektów, poleceń cmdlet programu PowerShell lub REST.

### <a name="health-reports"></a>Raportów o kondycji
[Raportów o kondycji](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) dla każdej jednostki w klastrze zawiera następujące informacje:

* **SourceId**. Ciąg, który unikatowo identyfikuje zgłaszającą zdarzenie kondycji.
* **Identyfikator jednostki**. Identyfikuje jednostkę, w której stosowana jest raport. Różni się zależnie od [typu jednostki](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Klaster. Brak.
  * Węzeł. Nazwa węzła (ciąg).
  * aplikacja. Nazwa aplikacji (URI). Reprezentuje nazwę wystąpienia aplikacji wdrożonych w klastrze.
  * Usługa. Nazwa usługi (URI). Reprezentuje nazwę wystąpienia usług wdrożonych w klastrze.
  * Partycja. Identyfikator (GUID). Reprezentuje unikatowy identyfikator partycji.
  * Replika. Identyfikator repliki usługi stanowej lub identyfikator wystąpienia usługi bezstanowej (INT64).
  * DeployedApplication. Nazwa aplikacji (URI) i nazwę węzła (ciąg).
  * DeployedServicePackage. Nazwa aplikacji (URI), nazwę węzła (ciąg) i service manifest nazwy (ciąg).
* **Właściwość**. A *ciąg* (nie wyliczania stały), która umożliwia zgłaszającą do kategoryzowania zdarzenie kondycji dla określonej właściwości jednostki. Na przykład reportera A mogą raportować kondycję Node01 właściwość "Magazyn" i reportera B mogą raportować kondycję Node01 właściwość "Łączność". W magazynie kondycji te raporty są traktowane jako zdarzenia dotyczące kondycji osobne jednostki Node01.
* **Opis elementu**. Ciąg, który umożliwia reportera zawiera szczegółowe informacje dotyczące zdarzenia kondycji. **SourceId**, **właściwość**, i **HealthState** pełni powinna opisywać raportu. Opis dodaje czytelny dla człowieka informacje na temat raportu. Tekst ułatwia dla administratorów i użytkowników zrozumieć raport o kondycji.
* **HealthState**. [Wyliczenie](service-fabric-health-introduction.md#health-states) opisujący stan kondycji raportu. Dopuszczalne wartości to OK, ostrzeżenia i błędu.
* **TimeToLive**. Przedział czasu, która wskazuje, ile raport o kondycji jest nieprawidłowy. Dzięki połączeniu z usługami **RemoveWhenExpired**, umożliwia ona magazynu kondycji, wiadomo, jak ocenić wygasłe zdarzenia. Domyślnie wartość jest nieograniczony, a raport jest prawidłowy w nieskończoność.
* **RemoveWhenExpired**. Wartość logiczna. Jeśli ustawiono wartość true, raport o kondycji wygasłe zostanie automatycznie usunięty z magazynu kondycji i raport nie ma wpływu na ocenę kondycji jednostki. Używane raportu jest prawidłowa w określonym przedziale czasu tylko wtedy, gdy zgłaszającą nie trzeba jawnie wyczyszczenie go. Również służy do usuwania raportów z magazynu kondycji (na przykład strażnika zostanie zmieniony i przestaje wysyłać raporty z poprzedniego źródła i właściwości). Może wysłać raport o krótkie TimeToLive wraz z RemoveWhenExpired w celu wyczyszczenia dowolnego poprzedniego stanu z magazynu kondycji. Jeśli wartość jest ustawiona na wartość false, wygasłych raportu jest traktowana jako błąd na ocenę kondycji. Wartość false sygnały w magazynie kondycji, czy źródło powinien wysyłać raporty okresowo dla tej właściwości. Jeśli nie, następnie musi istnieć problem ze strażnika. Kondycja strażnika są przechwytywane, biorąc pod uwagę zdarzeń jako błąd.
* **SequenceNumber**. Dodatnia liczba całkowita, która musi być coraz większej, reprezentuje kolejność raportów. W sklepie kondycji służy do wykrywania stare raportów, które są odbierane opóźnienia z powodu opóźnienia sieci lub innych kwestiach. Raport zostanie odrzucone, jeśli numer sekwencyjny jest mniejsza niż lub równe wykorzystanie ostatnio zastosowane numer dla tej samej jednostki, źródła i właściwości. Jeśli nie zostanie określony, numer sekwencji jest generowany automatycznie. Należy umieścić w numer sekwencji, tylko wtedy, gdy raportowanie przy zmianie stanu. W takiej sytuacji źródła musi zapamiętać raportów, których wysłanie go i zachowywanie informacji odzyskiwania w trybie failover.

Te cztery elementy informacji — SourceId, identyfikator jednostki, właściwości i HealthState — są wymagane dla każdego raportu o kondycji. Można uruchomić z prefiksem nie może być ciągiem SourceId "**systemu.**", który jest zarezerwowany dla raportów systemu. Dla tej samej jednostki jest tylko jeden raport dla tego samego źródła i właściwości. Wiele raportów dla tego samego źródła i właściwości przesłaniają się wzajemnie, kondycji po stronie klienta (jeśli są przetwarzane wsadowo) lub na kondycji przechowywane po stronie. Zastąpienie opiera się na numery sekwencyjne; nowsze raportów (o wyższych numerach) Zastąp starszych raportach.

### <a name="health-events"></a>Zdarzenia dotyczące kondycji
Wewnętrznie, utrzymuje magazynu kondycji [zdarzenia dotyczące kondycji](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), które zawierają wszystkie informacje z raportów i dodatkowe metadane. Metadane obejmują czas, który raport został przekazany klientowi kondycji i czas, który został zmodyfikowany po stronie serwera. Zdarzenia dotyczące kondycji, są zwracane przez [zapytania o kondycję](service-fabric-view-entities-aggregated-health.md#health-queries).

Dodano metadane zawierają:

* **SourceUtcTimestamp**. Czas raportu został przekazany klientowi kondycji (Coordinated Universal Time).
* **LastModifiedUtcTimestamp**. Czas ostatniej modyfikacji raport po stronie serwera (Coordinated Universal Time).
* **IsExpired**. Flaga wskazująca, czy raport wygasł, gdy zapytanie zostało wykonane przez Magazyn kondycji. Zdarzenia mogą wygasnąć, tylko wtedy, gdy RemoveWhenExpired ma wartość false. W przeciwnym razie zdarzenia nie są zwracane przez zapytanie i zostanie usunięty z magazynu.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Czas ostatniego przejścia OK/ostrzeżenia/błędu. Te pola zapewniają historię kondycji stanami dla zdarzenia.

Pola przejścia stanu może służyć do alerty pozwalają na sprawną lub informacji o zdarzeniu "historyczne" kondycji. Umożliwiają one scenariuszy takich jak:

* Zgłoś alert, gdy właściwość została na błąd/ostrzeżenie dla więcej niż X minut. Sprawdzanie warunku w okresie czasu pozwala uniknąć alertów na tymczasowe warunki. Na przykład można przetłumaczyć alert, gdy stan kondycji ma zostać ostrzeżenie dotyczące więcej niż pięć minut (HealthState == ostrzeżenie i LastWarningTransitionTime teraz - > 5 minut).
* Alert tylko w warunki, które zostały zmienione w ciągu ostatnich X minut. Jeśli raport został już przy błędzie przed upływem określonego terminu, można zignorowane, ponieważ został już zasygnalizowane wcześniej.
* Jeśli właściwość przełączania się między ostrzeżeniem a błędem, określić, jak długo był nieprawidłowy (czyli nie OK). Na przykład można przetłumaczyć alert, gdy właściwość nie było w złej kondycji przez więcej niż pięć minut (HealthState! = Ok, a teraz — LastOkTransitionTime > 5 minut).

## <a name="example-report-and-evaluate-application-health"></a>Przykład: Tworzenie raportów i oceny kondycji aplikacji
Poniższy przykład wysyła raport dotyczący kondycji za pomocą programu PowerShell aplikacji **Service fabric: / WordCount** ze źródła **MyWatchdog**. Raport o kondycji zawiera informacje o kondycji właściwości "availability" stanu kondycji błędu z TimeToLive nieskończone. Następnie wykonuje zapytanie kondycji aplikacji, co powoduje zwrócenie zagregowane błędy stan kondycji i zdarzenia kondycji zgłaszane na liście zdarzeń dotyczących kondycji.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Sposób użycia modelu kondycji
Model kondycji umożliwia usług w chmurze i podstawowej platformy usługi Service Fabric do skalowania, ponieważ oznaczeń monitorowania i kondycji są dystrybuowane między różnych monitorów w klastrze.
Inne systemy mają pojedyncza usługa scentralizowanego na poziomie klastra, która analizuje wszystkie *potencjalnie* przydatnych informacji wyemitowane przez usługi. To podejście hamuje ich skalowalność. On również nie zezwala na zbierać określone informacje ułatwiające identyfikację problemów i potencjalnych problemach, jak blisko głównej przyczyny problemu, jak to możliwe.

Model kondycji ciężko monitorowania i diagnostyki umożliwiające ocenę kondycji klastra i aplikacji i monitorowanych uaktualnień. Dane kondycji w innych usług są używane do wykonywania automatycznej naprawy, Historia kondycji klastra kompilacji i wydawania alerty pod pewnymi warunkami.

## <a name="next-steps"></a>Kolejne kroki
[Wyświetlanie raportów o kondycji usługi Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Użytek rozwiązywania problemów z raportami kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Jak raportować i sprawdzać kondycję usługi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Dodawanie niestandardowych raportów o kondycji usługi Service Fabric](service-fabric-report-health.md)

[Monitorować i diagnozować usługi lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uaktualnianie aplikacji usługi Service Fabric](service-fabric-application-upgrade.md)

