---
title: Wprowadzenie do usługi Azure Service Fabric zasobów modelu | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o modelu zasobów sieci szkieletowej usługi, uproszczone podejście do definiowania aplikacji usługi Service Fabric siatki.
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 3cee0ada75c4ea265c7e9c598408eb6b01477d6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810731"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Wprowadzenie do usługi Service Fabric zasobów modelu

Model zasobów usługi Service Fabric opisuje proste podejście do definiowania zasoby wchodzące w skład aplikacji usługi Service Fabric siatki. Poszczególne zasoby można wdrażać w dowolnym środowisku usługi Service Fabric.  Model zasobów usługi Service Fabric jest również zgodna z modelu usługi Azure Resource Manager. Następujące typy zasobów są obecnie obsługiwane w tym modelu:

- Aplikacje i usługi
- Sieci
- Bramy
- Klucze tajne i klucze tajne/wartości
- Woluminy

Każdy zasób jest opisana w deklaratywnie w pliku zasobu, który jest proste YAML lub dokument JSON, który opisuje siatki aplikację i jest przygotowany przez platformy usługi Service Fabric.

## <a name="applications-and-services"></a>Aplikacje i usługi

Zasób aplikacji jest jednostką wdrożenia, przechowywanie wersji i cyklu życia aplikacji siatki. Składa się z jednego lub więcej, zasoby usługi, które reprezentują mikrousługi. Każdy zasób usługi z kolei składa się z co najmniej jedną pakiety kodu, które opisują wszystko, co jest potrzebne do uruchomienia obrazu kontenera, skojarzone z pakietem kodu.

![Aplikacje i usługi][Image1]

Zasób usługi deklaruje następujące czynności:

- Nazwa kontenera, wersji i rejestru
- Zasoby Procesora i pamięci, które są wymagane dla każdego kontenera
- Punkty końcowe sieci
- Odwołania do innych zasobów, takich jak sieci, woluminów i wpisów tajnych 

Wszystkie pakiety kodu określonych w ramach zasobu usługi są wdrożone i aktywowane jako grupa. Zasób usługi opisano także ile wystąpień usługi, aby uruchomić, a także odwołuje się do innych zasobów (na przykład zasobu sieciowego) wybór rozwiązania zależy od.

Jeśli aplikacja z siatką składa się z więcej niż jedna usługa, mają nie gwarancję, że można uruchomić na tym samym węźle. Ponadto podczas uaktualniania aplikacji niepowodzenia uaktualniania jednej usługi spowoduje wszystkie usługi i przywrócenie ich poprzedniej wersji.

Jak wcześniej alluded, cyklu życia każdego wystąpienia aplikacji mogą być zarządzane niezależnie. Na przykład jedno wystąpienie aplikacji można uaktualnić niezależnie od innych wystąpień aplikacji. Zazwyczaj zachowanie liczba usług w aplikacji dość małe, jak więcej usług, które można umieścić w aplikacji, tym trudniej staje się do zarządzania w każdej usługi niezależne.

## <a name="networks"></a>Sieci

Zasób sieciowy jest indywidualnie do wdrożenia zasobów, niezależnie od zasób aplikacji lub usługi, który może odwoływać się do niego jako ich zależności. Służy do tworzenia sieci dla aplikacji. Wiele usług w różnych aplikacjach mogą być częścią tej samej sieci.  Aby uzyskać więcej informacji, przeczytaj o [sieć w aplikacjach usługi Service Fabric siatki](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> Bieżąca wersja zapoznawcza obsługuje tylko mapowanie jeden-do-jednego między aplikacjami i sieci

![Sieci i bramy][Image2]

## <a name="gateways"></a>Bramy
Zasób brama łączy dwie sieci i kieruje ruchem.  Brama umożliwia usługi do komunikacji z klientami zewnętrznymi i oferuje ruchu przychodzącego do Twojej usługi.  Bramy można również połączyć aplikację siatki z własnymi, istniejącej sieci wirtualnej. Aby uzyskać więcej informacji, przeczytaj o [sieć w aplikacjach usługi Service Fabric siatki](service-fabric-mesh-networks-and-gateways.md).

![Sieci i bramy][Image2]

## <a name="secrets"></a>Wpisy tajne

Wpisy tajne zasoby są niezależne możliwych do wdrożenia aplikacji lub zasobów usługi, która może odwoływać się do niego jako ich zależności. Służy do bezpiecznego dostarczania kluczy tajnych aplikacji. Wiele usług z różnymi aplikacjami można odwoływać się do wartości tego samego klucza tajnego.

## <a name="volumes"></a>Woluminy

Kontenery często udostępnić dyski tymczasowe. Dyski tymczasowe są jednak tymczasowych, więc uzyskiwanie nowego dysku tymczasowego i utracić informacji w przypadku awarii kontenera. Jest również był trudny do udostępniania informacji na temat dyski tymczasowe z innych kontenerów. Woluminy są katalogów, która ma zostać zainstalowany wewnątrz wystąpienia kontenera, które umożliwia utrwalanie stanu. Woluminy pozwalają magazynu plików ogólnego przeznaczenia i pozwala na odczyt/zapis plików za pomocą dysku normalnych operacji We/Wy plikowych interfejsów API. Zasób woluminu jest deklaratywne opisują, jak katalog jest zainstalowany i magazynu zapasowego dla niego (woluminu plików platformy Azure lub usługi Service Fabric Reliable woluminu).  Aby uzyskać więcej informacji, przeczytaj [przechowywania stanu](service-fabric-mesh-storing-state.md#volumes).

![Woluminy][Image3]

## <a name="programming-models"></a>Modele programowania
Zasób usługi wymaga tylko obraz kontenera do uruchamiania, który odwołuje się do pakietów kodu skojarzone z zasobem. Możesz uruchomić dowolny kod napisane w dowolnym języku, za pomocą dowolnej platformy, wewnątrz kontenera bez konieczności, aby dowiedzieć się lub Użyj określonych interfejsów API usługi Service Fabric siatki. 

Kod aplikacji pozostaje przenośnych nawet poza usługi Service Fabric siatki i wdrożeń aplikacji pozostają spójne bez względu na język lub framework używany do implementowania usługi. Aplikacja platformy ASP.NET Core, z rzeczywistym użyciem lub po prostu zestaw procesy i skrypty, czy w modelu wdrażania usługi Service Fabric siatki zasobów pozostaje taki sam. 

## <a name="packaging-and-deployment"></a>Pakowanie i wdrażanie

Aplikacje usługi Service Fabric siatki, w zależności od modelu zasobu, są dostarczane jako kontenery platformy Docker.  Usługi Service Fabric siatki to środowisko wielodostępne, współdzielonych i kontenery zapewniają wysoki poziom izolacji.  Aplikacje te są opisane za pomocą formatu JSON lub w formacie YAML, (która jest następnie konwertowana na format JSON). W przypadku wdrażania aplikacji siatki, do usługi Azure Service Fabric siatki, za pomocą pliku JSON do opisu aplikacji jest szablonu usługi Azure Resource Manager. Zasoby są mapowane do zasobów platformy Azure.  W przypadku wdrażania aplikacji siatki w klastrze usługi Service Fabric (autonomiczny lub hostowanej na platformie Azure), za pomocą pliku JSON do opisu aplikacji jest format podobny do szablonu usługi Azure Resource Manager.  Po wdrożeniu aplikacji siatki mogą być zarządzane za pośrednictwem protokołu HTTP interfejsy lub wiersza polecenia platformy Azure. 


## <a name="next-steps"></a>Kolejne kroki 
Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, zapoznaj się z omówieniem:
- [Omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
