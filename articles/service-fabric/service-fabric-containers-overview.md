---
title: Omówienie usługi Service Fabric i kontenery | Dokumentacja firmy Microsoft
description: Omówienie usługi Service Fabric i sposób korzystania z kontenerów, aby wdrożyć aplikacje mikrousług. Ten artykuł zawiera omówienie sposobu użycia kontenerów i możliwości dostępne w usłudze Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/8/2018
ms.author: aljo
ms.openlocfilehash: 5a45f14e5ac1da5152f320bd92b1ebb42be1d214
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60881426"
---
# <a name="service-fabric-and-containers"></a>Usługa Service Fabric i kontenery

## <a name="introduction"></a>Wprowadzenie

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług i kontenerów oraz zarządzanie nimi.

Usługa Service Fabric jest firmy Microsoft [orkiestrator kontenerów](service-fabric-cluster-resource-manager-introduction.md) do wdrażania mikrousług w klastrze maszyn. Usługa Service Fabric korzyści ze zdobytego podczas jego lat z usługami w firmie Microsoft na dużą skalę.

Mikrousługi można tworzyć na wiele sposobów, na przykład korzystając z [modeli programowania usługi Service Fabric](service-fabric-choose-framework.md) czy struktury [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) lub wdrażając [dowolnie wybrany kod](service-fabric-guest-executables-introduction.md). Lub, jeśli chcesz, aby [wdrażanie kontenerów i zarządzanie nimi](service-fabric-containers-overview.md), Usługa Service Fabric jest także doskonałym wyborem.

Domyślnie Usługa Service Fabric wdraża i aktywuje tych usług, ponieważ procesy. Procesy zapewnia najszybszy aktywacji i największym wykorzystaniu gęstość zasobów w klastrze. Usługa Service Fabric można także wdrożyć usługi w obrazów kontenerów. Można także łączyć usługi w procesach i usługi w kontenerach w tej samej aplikacji.

Aby przejść bezpośrednio i wypróbować kontenerów w usłudze Service Fabric, wypróbuj Szybki Start, samouczek lub próbki:  

[Szybki start: Wdrażanie aplikacji kontenera systemu Linux w usłudze Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Szybki start: Wdrażanie aplikacji kontenera Windows w usłudze Service Fabric](service-fabric-quickstart-containers.md)  
[Konteneryzowanie istniejącej aplikacji .NET](service-fabric-host-app-in-a-container.md)  
[Przykłady kontenera usługi Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Co to są kontenery

Kontenery problemu niezawodne uruchamiania aplikacji w różnych środowiskach obliczeniowych, zapewniając niezmienne środowisko do uruchamiania aplikacji. Kontenery opakować aplikację i wszystkie jego zależności, takich jak biblioteki i pliki konfiguracji do własnego izolowanego "pola" zawierający wszystkie elementy potrzebne do uruchamiania oprogramowania w kontenerze. Wszędzie tam, gdzie jest uruchomiony kontener, aplikacja wewnątrz zawsze ma wszystko, czego potrzebuje do uruchomienia, takich jak prawo wersje bibliotek zależnych, wszystkie pliki konfiguracji i wszystko inne, co potrzebne do uruchomienia.

Kontenery Uruchom bezpośrednio na jądro i izolowany widok systemu plików i innych zasobów. Aplikacja w kontenerze nie ma informacji o inne aplikacje lub procesy, poza jej kontenerem. Każdej aplikacji i jego bibliotek środowiska uruchomieniowego, zależności i system uruchamiane wewnątrz kontenera pełnej prywatne dostęp z Widok kontenera własnych izolowany system operacyjny. Oprócz dzięki czemu można łatwo zawierać wszystkie zależności aplikacji, musi działać w różnych środowiskach obliczeniowych, zabezpieczenia i izolację zasobów są istotne zalety stosowania kontenerów za pomocą usługi Service Fabric — które w przeciwnym razie uruchomienia usługi programu proces.

W porównaniu do maszyn wirtualnych, kontenerach ma następujące zalety:

* **Małe**: Kontenery korzystanie z jednego miejsca i warstwy wersje i aktualizacje, do zwiększania wydajności.
* **Szybkie**: Kontenery nie trzeba uruchomić cały system operacyjny, aby mogli rozpocząć znacznie szybciej — zwykle w ciągu kilku sekund.
* **Przenośność**: Obraz konteneryzowanej aplikacji mogą być przenoszone do uruchamiania w chmurze, lokalnie, wewnątrz maszyn wirtualnych lub bezpośrednio na maszynach fizycznych.
* **Nadzór nad zasobami**: Kontener można ograniczyć zasoby fizyczne, które mogą wykorzystywać sieciową hosta.

### <a name="container-types-and-supported-environments"></a>Typy kontenera i obsługiwanych środowisk

Usługa Service Fabric obsługuje kontenery na systemie Linux i Windows oraz w trybie izolacji funkcji Hyper-V na Windows.

#### <a name="docker-containers-on-linux"></a>Kontenery platformy docker w systemie Linux

Środowisko docker zawiera interfejsy API do tworzenia kontenerów i zarządzanie nimi na podstawie kontenerów jądra systemu Linux. Usługi docker Hub stanowi centralne repozytorium do przechowywania i pobierania obrazów kontenerów.
Samouczek opartych na systemie Linux, zobacz [tworzenie pierwszej aplikacji kontenera usługi Service Fabric w systemie Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Kontenery systemu Windows Server

System Windows Server 2016 zapewnia dwa różne typy kontenerów, które różnią się od poziomu izolacji. Kontenery systemu Windows Server i kontenerów platformy Docker są podobne, ponieważ zachowują się przestrzeń nazw i plik izolacji systemu podczas udostępniania jądro z hostem, do których są uruchomione. W systemie Linux ta izolacja tradycyjnie została przekazana przez cgroup i przestrzenie nazw i kontenery systemu Windows Server działają w podobny sposób.

Kontenery Windows z obsługą funkcji Hyper-V zapewniają więcej izolacji i zabezpieczeń, ponieważ kontener nie udostępnia jądra systemu operacyjnego przy użyciu innego kontenera lub z hostem. Z tego wyższego poziomu izolacji zabezpieczeń kontenery funkcji Hyper-V, włączone są przeznaczone wielodostępnych, potencjalnie szkodliwymi scenariuszy.
Samouczek z systemem Windows można znaleźć [utworzyć swoją pierwszą aplikację kontenera usługi Service Fabric na Windows](service-fabric-get-started-containers.md).

Na poniższej ilustracji przedstawiono różne rodzaje dostępnych poziomów wirtualizacji i izolacji.
![Platformy usługi Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Scenariusze dotyczące korzystania z kontenerów

Poniżej przedstawiono typowe przykłady gdy kontener jest dobrym wyborem:

* **Usługi IIS lift- and -shift**: Możesz umieścić istniejące [platformy ASP.NET MVC](https://www.asp.net/mvc) aplikacji w kontenerze zamiast migrowania go do platformy ASP.NET Core. Te aplikacje platformy ASP.NET MVC zależeć na Internet Information Services (IIS). Można spakować te aplikacje w kontenerze obrazów z precreated obrazu IIS, a następnie wdrażać je z usługą Service Fabric. Zobacz [obrazów kontenerów w systemie Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) uzyskać informacji na temat kontenerów Windows.

* **Mieszanie kontenerów oraz mikrousług usługi Service Fabric**: Użyj istniejącego obrazu kontenera dla części aplikacji. Na przykład, można na przykład [kontener NGINX](https://hub.docker.com/_/nginx/) dla frontonu sieci web, aplikacji i usług stanowych bardziej intensywnie korzystających z obliczeń zaplecza.

* **Ograniczenia wpływu usługi "hałaśliwym sąsiadów"** : Możliwości zarządzania zasób kontenerów umożliwia ograniczanie zasobów używanych przez usługi na hoście. Jeśli usługi mogą używać wielu zasobów i wpłynąć na wydajność innych osób (na przykład operacji długotrwałych, podobne do zapytania), należy rozważyć umieszczenie tych usług do kontenerów, które mają nadzór nad zasobami.

## <a name="service-fabric-support-for-containers"></a>Obsługa usługi Service Fabric dla kontenerów

Usługa Service Fabric obsługuje wdrażanie kontenerów Docker w systemie Linux i kontenerów systemu Windows Server w systemie Windows Server 2016, wraz z pomocy technicznej w trybie izolacji funkcji Hyper-V. 

Usługa Service Fabric udostępnia [model aplikacji](service-fabric-application-model.md) w kontenerze reprezentuje aplikację hosta w wielu usług, w której są umieszczane replik. Usługa Service Fabric obsługuje także [scenariusz pliku wykonywalnego gościa](service-fabric-guest-executables-introduction.md) w którym możesz nie należy używać wbudowanej usługi Service Fabric modeli programowania, ale zamiast tego pakietu istniejącej aplikacji, napisane przy użyciu dowolnego języka lub platformy, wewnątrz kontener. Ten scenariusz jest typowy przypadek użycia dla kontenerów.

Można również uruchomić [usługi Service Fabric w kontenerze](service-fabric-services-inside-containers.md). Obsługa usługi Service Fabric działającej wewnątrz kontenerów jest obecnie ograniczona.

Usługa Service Fabric udostępnia kilka możliwości kontenerów, które pomagają tworzyć aplikacje, które składają się z konteneryzowanych mikrousług, takich jak:

* Wdrażanie obrazu kontenera i aktywacji.
* Nadzór nad zasobami wraz z ustawieniem wartości zasobu domyślnie w klastrach platformy Azure.
* Uwierzytelnianie repozytorium.
* Kontenera typu port do hosta, mapowanie portów.
* Odnajdywanie na kontenerami i komunikacji.
* Możliwość Konfigurowanie i Ustawianie zmiennych środowiskowych.
* Możliwość ustawienia poświadczeń zabezpieczeń w kontenerze.
* Wybór różne tryby sieci dla kontenerów.

Kompletny przegląd kontenera działu pomocy technicznej na platformie Azure, takie jak sposób tworzenia klastra Kubernetes za pomocą usługi Azure Kubernetes Service, aby utworzyć prywatnego rejestru platformy Docker w usłudze Azure Container Registry i więcej, zobacz [Azure for Containers](https://docs.microsoft.com/azure/containers/).

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono informacje pomocy technicznej, których Usługa Service Fabric udostępnia uruchamiania kontenerów. Następnie przejdziemy przez przykłady każda z tych funkcji, aby pokazać, jak z nich korzystać.

[Tworzenie pierwszej aplikacji kontenera usługi Service Fabric w systemie Linux](service-fabric-get-started-containers-linux.md)  
[Tworzenie pierwszej aplikacji kontenera usługi Service Fabric na Windows](service-fabric-get-started-containers.md)  
[Dowiedz się więcej na temat kontenerów Windows](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png