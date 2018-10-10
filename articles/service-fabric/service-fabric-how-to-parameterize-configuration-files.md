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
ms.openlocfilehash: 9057cdc22e277e4e12e9f439f3fbe0c5a5cda2a2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900517"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Jak zdefiniować parametry pliki konfiguracji w usłudze Service Fabric

W tym artykule pokazano, jak zdefiniować parametry pliku konfiguracji w usłudze Service Fabric.  Jeśli nie masz już znasz podstawowe pojęcia dotyczące zarządzania aplikacjami dla wielu środowisk, zapoznaj się z [Zarządzanie aplikacjami dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedura parametryzacja pliki konfiguracji

W tym przykładzie można zastąpić wartości konfiguracji przy użyciu parametrów podczas wdrażania aplikacji.

1. Otwórz  *<MyService>\PackageRoot\Config\Settings.xml* plik w projekcie usługi.
1. Ustaw nazwę parametru konfiguracji i wartość, na przykład rozmiar pamięci podręcznej równa 25, dodając następujący kod XML:

  ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
  ```

1. Zapisz i zamknij plik.
1. Otwórz  *<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml* pliku.
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