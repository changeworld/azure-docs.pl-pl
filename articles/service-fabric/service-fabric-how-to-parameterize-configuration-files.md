---
title: Pliki konfiguracji Sparametryzuj na platformie Azure Service Fabric | Microsoft Docs
description: Dowiedz się, jak Sparametryzuj pliki konfiguracji w Service Fabric.
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: dad497978de7187177998524db3b2f2ee448c717
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464782"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Jak Sparametryzuj pliki konfiguracji w Service Fabric

W tym artykule opisano sposób Sparametryzuj pliku konfiguracji w Service Fabric.  Jeśli nie znasz już podstawowych pojęć związanych z zarządzaniem aplikacjami dla wielu środowisk, przeczytaj artykuł [Zarządzanie aplikacjami w wielu środowiskach](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedura dla plików konfiguracji parametryzacja

W tym przykładzie zastąpisz wartość konfiguracji przy użyciu parametrów we wdrożeniu aplikacji.

1. Otwórz plik *\PackageRoot\Config\Settings.XML usługi > w projekcie usługi. \<*
1. Ustaw nazwę i wartość parametru konfiguracji, na przykład rozmiar pamięci podręcznej równej 25, dodając następujący kod XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Zapisz i zamknij plik.
1. Otwórz plik *\ApplicationPackageRoot\ApplicationManifest.XML aplikacji >. \<*
1. W pliku ApplicationManifest. XML Zadeklaruj parametr i wartość domyślną w `Parameters` elemencie.  Zaleca się, aby nazwa parametru zawierała nazwę usługi (na przykład "Moja usługa").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. W sekcji pliku ApplicationManifest. XML `ConfigOverrides` Dodaj element i `ConfigOverride` , odwołując się do pakietu konfiguracyjnego, sekcji i parametru. `ServiceManifestImport`

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> W przypadku dodania elementu ConfigOverride, Service Fabric zawsze wybiera parametry aplikacji lub wartość domyślną określoną w manifeście aplikacji.
>
>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na temat innych funkcji zarządzania aplikacjami, które są dostępne w programie Visual Studio, zobacz [Zarządzanie aplikacjami Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).
