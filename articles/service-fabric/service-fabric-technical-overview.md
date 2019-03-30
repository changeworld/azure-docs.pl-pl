---
title: Dowiedz się, usługa Azure Service Fabric — terminologia | Dokumentacja firmy Microsoft
description: Przegląd terminologii, usługi Service Fabric. W tym artykule omówiono terminologii pojęć i terminów używanych w pozostałej części dokumentacji.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/17/2018
ms.author: masnider
ms.openlocfilehash: 085d5e560eec090ab76c263f8f93140786f2d734
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668448"
---
# <a name="service-fabric-terminology-overview"></a>Przegląd terminologii usługi Service Fabric
Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi.  Możesz [klastrów hostów usługi Service Fabric, gdziekolwiek](service-fabric-deploy-anywhere.md): W lokalnym centrum danych lub na wszystkich dostawców chmury platformy Azure.  Usługa Service Fabric jest programu orchestrator, która napędza [usługi Azure Service Fabric siatki](/azure/service-fabric-mesh). Tworzenie usług i wybierz miejsce uruchomić aplikację z wielu opcji środowiska, można użyć dowolnej platformy. Ten artykuł szczegółowo opisuje z terminologią używaną przez usługę Service Fabric do zrozumienia pojęć używanych w dokumentacji.

## <a name="infrastructure-concepts"></a>Pojęcia dotyczące infrastruktury
**Klaster**: Połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w których mikrousługi są wdrażania i zarządzania nimi.  Klastry mogą obejmować nawet tysiące maszyn.

**Węzeł**: Nosi nazwę komputera lub maszyny Wirtualnej, która jest częścią klastra *węzła*. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają cechami, takimi jak właściwości umieszczania. Każda maszyna lub maszyna wirtualna jest automatycznie uruchamiana usługa Windows `FabricHost.exe`, który zacznie działać po rozruchu, a następnie uruchamia dwa pliki wykonywalne: `Fabric.exe` i `FabricGateway.exe`. Te dwa pliki wykonywalne tworzą węzła. W przypadku testowania scenariuszy może obsługiwać wiele węzłów na jednym komputerze lub maszynie Wirtualnej, uruchamiając wiele wystąpień `Fabric.exe` i `FabricGateway.exe`.

## <a name="application-and-service-concepts"></a>Aplikacja i pojęcia dotyczące usługi

**Aplikacja usługi Service Fabric siatki**: Aplikacje usługi Service Fabric siatki są opisane przez Model zasobów (pliki zasobów YAML i JSON) i można wdrożyć w dowolnym środowisku, na którym działa Usługa Service Fabric.

**Aplikacja usługi Service Fabric natywnych**: Aplikacje natywne usługi Service Fabric są opisane przez Model aplikacji natywnych (oparte na języku XML aplikacji i manifestów usługi).  Aplikacje natywne usługi Service Fabric nie może uruchomić usługi Service Fabric siatki.

### <a name="service-fabric-mesh-application-concepts"></a>Pojęcia dotyczące aplikacji usługi Service Fabric siatki

**Aplikacja**: Aplikacja jest jednostką wdrożenia, przechowywanie wersji i cyklu życia aplikacji siatki. Cykl życia każdego wystąpienia aplikacji można zarządzać w sposób niezależny.  Aplikacje składają się z co najmniej jeden usługi pakiety kodu i ustawień. Aplikacja jest zdefiniowany, przy użyciu schematu modelu zasobów platformy Azure (RM).  Usługi są określane jako właściwości zasobu aplikacji w szablonie Menedżera zasobów.  Sieci i woluminów używanych przez aplikację są przywoływane przez aplikację.  Podczas tworzenia aplikacji, aplikacji, usług, sieci i woluminy są modelowane przy użyciu modelu zasobów usługi Service Fabric.

**Usługa**: Usługi w aplikacji reprezentuje mikrousługi i wykonuje pełne i autonomicznych funkcji. Każda usługa składa się z jednego lub więcej, pakiety kodu, które opisują wszystko, co jest potrzebne do uruchomienia obrazu kontenera, skojarzone z pakietem kodu.  Liczba usług w aplikacji można skalować w górę i w dół.

**Sieć**: Zasób sieciowy tworzy sieci prywatnej dla aplikacji i jest niezależna od aplikacji lub usług, które mogą odwoływać się do niego. Wiele usług w różnych aplikacjach mogą być częścią tej samej sieci. Sieci są można wdrożyć zasoby, które są przywoływane przez aplikacje.

**Pakiet kodu**: Pakiety kodu opisują wszystko, co jest potrzebne do uruchomienia obrazu kontenera, skojarzone z pakietem kodu, w tym następujące:

* Nazwa kontenera, wersji i rejestru
* Zasoby Procesora i pamięci, które są wymagane dla każdego kontenera
* Punkty końcowe sieci
* Woluminy należy zainstalować w kontenerze, odwołuje się do zasobów oddzielnego woluminu.

Wszystkie pakiety kodu określonych w ramach zasobu aplikacji są wdrażane i aktywowane jako grupa.

**Wolumin**: Woluminy są katalogów, która ma zostać zainstalowany wewnątrz wystąpienia kontenera, które umożliwia utrwalanie stanu. Sterownik woluminu usługi Azure Files na komputerze instalująca udział plików platformy Azure do kontenera i zapewnia niezawodny magazyn danych za pośrednictwem dowolnego interfejsu API, który obsługuje usługi file storage. Woluminy są dostępne do wdrożenia zasobów, które są przywoływane przez aplikacje.

### <a name="service-fabric-native-application-concepts"></a>Pojęcia dotyczące aplikacji natywnej usługi Service Fabric

**Aplikacja**: Aplikacja to kolekcja usług składowych, które wykonują niektórych funkcji lub funkcje. Cykl życia każdego wystąpienia aplikacji można zarządzać w sposób niezależny.

**Usługa**: Usługa wykonuje funkcję kompletne i autonomiczne i można uruchomić i Uruchom niezależnie od innych usług. Usługa składa się z kodu, konfiguracji i danych. Dla każdej usługi kod zawiera wykonywalne pliki binarne, konfiguracja obejmuje ustawienia usługi, które mogą być ładowane w czasie wykonywania i danych składa się z dowolnych danych statycznych do użycia przez usługę.

**Typ aplikacji**: Nazwa/wersja przypisane do kolekcji typów usług. Jest on zdefiniowany w `ApplicationManifest.xml` pliku i osadzone w katalogu pakietu aplikacji. Katalog zostanie następnie skopiowany do magazynu obrazów klastra usługi Service Fabric. Będzie można utworzyć aplikacji o nazwie tego typu aplikacji w klastrze.

Odczyt [model aplikacji](service-fabric-application-model.md) artykuł, aby uzyskać więcej informacji.

**Pakiet aplikacji**: Katalog dysk zawierający typ aplikacji `ApplicationManifest.xml` pliku. Odwołuje się do pakietów usługi dla każdego typu usług, który tworzy typ aplikacji. Pliki w katalogu pakietu aplikacji są kopiowane do magazynu obrazów klastra usługi Service Fabric. Na przykład pakiet aplikacji dla typu aplikacji poczty e-mail może zawierać odwołania do pakietu usług kolejki, pakiet usługi frontonu i pakiet usługi bazy danych.

**O nazwie aplikacji**: Po skopiowaniu pakietu aplikacji do magazynu obrazów, należy utworzyć wystąpienie aplikacji w klastrze. Należy utworzyć wystąpienie typu aplikacji pakietu aplikacji, jest określany na podstawie jego nazwy lub wersji. Każde wystąpienie typu aplikacji jest przypisany uniform resource identifier nazwę identyfikator (URI), która wygląda podobnie: `"fabric:/MyNamedApp"`. W ramach klastra możesz utworzyć wiele aplikacji o nazwie z typem pojedynczej aplikacji. Można również utworzyć aplikacje o nazwie z typy innej aplikacji. Każda aplikacja o nazwie jest niezależne zarządzane i kontrolą wersji.

**Typ usługi**: Nazwa/wersja przypisane pakiety kodu, pakiety danych oraz pakiety konfiguracji usługi. Typ usługi jest zdefiniowany w `ServiceManifest.xml` pliku i osadzone w katalogu pakietu usługi. Katalog pakietu usługi odwołuje się do następnie pakietu aplikacji `ApplicationManifest.xml` pliku. W ramach klastra po utworzeniu aplikacji o nazwie, można utworzyć nazwanej usługi z jednego z typów usług typu aplikacji. Typ usługi `ServiceManifest.xml` plik zawiera opis usługi.

Odczyt [model aplikacji](service-fabric-application-model.md) artykuł, aby uzyskać więcej informacji.

Istnieją dwa typy usług:

* **Bezstanowe**: Użyj usługi bezstanowej, gdy trwały stan usługi są przechowywane w usłudze magazynu zewnętrznego, takie jak usługi Azure Storage, Azure SQL Database lub Azure Cosmos DB. Użyj usługi bezstanowej, gdy usługa nie ma żadnych pojemności magazynu trwałego. Na przykład usługa Kalkulator, których wartości są przekazywane do usługi, obliczenie jest wykonywane, który używa tych wartości, a następnie wynik jest zwracany.
* **Stanowa**: Usługa stanowa należy używać usługi Service Fabric, aby zarządzać stanem usługi za pośrednictwem jego elementów Reliable Collections lub interfejsu Reliable Actors w modelach programowania. Po utworzeniu nazwaną usługę, należy określić liczbę partycji, ma być rozłożona na stan programu w przypadku skalowalności. Również określić, ile razy należy replikować swój stan między węzłami w celu zapewnienia niezawodności. Każda usługa o nazwie ma, gdzie pojedyncza replika podstawowa i wiele replik pomocniczych. Stan usługi o nazwie możesz modyfikować podczas zapisu do repliki podstawowej. Usługa Service Fabric są następnie replikowane ten stan do replik pomocniczych do synchronizowania stan programu. Usługa Service Fabric automatycznie wykrywa, gdy replika podstawowa zakończy się niepowodzeniem i udoskonalenie istniejącej repliki pomocniczej do repliki podstawowej. Usługa Service Fabric utworzy nową replikę pomocniczą.  

**Replik lub wystąpień** można znaleźć kod (i stan usługi stanowe) to usługa, która jest wdrażana i uruchomiony. Zobacz [repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md).

**Ponowna konfiguracja** odwołuje się do procesu wszelkie zmiany w zestawie usługi. Zobacz [ponownej konfiguracji](service-fabric-concepts-reconfiguration.md).

**Pakiet usługi**: Katalog dysk zawierający typ usługi `ServiceManifest.xml` pliku. Odwołuje się do tego pliku, kod, dane statyczne i pakiety konfiguracji dla typu usługi. Pliki w katalogu pakietu usługi odwołuje się typ aplikacji `ApplicationManifest.xml` pliku. Na przykład pakiet usługi może odnosić się do kodu, dane statyczne i pakiety konfiguracji, które składają się z usługą bazy danych.

**O nazwie usługi**: Po utworzeniu aplikacji o nazwie, można utworzyć wystąpienia jednego z jej typów usług w klastrze. Należy określić typ usługi za pomocą jego nazwa/wersja. Każde wystąpienie typ usługi ma przypisaną nazwę identyfikatora URI o określonym zakresie w swojej aplikacji o nazwie w identyfikatorze URI. Na przykład, jeśli tworzysz "Moja_baza_danych" o nazwie usługi w ramach "MyNamedApp" o nazwie aplikacji, identyfikator URI wygląda: `"fabric:/MyNamedApp/MyDatabase"`. W przypadku aplikacji o nazwie można utworzyć kilka usług o nazwie. Każda usługa o nazwie może mieć własny schemat partycji i wystąpienia lub liczby replik.

**Pakiet kodu**: Katalog dysku zawierającego pliki wykonywalne typ usługi, zazwyczaj są to pliki EXE/DLL. Pliki w katalogu pakietu kodu są określone przez typ usługi `ServiceManifest.xml` pliku. Podczas tworzenia usługi o nazwie pakietu kodu jest kopiowany do węzła lub węzły zaznaczone, aby uruchomić nazwaną usługę. Następnie kod rozpoczyna się. Istnieją dwa rodzaje plików wykonywalnych pakietu kodu:

* **Pliki wykonywalne gościa**: Pliki wykonywalne, które Uruchom jako-znajduje się w systemie operacyjnym hosta (Windows lub Linux). Te pliki wykonywalne nie połączyć lub odwoływać się do wszystkich plików środowiska uruchomieniowego usługi Service Fabric i dlatego nie używać dowolnej usługi Service Fabric modeli programowania. Te pliki wykonywalne są w stanie korzystać z niektórych funkcji usługi Service Fabric, takich jak Usługa nazewnictwa dla punktu końcowego odnajdywania. Pliki wykonywalne gościa nie można zgłosić metryki obciążenia, które są specyficzne dla każdego wystąpienia usługi.
* **Usługa pliki wykonywalne hosta**: Pliki wykonywalne, używanego przez usługi Service Fabric modeli programowania, tworząc plików środowiska uruchomieniowego usługi Service Fabric, włączenie funkcji usługi Service Fabric. Na przykład nazwanym wystąpieniu usługi za pomocą usługi nazewnictwa Service Fabric można rejestrować punktów końcowych i zgłaszać także metryk obciążenia.

**Pakiet danych**: Katalog na dysku, który zawiera pliki danych statycznych, tylko do odczytu typ usługi, zwykle fotografii, pliki dźwiękowe i wideo. Pliki w katalogu pakietu danych odwołuje się typ usługi `ServiceManifest.xml` pliku. Podczas tworzenia usługi o nazwie pakiet danych jest kopiowany do węzła lub węzły zaznaczone, aby uruchomić nazwaną usługę. Kod zacznie działać, a teraz mogą uzyskać dostęp do plików danych.

**Pakiet konfiguracji**: Katalogu na dysku, który zawiera pliki statyczne, tylko do odczytu konfiguracji typ usługi, zwykle w pliki tekstowe. Pliki w katalogu pakietu konfiguracji odwołuje się typ usługi `ServiceManifest.xml` pliku. Podczas tworzenia nazwaną usługę plików w pakiecie konfiguracji są kopiowane lub większą liczbę węzłów wybrany uruchomić nazwaną usługę. A następnie zaczyna się kod uruchomić i można teraz dostęp do plików konfiguracji.

**Kontenery**: Domyślnie Usługa Service Fabric wdraża i aktywuje usługi jako procesów. Usługa Service Fabric można także wdrożyć usługi w obrazów kontenerów. Kontenery są technologii wirtualizacji, która Wirtualizuje system operacyjny z aplikacji. Aplikacja i jej środowiska uruchomieniowego, zależności i biblioteki systemu uruchamiane wewnątrz kontenera. Kontener ma pełne, prywatny dostęp do widoku kontenera własnych izolowany konstrukcji systemu operacyjnego. Usługa Service Fabric obsługuje kontenery platformy Docker na kontenery systemu Linux i Windows Server. Aby uzyskać więcej informacji, przeczytaj [usługi Service Fabric i kontenery](service-fabric-containers-overview.md).

**Schemat partycji**: Kiedy tworzysz nazwaną usługę, należy określić schemat partycji. Usługi za pomocą znacznej ilości stanu podzielenie danych między partycjami, co się stanie w węzłach klastra. Dzieląc dane na partycje, można skalować w stanie nazwaną usługę. W ramach partycji bezstanowej usługi o nazwie mieć wystąpienia, o nazwie usługi stanowe mają replik. Zazwyczaj bezstanowej usługi o nazwie ma tylko jedną partycję, ponieważ mają one nie stan wewnętrzny. Wystąpienia partycji zapewnienia dostępności. Jeśli jedno wystąpienie ulegnie awarii, inne instancje kontynuował normalne działanie, a następnie usługi Service Fabric tworzy nowe wystąpienie. Stanowe o nazwie usługi utrzymanie ich stan w obrębie repliki i każda partycja ma swój własny zestawu, stan jest utrzymywanych w synchronizacji replik. Replika ulegnie awarii, Usługa Service Fabric tworzy nową replikę z istniejących replik.

Odczyt [Partition Service Fabric reliable services](service-fabric-concepts-partitioning.md) artykuł, aby uzyskać więcej informacji.

## <a name="system-services"></a>Usługi systemowe
Dostępne są usługi systemowe, które są tworzone w każdym klastrze, które zapewniają możliwości platformy usługi Service Fabric.

**Usługa nazewnictwa**: Każdy klaster usługi Service Fabric ma usługi nazewnictwa, która jest rozpoznawana jako nazwy usług lokalizacji w klastrze. Zarządzanie nazwy usług i właściwości, takich jak internet System nazw domen (DNS) dla klastra. Klienci komunikują się bezpiecznie z dowolnego węzła w klastrze za pomocą usługi nazewnictwa rozpoznać nazwę usługi i jego lokalizacji. Przenoszenie aplikacji w klastrze. Na przykład może to być z powodu błędów, równoważenia zasobów lub zmiany rozmiaru klastra. Użytkownik może tworzyć, usług i klientów, które rozwiązać bieżącą lokalizację sieciową. Klient uzyskać rzeczywistą adres IP i port, na którym jest ono aktualnie uruchomione.

Odczyt [komunikacja z usługami](service-fabric-connect-and-communicate-with-services.md) więcej informacji na temat interfejsów API, które współpracują z usługą nazewnictwa komunikacji klienta i usługi.

**Usługa obraz Store**: Każdy klaster usługi Service Fabric ma usługę Store obrazu, w których przechowywane są pakiety aplikacji wdrożone, numerów wersji. Skopiuj pakiet aplikacji do Store obrazu, a następnie zarejestrować typ aplikacji zawartych w tym pakiecie aplikacji. Po aprowizacji typu aplikacji, można utworzyć aplikację o nazwie z niego. Po usunięciu wszystkich jej aplikacji o nazwie można wyrejestrować typ aplikacji w usłudze Store obrazu.

Odczyt [omówienie ustawienia ImageStoreConnectionString](service-fabric-image-store-connection-string.md) Aby uzyskać więcej informacji o usłudze Store obrazu.

Odczyt [wdrażania aplikacji](service-fabric-deploy-remove-applications.md) artykuł, aby uzyskać więcej informacji na temat wdrażania aplikacji w usłudze Store obrazu.

**Usługa Menedżer trybu failover**: Każdy klaster usługi Service Fabric ma usługa Menedżer trybu Failover, która jest odpowiedzialna za następujące akcje:
   - Wykonuje funkcje związane z wysoką dostępność i spójność usług.
   - Organizuje uaktualnień aplikacji i klastra.
   - Współdziała z innymi składnikami systemu.

**Usługa Menedżera naprawy**: Jest to opcjonalna usługa systemowa umożliwiającą naprawę akcje do wykonania w klastrze w sposób bezpieczny, możliwą i przejrzysty. Menedżer naprawy jest używany w:
   - Konserwacja platformy Azure naprawia na [trwałości Silver i Gold](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) klastrów usługi Azure Service Fabric.
   - Wykonanie akcji naprawy dla [Patch Orchestration Application](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Modele wdrażania i aplikacji 

Aby wdrożyć usługi, musisz opisują, jak powinno być ono uruchomione. Usługa Service Fabric obsługuje trzy różne modele wdrażania:

### <a name="resource-model-preview"></a>Model zasobów (wersja zapoznawcza)
Zasoby sieci szkieletowej usługi są wszystkie elementy, które można wdrożyć osobno dla usługi Service Fabric; w tym aplikacjami, usługami, sieci i woluminy. Zasoby są definiowane przy użyciu pliku JSON, który można wdrożyć w punkcie końcowym klastra.  Dla usługi Service Fabric siatki schematu modelu zasobów platformy Azure jest używana. Schemat pliku YAML można również łatwiej tworzyć pliki definicji. Zasoby można wdrożyć wszędzie tam, gdzie działa Usługa Service Fabric. Model zasobu jest najprostszym sposobem opisują aplikacje usługi Service Fabric. Głównym celem jest na proste wdrażanie i zarządzanie usług konteneryzowanych. Aby dowiedzieć się więcej, przeczytaj [wprowadzenie do modelu zasobów usługi Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Natywny model
Model aplikacji natywnej udostępnia swoje aplikacje za pomocą pełnej niskiego poziomu dostępu do usługi Service Fabric. Aplikacje i usługi są definiowane jako zarejestrowanych typów w plikach manifestu XML.

Natywny model obsługuje usług Reliable Services i Reliable Actors platform, która zapewnia dostęp do środowiska uruchomieniowego usługi Service Fabric interfejsów API i Zarządzanie klastrem interfejsów API w języku C# i Java. Natywny model obsługuje również dowolnego kontenerów i plików wykonywalnych. Natywny model nie jest obsługiwana w [środowiska usługi Service Fabric siatki](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**Usług Reliable Services**: Interfejs API, aby tworzyć usługi stanowe i bezstanowe. Usług stanowych przechowywania ich stan w elementach Reliable Collections, takich jak obiekt dictionary lub kolejki. Można także podłączyć w różnych stosów komunikacji, takich jak interfejs API sieci Web i usług Windows Communication Foundation (WCF).

**Elementy Reliable Actors**: Interfejs API do tworzenia stanowe i bezstanowe obiektów za pomocą modelu programowania wirtualnego aktora. Ten model jest przydatne, jeśli masz wiele niezależnych zespołów obliczeń lub stanu. Ten model wykorzystuje tury modelu wątkowości, więc go najlepiej jest unikać kod, który wywołuje się do innych aktorów lub usług, ponieważ poszczególne aktora nie może przetworzyć innych żądań przychodzących, zakończenie swoje żądania wychodzącego.

Można również uruchomić istniejących aplikacji w usłudze Service Fabric:

**Kontenery**:  Usługa Service Fabric obsługuje wdrażanie kontenerów Docker na kontenery systemu Linux i Windows Server w systemie Windows Server 2016, wraz z pomocy technicznej w trybie izolacji funkcji Hyper-V. W usłudze Service Fabric [model aplikacji](service-fabric-application-model.md), kontener reprezentuje aplikację hosta w wielu usług, w której są umieszczane replik. Usługa Service Fabric można uruchomić wszystkie kontenery i scenariusz jest podobny do scenariusza pliku wykonywalnego gościa, gdy pakiet istniejącej aplikacji w kontenerze. Ponadto mogą [uruchomienia usługi Service Fabric w kontenerach](service-fabric-services-inside-containers.md) także.

**Pliki wykonywalne gościa**: Dowolnego typu kodu, takich jak Node.js, Java lub C++ w usłudze Azure Service Fabric można uruchomić jako usługę. Usługa Service Fabric odnosi się do tych typów usług jako gościnne pliki wykonywalne, które są traktowane jako bezstanowej usługi. Zalety do uruchomionego pliku wykonywalnego w klastrze usługi Service Fabric Gość obejmują wysoką dostępność, monitorowanie kondycji, Zarządzanie cyklem życia aplikacji, o wysokiej gęstości i odnajdywania.

Odczyt [Wybieranie modelu programowania usługi](service-fabric-choose-framework.md) artykuł, aby uzyskać więcej informacji.

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) jest częścią projektu platformy Docker. Usługa Service Fabric udostępnia ograniczoną obsługę [wdrażanie aplikacji przy użyciu narzędzia Docker Compose modelu](service-fabric-docker-compose.md).

## <a name="environments"></a>Środowiska

Usługa Service Fabric to technologia platformie typu open source, opartych na kilka różnych usług i produktów. Firma Microsoft udostępnia następujące opcje:

 - **Usługa Azure Service Fabric siatki**: W pełni zarządzana Usługa do uruchamiania aplikacji usługi Service Fabric w systemie Microsoft Azure.
 - **Azure Service Fabric**: Azure hostowane oferty klastra usługi Service Fabric. Umożliwia integrację usługi Service Fabric i infrastruktury platformy Azure, wraz z uaktualniania i zarządzania konfiguracją z klastrami usługi Service Fabric.
 - **Autonomiczna Usługa Service Fabric**: Zestaw narzędzi do instalacji i konfiguracji do [wdrożyć klastry usługi Service Fabric w dowolnym miejscu](/azure/service-fabric/service-fabric-deploy-anywhere) (lokalnie lub na wszystkich dostawców chmury). Nie są zarządzane przez platformę Azure.
 - **Klaster projektowy usługi Service Fabric**: Zapewnia środowisko lokalne programowanie na Windows, Linux lub Mac do tworzenia aplikacji usługi Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Macierz obsługi środowiska, framework i modelu wdrażania
Różne środowiska mają różne poziomy wsparcia dla platformy i modele wdrażania. W poniższej tabeli opisano obsługiwane framework i kombinacji modelu wdrażania.

| Typ aplikacji | Opisany przez | Azure Service Fabric Mesh | Usługa Azure Service Fabric Clusters (dowolny system operacyjny)| Klaster lokalny | Klaster autonomiczny |
|---|---|---|---|---|---|
| Aplikacje siatki usługi Service Fabric | Model zasobów (YAML & JSON) | Obsługiwane |Nieobsługiwane | Windows — obsługiwane systemu Linux i Mac nie jest obsługiwane | Windows — użytkownik nie jest obsługiwane |
|Aplikacje natywne usługi Service Fabric | Model aplikacji natywnych (XML) | Nieobsługiwane| Obsługiwane|Obsługiwane|Windows — obsługiwana|

W poniższej tabeli opisano modele innej aplikacji i narzędzi, który istnieje dla nich względem usługi Service Fabric.

| Typ aplikacji | Opisany przez | Visual Studio | Eclipse | SFCTL | INTERFEJS WIERSZA POLECENIA AZ | PowerShell|
|---|---|---|---|---|---|---|
| Aplikacje siatki usługi Service Fabric | Model zasobów (YAML & JSON) | VS 2017 |Nieobsługiwane |Nieobsługiwane | Obsługiwane — tylko w środowisku siatki | Nieobsługiwane|
|Aplikacje natywne usługi Service Fabric | Model aplikacji natywnych (XML) | Premiera programu VS 2017 i VS 2015| Obsługiwane|Obsługiwane|Obsługiwane|Obsługiwane|

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat usługi Service Fabric:

* [Omówienie usługi Service Fabric](service-fabric-overview.md)
* [Why a microservices approach to building applications?](service-fabric-overview-microservices.md) (Dlaczego warto tworzyć aplikacje z użyciem mikrousług?)
* [Scenariusze aplikacji](service-fabric-application-scenarios.md)

Aby dowiedzieć się więcej na temat usługi Service Fabric siatki:

* [Omówienie usługi Service Fabric siatki](/azure/service-fabric-mesh/service-fabric-mesh-overview)
