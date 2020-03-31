---
title: Uaktualnianie konfiguracji autonomicznego klastra
description: Dowiedz się, jak uaktualnić konfigurację, która uruchamia autonomiczny klaster sieci szkieletowej usług.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8e7e01dac29cb9ba91c83270dac4e46c73b2089e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610130"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Uaktualnianie konfiguracji autonomicznego klastra 

Dla każdego nowoczesnego systemu, możliwość aktualizacji jest kluczem do długoterminowego sukcesu produktu. Klaster sieci szkieletowej usług Azure to zasób, którego jesteś właścicielem. W tym artykule opisano sposób uaktualniania ustawień konfiguracji autonomicznego klastra sieci szkieletowej usług.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Dostosowywanie ustawień klastra w pliku ClusterConfig.json
Klastry autonomiczne są konfigurowane za pośrednictwem pliku *ClusterConfig.json.* Aby dowiedzieć się więcej o różnych ustawieniach, zobacz [Ustawienia konfiguracji autonomicznego klastra systemu Windows](service-fabric-cluster-manifest.md).

Ustawienia można dodawać, aktualizować `fabricSettings` lub usuwać w sekcji w sekcji [Właściwości klastra](./service-fabric-cluster-manifest.md#cluster-properties) w pliku *ClusterConfig.json*. 

Na przykład następujący JSON dodaje nowe ustawienie *MaxDiskQuotaInMB* do `fabricSettings`sekcji *Diagnostyka* w obszarze:

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

Po zmodyfikowaniu ustawień w pliku ClusterConfig.json [przetestuj konfigurację klastra,](#test-the-cluster-configuration) a następnie [uaktualnij konfigurację klastra,](#upgrade-the-cluster-configuration) aby zastosować ustawienia do klastra. 

## <a name="test-the-cluster-configuration"></a>Testowanie konfiguracji klastra
Przed zainicjowaniem uaktualnienia konfiguracji można przetestować nową konfigurację klastra JSON, uruchamiając następujący skrypt programu PowerShell w pakiecie autonomicznym:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Lub użyj tego skryptu:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Niektórych konfiguracji nie można uaktualnić, takich jak punkty końcowe, nazwa klastra, adres IP węzła itp. Nowa konfiguracja klastra JSON jest testowany na stary i zgłasza błędy w oknie programu PowerShell, jeśli występuje problem.

## <a name="upgrade-the-cluster-configuration"></a>Uaktualnianie konfiguracji klastra
Aby uaktualnić uaktualnienie konfiguracji klastra, uruchom [program Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Uaktualnienie konfiguracji jest przetwarzane domeny uaktualnienia przez domenę uaktualnienia.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Uaktualnianie konfiguracji certyfikatu klastra
Certyfikat klastra jest używany do uwierzytelniania między węzłami klastra. Najazd certyfikatu należy wykonać z zachowaniem szczególnej ostrożności, ponieważ błąd blokuje komunikację między węzłami klastra.

Obsługiwane są cztery opcje:  

* Uaktualnienie pojedynczego certyfikatu: Ścieżka uaktualnienia to Certyfikat A (podstawowy) -> certyfikat B (podstawowy) -> certyfikat C (podstawowy) ->....

* Podwójne uaktualnienie certyfikatu: Ścieżka uaktualnienia to Certyfikat A (podstawowy) -> certyfikat A (podstawowy) i B (pomocniczy) -> certyfikat B (podstawowy) -> certyfikat B (podstawowy) i C (pomocniczy) -> certyfikat C (podstawowy) ->....

* Uaktualnienie typu certyfikatu: konfiguracja certyfikatu oparta na odciskach palców < > konfigurację certyfikatu opartego na commonname. Na przykład odcisk palca certyfikatu A (podstawowy) i odcisk palca B (pomocniczy) -> Certyfikat CommonName C.

* Aktualizacja odcisku palca wystawcy certyfikatu: Ścieżka uaktualnienia to Certyfikat CN=A,IssuerThumbprint=IT1 (podstawowy) -> Certyfikat CN=A,IssuerThumbprint=IT1,IT2 (podstawowy) -> Certyfikat CN=A,IssuerThumbprint=IT2 (podstawowy).


## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak dostosować niektóre [ustawienia klastra sieci szkieletowej usług](service-fabric-cluster-fabric-settings.md).
* Dowiedz się, jak [skalować klaster w i na zewnątrz](service-fabric-cluster-scale-up-down.md).
* Dowiedz się więcej o [uaktualnieniach aplikacji](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
