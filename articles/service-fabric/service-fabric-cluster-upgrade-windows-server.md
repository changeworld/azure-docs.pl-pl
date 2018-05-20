---
title: Uaktualnianie klastra usługi sieć szkieletowa usług Azure autonomicznego w systemie Windows Server | Dokumentacja firmy Microsoft
description: Uaktualnij sieć szkieletowa usług Azure kod i/lub konfigurację, która uruchamia autonomicznej klastra sieci szkieletowej usług, w tym ustawieniu trybu aktualizacji klastra.
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
ms.openlocfilehash: 20526c1ddd55671f815dc39b3e03c4f9b2f91788
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Uaktualnić autonomiczne klastra usługi sieć szkieletowa usług Azure w systemie Windows Server 
> [!div class="op_single_selector"]
> * [Klastrze platformy Azure](service-fabric-cluster-upgrade.md)
> * [Klaster autonomiczny](service-fabric-cluster-upgrade-windows-server.md)
>
>

Systemu nowoczesnych możliwość uaktualnienia ma kluczowe znaczenie dla długoterminowym sukcesie produktu. Klastra usługi sieć szkieletowa usług Azure jest zasobem, którego jesteś właścicielem. W tym artykule opisano, jak można upewnić się, że klaster jest zawsze uruchamiany obsługiwane wersje kodu sieci szkieletowej usług i konfiguracji.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Kontrola wersji platformy Service Fabric, uruchomionego w klastrze
Aby skonfigurować klaster, aby pobrać aktualizacje sieci szkieletowej usług, gdy firma Microsoft publikuje nową wersję, ustaw dla konfiguracji klastra fabricClusterAutoupgradeEnabled *true*. Aby wybrać obsługiwanej wersji platformy Service Fabric ma w klastrze, ustaw dla konfiguracji klastra fabricClusterAutoupgradeEnabled *false*.

> [!NOTE]
> Upewnij się, że klaster zawsze działa obsługiwana wersja usługi Service Fabric. Microsoft ogłasza wydaniu nowej wersji platformy Service Fabric, poprzednia wersja jest oznaczony w celu obsługi po co najmniej 60 dni od daty anonsu. Nowe wersje są ogłaszane [na blogu zespołu usługi sieć szkieletowa](https://blogs.msdn.microsoft.com/azureservicefabric/). Nową wersję jest dostępna do wyboru w tym momencie.
>
>

Klaster można uaktualnić do nowej wersji, tylko wtedy, gdy jest użycie konfiguracji węzła stylu produkcji, gdzie każdy węzeł sieci szkieletowej usług jest przydzielane w oddzielnych maszyny fizycznej lub wirtualnej. Jeśli masz klaster programowanie, gdzie więcej niż jeden węzeł sieci szkieletowej usług znajduje się na jednej maszynie fizycznej lub wirtualnej, należy ponownie utworzyć klaster przy użyciu nowej wersji.

Dwa różne przepływów pracy można uaktualnić klaster do najnowszej wersji lub z obsługiwanej wersji platformy Service Fabric. Jeden przepływ pracy jest przeznaczony dla klastrów, które mają łączność z automatycznie pobrać najnowszą wersję. Przepływ pracy jest klastrów, które nie mają łączność z Pobierz najnowszą wersję usługi sieć szkieletowa usług.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Uaktualnij klastrów, które mają łączność z pobrać najnowsze kod i Konfiguracja
Wykonaj następujące kroki, aby uaktualnić klaster do obsługiwanej wersji Jeśli węzły klastra ma połączenia internetowego [Microsoft Download Center](http://download.microsoft.com).

W przypadku klastrów, które mają łączność z [Microsoft Download Center](http://download.microsoft.com), Microsoft okresowo sprawdza dostępność nowych wersji platformy Service Fabric.

Gdy dostępna jest nowa wersja usługi Service Fabric, pakiet jest pobierana lokalnie do klastra i udostępnione do uaktualnienia. Ponadto aby powiadomić klienta o tej nowej wersji, systemu zawiera ostrzeżenia kondycji jawne klastra, która jest podobny do następującego:

"Klastra w bieżącej wersji [wersja #] [date] kończy się pomocy technicznej."

Po uruchomieniu klastra najnowszej wersji ostrzeżenia zniknie.

#### <a name="cluster-upgrade-workflow"></a>Przepływ pracy uaktualniania klastra
Gdy pojawi się ostrzeżenie kondycji klastra, wykonaj następujące czynności:

1. Połącz się z klastrem z dowolnej maszyny, które ma dostęp administratora do wszystkich komputerów, które są wyświetlane jako węzły w klastrze. Ten skrypt uruchamiany na komputerze nie musi być częścią klastra.

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

2. Pobierz listę wersji platformy Service Fabric, które można uaktualnić.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Należy pobrać dane wyjściowe podobne do poniższego:

    ![Pobieranie wersji sieci szkieletowej usług][getfabversions]
3. Uruchom Uaktualnianie klastra, do wersji dostępnych przy użyciu [Start ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx) polecenia programu Windows PowerShell.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Aby monitorować postęp uaktualnienia, możesz użyć Eksploratora usługi sieć szkieletowa lub uruchom następujące polecenie programu PowerShell:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Aby określić zasady dotyczące kondycji niestandardowe polecenia Start-ServiceFabricClusterUpgrade, zobacz dokumentację dla [Start ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Po rozwiązaniu problemów, które spowodowało wycofywanie, należy ponownie zainicjować aktualizację, wykonując te same kroki w sposób opisany wcześniej.

### <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Uaktualnij klastrów, które mają *bez łączności* pobrać najnowsze kod i Konfiguracja
Wykonaj następujące kroki, aby uaktualnić klaster do obsługiwanej wersji węzły klastra braku łączności z Internetem [Microsoft Download Center](http://download.microsoft.com).

> [!NOTE]
> Jeśli korzystasz z klastra, który nie jest połączony z Internetem, należy monitorować blog zespołu usługi Service Fabric, aby dowiedzieć się więcej o nowych wersjach. System nie wyświetla ostrzeżenie kondycji klastra do informowania o nowych wersji.  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatyczne inicjowanie obsługi administracyjnej, a ręcznego inicjowania obsługi administracyjnej
Aby włączyć automatyczne pobieranie i rejestracji dla najnowszej wersji kodu, należy skonfigurować usług aktualizacji sieci szkieletowej usług. Aby uzyskać instrukcje, zobacz Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt wewnątrz [pakiet autonomiczny](service-fabric-cluster-standalone-package-contents.md).
Aby ręczny proces wykonaj te instrukcje.

Zmodyfikuj konfigurację klastra, aby ustawić następującej właściwości *false* przed rozpoczęciem uaktualniania konfiguracji:

        "fabricClusterAutoupgradeEnabled": false,

Szczegóły użycia, zobacz [polecenia programu PowerShell Start-ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/library/mt788302.aspx). Upewnij się, że aktualizacja "clusterConfigurationVersion" w Twojej JSON, przed rozpoczęciem uaktualniania konfiguracji.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Przepływ pracy uaktualniania klastra

1. Uruchom Get ServiceFabricClusterUpgrade w jednym z węzłów w klastrze i zanotuj TargetCodeVersion.

2. Uruchom następujące polecenie z maszynie podłączonej do Internetu, aby wyświetlić listę wszystkich wersji uaktualnienia zgodne z bieżącą wersją i Pobierz odpowiedni pakiet z łączy do pobierania:

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Połącz się z klastrem z dowolnej maszyny, które ma dostęp administratora do wszystkich komputerów, które są wyświetlane jako węzły w klastrze. Ten skrypt uruchamiany na komputerze nie musi być częścią klastra.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. Skopiuj pobrany pakiet do magazynu obrazu klastra.

5. Zarejestruj pakiet skopiowane.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Uruchom Uaktualnianie klastra, do dostępnej wersji.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Można monitorować postępy uaktualniania w narzędziu Service Fabric Explorer lub uruchomić następujące polecenie programu PowerShell:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Aby określić zasady dotyczące kondycji niestandardowe polecenia Start-ServiceFabricClusterUpgrade, zajrzyj do dokumentacji [Start ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Po rozwiązaniu problemów, które spowodowało wycofywanie, należy ponownie zainicjować aktualizację, wykonując te same kroki w sposób opisany wcześniej.


## <a name="upgrade-the-cluster-configuration"></a>Uaktualnij konfigurację klastra
Przed rozpoczęciem uaktualniania konfiguracji nowej konfiguracji klastra JSON można sprawdzić, uruchamiając następujący skrypt programu PowerShell w pakiecie autonomicznych:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
Lub użyj tego skryptu:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

Niektóre konfiguracje nie może zostać uaktualniony, takich jak punkty końcowe, nazwa klastra IP węzła itd. Dla nowej konfiguracji klastra JSON jest testowana starego i zwraca błędy w oknie programu PowerShell, jeśli występuje problem.

Aby uaktualnić uaktualnianie konfiguracji klastra, należy uruchomić Start ServiceFabricClusterConfigurationUpgrade. Uaktualnianie konfiguracji jest przetworzonych domeny uaktualnień w domenie uaktualnienia.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Uaktualnianie konfiguracji certyfikatu klastra  
Certyfikat klastra jest używany do uwierzytelniania między węzłami klastra. Przerzucanie certyfikatów należy wykonać z wyjątkową ostrożność, ponieważ błąd blokuje komunikację między węzłami klastra.

Z technicznego punktu widzenia są obsługiwane cztery opcje:  

* Uaktualnienie jeden certyfikat: Ścieżka uaktualniania prowadzi certyfikatu (podstawowy) -> B certyfikatu (podstawowy) -> C certyfikatu (podstawowy) ->...

* Podwójna certyfikatu uaktualnienia: Ścieżka uaktualniania prowadzi certyfikatu (podstawowy) -> certyfikatu (podstawowy) i B (pomocniczy) -> B certyfikatu (podstawowy) -> B certyfikatu (podstawowy) i C (pomocniczy) -> C certyfikatu (podstawowy) ->...

* Uaktualnianie typu certyfikatu: Konfiguracja certyfikatów na podstawie CommonName configuration <> — na podstawie odcisku palca certyfikatu. Na przykład odcisk palca certyfikatu (podstawowy) i certyfikatów CommonName C. -> B odcisk palca (pomocniczy)

* Uaktualnienie odcisk palca wystawcy certyfikatu: ścieżki uaktualnienia jest CN certyfikatu = A, IssuerThumbprint = IT1 (podstawowe) -> CN certyfikatu = A, IssuerThumbprint = IT1, IT2 (podstawowe) -> CN certyfikatu = A, IssuerThumbprint = IT2 (głównej).


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak dostosowywać niektóre [ustawienia klastra usługi sieć szkieletowa](service-fabric-cluster-fabric-settings.md).
* Dowiedz się, jak [skalowanie klastra i wylogowywanie](service-fabric-cluster-scale-up-down.md).
* Dowiedz się więcej o [uaktualnień aplikacji](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
