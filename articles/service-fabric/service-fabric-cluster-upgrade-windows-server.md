---
title: Uaktualnianie wersji autonomicznego klastra
description: Uaktualnij kod sieci szkieletowej usług Azure, który uruchamia autonomiczny klaster sieci szkieletowej usług.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 489a90180454e2b4a9dad34730fbd3c4f235a2ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598106"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Uaktualnianie wersji sieci szkieletowej usług, która działa w klastrze 

Dla każdego nowoczesnego systemu, możliwość aktualizacji jest kluczem do długoterminowego sukcesu produktu. Klaster sieci szkieletowej usług Azure to zasób, którego jesteś właścicielem. W tym artykule opisano sposób uaktualniania wersji sieci szkieletowej usług uruchomionej w klastrze autonomicznym.

> [!NOTE]
> Upewnij się, że klaster zawsze uruchamia obsługiwaną wersję sieci szkieletowej usług. Gdy firma Microsoft ogłosi wydanie nowej wersji sieci szkieletowej usług, poprzednia wersja jest oznaczona do końca pomocy technicznej po co najmniej 60 dniach od daty ogłoszenia. Nowe wydania są ogłaszane [na blogu zespołu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nowa wersja jest dostępna do wyboru w tym momencie.
>
>

Klaster można uaktualnić do nowej wersji tylko wtedy, gdy używasz konfiguracji węzła w stylu produkcyjnym, gdzie każdy węzeł sieci szkieletowej usług jest przydzielany na osobnej maszynie fizycznej lub wirtualnej. Jeśli masz klaster programistyczny, w którym więcej niż jeden węzeł sieci szkieletowej usług znajduje się na jednej maszynie fizycznej lub wirtualnej, należy ponownie utworzyć klaster przy nowej wersji.

Dwa różne przepływy pracy można uaktualnić klastra do najnowszej wersji lub obsługiwanej wersji sieci szkieletowej usług. Jeden przepływ pracy jest dla klastrów, które mają łączność, aby pobrać najnowszą wersję automatycznie. Inny przepływ pracy jest dla klastrów, które nie mają łączności, aby pobrać najnowszą wersję sieci szkieletowej usług.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Włączanie automatycznego uaktualniania wersji sieci szkieletowej klastra
Aby ustawić klaster do pobierania aktualizacji sieci szkieletowej usług, gdy firma Microsoft wyda nową wersję, ustaw konfigurację klastra na `fabricClusterAutoupgradeEnabled` *true*. Aby ręcznie wybrać obsługiwaną wersję sieci szkieletowej usług, na której `fabricClusterAutoupgradeEnabled` ma być włączony klaster, ustaw konfigurację klastra na *fałszywą*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Uaktualnianie klastrów, które mają łączność, aby pobrać najnowszy kod i konfigurację
Wykonaj następujące kroki, aby uaktualnić klaster do obsługiwanej wersji, jeśli węzły klastra mają połączenie z Internetem z [Centrum pobierania Microsoft](https://download.microsoft.com).

W przypadku klastrów, które mają łączność z [Centrum pobierania Microsoft,](https://download.microsoft.com)firma Microsoft okresowo sprawdza dostępność nowych wersji sieci szkieletowej usług.

Gdy dostępna jest nowa wersja sieci szkieletowej usług, pakiet jest pobierany lokalnie do klastra i aprowiowany w celu uaktualnienia. Ponadto, aby poinformować klienta o tej nowej wersji, system pokazuje jawne ostrzeżenie o kondycji klastra, które jest podobne do następujących:

"Bieżąca wersja klastra [wersja #] kończy [data]".

Po uruchomieniu klastra najnowszej wersji ostrzeżenie znika.

Po wyświetleniu ostrzeżenia o kondycji klastra uaktualnij klaster:

1. Połącz się z klastrem z dowolnego komputera, który ma dostęp administratora do wszystkich maszyn, które są wymienione jako węzły w klastrze. Komputer, na który jest uruchamiany ten skrypt, nie musi być częścią klastra.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Pobierz listę wersji sieci szkieletowej usług, do których można uaktualnić.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Powinieneś uzyskać dane wyjściowe podobne do tego:

    ![Pobierz wersje sieci szkieletowej usług][getfabversions]
3. Uruchom uaktualnienie klastra do dostępnej wersji przy użyciu polecenia [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Aby monitorować postęp uaktualnienia, można użyć Eksploratora sieci szkieletowej usług lub uruchomić następujące polecenie programu PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie zostanie wycofane. Aby określić niestandardowe zasady kondycji dla polecenia Start-ServiceFabricClusterUpgrade, zobacz dokumentację [dla start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Po rozwiązaniu problemów, które spowodowały wycofanie, zainicjuj uaktualnienie ponownie, wykonując te same kroki, co wcześniej opisane.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Uaktualnianie klastrów, które nie mają *łączności* w celu pobrania najnowszego kodu i konfiguracji
Wykonaj następujące kroki, aby uaktualnić klaster do obsługiwanej wersji, jeśli węzły klastra nie mają połączenia z Internetem z [Centrum pobierania Microsoft](https://download.microsoft.com).

> [!NOTE]
> Jeśli korzystasz z klastra, który nie jest połączony z Internetem, musisz monitorować [blog zespołu sieci szkieletowej usług,](https://blogs.msdn.microsoft.com/azureservicefabric/) aby dowiedzieć się więcej o nowych wersjach. System nie wyświetla ostrzeżenia o kondycji klastra, aby ostrzegać o nowych wersjach.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatyczne inicjowanie obsługi administracyjnej a ręczne inicjowanie obsługi administracyjnej
Aby włączyć automatyczne pobieranie i rejestrację dla najnowszej wersji kodu, skonfiguruj usługę aktualizacji sieci szkieletowej usług. Aby uzyskać instrukcje, zobacz *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* w [pakiecie autonomicznym](service-fabric-cluster-standalone-package-contents.md).

Aby wykonać proces ręczny, postępuj zgodnie z tymi instrukcjami.

Zmodyfikuj konfigurację klastra, aby ustawić następującą właściwość na *false* przed rozpoczęciem uaktualniania konfiguracji:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Aby uzyskać szczegółowe informacje na temat użycia, zobacz [polecenie Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell. Przed rozpoczęciem uaktualniania konfiguracji należy zaktualizować program "clusterConfigurationVersion" w usłudze JSON.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Przepływ pracy uaktualniania klastra

1. Uruchom [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) z jednego z węzłów w klastrze i zanotuj *TargetCodeVersion*.

2. Uruchom następujące czynności z komputera podłączonego do Internetu, aby wyświetlić listę wszystkich wersji zgodnych z uaktualnieniem z bieżącą wersją i pobrać odpowiedni pakiet z skojarzonych łączy pobierania:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Połącz się z klastrem z dowolnego komputera, który ma dostęp administratora do wszystkich maszyn, które są wymienione jako węzły w klastrze. Komputer, na który jest uruchamiany ten skrypt, nie musi być częścią klastra.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Skopiuj pobrany pakiet do magazynu obrazów klastra.

5. Zarejestruj skopiowany pakiet.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Rozpocznij uaktualnienie klastra do dostępnej wersji.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Można monitorować postęp uaktualnienia w Eksploratorze sieci szkieletowej usług lub uruchomić następujące polecenie programu PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie zostanie wycofane. Aby określić niestandardowe zasady kondycji dla polecenia Start-ServiceFabricClusterUpgrade, zobacz dokumentację [dla start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Po rozwiązaniu problemów, które spowodowały wycofanie, zainicjuj uaktualnienie ponownie, wykonując te same kroki, co wcześniej opisane.

## <a name="next-steps"></a>Następne kroki
* [Uaktualnianie konfiguracji autonomicznego klastra](service-fabric-cluster-config-upgrade-windows-server.md)
* Dostosuj niektóre [ustawienia klastra sieci szkieletowej usług](service-fabric-cluster-fabric-settings.md).
* [Skalowanie klastra w i na zewnątrz](service-fabric-cluster-scale-up-down.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
