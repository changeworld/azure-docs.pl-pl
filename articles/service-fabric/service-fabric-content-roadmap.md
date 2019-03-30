---
title: Dowiedz się więcej o usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Więcej informacji na temat podstawowych pojęć i główne obszary usługi Azure Service Fabric. Omówienie rozszerzonej usługi Service Fabric oraz sposób tworzenia mikrousług.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: atsenthi
ms.openlocfilehash: adbe101455bc200bc6be439920736b756f08b695
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667994"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Dlatego potrzebujesz więcej informacji na temat usługi Service Fabric?
Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi.  Usługa Service Fabric ma dużej powierzchni, jednak i jest dużo, aby dowiedzieć się więcej.  Ten artykuł zawiera streszczenie usługi Service Fabric i w tym artykule opisano podstawowe pojęcia programowania modeli i cyklem życia aplikacji, testowania, klastrów i monitorowanie kondycji. Odczyt [Przegląd](service-fabric-overview.md) i [co to są mikrousługi?](service-fabric-overview-microservices.md) wprowadzenie i jak usługi Service Fabric można utworzyć mikrousługi. Ten artykuł nie zawiera pełną listę zawartości, ale łącze do omówienie i wprowadzenie wprowadzenie artykułów dla każdego obszaru usługi Service Fabric. 

## <a name="core-concepts"></a>Kluczowe pojęcia
[Terminologia dotycząca usługi Service Fabric](service-fabric-technical-overview.md), [model aplikacji](service-fabric-application-model.md), i [obsługiwane modele programowania](service-fabric-choose-framework.md) zapewniają więcej pojęć i opisów, ale poniżej przedstawiono podstawowe informacje.

### <a name="design-time-application-type-service-type-application-package-and-manifest-service-package-and-manifest"></a>Czas projektowania: typ aplikacji, typ usługi, pakiet aplikacji i manifest pakietu usługi i manifestu
Typ aplikacji jest nazwa/wersja przypisane do kolekcji typów usług. To jest zdefiniowany w *ApplicationManifest.xml* pliku, który jest osadzony w katalogu pakietu aplikacji. Pakiet aplikacji jest następnie kopiowany do magazynu obrazów klastra usługi Service Fabric. Będzie można utworzyć aplikacji o nazwie tego typu aplikacji i następnie uruchamia w klastrze. 

Typ usługi jest nazwa/wersja przypisane pakiety kodu, pakiety danych oraz pakiety konfiguracji usługi. To jest zdefiniowany w pliku ServiceManifest.xml, który jest osadzony w katalogu pakietu usługi. Katalog pakietu usługi odwołuje się do następnie pakietu aplikacji *ApplicationManifest.xml* pliku. W ramach klastra po utworzeniu aplikacji o nazwie, można utworzyć nazwanej usługi z jednego z typów usług typu aplikacji. Typ usługi jest opisana przez jego *ServiceManifest.xml* pliku. Typ usługi składa się z kodu wykonywalnego typowe dla usługi konfiguracji ustawienia, które są ładowane w czasie wykonywania, i dane statyczne, które jest używane przez usługę.

![Typy aplikacji usługi Service Fabric i usługi][cluster-imagestore-apptypes]

Pakiet aplikacji jest katalogiem dysk zawierający typ aplikacji *ApplicationManifest.xml* pliku, który odwołuje się do pakietów usługi dla każdego typu usług, który tworzy typ aplikacji. Na przykład pakiet aplikacji dla typu aplikacji poczty e-mail może zawierać odwołania do pakietu usług kolejki, pakiet usługi frontonu i pakiet usługi bazy danych. Pliki w katalogu pakietu aplikacji są kopiowane do magazynu obrazów klastra usługi Service Fabric. 

Pakiet usługi jest katalogiem dysk zawierający typ usługi *ServiceManifest.xml* pliku, który odwołuje się kod, dane statyczne i pakiety konfiguracji dla typu usługi. Pliki w katalogu pakietu usługi odwołuje się typ aplikacji *ApplicationManifest.xml* pliku. Na przykład pakiet usługi mogłoby się odwoływać do kodu, dane statyczne i pakiety konfiguracji, które składają się z usługą bazy danych.

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Czas wykonywania: klastry i węzłów o nazwie aplikacji, usług, partycji i replik
[Klaster usługi Service Fabric](service-fabric-deploy-anywhere.md) jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym wdraża się mikrousługi i nimi zarządza. Klastry mogą obejmować nawet tysiące maszyn.

Maszyna lub maszyna wirtualna, która jest częścią klastra, jest nazywana węzłem. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają swoje właściwości — na przykład właściwości dotyczące umieszczania. Każda maszyna lub maszyna wirtualna jest automatycznie uruchamiana usługa Windows `FabricHost.exe`, która zacznie działać po rozruchu, a następnie uruchamia dwa pliki wykonywalne: `Fabric.exe` i `FabricGateway.exe`. Te dwa pliki wykonywalne tworzą węzła. Do rozwoju lub testowania scenariuszy może obsługiwać wiele węzłów na jednym komputerze lub maszynie Wirtualnej, uruchamiając wiele wystąpień `Fabric.exe` i `FabricGateway.exe`.

Aplikacja o nazwie to zbiór usług o nazwie, który wykonuje niektórych funkcji lub funkcje. Usługa wykonuje funkcję kompletne i autonomiczny (można uruchomić i Uruchom niezależnie od innych usług) i składa się z kodu, konfiguracji i danych. Po pakietu aplikacji jest kopiowany do magazynu obrazów, należy utworzyć wystąpienie aplikacji w klastrze, określając typ aplikacji pakietu aplikacji (przy użyciu jego nazwa/wersja). Każde wystąpienie typu aplikacji jest przypisana nazwa identyfikatora URI, który wygląda jak *Service fabric: / MyNamedApp*. W ramach klastra możesz utworzyć wiele aplikacji o nazwie z typem pojedynczej aplikacji. Można również utworzyć aplikacje o nazwie z typy innej aplikacji. Każda aplikacja o nazwie jest niezależne zarządzane i kontrolą wersji.

Po utworzeniu aplikacji o nazwie można utworzyć wystąpienia jednego z jej typów usługi (service o nazwie) w klastrze, określając typ usługi (za pomocą jego nazwa/wersja). Każde wystąpienie typ usługi ma przypisaną nazwę identyfikatora URI o określonym zakresie w swojej aplikacji o nazwie w identyfikatorze URI. Na przykład, jeśli tworzysz "Moja_baza_danych" o nazwie usługi w ramach "MyNamedApp" o nazwie aplikacji, identyfikator URI wygląda: *Service fabric: / MyNamedApp/mojabazadanych*. W przypadku aplikacji o nazwie można utworzyć co najmniej jedną usługę o nazwie. Każda usługa o nazwie może mieć własny schemat partycji i liczby wystąpień/replik. 

Istnieją dwa typy usług: stanowe i bezstanowe. Usługi bezstanowej nie należy przechowywać stanu w ramach usługi. Usługi bezstanowej masz nie pojemności magazynu trwałego na wszystkich ani nie przechowują trwały stan usługi magazynu zewnętrznego, takie jak usługi Azure Storage, Azure SQL Database lub Azure Cosmos DB. Usługa stanowa zapisuje stan w ramach usługi i używa elementów Reliable Collections lub elementów Reliable Actors modeli programowania, aby zarządzać stanem. 

Podczas tworzenia nazwaną usługę, należy określić schemat partycji. Usługi z dużą ilością stanu podzielenie danych między partycjami. Każda partycja jest odpowiedzialny za część pełny stan usługi, która jest dystrybuowane między węzłami klastra.  

Na poniższym diagramie przedstawiono relację między aplikacjami i wystąpieniami usługi, partycji i replik.

![Partycje i repliki w ramach usługi][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Partycjonowanie, skalowanie i dostępność
[Partycjonowanie](service-fabric-concepts-partitioning.md) nie jest unikatowy dla usługi Service Fabric. Popularną formą partycjonowania jest partycjonowanie danych i dzielenia aplikacji. Usługi stanowe z dużą ilością stanu podzielenie danych między partycjami. Każda partycja jest odpowiedzialny za część pełny stan usługi. 

Replik każdej partycji są dystrybuowane między węzłami klastra, co umożliwia Państwu nazwaną usługę [skalowania](service-fabric-concepts-scalability.md). Jak wzrostem wymagań danych partycji rozwoju i usługi Service Fabric rebalances partycji na węzłach w celu efektywnego wykorzystania zasobów sprzętowych. Po dodaniu nowych węzłów do klastra usługi Service Fabric będzie przeprowadzić ponowne równoważenie replik partycji na większą liczbę węzłów. Ogólna zwiększa wydajność aplikacji i zmniejsza rywalizacji o dostęp do pamięci. Jeśli nie są wydajnie używane węzły w klastrze, możesz zmniejszyć liczbę węzłów w klastrze. Usługa Service Fabric ponownie rebalances replik partycji na obniżenie liczby węzłów, aby lepiej wykorzystać możliwości sprzętu na każdym węźle.

W ramach partycji bezstanowej usługi o nazwie mają wystąpień, a usług stanowych o nazwie replik. Zazwyczaj bezstanowej usługi o nazwie istniało tylko jednej partycji, ponieważ mają one nie stanu wewnętrznego. Podaj wystąpienia partycji [dostępności](service-fabric-availability-services.md). Jeśli jedno wystąpienie ulegnie awarii, inne instancje kontynuował normalne działanie, a następnie usługi Service Fabric tworzy nowe wystąpienie. Stanowe o nazwie usługi utrzymanie ich stan w obrębie repliki i każda partycja ma swój własny zestawu replik. Operacje odczytu i zapisu są wykonywane w jednej z replik (nazywany podstawowym). Zmiany stanu z zapisu operacji są replikowane do wielu innych replik (nazywane aktywne pomocnicze bazy danych). Replika ulegnie awarii, Usługa Service Fabric tworzy nową replikę z istniejących replik.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Mikrousługi stanowe i bezstanowe dla usługi Service Fabric
Usługa Service Fabric umożliwia tworzenie aplikacji składających się z mikrousług lub kontenerów. Mikrousługi bezstanowe (na przykład bramy protokołów i internetowe serwery proxy) nie utrzymują modyfikowalnego stanu poza żądaniem i odpowiedzią serwera. Przykładem usługi bezstanowej są procesy robocze usług Azure Cloud Services. Mikrousługi stanowe (na przykład konta użytkowników, bazy danych, urządzenia, koszyki zakupów i kolejki) utrzymują modyfikowalny, autorytatywny stan poza żądaniem i odpowiedzią. Współczesne aplikacje internetowe łączą w sobie mikrousługi stanowe i bezstanowe. 

Ważną cechą wyróżniającą usługę Service Fabric jest koncentracja na tworzeniu usług stanowych przy użyciu [wbudowanych modeli programowania](service-fabric-choose-framework.md) lub konteneryzowanych usług stanowych. [Scenariusze zastosowania](service-fabric-application-scenarios.md) opisują sytuacje, w których używane są usługi stanowe.

Dlaczego warto mieć mikrousług stanowych wraz z tymi o bezstanowa? Dwie główne przyczyny są następujące:

* Można tworzyć wysokiej przepływności, małego opóźnienia, odpornych przetwarzania transakcji online (OLTP) usług poprzez zapewnienie kod i dane blisko Ciebie na tym samym komputerze. Niektóre przykłady są interaktywne sklepów, wyszukiwania, systemów Internetu rzeczy (IoT), systemów obrotu, systemy wykrywania oszustw i przetwarzania kart kredytowych i zarządzanie rekordami osobistych.
* Można Uproszczenie projektowania aplikacji. Mikrousługi stanowe eliminuje potrzebę dodatkowe kolejki i pamięci podręczne, które są tradycyjnie wymagane, aby sprostać wymaganiom dostępność i czas oczekiwania czysto bezstanowe aplikacji. Usługi stanowe są naturalnie wysokiej dostępności i małym opóźnieniem, co zmniejsza liczbę ruchomych części, aby zarządzać w aplikacji jako całości.

## <a name="supported-programming-models"></a>Obsługiwane modele programowania
Usługa Service Fabric oferuje wiele sposobów, aby zapisać i zarządzania usługami. Usługi mogą używać interfejsów API usługi Service Fabric, w pełni wykorzystać funkcje platformy i struktury aplikacji. Usługi mogą być również wszelkie skompilowany program wykonywalny napisane w dowolnym języku i hostowanych w klastrze usługi Service Fabric. Aby uzyskać więcej informacji, zobacz [obsługiwane modele programowania](service-fabric-choose-framework.md).

### <a name="containers"></a>Containers
Domyślnie Usługa Service Fabric wdraża i aktywuje usługi jako procesów. Usługa Service Fabric można także wdrożyć usługi w [kontenery](service-fabric-containers-overview.md). Co ważniejsze można łączyć usługi w procesach i usługi w kontenerach w tej samej aplikacji. Usługa Service Fabric obsługuje wdrażanie kontenerów systemu Linux i kontenerów Windows w systemie Windows Server 2016. Możesz wdrożyć istniejących aplikacji, usług bezstanowych lub stanowych usługi w kontenerach. 

### <a name="reliable-services"></a>Reliable Services
[Usług Reliable Services](service-fabric-reliable-services-introduction.md) to uproszczone środowisko do tworzenia usług, które integrują się z platformy usługi Service Fabric oraz korzystać z pełnego zestawu funkcji platformy. Usług Reliable Services może być bezstanowe (podobny do większości platform usług, takich jak serwery sieci web lub role proces roboczy w usługach Azure Cloud Services), gdzie stan są utrwalane w rozwiązaniu zewnętrznych, takich jak baza danych Azure lub usługi Azure Table Storage. Usług Reliable Services może być również stanowa, których stan jest zachowywany bezpośrednio w usłudze, używając elementów Reliable Collections. Wykonano stanu [o wysokiej dostępności](service-fabric-availability-services.md) przez funkcję replikacji i rozprowadzane za pośrednictwem [partycjonowanie](service-fabric-concepts-partitioning.md), wszystkie zarządzane automatycznie przez usługę Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Zbudowany na podstawie usług Reliable Services, [Reliable Actor](service-fabric-reliable-actors-introduction.md) framework to platforma aplikacji, która implementuje wzorzec wirtualnego aktora, oparte na wzorcu projektowym aktora. Framework Reliable Actor używa niezależnych zespołów mocy obliczeniowej i stan z jednowątkowe wykonywania o nazwie aktorów. Reliable Actors, który framework zawiera wbudowane komunikacji dla aktorów i wstępnie ustawiony stan trwałości i skalowalnego w poziomie konfiguracji.

### <a name="aspnet-core"></a>ASP.NET Core
Usługa Service Fabric jest zintegrowany z [platformy ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) jako najwyższej klasy model programowania do tworzenia aplikacji sieci web i interfejsów API.  Platformy ASP.NET Core można używać na dwa różne sposoby, w usłudze Service Fabric:

- Hostowany jako wykonywalnej gościa. Służy to głównie uruchamianie istniejących aplikacji programu ASP.NET Core w usłudze Service Fabric, bez konieczności wprowadzania zmian kodu.
- Uruchamiane wewnątrz niezawodnej usługi. Zezwala na lepszą integrację ze środowiskiem uruchomieniowym usługi Service Fabric i umożliwia usług stanowych platformy ASP.NET Core.

### <a name="guest-executables"></a>Pliki wykonywalne gościa
A [pliku wykonywalnego gościa](service-fabric-guest-executables-introduction.md) jest istniejące, dowolnego pliku wykonywalnego (napisane w dowolnym języku) hostowanych w klastrze usługi Service Fabric wraz z innymi usługami. Pliki wykonywalne gościa integrują się bezpośrednio z interfejsów API usługi Service Fabric. Jednak mogą nadal korzystać z funkcji, które oferuje platforma, takie jak niestandardowe kondycji i obciążenia, raportowanie i usługi odnajdywania przez wywołanie interfejsów API REST. Mają one również obsługę cyklu życia pełnej aplikacji. 

## <a name="application-lifecycle"></a>Cykl życia aplikacji
Zgodnie z innymi platformami aplikacji w usłudze Service Fabric zwykle odbywa się przez następujące fazy: projektowania, rozwoju, testowania, wdrażania, uaktualnianie, obsługi i usuwania. Usługa Service Fabric zapewnia najwyższej jakości pomoc techniczna dla całego cyklu życia aplikacji z aplikacji w chmurze, od projektowania do wdrażania, zarządzania infrastrukturą i konserwacji do ostatecznego wycofania. Model usługi umożliwia kilku różnych ról niezależnie uczestniczyć w cyklu życia aplikacji. [Cykl życia aplikacji usługi Service Fabric](service-fabric-application-lifecycle.md) zawiera omówienie interfejsów API i jak są one używane przez różne role, w fazach cyklu życia aplikacji usługi Service Fabric. 

Cykl życia całej aplikacji mogą być zarządzane przy użyciu [poleceń cmdlet programu PowerShell](/powershell/module/ServiceFabric/), [poleceń interfejsu wiersza polecenia](service-fabric-sfctl.md), [interfejsów API języka C#](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [interfejsów API języka Java](/java/api/overview/azure/servicefabric), i [ Interfejsy API REST](/rest/api/servicefabric/). Możesz również skonfigurować ciągłej integracji/ciągłego wdrażania potoki, za pomocą narzędzi takich jak [potoki Azure](service-fabric-set-up-continuous-integration.md) lub [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

## <a name="test-applications-and-services"></a>Testowanie aplikacji i usług
Aby utworzyć naprawdę usług w skali chmury, koniecznie sprawdź, czy aplikacje i usługi może wytrzymać rzeczywiste błędy. Usługa analizy błędów jest przeznaczony do testowania usług, które są wbudowane w usłudze Service Fabric. Za pomocą [usługa analizy błędów](service-fabric-testability-overview.md), można wywołać istotne błędy i uruchamianie scenariuszy ukończenia testowej względem aplikacji. Te błędy i scenariuszy wykonywania i zweryfikować liczne Stany i przejścia, które usługa będzie wystąpić w okresie swojego istnienia, wszystko to w sposób kontrolowany, bezpieczne i zgodne.

[Akcje](service-fabric-testability-actions.md) obiektem docelowym usługi do testowania przy użyciu poszczególne błędy. Deweloper usługi umożliwia je jako bloki konstrukcyjne pisania złożonych zadań. Symulowane błędów należą do nich:

* Uruchom ponownie węzeł, aby zasymulować dowolną liczbę sytuacji, w którym maszyna lub maszyna wirtualna jest uruchamiana ponownie.
* Przenieś repliki usługi stanowej w celu symulowania równoważenia obciążenia i trybu failover lub uaktualnienie aplikacji.
* Wywoływanie utraty kworum na usługi stanowej, aby uniknąć sytuacji, w których nie można kontynuować operacje zapisu, ponieważ nie ma wystarczającej liczby replik "wykonaj kopię zapasową" i "secondary" nowe dane.
* Wywoływanie utraty danych w usługi stanowej, aby uniknąć sytuacji, gdy wszystkie stany w pamięci całkowicie zostaną wyczyszczone.

[Scenariusze](service-fabric-testability-scenarios.md) złożonych operacji składają się z co najmniej jednej akcji. Usługa analizy błędów zapewnia dwie wbudowane pełne scenariusze:

* [Scenariusz chaos](service-fabric-controlled-chaos.md)-symuluje błędów ciągłe, przeplotem (łagodne i nieprawidłowego) w całym klastrze przez dłuższy czas.
* [Pracy awaryjnej](service-fabric-testability-scenarios.md#failover-test)-wersję chaos scenariusza testu, który jest przeznaczony dla partycji określonej usługi, pozostawiając inne usługi nie ma wpływu.

## <a name="clusters"></a>Klastry
[Klaster usługi Service Fabric](service-fabric-deploy-anywhere.md) jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym wdraża się mikrousługi i nimi zarządza. Klastry mogą obejmować nawet tysiące maszyn. Komputer lub maszynę Wirtualną, która jest częścią klastra, jest nazywana węzłem klastra. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają swoje właściwości — na przykład właściwości dotyczące umieszczania. Każda maszyna lub maszyna wirtualna jest automatycznie uruchamiana usługa `FabricHost.exe`, która zacznie działać po rozruchu, a następnie uruchamia dwa pliki wykonywalne: Fabric.exe i FabricGateway.exe. Te dwa pliki wykonywalne tworzą węzła. W przypadku testowania scenariuszy może obsługiwać wiele węzłów na jednym komputerze lub maszynie Wirtualnej, uruchamiając wiele wystąpień `Fabric.exe` i `FabricGateway.exe`.

Można tworzyć klastry usługi Service Fabric na maszynach wirtualnych lub fizycznych z systemem Windows Server lub Linux. Masz możliwość wdrażania i uruchamiania aplikacji usługi Service Fabric w każdym środowisku, w którym masz zestaw komputerów Windows Server lub Linux, które są połączone ze sobą: w środowisku lokalnym, Microsoft Azure lub wśród dostawców chmury.

### <a name="clusters-on-azure"></a>Klastry na platformie Azure
Z klastrami usługi Service Fabric na platformie Azure zapewnia integrację z innych funkcji platformy Azure i usług, co sprawia, że operacje i zarządzania klastrem łatwiejsze i bardziej niezawodne. Klaster jest zasobem usługi Azure Resource Manager, więc umożliwia modelowanie klastrów, takich jak wszystkich innych zasobów na platformie Azure. Resource Manager umożliwia również łatwe zarządzanie wszelkie zasoby używane przez klaster jako pojedyncza jednostka. Klastry na platformie Azure są zintegrowane z usługą Diagnostyka Azure i dzienniki usługi Azure Monitor. Typy węzłów klastra są [zestawy skalowania maszyn wirtualnych](/azure/virtual-machine-scale-sets/index), więc jest wbudowane funkcje automatycznego skalowania.

Można utworzyć klaster na platformie Azure za pośrednictwem [witryny Azure portal](service-fabric-cluster-creation-via-portal.md), z [szablonu](service-fabric-cluster-creation-via-arm.md), lub z [programu Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Usługa Service Fabric w systemie Linux umożliwia tworzenie, wdrażanie i zarządzanie wysoko dostępnej i skalowalnej aplikacji w systemie Linux, tak samo jak na Windows. Platformy usługi Service Fabric (usług Reliable Services i Reliable Actors) są dostępne w języku Java w systemie Linux, oprócz języka C# (.NET Core). Możesz również tworzyć [usługi pliku wykonywalnego gościa](service-fabric-guest-executables-introduction.md) za pomocą dowolnego języka lub platformy. Organizowanie kontenerów platformy Docker jest również obsługiwany. Kontenery platformy docker można uruchamiać pliki wykonywalne gościa lub natywny usługi Service Fabric, korzystających z platformy usługi Service Fabric. Aby uzyskać więcej informacji, przeczytaj o [usługi Service Fabric w systemie Linux](service-fabric-deploy-anywhere.md).

Wprowadzono kilka funkcji, które są obsługiwane w Windows, ale nie w systemie Linux. Aby dowiedzieć się więcej, przeczytaj [różnice między usługą Service Fabric w systemie Linux i Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Klastry autonomiczne
Usługa Service Fabric udostępnia pakiet instalacyjny, w celu utworzenia autonomicznego usługi Service Fabric clusters lokalnie lub na wszystkich dostawców chmury. Klastry autonomiczne umożliwianie użytkownikom do obsługi klastra, wszędzie tam, gdzie chcesz. Jeśli Twoje dane są zgodności lub ograniczenia prawne lub chcesz zachować dane lokalne, może obsługiwać własnego klastra i aplikacji. Aplikacje usługi Service Fabric można uruchomić w wielu środowiskach hostingu bez wprowadzania zmian, więc swojej wiedzy na temat tworzenia aplikacji są przenoszone z jednego środowiska hostingu do innego. 

[Tworzenie pierwszego autonomicznego klastra usługi Service Fabric](service-fabric-cluster-creation-for-windows-server.md)

Klastry autonomiczne systemu Linux nie są jeszcze obsługiwane.

### <a name="cluster-security"></a>Zabezpieczenia klastra
Klastry muszą być zabezpieczone uniemożliwiające nieupoważnionym użytkownikom łączenie z klastrem, szczególnie w przypadku, gdy ma ona obciążeń produkcyjnych na nim. Chociaż istnieje możliwość utworzenia z niezabezpieczonym klastrem, spowoduje to pozwala użytkownikom anonimowym na nawiązać z nim w przypadku punktów końcowych zarządzania są dostępne do publicznego Internetu. Nie jest możliwe później włączyć zabezpieczenia niezabezpieczonym klastrem: zabezpieczenia klastra jest włączona w momencie tworzenia klastra.

Dostępne są następujące scenariusze zabezpieczeń klastra:
* Węzeł węzeł zabezpieczeń
* Węzeł klienta zabezpieczeń
* Kontrola dostępu oparta na rolach (RBAC)

Aby uzyskać więcej informacji, przeczytaj [Zabezpieczanie klastra](service-fabric-cluster-security.md).

### <a name="scaling"></a>Skalowanie
Po dodaniu nowych węzłów do klastra usługi Service Fabric rebalances replik partycji i wystąpień na większą liczbę węzłów. Ogólna zwiększa wydajność aplikacji i zmniejsza rywalizacji o dostęp do pamięci. Jeśli nie są wydajnie używane węzły w klastrze, możesz zmniejszyć liczbę węzłów w klastrze. Usługa Service Fabric ponownie rebalances replik partycji i wystąpień na obniżenie liczby węzłów, aby lepiej wykorzystać możliwości sprzętu na każdym węźle. Tę wartość można skalować klastry na platformie Azure albo [ręcznie](service-fabric-cluster-scale-up-down.md) lub [programowo](service-fabric-cluster-programmatic-scaling.md). Klastry autonomiczne mogą być skalowane [ręcznie](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Uaktualnianie klastra
Okresowo są wydawane nowe wersje środowiska uruchomieniowego usługi Service Fabric. Wykonywać uaktualnienia środowiska uruchomieniowego lub sieci szkieletowej, w klastrze, dzięki czemu są zawsze uruchamiane [obsługiwana wersja](service-fabric-support.md). Tylko uaktualnienia sieci szkieletowej ale też aktualizować konfiguracji klastra, takie jak certyfikaty lub porty aplikacji.

Klaster usługi Service Fabric jest zasobem, który właścicielem, ale jest częściowo zarządzany przez firmę Microsoft. Firma Microsoft jest odpowiedzialna za poprawek podstawowego systemu operacyjnego i przeprowadzania uaktualnienia sieci szkieletowej w klastrze. Możesz ustawić do klastra, aby otrzymywać aktualizacje automatyczne sieci szkieletowej, gdy firma Microsoft publikuje nową wersję lub wybrać wersję obsługiwanych w sieci szkieletowej, która ma. Uaktualnienia sieci szkieletowej i konfiguracji można ustawić za pomocą witryny Azure portal lub za pomocą usługi Resource Manager. Aby uzyskać więcej informacji, przeczytaj [Uaktualnianie klastra usługi Service Fabric](service-fabric-cluster-upgrade.md). 

Autonomicznego klastra jest zasób, całkowicie własne. Odpowiedzialność stosowanie poprawek podstawowego systemu operacyjnego i Inicjowanie uaktualnienia sieci szkieletowej. Jeśli klaster może połączyć się z [ https://www.microsoft.com/download ](https://www.microsoft.com/download), można ustawić do klastra, aby automatycznie pobierać i aprowizować nowy pakiet środowiska uruchomieniowego usługi Service Fabric. Następnie będzie zainicjować uaktualnienie. Jeśli nie ma dostępu do klastra [ https://www.microsoft.com/download ](https://www.microsoft.com/download), możesz ręcznie pobrać nowy pakiet środowiska uruchomieniowego z maszynę połączoną z Internetem i następnie zainicjować uaktualnienie. Aby uzyskać więcej informacji, przeczytaj [Uaktualnianie klastra usługi Service Fabric autonomiczny](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitorowanie kondycji
Usługa Service Fabric wprowadza [modelu kondycji](service-fabric-health-introduction.md) przeznaczone do Flaga złej kondycji klastra i aplikacji warunki określonej jednostki (takie jak węzłów klastra i replik usług). Model kondycji używa raporty o kondycji (składników systemu i watchdogs). Celem jest łatwe i szybkie diagnozowanie i naprawy. Moduły zapisujące usługi, które należy wziąć pod ponoszonych z góry na temat kondycji i sposobie [projektowania, raportowanie stanu](service-fabric-report-health.md#design-health-reporting). Należy podać dowolny warunek, który może mieć wpływ na kondycję, zwłaszcza, jeśli może pomóc problemów flagi blisko głównego. Informacje o kondycji, można zaoszczędzić czas i nakład pracy na debugowanie i badania raz usługi została uruchomiona i działa na dużą skalę w środowisku produkcyjnym.

Monitor raporty usługi Service Fabric określone warunki zainteresowania. One raport na temat tych warunków, na podstawie ich widoku lokalnego. [Magazynu kondycji](service-fabric-health-introduction.md#health-store) agreguje dane kondycji wysyłane przez wszystkie raporty w celu ustalenia, czy jednostki są globalnie w dobrej kondycji. Model jest przeznaczony jako zaawansowane, elastyczne i łatwe w użyciu. Jakość raportów kondycji określa dokładność widoku kondycji klastra. Wyniki fałszywie dodatnie, które błędnie pokazują problemy w złej kondycji może niekorzystnie wpłynąć na uaktualnienia lub innych usług używających danych dotyczących kondycji. Przykłady takich usług to naprawy usługi i mechanizmy generowania alertów. W związku z tym rozwagą, konieczne są sporządzanie raportów, które przechwytują warunki publicznej w możliwie najlepszy sposób.

Raportowanie może odbywać się od:
* Monitorowane usługi Service Fabric service replik lub wystąpień.
* Wewnętrzny watchdogs wdrożony jako Usługa Service Fabric (na przykład usługi bezstanowej usługi Service Fabric monitoruje warunków, która wysyła raporty). Watchdogs można wdrożyć na wszystkich węzłach lub być skoligaconym do monitorowanej usługi.
* Wewnętrzny watchdogs uruchomienia w węzłach usługi Service Fabric, które nie są zaimplementowane jako usługi Service Fabric.
* Watchdogs zewnętrznych, które sondowania zasób z poza klastrem usługi Service Fabric (na przykład Usługa monitorowania takich jak Gomez).

Natychmiast po składniki usługi Service Fabric raportować kondycję na wszystkich jednostek w klastrze. [Raportów o kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) zapewniają widoczność klastrami i aplikacjami problemy oraz flagę funkcji, za pośrednictwem kondycji. Dla aplikacji i usług raportami kondycji systemu sprawdź, czy jednostki są implementowane i czy działa prawidłowo z punktu widzenia środowiska uruchomieniowego usługi Service Fabric. Raporty nie Podaj wszelkie monitorowanie kondycji logikę biznesową, usługi lub wykrywania zawieszone procesy. Aby dodać określone informacje o kondycji do usługi Logic Apps, [implementacji, raportowanie stanu niestandardowe](service-fabric-report-health.md) w usługach.

Usługa Service Fabric udostępnia wiele sposobów, aby [wyświetlanie raportów o kondycji](service-fabric-view-entities-aggregated-health.md) agregowane w magazynie kondycji:
* [Narzędzie Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) lub innych narzędzi do wizualizacji.
* Zapytania o kondycję (za pośrednictwem [PowerShell](/powershell/module/ServiceFabric/), [interfejsu wiersza polecenia](service-fabric-sfctl.md), [C# interfejsy API FabricClient](/dotnet/api/system.fabric.fabricclient.healthclient) i [Java interfejsy API FabricClient](/java/api/system.fabric), lub [REST Interfejsy API](/rest/api/servicefabric)).
* Ogólne kwerendy oznacza zwracaną listę obiektów, które mają kondycji jako jedna z właściwości (przy użyciu programu PowerShell, interfejsu wiersza polecenia, interfejsy API lub REST).

## <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka
[Monitorowanie i Diagnostyka](service-fabric-diagnostics-overview.md) mają kluczowe znaczenie dla programowania, testowania i wdrażania aplikacji i usług w każdym środowisku. Rozwiązania usługi Service Fabric działają najlepiej, podczas planowania i implementacji, monitorowania i diagnostyki, które pomagają upewnić się, aplikacje i usługi działają zgodnie z oczekiwaniami w środowisku programowania lokalnego lub w środowisku produkcyjnym.

Głównych celów monitorowania i diagnostyki są:

- Wykrywanie i diagnozowanie problemów sprzętu i infrastruktury
- Wykrywaj problemy związane z oprogramowania i aplikacji, zredukować przestoje usługi
- Omówienie zasobów zużycia i pomocy dysku podejmowanie decyzji dotyczących operacji
- Optymalizuj wydajność aplikacji, usług i infrastruktury
- Generowanie szczegółowych informacji biznesowych i zidentyfikować obszary ulepszeń
 
Ogólny przepływ pracy monitorowania i diagnostyki składa się z trzech kroków:

1. Generowanie zdarzeń: zdarzenia (dzienników, ślady, zdarzenia niestandardowe) w tym infrastruktury (klaster), platformy i poziom aplikacji / usługi
2. Zdarzenie agregacji: generowanych zdarzeń muszą być zbierane i zagregowane, zanim będzie można je wyświetlić
3. Analiza: zdarzenia muszą być wizualizowany i jest dostępny w niektórych formacie, aby zezwolić na potrzeby analizy i wyświetlania, zgodnie z potrzebami

Wiele produktów są dostępne, które obejmują te trzy obszary i masz swobodę wyboru różnych technologii dla każdego. Aby uzyskać więcej informacji, przeczytaj [monitorowania i diagnostyki dla usługi Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak utworzyć [klaster na platformie Azure](service-fabric-cluster-creation-via-portal.md) lub [autonomiczny klaster w systemie Windows](service-fabric-cluster-creation-for-windows-server.md).
* Spróbuj utworzyć usługę za pomocą usług [Reliable Services](service-fabric-reliable-services-quick-start.md) lub modelu programowania [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Dowiedz się, jak [migracji z usług w chmurze](service-fabric-cloud-services-migration-differences.md).
* Dowiedz się, jak [monitorowanie i diagnozowanie usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Dowiedz się, jak [testowania aplikacji i usług](service-fabric-testability-overview.md).
* Dowiedz się, jak [organizowanie zasobów klastra i zarządzanie nimi](service-fabric-cluster-resource-manager-introduction.md).
* Przeszukaj [przykłady usługi Service Fabric](https://aka.ms/servicefabricsamples).
* Dowiedz się więcej o [opcje pomocy technicznej usługi Service Fabric](service-fabric-support.md).
* Odczyt [blog zespołu](https://blogs.msdn.microsoft.com/azureservicefabric/) artykuły i anonsów.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
