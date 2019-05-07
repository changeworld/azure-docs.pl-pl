---
title: Tworzenie autonomicznego klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Tworzenie klastra usługi Azure Service Fabric na dowolnym komputerze (fizyczny lub wirtualny) uruchomiona w systemie Windows Server w środowisku lokalnym lub w dowolnej chmurze.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: ed775bfca2db02b9bfddebb85bbd3f1f668cf3e0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142690"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Tworzenie klastra autonomicznego w systemie Windows Server
Azure Service Fabric umożliwia tworzenie klastrów usługi Service Fabric na wszystkich maszynach wirtualnych lub komputerach z systemem Windows Server. Oznacza to, można wdrożyć i uruchamianie aplikacji usługi Service Fabric w dowolnym środowisku, które zawiera zestaw wzajemnie połączonych komputery serwera Windows, lokalnie lub za pomocą dowolnego dostawcy chmury. Usługa Service Fabric udostępnia pakiet instalacyjny służący do tworzenia klastrów usługi Service Fabric o nazwie pakietu autonomicznego w systemie Windows Server.

W tym artykule przedstawiono procedurę tworzenia klastra autonomicznego usługi Service Fabric.

> [!NOTE]
> Pakiet systemu Windows Server to autonomiczne jest dostępny bezpłatnie i mogą być używane w przypadku wdrożeń produkcyjnych. Ten pakiet może zawierać nowe funkcje usługi Service Fabric, które znajdują się w "Preview". Przewiń w dół do "[funkcje dołączone do tego pakietu w wersji zapoznawczej](#previewfeatures_anchor)." sekcja na liście funkcji w wersji zapoznawczej. Możesz [pobrać kopię tej umowy licencyjnej](https://go.microsoft.com/fwlink/?LinkID=733084) teraz.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Uzyskaj pomoc techniczną dla pakietu programu Service Fabric dla systemu Windows Server
* Zapytaj społeczność o pakietu autonomicznego usługi Service Fabric dla systemu Windows Server w [forum usługi Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Otwórz bilet dla [profesjonalnej pomocy dla usługi Service Fabric](https://support.microsoft.com/oas/default.aspx?prid=16146).  Dowiedz się więcej o profesjonalnej pomocy technicznej firmy Microsoft [tutaj](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Można również uzyskać pomoc techniczną dla tego pakietu, jako część [pomocy technicznej Microsoft Premier](https://support.microsoft.com/en-us/premier).
* Aby uzyskać więcej informacji, zobacz [opcje pomocy technicznej usługi Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Do zbierania dzienników dla celów pomocy technicznej, należy uruchomić [usługi Service Fabric autonomiczny moduł zbierający dzienniki](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Pobieranie pakietu usługi Service Fabric dla systemu Windows Server
Aby utworzyć klaster, należy użyć pakietu Service Fabric dla systemu Windows Server (Windows Server 2012 R2 i nowszych) znaleźć tutaj: <br>
[Pobierz Link - pakietu autonomicznego usługi Service Fabric — system Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Szczegółowe informacje można znaleźć na zawartość pakietu [tutaj](service-fabric-cluster-standalone-package-contents.md).

Od pakietu środowiska uruchomieniowego usługi Service Fabric jest automatycznie pobierana podczas tworzenia klastra. Jeśli wdrażana na komputerze nie jest połączony z Internetem, w tym miejscu pobrać od pakietu środowiska uruchomieniowego poza pasmem: <br>
[Pobierz Link — środowisko uruchomieniowe usługi Service Fabric — system Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Znajdź przykłady konfiguracji klastra autonomicznego na: <br>
[Przykłady konfiguracji klastra autonomicznego](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Tworzenie klastra
W ramach pakietu instalacyjnego jest instalowanych kilka przykładowych plików konfiguracji klastra. *ClusterConfig.Unsecure.DevCluster.json* to najprostsza konfiguracja klastra: niezabezpieczony klaster z trzema węzłami działającymi na pojedynczym komputerze.  Inne pliki konfiguracji zawierają opis klastrów dla jednej lub wielu maszyn, zabezpieczonych za pomocą certyfikatów X.509 lub zabezpieczeń systemu Windows.  Nie musisz modyfikować żadnych domyślnych ustawień konfiguracji w tym samouczku, ale przyjrzyj się plikowi konfiguracji i zapoznaj się z ustawieniami.  Sekcja **węzły** opisuje trzy węzły w klastrze: nazwę, adres IP, [typ węzła, domenę błędów i domenę uaktualnień](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  Sekcja **właściwości** definiuje [zabezpieczenia, poziom niezawodności, zbieranie diagnostyki i typy węzłów](service-fabric-cluster-manifest.md#cluster-properties) klastra.

Klaster utworzony w tym artykule jest niebezpieczne.  Każda osoba może połączyć się z nim anonimowo i przeprowadzić operacje związane z zarządzaniem. Dlatego też klastry produkcyjne należy zawsze zabezpieczać przy użyciu certyfikatów X.509 lub zabezpieczeń systemu Windows.  Zabezpieczenia są konfigurowane tylko w momencie tworzenia klastra. Nie można włączyć zabezpieczeń później, gdy klaster jest już utworzony. Aktualizacja pliku konfiguracji, Włącz [certyfikatów zabezpieczeń](service-fabric-windows-cluster-x509-security.md) lub [zabezpieczeń Windows](service-fabric-windows-cluster-windows-security.md). Dalsze informacje na temat zabezpieczeń klastra usługi Service Fabric można znaleźć w temacie [Secure a cluster](service-fabric-cluster-security.md) (Zabezpieczanie klastra).

### <a name="step-1-create-the-cluster"></a>Krok 1: Tworzenie klastra

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Scenariusz A: Utwórz niezabezpieczony lokalnego klastra projektowego
Usługa Service Fabric można wdrożyć klaster programowania z jednego komputera przy użyciu *ClusterConfig.Unsecure.DevCluster.json* plik dołączony [przykłady](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Rozpakowywanie pakietu autonomicznego na komputerze, skopiuj przykładowy plik konfiguracji na komputer lokalny, a następnie uruchom *CreateServiceFabricCluster.ps1* skryptu za pomocą sesji programu PowerShell administratora z folderu pakietu autonomicznego .

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Zobacz sekcję Konfiguracja środowiska na [planowanie i przygotowanie wdrożenia programu klastra](service-fabric-cluster-standalone-deployment-preparation.md) Aby uzyskać szczegółowe informacje o rozwiązywaniu.

Zakończeniu uruchamianie scenariuszy programowania można usunąć klastra usługi Service Fabric na komputerze, korzystając z procedury opisanej w sekcji "[usuwania klastra](#removecluster_anchor)". 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Scenariusz B: Tworzenie klastra dla wielu maszyn
Po przejściu planowania i przygotowania kroki szczegółowe w [planowanie i przygotowanie wdrożenia programu klastra](service-fabric-cluster-standalone-deployment-preparation.md), możesz przystąpić do tworzenia klastra produkcyjnego przy użyciu pliku konfiguracji klastra.

Administrator klastra wdrażający i konfigurujący klaster musi mieć uprawnienia administratora na komputerze. Usługi Service Fabric nie można zainstalować na kontrolerze domeny.

1. Skrypt *TestConfiguration.ps1* w pakiecie autonomicznym jest używany jako analizator najlepszych rozwiązań, który weryfikuje, czy można wdrożyć klaster w danym środowisku. Listę wymagań wstępnych i wymagań dotyczących środowiska można znaleźć w temacie [Deployment preparation](service-fabric-cluster-standalone-deployment-preparation.md) (Przygotowywanie środowiska). Uruchom skrypt, aby zweryfikować, czy można utworzyć klaster programowania:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Powinny pojawić się dane wyjściowe podobne do następujących: Jeśli pole dolnej "Zakończono pomyślnie" jest zwracana jako "Prawda", wykonuje sprawdzenie powiodło się i klaster wydaje się być możliwy do wdrożenia na podstawie danych wejściowych konfiguracji.

    ```powershell
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

2. Utwórz klaster:  Uruchom *CreateServiceFabricCluster.ps1* skrypt, aby wdrożyć klaster usługi Service Fabric w każdej maszynie w konfiguracji. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Ślady wdrożenia są zapisywane na maszynie lub maszynie wirtualnej, na której uruchomiono skrypt programu PowerShell CreateServiceFabricCluster.ps1. Znajdują się w podfolderze DeploymentTraces w katalogu, w którym uruchomiono skrypt. Aby sprawdzić, czy usługa Service Fabric została prawidłowo wdrożona na maszynie, poszukaj zainstalowanych plików w katalogu FabricDataRoot, zgodnie z sekcją FabricSettings w pliku konfiguracji klastra (domyślnie c:\ProgramData\SF). Ponadto w Menedżerze zadań możesz zobaczyć uruchomione procesy FabricHost.exe i Fabric.exe.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Scenariusz C: Tworzenie klastra w trybie offline (odłączony od Internetu)
Od pakietu środowiska uruchomieniowego usługi Service Fabric jest automatycznie pobierana podczas tworzenia klastra. W przypadku wdrażania klastra na maszynach, które nie jest połączony z Internetem, należy pobrać oddzielnie od pakietu środowiska uruchomieniowego usługi Service Fabric i podaj ścieżkę do niego podczas tworzenia klastra.
Od pakietu środowiska uruchomieniowego można je pobrać oddzielnie, z innego komputera połączonego z Internetem w [łącze Pobierz — środowisko uruchomieniowe usługi Service Fabric — system Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Skopiuj pakiet środowiska uruchomieniowego, do której są wdrażane do klastra w trybie offline z, a następnie utworzyć klaster, uruchamiając `CreateServiceFabricCluster.ps1` z `-FabricRuntimePackagePath` parametru, jak pokazano w poniższym przykładzie: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.JSON* i *.\MicrosoftAzureServiceFabric.cab* są odpowiednio ścieżki do konfiguracji klastra i plik cab środowiska uruchomieniowego.

### <a name="step-2-connect-to-the-cluster"></a>Krok 2: Łączenie z klastrem
Łączenie z klastrem, aby sprawdzić, czy klaster jest uruchomione i dostępne. Moduł ServiceFabric programu PowerShell został zainstalowany w środowisku uruchomieniowym.  Możesz połączyć się z klastrem w jednym z węzłów klastra lub z komputera zdalnego ze środowiskiem uruchomieniowym usługi Service Fabric.  Polecenie cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) umożliwia ustanowienie połączenia z klastrem.

Aby połączyć się niezabezpieczonym klastrem, uruchom następujące polecenie programu PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Na przykład:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```

Inne przykłady łączenia z klastrem można znaleźć w temacie [Nawiązywanie połączenia z zabezpieczonym klastrem](service-fabric-connect-to-secure-cluster.md). Po nawiązaniu połączenia z klastrem użyj polecenia cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps), aby wyświetlić listę węzłów w klastrze oraz informacje o stanie każdego węzła. Element **HealthState** powinien mieć wartość *OK* dla każdego węzła.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Krok 3: Wizualizowanie klastra za pomocą narzędzia Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) to odpowiednie narzędzie do wizualizowania klastra i zarządzania aplikacjami.  Service Fabric Explorer to usługa, która działa w klastrze otwieranym w przeglądarce, przechodząc do [ http://localhost:19080/Explorer ](http://localhost:19080/Explorer).

Pulpit nawigacyjny klastra zawiera omówienie klastra, w tym podsumowanie kondycji węzła i aplikacji. Widok węzła przedstawia fizyczny układ klastra. Dla danego węzła można sprawdzić, które aplikacje mają kod wdrożony w tym węźle.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Dodawanie i usuwanie węzłów
Możesz dodawać i usuwać węzły w klastrze autonomicznym usługi Service Fabric stosownie do zmieniających się potrzeb biznesowych. Aby uzyskać szczegółowe instrukcje, zobacz [Add or Remove nodes to a Service Fabric standalone cluster (Dodawanie i usuwanie węzłów w klastrze autonomicznym usługi Service Fabric)](service-fabric-cluster-windows-server-add-remove-nodes.md).

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Usuwanie klastra
Aby usunąć klaster, uruchom skrypt *RemoveServiceFabricCluster.ps1* programu PowerShell z folderu pakietu i przekaż ścieżkę do pliku konfiguracji JSON. Możesz opcjonalne określić lokalizację dziennika usuwania.

Ten skrypt można uruchomić na dowolnym komputerze, który ma dostęp administratora do wszystkich maszyn, które są wymienione jako węzły w pliku konfiguracji klastra. Komputer, na którym uruchamiany jest skrypt nie musi być częścią klastra.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Dane telemetryczne zbierane i jak zrezygnować z niego
Domyślnie produktu gromadzi dane telemetryczne użycia usługi Service Fabric w ulepszaniu produktu. Analizator najlepszych rozwiązań, która działa jako część instalacji sprawdza, czy łączność [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1). Jeśli nie jest dostępny, instalacja kończy się niepowodzeniem, jeśli nie możesz zrezygnować z danych telemetrycznych.

1. Potok danych telemetrycznych spróbuje przekazać następujące dane [ https://vortex.data.microsoft.com/collect/v1 ](https://vortex.data.microsoft.com/collect/v1) jeden raz każdego dnia. Jest przekazywanie największej staranności i nie ma wpływu na funkcjonalność klastra. Dane telemetryczne są zgodne z węzła, który jest uruchamiany w trybie failover Menedżerze podstawowym. Nie inne węzły Wyślij dane telemetryczne.
2. Dane telemetryczne składa się z następujących czynności:

* Liczba usług
* Liczba ServiceTypes
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
* Adres IP maszyny wirtualnej lub maszyny, z którego został przekazany telemetrii

Aby wyłączyć telemetrię, Dodaj następujący kod do *właściwości* w pliku config klastra: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Dołączone do tego pakietu w wersji zapoznawczej
Brak.


> [!NOTE]
> Uruchamianie przy użyciu nowego [wersją GA usługi klastra autonomicznego w systemie Windows Server (wersja 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), klaster można uaktualnić do kolejnych wersji ręcznie lub automatycznie. Zapoznaj się [uaktualnienie wersji autonomicznego klastra usługi Service Fabric](service-fabric-cluster-upgrade-windows-server.md) dokumentu, aby uzyskać szczegółowe informacje.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Wdrażanie i usunąć aplikacje przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md)
* [Ustawienia konfiguracji dla autonomicznego klastra Windows](service-fabric-cluster-manifest.md)
* [Dodawanie lub usuwanie węzłów do klastra usługi Service Fabric autonomicznego](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Uaktualnienie wersji autonomicznego klastra usługi Service Fabric](service-fabric-cluster-upgrade-windows-server.md)
* [Tworzenie klastra autonomicznego usługi Service Fabric z systemem Windows maszyn wirtualnych platformy Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Zabezpieczanie klastra autonomicznego w Windows przy użyciu zabezpieczeń Windows](service-fabric-windows-cluster-windows-security.md)
* [Zabezpieczanie klastra autonomicznego w Windows przy użyciu X509 certyfikatów](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
