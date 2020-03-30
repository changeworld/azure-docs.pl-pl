---
title: Poznaj terminologię sieci szkieletowej usług Azure
description: Poznaj terminologię sieci szkieletowej kluczowych usług i pojęcia używane w pozostałej części dokumentacji.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: a9266c2a8d2ad179cfdb12e367a14f37d1abc9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258241"
---
# <a name="service-fabric-terminology-overview"></a>Omówienie terminologii sieci szkieletowej usług

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi.  [Klastry sieci szkieletowej usług można hostować w dowolnym miejscu:](service-fabric-deploy-anywhere.md)platformie Azure, w lokalnym centrum danych lub u dowolnego dostawcy chmury.  Sieć szkieletowa usług jest koordynatorem, który zasila [usługę Azure Service Fabric Mesh](/azure/service-fabric-mesh). Można użyć dowolnej struktury do zapisu usług i wybrać, gdzie uruchomić aplikację z wielu opcji środowiska. W tym artykule opisano terminologię używaną przez sieci szkieletowej usług, aby zrozumieć terminy używane w dokumentacji.

## <a name="infrastructure-concepts"></a>Koncepcje infrastruktury

**Klaster:** Połączony z siecią zestaw maszyn wirtualnych lub fizycznych, na których mikrousług są wdrażane i zarządzane.  Klastry mogą obejmować nawet tysiące maszyn.

**Węzeł:** Maszyna lub maszyna wirtualna, która jest częścią klastra, jest nazywana *węzłem*. Każdemu węzłowi jest przypisywana nazwa węzła (ciąg). Węzły mają właściwości, takie jak właściwości umieszczania. Każdy komputer lub maszyna wirtualna ma `FabricHost.exe`usługę automatycznego uruchamiania systemu Windows, która rozpoczyna `Fabric.exe` `FabricGateway.exe`się po uruchomieniu, a następnie uruchamia dwa pliki wykonywalne: i . Te dwa pliki wykonywalne tworzą węzeł. W przypadku scenariuszy testowych można obsługiwać wiele węzłów na jednym `Fabric.exe` komputerze `FabricGateway.exe`lub maszynie wirtualnej, uruchamiając wiele wystąpień i .

## <a name="application-and-service-concepts"></a>Pojęcia dotyczące aplikacji i usług

**Aplikacja siatki sieci szkieletowej usług:** Aplikacje siatki sieci szkieletowej usług są opisane przez model zasobów (pliki zasobów YAML i JSON) i mogą być wdrażane w dowolnym środowisku, w którym działa sieć szkieletowa usług.

**Natywna aplikacja sieci szkieletowej usług:** Aplikacje macierzyste sieci szkieletowej usług są opisane przez natywny model aplikacji (manifesty aplikacji i usługi oparte na języku XML).  Aplikacje natywne sieci szkieletowej usług nie można uruchomić w sieci szkieletowej usług.

### <a name="service-fabric-mesh-application-concepts"></a>Pojęcia aplikacji siatki sieci szkieletowej usług

**Aplikacja**: Aplikacja jest jednostką wdrożenia, przechowywania wersji i okresu istnienia aplikacji mesh. Cykl życia każdego wystąpienia aplikacji można zarządzać niezależnie.  Aplikacje składają się z co najmniej jednego pakietów kodu usługi i ustawień. Aplikacja jest definiowana przy użyciu schematu modelu zasobów platformy Azure (RM).  Usługi są opisane jako właściwości zasobu aplikacji w szablonie RM.  Sieci i woluminy używane przez aplikację są odwoływane przez aplikację.  Podczas tworzenia aplikacji aplikacja, usługi, sieć i woluminy są modelowane przy użyciu modelu zasobów sieci szkieletowej usług.

**Usługa**: Usługa w aplikacji reprezentuje mikrousługi i wykonuje pełną i autonomiczną funkcję. Każda usługa składa się z jednego lub więcej pakietów kodu, które opisują wszystko, co potrzebne do uruchomienia obrazu kontenera skojarzonego z pakietem kodu.  Liczbę usług w aplikacji można skalować w górę i w dół.

**Sieć:** Zasób sieciowy tworzy sieć prywatną dla aplikacji i jest niezależny od aplikacji lub usług, które mogą się do niego odwoływać. Wiele usług z różnych aplikacji może być częścią tej samej sieci. Sieci są zasoby można wdrożyć, które są przywoływany przez aplikacje.

**Pakiet kodu:** Pakiety kodu opisują wszystko, co jest potrzebne do uruchomienia obrazu kontenera skojarzonego z pakietem kodu, w tym następujące:

* Nazwa kontenera, wersja i rejestr
* Zasoby procesora i pamięci wymagane dla każdego kontenera
* Sieciowe punkty końcowe
* Woluminy do zainstalowania w kontenerze, odwołując się do oddzielnego zasobu woluminu.

Wszystkie pakiety kodu zdefiniowane jako część zasobu aplikacji są wdrażane i aktywowane razem jako grupa.

**Wolumin:** Woluminy są katalogami, które są instalowane wewnątrz wystąpień kontenera, których można użyć do utrwalenia stanu. Sterownik woluminu usługi Azure Files umożliwia zainstalowanie udziału usługi Azure Files w kontenerze i zapewnia niezawodne przechowywanie danych za pośrednictwem dowolnego interfejsu API obsługującego magazyn plików. Woluminy są zasoby można wdrożyć, do których odwołują się aplikacje.

### <a name="service-fabric-native-application-concepts"></a>Pojęcia aplikacji natywnych sieci szkieletowej usług

**Aplikacja**: Aplikacja jest kolekcją usług składowych, które wykonują określoną funkcję lub funkcje. Cykl życia każdego wystąpienia aplikacji można zarządzać niezależnie.

**Usługa**: Usługa wykonuje pełną i autonomiczną funkcję i może uruchamiać i uruchamiać niezależnie od innych usług. Usługa składa się z kodu, konfiguracji i danych. Dla każdej usługi kod składa się z plików binarnych wykonywalnych, konfiguracja składa się z ustawień usługi, które mogą być ładowane w czasie wykonywania, a dane składają się z dowolnych danych statycznych, które mają być używane przez usługę.

**Typ aplikacji:** Nazwa/wersja przypisana do kolekcji typów usług. Jest on zdefiniowany `ApplicationManifest.xml` w pliku i osadzony w katalogu pakietu aplikacji. Katalog jest następnie kopiowany do magazynu obrazów klastra sieci szkieletowej usług. Następnie można utworzyć nazwaną aplikację z tego typu aplikacji w klastrze.

Przeczytaj artykuł [o modelu aplikacji,](service-fabric-application-model.md) aby uzyskać więcej informacji.

**Pakiet aplikacji**: Katalog dysku zawierający `ApplicationManifest.xml` plik typu aplikacji. Odwołuje się do pakietów usług dla każdego typu usługi, który składa się na typ aplikacji. Pliki w katalogu pakietów aplikacji są kopiowane do magazynu obrazów klastra sieci szkieletowej usług. Na przykład pakiet aplikacji dla typu aplikacji poczty e-mail może zawierać odwołania do pakietu usługi kolejki, pakietu usługi frontend i pakietu usługi bazy danych.

**Nazwana aplikacja:** Po skopiowaniu pakietu aplikacji do magazynu obrazów należy utworzyć wystąpienie aplikacji w klastrze. Wystąpienie można utworzyć po określeniu typu aplikacji pakietu aplikacji przy użyciu jego nazwy lub wersji. Każdemu wystąpieniu typu aplikacji jest przypisywana jednolita nazwa identyfikatora `"fabric:/MyNamedApp"`zasobu (URI), która wygląda następująco: . W klastrze można utworzyć wiele nazwanych aplikacji z jednego typu aplikacji. Można również utworzyć nazwane aplikacje z różnych typów aplikacji. Każda nazwana aplikacja jest zarządzana i wersjona niezależnie.

**Typ usługi:** Nazwa/wersja przypisana do pakietów kodu usługi, pakietów danych i pakietów konfiguracyjnych. Typ usługi jest zdefiniowany w `ServiceManifest.xml` pliku i osadzony w katalogu pakietów usług. Katalog pakietów usług jest następnie odwołuje się `ApplicationManifest.xml` do pliku pakietu aplikacji. W klastrze po utworzeniu nazwanej aplikacji można utworzyć nazwaną usługę z jednego z typów usług typu aplikacji. `ServiceManifest.xml` Plik typu usługi opisuje usługę.

Przeczytaj artykuł [o modelu aplikacji,](service-fabric-application-model.md) aby uzyskać więcej informacji.

Istnieją dwa rodzaje usług:

* **Bezstanowy:** Użyj usługi bezstanowej, gdy stan trwały usługi jest przechowywany w zewnętrznej usłudze magazynu, takiej jak Usługa Azure Storage, usługa Azure SQL Database lub usługa Azure Cosmos DB. Użyj usługi bezstanowej, gdy usługa nie ma magazynu trwałego. Na przykład dla usługi kalkulatora, gdzie wartości są przekazywane do usługi, obliczeń, który używa tych wartości, a następnie zwracany jest wynik.
* **Stanowe:** Użyj usługi stanowej, gdy chcesz sieci szkieletowej usług do zarządzania stanem usługi za pośrednictwem jego niezawodne kolekcje lub modele programowania reliable actors. Podczas tworzenia usługi o nazwie, należy określić, ile partycji chcesz rozłożyć stan na skalowalność. Należy również określić, ile razy należy replikować stan między węzłami, aby zapewnić niezawodność. Każda nazwana usługa ma jedną replikę podstawową i wiele replik pomocniczych. Podczas zapisywania w replice podstawowej można zmodyfikować stan usługi o nazwie. Sieci szkieletowej usług następnie replikuje ten stan do wszystkich replik pomocniczych, aby zachować stan w synchronizacji. Sieci szkieletowej usług automatycznie wykrywa, gdy replika podstawowa nie powiedzie się i promuje istniejącą replikę pomocniczą do repliki podstawowej. Sieć szkieletowa usług następnie tworzy nową replikę pomocniczą.  

**Repliki lub wystąpienia** odnoszą się do kodu (i stanu dla usług stanowych) usługi, która jest wdrażana i uruchomiona. Zobacz [Repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md).

**Ponowna konfiguracja** odnosi się do procesu wszelkich zmian w zestawie replik usługi. Zobacz [Rekonfiguracja](service-fabric-concepts-reconfiguration.md).

**Pakiet usługi:** Katalog dysku zawierający `ServiceManifest.xml` plik typu usługi. Ten plik odwołuje się do kodu, danych statycznych i pakietów konfiguracyjnych dla typu usługi. Pliki w katalogu pakietów usług są odwoływane przez `ApplicationManifest.xml` plik typu aplikacji. Na przykład pakiet usługi może odwoływać się do kodu, danych statycznych i pakietów konfiguracyjnych, które tworzą usługę bazy danych.

**Nazwana usługa:** Po utworzeniu nazwanej aplikacji można utworzyć wystąpienie jednego z jej typów usług w klastrze. Typ usługi można określić przy użyciu jego nazwy/wersji. Każde wystąpienie typu usługi jest przypisywane nazwę identyfikatora URI o zakresie w ramach identyfikatora URI jego nazwanej aplikacji. Na przykład jeśli utworzysz usługę "MyDatabase" o nazwie w aplikacji "MyNamedApp", identyfikator URI wygląda następująco: `"fabric:/MyNamedApp/MyDatabase"`. W ramach nazwanej aplikacji można utworzyć kilka nazwanych usług. Każda nazwana usługa może mieć swój własny schemat partycji i liczbę wystąpień lub replik.

**Pakiet kodu**: Katalog dysku zawierający pliki wykonywalne typu usługi, zazwyczaj pliki EXE/DLL. Pliki w katalogu pakietów kodu są odwoływane przez `ServiceManifest.xml` plik typu usługi. Podczas tworzenia usługi o nazwie pakiet kodu jest kopiowany do węzła lub węzłów wybranych do uruchomienia nazwanej usługi. Następnie kod zaczyna działać. Istnieją dwa typy plików wykonywalnych pakietu kodu:

* **Pliki wykonywalne gościa:** Pliki wykonywalne, które działają w stanie takim, w jakim są w systemie operacyjnym hosta (Windows lub Linux). Te pliki wykonywalne nie łączą się ani nie odwołują się do plików środowiska uruchomieniowego sieci szkieletowej usług i dlatego nie używają żadnych modeli programowania sieci szkieletowej usług. Te pliki wykonywalne nie mogą korzystać z niektórych funkcji sieci szkieletowej usług, takich jak usługa nazewnictwa do odnajdowania punktu końcowego. Pliki wykonywalne gościa nie mogą raportować metryk obciążenia specyficznych dla każdego wystąpienia usługi.
* **Pliki wykonywalne hosta usług:** Pliki wykonywalne korzystające z modeli programowania sieci szkieletowej usług, łącząc się z plikami środowiska uruchomieniowego sieci szkieletowej sieci szkieletowej usług, włączając funkcje sieci szkieletowej usług. Na przykład wystąpienie usługi o nazwie można zarejestrować punkty końcowe z usługi nazewnictwa sieci szkieletowej usług i może również raportować metryki obciążenia.

**Pakiet danych**: Katalog dysku zawierający statyczne pliki danych typu usługi, zazwyczaj pliki zdjęć, dźwięku i wideo. Pliki w katalogu pakietów danych są odwoływane przez `ServiceManifest.xml` plik typu usługi. Podczas tworzenia usługi o nazwie pakiet danych jest kopiowany do węzła lub węzłów wybranych do uruchomienia nazwanej usługi. Kod zaczyna działać i może teraz uzyskiwać dostęp do plików danych.

**Pakiet konfiguracyjny:** Katalog dysku zawierający statyczne pliki konfiguracyjne typu usługi, zazwyczaj pliki tekstowe. Pliki w katalogu pakietów konfiguracyjnych są odwoływane `ServiceManifest.xml` przez plik typu usługi. Podczas tworzenia usługi o nazwie pliki w pakiecie konfiguracyjnym są kopiowane do jednego lub więcej węzłów wybranych do uruchomienia nazwanej usługi. Następnie kod zaczyna działać i może teraz uzyskać dostęp do plików konfiguracyjnych.

**Kontenery:** Domyślnie sieci szkieletowej usług wdraża i aktywuje usługi jako procesy. Sieci szkieletowej usług można również wdrożyć usługi w obrazach kontenerów. Kontenery to technologia wirtualizacji, która wyodrębnia podstawowy system operacyjny z aplikacji. Aplikacja i jej środowisko uruchomieniowe, zależności i biblioteki systemowe są uruchamiane wewnątrz kontenera. Kontener ma pełny, prywatny dostęp do kontenera własny izolowany widok konstrukcji systemu operacyjnego. Usługa Fabric obsługuje kontenery systemu Windows Server i kontenery platformy Docker w systemie Linux. Aby uzyskać więcej informacji, zobacz [Sieci szkieletowe usług i kontenery](service-fabric-containers-overview.md).

**Schemat partycji:** Podczas tworzenia nazwanej usługi należy określić schemat partycji. Usługi ze znaczną ilością stanu podzielić dane między partycjami, co rozprzestrzenia się stan w węzłach klastra. Dzieląc dane między partycje, stan usługi o nazwie można skalować. W ramach partycji bezstanowe nazwane usługi mają wystąpienia, podczas gdy usługi o nazwie stanowe mają repliki. Zazwyczaj bezstanowe usługi o nazwie mają tylko jedną partycję, ponieważ nie mają stanu wewnętrznego. Wystąpienia partycji zapewniają dostępność. Jeśli jedno wystąpienie nie powiedzie się, inne wystąpienia nadal działają normalnie, a następnie sieć szkieletowa usług tworzy nowe wystąpienie. Stateful nazwanych usług zachować ich stan w replikach i każda partycja ma swój własny zestaw replik, więc stan jest utrzymywany w synchronizacji. Jeśli replika nie powiedzie się, sieci szkieletowej usług tworzy nową replikę z istniejących replik.

Przeczytaj artykuł o [niezawodnych usługach sieci szkieletowej usług partycji,](service-fabric-concepts-partitioning.md) aby uzyskać więcej informacji.

## <a name="system-services"></a>Usługi systemowe

Istnieją usługi systemowe, które są tworzone w każdym klastrze, które zapewniają możliwości platformy sieci szkieletowej usług.

**Usługa nazewnictwa:** Każdy klaster sieci szkieletowej usług ma usługę nazewnictwa, która rozpoznaje nazwy usług do lokalizacji w klastrze. Zarządzasz nazwami i właściwościami usług, takimi jak internetowy system nazw domen (DNS) dla klastra. Klienci bezpiecznie komunikują się z dowolnym węzłem w klastrze za pomocą usługi nazewnictwa w celu rozpoznania nazwy usługi i jej lokalizacji. Aplikacje są przesują się w obrębie klastra. Na przykład może to być spowodowane błędami, równoważeniem zasobów lub zmiany rozmiaru klastra. Można tworzyć usługi i klientów, które rozpoznają bieżącą lokalizację sieciową. Klienci uzyskują rzeczywisty adres IP komputera i port, w którym jest aktualnie uruchomiony.

Przeczytaj [komunikację z usługami,](service-fabric-connect-and-communicate-with-services.md) aby uzyskać więcej informacji na temat interfejsów API komunikacji klienta i usługi, które współpracują z usługą nazewnictwa.

**Usługa magazynu obrazów:** Każdy klaster sieci szkieletowej usług ma usługę Magazynu obrazów, w której są przechowywane wdrożone, wersjone pakiety aplikacji. Skopiuj pakiet aplikacji do magazynu obrazów, a następnie zarejestruj typ aplikacji zawarty w tym pakiecie aplikacji. Po zainicjowaniu obsługi administracyjnej typu aplikacji należy utworzyć z niej nazwaną aplikację. Po usunięciu wszystkich nazwanych aplikacji można wyrejestrować typ aplikacji z usługi Image Store.

Przeczytaj [artykuł Opis ustawienia ImageStoreConnectionString,](service-fabric-image-store-connection-string.md) aby uzyskać więcej informacji na temat usługi Sklep z obrazami.

Przeczytaj artykuł [Wdrażanie aplikacji,](service-fabric-deploy-remove-applications.md) aby uzyskać więcej informacji na temat wdrażania aplikacji w usłudze Magazynu obrazów.

**Usługa Menedżera trybu failover:** Każdy klaster sieci szkieletowej usług ma usługę Menedżera trybu failover, która jest odpowiedzialna za następujące akcje:

 - Wykonuje funkcje związane z wysoką dostępnością i spójnością usług.
 - Organizuje uaktualnienia aplikacji i klastra.
 - Współdziała z innymi składnikami systemu.

**Usługa Menedżera napraw:** Jest to opcjonalna usługa systemowa, która umożliwia wykonanie akcji naprawczych w klastrze w sposób bezpieczny, zautomatyzowany i przejrzysty. Kierownik naprawy jest używany w:

   - Wykonywanie napraw konserwacji platformy Azure w klastrach sieci szkieletowej azure service [service azure o trwałości i użyteczności 10: 100](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) 000 000 000 000 000 000
   - Wykonywanie akcji naprawczych dla [aplikacji Patch Orchestration](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Modele wdrażania i aplikacji

Aby wdrożyć usługi, należy opisać, jak powinny działać. Usługa Service Fabric obsługuje trzy różne modele wdrażania:

### <a name="resource-model-preview"></a>Model zasobu (wersja zapoznawcza)

Zasoby sieci szkieletowej usług są wszystko, co można wdrożyć indywidualnie do sieci szkieletowej usług; aplikacji, usług, sieci i woluminów. Zasoby są definiowane przy użyciu pliku JSON, który można wdrożyć w punkcie końcowym klastra.  W przypadku siatki sieci szkieletowej usług używany jest schemat modelu zasobów platformy Azure. Schemat pliku YAML może być również używany do łatwiejszego tworzenia plików definicji. Zasoby można wdrożyć w dowolnym miejscu uruchomień sieci szkieletowej usług. Model zasobów jest najprostszym sposobem opisania aplikacji sieci szkieletowej usług. Jego głównym celem jest proste wdrażanie i zarządzanie konteneryzowanymi usługami. Aby dowiedzieć się więcej, przeczytaj [artykuł Wprowadzenie do modelu zasobów sieci szkieletowej usług](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Model macierzysty

Natywny model aplikacji zapewnia aplikacjom pełny niski poziom dostępu do sieci szkieletowej usług. Aplikacje i usługi są definiowane jako zarejestrowane typy w plikach manifestów XML.

Model macierzysty obsługuje struktury niezawodne usługi i niezawodne podmioty, która zapewnia dostęp do interfejsów API środowiska sieci szkieletowej sieci szkieletowej i interfejsów API zarządzania klastrami w językach C# i Java. Model macierzysty obsługuje również dowolne kontenery i pliki wykonywalne. Model macierzysty nie jest obsługiwany w [środowisku Siatki sieci szkieletowej usług](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**Niezawodne usługi:** interfejs API do tworzenia usług bezstanowych i stanowych. Usługi stanowe przechowują swój stan w niezawodnych kolekcjach, takich jak słownik lub kolejka. Można również podłączyć różne stosy komunikacji, takie jak interfejs API sieci Web i Windows Communication Foundation (WCF).

**Reliable Actors:** Interfejs API do tworzenia bezstanowych i stanowych obiektów za pośrednictwem modelu programowania wirtualnego aktora. Ten model jest przydatny, gdy masz wiele niezależnych jednostek obliczeń lub stanu. Ten model używa modelu wątków turowych, dlatego najlepiej unikać kodu, który wywołuje innych aktorów lub usług, ponieważ pojedynczy aktor nie może przetwarzać innych żądań przychodzących, dopóki nie zostaną zakończone wszystkie jego żądania wychodzące.

Istniejące aplikacje można również uruchamiać w sieci szkieletowej usług:

**Kontenery:** Usługa Service Fabric obsługuje wdrażanie kontenerów platformy Docker w kontenerach systemu Linux i Windows Server w systemie Windows Server 2016, a także obsługę trybu izolacji funkcji Hyper-V. W modelu [aplikacji sieci](service-fabric-application-model.md)szkieletowej usług kontener reprezentuje hosta aplikacji, w którym są umieszczane wiele replik usług. Sieć szkieletowa usług można uruchomić żadnych kontenerów, a scenariusz jest podobny do scenariusza wykonywalnego gościa, gdzie można spakować istniejącą aplikację wewnątrz kontenera. Ponadto można [uruchomić usługi sieci szkieletowej usług wewnątrz kontenerów,](service-fabric-services-inside-containers.md) jak również.

**Pliki wykonywalne gościa:** Można uruchomić dowolny typ kodu, taki jak Node.js, Python, Java lub C++ w usłudze Azure Service Fabric jako usługa. Sieć szkieletowa usług odnosi się do tego typu usług jako pliki wykonywalne gościa, które są traktowane jako usługi bezstanowe. Zalety uruchamiania pliku wykonywalnego gościa w klastrze sieci szkieletowej usług obejmują wysoką dostępność, monitorowanie kondycji, zarządzanie cyklem życia aplikacji, wysoką gęstość i wykrywalność.

Przeczytaj [artykuł Wybierz model programowania dla usługi, aby](service-fabric-choose-framework.md) uzyskać więcej informacji.

### <a name="docker-compose"></a>Docker Compose 

[Docker Compose](https://docs.docker.com/compose/) jest częścią projektu platformy Docker. Sieci szkieletowej usług zapewnia ograniczoną obsługę [wdrażania aplikacji przy użyciu modelu docker compose](service-fabric-docker-compose.md).

## <a name="environments"></a>Środowiska

Sieć szkieletowa usług to technologia platformy typu open source, na podstawie których opiera się kilka różnych usług i produktów. Firma Microsoft udostępnia następujące opcje:

 - **Usługa Azure Service Fabric Mesh:** w pełni zarządzana usługa do uruchamiania aplikacji sieci szkieletowej usług na platformie Microsoft Azure.
 - **Usługa Azure Service Fabric:** oferta klastra hostowana przez usługę Azure. Zapewnia integrację między siecią szkieletową usług a infrastrukturą platformy Azure, a także zarządzanie uaktualnianiem i konfiguracją klastrów sieci szkieletowej usług.
 - **Autonomiczna sieci szkieletowej usług:** Zestaw narzędzi instalacyjnych i konfiguracyjnych do [wdrażania klastrów sieci szkieletowej usług w dowolnym miejscu](/azure/service-fabric/service-fabric-deploy-anywhere) (lokalnie lub u dowolnego dostawcy chmury). Nie jest zarządzany przez platformę Azure.
 - **Klaster programistyczny sieci szkieletowej usług:** zapewnia lokalne środowisko programistyczne w systemach Windows, Linux lub Mac do tworzenia aplikacji sieci szkieletowej usług.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Macierz obsługi środowiska, struktury i modelu wdrażania

Różne środowiska mają różne poziomy obsługi dla struktur i modeli wdrażania. W poniższej tabeli opisano obsługiwane kombinacje modeli struktury i wdrażania.

| Rodzaj aplikacji | Opisane przez | Azure Service Fabric Mesh | Klastry sieci szkieletowej usług Azure (dowolny system operacyjny)| Klaster lokalny | Klaster autonomiczny |
|---|---|---|---|---|---|
| Aplikacje siatki sieci szkieletowej usług | Model zasobów (YAML & JSON) | Obsługiwane |Nieobsługiwane | Obsługiwane przez windowsa, Linux i Mac - nie obsługiwane | Windows- nie jest obsługiwany |
|Aplikacje natywne sieci szkieletowej usług | Natywny model aplikacji (XML) | Nieobsługiwane| Obsługiwane|Obsługiwane|Obsługiwane przez system Windows|

W poniższej tabeli opisano różne modele aplikacji i narzędzia, które istnieje dla nich dla sieci szkieletowej usług.

| Rodzaj aplikacji | Opisane przez | Visual Studio | Eclipse | SFCTL (SFCTL) | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Aplikacje siatki sieci szkieletowej usług | Model zasobów (YAML & JSON) | VS 2017 |Nieobsługiwane |Nieobsługiwane | Obsługiwane — tylko środowisko siatki | Nieobsługiwane|
|Aplikacje natywne sieci szkieletowej usług | Natywny model aplikacji (XML) | VS 2017 i VS 2015| Obsługiwane|Obsługiwane|Obsługiwane|Obsługiwane|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sieci szkieletowej usług:

* [Omówienie usługi Service Fabric](service-fabric-overview.md)
* [Why a microservices approach to building applications?](service-fabric-overview-microservices.md) (Dlaczego warto tworzyć aplikacje z użyciem mikrousług?)
* [Scenariusze aplikacji](service-fabric-application-scenarios.md)

Aby dowiedzieć się więcej o sieci szkieletowej usług:

* [Omówienie siatki sieci szkieletowej usług](/azure/service-fabric-mesh/service-fabric-mesh-overview)
