---
title: Parametryzacja pliki konfiguracji w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zdefiniować parametry pliki konfiguracji w usłudze Service Fabric.
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
ms.openlocfilehash: 3d03ca5cec2cef67862c2678b3b0a8f17b413787
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482441"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Jak zdefiniować parametry pliki konfiguracji w usłudze Service Fabric

W tym artykule pokazano, jak zdefiniować parametry pliku konfiguracji w usłudze Service Fabric.  Jeśli nie masz już znasz podstawowe pojęcia dotyczące zarządzania aplikacjami dla wielu środowisk, zapoznaj się z [Zarządzanie aplikacjami dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedura parametryzacja pliki konfiguracji

W tym przykładzie można zastąpić wartości konfiguracji przy użyciu parametrów podczas wdrażania aplikacji.

1. Otwórz  *\<Moja_usługa > \PackageRoot\Config\Settings.xml* plik w projekcie usługi.
1. Ustaw nazwę parametru konfiguracji i wartość, na przykład rozmiar pamięci podręcznej równa 25, dodając następujący kod XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Zapisz i zamknij plik.
1. Otwórz  *\<MojaAplikacja > \ApplicationPackageRoot\ApplicationManifest.xml* pliku.
1. W pliku ApplicationManifest.xml zadeklarować parametru i domyślne wartości w `Parameters` elementu.  Zaleca się, że nazwa parametru zawiera nazwę usługi (na przykład "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. W `ServiceManifestImport` sekcji znajduje się plik ApplicationManifest.xml, dodać `ConfigOverride` elementu, odwołania do pakietu konfiguracyjnego, sekcji i parametru.

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
> W przypadku, w którym dodajesz ConfigOverride Usługa Service Fabric wybiera zawsze parametry aplikacji lub wartość domyślna określona w manifeście aplikacji.
>
>

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacji na temat innych funkcji zarządzania aplikacji, które są dostępne w programie Visual Studio, zobacz [Zarządzanie aplikacjami usługi Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).