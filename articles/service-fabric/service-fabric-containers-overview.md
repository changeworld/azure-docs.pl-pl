---
title: Omówienie sieci szkieletowej usług i kontenery | Dokumentacja firmy Microsoft
description: Omówienie sieci szkieletowej usług i korzystanie z kontenerów do wdrażania aplikacji mikrousługi. Ten artykuł zawiera omówienie sposobu użycia kontenery oraz możliwości dostępne w sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/20/2017
ms.author: msfussell
ms.openlocfilehash: c70db92d6071a295dfc329768ab8a0fd561f8ce5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-and-containers"></a>Sieć szkieletowa usług i kontenerów
> [!NOTE]
> Wdrażanie kontenerów do klastra usługi sieć szkieletowa usług w systemie Windows 10 lub Docker CE nie jest obsługiwane. 
>   

> [!NOTE]
> Usługa Service Fabric w wersji 6.1 obejmuje obsługę wersji zapoznawczej systemu Windows Server w wersji 1709. Otwarta sieć i usługa DNS w usłudze Service Fabric nie działają w systemie Windows Server w wersji 1709. 
> 

## <a name="introduction"></a>Wprowadzenie
Sieć szkieletowa usług Azure to [orchestrator](service-fabric-cluster-resource-manager-introduction.md) usługi w klastrze maszyn z wielu lat użycia i optymalizację na ogromną skalę usług firmy Microsoft. Usługi mogą być opracowane na wiele sposobów korzystania z [usługi sieć szkieletowa modele programowania](service-fabric-choose-framework.md) wdrażanie [pliki wykonywalne gościa](service-fabric-guest-executables-introduction.md). Domyślnie usługi sieć szkieletowa wdraża i aktywuje tych usług jako procesów. Procesy Podaj najszybszym aktywacji i najwyższy gęstość użycia zasobów w klastrze. Sieć szkieletowa usług można także wdrożyć usługi kontenera obrazów. Ważne można mieszać usług w procesach i usług w kontenerach w tej samej aplikacji.   

## <a name="what-are-containers"></a>Co to są kontenerami?
Kontenery są hermetyzowany, indywidualnie do wdrożenia składników działających jako izolowanych wystąpień w tej samej jądra, aby móc korzystać z wirtualizacji, który zawiera system operacyjny. W związku z tym każdej aplikacji i bibliotek środowiska uruchomieniowego, zależności i system uruchomienia wewnątrz kontenera pełne, prywatny dostęp do widoku kontenera własnych izolowane konstrukcji systemu operacyjnego. Wraz z przenośnością poziomu izolacji zabezpieczeń i zasobów jest głównym korzyści korzystania z usługi Service Fabric uruchamianego w przeciwnym razie usług w procesach przy użyciu kontenerów.

Kontenery są technologii wirtualizacji, która Wirtualizuje system operacyjny z aplikacji. Kontenery dostarczenia niezmienne środowiska aplikacji do uruchamiania w różnym stopniu izolacji. Kontenery działają bezpośrednio jądra i izolowane widok systemu plików i innych zasobów. Kontenery w porównaniu do maszyn wirtualnych, ma następujące zalety:

* **Mała**: kontenery Użyj jednego miejsca i wersji warstwy i aktualizacje, aby zwiększyć wydajność.
* **Szybkie**: kontenery nie trzeba uruchomić całego systemu operacyjnego, więc mogą rozpocząć znacznie szybciej, zwykle w sekundach.
* **Przenośność**: obraz konteneryzowanych aplikacji można przenieść do uruchamiania w chmurze lokalnie w maszynach wirtualnych lub bezpośrednio na maszyny fizyczne.
* **Zarządzanie zasobów**: kontener może ograniczyć zasoby fizyczne, które może zużyć na jej hosta.

## <a name="container-types-and-supported-environments"></a>Typy kontenera i obsługiwane środowiska
Sieć szkieletowa usług obsługuje kontenery na systemie Linux i Windows i obsługuje również trybem izolacji funkcji Hyper-V na drugie. 

> [!NOTE]
> Wdrażanie kontenerów do klastra usługi sieć szkieletowa usług w systemie Windows 10 nie jest obecnie obsługiwany. 
> 

### <a name="docker-containers-on-linux"></a>Kontenery docker w systemie Linux
Docker zapewnia wysokiego poziomu interfejsy API umożliwiające tworzenie i zarządzanie nimi kontenerów na górze kontenery jądra systemu Linux. Centrum docker jest centralnym repozytorium do przechowywania i pobierania obrazów kontenera.
Samouczek, zobacz [wdrożenia kontenera Docker sieci szkieletowej usług](service-fabric-get-started-containers-linux.md).

### <a name="windows-server-containers"></a>Kontenery systemu Windows Server
Windows Server 2016 oferuje dwa różne typy kontenerów, które różnią się na poziomie izolacji podana. Kontenery systemu Windows Server i Docker kontenery są podobne, ponieważ zarówno ma przestrzeń nazw i plik izolacji systemu, ale udostępniać jądra hosta, na którym jest uruchomiona na. W systemie Linux izolacja tradycyjnie została dostarczona przez `cgroups` i `namespaces`, i kontenery systemu Windows Server zachowują się podobnie.

Kontenery systemu Windows z obsługą funkcji Hyper zapewniają więcej izolacji i zabezpieczeń, ponieważ każdego kontenera nie udostępnia jądra systemu operacyjnego z innych kontenery lub z hosta. Z wyższego poziomu izolacji zabezpieczeń funkcji Hyper-V włączono kontenery są przeznaczone do szkodliwy, wielodostępnym scenariusze.
Samouczek, zobacz [wdrożenia kontenera systemu Windows w sieci szkieletowej usług](service-fabric-get-started-containers.md).

Na poniższej ilustracji przedstawiono różne typy dostępnych w systemie operacyjnym poziomów wirtualizacji i izolacji.
![Platforma sieci szkieletowej usług][Image1]

## <a name="scenarios-for-using-containers"></a>Scenariusze korzystania z kontenerów
Poniżej przedstawiono typowe przykłady gdzie jest dobrym rozwiązaniem, kontener:

* **Usługi IIS Podnieś i przesunięcia**: Jeśli masz istniejące [ASP.NET MVC](https://www.asp.net/mvc) aplikacje, które mają w dalszym ciągu korzystać, umieść je w kontenerze zamiast migracji je do platformy ASP.NET Core. Te aplikacje ASP.NET MVC zależeć na Internet Information Services (IIS). Można spakować te aplikacje do kontenera obrazów z precreated obrazu usług IIS, a następnie wdrożyć je z sieci szkieletowej usług. Zobacz [kontener obrazów w systemie Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) informacji o kontenery systemu Windows.
* **Mieszać kontenery i sieci szkieletowej usług mikrousług**: Użyj istniejącego obrazu kontenera części aplikacji. Na przykład może użyć [kontener NGINX](https://hub.docker.com/_/nginx/) dla frontonu sieci web, aplikacji i usług stanowych do bardziej intensywnie obliczeń zaplecza.
* **Ograniczenia wpływu usług "zakłócenia sąsiadów"**: możliwości zarządzania zasób kontenerów służy do ograniczania zasobów używanych przez usługi na hoście. Jeśli usługi może korzystać z wielu zasobów i negatywnie wpłynąć na wydajność innych (na przykład operacji długotrwałych, typu kwerendy), należy rozważyć wprowadzenie tych usług do kontenerów, które mają ładu zasobów.

## <a name="service-fabric-support-for-containers"></a>Obsługa sieci szkieletowej usług dla kontenerów
Sieć szkieletowa usług obsługuje wdrażanie kontenerów Docker na kontenery systemu Linux i Windows Server w systemie Windows Server 2016, wraz z możliwością w trybie izolacji funkcji Hyper-V. 

W sieci szkieletowej usług [model aplikacji](service-fabric-application-model.md), kontener reprezentuje hosta aplikacji, w których wiele usługi repliki są umieszczane. Usługi sieć szkieletowa można uruchamiać żadnych kontenerów oraz scenariusz jest podobny do [scenariusza pliku wykonywalnego gościa](service-fabric-guest-executables-introduction.md), gdy pakiet istniejącej aplikacji wewnątrz kontenera. Ten scenariusz jest często zdarza używany dla kontenerów i przykłady, uruchomienie aplikacji napisane przy użyciu dowolnego języka lub struktury, ale nie za pomocą wbudowanych modele programowania sieci szkieletowej usług.

Ponadto można uruchomić [usługi sieć szkieletowa usług wewnątrz kontenerów](service-fabric-services-inside-containers.md) również. Obsługa uruchomione usługi sieć szkieletowa usług wewnątrz kontenerów jest aktualnie ograniczona i zostanie poprawiona w kolejnych wersjach.

Sieć szkieletowa usług ma kilka możliwości kontenera, które pomagają tworzyć aplikacje, które składają się z mikrousług, które są konteneryzowanych. Sieć szkieletowa usług oferuje następujące możliwości konteneryzowanych usług:

* Kontener obrazu wdrożenia i aktywacji.
* Ustawienie wartości zasobów domyślnie w klastrach Azure w tym ładu zasobów.
* Uwierzytelnianie repozytorium.
* Port kontenera do mapowania port hosta.
* Odnajdywanie kontenera do kontenera i komunikacji.
* Możliwość konfigurowania i ustaw zmienne środowiskowe.
* Zdolność ustawiania poświadczeń zabezpieczeń w kontenerze.
* Do wyboru różnych trybach sieci dla kontenerów.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono o kontenerów, sieć szkieletowa usług to kontener programu orchestrator, czy tej usługi Service Fabric zawiera funkcje, które obsługuje kontenery. Jako kolejny krok możemy będą przekazywane przykłady każdą z funkcji pokazanie sposobu ich używania.

[Tworzenie pierwszej aplikacji kontenera sieci szkieletowej usług w systemie Windows](service-fabric-get-started-containers.md)

[Tworzenie pierwszej aplikacji kontenera sieci szkieletowej usług w systemie Linux](service-fabric-get-started-containers-linux.md)

[Dowiedz się więcej o kontenery systemu Windows](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
