---
title: Parametryzowanie plików konfiguracyjnych w sieci szkieletowej usług Azure
description: Dowiedz się, jak parametryzować pliki konfiguracyjne w sieci szkieletowej usług, co jest użyteczną techniką podczas zarządzania wieloma środowiskami.
author: mikkelhegn
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 4e96a732cffd70b0a5c24e7ebafe214297a72720
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644634"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Jak parametryzować pliki konfiguracyjne w sieci szkieletowej usług

W tym artykule pokazano, jak parametryzować plik konfiguracji w sieci szkieletowej usług.  Jeśli nie znasz jeszcze podstawowych pojęć zarządzania aplikacjami w wielu środowiskach, przeczytaj artykuł [Zarządzanie aplikacjami w wielu środowiskach.](service-fabric-manage-multiple-environment-app-configuration.md)

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedura parametryzacji plików konfiguracyjnych

W tym przykładzie należy zastąpić wartość konfiguracji przy użyciu parametrów we wdrożeniu aplikacji.

1. Otwórz plik * \<MyService>\PackageRoot\Config\Settings.xml* w projekcie usługi.
1. Ustaw nazwę i wartość parametru konfiguracji, na przykład rozmiar pamięci podręcznej równy 25, dodając następujący kod XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Zapisz i zamknij plik.
1. Otwórz plik * \<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml.*
1. W pliku ApplicationManifest.xml zadeklaruj parametr `Parameters` i wartość domyślną w elemencie.  Zaleca się, aby nazwa parametru zawierała nazwę usługi (na przykład "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. W `ServiceManifestImport` sekcji pliku ApplicationManifest.xml dodaj `ConfigOverrides` i `ConfigOverride` element, odwołując się do pakietu konfiguracji, sekcji i parametru.

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
> W przypadku, gdy dodasz ConfigOverride, sieci szkieletowej usług zawsze wybiera parametry aplikacji lub wartość domyślną określoną w manifeście aplikacji.
>
>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o innych funkcjach zarządzania aplikacjami dostępnych w programie Visual Studio, zobacz [Zarządzanie aplikacjami sieci szkieletowej usług w programie Visual Studio.](service-fabric-manage-application-in-visual-studio.md)
