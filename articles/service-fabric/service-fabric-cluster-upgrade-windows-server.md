---
title: Uaktualnianie klastra usługi Azure Service Fabric autonomicznego w systemie Windows Server | Dokumentacja firmy Microsoft
description: Uaktualnienie kodu usługi Azure Service Fabric i/lub konfiguracji, która uruchamia autonomicznego klastra usługi Service Fabric, włącznie z ustawieniem trybu aktualizacji klastra.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 2190978b2583b2f5d8a1b10431c65fd24fe6842d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228151"
---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Uaktualnianie klastra usługi Azure Service Fabric autonomicznego w systemie Windows Server 
> [!div class="op_single_selector"]
> * [Klaster usługi Azure](service-fabric-cluster-upgrade.md)
> * [Klaster autonomiczny](service-fabric-cluster-upgrade-windows-server.md)
>
>

Dla każdego nowoczesnego systemu możliwość uaktualnienia jest kluczem do długoterminowym sukcesie produktu. Klaster usługi Azure Service Fabric jest zasobem, którego jesteś właścicielem. W tym artykule opisano, jak możesz upewnić się, że klaster jest zawsze uruchamiany obsługiwane wersje kodu usługi Service Fabric i konfiguracji.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Kontrola wersji usługi Service Fabric, który działa w klastrze
Do klastra, aby pobrać aktualizacje usługi Service Fabric, gdy firma Microsoft publikuje nową wersję, ustawia fabricClusterAutoupgradeEnabled konfiguracji klastra *true*. Aby wybrać obsługiwaną wersję usługi Service Fabric, który należy do klastra, aby być na, ustaw konfiguracji klastra fabricClusterAutoupgradeEnabled *false*.

> [!NOTE]
> Upewnij się, że klaster zawsze działa obsługiwana wersja usługi Service Fabric. Microsoft zapowiada wydaniu nowej wersji usługi Service Fabric, poprzednia wersja jest oznaczony do zakończenia wsparcia po co najmniej 60 dni od daty anonsu. Nowe wersje są anonsowane [na blogu zespołu usługi Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nowa wersja jest dostępna do wyboru, w tym momencie.
>
>

Klaster można uaktualnić do nowej wersji, tylko wtedy, gdy używasz się konfiguracji węzła stylu produkcji, gdzie każdy węzeł usługi Service Fabric jest przydzielona do oddzielnych maszyny fizycznej lub wirtualnej. Jeśli masz klaster projektowy, gdy więcej niż jeden węzeł usługi Service Fabric znajduje się na jednej maszynie fizycznej lub wirtualnej, należy ponownie utworzyć klaster przy użyciu nowej wersji.

Klaster można uaktualnić do najnowszej wersji lub obsługiwana wersja usługi Service Fabric dwóch różnych przepływów pracy. Jeden przepływ pracy jest w przypadku klastrów, które mają łączność, aby pobrać najnowszą wersję automatycznie. Inny przepływ pracy jest w przypadku klastrów, które nie mają połączenia, aby pobrać najnowszą wersję usługi Service Fabric.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Uaktualnianie klastrów, które mają łączność do pobierania najnowszych kodu i konfiguracji
Wykonaj następujące kroki w celu uaktualnienia klastra do obsługiwanej wersji, jeśli węzły klastra ma połączenia internetowego z [Microsoft Download Center](https://download.microsoft.com).

W przypadku klastrów, które mają łączność z [Microsoft Download Center](https://download.microsoft.com), Microsoft okresowo sprawdza dostępność nowych wersji usługi Service Fabric.

Gdy dostępna jest nowa wersja usługi Service Fabric, pakiet jest pobierana lokalnie z klastrem i zainicjowana obsługa administracyjna dla uaktualnienie. Ponadto informowanie klientów o tej nowej wersji, system wyświetla ostrzeżenie kondycji jawne klastra, który jest podobny do następującego:

"Bieżącą wersję klastra [wersja #] Obsługa kończy się [date]."

Po uruchomieniu klastra najnowszej wersji ostrzeżenie stanie się niepotrzebna.

#### <a name="cluster-upgrade-workflow"></a>Przepływ pracy uaktualniania klastra
Gdy pojawi się ostrzeżenie kondycji klastra, wykonaj następujące czynności:

1. Połącz się z klastrem z dowolnej maszyny, które ma dostęp administratora do wszystkich maszyn, które są wymienione jako węzły w klastrze. Komputer, na którym uruchamiany jest skrypt nie musi być częścią klastra.

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

2. Pobierz listę wersji usługi Service Fabric, które można uaktualnić.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Należy otrzymasz dane wyjściowe podobne do poniższego:

    ![Pobierz wersje usługi Service Fabric][getfabversions]
3. Uruchomić uaktualniania klastra do wersji dostępna przy użyciu [Start ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) polecenia programu Windows PowerShell.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Aby monitorować postęp uaktualnienia, możesz użyć narzędzia Service Fabric Explorer lub uruchomienie następującego polecenia programu PowerShell:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Aby określić zasady dotyczące kondycji niestandardowego dla polecenia Start-ServiceFabricClusterUpgrade, zajrzyj do dokumentacji [Start ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Po rozwiązaniu problemów, które spowodowało wycofywania, należy ponownie zainicjować uaktualnienie wykonując te same czynności w sposób opisany wcześniej.

### <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Uaktualnianie klastrów, które mają *braku łączności z* można pobrać najnowszy kod i Konfiguracja
Wykonaj następujące kroki w celu uaktualnienia klastra do obsługiwanej wersji, jeśli węzły klastra nie ma łączności z Internetem [Microsoft Download Center](https://download.microsoft.com).

> [!NOTE]
> Jeśli korzystasz z klastra, który nie jest połączony z Internetem, należy monitorować blog zespołu usługi Service Fabric, aby dowiedzieć się więcej o nowych wersjach. System nie wyświetla ostrzeżenie kondycji klastra o nowych wersjach.  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatyczna aprowizacja a aprowizacja ręczna
Aby włączyć automatyczne pobieranie i rejestracja w celu uzyskania najnowszej wersji kodu, należy skonfigurować usługi Service Fabric aktualizacji. Aby uzyskać instrukcje, zobacz Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt wewnątrz [pakietu autonomicznego](service-fabric-cluster-standalone-package-contents.md).
Aby ręczny proces wykonaj te instrukcje.

Zmodyfikuj konfigurację klastra, aby ustawić następujące właściwości *false* przed rozpoczęciem uaktualniania konfiguracji:

        "fabricClusterAutoupgradeEnabled": false,

Aby uzyskać szczegóły użycia, zobacz [Start ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) polecenia programu PowerShell. Upewnij się zaktualizować "clusterConfigurationVersion" w JSON, przed rozpoczęciem uaktualniania konfiguracji.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Przepływ pracy uaktualniania klastra

1. Uruchom [Get ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) w jednym z węzłów w klastrze i zwróć uwagę, *TargetCodeVersion*.

2. Uruchom następujące polecenie z maszyny podłączonej do Internetu, aby wyświetlić listę wszystkich wersji uaktualnienia zgodne z bieżącą wersją i pobrać odpowiedni pakiet z łączy do pobierania:

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Połącz się z klastrem z dowolnej maszyny, które ma dostęp administratora do wszystkich maszyn, które są wymienione jako węzły w klastrze. Komputer, na którym uruchamiany jest skrypt nie musi być częścią klastra.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. Skopiuj pobrany pakiet do magazynu obrazów klastra.

5. Zarejestruj pakiet skopiowany.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Uruchomić uaktualniania klastra do dostępnej wersji.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Można monitorować postępy uaktualniania w narzędziu Service Fabric Explorer lub uruchomić następujące polecenie programu PowerShell:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Aby określić zasady dotyczące kondycji niestandardowego dla polecenia Start-ServiceFabricClusterUpgrade, zajrzyj do dokumentacji [Start ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Po rozwiązaniu problemów, które spowodowało wycofywania, należy ponownie zainicjować uaktualnienie wykonując te same czynności w sposób opisany wcześniej.


## <a name="upgrade-the-cluster-configuration"></a>Uaktualnij konfigurację klastra
Przed rozpoczęciem uaktualniania konfiguracji, można przetestować nową konfigurację klastra w formacie JSON, uruchamiając następujący skrypt programu PowerShell w pakiecie autonomicznym:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
Lub użyj tego skryptu:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

Niektóre konfiguracje nie może być uaktualniany, takich jak punkty końcowe, nazwą klastra, adres IP węzła, itp. Nowy plik JSON z konfiguracją klastra jest testowany dla starego i zgłasza błędy w oknie programu PowerShell, jeśli występuje problem.

Aby uaktualnić uaktualniania konfiguracji klastra, uruchom [Start ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Uaktualnianie konfiguracji jest przetworzonych domeny uaktualnień według domeny uaktualnienia.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Uaktualnienie konfiguracji certyfikatu klastra  
Certyfikat klastra jest używany do uwierzytelniania między węzłami klastra. Przerzucanie certyfikatów powinno być przeprowadzane za pomocą wyjątkową ostrożność, ponieważ błąd blokuje komunikację między węzłami klastra.

Technicznie rzecz biorąc są obsługiwane cztery opcje:  

* Uaktualnienie pojedynczego certyfikatu: Ścieżka uaktualniania prowadzi certyfikatu (podstawowy) -> certyfikat B (podstawowy) -> C certyfikatu (podstawowy) ->...

* Double uaktualnić certyfikatu: Ścieżka uaktualniania prowadzi certyfikatu (podstawowy) -> certyfikatu (podstawowy), a B (informacje pomocnicze) -> certyfikat B (podstawowy) -> certyfikat B (podstawowy) i języka C (informacje pomocnicze) -> C certyfikatu (podstawowy) ->...

* Uaktualnianie typu certyfikatu: Konfiguracja certyfikatów na podstawie CommonName configuration <> — na podstawie odcisku palca certyfikatu. Na przykład odcisk palca certyfikatu (podstawowy) i odcisk palca B (informacje pomocnicze) -> certyfikat CommonName C.

* Uaktualnienie odcisk palca wystawcy certyfikatu: Ścieżka uaktualniania prowadzi CN certyfikatu = A, IssuerThumbprint = IT1 (podstawowy) -> CN certyfikatu = A, IssuerThumbprint = IT1, IT2 (podstawowy) -> CN certyfikatu = A, IssuerThumbprint = IT2 (głównej).


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak dostosować niektóre [ustawienia klastra usługi Service Fabric](service-fabric-cluster-fabric-settings.md).
* Dowiedz się, jak [skalowania klastra i pomniejszać](service-fabric-cluster-scale-up-down.md).
* Dowiedz się więcej o [uaktualnień aplikacji](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
