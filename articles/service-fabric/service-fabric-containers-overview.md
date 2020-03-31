---
title: Omówienie sieci szkieletowej usług i kontenerów
description: Omówienie sieci szkieletowej usług i użycia kontenerów do wdrażania aplikacji mikrousług. Ten artykuł zawiera omówienie sposobu, w jaki kontenery mogą być używane i dostępne możliwości w sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 8/8/2018
ms.openlocfilehash: 884cefa3d6a60f55269afac73c40b9f6b21518f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458225"
---
# <a name="service-fabric-and-containers"></a>Tkanina serwisowa i kontenery

## <a name="introduction"></a>Wprowadzenie

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług i kontenerów oraz zarządzanie nimi.

Usługa Sieci szkieletowej jest [koordynatorem kontenerów](service-fabric-cluster-resource-manager-introduction.md) firmy Microsoft do wdrażania mikrousług w klastrze komputerów. Usługa Service Fabric korzysta z doświadczeń zdobytych podczas jego lat pracy usług w firmie Microsoft na masową skalę.

Mikrousługi można tworzyć na wiele sposobów, na przykład korzystając z [modeli programowania usługi Service Fabric](service-fabric-choose-framework.md) czy struktury [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) lub wdrażając [dowolnie wybrany kod](service-fabric-guest-executables-introduction.md). Lub, jeśli chcesz po prostu [wdrożyć i zarządzać kontenerami,](service-fabric-containers-overview.md)sieci szkieletowej usług jest również doskonałym wyborem.

Domyślnie sieci szkieletowej usług wdraża i aktywuje te usługi jako procesy. Procesy zapewniają najszybszą aktywację i najwyższą gęstość użycia zasobów w klastrze. Sieci szkieletowej usług można również wdrożyć usługi w obrazach kontenerów. Można również mieszać usługi w procesach i usług w kontenerach, w tej samej aplikacji.

Aby przejść bezpośrednio i wypróbować kontenery w sieci szkieletowej usług, spróbuj szybki start, samouczek lub przykład:  

[Szybki start: wdrażanie aplikacji kontenera systemu Linux w sieci szkieletowej usług](service-fabric-quickstart-containers-linux.md)  
[Szybki start: wdrażanie aplikacji kontenera systemu Windows w sieci szkieletowej usług](service-fabric-quickstart-containers.md)  
[Konteneryzowanie istniejącej aplikacji .NET](service-fabric-host-app-in-a-container.md)  
[Przykłady kontenera usługi Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Co to są kontenery

Kontenery rozwiązać problem uruchamiania aplikacji niezawodnie w różnych środowiskach komputerowych, zapewniając niezmienne środowisko dla aplikacji do uruchomienia w. Kontenery zawijają aplikację i wszystkie jej zależności, takie jak biblioteki i pliki konfiguracyjne, do własnego izolowanego "pola", które zawiera wszystko, co jest potrzebne do uruchomienia oprogramowania wewnątrz kontenera. Wszędzie tam, gdzie działa kontener, aplikacja wewnątrz zawsze ma wszystko, czego potrzebuje do uruchomienia, takie jak odpowiednie wersje bibliotek zależnych, pliki konfiguracyjne i wszystko, czego potrzebuje do uruchomienia.

Kontenery są uruchamiane bezpośrednio na jądrze i mają izolowany widok systemu plików i innych zasobów. Aplikacja w kontenerze nie ma wiedzy o innych aplikacjach lub procesach poza jego kontenerem. Każda aplikacja i jej środowisko uruchomieniowe, zależności i biblioteki systemowe są uruchamiane wewnątrz kontenera z pełnym, prywatnym dostępem do własnego izolowanego widoku kontenera systemu operacyjnego. Oprócz ułatwienia dostarczania wszystkich zależności aplikacji, które muszą działać w różnych środowiskach obliczeniowych, bezpieczeństwo i izolacja zasobów są ważnymi korzyściami z używania kontenerów z siecią szkieletową usług - która w przeciwnym razie uruchamia usługi w Proces.

W porównaniu do maszyn wirtualnych kontenery mają następujące zalety:

* **Małe:** Kontenery używają pojedynczego miejsca do magazynowania i wersji warstwy oraz aktualizacji, aby zwiększyć wydajność.
* **Szybko:** kontenery nie muszą uruchamiać całego systemu operacyjnego, dzięki czemu mogą uruchamiać się znacznie szybciej — zazwyczaj w ciągu kilku sekund.
* **Przenośność:** konteneryzowany obraz aplikacji można przenieść do pracy w chmurze, lokalnie, wewnątrz maszyn wirtualnych lub bezpośrednio na komputerach fizycznych.
* **Zarządzanie zasobami:** Kontener może ograniczyć zasoby fizyczne, które może zużywać na swoim hoście.

### <a name="container-types-and-supported-environments"></a>Typy kontenerów i obsługiwane środowiska

Usługa Service Fabric obsługuje kontenery w systemie Linux i Windows i obsługuje tryb izolacji funkcji Hyper-V w systemie Windows.

#### <a name="docker-containers-on-linux"></a>Kontenery platformy Docker w systemie Linux

Docker udostępnia interfejsy API do tworzenia kontenerów i zarządzania nimi na kontenerach jądra systemu Linux. Docker Hub udostępnia centralne repozytorium do przechowywania i pobierania obrazów kontenerów.
Aby zapoznać się z samouczkiem opartym na systemie Linux, zobacz [Tworzenie pierwszej aplikacji kontenera sieci szkieletowej usługi w systemie Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Kontenery systemu Windows Server

System Windows Server 2016 udostępnia dwa różne typy kontenerów, które różnią się poziomem izolacji. Kontenery systemu Windows Server i kontenery platformy Docker są podobne, ponieważ oba mają obszar nazw i izolację systemu plików, podczas udostępniania jądra z hostem, na który są uruchomione. W systemie Linux ta izolacja tradycyjnie została dostarczona przez cgroups i przestrzenie nazw, a kontenery systemu Windows Server zachowują się podobnie.

Kontenery systemu Windows z obsługą funkcji Hyper-V zapewniają większą izolację i bezpieczeństwo, ponieważ żaden kontener nie współużytkuje jądra systemu operacyjnego z innym kontenerem lub hostem. Przy tym wyższym poziomie izolacji zabezpieczeń kontenery z obsługą funkcji Hyper-V są przeznaczone dla potencjalnie wrogich scenariuszy z wieloma dzierżawami.
Aby zapoznać się z samouczkiem opartym na systemie Windows, zobacz [Tworzenie pierwszej aplikacji kontenera sieci szkieletowej usług w systemie Windows](service-fabric-get-started-containers.md).

Na poniższej ilustracji przedstawiono różne typy dostępnych poziomów wirtualizacji i izolacji.
![Platforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Scenariusze używania kontenerów

Oto typowe przykłady, w których kontener jest dobrym wyborem:

* **Usługi IIS lift and shift**: Można umieścić istniejącą [aplikację ASP.NET MVC](https://www.asp.net/mvc) w kontenerze zamiast migrować ją do ASP.NET Core. Te ASP.NET aplikacje MVC zależą od internetowych usług informacyjnych (IIS). Można spakować te aplikacje do obrazów kontenerów z wstępnie stwórzonego obrazu usług IIS i wdrożyć je za pomocą sieci szkieletowej usług. Aby uzyskać informacje o kontenerach systemu Windows, zobacz [Obrazy kontenerów w systemie Windows.](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)

* **Mieszaj kontenery i mikrousług sieci szkieletowej usług:** Użyj istniejącego obrazu kontenera dla części aplikacji. Na przykład można użyć [kontenera NGINX](https://hub.docker.com/_/nginx/) dla frontonu sieci web aplikacji i usług stanowych dla bardziej intensywnych obliczeń zaplecza.

* **Zmniejsz wpływ usług "hałaśliwych sąsiadów":** można użyć możliwości zarządzania zasobami kontenerów, aby ograniczyć zasoby używane przez usługę na hoście. Jeśli usługi mogą zużywać wiele zasobów i wpływać na wydajność innych (takich jak długotrwała operacja podobna do kwerend), należy rozważyć umieszczenie tych usług w kontenerach, które mają zarządzanie zasobami.

## <a name="service-fabric-support-for-containers"></a>Obsługa usługi Service Fabric dla kontenerów

Usługa Sieci szkieletowej obsługuje wdrażanie kontenerów platformy Docker w systemie Linux i kontenerów systemu Windows Server w systemie Windows Server 2016, a także obsługę trybu izolacji funkcji Hyper-V. 

Usługa Sieci szkieletowej udostępnia [model aplikacji,](service-fabric-application-model.md) w którym kontener reprezentuje hosta aplikacji, w którym są umieszczane wiele replik usług. Sieć szkieletowa usług obsługuje również [scenariusz pliku wykonywalnego gościa,](service-fabric-guest-executables-introduction.md) w którym nie używasz wbudowanych modeli programowania sieci szkieletowej usług, ale zamiast tego pakujesz istniejącą aplikację, napisaną przy użyciu dowolnego języka lub struktury, wewnątrz kontenera. W tym scenariuszu jest typowy przypadek użycia kontenerów.

Można również uruchomić [usługi sieci szkieletowej usług wewnątrz kontenera](service-fabric-services-inside-containers.md). Obsługa uruchamiania usług sieci szkieletowej usług wewnątrz kontenerów jest obecnie ograniczona.

Sieci szkieletowej usług zawiera kilka możliwości kontenera, które ułatwiają tworzenie aplikacji, które składają się z konteneryzowanych mikrousług, takich jak:

* Wdrażanie i aktywacja obrazu kontenera.
* Zarządzanie zasobami, w tym domyślne ustawianie wartości zasobów w klastrach platformy Azure.
* Uwierzytelnianie repozytorium.
* Port kontenera do hostowania mapowania portów.
* Odnajdowanie i komunikacja między kontenerami.
* Możliwość konfigurowania i ustawiania zmiennych środowiskowych.
* Możliwość ustawiania poświadczeń zabezpieczeń w kontenerze.
* Wybór różnych trybów sieci dla kontenerów.

Aby uzyskać kompleksowe omówienie obsługi kontenerów na platformie Azure, takie jak tworzenie klastra Kubernetes za pomocą usługi Azure Kubernetes, jak utworzyć prywatny rejestr platformy Docker w rejestrze kontenerów platformy Azure i inne, zobacz [Azure for Containers.](https://docs.microsoft.com/azure/containers/)

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiedziałeś się o pomocy technicznej sieci szkieletowej usług zapewnia uruchamianie kontenerów. Następnie przejdziemy do przykładów każdej z funkcji, aby pokazać, jak z nich korzystać.

[Tworzenie pierwszej aplikacji kontenera usługi Service Fabric w systemie Linux](service-fabric-get-started-containers-linux.md)  
[Tworzenie pierwszej aplikacji kontenera usługi Service Fabric w systemie Windows](service-fabric-get-started-containers.md)  
[Dowiedz się więcej o kontenerach systemu Windows](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png