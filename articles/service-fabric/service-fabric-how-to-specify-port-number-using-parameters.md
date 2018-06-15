---
title: Jak określić numer portu usługi przy użyciu parametrów w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Demonstracja używania parametrów, aby określić port dla aplikacji w sieci szkieletowej usług
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
ms.openlocfilehash: 06cfb375c6c18082a0d0316cfcb742a7779fc8a8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206382"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Jak określić numer portu usługi przy użyciu parametrów w sieci szkieletowej usług

W tym artykule przedstawiono sposób określić numer portu usługi przy użyciu parametrów w sieci szkieletowej usług za pomocą programu Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procedury do określenia numeru portu usługi przy użyciu parametrów

W tym przykładzie należy ustawić numer portu dla platformy asp.net core interfejsu API sieci web za pomocą parametru.

1. Otwórz program Visual Studio i utworzyć nową aplikację usługi sieć szkieletowa usług.
1. Wybierz szablon bezstanowych platformy ASP.NET Core.
1. Wybierz interfejs API sieci Web.
1. Otwórz plik ServiceManifest.xml.
1. Zanotuj nazwę punktu końcowego określona dla usługi. Wartość domyślna to `ServiceEndpoint`.
1. Otwieranie pliku ApplicationManifest.xml
1. W `ServiceManifestImport` elementu, Dodaj nową `RessourceOverrides` elementu z odwołaniem do punktu końcowego w pliku ServiceManifest.xml.

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

1. W `Endpoint` elementu, można teraz zmienić dowolny atrybut za pomocą parametru. W tym przykładzie należy określić `Port` i ustaw ją na nazwę parametru za pomocą nawiasów kwadratowych — na przykład `[MyWebAPI_PortNumber]`

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

1. Następnie nadal w pliku ApplicationManifest.xml, możesz określić parametr w `Parameters` — element

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
1. Aby określić inny port do użycia podczas publikowania do zdalnego klastra, Dodaj parametr numer portu do tego pliku.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="80" />
      </Parameters>
    ```

Podczas publikowania aplikacji w programie Visual Studio przy użyciu Cloud.xml profilu publikowania usługi jest skonfigurowany do używania portu 80. W przypadku wdrożenia aplikacji bez określenia parametru MyWebAPI_PortNumber, usługa korzysta z portu 8080.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat niektórych podstawowych pojęciach, które zostały omówione w tym artykule, zobacz [zarządzania aplikacjami dla wielu artykułach środowisk](service-fabric-manage-multiple-environment-app-configuration.md).

Aby uzyskać informacje o inne funkcje zarządzania aplikacjami, które są dostępne w programie Visual Studio, zobacz [Zarządzaj aplikacjami sieci szkieletowej usług w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).