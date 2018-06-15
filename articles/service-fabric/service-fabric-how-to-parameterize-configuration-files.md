---
title: Jak parametryzacja pliki konfiguracji w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Pokazuje, jak parametryzacja pliki konfiguracji w sieci szkieletowej usług
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: e5bb2f270cc5a6f288e1e995f4bfa74f4e3551b7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207822"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Jak parametryzacja pliki konfiguracji w sieci szkieletowej usług

W tym artykule przedstawiono sposób parametryzacja plik konfiguracji w sieci szkieletowej usług.

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedura parametryzacja pliki konfiguracji

W tym przykładzie można zastąpić wartości konfiguracji we wdrożeniu aplikacji przy użyciu parametrów.

1. Otwórz plik Config\Settings.xml.
1. Ustaw parametr konfiguracji, dodając następujący kod XML:

    ```xml
      <Section Name="MyConfigSection">
        <Parameter Name="CacheSize" Value="25" />
      </Section>
    ```

1. Zapisz i zamknij plik.
1. Otwórz plik `ApplicationManifest.xml`.
1. Dodaj `ConfigOverride` element odwołanie do pakietu konfiguracji, sekcji, a parametr.

      ```xml
        <ConfigOverrides>
          <ConfigOverride Name="Config">
              <Settings>
                <Section Name="MyConfigSection">
                    <Parameter Name="CacheSize" Value="[Stateless1_CacheSize]" />
                </Section>
              </Settings>
          </ConfigOverride>
        </ConfigOverrides>
      ```

1. Następnie nadal w pliku ApplicationManifest.xml, możesz określić parametr w `Parameters` — element

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" />
      </Parameters>
    ```

1. I zdefiniuj `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" DefaultValue="80" />
      </Parameters>
    ```

> [!NOTE]
> W przypadku których dodawane ConfigOverride sieci szkieletowej usług zawsze wybiera parametry aplikacji lub wartość domyślna określona w manifeście aplikacji.
>
>

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat niektórych podstawowych pojęciach, które zostały omówione w tym artykule, zobacz [zarządzania aplikacjami dla wielu artykułach środowisk](service-fabric-manage-multiple-environment-app-configuration.md).

Aby uzyskać informacje o inne funkcje zarządzania aplikacjami, które są dostępne w programie Visual Studio, zobacz [Zarządzaj aplikacjami sieci szkieletowej usług w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).