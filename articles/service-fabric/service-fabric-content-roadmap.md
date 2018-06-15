---
title: Dowiedz się więcej o sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat podstawowych pojęć i głównych obszarów sieci szkieletowej usług Azure. Zawiera omówienie rozszerzonej sieci szkieletowej usług oraz sposobu tworzenia mikrousług.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: ryanwi
ms.openlocfilehash: 1c3ea5b041cf2a961ef57bc168ae86b83412e044
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212827"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Dlatego chcesz Dowiedz się więcej o sieci szkieletowej usług?
Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi.  Sieć szkieletowa usług ma dużej powierzchni, jednak i jest dużo, aby dowiedzieć się więcej.  Ten artykuł przedstawia streszczenie sieci szkieletowej usług oraz podstawowe koncepcje programowania modeli, cyklem życia aplikacji, testowania, klastrami i monitorowanie kondycji. Odczyt [omówienie](service-fabric-overview.md) i [co to są mikrousług?](service-fabric-overview-microservices.md) wprowadzenie i jak sieci szkieletowej usług może służyć do tworzenia mikrousług. W tym artykule nie zawiera pełną listę zawartości, ale łącze do omówienie i pobieranie rozpoczęte artykułów dla każdej części sieci szkieletowej usług. 

## <a name="core-concepts"></a>Kluczowe pojęcia
[Terminologia usługi sieć szkieletowa](service-fabric-technical-overview.md), [model aplikacji](service-fabric-application-model.md), i [obsługiwane modele programowania](service-fabric-choose-framework.md) zapewniają większą pojęcia i opisy, ale poniżej przedstawiono podstawowe informacje.

<table><tr><th>Kluczowe pojęcia</th><th>Czas projektowania</th><th>W czasie wykonywania</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-application-type-service-type-application-package-and-manifest-service-package-and-manifest"></a>Czas projektowania: typ aplikacji, typ usługi, pakiet aplikacji i manifest, pakiet usługi i manifestu
Typem aplikacji jest nazwa wersji przypisane do kolekcji typu usługi. To jest zdefiniowany w *ApplicationManifest.xml* pliku, który jest osadzony w katalogu pakietu aplikacji. Pakiet aplikacji jest następnie skopiowana do magazynu obrazu klastra sieci szkieletowej usług. Następnie można utworzyć nazwanego aplikacji tego typu aplikacji, która następnie działa w klastrze. 

Typ usługi jest nazwa wersji przypisane do pakietów kodu usługi, danych i pakietów konfiguracji. To jest zdefiniowany w pliku ServiceManifest.xml jest osadzony w katalogu pakietów usługi. Katalog pakietu usługi następnie odwołuje się pakietu aplikacji *ApplicationManifest.xml* pliku. W ramach klastra po utworzeniu aplikacji o nazwie, można utworzyć nazwanego usługi jednego z typów usługi typu aplikacji. Typ usługi jest opisane przez jego *ServiceManifest.xml* pliku. Typ usługi składa się z ustawienia konfiguracji usługi kodu wykonywalnego, które są ładowane w czasie wykonywania, a dane statyczne, które jest używane przez usługę.

![Typy aplikacji usługi Service Fabric i usługi][cluster-imagestore-apptypes]

Pakiet aplikacji jest katalogiem dysk zawierający ten typ aplikacji *ApplicationManifest.xml* pliku, który odwołuje się do pakietów usług dla każdego typu usług, tworzącą typu aplikacji. Na przykład pakiet aplikacji dla typu aplikacji poczty e-mail może zawierać odwołania do pakietu usług kolejki, pakiet usługi frontonu i pakiet usługi bazy danych. Pliki w katalogu pakietu aplikacji są kopiowane do magazynu obrazu klastra sieci szkieletowej usług. 

Pakiet usługi jest katalogiem dysk zawierający ten typ usługi *ServiceManifest.xml* pliku, który odwołuje się do kodu, dane statyczne i pakiety konfiguracji dla typu usługi. Pliki w katalogu pakietu usługi odwołuje się typ aplikacji *ApplicationManifest.xml* pliku. Na przykład pakiet usługi może odnosić się do kodu, dane statyczne i pakiety konfiguracji, które tworzą usługi bazy danych.

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Czas wykonywania: klastry i węzły o nazwie aplikacji o nazwie usług, partycji i replik
[Klaster usługi Service Fabric](service-fabric-deploy-anywhere.md) jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym wdraża się mikrousługi i nimi zarządza. Klastry mogą obejmować nawet tysiące maszyn.

Maszyna lub maszyna wirtualna, która jest częścią klastra, jest nazywana węzłem. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają swoje właściwości — na przykład właściwości dotyczące umieszczania. Każdy komputer lub maszyna wirtualna jest automatycznie uruchamiana usługa systemu Windows, `FabricHost.exe`, której zacznie działać po rozruchu, a następnie uruchamia dwa pliki wykonywalne: `Fabric.exe` i `FabricGateway.exe`. Te dwa pliki wykonywalne składają się na węźle. Do rozwoju lub testowania scenariuszy może obsługiwać wiele węzłów na jednym komputerze lub maszynie Wirtualnej, uruchamiając wiele wystąpień `Fabric.exe` i `FabricGateway.exe`.

Aplikacja o nazwie jest kolekcję o nazwie usług wykonuje niektórych funkcji lub funkcji. Usługa wykonuje funkcję pełny i autonomiczne (go można uruchomić i przeprowadzić niezależnie od innych usług) i składa się z kodu, konfiguracji i danych. Po pakietu aplikacji jest kopiowany do magazynu obrazu, należy utworzyć wystąpienie aplikacji w klastrze, określając pakiet aplikacji typu aplikacji (przy użyciu jego nazwa/wersja). Każde wystąpienie typu aplikacji jest przypisany nazwa identyfikatora URI, który wygląda jak *fabric: / MyNamedApp*. W ramach klastra można utworzyć wiele aplikacji o nazwie z typem pojedynczej aplikacji. Można również utworzyć nazwanego aplikacje z typów inną aplikację. Każda aplikacja o nazwie jest niezależnie zarządzane i numerów wersji.

Po utworzeniu aplikacji o nazwie, można utworzyć wystąpienia jednego z jego typów usług (usługa o nazwie) w klastrze, określając typ usługi (za pomocą jego nazwa/wersja). Każdego wystąpienia typu usługi przypisano nazwę identyfikatora URI zakres w obszarze jej aplikację o nazwie identyfikatora URI. Na przykład, jeśli tworzysz "Mojabazadanych" o nazwie service w "MyNamedApp" o nazwie aplikacji, identyfikator URI wygląda: *fabric: / MyNamedApp/mojabazadanych*. W ramach aplikacji o nazwie można utworzyć co najmniej jedną usługę o nazwie. Każda usługa o nazwie może być schemat partycji i liczby wystąpień/repliki. 

Istnieją dwa typy usług: bezstanowych i stanowych. Usługi bezstanowej można przechowywać trwały stan usługi magazynu zewnętrznego, takiego jak magazyn Azure, baza danych SQL Azure lub bazy danych Azure rozwiązania Cosmos. Użyj usługi bezstanowej, jeśli usługa nie ma w ogóle nie magazynu trwałego. Usługa stanowa używa sieci szkieletowej usług do zarządzania stanem usługi za pośrednictwem jej wiarygodnych kolekcji lub modele programowania Reliable Actors. 

Podczas tworzenia usługi o nazwie, należy określić schemat partycji. Usługi z dużą ilością stanu podzielić danych na partycji. Każda partycja jest odpowiedzialny za część pełny stan usługi, która jest dystrybuowane między węzłami klastra.  

Na poniższym diagramie przedstawiono związek między aplikacjami i wystąpień usług, partycji i replik.

![Partycji i replik w ramach usługi][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Podział na partycje, skalowanie i dostępności
[Partycjonowanie](service-fabric-concepts-partitioning.md) nie jest unikatowa w sieci szkieletowej usług. Formularz dobrze znanych partycjonowania jest podział danych lub dzielenia na fragmenty. Usługi stanowe z dużą ilością stanu podzielić danych na partycji. Każda partycja jest odpowiedzialny za część pełny stan usługi. 

Replik każdej partycji są rozkładane między węzłami klastra, dzięki czemu usługa o nazwie stanu, tak aby [skali](service-fabric-concepts-scalability.md). Dane wymogów, powiększania partycji i sieci szkieletowej usług rebalances partycji w węzłach wydajne wykorzystanie zasobów sprzętowych. Po dodaniu nowych węzłów do klastra usługi sieć szkieletowa będzie ponowne zrównoważenie replik partycji na zwiększenie liczby węzłów. Ogólne poprawia wydajność aplikacji i zmniejsza rywalizacji o dostęp do pamięci. Jeśli nie są wydajnie używane węzłów w klastrze, można zmniejszyć liczbę węzłów w klastrze. Sieć szkieletowa usług ponownie rebalances replik partycji między zmniejszyć liczbę węzłów w celu lepszego wykorzystania sprzętu na każdym węźle.

W partycji bezstanowych usługi o nazwie jest wystąpień podczas stanowych usług o nazwie jest replik. Zazwyczaj bezstanowych usługi o nazwie istniało tylko jedną partycję, ponieważ mają one nie stan wewnętrzny. Podaj wystąpienia partycji [dostępności](service-fabric-availability-services.md). Jeśli jedno wystąpienie nie powiedzie się, inne wystąpienia kontynuował normalne działanie, a następnie sieć szkieletowa usług tworzy nowe wystąpienie. Obsługa stateful o nazwie usługi ich stan w obrębie repliki i każda partycja ma własną zestawu replik. Operacje odczytu i zapisu są wykonywane w jednej replice (nazywany podstawowym). Zmiany stanu z zapisu operacji są replikowane do wielu innych replik (nazywane aktywne pomocnicze bazy danych). Replika ulegnie awarii, sieć szkieletowa usług tworzy nową replikę z istniejących replik.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Mikrousługi stanowe i bezstanowe dla usługi Service Fabric
Usługa Service Fabric umożliwia tworzenie aplikacji składających się z mikrousług lub kontenerów. Mikrousługi bezstanowe (na przykład bramy protokołów i internetowe serwery proxy) nie utrzymują modyfikowalnego stanu poza żądaniem i odpowiedzią serwera. Przykładem usługi bezstanowej są procesy robocze usług Azure Cloud Services. Mikrousługi stanowe (na przykład konta użytkowników, bazy danych, urządzenia, koszyki zakupów i kolejki) utrzymują modyfikowalny, autorytatywny stan poza żądaniem i odpowiedzią. Współczesne aplikacje internetowe łączą w sobie mikrousługi stanowe i bezstanowe. 

Klucza differentation z sieci szkieletowej usług jest jego skoncentrowanie się na tworzeniu usługi stanowej, za pomocą [wbudowane modele programowania ](service-fabric-choose-framework.md) lub konteneryzowanych usług stanowych. [Scenariusze zastosowania](service-fabric-application-scenarios.md) opisują sytuacje, w których używane są usługi stanowe.

Dlaczego masz stanowe mikrousług wraz z nich bezstanowych? Dostępne są następujące dwie główne przyczyny:

* Można tworzyć wysokiej przepływności, małych opóźnieniach, odpornych przetwarzania transakcji online (OLTP) usług przez zachowanie kodu i zamknij danych na tym samym komputerze. Przykłady są interaktywne sklepy, wyszukiwania, systemów Internetu rzeczy (IoT), systemów handlu, systemy wykrywania oszustw i przetwarzania kart kredytowych i zarządzania osobiste rekordu.
* Upraszcza projektowanie aplikacji. Stanowe mikrousług Usuń potrzebę dodatkowe kolejki i pamięci podręczne, które tradycyjnie sprostać wymaganiom dostępności i opóźnienia czysto bezstanowych aplikacji. Stanowe usług są naturalnie wysokiej dostępności i małe opóźnienia, co zmniejsza liczbę części ruchu do zarządzania w aplikacji jako całość.

## <a name="supported-programming-models"></a>Obsługiwane modele programowania
Sieć szkieletowa usług oferuje wiele sposobów, aby zapisać i zarządzania usługami. Usługi mogą używać interfejsów API usługi Service Fabric w pełni wykorzystać funkcje platformy i struktury aplikacji. Można także wszelkie skompilowany program wykonywalny w dowolnym języku i hostowanych w klastrze usługi sieć szkieletowa usług. Aby uzyskać więcej informacji, zobacz [obsługiwane modele programowania](service-fabric-choose-framework.md).

### <a name="containers"></a>Containers
Domyślnie usługi sieć szkieletowa wdraża i aktywuje usługi jako procesów. Sieć szkieletowa usług można także wdrożyć usług w [kontenery](service-fabric-containers-overview.md). Ważne można mieszać usług w procesach i usług w kontenerach w tej samej aplikacji. Sieć szkieletowa usług obsługuje wdrażanie kontenerów Linux kontenery systemu Windows w systemie Windows Server 2016. Można wdrożyć istniejących aplikacji usług bezstanowych i stanowych usług w kontenerach. 

### <a name="reliable-services"></a>Reliable Services
[Niezawodne usługi](service-fabric-reliable-services-introduction.md) jest lekki framework dla usług, które integrują się z platformy sieć szkieletowa usług i korzystać z pełnego zestawu funkcji zapisywania. Niezawodne usługi może być bezstanowej (podobny do większości platform usługi, takimi jak serwery sieci web lub roli proces roboczy w usług Azure Cloud Services), których stan jest umieszczany w zewnętrznych rozwiązania, takie jak bazy danych platformy Azure lub magazynu tabel Azure. Niezawodne usługi mogą być również stateful, których stan jest trwały bezpośrednio w usłudze przy użyciu niezawodnych kolekcji. Staje się stan [wysokiej dostępności](service-fabric-availability-services.md) przy użyciu replikacji i rozprowadzane za pośrednictwem [partycjonowania](service-fabric-concepts-partitioning.md)wszystkie zarządzane automatycznie przez sieć szkieletowa usług.

### <a name="reliable-actors"></a>Reliable Actors
Oparty na niezawodne usługi [niezawodnego aktora](service-fabric-reliable-actors-introduction.md) framework jest struktury aplikacji, która implementuje wzorca aktora wirtualnego, oparte na wzorcu aktora. Platforma niezawodnego aktora korzysta niezależne jednostki zasobów obliczeniowych i stan z jednowątkowego wykonywania o nazwie złośliwych użytkowników. Niezawodnego aktora, który udostępnia framework wbudowany w komunikacie osób i wstępnie ustawiony stan konfiguracji trwałości i skalowalnego w poziomie.

### <a name="aspnet-core"></a>ASP.NET Core
Sieć szkieletowa usług integruje się z [platformy ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) jako pierwszej klasie model programowania do tworzenia sieci web i aplikacji interfejsu API.  Platformy ASP.NET Core mogą być używane w sieci szkieletowej usług na dwa sposoby:

- Obsługiwane jako pliku wykonywalnego gościa. Służy to głównie do uruchomienia aplikacji platformy ASP.NET Core w sieci szkieletowej usług bez zmian kodu.
- Uruchom wewnątrz niezawodnej usługi. Umożliwia lepszą integrację z środowiska uruchomieniowego platformy Service Fabric i umożliwia stanowe platformy ASP.NET Core services.

### <a name="guest-executables"></a>Pliki wykonywalne gościa
A [pliku wykonywalnego gościa](service-fabric-guest-executables-introduction.md) jest istniejący, dowolnego pliku wykonywalnego (zapisany w dowolnym języku) hostowanych w klastrze usługi sieć szkieletowa równolegle z innymi usługami. Pliki wykonywalne gościa nie łączyć bezpośrednio z interfejsów API usługi Service Fabric. Jednak one nadal korzystać z funkcji oferuje platformie, takich jak niestandardowe kondycji i załadować raportowania i usługi odnajdywania przez wywoływanie interfejsów API REST. Mają one również pełny cykl życia pomocy technicznej. 

## <a name="application-lifecycle"></a>Cykl życia aplikacji
Ponieważ z innych platform, aplikacji w sieci szkieletowej usług zwykle przechodzi przez następujących faz: projekt, rozwoju, testowania, wdrożenia, uaktualniania, obsługi i usuwania. Sieć szkieletowa usług zawiera najwyższej jakości pomoc techniczną dla pełnej aplikacji cyklem życia aplikacji w chmurze, od projektowania do wdrożenia, codziennego zarządzania i konserwacji do likwidacji ostatecznego. Model usługi umożliwia kilku różnych ról niezależnie uczestniczyć w cyklu życia aplikacji. [Cykl życia aplikacji usługi sieć szkieletowa](service-fabric-application-lifecycle.md) zawiera omówienie interfejsów API i jak są używane przez różne role w etapy cyklu życia aplikacji sieci szkieletowej usług. 

Cykl życia całej aplikacji można zarządzać za pomocą [poleceń cmdlet programu PowerShell](/powershell/module/ServiceFabric/), [polecenia interfejsu wiersza polecenia](service-fabric-sfctl.md), [interfejsów API języka C#](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [interfejsów API języka Java](/java/api/system.fabric._application_management_client), i [ Interfejsy API REST](/rest/api/servicefabric/). Można także skonfigurować ciągłej integracji/ciągłego potoki wdrożenia za pomocą narzędzi takich jak [Visual Studio Team Services](service-fabric-set-up-continuous-integration.md) lub [Wpięć](service-fabric-cicd-your-linux-applications-with-jenkins.md).

W poniższym filmie Microsoft Virtual Academy opisano sposób zarządzania cyklu użytkowania Twojej aplikacji: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-applications-and-services"></a>Testowanie aplikacji i usług
Aby utworzyć naprawdę skali chmury usługi, jest krytyczne, aby zweryfikować, że aplikacje i usługi może wytrzymać rzeczywistych błędów. Usługi analizy błędów jest przeznaczony do testowania usług, które są wbudowane w sieci szkieletowej usług. Z [usługi analiza błędów](service-fabric-testability-overview.md), można wywołać znaczenie błędów i uruchom scenariusze ukończenia testowej względem aplikacji. Te błędy i scenariusze wykonywania i sprawdzania poprawności wiele stanów i przejść, które usługa może wystąpić w jego okres istnienia, w sposób kontrolowany, bezpieczne i zgodne.

[Akcje](service-fabric-testability-actions.md) docelowa usługi do testowania za pomocą poszczególnych błędów. Projektant usługi można użyć je jako bloków konstrukcyjnych, aby zapisać złożonych zadań. Przykłady symulowane błędów:

* Uruchom ponownie węzeł, aby symulować dowolną liczbę sytuacji, w którym ponownego uruchomienia komputera lub maszyny Wirtualnej.
* Przenieś repliki usługi stanowej do symulowania równoważenia obciążenia i trybu failover lub uaktualniania aplikacji.
* Wywołaj utraty kworum usługi stanowej, aby uniknąć sytuacji, gdy nie można kontynuować operacji zapisu, ponieważ nie ma wystarczającej liczby replik "kopii zapasowych" i "secondary" nowe dane.
* Wywołanie usługi stanowej, aby uniknąć sytuacji, gdy całkowicie wyczyszczeniem wszystkich stanów w pamięci poza utraty danych.

[Scenariusze](service-fabric-testability-scenarios.md) złożonych operacji składają się z co najmniej jednej akcji. Usługi analizy błędów zawiera dwa wbudowane pełne scenariusze:

* [Scenariusz chaos](service-fabric-controlled-chaos.md)-symuluje błędów stałego, przeplotem (bezpieczne i nieprawidłowego) w całym klastrze przez dłuższy czas.
* [Scenariusz trybu failover](service-fabric-testability-scenarios.md#failover-test)— wersja scenariusza testu chaos przeznaczonego partycji określonej usługi, pozostawiając nie dotyczy innych usług.

## <a name="clusters"></a>Klastry
[Klaster usługi Service Fabric](service-fabric-deploy-anywhere.md) jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym wdraża się mikrousługi i nimi zarządza. Klastry mogą obejmować nawet tysiące maszyn. Komputer lub maszynę Wirtualną, która jest częścią klastra jest nazywany węzłem klastra. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają swoje właściwości — na przykład właściwości dotyczące umieszczania. Każdy komputer lub maszyna wirtualna jest automatycznie uruchamiana usługa `FabricHost.exe`, której zacznie działać po rozruchu, a następnie uruchamia dwa pliki wykonywalne: Fabric.exe i FabricGateway.exe. Te dwa pliki wykonywalne składają się na węźle. Dla scenariuszy testowania, może obsługiwać wiele węzłów na jednym komputerze lub maszynie Wirtualnej, uruchamiając wiele wystąpień `Fabric.exe` i `FabricGateway.exe`.

Klastrów sieci szkieletowej usług mogą być tworzone na maszynach wirtualnych lub fizycznych z systemem Windows Server lub Linux. Możliwe jest wdrażanie i uruchamianie aplikacji sieci szkieletowej usług w każdym środowisku, w którym znajduje się zestaw komputerów systemu Windows Server lub Linux, które są połączone ze sobą: lokalnymi, Microsoft Azure lub u innego dostawcy chmury.

Następujące Microsoft Virtual Academy wideo w tym artykule opisano klastrów sieci szkieletowej usług: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Klastry na platformie Azure
Działające klastry usługi sieć szkieletowa usług Azure zapewnia integrację z innymi funkcje platformy Azure i usługi, co sprawia, że operacje i zarządzania klastrem łatwiejsze i bardziej niezawodny. Klaster jest zasobów usługi Azure Resource Manager, więc można modelu klastrów, takich jak innymi zasobami na platformie Azure. Menedżer zasobów umożliwia również łatwe zarządzanie wszystkie zasoby używane przez klaster jako pojedyncza jednostka. Klastry na platformie Azure są zintegrowane z usługą Diagnostyka Azure i analizy dzienników. Typy węzłów klastra są [zestawy skalowania maszyny wirtualnej](/azure/virtual-machine-scale-sets/index), więc jest wbudowane funkcje skalowania automatycznego.

Można utworzyć klaster na platformie Azure za pośrednictwem [portalu Azure](service-fabric-cluster-creation-via-portal.md), z [szablonu](service-fabric-cluster-creation-via-arm.md), lub z [programu Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Sieć szkieletowa usług w systemie Linux pozwala na tworzenie, wdrażanie i zarządzanie wysoce skalowalne, wysoko dostępne aplikacje w systemie Linux, tak jak w systemie Windows. Struktury sieci szkieletowej usług (Reliable Services i Reliable Actors) są dostępne w języku Java w systemie Linux, oprócz C# (.NET Core). Można również tworzyć [usługi pliku wykonywalnego gościa](service-fabric-guest-executables-introduction.md) z dowolnego języka lub struktury. Organizowanie kontenery Docker jest również obsługiwany. Kontenery docker można uruchamiać pliki wykonywalne gościa lub macierzysty usług sieci szkieletowej usług, korzystających z platformy sieć szkieletowa usług. Aby uzyskać więcej informacji, przeczytaj o [sieci szkieletowej usług w systemie Linux](service-fabric-deploy-anywhere.md).

Brak niektórych funkcji, które są obsługiwane w systemie Windows, ale nie w systemie Linux. Aby dowiedzieć się więcej, przeczytaj [różnice między sieci szkieletowej usług w systemie Linux i Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Klastry autonomiczne
Usługa Service Fabric realizuje pakietu instalacyjnego do utworzenia autonomicznej usługi sieć szkieletowa klastrów lokalnymi lub u innego dostawcy chmury. Klastry autonomicznego nadaj swobody hosta klastra w miejscu. Zgodność ze standardami lub przepisami ograniczenia podlega danych, czy chcesz zachować dane lokalne, może obsługiwać własny klastra i aplikacji. Aplikacje sieci szkieletowej usług mogą działać w wielu środowiskach hostingu bez zmian, więc swoją wiedzę na temat tworzenia aplikacji są przenoszone z jednego środowiska hostingu do innego. 

[Tworzenie pierwszego autonomicznej klastra sieci szkieletowej usług](service-fabric-get-started-standalone-cluster.md)

Klastry z systemem Linux autonomiczny nie są jeszcze obsługiwane.

### <a name="cluster-security"></a>Zabezpieczenia klastra
Aby zapobiec nieautoryzowanemu połączenie z klastrem, szczególnie w przypadku, gdy ma ona obciążeń produkcyjnych na nim uruchomione, musi być zabezpieczona klastrów. Chociaż można utworzyć klaster niezabezpieczoną, to pozwala użytkowników anonimowych się z nim połączyć, jeśli punkty końcowe zarządzania są dostępne do publicznego Internetu. Nie można później włączyć zabezpieczeń w klastrze niezabezpieczona: zabezpieczeń klastra jest włączony podczas tworzenia klastra.

Dostępne są następujące scenariusze zabezpieczeń klastra:
* Zabezpieczenia węzła do węzła
* Węzeł klienta zabezpieczeń
* Kontrola dostępu oparta na rolach (RBAC)

Aby uzyskać więcej informacji, przeczytaj [Secure klastra](service-fabric-cluster-security.md).

### <a name="scaling"></a>Skalowanie
Po dodaniu nowych węzłów do klastra usługi sieć szkieletowa rebalances replik partycji i wystąpień przez zwiększenie liczby węzłów. Ogólne poprawia wydajność aplikacji i zmniejsza rywalizacji o dostęp do pamięci. Jeśli nie są wydajnie używane węzłów w klastrze, można zmniejszyć liczbę węzłów w klastrze. Sieć szkieletowa usług ponownie rebalances replik partycji i wystąpień między zmniejszyć liczbę węzłów w celu lepszego wykorzystania sprzętu na każdym węźle. Możesz skalować klastrów w systemie Azure albo [ręcznie](service-fabric-cluster-scale-up-down.md) lub [programowo](service-fabric-cluster-programmatic-scaling.md). Autonomiczny klastrów mogą być skalowane [ręcznie](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Uaktualnienia klastra
Okresowo są wydawane nowe wersje środowiska uruchomieniowego platformy Service Fabric. Wykonaj uaktualnienia środowiska uruchomieniowego lub sieci szkieletowej, w klastrze, aby zawsze działają [obsługiwana wersja](service-fabric-support.md). Oprócz uaktualnienia sieci szkieletowej należy zaktualizować konfigurację klastra, takich jak certyfikaty lub porty aplikacji.

Klaster sieci szkieletowej usług jest z zasobem, który jest właścicielem, ale jest częściowo zarządzany przez firmę Microsoft. Microsoft jest odpowiedzialny za stosowanie poprawek do podstawowego systemu operacyjnego i przeprowadzanie uaktualnienia sieci szkieletowej w klastrze. Możesz ustawić klastra do otrzymywania uaktualnień automatyczne sieci szkieletowej, gdy firma Microsoft publikuje nową wersję, lub wybrać opcję Wybierz wersję obsługiwanych sieci szkieletowej, która ma. Uaktualnienia sieci szkieletowej i konfiguracji można ustawić za pomocą portalu Azure lub za pomocą Menedżera zasobów. Aby uzyskać więcej informacji, przeczytaj [uaktualnienia klastra usługi sieć szkieletowa](service-fabric-cluster-upgrade.md). 

Zasób jest klaster z autonomicznej tego posiadana całkowicie. Jest odpowiedzialny za stosowanie poprawek do podstawowego systemu operacyjnego i zainicjowaniu uaktualnienia sieci szkieletowej. Jeśli można nawiązać połączenia z klastrem [ https://www.microsoft.com/download ](https://www.microsoft.com/download), można ustawić z klastrem w celu automatycznego pobrania i udostępnić nowy pakiet środowiska uruchomieniowego platformy Service Fabric. Następnie może zainicjować aktualizację. Jeśli nie ma dostępu do klastra [ https://www.microsoft.com/download ](https://www.microsoft.com/download), możesz ręcznie pobrać nowy pakiet obsługi z maszyny podłączonej do Internetu i następnie zainicjować aktualizację. Aby uzyskać więcej informacji, przeczytaj [uaktualnienia klastra usługi sieć szkieletowa autonomiczny](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitorowanie kondycji
Sieć szkieletowa usług wprowadza [model kondycji](service-fabric-health-introduction.md) umożliwiającą Flaga zła klastra i warunki aplikacji na konkretnych obiektów (na przykład węzłów klastra i repliki usługi). Model kondycji używa Raporty kondycji (składników systemu i watchdogs). Celem jest łatwe i szybkie diagnozowanie i naprawy. Moduły zapisujące usługi należy wziąć pod uwagę góry o kondycji i sposobie [projektowania raportowania kondycji](service-fabric-report-health.md#design-health-reporting). Wszelkie warunek, który może mieć wpływ na kondycji należy podać, zwłaszcza, jeśli może pomóc problemów flagi bliski katalogu głównego. Informacje o kondycji, można zaoszczędzić czas i nakładu pracy na analizie raz usługi i debugowanie jest uruchomiona na dużą skalę w środowisku produkcyjnym.

Monitor Raporty sieci szkieletowej usług określone warunki zainteresowań. Zgłaszają przy użyciu tych warunków na podstawie ich lokalnego widoku. [Magazynu kondycji](service-fabric-health-introduction.md#health-store) agreguje dane kondycji wysyłane przez wszystkie raporty w celu ustalenia, czy jednostki są globalnie dobrej kondycji. Model jest przeznaczony do sformatowanego, elastyczne i łatwe w użyciu. Jakość raportów kondycji określa dokładność widoku kondycji klastra. Fałszywych alarmów, przedstawiających błędnie zła problemów może niekorzystnie wpłynąć na uaktualnienia lub innych usług używających danych kondycji. Przykładami takich usług są usługi repair i mechanizmów alertów. W związku z tym rozwagą jest potrzebna do zapewnienia raportów, które przechwytywania warunki odsetek w najlepszy możliwy sposób.

Raportowanie może odbywać się od:
* Monitorowane repliki usługi sieć szkieletowa usług lub wystąpienie.
* Wewnętrzny watchdogs wdrożony jako usługa sieci szkieletowej usług (na przykład sieci szkieletowej usług bezstanowych Usługa monitoruje warunki i generuje raporty). Watchdogs można wdrożyć na wszystkich węzłach lub mogą być koligowane z monitorowanej usługi.
* Wewnętrzny watchdogs uruchamianie w węzłach sieci szkieletowej usług, które nie są zaimplementowane jako usługi sieci szkieletowej usług.
* Watchdogs zewnętrznych, które sondowania zasobu z poza klaster sieci szkieletowej usług (na przykład monitorowania usługi, takiej jak Gomez).

Fabrycznej składniki sieci szkieletowej usług Raport kondycji na wszystkich jednostek w klastrze. [Raportów o kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) zapewniają wgląd w klastra i aplikacji funkcji i flagi błędów za pomocą kondycji. Dla aplikacji i usług systemowych raportów kondycji Sprawdź, czy jednostki są zaimplementowane i są działa prawidłowo z punktu widzenia środowiska uruchomieniowego platformy Service Fabric. Raporty nie zapewniają wszelkie monitorowanie kondycji logiki biznesowej usługi lub wykryć zawieszone procesy. Aby dodać określone informacje o kondycji logiki z usługą [zaimplementować raportowania kondycji niestandardowych](service-fabric-report-health.md) w usługach.

Sieć szkieletowa usług oferuje wiele sposobów [wyświetlanie raportów kondycji](service-fabric-view-entities-aggregated-health.md) zagregowane w magazynie kondycji:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) lub innych narzędzi wizualizacji.
* Zapytań o kondycję (za pośrednictwem [PowerShell](/powershell/module/ServiceFabric/), [CLI](service-fabric-sfctl.md), [C# interfejsów API klienta fabricclient z rolą](/dotnet/api/system.fabric.fabricclient.healthclient) i [interfejsów API klienta fabricclient z rolą Java](/java/api/system.fabric._health_client), lub [REST Interfejsy API](/rest/api/servicefabric)).
* Ogólne wysyła zapytanie to zwracany listę obiektów, które mają kondycji jako jedna z właściwości (przy użyciu programu PowerShell, interfejsu wiersza polecenia, interfejsy API lub REST).

W poniższym filmie Microsoft Virtual Academy opisano model kondycji sieci szkieletowej usług i sposobie ich użycia: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka
[Monitorowania i diagnostyki](service-fabric-diagnostics-overview.md) są krytyczne dla programowania, testowania i wdrażania aplikacji i usług w każdym środowisku. Rozwiązania sieci szkieletowej usług najlepiej podczas planowanie i implementowanie monitorowania i diagnostyki, które pomagają upewnij się, aplikacji i usług działają zgodnie z oczekiwaniami w środowisku projektowym lokalnej lub w środowisku produkcyjnym.

Główne cele monitorowania i diagnostyki są:

- Wykrywanie i diagnozowanie problemów dotyczących sprzętu i infrastruktury
- Wykrywaj problemy związane z oprogramowania i aplikacji, skrócić przestoje związane z usługi
- Podejmowanie świadomych zasobów i zmniejsza zużycie dysku operacje decyzji
- Optymalizowanie aplikacji, usług i infrastruktury
- Generuj informacje biznesowe i zidentyfikuj obszary wymagające poprawy
 
Ogólny przepływ pracy monitorowania i diagnostyki obejmuje trzy kroki:

1. Generowanie zdarzeń: na poziomie aplikacji / usługi infrastruktury (klaster), platformy i obejmuje to zdarzenia (dzienników, śledzenie zdarzeń niestandardowych)
2. Agregacja zdarzeń: generowanych zdarzeń muszą być zbierane i agregować przed mogą być wyświetlane
3. Analiza: zdarzeń muszą być wizualizowanego i jest dostępna w niektórych formacie, aby umożliwić analizę i wyświetlane w razie potrzeby

Wiele produktów są dostępne, który obejmuje te trzy obszary i wolnych do wyboru różnych technologii dla każdego. Aby uzyskać więcej informacji, przeczytaj [monitorowania i diagnostyki dla sieci szkieletowej usług Azure](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak utworzyć [klaster na platformie Azure](service-fabric-cluster-creation-via-portal.md) lub [autonomiczny klaster w systemie Windows](service-fabric-cluster-creation-for-windows-server.md).
* Spróbuj utworzyć usługę za pomocą usług [Reliable Services](service-fabric-reliable-services-quick-start.md) lub modelu programowania [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Dowiedz się, jak [migracji z usługi w chmurze](service-fabric-cloud-services-migration-differences.md).
* Dowiedz się, jak [monitorowanie i diagnozowanie usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Dowiedz się, jak [testowania aplikacji i usług](service-fabric-testability-overview.md).
* Dowiedz się, jak [zarządzania i organizowania zasobów klastra](service-fabric-cluster-resource-manager-introduction.md).
* Przejrzyj [przykłady usługi Service Fabric](http://aka.ms/servicefabricsamples).
* Dowiedz się więcej o [opcje pomocy technicznej usługi sieć szkieletowa](service-fabric-support.md).
* Odczyt [blogu zespołu](https://blogs.msdn.microsoft.com/azureservicefabric/) dla artykułów i anonsów.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
