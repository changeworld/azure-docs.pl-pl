---
title: Instrukcje określania zmiennych środowiskowych dla usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiesz się, jak używać zmiennych środowiskowych dla aplikacji w usłudze Service Fabric
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
ms.openlocfilehash: f75de635f08ae06db349387a436c636c149ec9f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720233"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Sposobu określania zmiennych środowiskowych dla usług w usłudze Service Fabric

W tym artykule przedstawiono sposób określania zmiennych środowiskowych dla usługi lub kontenera w usłudze Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedura do określania zmiennych środowiskowych dla usług

W tym przykładzie możesz ustawić zmienną środowiskową dla kontenera. Tego artykułu przyjęto założenie, że masz już manifest aplikacji i usługi.

1. Otwórz plik ServiceManifest.xml.
1. W `CodePackage` elementu, Dodaj nowy `EnvironmentVariables` elementu i `EnvironmentVariable` element dla każdej zmiennej środowiskowej.

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

1. Aby zastąpić zmiennych środowiskowych w manifeście aplikacji, użyj `EnvironmentOverrides` elementu.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat niektóre podstawowe pojęcia, które zostały omówione w tym artykule, zobacz [Zarządzanie aplikacjami dla wielu środowisk artykułach](service-fabric-manage-multiple-environment-app-configuration.md).

Aby uzyskać informacji na temat innych funkcji zarządzania aplikacji, które są dostępne w programie Visual Studio, zobacz [Zarządzanie aplikacjami usługi Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).