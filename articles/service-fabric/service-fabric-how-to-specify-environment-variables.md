---
title: Jak określić zmienne środowiskowe dla usług na platformie Azure Service Fabric | Microsoft Docs
description: Pokazuje, jak używać zmiennych środowiskowych dla aplikacji w Service Fabric
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
ms.openlocfilehash: df9b199c24301016b9f9da8a8dec52129bbf94bd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703540"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Jak określić zmienne środowiskowe dla usług w Service Fabric

W tym artykule opisano sposób określania zmiennych środowiskowych dla usługi lub kontenera w Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedura określania zmiennych środowiskowych dla usług

W tym przykładzie ustawisz zmienną środowiskową dla kontenera. W tym artykule założono, że masz już manifest aplikacji i usługi.

1. Otwórz plik servicemanifest. XML.
2. W elemencie `CodePackage` Dodaj nowy element `EnvironmentVariables` i element `EnvironmentVariable` dla każdej zmiennej środowiskowej.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Zmienne środowiskowe można przesłonić w manifeście aplikacji.

3. Aby zastąpić zmienne środowiskowe w manifeście aplikacji, użyj elementu `EnvironmentOverrides`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Dynamiczne określanie zmiennych środowiskowych za pomocą Docker Compose

Service Fabric obsługuje możliwość [użycia Docker Compose do wdrożenia](service-fabric-docker-compose.md#supported-compose-directives). Pliki redagowania mogą źródłowe zmienne środowiskowe z powłoki. To zachowanie może służyć do dynamicznego podstawiania żądanych wartości środowiska:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat podstawowych pojęć omówionych w tym artykule, zobacz [artykuł Zarządzanie aplikacjami dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md).

Aby uzyskać informacje na temat innych funkcji zarządzania aplikacjami, które są dostępne w programie Visual Studio, zobacz [Zarządzanie aplikacjami Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).
