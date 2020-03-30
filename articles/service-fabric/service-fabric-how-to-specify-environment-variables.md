---
title: Określanie zmiennych środowiskowych dla usług
description: Pokazuje, jak używać zmiennych środowiskowych dla aplikacji w sieci szkieletowej usług
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f4c4f2a1c140e3d0f181c4fd55482056f9f91b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614319"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Jak określić zmienne środowiskowe dla usług w sieci szkieletowej usług

W tym artykule pokazano, jak określić zmienne środowiskowe dla usługi lub kontenera w sieci szkieletowej usług.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedura określania zmiennych środowiskowych dla usług

W tym przykładzie można ustawić zmienną środowiskową dla kontenera. W artykule przyjęto założenie, że masz już manifest aplikacji i usługi.

1. Otwórz plik ServiceManifest.xml.
2. W `CodePackage` elemencie `EnvironmentVariables` dodaj nowy `EnvironmentVariable` element i element dla każdej zmiennej środowiskowej.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Zmienne środowiskowe mogą być zastąpione w manifeście aplikacji.

3. Aby zastąpić zmienne środowiskowe w manifeście `EnvironmentOverrides` aplikacji, należy użyć elementu.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Dynamiczne określanie zmiennych środowiskowych przy użyciu kompozycji dokowane

Sieci szkieletowej usług obsługuje możliwość [korzystania z dokowania compose dla wdrożenia](service-fabric-docker-compose.md#supported-compose-directives). Redagowanie plików może pozyskiwać zmienne środowiskowe z powłoki. To zachowanie może służyć do dynamicznego zastępowanie żądanych wartości środowiska:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o niektórych podstawowych pojęciach, które zostały omówione w tym artykule, zobacz [Zarządzanie aplikacjami dla wielu środowisk artykułów](service-fabric-manage-multiple-environment-app-configuration.md).

Aby uzyskać informacje o innych funkcjach zarządzania aplikacjami dostępnych w programie Visual Studio, zobacz [Zarządzanie aplikacjami sieci szkieletowej usług w programie Visual Studio.](service-fabric-manage-application-in-visual-studio.md)
