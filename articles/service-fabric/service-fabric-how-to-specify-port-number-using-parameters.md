---
title: Określanie numeru portu usługi przy użyciu parametrów
description: Pokazuje, jak używać parametrów do określania portu dla aplikacji w sieci szkieletowej usług
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609863"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Jak określić numer portu usługi przy użyciu parametrów w sieci szkieletowej usług

W tym artykule pokazano, jak określić numer portu usługi przy użyciu parametrów w sieci szkieletowej usług przy użyciu programu Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procedura określania numeru portu usługi przy użyciu parametrów

W tym przykładzie można ustawić numer portu dla asp.net podstawowego interfejsu API sieci web przy użyciu parametru.

1. Otwórz program Visual Studio i utwórz nową aplikację sieci szkieletowej usług.
1. Wybierz szablon Bezstanowy ASP.NET podstawowy.
1. Wybierz interfejs API sieci Web.
1. Otwórz plik ServiceManifest.xml.
1. Zanotuj nazwę punktu końcowego określonego dla usługi. Wartość domyślna to `ServiceEndpoint`.
1. Otwórz plik ApplicationManifest.xml
1. W `ServiceManifestImport` elemencie `RessourceOverrides` dodaj nowy element z odwołaniem do punktu końcowego w pliku ServiceManifest.xml.

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

1. W `Endpoint` elemencie można teraz zastąpić dowolny atrybut za pomocą parametru. W tym przykładzie `Port` należy określić i ustawić go na nazwę parametru przy użyciu nawiasów kwadratowych - na przykład`[MyWebAPI_PortNumber]`

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

1. Nadal w pliku ApplicationManifest.xml należy określić `Parameters` parametr w elemencie

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. I zdefiniować`DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Otwórz folder ApplicationParameters `Cloud.xml` i plik
1. Aby określić inny port, który ma być używany podczas publikowania w klastrze zdalnym, dodaj parametr z numerem portu do tego pliku.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Podczas publikowania aplikacji z programu Visual Studio przy użyciu profilu publikowania Cloud.xml usługa jest skonfigurowana do używania portu 80. Jeśli wdrożysz aplikację bez określania parametru MyWebAPI_PortNumber, usługa używa portu 8080.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o niektórych podstawowych pojęciach, które zostały omówione w tym artykule, zobacz [Zarządzanie aplikacjami dla wielu środowisk artykułów](service-fabric-manage-multiple-environment-app-configuration.md).

Aby uzyskać informacje o innych funkcjach zarządzania aplikacjami dostępnych w programie Visual Studio, zobacz [Zarządzanie aplikacjami sieci szkieletowej usług w programie Visual Studio.](service-fabric-manage-application-in-visual-studio.md)
