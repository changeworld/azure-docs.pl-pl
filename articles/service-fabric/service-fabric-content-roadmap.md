---
title: Dowiedz się więcej o sieci szkieletowej usług Azure
description: Dowiedz się więcej o podstawowych pojęciach i głównych obszarach sieci szkieletowej usług Azure. Zawiera rozszerzone omówienie sieci szkieletowej usług i jak tworzyć mikrousługi.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 4e6e21f5f9ebfeddb5292e00dc8a929341e77372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458148"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Więc chcesz dowiedzieć się więcej o sieci szkieletowej usług?
Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi.  Usługa Fabric ma jednak dużą powierzchnię i jest wiele do nauczenia się.  Ten artykuł zawiera streszczenie sieci szkieletowej usług i opisano podstawowe pojęcia, modele programowania, cykl życia aplikacji, testowanie, klastry i monitorowanie kondycji. Przeczytaj [omówienie](service-fabric-overview.md) i [Co to są mikrousługi?](service-fabric-overview-microservices.md) Ten artykuł nie zawiera wyczerpującej listy zawartości, ale zawiera łącze do omówienia i rozpoczynania artykułów dla każdego obszaru sieci szkieletowej usług. 

## <a name="core-concepts"></a>Kluczowe pojęcia
[Terminologia sieci szkieletowej usług,](service-fabric-technical-overview.md) [model aplikacji](service-fabric-application-model.md)i [obsługiwane modele programowania](service-fabric-choose-framework.md) zawierają więcej pojęć i opisów, ale oto podstawy.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Czas projektowania: typ usługi, pakiet i manifest usługi, typ aplikacji, pakiet aplikacji i manifest
Typ usługi to nazwa/wersja przypisana do pakietów kodu usługi, pakietów danych i pakietów konfiguracyjnych. Jest to zdefiniowane w pliku ServiceManifest.xml. Typ usługi składa się z ustawień konfiguracji kodu wykonywalnego i usługi, które są ładowane w czasie wykonywania, oraz danych statycznych, które są używane przez usługę.

Pakiet usługi jest katalogiem dysku zawierającym plik ServiceManifest.xml typu usługi, który odwołuje się do kodu, danych statycznych i pakietów konfiguracyjnych dla typu usługi. Na przykład pakiet usługi może odwoływać się do kodu, danych statycznych i pakietów konfiguracyjnych, które tworzą usługę bazy danych.

Typ aplikacji to nazwa/wersja przypisana do kolekcji typów usług. Jest to zdefiniowane w pliku ApplicationManifest.xml.

![Typy aplikacji sieci szkieletowej usług i typy usług][cluster-imagestore-apptypes]

Pakiet aplikacji jest katalogiem dysku, który zawiera plik ApplicationManifest.xml typu aplikacji, który odwołuje się do pakietów usług dla każdego typu usługi, który składa się na typ aplikacji. Na przykład pakiet aplikacji dla typu aplikacji poczty e-mail może zawierać odwołania do pakietu usługi kolejki, pakietu usługi frontendu i pakietu usługi bazy danych.  

Pliki w katalogu pakietów aplikacji są kopiowane do magazynu obrazów klastra sieci szkieletowej usług. Następnie można utworzyć nazwaną aplikację z tego typu aplikacji, która następnie jest uruchamiana w klastrze. Po utworzeniu nazwanej aplikacji można utworzyć nazwaną usługę z jednego z typów usług typu aplikacji. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Czas wykonywania: klastry i węzły, nazwane aplikacje, nazwane usługi, partycje i repliki
[Klaster sieci szkieletowej usług](service-fabric-deploy-anywhere.md) to połączony z siecią zestaw maszyn wirtualnych lub fizycznych, na których mikrousługi są wdrażane i zarządzane. Klastry mogą obejmować nawet tysiące maszyn.

Maszyna lub maszyna wirtualna, która jest częścią klastra, jest nazywana węzłem. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają swoje właściwości — na przykład właściwości dotyczące umieszczania. Każdy komputer lub maszyna wirtualna ma `FabricHost.exe`usługę automatycznego uruchamiania systemu Windows, która rozpoczyna `Fabric.exe` `FabricGateway.exe`się po uruchomieniu, a następnie uruchamia dwa pliki wykonywalne: i . Te dwa pliki wykonywalne tworzą węzeł. W przypadku scenariuszy programowania lub testowania można obsługiwać wiele węzłów na `Fabric.exe` jednym `FabricGateway.exe`komputerze lub maszynie wirtualnej, uruchamiając wiele wystąpień i .

Nazwana aplikacja jest kolekcją nazwanych usług, która wykonuje określoną funkcję lub funkcje. Usługa wykonuje pełną i autonomiczną funkcję (można uruchomić i uruchomić niezależnie od innych usług) i składa się z kodu, konfiguracji i danych. Po skopiowaniu pakietu aplikacji do magazynu obrazów należy utworzyć wystąpienie aplikacji w klastrze, określając typ aplikacji pakietu aplikacji (przy użyciu jego nazwy/wersji). Każdemu wystąpieniu typu aplikacji jest przypisywana nazwa identyfikatora URI, która wygląda jak *fabric:/MyNamedApp*. W klastrze można utworzyć wiele nazwanych aplikacji z jednego typu aplikacji. Można również utworzyć nazwane aplikacje z różnych typów aplikacji. Każda nazwana aplikacja jest zarządzana i wersjona niezależnie.

Po utworzeniu nazwanej aplikacji można utworzyć wystąpienie jednego z jej typów usług (nazwanej usługi) w klastrze, określając typ usługi (przy użyciu jego nazwy/wersji). Każde wystąpienie typu usługi jest przypisywane nazwę identyfikatora URI o zakresie w ramach identyfikatora URI jego nazwanej aplikacji. Na przykład jeśli utworzysz usługę "MyDatabase" o nazwie "MyNamedApp", identyfikator URI wygląda następująco: *fabric:/MyNamedApp/MyDatabase*. W ramach nazwanej aplikacji można utworzyć jedną lub więcej nazwanych usług. Każda nazwana usługa może mieć swój własny schemat partycji i liczbę wystąpień/replik. 

Istnieją dwa rodzaje usług: bezstanowe i stanowe. Usługi bezstanowe nie przechowują stanu w ramach usługi. Usługi bezstanowe nie mają trwałego magazynu w ogóle lub przechowywać stan trwały w zewnętrznej usłudze magazynu, takich jak Usługa Azure Storage, Usługa Azure SQL Database lub Usługi Azure Cosmos DB. Usługa stanowa przechowuje stan w ramach usługi i używa niezawodnych kolekcji lub modeli programowania Reliable Actors do zarządzania stanem. 

Podczas tworzenia usługi o nazwie należy określić schemat partycji. Usługi z dużą ilością stanu podzielić dane między partycje. Każda partycja jest odpowiedzialna za część pełnego stanu usługi, która jest rozłożona na węzły klastra.  

Na poniższym diagramie przedstawiono relację między aplikacjami i wystąpień usługi, partycje i repliki.

![Partycje i repliki w usłudze][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Partycjonowanie, skalowanie i dostępność
[Partycjonowanie](service-fabric-concepts-partitioning.md) nie jest unikatowe dla sieci szkieletowej usług. Dobrze znaną formą partycjonowania jest partycjonowanie danych lub dzielenie na fragmenty. Usługi stanowe z dużą ilością stanu podzielić dane między partycje. Każda partycja jest odpowiedzialna za część pełnego stanu usługi. 

Repliki każdej partycji są rozłożone na węzły klastra, co umożliwia [skalowanie](service-fabric-concepts-scalability.md)stanu nazwanej usługi. Wraz ze wzrostem potrzeb danych partycje rosną, a usługa Fabric równoważy partycje między węzłami, aby efektywnie wykorzystywać zasoby sprzętowe. Jeśli dodasz nowe węzły do klastra, sieci szkieletowej usług będzie równoważenia replik partycji w zwiększonej liczbie węzłów. Ogólna wydajność aplikacji poprawia i rywalizacji o dostęp do pamięci zmniejsza. Jeśli węzły w klastrze nie są używane efektywnie, można zmniejszyć liczbę węzłów w klastrze. Sieci szkieletowej usług ponownie równoważy repliki partycji w zmniejszonej liczbie węzłów, aby lepiej wykorzystać sprzęt w każdym węźle.

W ramach partycji bezstanowe nazwane usługi mają wystąpienia, podczas gdy usługi o nazwie stanowe mają repliki. Zazwyczaj bezstanowe usługi o nazwie mają tylko jedną partycję, ponieważ nie mają stanu wewnętrznego. Wystąpienia partycji zapewniają [dostępność](service-fabric-availability-services.md). Jeśli jedno wystąpienie nie powiedzie się, inne wystąpienia nadal działają normalnie, a następnie sieć szkieletowa usług tworzy nowe wystąpienie. Stateful nazwanych usług zachować ich stan w replikach i każda partycja ma swój własny zestaw replik. Operacje odczytu i zapisu są wykonywane w jednej replice (zwanej podstawową). Zmiany stanu z operacji zapisu są replikowane do wielu innych replik (o nazwie Active Secondaries). Jeśli replika nie powiedzie się, sieci szkieletowej usług tworzy nową replikę z istniejących replik.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Mikrousługi stanowe i bezstanowe dla usługi Service Fabric
Usługa Service Fabric umożliwia tworzenie aplikacji składających się z mikrousług lub kontenerów. Mikrousługi bezstanowe (na przykład bramy protokołów i internetowe serwery proxy) nie utrzymują modyfikowalnego stanu poza żądaniem i odpowiedzią serwera. Przykładem usługi bezstanowej są procesy robocze usług Azure Cloud Services. Mikrousługi stanowe (na przykład konta użytkowników, bazy danych, urządzenia, koszyki zakupów i kolejki) utrzymują modyfikowalny, autorytatywny stan poza żądaniem i odpowiedzią. Współczesne aplikacje internetowe łączą w sobie mikrousługi stanowe i bezstanowe. 

Kluczowe zróżnicowanie z sieci szkieletowej usług jest jego silny nacisk na tworzenie usług stanowych, albo z [wbudowanych modeli programowania](service-fabric-choose-framework.md) lub konteneryzowanych usług stanowych. [Scenariusze zastosowania](service-fabric-application-scenarios.md) opisują sytuacje, w których używane są usługi stanowe.

Dlaczego mają stanowe mikrousługi wraz z bezstanowych? Dwa główne powody to:

* Usługi oltp (o wysokiej przepływności, małych opóźnieniach i transakcjach online (OLTP) można tworzyć, utrzymując kod i dane blisko na tym samym komputerze. Niektóre przykłady to interaktywne witryny sklepowe, wyszukiwanie, systemy Internetu rzeczy (IoT), systemy handlowe, systemy przetwarzania kart kredytowych i wykrywania oszustw oraz osobiste zarządzanie rekordami.
* Można uprościć projektowanie aplikacji. Mikrousługi stanowe usunąć potrzebę dodatkowych kolejek i pamięci podręcznych, które są tradycyjnie wymagane do rozwiązania wymagań dostępności i opóźnienia aplikacji czysto bezstanowe. Usługi stanowe są naturalnie wysokiej dostępności i małe opóźnienia, co zmniejsza liczbę ruchomych części do zarządzania w aplikacji jako całości.

## <a name="supported-programming-models"></a>Obsługiwane modele programowania
Usługa Service Fabric oferuje wiele sposobów pisania i zarządzania usługami. Usługi mogą korzystać z interfejsów API sieci szkieletowej usług, aby w pełni korzystać z funkcji platformy i struktur aplikacji. Usługi mogą być również dowolne skompilowany program wykonywalny napisany w dowolnym języku i hostowany w klastrze sieci szkieletowej usług. Aby uzyskać więcej informacji, zobacz [Obsługiwane modele programowania](service-fabric-choose-framework.md).

### <a name="containers"></a>Kontenery
Domyślnie sieci szkieletowej usług wdraża i aktywuje usługi jako procesy. Sieci szkieletowej usług można również wdrożyć usługi w [kontenerach.](service-fabric-containers-overview.md) Co ważne, można mieszać usługi w procesach i usługach w kontenerach w tej samej aplikacji. Usługa Sieci szkieletowej obsługuje wdrażanie kontenerów systemu Linux i kontenerów systemu Windows w systemie Windows Server 2016. W kontenerach można wdrożyć istniejące aplikacje, usługi bezstanowe lub usługi stanowe. 

### <a name="reliable-services"></a>Reliable Services
[Niezawodne usługi](service-fabric-reliable-services-introduction.md) to uproszczona struktura do pisania usług, które integrują się z platformą sieci szkieletowej usług i korzystają z pełnego zestawu funkcji platformy. Niezawodne usługi mogą być bezstanowe (podobnie jak większość platform usług, takich jak serwery sieci web lub role procesu roboczego w usługach w chmurze Azure), gdzie stan jest utrwalony w rozwiązaniu zewnętrznym, takim jak Usługa Azure DB lub Usługa Azure Table Storage. Niezawodne usługi mogą być również stanowe, gdzie stan jest utrwalony bezpośrednio w samej usłudze przy użyciu niezawodnych kolekcji. Stan jest [wysoce dostępny](service-fabric-availability-services.md) za pośrednictwem replikacji i dystrybuowane za pośrednictwem [partycjonowania,](service-fabric-concepts-partitioning.md)wszystkie zarządzane automatycznie przez sieci szkieletowej usług.

### <a name="reliable-actors"></a>Reliable Actors
Zbudowany na podstawie niezawodnych usług, [reliable actor](service-fabric-reliable-actors-introduction.md) framework jest framework aplikacji, która implementuje wzorzec aktora wirtualnego, na podstawie wzorca projektu aktora. Struktura niezawodnego aktora używa niezależnych jednostek obliczeniowych i stanu z wykonywaniem jednowątkowym o nazwie actors. Struktura niezawodnego aktora zapewnia wbudowaną komunikację dla aktorów i wstępnie ustawionych konfiguracji stanu i skalowania w poziomie.

### <a name="aspnet-core"></a>ASP.NET Core
Sieć szkieletowa usług integruje się z [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) jako model programowania pierwszej klasy do tworzenia aplikacji sieci web i interfejsu API.  ASP.NET Core może być używany na dwa różne sposoby w sieci szkieletowej usług:

- Hostowany jako plik wykonywalny gościa. Jest to używane przede wszystkim do uruchamiania istniejących aplikacji ASP.NET Core w sieci szkieletowej usług bez zmian kodu.
- Uruchom wewnątrz niezawodnej usługi. Umożliwia to lepszą integrację ze środowiska uruchomieniowego sieci szkieletowej usług i umożliwia stanowe usługi ASP.NET Core.

### <a name="guest-executables"></a>Pliki wykonywalne gościa
Plik [wykonywalny gościa](service-fabric-guest-executables-introduction.md) to istniejący, dowolny plik wykonywalny (napisany w dowolnym języku) hostowany w klastrze sieci szkieletowej usług obok innych usług. Pliki wykonywalne gościa nie integrują się bezpośrednio z interfejsami API sieci szkieletowej usług. Jednak nadal korzystają z funkcji, które oferuje platforma, takich jak niestandardowe raportowanie kondycji i obciążenia oraz wykrywalność usług, wywołując interfejsy API REST. Mają również pełną obsługę cyklu życia aplikacji. 

## <a name="application-lifecycle"></a>Cykl życia aplikacji
Podobnie jak w przypadku innych platform, aplikacja w sieci szkieletowej usług zwykle przechodzi przez następujące fazy: projektowanie, tworzenie, testowanie, wdrażanie, uaktualnianie, konserwacja i usuwanie. Usługa Service Fabric zapewnia najwyższej klasy obsługę pełnego cyklu życia aplikacji w chmurze, od tworzenia przez wdrażanie, codzienne zarządzanie i konserwację do ostatecznej likwidacji. Model usługi umożliwia kilka różnych ról do udziału niezależnie w cyklu życia aplikacji. [Cykl życia aplikacji sieci szkieletowej usług](service-fabric-application-lifecycle.md) zawiera omówienie interfejsów API i sposobu ich użycia przez różne role na wszystkich etapach cyklu życia aplikacji sieci szkieletowej usług. 

Całym cyklem życia aplikacji można zarządzać za pomocą [poleceń cmdlet programu PowerShell,](/powershell/module/ServiceFabric/) [poleceń interfejsu wiersza polecenia,](service-fabric-sfctl.md) [interfejsów API języka C#,](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) [interfejsów API języka Java](/java/api/overview/azure/servicefabric)i [interfejsów API REST](/rest/api/servicefabric/). Można również skonfigurować ciągłe potoki integracji/ciągłego wdrażania przy użyciu narzędzi, takich jak [potoki platformy Azure](service-fabric-set-up-continuous-integration.md) lub [Jenkins.](service-fabric-cicd-your-linux-applications-with-jenkins.md)

## <a name="test-applications-and-services"></a>Testowanie aplikacji i usług
Aby stworzyć usługi w skali chmury, ważne jest, aby sprawdzić, czy aplikacje i usługi mogą wytrzymać rzeczywiste awarie. Usługa analizy błędów jest przeznaczona do testowania usług, które są oparte na sieci szkieletowej usług. Za pomocą [usługi analizy usterek](service-fabric-testability-overview.md)można wywoływać znaczące błędy i uruchamiać kompletne scenariusze testów dla aplikacji. Te błędy i scenariusze wykonywania i sprawdzania poprawności wielu stanów i przejść, które usługa będzie doświadczenie przez cały okres jej istnienia, wszystko w sposób kontrolowany, bezpieczny i spójny.

[Akcje](service-fabric-testability-actions.md) są przeznaczone dla usługi do testowania przy użyciu poszczególnych błędów. Deweloper usługi można użyć tych jako bloków konstrukcyjnych do pisania skomplikowanych scenariuszy. Przykładami symulowanych usterek są:

* Uruchom ponownie węzeł, aby symulować dowolną liczbę sytuacji, w których komputer lub maszyna wirtualna jest ponownie uruchamiana.
* Przenieś replikę usługi stanowej, aby symulować równoważenie obciążenia, przewijanie w błąd lub uaktualnianie aplikacji.
* Wywołaj utratę kworum w usłudze stanowej, aby utworzyć sytuację, w której operacje zapisu nie mogą być kontynuowane, ponieważ nie ma wystarczającej liczby replik "kopii zapasowej" lub "pomocniczej", aby zaakceptować nowe dane.
* Wywołaj utratę danych w usłudze stanowej, aby utworzyć sytuację, w której cały stan w pamięci jest całkowicie wymazany.

[Scenariusze](service-fabric-testability-scenarios.md) są złożone operacje składające się z jednej lub więcej akcji. Usługa analizy błędów udostępnia dwa wbudowane pełne scenariusze:

* [Scenariusz chaosu](service-fabric-controlled-chaos.md)— symuluje ciągłe, przeplatane błędy (zarówno wdzięczne, jak i niewdzięczne) w całym klastrze przez dłuższy czas.
* [Scenariusz pracy awaryjnej](service-fabric-testability-scenarios.md#failover-test)— wersja scenariusza testu chaosu, który jest przeznaczony dla określonej partycji usługi, pozostawiając inne usługi bez zmian.

## <a name="clusters"></a>Klastry
[Klaster sieci szkieletowej usług](service-fabric-deploy-anywhere.md) to połączony z siecią zestaw maszyn wirtualnych lub fizycznych, na których mikrousługi są wdrażane i zarządzane. Klastry mogą obejmować nawet tysiące maszyn. Maszyna lub maszyna wirtualna, która jest częścią klastra jest nazywany węzłem klastra. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają swoje właściwości — na przykład właściwości dotyczące umieszczania. Każdy komputer lub maszyna wirtualna ma `FabricHost.exe`usługę automatycznego uruchamiania, która rozpoczyna się po rozruchu, a następnie uruchamia dwa pliki wykonywalne: Fabric.exe i FabricGateway.exe. Te dwa pliki wykonywalne tworzą węzeł. W przypadku scenariuszy testowych można obsługiwać wiele węzłów na jednym `Fabric.exe` komputerze `FabricGateway.exe`lub maszynie wirtualnej, uruchamiając wiele wystąpień i .

Klastry sieci szkieletowej usług można tworzyć na komputerach wirtualnych lub fizycznych z systemem Windows Server lub Linux. Można wdrażać i uruchamiać aplikacje sieci szkieletowej usług w dowolnym środowisku, w którym masz zestaw komputerów z systemem Windows Server lub Linux, które są ze sobą połączone: lokalnie, na platformie Microsoft Azure lub u dowolnego dostawcy chmury.

### <a name="clusters-on-azure"></a>Klastry na platformie Azure
Uruchamianie klastrów sieci szkieletowej usług na platformie Azure zapewnia integrację z innymi funkcjami i usługami platformy Azure, co sprawia, że operacje i zarządzanie klastrem są łatwiejsze i bardziej niezawodne. Klaster jest zasobem usługi Azure Resource Manager, dzięki czemu można modelować klastry, jak inne zasoby na platformie Azure. Menedżer zasobów zapewnia również łatwe zarządzanie wszystkimi zasobami używanymi przez klaster jako pojedyncza jednostka. Klastry na platformie Azure są zintegrowane z diagnostyką platformy Azure i dziennikami usługi Azure Monitor. Typy węzłów klastra są [zestawami skalowania maszyny wirtualnej,](/azure/virtual-machine-scale-sets/index)więc wbudowana jest funkcja skalowania automatycznego.

Klaster można utworzyć na platformie Azure za pośrednictwem [portalu Azure](service-fabric-cluster-creation-via-portal.md), na podstawie [szablonu](service-fabric-cluster-creation-via-arm.md)lub [programu Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Usługa Fabric w systemie Linux umożliwia tworzenie, wdrażanie i zarządzanie wysoce skalowalnymi aplikacjami w systemie Linux, tak jak w systemie Windows. Struktury sieci szkieletowej usług (niezawodne usługi i niezawodne podmioty) są dostępne w języku Java w systemie Linux, oprócz języka C# (.NET Core). Można również tworzyć [usługi wykonywalne gościa](service-fabric-guest-executables-introduction.md) z dowolnego języka lub struktury. Obsługiwane jest również organizowanie kontenerów platformy Docker. Kontenery platformy Docker mogą uruchamiać pliki wykonywalne gościa lub natywne usługi sieci szkieletowej usług, które używają struktur sieci szkieletowej usług. Aby uzyskać więcej informacji, przeczytaj o [sieci szkieletowej usługi w systemie Linux](service-fabric-deploy-anywhere.md).

Istnieje kilka funkcji, które są obsługiwane w systemie Windows, ale nie w systemie Linux. Aby dowiedzieć się więcej, przeczytaj [artykuł Różnice między siecią szkieletową usług w systemie Linux i Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Klastry autonomiczne
Sieć szkieletowa usług udostępnia pakiet instalacyjny do tworzenia autonomicznych klastrów sieci szkieletowej usług lokalnie lub u dowolnego dostawcy chmury. Klastry autonomiczne zapewniają swobodę hostowania klastra w dowolnym miejscu. Jeśli dane podlegają ograniczeniom zgodności lub ograniczeniom regulacyjnym lub chcesz zachować lokalne dane, możesz hostować własny klaster i aplikacje. Aplikacje sieci szkieletowej usług można uruchomić w wielu środowiskach hostingowych bez żadnych zmian, więc twoja wiedza na temat tworzenia aplikacji przenosi się z jednego środowiska hostingowego do drugiego. 

[Tworzenie pierwszego autonomicznego klastra usługi Service Fabric](service-fabric-cluster-creation-for-windows-server.md)

Autonomiczne klastry systemu Linux nie są jeszcze obsługiwane.

### <a name="cluster-security"></a>Zabezpieczenia klastra
Klastry muszą być zabezpieczone, aby uniemożliwić nieautoryzowanym użytkownikom łączenie się z klastrem, zwłaszcza gdy ma uruchomione obciążenia produkcyjne. Chociaż istnieje możliwość utworzenia klastra niezabezpieczonego, w ten sposób umożliwia anonimowym użytkownikom łączenie się z nim, jeśli punkty końcowe zarządzania są narażone na publiczny Internet. Nie można później włączyć zabezpieczeń w niezabezpieczonym klastrze: zabezpieczenia klastra są włączone w czasie tworzenia klastra.

Scenariusze zabezpieczeń klastra są następujące:
* Zabezpieczenia między węzłami
* Zabezpieczenia między klientami
* Kontrola dostępu oparta na rolach (RBAC)

Aby uzyskać więcej informacji, zobacz [Zabezpieczanie klastra](service-fabric-cluster-security.md).

### <a name="scaling"></a>Skalowanie
Jeśli dodasz nowe węzły do klastra, sieci szkieletowej usług równoważe repliki partycji i wystąpień w zwiększonej liczbie węzłów. Ogólna wydajność aplikacji poprawia i rywalizacji o dostęp do pamięci zmniejsza. Jeśli węzły w klastrze nie są używane efektywnie, można zmniejszyć liczbę węzłów w klastrze. Sieci szkieletowej usług ponownie równoważy repliki partycji i wystąpień w całej zmniejszonej liczby węzłów, aby lepiej wykorzystać sprzęt w każdym węźle. Klastry można skalować na [platformie](service-fabric-cluster-scale-up-down.md) Azure ręcznie lub [programowo.](service-fabric-cluster-programmatic-scaling.md) Klastry autonomiczne można skalować [ręcznie.](service-fabric-cluster-windows-server-add-remove-nodes.md)

### <a name="cluster-upgrades"></a>Uaktualnienia klastra
Okresowo nowe wersje środowiska uruchomieniowego sieci szkieletowej usług są zwalniane. Wykonaj uaktualnienia środowiska wykonawczego lub sieci szkieletowej w klastrze, aby zawsze uruchomić [obsługiwaną wersję](service-fabric-support.md). Oprócz uaktualnień sieci szkieletowej można również zaktualizować konfigurację klastra, taką jak certyfikaty lub porty aplikacji.

Klaster sieci szkieletowej usług jest zasobem, który jest własnością, ale jest częściowo zarządzany przez firmę Microsoft. Firma Microsoft jest odpowiedzialna za instalowanie poprawek bazowego systemu operacyjnego i wykonywanie uaktualnień sieci szkieletowej w klastrze. Klaster można ustawić tak, aby odbierał automatyczne uaktualnienia sieci szkieletowej, gdy firma Microsoft wyda nową wersję, lub wybrać obsługiwaną wersję sieci szkieletowej, która ma być dostępna. Uaktualnienia sieci szkieletowej i konfiguracji można ustawić za pośrednictwem witryny Azure portal lub za pośrednictwem Menedżera zasobów. Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra sieci szkieletowej usług](service-fabric-cluster-upgrade.md). 

Klaster autonomiczny jest zasobem, który jest całkowicie własnością. Użytkownik jest odpowiedzialny za łatanie bazowego systemu operacyjnego i inicjowanie uaktualnień sieci szkieletowej. Jeśli klaster może [https://www.microsoft.com/download](https://www.microsoft.com/download)się połączyć z klastrem, można ustawić klaster, aby automatycznie pobierał i aprowizył nowy pakiet środowiska uruchomieniowego sieci szkieletowej usług. Następnie należy zainicjować uaktualnienie. Jeśli klaster nie [https://www.microsoft.com/download](https://www.microsoft.com/download)może uzyskać dostępu, można ręcznie pobrać nowy pakiet środowiska wykonawczego z komputera podłączonego do Internetu, a następnie zainicjować uaktualnienie. Aby uzyskać więcej informacji, zobacz [Uaktualnianie autonomicznego klastra sieci szkieletowej usług](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitorowanie kondycji
Usługa Sieci szkieletowej wprowadza [model kondycji](service-fabric-health-introduction.md) przeznaczony do flagi w złej kondycji klastra i warunki aplikacji dla określonych jednostek (takich jak węzły klastra i repliki usługi). Model kondycji używa reporterów kondycji (składniki systemu i watchdogs). Celem jest łatwa i szybka diagnoza i naprawa. Autorzy usług muszą myśleć z góry o zdrowiu i o tym, jak [projektować raportowanie kondycji](service-fabric-report-health.md#design-health-reporting). Każdy warunek, który może mieć wpływ na zdrowie, powinien być zgłaszany, zwłaszcza jeśli może pomóc flagi problemów w pobliżu katalogu głównego. Informacje o kondycji można zaoszczędzić czas i wysiłek na debugowanie i badania, gdy usługa jest uruchomiony na dużą skalę w produkcji.

Reporterzy Service Fabric monitorują zidentyfikowane warunki zainteresowania. Informują o tych warunkach na podstawie ich lokalnego poglądu. [Magazyn kondycji](service-fabric-health-introduction.md#health-store) agreguje dane kondycji wysyłane przez wszystkich reporterów, aby ustalić, czy jednostki są globalnie zdrowe. Model ma być bogaty, elastyczny i łatwy w użyciu. Jakość raportów kondycji określa dokładność widoku kondycji klastra. Fałszywe alarmy, które błędnie pokazują niezdrowe problemy mogą negatywnie wpłynąć na uaktualnienia lub inne usługi korzystające z danych o kondycji. Przykładami takich usług są usługi naprawcze i mechanizmy alarmowe. W związku z tym niektóre myśli jest potrzebne do dostarczania raportów, które przechwytywania warunki zainteresowania w najlepszy możliwy sposób.

Raportowanie można wykonać z:
* Monitorowana replika lub wystąpienie usługi sieci szkieletowej usług.
* Wewnętrzne watchdogs wdrożone jako usługa sieci szkieletowej usług (na przykład usługa bezstanowa sieci szkieletowej usług, która monitoruje warunki i raporty problemów). Watchdogs można wdrożyć na wszystkich węzłach lub mogą być affinitized do monitorowana usługa.
* Wewnętrzne watchdogs, które są uruchamiane w węzłach sieci szkieletowej usług, ale nie są implementowane jako usługi sieci szkieletowej usług.
* Zewnętrzne watchdogs, które sondują zasób spoza klastra sieci szkieletowej usług (na przykład usługi monitorowania, takich jak Gomez).

Po wyjęciu z pudełka składniki sieci szkieletowej usług raportują kondycję wszystkich jednostek w klastrze. [Raporty kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) zapewniają wgląd w funkcje klastra i aplikacji oraz flagi problemów za pośrednictwem kondycji. W przypadku aplikacji i usług raporty kondycji systemu weryfikują, czy jednostki są implementowane i zachowują się poprawnie z punktu widzenia środowiska uruchomieniowego sieci szkieletowej usług. Raporty nie zapewniają monitorowania kondycji logiki biznesowej usługi lub wykrywania procesów, które przestały odpowiadać. Aby dodać informacje o kondycji specyficzne dla logiki usługi, [należy zaimplementować niestandardowe raportowanie kondycji](service-fabric-report-health.md) w usługach.

Sieci szkieletowej usług oferuje wiele sposobów [wyświetlania raportów kondycji](service-fabric-view-entities-aggregated-health.md) zagregowanych w magazynie kondycji:
* [Eksplorator sieci szkieletowej usług](service-fabric-visualizing-your-cluster.md) lub inne narzędzia do wizualizacji.
* Zapytania dotyczące kondycji (za pośrednictwem programu [PowerShell,](/powershell/module/ServiceFabric/) [interfejsu wiersza polecenia,](service-fabric-sfctl.md) [interfejsów API FabricClient języka C#](/dotnet/api/system.fabric.fabricclient.healthclient) i [interfejsów API Java FabricClient](/java/api/system.fabric)lub [interfejsów API REST).](/rest/api/servicefabric)
* Ogólne kwerendy, które zwracają listę jednostek, które mają kondycję jako jedną z właściwości (za pośrednictwem programu PowerShell, interfejsu wiersza polecenia, interfejsów API lub REST).

## <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka
[Monitorowanie i diagnostyka](service-fabric-diagnostics-overview.md) mają kluczowe znaczenie dla tworzenia, testowania i wdrażania aplikacji i usług w dowolnym środowisku. Rozwiązania sieci szkieletowej usług działają najlepiej podczas planowania i wdrażania monitorowania i diagnostyki, które pomagają zapewnić, że aplikacje i usługi działają zgodnie z oczekiwaniami w lokalnym środowisku programistycznym lub w środowisku produkcyjnym.

Głównymi celami monitorowania i diagnostyki są:

- Wykrywanie i diagnozowanie problemów ze sprzętem i infrastrukturą
- Wykrywanie problemów z oprogramowaniem i aplikacjami, skracynie przestojów usługi
- Opis zużycia zasobów i pomoc w podejmowaniu decyzji operacyjnych
- Optymalizacja wydajności aplikacji, usług i infrastruktury
- Generowanie analiz biznesowych i identyfikowanie obszarów ulepszeń
 
Ogólny przepływ pracy z monitoringu i diagnostyki składa się z trzech etapów:

1. Generowanie zdarzeń: obejmuje zdarzenia (dzienniki, ślady, zdarzenia niestandardowe) w infrastrukturze (klastrze), platformie i poziomie aplikacji / usługi
2. Agregacja zdarzeń: wygenerowane zdarzenia muszą być zbierane i agregowane, zanim będą mogły być wyświetlane
3. Analiza: zdarzenia muszą być wizualizowane i dostępne w jakimś formacie, aby umożliwić analizę i wyświetlanie w razie potrzeby

Dostępnych jest wiele produktów, które obejmują te trzy obszary, i masz swobodę wyboru różnych technologii dla każdego. Aby uzyskać więcej informacji, zobacz [Monitorowanie i diagnostyka sieci szkieletowej usług Azure](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak utworzyć [klaster na platformie Azure](service-fabric-cluster-creation-via-portal.md) lub [autonomiczny klaster w systemie Windows](service-fabric-cluster-creation-for-windows-server.md).
* Spróbuj utworzyć usługę za pomocą usług [Reliable Services](service-fabric-reliable-services-quick-start.md) lub modelu programowania [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Dowiedz się, jak [przeprowadzić migrację z usług w chmurze.](service-fabric-cloud-services-migration-differences.md)
* Naucz się [monitorować i diagnozować usługi](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Dowiedz się, jak [testować aplikacje i usługi](service-fabric-testability-overview.md).
* Dowiedz się, jak [zarządzać zasobami klastra i aranżować](service-fabric-cluster-resource-manager-introduction.md)go.
* Przejrzyj [przykłady sieci szkieletowej usług](https://aka.ms/servicefabricsamples).
* Dowiedz się więcej o [opcjach pomocy technicznej sieci szkieletowej](service-fabric-support.md)usług .
* Przeczytaj [blog zespołu,](https://blogs.msdn.microsoft.com/azureservicefabric/) aby zapoznać się z artykułami i ogłoszeniami.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
