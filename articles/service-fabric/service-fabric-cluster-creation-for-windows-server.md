---
title: Tworzenie klastra sieci szkieletowej usług Azure autonomiczny | Dokumentacja firmy Microsoft
description: Tworzenie klastra usługi sieć szkieletowa usług Azure na dowolnym komputerze (fizycznych lub wirtualnych) systemem Windows Server, czy jest lokalnie lub w dowolnym chmury.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: dekapur
ms.openlocfilehash: efa48aa90806b45c99237404af24cb8aba762d15
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209158"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Tworzenie autonomicznych klastra w systemie Windows Server
Sieć szkieletowa usług Azure służy do tworzenia klastrów sieci szkieletowej usług na dowolnym maszyn wirtualnych lub komputerach z systemem Windows Server. Oznacza to, można wdrażać i uruchamianie aplikacji platformy Service Fabric w dowolnym środowisku, które zawiera zestaw połączonych komputerów z systemem Windows Server, lokalnie i z każdego dostawcy chmury. Usługa Service Fabric realizuje pakietu instalacyjnego do tworzenia klastrów sieci szkieletowej usług o nazwie autonomicznych pakietu systemu Windows Server.

W tym artykule przedstawiono kroki tworzenia klastra usługi sieć szkieletowa autonomicznych.

> [!NOTE]
> Ten pakiet Windows Server autonomiczny jest dostępnych na rynku, może być używany we wdrożeniach produkcyjnych. Ten pakiet może zawierać nowe funkcje sieci szkieletowej usług, które znajdują się w "W wersji zapoznawczej". Przewiń w dół do "[Podgląd funkcje zawarte w tym pakiecie](#previewfeatures_anchor)." sekcja listę funkcji w wersji zapoznawczej. Możesz [Pobierz kopię umowy licencyjnej](http://go.microsoft.com/fwlink/?LinkID=733084) teraz.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Uzyskać pomoc techniczną dla pakietu sieci szkieletowej usług dla systemu Windows Server
* Poproś społeczność o pakiecie autonomicznej usługi sieć szkieletowa usług dla systemu Windows Server w [forum usługi Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Otwórz bilet dla [Professional obsługę sieci szkieletowej usług](http://support.microsoft.com/oas/default.aspx?prid=16146).  Dowiedz się więcej na temat pomocy technicznej Professional firmy Microsoft [tutaj](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Można również uzyskać pomoc techniczną dla tego pakietu, jako część [pomocy technicznej Microsoft Premier](https://support.microsoft.com/en-us/premier).
* Aby uzyskać więcej informacji, zobacz [opcje pomocy technicznej usługi Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Do zbierania dzienników dla celów pomocy technicznej, uruchom [logowania do usługi sieci szkieletowej autonomiczny moduł zbierający](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Pobierz pakiet sieci szkieletowej usług dla systemu Windows Server
Aby utworzyć klaster, użyj pakietu sieci szkieletowej usług dla systemu Windows Server (Windows Server 2012 R2 i nowszych) znaleźć tutaj: <br>
[Pobierz Link - pakietu autonomicznego sieci szkieletowej usług - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)

Znajduje szczegóły na zawartość pakietu [tutaj](service-fabric-cluster-standalone-package-contents.md).

Pakiet środowiska uruchomieniowego platformy Service Fabric jest automatycznie pobierana podczas tworzenia klastra. Jeśli wdrażane na komputerze nie jest połączony z Internetem, Pobierz pakiet obsługi poza pasmem w tym miejscu: <br>
[Pobierz łącze — środowisko uruchomieniowe usługi sieć szkieletowa - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Szukaj przykładowych autonomicznej konfiguracji klastra na: <br>
[Przykłady konfiguracji autonomicznej klastra](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Tworzenie klastra
Sieci szkieletowej usług można wdrożyć klaster programowanie jednego komputera przy użyciu *ClusterConfig.Unsecure.DevCluster.json* zawarte w pliku [przykłady](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Rozpakuj pakiet autonomicznego do komputera, skopiuj przykładowy plik konfiguracji na komputerze lokalnym, a następnie uruchom *CreateServiceFabricCluster.ps1* skryptu za pomocą sesji programu PowerShell administrator z folderu pakietu autonomicznego:
### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>Krok 1A: tworzenie niezabezpieczona lokalnego klastra projektowego
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Zobacz sekcję konfigurowania środowiska w [planowanie i przygotowanie wdrożenia klastra](service-fabric-cluster-standalone-deployment-preparation.md) dla szczegóły dotyczące rozwiązywania problemów.

Wszystko uruchomionych scenariusze programowania, można usunąć klastra sieci szkieletowej usług na komputerze, odwołując się do czynności opisane w sekcji "[usunąć klaster](#removecluster_anchor)". 

### <a name="step-1b-create-a-multi-machine-cluster"></a>Krok 1B: tworzenie obsługi wielu komputerów klastra
Po przejściu planowania i przygotowywania szczegółowych w poniżej łącze, można przystąpić do tworzenia klastra produkcyjnego przy użyciu pliku konfiguracji klastra. <br>
[Planowanie i przygotowanie wdrożenia klastra](service-fabric-cluster-standalone-deployment-preparation.md)

1. Sprawdzanie poprawności pliku konfiguracji, które zostały zapisane, uruchamiając *TestConfiguration.ps1* skryptu z folderu pakietu autonomicznego:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Powinny pojawić się dane wyjściowe, takich jak poniżej. Jeśli pole dolnej "Passed" jest zwracana jako "Prawda", związane z poprawnością sprawdzenie powiodło się i klastra wygląda się do wdrożenia na podstawie wejściowych konfiguracji.

    ```
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Utworzenie klastra: Uruchom *CreateServiceFabricCluster.ps1* skrypt, aby wdrożyć klaster sieci szkieletowej usług w każdej maszynie w konfiguracji. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Wdrożenie śledzenia są zapisywane do maszyny Wirtualnej/maszyny, na których uruchomiono skrypt programu CreateServiceFabricCluster.ps1 PowerShell. Te znajdują się w podfolderze DeploymentTraces, na podstawie w katalogu, z którego uruchomiono skrypt. Aby zobaczyć, jeśli sieci szkieletowej usług został poprawnie wdrożony do maszyny, Znajdź zainstalowanych plików w katalogu zmiennej FabricDataRoot, zgodnie z opisem w sekcji FabricSettings pliku konfiguracji klastra (c:\ProgramData\SF domyślną). Jak również procesy FabricHost.exe i Fabric.exe są widoczne w Menedżerze zadań.
> 
> 

### <a name="step-1c-create-an-offline-internet-disconnected-cluster"></a>Krok 1C: Tworzenie klastra w trybie offline (odłączony od Internetu)
Pakiet środowiska uruchomieniowego platformy Service Fabric jest automatycznie pobierana podczas tworzenia klastra. W przypadku wdrażania klastra na komputerach, które nie jest połączony z Internetem, należy pobrać pakiet środowiska uruchomieniowego platformy Service Fabric osobno i podaj ścieżkę do niego podczas tworzenia klastra.
Pakiet środowiska uruchomieniowego można pobrać oddzielnie, z innego komputera połączonego z Internetem w [łącze Pobierz — środowisko uruchomieniowe usługi sieć szkieletowa - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Skopiuj pakiet środowiska uruchomieniowego do której wdrażania klastra w trybie offline z, a następnie utworzyć klaster, uruchamiając `CreateServiceFabricCluster.ps1` z `-FabricRuntimePackagePath` parametru, jak pokazano poniżej: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```
gdzie `.\ClusterConfig.json` i `.\MicrosoftAzureServiceFabric.cab` są odpowiednio ścieżki do konfiguracji klastra i środowiska uruchomieniowego pliku cab.


### <a name="step-2-connect-to-the-cluster"></a>Krok 2: Połącz się z klastrem
Aby nawiązać bezpiecznego klastra, zobacz [usługi sieć szkieletowa nawiązać bezpiecznego klastra](service-fabric-connect-to-secure-cluster.md).

Aby połączyć niezabezpieczone klastra, uruchom następujące polecenie programu PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```
Przykład:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```
### <a name="step-3-bring-up-service-fabric-explorer"></a>Krok 3: Wywołaj okno Eksploratora usługi sieć szkieletowa
Teraz możesz połączyć się z klastrem z Eksploratora usługi sieć szkieletowa bezpośrednio od jednej z maszyn z http://localhost:19080/Explorer/index.html lub zdalnie za pomocą http://<*IPAddressofaMachine*>: 19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Dodawanie i usuwanie węzłów
Można dodać lub usunąć węzły do klastra usługi sieć szkieletowa autonomicznych, jak zmieniających się potrzeb firmy. Zobacz [dodać lub usunąć węzły do klastra usługi sieć szkieletowa autonomiczny](service-fabric-cluster-windows-server-add-remove-nodes.md) szczegółowy opis kroków.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Usuwanie klastra
Aby usunąć klaster, uruchom skrypt *RemoveServiceFabricCluster.ps1* programu PowerShell z folderu pakietu i przekaż ścieżkę do pliku konfiguracji JSON. Możesz opcjonalne określić lokalizację dziennika usuwania.

Ten skrypt można uruchomić na dowolnym komputerze, który ma dostęp administratora do wszystkich komputerów, które są wyświetlane jako węzły w pliku konfiguracji klastra. Ten skrypt uruchamiany na komputerze nie ma być częścią klastra.

```
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Dane telemetryczne zbierane i jak rezygnacji z jej
Domyślnie produktu zbiera dane telemetryczne na użycie usługi sieć szkieletowa ulepszania produktów. Analizator najlepszych rozwiązań, która działa jako część instalacji sprawdza, czy łączność [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1). Jeśli nie jest dostępny, instalacja zakończy się niepowodzeniem, Jeśli zrezygnujesz z telemetrii.

1. Potok telemetrii próbuje przekazać następujące dane [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1) raz każdego dnia. Jest przekazywanie optymalnych i nie ma wpływu na funkcjonalność klastra. Dane telemetryczne są zgodne z węzła, który uruchamia przejście w tryb failover manager podstawowego. Nie inne węzły wysłać dane telemetryczne.
2. Dane telemetryczne składa się z następujących czynności:

* Liczba usług
* Liczba Serivcetype
* Liczba aplikacji
* Liczba ApplicationUpgrades
* Liczba jednostek trybu failover
* Liczba stanie Inbuild
* Liczba UnhealthyFailoverUnits
* Liczba replik
* Liczba InBuildReplicas
* Liczba StandByReplicas
* Liczba OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Liczba węzłów
* IsContextComplete: PRAWDA/FAŁSZ
* ClusterId: Jest to identyfikator GUID generowany losowo dla każdego klastra
* ServiceFabricVersion
* Adres IP maszyny wirtualnej lub maszyny, z którego jest przekazywany telemetrii

Aby wyłączyć telemetrię, Dodaj następujący kod do *właściwości* w pliku config klastra: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Funkcje w wersji zapoznawczej zawarte w tym pakiecie
Brak.


> [!NOTE]
> Począwszy od nowa [wersją GA klastra autonomiczny dla systemu Windows Server (wersja 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), możesz uaktualnić klaster do przyszłych wydaniach ręcznie lub automatycznie. Zapoznaj się [uaktualnienie wersji autonomicznej klastra sieci szkieletowej usług](service-fabric-cluster-upgrade-windows-server.md) dokumentu, aby uzyskać szczegółowe informacje.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Wdrażanie i usunąć aplikacje przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md)
* [Ustawienia konfiguracji dla autonomicznych klastra systemu Windows](service-fabric-cluster-manifest.md)
* [Dodaj lub usuń węzły do klastra usługi sieć szkieletowa autonomiczny](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Uaktualnienie wersji autonomicznej klastra sieci szkieletowej usług](service-fabric-cluster-upgrade-windows-server.md)
* [Tworzenie klastra usługi sieć szkieletowa autonomiczny z systemem Windows maszynach wirtualnych platformy Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Zabezpieczanie klastra autonomicznego w systemie Windows przy użyciu zabezpieczeń systemu Windows](service-fabric-windows-cluster-windows-security.md)
* [Secure autonomiczny klastra w systemie Windows przy użyciu X509 certyfikatów](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
