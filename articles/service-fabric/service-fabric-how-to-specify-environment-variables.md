---
title: Instrukcje określania zmiennych środowiskowych dla usług w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Przedstawiono sposób używać zmiennych środowiskowych dla aplikacji w sieci szkieletowej usług
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
ms.openlocfilehash: 4325b3acd3cbc73ee5976021bebe96c267b2a6dd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Sposób określania zmiennych środowiskowych dla usług w sieci szkieletowej usług

W tym artykule przedstawiono sposób określania zmiennych środowiskowych dla danej usługi lub kontenera w sieci szkieletowej usług.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedura określania zmiennych środowiskowych dla usług

W tym przykładzie należy ustawić wartość zmiennej środowiskowej dla kontenera. Artykule przyjęto założenie, że masz już manifest aplikacji i usług.

1. Otwórz plik ServiceManifest.xml.
1. W `CodePackage` elementu, Dodaj nową `EnvironmentVariables` elementu i `EnvironmentVariable` elementu dla każdej zmiennej środowiskowej.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DeafultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    Zmienne środowiskowe może zostać zastąpiona w manifeście aplikacji.

1. Aby zastąpić zmienne środowiskowe w manifeście aplikacji, użyj `EnvironmentOverrides` elementu.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat niektórych podstawowych pojęciach, które zostały omówione w tym artykule, zobacz [zarządzania aplikacjami dla wielu artykułach środowisk](service-fabric-manage-multiple-environment-app-configuration.md).

Aby uzyskać informacje o inne funkcje zarządzania aplikacjami, które są dostępne w programie Visual Studio, zobacz [Zarządzaj aplikacjami sieci szkieletowej usług w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).