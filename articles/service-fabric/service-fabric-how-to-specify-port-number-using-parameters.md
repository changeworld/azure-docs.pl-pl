---
title: Jak określić numer portu usługi Azure Service Fabric przy użyciu parametrów | Dokumentacja firmy Microsoft
description: Dowiesz się, jak określić port dla aplikacji w usłudze Service Fabric za pomocą parametrów
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: d69e02126564388bf045693b9960e6e574307641
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720250"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Jak określić numer portu usługi Service Fabric przy użyciu parametrów

W tym artykule pokazano, jak określić numer portu usługi przy użyciu parametrów w usłudze Service Fabric przy użyciu programu Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procedura do określania numeru portu usługi przy użyciu parametrów

W tym przykładzie należy ustawić numer portu dla platformy asp.net core interfejsu API sieci web za pomocą parametru.

1. Otwórz program Visual Studio i Utwórz nową aplikację usługi Service Fabric.
1. Wybierz szablon o bezstanowa ASP.NET Core.
1. Wybierz interfejs API sieci Web.
1. Otwórz plik ServiceManifest.xml.
1. Zanotuj nazwę punktu końcowego określonego dla Twojej usługi. Wartość domyślna to `ServiceEndpoint`.
1. Otwórz plik ApplicationManifest.xml
1. W `ServiceManifestImport` elementu Dodawanie nowej `RessourceOverrides` elementu z odwołaniem do endpoint w pliku ServiceManifest.xml.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. W `Endpoint` elementu, możesz teraz zastąpić za pomocą parametru atrybutu. W tym przykładzie należy określić `Port` i ustaw go na nazwę parametru za pomocą nawiasami kwadratowymi — na przykład `[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Następnie w pliku ApplicationManifest.xml możesz określić parametr w `Parameters` — element

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. I zdefiniuj `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Otwórz ApplicationParameters folder i `Cloud.xml` pliku
1. Aby określić inny port do użycia podczas publikowania w klastrze zdalnym, należy dodać parametr numer portu do tego pliku.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Podczas publikowania aplikacji w programie Visual Studio przy użyciu Cloud.xml profil publikowania usługi jest skonfigurowany do używania portu 80. Jeśli wdrożysz aplikację bez określenia parametru MyWebAPI_PortNumber usługa korzysta z portu 8080.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat niektóre podstawowe pojęcia, które zostały omówione w tym artykule, zobacz [Zarządzanie aplikacjami dla wielu środowisk artykułach](service-fabric-manage-multiple-environment-app-configuration.md).

Aby uzyskać informacji na temat innych funkcji zarządzania aplikacji, które są dostępne w programie Visual Studio, zobacz [Zarządzanie aplikacjami usługi Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).
