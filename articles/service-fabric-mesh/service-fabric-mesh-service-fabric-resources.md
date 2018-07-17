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
ms.date: 07/12/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 6a96995a46cd2ad0a1afe4f4afe49d62409f2b68
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076237"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Wprowadzenie do usługi Service Fabric zasobów modelu

Model zasobów sieci szkieletowej usług opisuje proste podejście do definiowania zasoby wchodzące w skład aplikacji usługi Service Fabric siatki. Następujące typy zasobów są obecnie obsługiwane w tym modelu:

- Aplikacje
- Usługi
- Woluminy
- Sieci

Każdy zasób jest opisana w deklaratywnie w pliku zasobu, który jest proste YAML lub dokument JSON, który opisuje siatki aplikację i jest przygotowany przez platformy usługi Service Fabric.

## <a name="resource-overview"></a>Przegląd zasobów

### <a name="applications-and-services"></a>Aplikacje i usługi

Zasób aplikacji jest jednostką wdrożenia, przechowywanie wersji i cyklu życia aplikacji siatki. Składa się z jednego lub więcej zasobów usługi, które reprezentują mikrousługi. Każdy zasób usługi z kolei składa się z co najmniej jedną pakiety kodu, które opisują wszystko, co jest potrzebne do uruchomienia obrazu kontenera, skojarzone z pakietem kodu, w tym następujące:

- Nazwa kontenera, wersji i rejestru
- Zasoby Procesora i pamięci, które są wymagane dla każdego kontenera
- Punkty końcowe sieci
- Woluminy należy zainstalować w kontenerze, odwołuje się do zasobów oddzielnego woluminu.

Wszystkie pakiety kodu określonych w ramach zasobu usługi są wdrożone i aktywowane jako grupa. Zasób usługi opisano także ile wystąpień usługi, aby uruchomić, a także odwołuje się do innych zasobów (na przykład zasobu sieciowego) wybór rozwiązania zależy od.

Jeśli aplikacja z siatką składa się z więcej niż jedna usługa, mają nie gwarancję, że można uruchomić na tym samym węźle. Ponadto podczas uaktualniania aplikacji niepowodzenia uaktualniania jednej usługi spowoduje wszystkie usługi i przywrócenie ich poprzedniej wersji.



```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

Jak wcześniej alluded, cyklu życia każdego wystąpienia aplikacji mogą być zarządzane niezależnie. Na przykład jedno wystąpienie aplikacji można uaktualnić niezależnie od innych wystąpień aplikacji. Zazwyczaj zachowanie liczba usług w aplikacji dość małe, jak więcej usług, które można umieścić w aplikacji, tym trudniej staje się do zarządzania w każdej usługi niezależne.

### <a name="networks"></a>Sieci

Zasób sieciowy jest indywidualnie do wdrożenia zasobów, niezależnie od zasób aplikacji lub usługi, który może odwoływać się do niego jako ich zależności. Służy do tworzenia sieci prywatnej dla aplikacji. Wiele usług w różnych aplikacjach mogą być częścią tej samej sieci.

> [!NOTE]
> Bieżąca wersja zapoznawcza obsługuje tylko mapowanie jeden-do-jednego między aplikacjami i sieci

### <a name="volumes"></a>Woluminy

Woluminy są katalogów, która ma zostać zainstalowany wewnątrz wystąpienia kontenera, które umożliwia utrwalanie stanu. Zasób woluminu jest deklaratywne opisują, jak katalog jest zainstalowany i magazynu zapasowego dla niego.

## <a name="programming-models"></a>Modele programowania
Zasób usługi wymaga tylko obraz kontenera do uruchamiania, który odwołuje się do pakietów kodu skojarzone z zasobem. Możesz uruchomić dowolny kod napisane w dowolnym języku, za pomocą dowolnej platformy, wewnątrz kontenera bez konieczności, aby dowiedzieć się lub Użyj określonych interfejsów API usługi Service Fabric siatki. 

Kod aplikacji pozostaje przenośnych nawet poza usługi Service Fabric siatki i wdrożeń aplikacji pozostają spójne bez względu na język lub framework używany do implementowania usługi. Aplikacja platformy ASP.NET Core, z rzeczywistym użyciem lub po prostu zestaw procesy i skrypty, czy w modelu wdrażania usługi Service Fabric siatki zasobów pozostaje taki sam. 

## <a name="deployment"></a>Wdrożenie

Podczas wdrażania usługi Service Fabric siatki, zasoby są wdrażane jako szablony usługi Azure Resource Manager na platformie Azure za pośrednictwem protokołu HTTP lub wiersza polecenia platformy Azure. 


## <a name="next-steps"></a>Kolejne kroki 
Aby dowiedzieć się więcej na temat usługi Service Fabric siatki, zapoznaj się z omówieniem:
- [Omówienie usługi Service Fabric siatki](service-fabric-mesh-overview.md)
