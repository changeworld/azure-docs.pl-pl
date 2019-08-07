---
title: Poznaj terminologię dotyczącą platformy Azure Service Fabric | Microsoft Docs
description: Przegląd terminologii Service Fabric. Omawia kluczowe pojęcia i terminy używane w pozostałej części dokumentacji.
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
ms.openlocfilehash: 82672114722db843fcb5d0bdff28cf14cddb1aef
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68811919"
---
# <a name="service-fabric-terminology-overview"></a>Przegląd terminologii Service Fabric
Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi.  Klastry [Service Fabric można hostować w dowolnym miejscu](service-fabric-deploy-anywhere.md): Azure, w lokalnym centrum danych lub u dowolnego dostawcy chmury.  Service Fabric jest koordynatorem, który ma uprawnienia do [usługi Azure Service Fabric siatką](/azure/service-fabric-mesh). Możesz użyć dowolnej platformy, aby napisać usługi i wybrać miejsce uruchamiania aplikacji z wielu opcji środowiska. Ten artykuł zawiera szczegółowe informacje dotyczące terminologii używanej przez program Service Fabric do zrozumienia terminów używanych w dokumentacji.

## <a name="infrastructure-concepts"></a>Pojęcia dotyczące infrastruktury
**Klaster**: Połączony z siecią zestaw maszyn wirtualnych lub fizycznych, w których są wdrażane i zarządzane mikrousługi.  Klastry mogą obejmować nawet tysiące maszyn.

**Węzeł**: Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają właściwości, takie jak właściwości umieszczania. Każda maszyna lub maszyna wirtualna ma uruchomioną funkcję autostartu `FabricHost.exe`systemu Windows, która jest uruchamiana po rozruchu, a następnie uruchamia `Fabric.exe` dwa `FabricGateway.exe`pliki wykonywalne: i. Te dwa pliki wykonywalne składają się na węzeł. Scenariusze testowania umożliwiają hostowanie wielu węzłów na pojedynczej maszynie lub maszynie wirtualnej przez uruchomienie wielu wystąpień `Fabric.exe` i. `FabricGateway.exe`

## <a name="application-and-service-concepts"></a>Pojęcia dotyczące aplikacji i usług

**Aplikacja siatki Service Fabric**: Aplikacje siatki Service Fabric są opisane przez model zasobów (pliki zasobów YAML i JSON) i można je wdrożyć w dowolnym środowisku, w którym są uruchamiane Service Fabric.

**Aplikacja natywna Service Fabric**: Aplikacje natywne Service Fabric są opisane przez natywny model aplikacji (oparty na języku XML i manifesty usług).  Aplikacje natywne Service Fabric nie mogą być uruchamiane w Service Fabric siatki.

### <a name="service-fabric-mesh-application-concepts"></a>Pojęcia dotyczące aplikacji Service Fabric siatki

**Aplikacja**: Aplikacja jest jednostką wdrożenia, przechowywaniem wersji i okresem istnienia aplikacji siatkowej. Cykl życia każdego wystąpienia aplikacji może być zarządzany niezależnie.  Aplikacje składają się z co najmniej jednego pakietu i ustawień kodu usługi. Aplikacja jest definiowana przy użyciu schematu modelu zasobów platformy Azure (RM).  Usługi są opisane jako właściwości zasobu aplikacji w szablonie Menedżera zasobów.  Aplikacje i woluminy używane przez aplikację są przywoływane przez aplikację.  Podczas tworzenia aplikacji aplikacje, usługi, sieci i woluminy są modelowane przy użyciu modelu zasobów Service Fabric.

**Usługa**: Usługa w aplikacji reprezentuje mikrousługę i wykonuje pełną i autonomiczną funkcję. Każda usługa składa się z jednego lub więcej pakietów kodu, które opisują wszystko, czego potrzebujesz do uruchomienia obrazu kontenera skojarzonego z pakietem kodu.  Liczbę usług w aplikacji można skalować w górę i w dół.

**Sieć**: Zasób sieciowy tworzy sieć prywatną dla aplikacji i jest niezależna od aplikacji lub usług, które mogą się do niej odwoływać. Wiele usług z różnych aplikacji może być częścią tej samej sieci. Sieci to zasoby do wdrożenia, do których odwołują się aplikacje.

**Pakiet kodu**: Pakiety kodu opisują wszystkie elementy potrzebne do uruchomienia obrazu kontenera skojarzonego z pakietem kodu, w tym następujące:

* Nazwa, wersja i rejestr kontenera
* Zasoby procesora i pamięci wymagane dla każdego kontenera
* Punkty końcowe sieci
* Woluminy do zainstalowania w kontenerze, odwołujące się do oddzielnego zasobu woluminu.

Wszystkie pakiety kodu zdefiniowane jako część zasobu aplikacji są wdrażane i uaktywniane razem jako Grupa.

**Wolumin**: Woluminy są katalogami, które są instalowane w ramach wystąpień kontenera, których można użyć do utrwalania stanu. Sterownik woluminu Azure Files instaluje udział Azure Files w kontenerze i zapewnia niezawodne przechowywanie danych za pomocą dowolnych interfejsów API, które obsługują magazyn plików. Woluminy są zasobami do wdrożenia, do których odwołują się aplikacje.

### <a name="service-fabric-native-application-concepts"></a>Service Fabric natywne koncepcje aplikacji

**Aplikacja**: Aplikacja jest kolekcją usług składowych, które wykonują określoną funkcję lub funkcje. Cykl życia każdego wystąpienia aplikacji może być zarządzany niezależnie.

**Usługa**: Usługa wykonuje kompletną i autonomiczną funkcję, która może być uruchamiana i uruchamiana niezależnie od innych usług. Usługa składa się z kodu, konfiguracji i danych. W przypadku każdej usługi kod składa się z wykonywalnych plików binarnych, konfiguracja składa się z ustawień usługi, które mogą być ładowane w czasie wykonywania, a dane składają się z dowolnych danych statycznych, które mają być używane przez usługę.

**Typ aplikacji**: Nazwa/wersja przypisana do kolekcji typów usług. Jest on zdefiniowany w `ApplicationManifest.xml` pliku i osadzony w katalogu pakietu aplikacji. Katalog jest następnie kopiowany do magazynu obrazów klastra Service Fabric. Następnie można utworzyć nazwaną aplikację z tego typu aplikacji w ramach klastra.

Aby uzyskać więcej informacji, przeczytaj artykuł dotyczący [modelu aplikacji](service-fabric-application-model.md) .

**Pakiet aplikacji**: Katalog dyskowy zawierający `ApplicationManifest.xml` plik typu aplikacji. Odwołuje się do pakietów usługi dla każdego typu usługi tworzących typ aplikacji. Pliki w katalogu pakietu aplikacji są kopiowane do magazynu obrazów klastra Service Fabric. Na przykład pakiet aplikacji typu aplikacja poczty e-mail może zawierać odwołania do pakietu usługi kolejki, pakietu usługi frontonu i pakietu usługi Database.

**Nazwana aplikacja**: Po skopiowaniu pakietu aplikacji do magazynu obrazów należy utworzyć wystąpienie aplikacji w ramach klastra. Wystąpienie jest tworzone podczas określania typu aplikacji pakietu aplikacji przy użyciu jego nazwy lub wersji. Każde wystąpienie typu aplikacji ma przypisaną nazwę URI (Uniform Resource Identifier), która wygląda następująco: `"fabric:/MyNamedApp"`. W ramach klastra można utworzyć wiele nazwanych aplikacji z jednego typu aplikacji. Można również tworzyć nazwane aplikacje z różnych typów aplikacji. Każda nazwana aplikacja jest zarządzana i niezależnie od wersji.

**Typ usługi**: Nazwa/wersja przypisana do pakietów kodu usługi, pakietów danych i pakietów konfiguracyjnych. Typ usługi jest zdefiniowany w `ServiceManifest.xml` pliku i osadzony w katalogu pakietu usługi. Do katalogu pakietu usługi odwołuje się `ApplicationManifest.xml` plik pakietu aplikacji. W klastrze po utworzeniu nazwanej aplikacji można utworzyć nazwę usługi na podstawie jednego z typów usług typu aplikacji. `ServiceManifest.xml` Plik typu usługi zawiera opis usługi.

Aby uzyskać więcej informacji, przeczytaj artykuł dotyczący [modelu aplikacji](service-fabric-application-model.md) .

Istnieją dwa typy usług:

* Bezstanowa: Użyj usługi bezstanowej, gdy stan trwały usługi jest przechowywany w zewnętrznej usłudze magazynu, takiej jak Azure Storage, Azure SQL Database lub Azure Cosmos DB. Użyj usługi bezstanowej, gdy usługa nie ma trwałego magazynu. Na przykład w przypadku usługi kalkulatora, w której wartości są przesyłane do usługi, wykonywane jest obliczenie, które korzysta z tych wartości, a następnie zwracany jest wynik.
* **Stanowa**: Użyj usługi stanowej, jeśli chcesz, aby Service Fabric zarządzać stanem usługi za pośrednictwem jego niezawodnych kolekcji lub Reliable Actors modeli programowania. Podczas tworzenia nazwanej usługi Określ, ile partycji chcesz rozłożyć stan na skalowalność. Określ również, ile razy należy replikować stan między węzłami w celu zapewnienia niezawodności. Każda nazwana usługa ma jedną replikę podstawową i wiele replik pomocniczych. Użytkownik modyfikuje stan nazwanej usługi podczas zapisu w replice podstawowej. Service Fabric następnie replikuje ten stan do wszystkich replik pomocniczych, aby zachować stan synchronizacji. Service Fabric automatycznie wykrywa, kiedy replika podstawowa kończy się niepowodzeniem i promuje istniejącą replikę pomocniczą do repliki podstawowej. Service Fabric następnie tworzy nową replikę pomocniczą.  

**Repliki lub wystąpienia** odnoszą się do kodu (i stanu dla usług stanowych) usługi, która jest wdrażana i uruchomiona. Zobacz [repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md).

**Ponowna konfiguracja** dotyczy procesu zmiany w zestawie replik usługi. Zobacz [ponowna konfiguracja](service-fabric-concepts-reconfiguration.md).

**Pakiet usługi**: Katalog dyskowy zawierający `ServiceManifest.xml` plik typu usługi. Ten plik odwołuje się do kodu, danych statycznych i pakietów konfiguracyjnych dla typu usługi. Pliki w katalogu pakietu usługi są przywoływane przez `ApplicationManifest.xml` plik typu aplikacji. Na przykład pakiet usługi może odwoływać się do kodu, danych statycznych i pakietów konfiguracyjnych tworzących usługę bazy danych.

**Nazwana usługa**: Po utworzeniu nazwanej aplikacji można utworzyć wystąpienie jednego z jego typów usług w ramach klastra. Typ usługi należy określić przy użyciu jego nazwy lub wersji. Każde wystąpienie typu usługi ma przypisaną nazwę identyfikatora URI w zakresie identyfikatora URI aplikacji. Jeśli na przykład utworzysz nazwę usługi "Moja baza danych" w nazwie "MyNamedApp", identyfikator URI wygląda następująco: `"fabric:/MyNamedApp/MyDatabase"`. W ramach nazwanej aplikacji można utworzyć kilka nazwanych usług. Każda nazwana usługa może mieć własny schemat partycji oraz liczbę wystąpień lub replik.

**Pakiet kodu**: Katalog dysku zawierający pliki wykonywalne typu usługi, zazwyczaj pliki EXE/DLL. Pliki w katalogu pakietu kodu są przywoływane przez `ServiceManifest.xml` plik typu usługi. Podczas tworzenia nazwanej usługi pakiet kodu jest kopiowany do węzła lub węzłów wybranych do uruchomienia nazwanej usługi. Następnie uruchamiany jest kod. Istnieją dwa typy plików wykonywalnych pakietu kodu:

* **Pliki wykonywalne gościa**: Pliki wykonywalne uruchomione jako — są w systemie operacyjnym hosta (Windows lub Linux). Te pliki wykonywalne nie łączą się ani nie odwołują do żadnych plików środowiska uruchomieniowego Service Fabric i w związku z tym nie używają Service Fabric modeli programowania. Te pliki wykonywalne nie mogą używać niektórych funkcji Service Fabric, takich jak usługa nazewnictwa dla odnajdywania punktów końcowych. Pliki wykonywalne gościa nie mogą raportować metryk obciążenia, które są specyficzne dla każdego wystąpienia usługi.
* **Pliki wykonywalne hosta usługi**: Pliki wykonywalne, które używają Service Fabric modeli programowania, łącząc do plików środowiska uruchomieniowego Service Fabric, włączając funkcje Service Fabric. Na przykład nazwane wystąpienie usługi może zarejestrować punkty końcowe z Usługa nazewnictwa Service Fabric, a także zgłaszać metryki obciążenia.

**Pakiet danych**: Katalog dysku, który zawiera statyczny, plik danych tylko do odczytu, zazwyczaj pliki z fotografiami, dźwiękiem i wideo. Pliki w katalogu pakietu danych są przywoływane przez `ServiceManifest.xml` plik typu usługi. Podczas tworzenia nazwanej usługi pakiet danych jest kopiowany do węzła lub węzłów wybranych do uruchomienia nazwanej usługi. Kod zaczyna działać i teraz może uzyskiwać dostęp do plików danych.

**Pakiet konfiguracyjny**: Katalog dysku zawierający statyczne pliki konfiguracji typu usługi, które są zazwyczaj plikami tekstowymi. Pliki w katalogu pakietu konfiguracyjnego są przywoływane przez `ServiceManifest.xml` plik typu usługi. Podczas tworzenia nazwanej usługi pliki w pakiecie konfiguracyjnym są kopiowane co najmniej jeden węzeł wybrany do uruchomienia nazwanej usługi. Następnie kod rozpocznie pracę i teraz może uzyskiwać dostęp do plików konfiguracji.

**Kontenery**: Domyślnie program Service Fabric wdraża i aktywuje usługi jako procesy. Service Fabric może również wdrażać usługi w obrazach kontenera. Kontenery są technologią wirtualizacji, która umożliwia wirtualizację bazowego systemu operacyjnego od aplikacji. Aplikacja i jej środowisko uruchomieniowe, zależności i biblioteki systemowe działają wewnątrz kontenera. Kontener ma pełny, prywatny dostęp do własnego widoku izolowanego konstrukcji systemu operacyjnego kontenera. Service Fabric obsługuje kontenery platformy Docker w kontenerach systemu Linux i Windows Server. Aby uzyskać więcej informacji, Przeczytaj [Service Fabric i kontenery](service-fabric-containers-overview.md).

**Schemat partycji**: Podczas tworzenia nazwanej usługi należy określić schemat partycji. Usługi o znacznym stanie dzielą dane między partycjami, co umożliwia rozmieszczenie stanu w węzłach klastra. Dzielenie danych między partycjami umożliwia skalowanie stanu nazwanej usługi. W ramach partycji usługi bezstanowe mają wystąpienia, a stanowe usługi mają repliki. Zwykle o nazwie usługi bezstanowe mają tylko jedną partycję, ponieważ nie mają wewnętrznego stanu. Wystąpienia partycji zapewniają dostępność. Jeśli jedno wystąpienie nie powiedzie się, inne wystąpienia nadal działają normalnie, a następnie Service Fabric tworzy nowe wystąpienie. Stanowe nazwy usług utrzymują swój stan w replikach, a każda partycja ma swój własny zestaw replik, aby zachować stan synchronizacji. Jeśli replika nie powiedzie się, Service Fabric kompiluje nową replikę z istniejących replik.

Aby uzyskać więcej informacji, przeczytaj artykuł [Partition Service Fabric unniezawodny Services](service-fabric-concepts-partitioning.md) .

## <a name="system-services"></a>Usługi systemowe
Istnieją usługi systemowe, które są tworzone w każdym klastrze, który zapewnia możliwości platformy Service Fabric.

**Usługa nazewnictwa**: Każdy klaster Service Fabric ma Usługa nazewnictwa, który rozwiązuje nazwy usług do lokalizacji w klastrze. Zarządzasz nazwami i właściwościami usługi, takimi jak system DNS (Internet Name Domain) klastra. Klienci bezpiecznie komunikują się z dowolnym węzłem w klastrze za pomocą Usługa nazewnictwa w celu rozpoznania nazwy usługi i jej lokalizacji. Aplikacje są przenoszone w ramach klastra. Na przykład może to być spowodowane błędami, równoważeniem zasobów lub zmianą wielkości klastra. Można opracowywać usługi i klientów, które rozwiązują bieżącą lokalizację sieciową. Klienci uzyskują rzeczywisty adres IP i port na komputerze, na którym jest obecnie uruchomiony.

Przeczytaj temat [komunikacja z usługami](service-fabric-connect-and-communicate-with-services.md) , aby uzyskać więcej informacji na temat interfejsów API komunikacji klienta i usługi, które współpracują z usługa nazewnictwa.

**Usługa Magazyn obrazów**: Każdy klaster Service Fabric ma usługę Magazyn obrazów, w której wdrożone są pakiety aplikacji w wersji. Skopiuj pakiet aplikacji do Magazyn obrazów a następnie zarejestruj typ aplikacji zawarty w pakiecie aplikacji. Po zainicjowaniu obsługi typu aplikacji utworzysz nazwę aplikacji. Typ aplikacji można wyrejestrować z usługi Magazyn obrazów po usunięciu wszystkich jej nazwanych aplikacji.

Aby uzyskać więcej informacji na temat usługi Magazyn obrazów, Przeczytaj [Opis ustawienia ImageStoreConnectionString](service-fabric-image-store-connection-string.md) .

Zapoznaj się z artykułem [wdrażanie aplikacji](service-fabric-deploy-remove-applications.md) , aby uzyskać więcej informacji na temat wdrażania aplikacji w usłudze Magazyn obrazów.

**Usługa Menedżer trybu failover**: Każdy klaster Service Fabric ma usługę Menedżer trybu failover, która jest odpowiedzialna za następujące działania:
   - Wykonuje funkcje związane z wysoką dostępnością i spójnością usług.
   - Organizuje uaktualnienia aplikacji i klastra.
   - Współdziała z innymi składnikami systemowymi.

**Usługa Menedżer naprawy**: Jest to opcjonalna usługa systemowa, która umożliwia wykonywanie akcji naprawczych w klastrze w taki sposób, który jest bezpieczny, automatyzuje i przejrzysty. Menedżer naprawy jest używany w programie:
   - Przeprowadzanie napraw na platformie Azure w przypadku klastrów usługi Azure Service Fabric [Silver i Gold trwałości](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) .
   - Wykonywanie akcji naprawy dla [aplikacji aranżacji patch](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Modele wdrożenia i aplikacji 

Aby wdrożyć usługi, należy opisać sposób ich działania. Service Fabric obsługuje trzy różne modele wdrażania:

### <a name="resource-model-preview"></a>Model zasobów (wersja zapoznawcza)
Zasoby Service Fabric są dowolne, które mogą być wdrażane pojedynczo do Service Fabric; obejmuje to aplikacje, usługi, sieci i woluminy. Zasoby są definiowane przy użyciu pliku JSON, który można wdrożyć w punkcie końcowym klastra.  W przypadku Service Fabric siatki używany jest schemat modelu zasobów platformy Azure. Schemat pliku YAML może być również używany do łatwiejszego tworzenia plików definicji. Zasoby można wdrożyć w dowolnym miejscu Service Fabric uruchomienia. Model zasobów to najprostszy sposób opisywania Service Fabric aplikacji. Głównym celem jest proste wdrażanie usług kontenerowych i zarządzanie nimi. Aby dowiedzieć się więcej, Przeczytaj [wprowadzenie do modelu zasobów Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Model natywny
Natywny model aplikacji zapewnia aplikacjom pełny dostęp niskiego poziomu do Service Fabric. Aplikacje i usługi są zdefiniowane jako zarejestrowane typy w plikach manifestu XML.

Model natywny obsługuje struktury Reliable Services i Reliable Actors, które zapewniają dostęp do interfejsów API środowiska uruchomieniowego Service Fabric oraz interfejsów API zarządzania klastrami w C# językach i Java. Model natywny obsługuje również dowolne kontenery i pliki wykonywalne. Model natywny nie jest obsługiwany w [środowisku Service Fabric siatki](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**Reliable Services**: Interfejs API służący do budowania usług bezstanowych i stanowych. Usługi stanowe przechowują swój stan w niezawodnych kolekcjach, takich jak słownik lub kolejka. Możesz również podłączyć różne stosy komunikacji, takie jak Web API i Windows Communication Foundation (WCF).

**Reliable Actors**: Interfejs API służący do tworzenia bezstanowych i stanowych obiektów za pomocą modelu programowania aktorów wirtualnych. Ten model jest przydatny w przypadku wielu niezależnych jednostek obliczeniowych lub stanu. Ten model korzysta z modelu wątkowego opartego na przejściu, dlatego najlepiej jest unikać kodu, który wywołuje inne aktory lub usługi, ponieważ pojedynczy aktor nie może przetwarzać innych żądań przychodzących, dopóki nie zostaną zakończone wszystkie żądania wychodzące.

Możesz również uruchomić istniejące aplikacje na Service Fabric:

**Kontenery**:  Service Fabric obsługuje wdrażanie kontenerów platformy Docker w kontenerach systemu Linux i Windows Server w systemie Windows Server 2016 wraz z obsługą trybu izolacji funkcji Hyper-V. W [modelu aplikacji](service-fabric-application-model.md)Service Fabric kontener reprezentuje hosta aplikacji, w którym umieszczane są wiele replik usługi. Service Fabric może uruchamiać dowolne kontenery, a scenariusz jest podobny do scenariusza plików wykonywalnych gościa, w którym jest spakowana istniejąca aplikacja w kontenerze. Ponadto można również [uruchamiać usługi Service Fabric w kontenerach](service-fabric-services-inside-containers.md) .

**Pliki wykonywalne gościa**: Można uruchomić dowolny typ kodu, taki jak Node. js, Python, Java lub C++ na platformie Azure Service Fabric jako usługa. Service Fabric odnosi się do tych typów usług jako plików wykonywalnych gościa, które są traktowane jako usługi bezstanowe. Zalety uruchamiania pliku wykonywalnego gościa w klastrze Service Fabric obejmują wysoką dostępność, monitorowanie kondycji, zarządzanie cyklem życia aplikacji, wysoką gęstość i możliwość odnajdywania.

Aby uzyskać więcej informacji, zapoznaj się z artykułem [Wybieranie modelu programowania dla usługi](service-fabric-choose-framework.md) .

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) jest częścią projektu platformy Docker. Service Fabric zapewnia ograniczoną obsługę [wdrażania aplikacji przy użyciu modelu Docker Compose](service-fabric-docker-compose.md).

## <a name="environments"></a>Środowiska

Service Fabric to technologia platformy Open Source, w której bazują kilka różnych usług i produktów. Firma Microsoft oferuje następujące opcje:

 - **Siatka Service Fabric platformy Azure**: W pełni zarządzana usługa do uruchamiania aplikacji Service Fabric w Microsoft Azure.
 - **Service Fabric platformy Azure**: Oferta hostowanego klastra Service Fabric platformy Azure. Zapewnia ona integrację między Service Fabric i infrastrukturą platformy Azure oraz zarządzaniem i konfiguracją klastrów Service Fabric.
 - **Service Fabric autonomiczny**: Zestaw narzędzi instalacji i konfiguracji do [wdrażania klastrów Service Fabric w dowolnym miejscu](/azure/service-fabric/service-fabric-deploy-anywhere) (lokalnie lub u dowolnego dostawcy chmury). Niezarządzana przez platformę Azure.
 - **Service Fabric klaster projektowy**: Program udostępnia lokalne środowisko programistyczne w systemie Windows, Linux lub Mac na potrzeby programowania aplikacji Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Macierz obsługi środowiska, architektury i modelu wdrażania
Różne środowiska mają różne poziomy obsługi dla struktur i modeli wdrażania. W poniższej tabeli opisano obsługiwane kombinacje platform i modeli wdrażania.

| Typ aplikacji | Opisane przez | Azure Service Fabric Mesh | Klastry usługi Azure Service Fabric (każdy system operacyjny)| Klaster lokalny | Klaster autonomiczny |
|---|---|---|---|---|---|
| Aplikacje siatki Service Fabric | Model zasobów (YAML & JSON) | Obsługiwane |Nieobsługiwane | Windows — obsługiwane, Linux i Mac — nieobsługiwane | Windows — nieobsługiwane |
|Aplikacje natywne Service Fabric | Natywny model aplikacji (XML) | Brak obsługi| Obsługiwane|Obsługiwane|Windows — obsługiwane|

W poniższej tabeli opisano różne modele aplikacji i narzędzia, które istnieją dla nich w odniesieniu do Service Fabric.

| Typ aplikacji | Opisane przez | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Aplikacje siatki Service Fabric | Model zasobów (YAML & JSON) | VS 2017 |Nieobsługiwane |Nieobsługiwane | Tylko obsługiwane środowisko siatki | Brak obsługi|
|Aplikacje natywne Service Fabric | Natywny model aplikacji (XML) | VS 2017 i VS 2015| Obsługiwane|Obsługiwane|Obsługiwane|Obsługiwane|

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Service Fabric:

* [Omówienie Service Fabric](service-fabric-overview.md)
* [Why a microservices approach to building applications?](service-fabric-overview-microservices.md) (Dlaczego warto tworzyć aplikacje z użyciem mikrousług?)
* [Scenariusze aplikacji](service-fabric-application-scenarios.md)

Aby dowiedzieć się więcej na temat Service Fabric siatki:

* [Przegląd Service Fabric siatki](/azure/service-fabric-mesh/service-fabric-mesh-overview)
