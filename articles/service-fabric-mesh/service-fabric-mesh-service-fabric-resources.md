---
title: Wprowadzenie do modelu zasobów sieci szkieletowej usług Azure
description: Dowiedz się więcej o modelu zasobów sieci szkieletowej usług, uproszczone podejście do definiowania aplikacji sieci szkieletowej sieci szkieletowej usług.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 0ae2ed163560aee4c0c3525ab31910e37afaa5b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259138"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Wprowadzenie do modelu zasobów usługi Service Fabric

Model zasobów sieci szkieletowej usług opisuje proste podejście do definiowania zasobów, które składają się na aplikację siatki sieci szkieletowej usług. Poszczególne zasoby można wdrożyć w dowolnym środowisku sieci szkieletowej usług.  Model zasobów sieci szkieletowej usług jest również zgodny z modelem usługi Azure Resource Manager. Następujące typy zasobów są obecnie obsługiwane w tym modelu:

- Aplikacje i usługi
- Sieci
- Bramy
- Wpisy tajne/wartości
- Woluminy

Każdy zasób jest opisany deklaratywnie w pliku zasobów, który jest prostym dokumentem YAML lub JSON, który opisuje aplikację siatki i jest aprowizowana przez platformę sieci szkieletowej usług.

## <a name="applications-and-services"></a>Aplikacje i usługi

Zasób aplikacji jest jednostką wdrożenia, przechowywania wersji i okresu istnienia aplikacji Mesh. Składa się z jednego lub więcej zasobów usługi, które reprezentują mikrousługi. Każdy zasób usługi z kolei składa się z jednego lub więcej pakietów kodu, które opisują wszystko, co potrzebne do uruchomienia obrazu kontenera skojarzonego z pakietem kodu.

![Aplikacje i usługi][Image1]

Zasób usługi deklaruje, co następuje:

- Nazwa kontenera, wersja i rejestr
- Zasoby procesora i pamięci wymagane dla każdego kontenera
- Sieciowe punkty końcowe
- Odwołania do innych zasobów, takich jak sieci, woluminy i wpisy tajne 

Wszystkie pakiety kodu zdefiniowane jako część zasobu usługi są wdrażane i aktywowane razem jako grupa. Zasób usługi opisuje również, ile wystąpień usługi do uruchomienia, a także odwołuje się do innych zasobów (na przykład zasobów sieciowych), od których zależy.

Jeśli aplikacja siatki składa się z więcej niż jednej usługi, nie są one gwarantowane do uruchamiania razem w tym samym węźle. Ponadto podczas uaktualniania Aplikacji niepowodzenie uaktualnienia pojedynczej Usługi spowoduje przywrócenie wszystkich Usług do poprzedniej wersji.

Jak nawiązywał wcześniej, cykl życia każdego wystąpienia aplikacji można zarządzać niezależnie. Na przykład jedno wystąpienie aplikacji można uaktualnić niezależnie od innych wystąpień aplikacji. Zazwyczaj liczba usług w aplikacji jest dość mała, ponieważ im więcej usług zostanie wprowadzonych do aplikacji, tym trudniej będzie zarządzać każdą usługą niezależnie.

## <a name="networks"></a>Sieci

Zasób sieciowy jest indywidualnie wdrażany zasób, niezależnie od aplikacji lub usługi zasobu, który może odnosić się do niego jako ich zależności. Służy do tworzenia sieci dla aplikacji. Wiele usług z różnych aplikacji może być częścią tej samej sieci.  Aby uzyskać więcej informacji, przeczytaj artykuł o [sieci w aplikacjach sieci szkieletowej usług](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> Bieżąca wersja zapoznawcza obsługuje tylko mapowanie jeden do jednego między aplikacjami i sieciami

![Sieć i brama][Image2]

## <a name="gateways"></a>Bramy
Zasób bramy łączy dwie sieci i kieruje ruch.  Brama umożliwia usługom komunikowanie się z klientami zewnętrznymi i zapewnia przychylić się do usług.  Brama może być również używana do łączenia aplikacji Mesh z własną istniejącą siecią wirtualną. Aby uzyskać więcej informacji, przeczytaj artykuł o [sieci w aplikacjach sieci szkieletowej usług](service-fabric-mesh-networks-and-gateways.md).

![Sieć i brama][Image2]

## <a name="secrets"></a>Wpisy tajne

Zasoby wpisowe można wdrożyć niezależnie od aplikacji lub zasobu usługi, które mogą odnosić się do niego jako ich zależności. Służy do bezpiecznego dostarczania wpisów tajnych do aplikacji. Wiele usług z różnych aplikacji można odwoływać się do wartości tego samego klucza tajnego.

## <a name="volumes"></a>Woluminy

Kontenery często udostępniają dyski tymczasowe. Dyski tymczasowe są jednak efemeryczne, więc otrzymasz nowy dysk tymczasowy i utracisz informacje, gdy kontener ulegnie awarii. Jest również trudne do udostępniania informacji na dyskach tymczasowych z innymi kontenerami. Woluminy są katalogi, które są zainstalowane wewnątrz wystąpień kontenera, które można użyć do utrwalenia stanu. Woluminy zapewniają magazyn plików ogólnego przeznaczenia i umożliwiają odczytywanie/zapisywanie plików przy użyciu zwykłych interfejsów API plików we/wy dysku. Zasób woluminu jest deklaratywny sposób, aby opisać, jak katalog jest zainstalowany i magazynu zapasowego dla niego (albo Wolumin azure files lub sieci szkieletowej usługi niezawodny wolumin).  Aby uzyskać więcej informacji, przeczytaj [stan przechowywania](service-fabric-mesh-storing-state.md#volumes).

![Woluminy][Image3]

## <a name="programming-models"></a>Modele programowania
Zasób usługi wymaga tylko uruchomienia obrazu kontenera, do którego odwołuje się pakiet kodu skojarzony z zasobem. Można uruchomić dowolny kod, napisany w dowolnym języku, przy użyciu dowolnej struktury wewnątrz kontenera bez konieczności wiedzieć lub używać, Service Fabric Mesh określonych interfejsów API. 

Kod aplikacji pozostaje przenośny nawet poza siecią szkieletową usługi Mesh, a wdrożenia aplikacji pozostają spójne niezależnie od języka lub struktury używanej do implementacji usług. Niezależnie od tego, czy aplikacja jest ASP.NET Core, Go, czy tylko zestaw procesów i skryptów, model wdrażania zasobów sieci szkieletowej sieci szkieletowej usługi pozostaje taki sam. 

## <a name="packaging-and-deployment"></a>Pakowanie i wdrażanie

Aplikacje sieci szkieletowej usługi oparte na modelu zasobów są pakowane jako kontenery platformy Docker.  Usługa Mesh sieci szkieletowej jest środowiskiem współużytkowane, wielodostępne i kontenery zapewniają wysoki poziom izolacji.  Aplikacje te są opisane przy użyciu formatu JSON lub formatu YAML (który jest następnie konwertowany na JSON). Podczas wdrażania aplikacji mesh w usłudze Azure Service Fabric Mesh, JSON używane do opisywania aplikacji jest szablonem Usługi Azure Resource Manager. Zasoby są mapowane na zasoby platformy Azure.  Podczas wdrażania aplikacji mesh do klastra sieci szkieletowej usług (autonomiczne lub hostowane na platformie Azure), JSON używane do opisywania aplikacji jest format podobny do szablonu Usługi Azure Resource Manager.  Po wdrożeniu aplikacjami mesh można zarządzać za pomocą interfejsów HTTP lub interfejsu wiersza polecenia platformy Azure. 


## <a name="next-steps"></a>Następne kroki 
Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, zapoznaj się z omówieniem:
- [Omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
