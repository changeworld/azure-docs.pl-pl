---
title: Terminologia dotycząca usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Poznaj typowe terminy dla usługi Azure Service Fabric siatki.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 2d2661593ba3d9be2755d81803c8e248a2f7d0e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810606"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologia usługi Service Fabric siatki

Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. Ten artykuł szczegółowo opisuje terminologię, używane przez usługi Azure Service Fabric siatki Zrozumienie pojęć używanych w dokumentacji.

## <a name="service-fabric"></a>Service Fabric

[Usługa Service Fabric](/azure/service-fabric/) to platforma systemów rozproszonych "open source", która ułatwia pakowanie, wdrażanie i zarządzanie nimi skalowalnych i niezawodnych mikrousług. Usługa Service Fabric jest koordynatora, na której działają usługi Service Fabric siatki. Usługa Service Fabric udostępnia opcje, jak tworzyć i uruchamiać aplikacje mikrousług. Tworzenie usług i wybierz miejsce uruchomić aplikację z wielu opcji środowiska, można użyć dowolnej platformy.

## <a name="application-and-service-concepts"></a>Aplikacja i pojęcia dotyczące usługi

**Aplikacja usługi Service Fabric siatki**: Aplikacje usługi Service Fabric siatki są opisane przez [Model zasobów](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (pliki zasobów YAML i JSON) i można wdrożyć w dowolnym środowisku, na którym działa Usługa Service Fabric.

**Aplikacja usługi Service Fabric natywnych**: Aplikacje natywne usługi Service Fabric są opisane przez [natywnego modelu aplikacji](/azure/service-fabric/service-fabric-application-model) (oparte na języku XML aplikacji i manifestów usługi).  Aplikacje natywne usługi Service Fabric nie może uruchomić usługi Service Fabric siatki.

**Aplikacja**: Aplikacja usługi Service Fabric siatki jest jednostką wdrożenia, przechowywanie wersji i cyklu życia aplikacji siatki. Cykl życia każdego wystąpienia aplikacji można zarządzać w sposób niezależny.  Aplikacje składają się z co najmniej jeden usługi pakiety kodu i ustawień. Aplikacja jest zdefiniowany, przy użyciu schematu modelu zasobów platformy Azure (RM).  Usługi są określane jako właściwości zasobu aplikacji w szablonie Menedżera zasobów.  Sieci i woluminów używanych przez aplikację są przywoływane przez aplikację.  Podczas tworzenia aplikacji, aplikacji, usług, sieci i woluminy są modelowane przy użyciu modelu zasobów usługi Service Fabric.

**Usługa**: Usługi w aplikacji reprezentuje mikrousługi i wykonuje pełne i autonomicznych funkcji. Każda usługa składa się z jednego lub więcej, pakiety kodu, które opisują wszystko, co jest potrzebne do uruchomienia obrazu kontenera, skojarzone z pakietem kodu.  Liczba replik usługi w aplikacji mogą być skalowane wewnątrz i na zewnątrz.

**Pakiet kodu**: Pakiety kodu opisują wszystko, co jest potrzebne do uruchomienia obrazu kontenera, skojarzone z pakietem kodu, w tym następujące:

* Nazwa kontenera, wersji i rejestru
* Zasoby Procesora i pamięci, które są wymagane dla każdego kontenera
* Punkty końcowe sieci
* Woluminy należy zainstalować w kontenerze, odwołuje się do zasobów oddzielnego woluminu.

## <a name="deployment-and-application-models"></a>Modele wdrażania i aplikacji 

Aby wdrożyć usługi, musisz opisują, jak powinno być ono uruchomione. Usługa Service Fabric obsługuje trzy różne modele wdrażania:

### <a name="resource-model"></a>Model zasobów
Zasoby sieci szkieletowej usługi są wszystkie elementy, które można wdrożyć osobno dla usługi Service Fabric; w tym aplikacjami, usługami, sieci i woluminy. Zasoby są definiowane przy użyciu pliku JSON, który można wdrożyć w punkcie końcowym klastra.  Dla usługi Service Fabric siatki schematu modelu zasobów platformy Azure jest używana. Schemat pliku YAML można również łatwiej tworzyć pliki definicji. Zasoby można wdrożyć wszędzie tam, gdzie działa Usługa Service Fabric. Model zasobu jest najprostszym sposobem opisują aplikacje usługi Service Fabric. Głównym celem jest na proste wdrażanie i zarządzanie usług konteneryzowanych. Aby dowiedzieć się więcej, przeczytaj [wprowadzenie do modelu zasobów usługi Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Natywny model
Model aplikacji natywnej udostępnia swoje aplikacje za pomocą pełnej niskiego poziomu dostępu do usługi Service Fabric. Aplikacje i usługi są definiowane jako zarejestrowanych typów w plikach manifestu XML.

Natywny model obsługuje struktury usług Reliable Services, która zapewnia dostęp do środowiska uruchomieniowego usługi Service Fabric interfejsów API i Zarządzanie klastrem interfejsów API w języku C# i Java. Natywny model obsługuje również dowolnego kontenerów i plików wykonywalnych.

Natywny model nie jest obsługiwana w środowisku usługi Service Fabric siatki.  Aby uzyskać więcej informacji, zobacz [omówienie modelu programowania](/azure/service-fabric/service-fabric-choose-framework).

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) jest częścią projektu platformy Docker. Usługa Service Fabric udostępnia ograniczoną obsługę wdrażania aplikacji przy użyciu narzędzia Docker Compose modelu.

## <a name="environments"></a>Środowiska

Usługa Service Fabric to technologia platformie typu open source, opartych na kilka różnych usług i produktów. Firma Microsoft udostępnia następujące opcje:

 - **Usługa Service Fabric siatki**: W pełni zarządzana Usługa do uruchamiania aplikacji usługi Service Fabric w systemie Microsoft Azure.
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

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).

Znajdź odpowiedzi na [często zadawane pytania](service-fabric-mesh-faq.md).
