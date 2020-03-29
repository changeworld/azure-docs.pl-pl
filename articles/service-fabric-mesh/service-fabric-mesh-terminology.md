---
title: Terminologia dla siatki sieci szkieletowej usług Azure
description: W tym artykule opisano terminologię używaną przez usługę Azure Service Fabric Mesh, aby lepiej zrozumieć terminy używane w dokumentacji.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 6df7a6b708bca81f1390ac879f24ea4c22c38bee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351976"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologia siatki sieci szkieletowej usług

Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. W tym artykule opisano terminologię używaną przez usługę Azure Service Fabric Mesh, aby lepiej zrozumieć terminy używane w dokumentacji.

## <a name="service-fabric"></a>Service Fabric

[Sieć szkieletowa usług](/azure/service-fabric/) to platforma systemów rozproszonych typu open source, która ułatwia pakowanie, wdrażanie i zarządzanie skalowalnymi i niezawodnymi mikrousługami. Sieć szkieletowa usług jest koordynatorem, który zasila siatkę sieci szkieletowej usług. Sieci szkieletowej usług zawiera opcje, jak można tworzyć i uruchamiać aplikacje mikrousług. Można użyć dowolnej struktury do zapisu usług i wybrać, gdzie uruchomić aplikację z wielu opcji środowiska.

## <a name="application-and-service-concepts"></a>Pojęcia dotyczące aplikacji i usług

**Aplikacja siatki sieci szkieletowej usług:** Aplikacje siatki sieci szkieletowej usług są opisane przez [model zasobów](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (pliki zasobów YAML i JSON) i mogą być wdrażane w dowolnym środowisku, w którym działa sieć szkieletowa usług.

**Natywna aplikacja sieci szkieletowej usług:** Aplikacje macierzyste sieci szkieletowej usług są opisane przez [natywny model aplikacji](/azure/service-fabric/service-fabric-application-model) (manifesty aplikacji i usługi oparte na języku XML).  Aplikacje natywne sieci szkieletowej usług nie można uruchomić w sieci szkieletowej usług.

**Aplikacja:** Aplikacja siatki sieci szkieletowej usług jest jednostką wdrażania, przechowywania wersji i okresu istnienia aplikacji mesh. Cykl życia każdego wystąpienia aplikacji można zarządzać niezależnie.  Aplikacje składają się z co najmniej jednego pakietów kodu usługi i ustawień. Aplikacja jest definiowana przy użyciu schematu modelu zasobów platformy Azure (RM).  Usługi są opisane jako właściwości zasobu aplikacji w szablonie RM.  Sieci i woluminy używane przez aplikację są odwoływane przez aplikację.  Podczas tworzenia aplikacji aplikacja, usługi, sieć i woluminy są modelowane przy użyciu modelu zasobów sieci szkieletowej usług.

**Usługa**: Usługa w aplikacji reprezentuje mikrousługi i wykonuje pełną i autonomiczną funkcję. Każda usługa składa się z jednego lub więcej pakietów kodu, które opisują wszystko, co potrzebne do uruchomienia obrazu kontenera skojarzonego z pakietem kodu.  Liczba replik usługi w aplikacji można skalować i wysuw.

**Pakiet kodu:** Pakiety kodu opisują wszystko, co jest potrzebne do uruchomienia obrazu kontenera skojarzonego z pakietem kodu, w tym następujące:

* Nazwa kontenera, wersja i rejestr
* Zasoby procesora i pamięci wymagane dla każdego kontenera
* Sieciowe punkty końcowe
* Woluminy do zainstalowania w kontenerze, odwołując się do oddzielnego zasobu woluminu.

## <a name="deployment-and-application-models"></a>Modele wdrażania i aplikacji 

Aby wdrożyć usługi, należy opisać, jak powinny działać. Usługa Service Fabric obsługuje trzy różne modele wdrażania:

### <a name="resource-model"></a>Model zasobów
Zasoby sieci szkieletowej usług są wszystko, co można wdrożyć indywidualnie do sieci szkieletowej usług; aplikacji, usług, sieci i woluminów. Zasoby są definiowane przy użyciu pliku JSON, który można wdrożyć w punkcie końcowym klastra.  W przypadku siatki sieci szkieletowej usług używany jest schemat modelu zasobów platformy Azure. Schemat pliku YAML może być również używany do łatwiejszego tworzenia plików definicji. Zasoby można wdrożyć w dowolnym miejscu uruchomień sieci szkieletowej usług. Model zasobów jest najprostszym sposobem opisania aplikacji sieci szkieletowej usług. Jego głównym celem jest proste wdrażanie i zarządzanie konteneryzowanymi usługami. Aby dowiedzieć się więcej, przeczytaj [artykuł Wprowadzenie do modelu zasobów sieci szkieletowej usług](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Model macierzysty
Natywny model aplikacji zapewnia aplikacjom pełny niski poziom dostępu do sieci szkieletowej usług. Aplikacje i usługi są definiowane jako zarejestrowane typy w plikach manifestów XML.

Model macierzysty obsługuje platformę niezawodne usługi, która zapewnia dostęp do interfejsów API środowiska uruchomieniowego sieci szkieletowej usług i interfejsów API zarządzania klastrami w językach C# i Java. Model macierzysty obsługuje również dowolne kontenery i pliki wykonywalne.

Model macierzysty nie jest obsługiwany w środowisku siatki sieci szkieletowej usług.  Aby uzyskać więcej informacji, zobacz [omówienie modelu programowania](/azure/service-fabric/service-fabric-choose-framework).

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) jest częścią projektu platformy Docker. Sieci szkieletowej usług zapewnia ograniczoną obsługę wdrażania aplikacji przy użyciu modelu docker compose.

## <a name="environments"></a>Środowiska

Sieć szkieletowa usług to technologia platformy typu open source, na podstawie których opiera się kilka różnych usług i produktów. Firma Microsoft udostępnia następujące opcje:

 - **Mesh sieci szkieletowej**usług: w pełni zarządzana usługa do uruchamiania aplikacji sieci szkieletowej usług na platformie Microsoft Azure.
 - **Usługa Azure Service Fabric:** oferta klastra hostowana przez usługę Azure. Zapewnia integrację między siecią szkieletową usług a infrastrukturą platformy Azure, a także zarządzanie uaktualnianiem i konfiguracją klastrów sieci szkieletowej usług.
 - **Autonomiczna sieci szkieletowej usług:** Zestaw narzędzi instalacyjnych i konfiguracyjnych do [wdrażania klastrów sieci szkieletowej usług w dowolnym miejscu](/azure/service-fabric/service-fabric-deploy-anywhere) (lokalnie lub u dowolnego dostawcy chmury). Nie jest zarządzany przez platformę Azure.
 - **Klaster programistyczny sieci szkieletowej usług:** zapewnia lokalne środowisko programistyczne w systemach Windows, Linux lub Mac do tworzenia aplikacji sieci szkieletowej usług.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Macierz obsługi środowiska, struktury i modelu wdrażania
Różne środowiska mają różny poziom obsługi struktur i modeli wdrażania. W poniższej tabeli opisano obsługiwane kombinacje modeli struktury i wdrażania.

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

Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).

Znajdź odpowiedzi na [często zadawane pytania](service-fabric-mesh-faq.md).
