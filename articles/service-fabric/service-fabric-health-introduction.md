---
title: Monitorowanie kondycji w sieci szkieletowej usług | Dokumentacja firmy Microsoft
description: Wprowadzenie do monitorowania modelu, który zapewnia monitorowanie klastra i jego aplikacji i usług kondycji sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: 1d979210-b1eb-4022-be24-799fd9d8e003
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: ed1a307cb2a2613fc7701392cd7b408715f10910
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207302"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Wprowadzenie do monitorowania kondycji usługi Service Fabric
Sieć szkieletowa usług Azure wprowadza modelu kondycji, który zawiera oceny kondycji sformatowanego, elastyczny i rozszerzalny i raportowania. Model pozwala niemal czasie rzeczywistym monitorowania stanu klastra i usługi działające w nim. Można łatwo uzyskać informacje o kondycji i rozwiązać potencjalne problemy przed kaskadowo i spowodować duże awarii. W typowej modelu usług wysyłania raportów opartych na ich lokalnych widoków, a następnie agregowane zapewnienie ogólną informacji klastra poziomie widoku.

Składniki sieci szkieletowej usług służy ten model kondycji sformatowanego do zgłaszania ich bieżącego stanu. Można ten sam mechanizm raportu kondycji z aplikacji. Jeśli poświęcić kondycji wysokiej jakości raportowania, który przechwytuje niestandardowe warunki, można wykrywać i znacznie łatwiejsze Rozwiązywanie problemów w uruchomionej aplikacji.

W poniższym filmie Microsoft Virtual Academy opisano model kondycji sieci szkieletowej usług i sposobie ich użycia: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-health-introduction/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

> [!NOTE]
> Możemy uruchomić podsystemu kondycji do zaspokojenia danej potrzeby monitorowanych uaktualnień. Usługa Service Fabric realizuje monitorowanej aplikacji i klaster uaktualnień, które zapewniają pełną dostępność, bez przestojów i minimalnym nie interwencji użytkownika. Na osiągnięcie tych celów, uaktualnienie sprawdza kondycji na podstawie skonfigurowanych zasad uaktualniania. Uaktualnienie można kontynuować tylko wtedy, gdy odpowiednie progi szanuje kondycji. W przeciwnym razie uaktualnienia jest automatycznie wycofana lub wstrzymane umożliwiają administratorom możliwość rozwiązać problemy. Aby dowiedzieć się więcej na temat uaktualniania aplikacji, zobacz [w tym artykule](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Magazynu kondycji
Magazynu kondycji przechowuje informacje dotyczące jednostek kondycji klastra na potrzeby łatwego pobierania i oceny. Zaimplementowano jako usługi sieć szkieletowa utrwalone usługi stanowej, aby zapewnić wysoką dostępność i skalowalność. Magazynu kondycji jest częścią **fabric: / System** aplikacji który jest dostępny, gdy klaster jest uruchomiony i działa.

## <a name="health-entities-and-hierarchy"></a>Jednostki kondycji i hierarchii
Jednostki kondycji są zorganizowane w hierarchii logicznej, która przechwytuje interakcji i zależności między różnymi jednostkami. Magazynu kondycji automatycznie tworzy jednostki kondycji i hierarchii, na podstawie raportów otrzymanych od składniki sieci szkieletowej usług.

Jednostki kondycji duplikatów jednostki sieci szkieletowej usług. (Na przykład **kondycji aplikacji jednostki** odpowiada wystąpienia aplikacji wdrożone w klastrze, podczas gdy **jednostek node kondycji** węzłem klastra usługi sieć szkieletowa jest zgodna.) Hierarchia kondycji przechwytuje interakcji jednostek systemowych i jest podstawą oceny kondycji zaawansowane. Informacje na temat podstawowych pojęć sieci szkieletowej usług w [omówienie techniczne sieci szkieletowej usług](service-fabric-technical-overview.md). Aby uzyskać więcej informacji na temat aplikacji, zobacz [model aplikacji usługi sieć szkieletowa](service-fabric-application-model.md).

Jednostki kondycji i hierarchii umożliwiają klastra i skutecznie można podać, debugowania i monitorowane. Model kondycji zawiera dokładne *szczegółowego* reprezentację kondycji z wielu części ruchu w klastrze.

![Kondycja jednostki.][1]
Jednostki kondycji zorganizowane w hierarchii, na podstawie relacji nadrzędny podrzędny.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Jednostki kondycji są:

* **Klaster**. Przedstawia kondycję klastra sieci szkieletowej usług. Raporty kondycji klastra opisują warunki, które mają wpływ na cały klaster. Te warunki mają wpływ na wiele jednostek w klastrze lub samego klastra. Osoby zgłaszającej, na podstawie warunku, nie można ograniczyć problem do jednego lub więcej podrzędnych złej kondycji. Przykładami informacji dzielenia spowodowane problemami z siecią partycjonowanie lub komunikacji klastra.
* **Węzeł**. Przedstawia kondycję węzła sieci szkieletowej usług. Raporty kondycji węzła opisują warunki, które mają wpływ na funkcjonalność węzła. Zazwyczaj wpływają na wszystkie jednostki wdrożonych w niej uruchomiona. Przykłady węzeł mało miejsca na dysku (lub inne właściwości komputera, takie jak pamięć, połączenia) i gdy węzeł nie działa. Obiekt węzła jest identyfikowane przez nazwę węzła (ciąg).
* **Aplikacja**. Przedstawia kondycję wystąpienie aplikacji uruchamianych w klastrze. Raporty o kondycji aplikacji opisują warunki, które mają wpływ na ogólną kondycję aplikacji. Nie można ich można zawęzić do poszczególnych elementów podrzędnych (usługi lub aplikacje wdrożone). Przykładami interakcji na trasie między różnych usług w aplikacji. Jednostka aplikacji jest identyfikowane przez nazwę aplikacji (URI).
* **Usługa**. Reprezentuje kondycję usługi uruchomione w klastrze. Raporty o kondycji usługi opisano warunki, które mają wpływ na ogólną kondycję usługi. Osoby zgłaszającej nie zawęzić problem zła partycji lub repliki. Przykładami konfiguracji usługi (np. port lub udziału plików zewnętrznych), będący przyczyną problemów dla wszystkich partycji. Jednostki usługi jest identyfikowane przez nazwę usługi (URI).
* **Partycja**. Przedstawia kondycję partycji usługi. Raporty kondycji partycji opisują warunki, które mają wpływ na całym zestawie replik. Przykładami przy poniżej docelowa liczba liczba replik i gdy partycja jest w wyniku utraty kworum. Jednostka partycji jest identyfikowany przez identyfikator (GUID) partycji.
* **Replika**. Przedstawia kondycję repliki usługi stanowej lub wystąpienia usługi bezstanowej. Replika jest najmniejsza jednostka, która watchdogs i składniki systemu mogą raport na temat aplikacji. Dla stanowych usług przykładami replikę podstawową, którego nie można replikować operacje pomocnicze bazy danych i replikacji powolne. Ponadto bezstanowych wystąpienia może raportować zaczyna brakować zasobów lub ma problemy z połączeniem. Jednostki repliki jest identyfikowany przez identyfikator (GUID) partycji i identyfikator repliki lub wystąpienia (Liczba długa).
* **DeployedApplication**. Reprezentuje kondycję *aplikacji uruchomionej w węźle*. Raporty kondycji wdrożonej aplikacji opisują warunki dotyczące tej aplikacji na węźle, który nie może być zawęzić pakiety usługi wdrożone na tym samym węźle. Przykładami błędy podczas w tym węźle nie można pobrać pakietu aplikacji i problemy dotyczące konfigurowania aplikacji podmiotów zabezpieczeń w węźle. Wdrożonej aplikacji jest identyfikowane przez nazwę aplikacji (URI) i nazwę węzła (ciąg).
* **DeployedServicePackage**. Przedstawia kondycję pakietu usługi uruchomione w węźle w klastrze. Opisuje warunki specyficzne dla pakietu usług, które nie mają wpływu na inne pakiety usługi na tym samym węźle dla tej samej aplikacji. Przykładami pakiet kodu w pakiecie usługi, której nie można uruchomić i pakiet konfiguracji, który nie może zostać odczytany. Pakiet wdrożonej usługi jest identyfikowane przez nazwę aplikacji (URI), nazwę węzła (ciąg), nazwa manifestu usługi (ciąg) i identyfikator aktywacji usługi pakietu (ciąg).

Poziom szczegółowości model kondycji ułatwia wykrycie i rozwiązanie problemów. Na przykład jeśli usługa nie odpowiada, jest to możliwe, aby zgłosić, że wystąpienie aplikacji jest zła. Raportowanie na czy poziom nie jest idealnym rozwiązaniem, jednak ponieważ problem może nie mieć wpływ na wszystkie usługi tej aplikacji. Raport powinny być stosowane do usługi w złej kondycji lub partycji określonych podrzędnej, jeśli wskazuje więcej informacji o tej partycji. Dane automatycznie powierzchni za pośrednictwem hierarchii i partycji zła stają się widoczne na poziomach usług i aplikacji. Ta agregacja ułatwia identyfikowanie i szybciej rozwiązać głównej przyczyny problemu.

Hierarchia kondycji składa się z relacjami nadrzędny podrzędny. Klaster składa się z węzłów i aplikacji. Aplikacje usług i wdrożone aplikacje. Wdrożone aplikacje wdrożeniu usługi pakietów. Usługi mają partycje, a każda partycja zawiera jeden lub więcej repliki. Brak specjalne relacji między węzłami i wdrożone jednostek. Węzłów w złej kondycji zgłoszonych przez składnik systemu jego urzędu, usługą Failover Manager service dotyczy wdrożone aplikacje, pakiety usługi i repliki na nim wdrożone.

Hierarchia kondycji reprezentuje najnowszy stan systemu, w oparciu o najnowsze raportów kondycji, który jest niemal w czasie rzeczywistym informacje.
Na tej samej jednostki na podstawie logiki specyficzne dla aplikacji lub niestandardowe warunki monitorowanych zgłosić watchdogs wewnętrznych i zewnętrznych. Raporty użytkownika współistnieć z raportów systemu.

Planowanie inwestycji w sposób raportu i odpowiadać podczas projektowania usługi w chmurze dużych kondycji. Ta początkowych inwestycyjny ułatwia usługę debugowania, monitorowania i działać.

## <a name="health-states"></a>Stany kondycji
Platforma Service Fabric korzysta trzy stany kondycji do opisywania, czy obiekt jest dobra: OK ostrzeżeń i błędów. Żadnych raportów wysyłane do magazynu kondycji należy określić jeden z tych stanów. Wynik oceny kondycji jest jeden z tych stanów.

Możliwe [stanów kondycji](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) są:

* **OK**. Jednostka jest w dobrej kondycji. Nie są znane problemy zgłoszone go lub jego elementów podrzędnych (jeśli jest to wymagane).
* **Ostrzeżenie**. Jednostka ma kilka problemów, ale mogą nadal działać prawidłowo. Na przykład występują opóźnienia, ale nie powoduje żadnych problemów funkcjonalnych jeszcze. W niektórych przypadkach stan ostrzeżenia może rozwiązać się bez interwencji zewnętrznych. W takich przypadkach raportów o kondycji podnieść świadomość i zapewniają wgląd w co się dzieje. W innych przypadkach stan ostrzeżenia mogą obniżyć poważny problem, bez interwencji użytkownika.
* **Błąd**. Jednostka ma niepoprawny stan. Działania należy podjąć w celu ustalenia stanu jednostki, ponieważ nie może działać prawidłowo.
* **Nieznany**. Jednostka nie istnieje w magazynie kondycji. Wynik ten można uzyskać z zapytań rozproszonych, które łączą się wyniki z wielu składników. Na przykład zapytanie listy węzła get przechodzi do **FailoverManager**, **ClusterManager**, i **HealthManager**; uzyskiwanie aplikacji zapytanie listy przechodzi do **ClusterManager** i **HealthManager**. Te zapytania scalania wyniki z wielu składników systemu. Jeśli inny składnik systemu zwraca jednostki, która nie znajduje się w magazynie kondycji, scalonych wyników ma nieznany stan kondycji. Jednostka nie jest w magazynie, ponieważ nie zostały jeszcze przetworzone Raporty kondycji lub jednostki zostały wyczyszczone po usunięciu.

## <a name="health-policies"></a>Zasady dotyczące kondycji
Magazynu kondycji stosuje zasady dotyczące kondycji, aby określić, czy jednostka jest w dobrej kondycji na podstawie jego raporty i jego elementów podrzędnych.

> [!NOTE]
> Zasady dotyczące kondycji można określić w manifeście klastra (dla klastra i węzła oceny kondycji) lub w manifeście aplikacji (w przypadku ocena aplikacji i wszystkich jego elementów podrzędnych). Żądania oceny kondycji można również przekazać w ramach zasad oceny kondycji niestandardowych, które są używane tylko w przypadku tej oceny.
> 
> 

Domyślnie usługi sieć szkieletowa stosuje ścisłych zasad (wszystko musi wskazywać dobrej kondycji) dla hierarchicznych relacji nadrzędny podrzędny. Jeśli nawet jednego elementu podrzędnego ma jedno zdarzenie złej kondycji, element nadrzędny jest określana jako zła.

### <a name="cluster-health-policy"></a>Zasady dotyczące kondycji klastra
[Klastra zasad dotyczących kondycji](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) jest używany do oceny stanu kondycji klastra i węzła stanów kondycji. Zasady można definiować w manifeście klastra. Jeśli nie jest obecny, jest używana domyślna zasada (zero tolerowaną awarii).
Zawiera zasad dotyczących kondycji klastra:

* [Elementów ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Określa, czy traktować ostrzeżenie kondycji raportuje jako błędy podczas oceny kondycji. Domyślnie: false.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Określa maksymalny udział procentowy tolerowaną aplikacje, które mogą być zła, zanim klaster zostanie uznane za błąd.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Określa maksymalny udział procentowy tolerowaną węzły, które mogą być zła, zanim klaster zostanie uznane za błąd. W dużych klastrach niektóre węzły są zawsze w dół lub wychodzących dla naprawy, dzięki tej wartości procentowej, należy skonfigurować tolerować, który.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Mapowanie zasad kondycji typu aplikacji można podczas oceny kondycji klastra opisano typy aplikacji specjalnych. Domyślnie wszystkie aplikacje są umieszczane w puli i oceniać za pomocą MaxPercentUnhealthyApplications. Niektóre typy aplikacji powinny być traktowane inaczej, może zostać pobrany z globalnej puli. Zamiast tego są one oceniane przed wartości procentowe skojarzone z nazwą typu aplikacji, ich na mapie. Na przykład w klastrze istnieją tysiące aplikacji o różnych typach i kilka wystąpień aplikacji formantu typu specjalnej aplikacji. Błąd nigdy nie powinna mieć kontroli aplikacji. Można określić MaxPercentUnhealthyApplications globalne do 20% tolerować błędy niektórych, ale dla aplikacji typu "ControlApplicationType" Ustaw MaxPercentUnhealthyApplications na 0. Dzięki temu w przypadku niektórych wiele aplikacji jest w złej kondycji, ale poniżej globalnych procent złej kondycji, klaster może przyjąć ostrzeżenie. Uaktualnianie klastra nie ma wpływu na ostrzegawczy stan kondycji lub innym monitorowania wyzwolone przez stanu kondycji błędu. Ale nawet jednego sterowania błędu spowodowałoby klastra złej kondycji, które wyzwala wycofywania lub wstrzymuje Uaktualnianie klastra, w zależności od konfiguracji uaktualnienia.
  Dla typów aplikacji, zdefiniowanych na mapie wszystkich wystąpień aplikacji są wyjmowane z globalnej puli aplikacji. Są one oceniane na podstawie całkowitej liczby aplikacji tego typu aplikacji, przy użyciu określonych MaxPercentUnhealthyApplications z mapy. Wszystkie pozostałe aplikacje pozostają w puli globalnej i są oceniane przy MaxPercentUnhealthyApplications.

Poniższy przykład zawiera fragment manifestu klastra. Aby zdefiniować wpisy mapy typu aplikacji, przed nazwą parametru "ApplicationTypeMaxPercentUnhealthyApplications-", a następnie według nazwy typu aplikacji.

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
[Zasady kondycji aplikacji](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) w tym artykule opisano, jak oceny zdarzeń oraz Stany podrzędnych agregacji jest wykonywane dla aplikacji i ich elementy podrzędne. Może być zdefiniowany w manifeście aplikacji **ApplicationManifest.xml**, w pakiecie aplikacji. Jeśli nie określono żadnych zasad, sieci szkieletowej usług zakłada, że jednostki jest nieprawidłowy, jeśli ma ona raport o kondycji lub element podrzędny w stanu kondycji ostrzeżenia lub błędu.
Można skonfigurować zasady są:

* [Elementów ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.considerwarningaserror.aspx). Określa, czy traktować ostrzeżenie kondycji raportuje jako błędy podczas oceny kondycji. Domyślnie: false.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Określa maksymalny udział procentowy tolerowaną wdrożone aplikacje, które mogą być zła, zanim aplikacja zostanie uznane za błąd. Ta wartość jest obliczana na podstawie dzielenia liczby wdrożonych aplikacji w złej kondycji przez liczbę węzłów, które aplikacje są obecnie wdrożona w klastrze. Obliczenia Zaokrągla wartość w do tolerowanie awarii jednego w małej liczby węzłów. Domyślna wartość procentowa: zero.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Określa domyślne usługi typu zasad dotyczących kondycji, która zastępuje domyślną zasadę kondycji dla wszystkich typów usług w aplikacji.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Miejsce mapy zasad dotyczących kondycji usługi dla typów usług. Te zasady zastępują domyślne zasady kondycji typu usługi dla każdego określonego typu. Na przykład jeśli aplikacja ma typ usługi bezstanowej bramy i typ usługi stanowej aparatu, możesz można skonfigurować zasady dotyczące kondycji ich oceny inaczej. Po określeniu zasad na typ usługi, aby uzyskać większą kontrolę nad kondycji usługi.

### <a name="service-type-health-policy"></a>Zasady dotyczące kondycji usługi typu
[Obsługi zasad dotyczących kondycji typu](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) Określa, jak ocena i agregowanie usługi i elementy podrzędne usług. Zasady zawierają:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Określa maksymalny procent tolerowaną zła partycji, zanim usługa jest określana jako zła. Domyślna wartość procentowa: zero.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Określa maksymalny udział procentowy tolerowaną repliki w złej kondycji, przed partycji jest określana jako zła. Domyślna wartość procentowa: zero.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Określa maksymalny procent tolerowaną usługi w złej kondycji, zanim aplikacja jest określana jako zła. Domyślna wartość procentowa: zero.

Poniższy przykład zawiera fragment manifest aplikacji:

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

## <a name="health-evaluation"></a>Oceny kondycji
Użytkowników i usług automatycznych można obliczyć kondycji dla dowolnej jednostki w dowolnym momencie. Do oceny kondycji jednostki, zagregowanych danych z magazynu kondycji kondycji wszystkich raportów w ramach jednostki i oblicza wszystkie elementy podrzędne (jeśli jest to wymagane). Algorytm agregacji kondycji używa zasad dotyczących kondycji, które określają sposób oceny raportów o kondycji i agregowanie stanów kondycji podrzędnych (jeśli jest to wymagane).

### <a name="health-report-aggregation"></a>Agregacja raportów kondycji
Jeden podmiot może mieć wiele raportów kondycji wysyłane przez różne raporty (składników systemowych lub watchdogs) na inne właściwości. Agregacja używa zasady skojarzone kondycji, w szczególności elementów ConsiderWarningAsError członkiem zasad dotyczących kondycji aplikacji lub w klastrze. Elementów ConsiderWarningAsError określa sposób oceny ostrzeżenia.

Stan kondycji zagregowane jest wyzwalany przez *najgorszy* raporty dotyczące kondycji jednostki. W przypadku raport o kondycji co najmniej jeden błąd, stan kondycji zagregowane, występuje błąd.

![Agregacja raportu kondycji z raportu o błędach.][2]

Jednostki kondycji, która ma co najmniej jeden błąd Raport kondycji jest traktowane jako błąd. Dotyczy to także raport o kondycji wygasłe, niezależnie od jej stan kondycji.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Jeśli istnieją nie raporty o błędach i co najmniej jedno ostrzeżenie, stan kondycji zagregowane jest ostrzeżenia lub błędu, w zależności od elementów ConsiderWarningAsError flagi zasad.

![Agregacja raportu kondycji z raportem ostrzeżenie i elementów ConsiderWarningAsError false.][3]

Agregacja raportu kondycji z raportem ostrzeżenie i elementów ConsiderWarningAsError, wartość false (ustawienie domyślne).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregacja kondycji podrzędne
Zagregowane kondycja jednostki odzwierciedla stanów kondycji podrzędnych (jeśli jest to wymagane). Do agregowania stanów kondycji podrzędnych algorytm zasady dotyczące kondycji stosowane na podstawie typu jednostki.

![Agregacja kondycji jednostek podrzędnych.][4]

Na podstawie zasad kondycji agregacji podrzędnych.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Po oceniane magazynu kondycji wszystkich elementów podrzędnych agreguje ich stan kondycji, w oparciu skonfigurowany maksymalny procent podrzędne o złej kondycji. Ta wartość jest pobierana z zasady na podstawie typu jednostki i podrzędnych.

* Jeśli wszystkie elementy podrzędne OK stanów, stan kondycji podrzędnych zagregowane jest OK.
* Jeśli zarówno OK i ostrzeżenie stanów elementów podrzędnych, stan kondycji podrzędnych zagregowane jest ostrzeżenie.
* Jeśli istnieją elementy podrzędne z stanów błędów, które nie przestrzega maksymalny dozwolony odsetek podrzędne o złej kondycji, stan kondycji zagregowane, występuje błąd.
* Jeśli elementy podrzędne stanów błąd przestrzegać maksymalny procent dozwolonych elementów podrzędnych w złej kondycji, stan kondycji zagregowane jest ostrzeżenie.

## <a name="health-reporting"></a>Raportowania kondycji
Składniki systemu, aplikacji sieci szkieletowej systemu i wewnętrznej/zewnętrznej watchdogs może raportować względem jednostki sieci szkieletowej usług. Raporty upewnij *lokalnego* oznaczeń kondycję monitorowanych jednostek, na podstawie wybranych warunków, ich monitorowania. Nie należy przyjrzeć się żadnych stan globalny lub agregowanie danych. Zachowanie jest raporty proste i złożone organizmy, które należy przyjrzeć się wiele rzeczy w celu uwzględnienia informacji do wysyłania.

Aby wysłać dane kondycji w magazynie kondycji, osoby zgłaszającej musi zidentyfikować odpowiednich jednostek i utworzyć raport o kondycji. Aby wysłać raport, należy użyć [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) interfejsu API, Raport kondycji interfejsów API narażone na `Partition` lub `CodePackageActivationContext` obiektów, poleceń cmdlet programu PowerShell i REST.

### <a name="health-reports"></a>Raportów o kondycji
[Raportów o kondycji](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) dla każdej jednostki w klastrze zawiera następujące informacje:

* **SourceId**. Ciąg unikatowo identyfikujący osoby zgłaszającej zdarzenia kondycji.
* **Identyfikator jednostki**. Określa jednostki, w których jest stosowane raportu. Różni się na podstawie [typu jednostki](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Klaster. Brak.
  * Węzeł. Nazwa węzła (ciąg).
  * Aplikacja. Nazwa aplikacji (URI). Reprezentuje nazwę wystąpienia aplikacji wdrożonych w klastrze.
  * Usługa. Nazwa usługi (URI). Reprezentuje nazwę wystąpienia usługi wdrożone w klastrze.
  * Partycja. Identyfikator (GUID). Reprezentuje unikatowy identyfikator partycji.
  * Repliki. Identyfikator repliki usługi stanowej lub identyfikator wystąpienia usługi bezstanowej (INT64).
  * DeployedApplication. Nazwa aplikacji (URI) i nazwę węzła (ciąg).
  * DeployedServicePackage. Nazwa aplikacji (URI), nazwę węzła (ciąg) i service manifest nazwę (ciąg).
* **Właściwość**. A *ciąg* (nie stałej wyliczenia), która umożliwia osoby zgłaszającej kategoryzację zdarzenie kondycji dla konkretnej właściwości jednostki. Na przykład osoby zgłaszającej A może raportować kondycję Node01 właściwość "Magazyn" i osoby zgłaszającej B może raportować kondycję Node01 właściwość "Łączność". W magazynie kondycji te raporty są traktowane jako zdarzenia kondycji osobne jednostki Node01.
* **Opis elementu**. Ciąg, który umożliwia reportera do zawierają szczegółowe informacje o kondycji zdarzenie. **SourceId**, **właściwości**, i **HealthState** pełni powinna zawierać opis raportu. Opis dodaje czytelny dla człowieka informacje na temat raportu. Tekst ułatwia dla administratorów i użytkowników zrozumieć raport o kondycji.
* **HealthState**. [Wyliczenie](service-fabric-health-introduction.md#health-states) opisujący stan kondycji raportu. Dopuszczalne wartości to OK, ostrzeżenia i błędu.
* **TimeToLive**. Zakres czasu, który wskazuje, jak długo raport o kondycji jest nieprawidłowy. Połączone z **RemoveWhenExpired**, umożliwia magazynu kondycji, wiadomo, jak można obliczyć wygasłe zdarzenia. Domyślnie wartość to nieskończoność, a raport jest prawidłowy w nieskończoność.
* **RemoveWhenExpired**. Wartość logiczna. Jeśli ma wartość true, raport o kondycji wygasłe zostanie automatycznie usunięta z magazynu kondycji i raport bez wpływu na ocenę kondycji jednostki. Użyć, gdy raport jest nieprawidłowy w podanym okresie tylko raz, a osoby zgłaszającej nie trzeba jawnie wyczyszczenie go. Również służy do usuwania raporty z magazynu kondycji (na przykład programu alarmowego zostało zmienione i zatrzymuje wysyłanie raportów o poprzednim źródła i właściwości). Może wysłać raport o krótki TimeToLive wraz z RemoveWhenExpired, aby wyczyścić wszystkie poprzednie stan z magazynu kondycji. Jeśli wartość jest równa false, wygasłych raportu jest traktowana jako błąd po oceny kondycji. Sygnały wartości false w magazynie kondycji, które okresowo Zgłoś źródła dla tej właściwości. W przeciwnym razie należy problem ze programu alarmowego. Kondycji programu alarmowego są przechwytywane z uwzględnieniem zdarzeń jako błąd.
* **SequenceNumber**. Dodatnia liczba całkowita, która musi być coraz większe, reprezentuje kolejność raporty. W magazynie kondycji służy do wykrywania starych raportów, które są odbierane opóźnienia z powodu opóźnienia sieci lub inne problemy. Raport został odrzucony, jeśli numer sekwencji jest mniejsza niż maksymalne ostatnio zastosowane numer tej samej jednostki źródłowej, a właściwość. Jeśli nie zostanie określony, numer sekwencji jest generowany automatycznie. Należy umieścić w numer sekwencyjny tylko wtedy, gdy raportowanie przy zmianie stanu. W takiej sytuacji źródło musi do zapamiętania raportów, których wysłanie go i Zachowaj informacje odzyskiwania w tryb failover.

Te cztery części informacji — SourceId, identyfikator jednostki i właściwości oraz HealthState — są wymagane dla każdego raportu o kondycji. Ciąg SourceId nie może rozpoczynać się od prefiksu "**systemu.**", który jest zarezerwowany dla raportów systemu. Dla tej samej jednostki istnieje tylko jeden raport dla tego samego źródła i właściwości. Wiele raportów dla tego samego źródła i właściwości zastąpienia siebie, na stronie kondycji klienta (jeśli są przetwarzane wsadowo) albo na kondycję sklepu po stronie. Zastąpienie opiera się na numerów; Raporty nowszej (o wyższych numerach) zastąpić starszą raportów.

### <a name="health-events"></a>Zdarzenia kondycji
Wewnętrznie śledzi magazynu kondycji [zdarzenia kondycji](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), które zawierają wszystkie informacje z raportów i dodatkowe metadane. Metadane obejmują czasem raport został przekazany klientowi kondycji i czasu, który został zmodyfikowany po stronie serwera. Zdarzenia kondycji są zwracane przez [zapytań o kondycję](service-fabric-view-entities-aggregated-health.md#health-queries).

Dodano metadane zawierają:

* **SourceUtcTimestamp**. Czas raport został przekazany klientowi kondycji (Coordinated Universal Time).
* **LastModifiedUtcTimestamp**. Czas ostatniej modyfikacji raportu po stronie serwera (Coordinated Universal Time).
* **IsExpired**. Flaga wskazująca, czy raport wygasła, gdy zapytanie zostało wykonane przez Magazyn kondycji. Zdarzenia mogą wygasnąć tylko wtedy, gdy RemoveWhenExpired ma wartość false. W przeciwnym razie zdarzenia nie są zwracane przez zapytanie i zostanie usunięty z magazynu.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Czas ostatniej OK/ostrzeżenie/błąd przejścia. Te pola nadaj historię kondycji przejść stanu zdarzenia.

Pola przejścia stanu może służyć powiadomień o alertach inteligentny lub "historyczne" kondycji zdarzenie informacji. Umożliwiają one scenariuszy takich jak:

* Generuj alert, jeśli właściwość została na ostrzeżenie/błąd dla więcej niż X minut. Sprawdzanie warunku w danym okresie czasu pozwala uniknąć alertów dotyczących tymczasowych warunków. Na przykład można przetłumaczyć alert, gdy stan kondycji ma zostały ostrzeżenie przez więcej niż pięć minut (HealthState == ostrzeżenie i LastWarningTransitionTime teraz - > 5 minut).
* Alert tylko w warunkach, które zostały zmienione w ciągu ostatnich X minut. Jeśli raport został już przy błędzie przed upływem określonego terminu, można zignorować, ponieważ zostało już zasygnalizowane wcześniej.
* Jeśli właściwość jest przełączania się między ostrzeżenia i błędu, określić, jak długo był nieprawidłowy (to znaczy nie OK). Na przykład można przetłumaczyć alert, gdy właściwość nie było w złej kondycji przez więcej niż pięć minut (HealthState! = Ok i teraz - LastOkTransitionTime > 5 minut).

## <a name="example-report-and-evaluate-application-health"></a>Przykład: Raport i oceny kondycji aplikacji
Poniższy przykład wysyła raport dotyczący kondycji za pomocą programu PowerShell w aplikacji **fabric: / WordCount** ze źródła **MyWatchdog**. Raport o kondycji zawiera informacje o kondycji właściwości "availability" w stanie kondycji błędu z TimeToLive nieskończoną. Następnie wykonuje zapytanie kondycji aplikacji, która zwraca wartość zagregowana błędy stanu kondycji i zdarzeń zgłoszonych kondycji na liście zdarzenia kondycji.

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
Model kondycji umożliwia usługi w chmurze i platforma sieci szkieletowej usług podstawowa, aby skalować, ponieważ monitorowania i kondycji są rozmieszczone w różnych monitorów w klastrze.
Inne systemy ma jeden, scentralizowane usługi na poziomie klastra, która analizuje wszystkie *potencjalnie* przydatnych informacji na temat emitowane przez usługi. Takie podejście przeszkadza ich skalowalności. On również nie zezwala na zbierać określone informacje ułatwiają identyfikację problemów i potencjalnych problemach, jak blisko głównej przyczyny problemu, jak to możliwe.

Model kondycji ciężko monitorowania i diagnostyki dla oceny kondycji klastra i aplikacji i monitorowanych uaktualnienia. Inne usługi użyj dane kondycji do wykonywania automatycznej naprawy, kompilacji Historia kondycji klastra i wystawić alerty przy użyciu określonych warunków.

## <a name="next-steps"></a>Kolejne kroki
[Wyświetl raporty dotyczące kondycji sieci szkieletowej usług](service-fabric-view-entities-aggregated-health.md)

[Użyj systemowych raportów kondycji do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Jak zgłosić i Sprawdź kondycję usług](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Dodawanie niestandardowych raportów kondycji sieci szkieletowej usług](service-fabric-report-health.md)

[Monitorowanie i diagnozowania usług lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uaktualnianie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)

