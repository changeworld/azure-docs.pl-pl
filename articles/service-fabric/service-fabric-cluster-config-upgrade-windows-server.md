---
title: Uaktualnij konfigurację autonomicznego klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uaktualnić konfigurację, która uruchamia autonomicznego klastra usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: f99c1ebb64bf881bcd42f15e13bb81b96ccfa064
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60387132"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Uaktualnij konfigurację klastra autonomicznego 

Dla każdego nowoczesnego systemu możliwość uaktualnienia jest kluczem do długoterminowym sukcesie produktu. Klaster usługi Azure Service Fabric jest zasobem, którego jesteś właścicielem. W tym artykule opisano sposób uaktualniania ustawienia konfiguracji klastra autonomicznego usługi Service Fabric.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Dostosowywanie ustawień klastra w pliku ClusterConfig.json
Klastry autonomiczne można skonfigurować za pomocą *ClusterConfig.json* pliku. Aby dowiedzieć się więcej o różnych ustawieniach, zobacz [ustawienia konfiguracji dla autonomicznego klastra Windows](service-fabric-cluster-manifest.md).

Dodawanie, aktualizowanie lub Usuń ustawienia w `fabricSettings` sekcji [właściwości klastra](./service-fabric-cluster-manifest.md#cluster-properties) sekcji *ClusterConfig.json*. 

Na przykład następujące dane JSON dodaje nowe ustawienie *MaxDiskQuotaInMB* do *diagnostyki* sekcji `fabricSettings`:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Po zmodyfikowaniu ustawień w pliku ClusterConfig.json [test konfiguracji klastra](#test-the-cluster-configuration) i następnie [Uaktualnij konfigurację klastra](#upgrade-the-cluster-configuration) do zastosowania ustawień do klastra. 

## <a name="test-the-cluster-configuration"></a>Testowanie konfiguracji klastra
Przed rozpoczęciem uaktualniania konfiguracji, można przetestować nową konfigurację klastra w formacie JSON, uruchamiając następujący skrypt programu PowerShell w pakiecie autonomicznym:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Lub użyj tego skryptu:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Niektóre konfiguracje nie może być uaktualniany, takich jak punkty końcowe, nazwą klastra, adres IP węzła, itp. Nowy plik JSON z konfiguracją klastra jest testowany dla starego i zgłasza błędy w oknie programu PowerShell, jeśli występuje problem.

## <a name="upgrade-the-cluster-configuration"></a>Uaktualnij konfigurację klastra
Aby uaktualnić uaktualniania konfiguracji klastra, uruchom [Start ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Uaktualnianie konfiguracji jest przetworzonych domeny uaktualnień według domeny uaktualnienia.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Uaktualnij konfigurację certyfikatu klastra
Certyfikat klastra jest używany do uwierzytelniania między węzłami klastra. Przerzucanie certyfikatów powinno być przeprowadzane za pomocą wyjątkową ostrożność, ponieważ błąd blokuje komunikację między węzłami klastra.

Obsługiwane są cztery opcje:  

* Uaktualnienie pojedynczego certyfikatu: Ścieżka uaktualniania prowadzi certyfikatu (podstawowy) -> certyfikat B (podstawowy) -> C certyfikatu (podstawowy) ->...

* Double uaktualnić certyfikatu: Ścieżka uaktualniania prowadzi certyfikatu (podstawowy) -> certyfikatu (podstawowy), a B (informacje pomocnicze) -> certyfikat B (podstawowy) -> certyfikat B (podstawowy) i języka C (informacje pomocnicze) -> C certyfikatu (podstawowy) ->...

* Uaktualnianie typ certyfikatu: Konfiguracja certyfikatu opartego na CommonName configuration <> — na podstawie odcisku palca certyfikatu. Na przykład odcisk palca certyfikatu (podstawowy) i odcisk palca B (informacje pomocnicze) -> certyfikat CommonName C.

* Uaktualnianie odcisk palca wystawcy certyfikatu: Ścieżka uaktualniania prowadzi certyfikatu, CN = A, IssuerThumbprint = IT1 (podstawowy) -> CN certyfikatu = A, IssuerThumbprint = IT1, IT2 (podstawowy) -> CN certyfikatu = A, IssuerThumbprint = IT2 (podstawowy).


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak dostosować niektóre [ustawienia klastra usługi Service Fabric](service-fabric-cluster-fabric-settings.md).
* Dowiedz się, jak [skalowania klastra i pomniejszać](service-fabric-cluster-scale-up-down.md).
* Dowiedz się więcej o [uaktualnień aplikacji](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
