---
title: Monitorowanie kondycji w sieci szkieletowej usług
description: Wprowadzenie do modelu monitorowania kondycji usługi Azure Service Fabric, który zapewnia monitorowanie klastra i jego aplikacji i usług.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 473aa2b9a74193a857390cd3e29b2b559b6084d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282421"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Wprowadzenie do monitorowania kondycji usługi Service Fabric
Usługa Azure Service Fabric wprowadza model kondycji, który zapewnia bogatą, elastyczną i rozszerzalną ocenę kondycji i raportowanie. Model umożliwia monitorowanie w czasie zbliżonym do rzeczywistego stanu klastra i uruchomionych w nim usług. Można łatwo uzyskać informacje o stanie zdrowia i rozwiązać potencjalne problemy, zanim kaskadowo i spowodować ogromne awarie. W typowym modelu usługi wysyłają raporty na podstawie ich widoków lokalnych, a informacje są agregowane w celu zapewnienia ogólnego widoku na poziomie klastra.

Składniki sieci szkieletowej usług używają tego bogatego modelu kondycji do raportowania ich bieżącego stanu. Można użyć tego samego mechanizmu do raportowania kondycji z aplikacji. Jeśli zainwestujesz w wysokiej jakości raportowanie kondycji, które przechwytuje warunki niestandardowe, można wykryć i rozwiązać problemy dla uruchomionej aplikacji znacznie łatwiej.

> [!NOTE]
> Uruchomiliśmy podsystem kondycji, aby zaspokoić potrzebę monitorowania uaktualnień. Usługa Sieci szkieletowej zapewnia monitorowane uaktualnienia aplikacji i klastra, które zapewniają pełną dostępność, bez przestojów i minimalne do żadnej interwencji użytkownika. Aby osiągnąć te cele, uaktualnienie sprawdza kondycję na podstawie skonfigurowanych zasad uaktualniania. Uaktualnienie można kontynuować tylko wtedy, gdy kondycja respektuje żądane progi. W przeciwnym razie uaktualnienie zostanie automatycznie wycofane lub wstrzymane, aby dać administratorom szansę na rozwiązanie problemów. Aby dowiedzieć się więcej o uaktualnieniach aplikacji, zobacz [ten artykuł](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Sklep ze zdrowiem
Magazyn kondycji przechowuje informacje związane ze zdrowiem o jednostkach w klastrze w celu łatwego pobierania i oceny. Jest zaimplementowana jako usługa sieci szkieletowej usług utrwalonych stanowych, aby zapewnić wysoką dostępność i skalowalność. Magazyn kondycji jest częścią **sieci szkieletowej:/System** aplikacji i jest dostępny, gdy klaster jest uruchomiony.

## <a name="health-entities-and-hierarchy"></a>Jednostki kondycji i hierarchia
Jednostki kondycji są zorganizowane w hierarchii logicznej, która przechwytuje interakcje i zależności między różnymi jednostkami. Magazyn kondycji automatycznie tworzy jednostki kondycji i hierarchię na podstawie raportów otrzymanych ze składników sieci szkieletowej usług.

Jednostki kondycji dublowanie jednostek sieci szkieletowej usług. (Na przykład **jednostka aplikacji kondycji** pasuje do wystąpienia aplikacji wdrożonego w klastrze, podczas gdy jednostka **węzła kondycji** pasuje do węzła klastra sieci szkieletowej usług). Hierarchia kondycji przechwytuje interakcje jednostek systemu i jest podstawą zaawansowanej oceny kondycji. Informacje na temat kluczowych koncepcji sieci szkieletowej usług można dowiedzieć się [w omówienie techniczne sieci szkieletowej usług.](service-fabric-technical-overview.md) Aby uzyskać więcej informacji na temat aplikacji, zobacz [model aplikacji sieci szkieletowej usług](service-fabric-application-model.md).

Jednostki kondycji i hierarchii umożliwiają klastra i aplikacji, które mają być skutecznie zgłaszane, debugowane i monitorowane. Model kondycji zapewnia dokładną, *szczegółową* reprezentację kondycji wielu ruchomych elementów w klastrze.

![Jednostki kondycji.][1]
Encje kondycji, zorganizowane w hierarchii na podstawie relacji nadrzędny podrzędny.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Jednostki kondycji są:

* **Klastra**. Reprezentuje kondycję klastra sieci szkieletowej usług. Raporty kondycji klastra opisują warunki, które wpływają na cały klaster. Te warunki mają wpływ na wiele jednostek w klastrze lub samego klastra. Na podstawie warunku reporter nie może zawęzić problemu do jednego lub więcej niezdrowych dzieci. Przykłady obejmują mózg klastra podziału z powodu partycjonowania sieci lub problemy z komunikacją.
* **Węzeł**. Reprezentuje kondycję węzła sieci szkieletowej usług. Raporty kondycji węzła opisują warunki, które wpływają na funkcjonalność węzła. Zazwyczaj mają one wpływ na wszystkie wdrożone jednostki uruchomione na nim. Przykłady obejmują węzeł z miejsca na dysku (lub inne właściwości całego komputera, takie jak pamięć, połączenia) i gdy węzeł jest w dół. Jednostka węzła jest identyfikowana przez nazwę węzła (ciąg).
* **Zastosowanie**. Reprezentuje kondycję wystąpienia aplikacji uruchomionego w klastrze. Raporty dotyczące kondycji aplikacji opisują warunki, które wpływają na ogólną kondycję aplikacji. Nie można ich zawęzić do poszczególnych dziewiętnaszki (usługi lub wdrożone aplikacje). Przykłady obejmują interakcji end-to-end między różnymi usługami w aplikacji. Jednostka aplikacji jest identyfikowana przez nazwę aplikacji (URI).
* **serwis .** Reprezentuje kondycję usługi uruchomionej w klastrze. Raporty kondycji usługi opisują warunki, które wpływają na ogólną kondycję usługi. Reporter nie można zawęzić problem do złej partycji lub repliki. Przykłady obejmują konfigurację usługi (na przykład port lub zewnętrzny udział plików), która powoduje problemy dla wszystkich partycji. Jednostka usługi jest identyfikowana przez nazwę usługi (URI).
* **Partycja**. Reprezentuje kondycję partycji usługi. Raporty kondycji partycji opisują warunki, które wpływają na cały zestaw replik. Przykłady obejmują, gdy liczba replik jest poniżej liczby docelowej i gdy partycja jest w utracie kworum. Jednostka partycji jest identyfikowana przez identyfikator partycji (GUID).
* **Replika**. Reprezentuje kondycję repliki usługi stanowej lub wystąpienia usługi bezstanowej. Replika jest najmniejszą jednostką, którą watchdogs i składniki systemu mogą raportować dla aplikacji. W przypadku usług stanowych przykłady obejmują replikę podstawową, która nie może replikować operacji do pomocniczych i powolnej replikacji. Ponadto wystąpienie bezstanowe może zgłaszać, gdy kończy się zasoby lub ma problemy z łącznością. Jednostka repliki jest identyfikowana przez identyfikator partycji (GUID) i identyfikator repliki lub wystąpienia (długi).
* **Wdrożonaaplikacja**. Reprezentuje kondycję *aplikacji uruchomionej w węźle*. Wdrożone raporty kondycji aplikacji opisują warunki specyficzne dla aplikacji w węźle, których nie można zawęzić do pakietów usług wdrożonych w tym samym węźle. Przykłady obejmują błędy, gdy nie można pobrać pakietu aplikacji w tym węźle i problemy z konfigurowaniem podmiotów zabezpieczeń aplikacji w węźle. Wdrożona aplikacja jest identyfikowana przez nazwę aplikacji (URI) i nazwę węzła (ciąg).
* **Wdrożony pakiet usług**. Reprezentuje kondycję pakietu usługi uruchomionego w węźle w klastrze. Opisano w nim warunki specyficzne dla pakietu usług, które nie mają wpływu na inne pakiety usług w tym samym węźle dla tej samej aplikacji. Przykłady obejmują pakiet kodu w pakiecie usługi, który nie można uruchomić i pakiet konfiguracyjny, który nie może być odczytany. Wdrożony pakiet usług jest identyfikowany przez nazwę aplikacji (URI), nazwę węzła (ciąg), nazwę manifestu usługi (ciąg) i identyfikator aktywacji pakietu usługi (ciąg).

Ziarnistość modelu kondycji ułatwia wykrywanie i rozwiązywanie problemów. Na przykład jeśli usługa nie odpowiada, jest możliwe, aby zgłosić, że wystąpienie aplikacji jest w złej kondycji. Raportowanie na tym poziomie nie jest jednak idealne, ponieważ problem może nie dotyczyć wszystkich usług w ramach tej aplikacji. Raport powinien być stosowany do usługi w złej kondycji lub do określonej partycji podrzędnej, jeśli więcej informacji wskazuje na tę partycję. Dane są automatycznie powierzchniowe w hierarchii, a partycja w złej kondycji jest widoczna na poziomie usługi i aplikacji. Ta agregacja pomaga szybciej zidentyfikować i rozwiązać główną przyczynę problemu.

Hierarchia kondycji składa się z relacji nadrzędny podrzędny. Klaster składa się z węzłów i aplikacji. Aplikacje mają usługi i wdrożone aplikacje. Wdrożone aplikacje wdrożyły pakiety usług. Usługi mają partycje, a każda partycja ma jedną lub więcej replik. Istnieje specjalna relacja między węzłami i wdrożonymi jednostkami. Węzeł w złej kondycji zgłoszony przez składnik systemu uprawnień, usługę Menedżer trybu failover, wpływa na wdrożone aplikacje, pakiety usług i repliki wdrożone na nim.

Hierarchia kondycji reprezentuje najnowszy stan systemu na podstawie najnowszych raportów dotyczących kondycji, które są informacjami niemal w czasie rzeczywistym.
Wewnętrzne i zewnętrzne watchdogs można raportować na tych samych jednostek na podstawie logiki specyficzne dla aplikacji lub niestandardowych monitorowanych warunków. Raporty użytkowników współistnieją z raportami systemowym.

Zaplanuj inwestowanie w sposób raportowania i reagowania na zdrowie podczas projektowania dużej usługi w chmurze. Ta inwestycja z góry ułatwia debugowanie, monitorowanie i obsługę usługi.

## <a name="health-states"></a>Stany zdrowia
Usługa Sieci szkieletowej używa trzech stanów kondycji, aby opisać, czy jednostka jest w dobrej kondycji, czy nie: OK, ostrzeżenie i błąd. Każdy raport wysłany do magazynu kondycji należy określić jeden z tych stanów. Wynik oceny kondycji jest jednym z tych stanów.

Możliwe [stany zdrowia](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) to:

* **OK**. Jednostka jest w dobrej kondycji. Nie są znane żadne problemy zgłaszane na nim lub jego dzieci (w stosownych przypadkach).
* **Ostrzeżenie**. Jednostka ma pewne problemy, ale nadal może działać poprawnie. Na przykład występują opóźnienia, ale nie powodują one jeszcze żadnych problemów funkcjonalnych. W niektórych przypadkach warunek ostrzeżenia może naprawić się bez interwencji zewnętrznej. W takich przypadkach raporty zdrowotne zwiększają świadomość i zapewniają wgląd w to, co się dzieje. W innych przypadkach stan ostrzegawczy może ulec poważnej sytuacji bez interwencji użytkownika.
* **Błąd**. Jednostka jest w złej kondycji. Należy podjąć działania w celu ustalenia stanu jednostki, ponieważ nie może ona działać poprawnie.
* **Nieznany**. Jednostka nie istnieje w magazynie kondycji. Ten wynik można uzyskać z rozproszonych kwerend, które scalają wyniki z wielu składników. Na przykład kwerenda listy węzłów pobierz przechodzi do **programu FailoverManager**, **ClusterManager**i **HealthManager**; pobierz kwerendę listy aplikacji przechodzi do **ClusterManager** i **HealthManager**. Te kwerendy scalają wyniki z wielu składników systemu. Jeśli inny składnik systemu zwraca jednostkę, która nie jest obecna w magazynie kondycji, scalony wynik ma nieznany stan kondycji. Jednostka nie jest w magazynie, ponieważ raporty kondycji nie zostały jeszcze przetworzone lub jednostka została wyczyszczona po usunięciu.

## <a name="health-policies"></a>Polityka zdrowotna
Magazyn kondycji stosuje zasady kondycji, aby określić, czy jednostka jest w dobrej kondycji na podstawie swoich raportów i jej ków podrzędnych.

> [!NOTE]
> Zasady kondycji można określić w manifeście klastra (dla oceny kondycji klastra i węzła) lub w manifeście aplikacji (do oceny aplikacji i dowolnego z jego śr.). Żądania oceny kondycji można również przekazać w niestandardowych zasad oceny kondycji, które są używane tylko dla tej oceny.
> 
> 

Domyślnie sieci szkieletowej usług stosuje ścisłe reguły (wszystko musi być w dobrej kondycji) dla relacji hierarchicznej nadrzędny podrzędność. Jeśli nawet jeden z dziemia ma jedno zdarzenie w złej kondycji, rodzic jest uważany za niezdrowy.

### <a name="cluster-health-policy"></a>Zasady kondycji klastra
[Zasady kondycji klastra](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) służy do oceny stanu kondycji klastra i stanu kondycji węzła. Zasady można zdefiniować w manifeście klastra. Jeśli nie jest obecny, używana jest zasada domyślna (zero tolerowanych błędów).
Zasady kondycji klastra zawierają:

* [RozważWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Określa, czy raporty dotyczące kondycji ostrzeżenia mają być traktowane jako błędy podczas oceny kondycji. Wartość domyślna: false.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Określa maksymalny tolerowany procent aplikacji, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Określa maksymalny tolerowany procent węzłów, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. W dużych klastrach niektóre węzły są zawsze w dół lub obecnie do naprawy, więc ten procent powinien być skonfigurowany do tolerowania tego.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Mapa zasad kondycji typu aplikacji może służyć podczas oceny kondycji klastra do opisywania specjalnych typów aplikacji. Domyślnie wszystkie aplikacje są umieszczane w puli i oceniane za pomocą MaxPercentUnhealthyApplications. Jeśli niektóre typy aplikacji powinny być traktowane inaczej, mogą być wyjęte z puli globalnej. Zamiast tego są one oceniane względem wartości procentowych skojarzonych z ich nazwą typu aplikacji na mapie. Na przykład w klastrze istnieją tysiące aplikacji różnych typów i kilka wystąpień aplikacji kontroli specjalnego typu aplikacji. Aplikacje sterujące nigdy nie powinny być błędne. Można określić globalne MaxPercentUnhealthyApplications do 20%, aby tolerować niektóre błędy, ale dla typu aplikacji "ControlApplicationType" ustaw MaxPercentUnhealthyApplications na 0. W ten sposób, jeśli niektóre z wielu aplikacji są w złej kondycji, ale poniżej globalnego procentu złej kondycji, klaster zostanie oceniony na ostrzeżenie. Stan kondycji ostrzeżenia nie ma wpływu na uaktualnienie klastra lub inne monitorowanie wyzwalane przez stan kondycji błąd. Ale nawet jedna aplikacja sterująca w błąd spowoduje, że klaster w złej kondycji, który wyzwala wycofać lub wstrzymuje uaktualnienie klastra, w zależności od konfiguracji uaktualnienia.
  Dla typów aplikacji zdefiniowanych na mapie wszystkie wystąpienia aplikacji są pobierane z globalnej puli aplikacji. Są one oceniane na podstawie całkowitej liczby aplikacji typu aplikacji, przy użyciu określonych MaxPercentUnhealthyApplications z mapy. Wszystkie pozostałe aplikacje pozostają w puli globalnej i są oceniane za pomocą MaxPercentUnhealthyApplications.

Poniższy przykład jest fragment manifestu klastra. Aby zdefiniować wpisy na mapie typu aplikacji, przedrostek nazwę parametru z "ApplicationTypeMaxPercentUnhealthyApplications-", a następnie nazwę typu aplikacji.

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

### <a name="application-health-policy"></a>Zasady dotyczące kondycji aplikacji
[Zasady kondycji aplikacji](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) opisuje, jak ocena zdarzeń i agregacji stanów podrzędnych jest wykonywana dla aplikacji i ich dzieci. Można go zdefiniować w manifeście aplikacji **ApplicationManifest.xml**w pakiecie aplikacji. Jeśli nie określono żadnych zasad, sieci szkieletowej usług zakłada, że jednostka jest w złej kondycji, jeśli ma raport kondycji lub podrzędny w stanie zdrowia ostrzeżenia lub błędu.
Konfigurowalne zasady to:

* [RozważWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Określa, czy raporty dotyczące kondycji ostrzeżenia mają być traktowane jako błędy podczas oceny kondycji. Wartość domyślna: false.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Określa maksymalny tolerowany procent wdrożonych aplikacji, które mogą być w złej kondycji, zanim aplikacja zostanie uznana za błędną. Ta wartość procentowa jest obliczana przez podzielenie liczby wdrożonych aplikacji w złej kondycji na liczbę węzłów, na których aplikacje są obecnie wdrażane w klastrze. Obliczenia zaokrągla się tolerować jeden błąd na małej liczbie węzłów. Domyślna wartość procentowa: zero.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Określa domyślną zasadę kondycji typu usługi, która zastępuje domyślne zasady kondycji dla wszystkich typów usług w aplikacji.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Zawiera mapę zasad kondycji usługi dla typu usługi. Te zasady zastępują domyślne zasady kondycji typu usługi dla każdego określonego typu usługi. Na przykład jeśli aplikacja ma typ usługi bramy bezstanowej i typ usługi aparat stanowy, można skonfigurować zasady kondycji dla ich oceny inaczej. Po określeniu zasad dla typu usługi, można uzyskać bardziej szczegółową kontrolę kondycji usługi.

### <a name="service-type-health-policy"></a>Zasady kondycji typu usługi
[Zasady kondycji typu usługi](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) określa sposób oceny i agregacji usług i śmiga usług. Zasady zawierają:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Określa maksymalny tolerowany procent partycji w złej kondycji, zanim usługa zostanie uznana za złą w złą kondycję. Domyślna wartość procentowa: zero.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Określa maksymalny tolerowany procent replik w złej kondycji, zanim partycja zostanie uznana za złą w złej kondycji. Domyślna wartość procentowa: zero.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Określa maksymalny tolerowany procent usług w złej kondycji, zanim aplikacja zostanie uznana za złą w złej kondycji. Domyślna wartość procentowa: zero.

Poniższy przykład jest fragment manifestu aplikacji:

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

## <a name="health-evaluation"></a>Ocena stanu zdrowia
Użytkownicy i zautomatyzowane usługi mogą ocenić kondycję dla dowolnej jednostki w dowolnym momencie. Aby ocenić kondycję jednostki, magazyn kondycji agreguje wszystkie raporty kondycji jednostki i ocenia wszystkie jej wiązki owe (w stosownych przypadkach). Algorytm agregacji kondycji używa zasad kondycji, które określają, jak oceniać raporty kondycji i jak agregować stany kondycji dzieci (w stosownych przypadkach).

### <a name="health-report-aggregation"></a>Agregacja raportu o kondycji
Jedna jednostka może mieć wiele raportów kondycji wysyłanych przez różnych reporterów (składniki systemu lub watchdogs) na różnych właściwościach. Agregacja używa skojarzonych zasad kondycji, w szczególności ConsiderWarningAsError członka aplikacji lub zasad kondycji klastra. ConsiderWarningAsError określa sposób oceny ostrzeżeń.

Zagregowany stan kondycji jest wyzwalany przez *raporty najgorszego* stanu kondycji w jednostce. Jeśli istnieje co najmniej jeden raport o kondycji błędu, zagregowany stan kondycji jest błędem.

![Agregacja raportu kondycji z raportem o błędach.][2]

Jednostka kondycji, która ma jeden lub więcej raportów kondycji błędów jest oceniane jako błąd. To samo dotyczy wygasłego raportu kondycji, niezależnie od jego stanu kondycji.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Jeśli nie ma żadnych raportów o błędach i co najmniej jednego ostrzeżenia, zagregowany stan kondycji jest ostrzeżeniem lub błędem, w zależności od flagi zasad ConsiderWarningAsError.

![Agregacja raportu kondycji z raportem ostrzegawczym i ConsiderWarningAsError false.][3]

Agregacja raportu kondycji z raportem ostrzegawczym i ConsiderWarningAsError ustawiona na false (domyślnie).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregacja zdrowia dziecka
Zagregowany stan kondycji jednostki odzwierciedla stany kondycji dziecka (w stosownych przypadkach). Algorytm do agregowania stanów kondycji podrzędnych używa zasad kondycji mających zastosowanie na podstawie typu jednostki.

![Agregacja kondycji jednostek podrzędnych.][4]

Agregacja podrzędnych na podstawie zasad dotyczących kondycji.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Po magazyn zdrowia ocenił wszystkie dzieci, agreguje ich stany kondycji na podstawie skonfigurowanego maksymalnego odsetka niezdrowych obrażeń podrzędnych. Ta wartość procentowa jest pobierana z zasad na podstawie jednostki i typu podrzędnego.

* Jeśli wszystkie dzieci mają ok stany, podrzędny stan kondycji zagregowane jest OK.
* Jeśli dzieci mają zarówno OK i stany ostrzegawcze, podrzędny zagregowany stan kondycji jest ostrzeżenie.
* Jeśli istnieją dzieci z uszanami błędów, które nie są zgodne z maksymalnym dozwolonym procentem złej kondycji dzieczy, zagregowany nadrzędny stan kondycji jest błędem.
* Jeśli podrzędne stany błędów przestrzegają maksymalnego dozwolonego odsetka strojów strojów dzieszczonych, zagregowany nadrzędny stan kondycji jest ostrzeżeniem.

## <a name="health-reporting"></a>Raportowanie kondycji
Składniki systemu, aplikacje sieci szkieletowej systemu i wewnętrzne/zewnętrzne watchdogs mogą raportować dane dotyczące jednostek sieci szkieletowej usług. Reporterzy dokonują *lokalnych* ustaleń dotyczących stanu zdrowia monitorowanych jednostek, w oparciu o warunki, które monitorują. Nie muszą patrzeć na stan globalny lub dane zagregowane. Pożądane zachowanie jest mieć proste reporterów, a nie złożone organizmy, które muszą patrzeć na wiele rzeczy, aby wywnioskować, jakie informacje wysłać.

Aby wysłać dane dotyczące kondycji do magazynu kondycji, reporter musi zidentyfikować jednostkę, której dotyczy problem, i utworzyć raport kondycji. Aby wysłać raport, należy użyć interfejsu API [FabricClient.HealthClient.ReportHealth,](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) zgłoś interfejsy API kondycji udostępniane na `Partition` obiektach lub `CodePackageActivationContext` obiektach, poleceniach cmdlet programu PowerShell lub REST.

### <a name="health-reports"></a>Raporty o stanie zdrowia
[Raporty kondycji](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) dla każdej z jednostek w klastrze zawierają następujące informacje:

* **SourceId**. Ciąg, który jednoznacznie identyfikuje reporter zdarzenia kondycji.
* **Identyfikator jednostki**. Identyfikuje jednostkę, w której jest stosowany raport. Różni się w zależności od [typu jednostki:](service-fabric-health-introduction.md#health-entities-and-hierarchy)
  
  * Klastra. Brak.
  * Węzła. Nazwa węzła (ciąg).
  * Aplikacji. Nazwa aplikacji (URI). Reprezentuje nazwę wystąpienia aplikacji wdrożonego w klastrze.
  * Usługi. Nazwa usługi (URI). Reprezentuje nazwę wystąpienia usługi wdrożonego w klastrze.
  * Partycji. Identyfikator partycji (GUID). Reprezentuje unikatowy identyfikator partycji.
  * Repliki. Identyfikator repliki usługi stanowej lub identyfikator wystąpienia usługi bezstanowej (INT64).
  * Wdrożonaaplikacja. Nazwa aplikacji (URI) i nazwa węzła (ciąg).
  * Wdrożony pakiet usług. Nazwa aplikacji (URI), nazwa węzła (ciąg) i nazwa manifestu usługi (ciąg).
* **Właściwość**. *Ciąg* (nie stałe wyliczenie), który umożliwia reporterowi kategoryzowanie zdarzenia kondycji dla określonej właściwości jednostki. Na przykład reporter A może raportować kondycję właściwości Node01 "Magazyn", a reporter B może raportować kondycję właściwości Node01 "Łączność". W magazynie kondycji te raporty są traktowane jako oddzielne zdarzenia kondycji dla node01 jednostki.
* **Opis**. Ciąg, który umożliwia reporterowi dostarczenie szczegółowych informacji o zdarzeniu kondycji. **SourceId**, **Właściwość**i **HealthState** należy w pełni opisać raport. Opis dodaje informacje o raporcie czytelne dla człowieka. Tekst ułatwia administratorom i użytkownikom zrozumienie raportu o kondycji.
* **HealthState**. [Wyliczenie opisujące](service-fabric-health-introduction.md#health-states) stan kondycji raportu. Akceptowane wartości to OK, Ostrzeżenie i Błąd.
* **TimeToLive**. Czas, który wskazuje, jak długo raport kondycji jest prawidłowy. W połączeniu z **RemoveWhenExpired**, pozwala magazynowi zdrowia wiedzieć, jak ocenić wygasłe zdarzenia. Domyślnie wartość jest nieskończona, a raport jest ważny na zawsze.
* **UsuńWhenExpired**. Wartość logiczna. Jeśli ustawiona na true, wygasły raport kondycji jest automatycznie usuwany z magazynu kondycji, a raport nie ma wpływu na ocenę kondycji jednostki. Używane, gdy raport jest prawidłowy tylko przez określony czas, a reporter nie musi jawnie wyczyścić go. Służy również do usuwania raportów z magazynu kondycji (na przykład watchdog jest zmieniany i zatrzymuje wysyłanie raportów z poprzedniego źródła i właściwości). Można wysłać raport z krótkim TimeToLive wraz z RemoveWhenExpired, aby wyczyścić każdy poprzedni stan ze sklepu kondycji. Jeśli wartość jest ustawiona na false, wygasły raport jest traktowany jako błąd w ocenie kondycji. Wartość false sygnalizuje do magazynu kondycji, że źródło powinno okresowo raportować tę właściwość. Jeśli tak nie jest, to musi być coś nie tak z watchdog. Kondycja strażnika jest przechwytywany przez biorąc zdarzenie jako błąd.
* **SequenceNumber**. Dodatnia liczba całkowita, która musi być stale rosnąca, reprezentuje kolejność raportów. Jest on używany przez magazyn kondycji do wykrywania starych raportów, które są odbierane z opóźnieniem z powodu opóźnień w sieci lub innych problemów. Raport jest odrzucany, jeśli numer sekwencyjny jest mniejszy lub równy ostatnio zastosowanej liczbie dla tej samej jednostki, źródła i właściwości. Jeśli nie zostanie określony, numer sekwencyjny jest generowany automatycznie. Konieczne jest umieszczenie w numerze sekwencyjnym tylko podczas raportowania o przejściach stanu. W tej sytuacji źródło musi pamiętać, które raporty wysłał i zachować informacje do odzyskania w pracy awaryjnej.

Te cztery elementy informacji — — identyfikator jednostki, właściwość i stan zdrowia — są wymagane dla każdego raportu kondycji. Ciąg SourceId nie może rozpoczynać się od prefiksu **"System",** który jest zarezerwowany dla raportów systemowych. Dla tej samej jednostki istnieje tylko jeden raport dla tego samego źródła i właściwości. Wiele raportów dla tego samego źródła i właściwości zastępują się nawzajem, po stronie klienta kondycji (jeśli są wsadowe) lub po stronie magazynu kondycji. Wymiana jest oparta na numerach sekwencyjnych; nowsze raporty (z wyższymi numerami sekwencyjnymi) zastępują starsze raporty.

### <a name="health-events"></a>Zdarzenia zdrowotne
Wewnętrznie magazyn kondycji przechowuje [zdarzenia kondycji](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), które zawierają wszystkie informacje z raportów i dodatkowe metadane. Metadane obejmują czas, przez jaki raport został przekazany klientowi kondycji i czas jego modyfikacji po stronie serwera. Zdarzenia dotyczące kondycji są zwracane przez [kwerendy dotyczące kondycji.](service-fabric-view-entities-aggregated-health.md#health-queries)

Dodane metadane zawierają:

* **SourceUtcTimestamp**. Czas, w której raport został przekazany klientowi kondycji (Skoordynowany czas uniwersalny).
* **LastModifiedUtcTimestamp**. Czas ostatniej modyfikacji raportu po stronie serwera (Skoordynowany czas uniwersalny).
* **IsExpired**. Flaga wskazująca, czy raport wygasł, gdy kwerenda została wykonana przez magazyn kondycji. Zdarzenie może wygasnąć tylko wtedy RemoveWhenExpired jest false. W przeciwnym razie zdarzenie nie jest zwracany przez kwerendę i jest usuwany ze sklepu.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Ostatni raz dla przejść OK/warning/error. Te pola dają historię przejścia stanu kondycji dla zdarzenia.

Pola przejścia stanu mogą służyć do inteligentniejszych alertów lub "historycznych" informacji o zdarzeniach kondycji. Umożliwiają one scenariusze, takie jak:

* Alert, gdy właściwość została w ostrzeżeniu/błędzie przez więcej niż X minut. Sprawdzanie stanu przez pewien czas pozwala uniknąć alertów o tymczasowych warunkach. Na przykład alert, jeśli stan kondycji został ostrzeżenie przez więcej niż pięć minut mogą być tłumaczone na (HealthState == Warning and Now - LastWarningTransitionTime > 5 minut).
* Alert tylko na warunkach, które uległy zmianie w ciągu ostatnich X minut. Jeśli raport był już w błędzie przed określonym czasem, można go zignorować, ponieważ był już sygnalizowany wcześniej.
* Jeśli właściwość jest przełączanie między ostrzeżeniem i błędem, należy określić, jak długo to było w złej kondycji (to jest, nie OK). Na przykład alert, jeśli właściwość nie była w dobrej kondycji przez więcej niż pięć minut mogą być tłumaczone na (HealthState != Ok i Teraz - LastOkTransitionTime > 5 minut).

## <a name="example-report-and-evaluate-application-health"></a>Przykład: Zgłaszanie i ocena kondycji aplikacji
Poniższy przykład wysyła raport o kondycji za pośrednictwem programu PowerShell na **sieci szkieletowej aplikacji:/WordCount** ze źródła **MyWatchdog**. Raport kondycji zawiera informacje o właściwości kondycji "dostępność" w stanie kondycji błędu, z nieskończoną TimeToLive. Następnie odpytuje kondycję aplikacji, która zwraca zagregowane błędy stanu kondycji i zgłoszone zdarzenia kondycji na liście zdarzeń kondycji.

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

## <a name="health-model-usage"></a>Użycie modelu kondycji
Model kondycji umożliwia usługi w chmurze i podstawowej platformy sieci szkieletowej usług do skalowania, ponieważ monitorowania i określania kondycji są dystrybuowane między różnymi monitorami w klastrze.
Inne systemy mają jedną, scentralizowaną usługę na poziomie klastra, która analizuje wszystkie *potencjalnie* przydatne informacje emitowane przez usługi. Takie podejście utrudnia ich skalowalność. Nie pozwala im również zbierać konkretnych informacji, aby pomóc w identyfikacji problemów i potencjalnych problemów tak blisko głównej przyczyny, jak to możliwe.

Model kondycji jest intensywnie używany do monitorowania i diagnostyki, do oceny kondycji klastra i aplikacji oraz do monitorowania uaktualnień. Inne usługi używają danych kondycji do wykonywania automatycznych napraw, tworzenia historii kondycji klastra i wystawiania alertów o określonych warunkach.

## <a name="next-steps"></a>Następne kroki
[Wyświetlanie raportów kondycji sieci szkieletowej usług](service-fabric-view-entities-aggregated-health.md)

[Używanie raportów kondycji systemu do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Jak raportować i sprawdzać kondycję usługi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Dodawanie niestandardowych raportów kondycji sieci szkieletowej usług](service-fabric-report-health.md)

[Lokalne monitorowanie i diagnozowanie usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uaktualnienie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)

