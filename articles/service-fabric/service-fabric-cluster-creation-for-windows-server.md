---
title: Tworzenie autonomicznego klastra sieci szkieletowej usług Azure
description: Utwórz klaster sieci szkieletowej usług Azure na dowolnym komputerze (fizycznym lub wirtualnym) z systemem Windows Server, niezależnie od tego, czy jest on lokalny, czy w dowolnej chmurze.
author: dkkapur
ms.topic: conceptual
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 461d6021a201ca1fa5722bb44c427baca2a7728e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258826"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Tworzenie klastra autonomicznego działającego w systemie Windows Server
Za pomocą usługi Azure Service Fabric można tworzyć klastry sieci szkieletowej usług na wszystkich maszynach wirtualnych lub komputerach z systemem Windows Server. Oznacza to, że można wdrażać i uruchamiać aplikacje sieci szkieletowej usług w dowolnym środowisku, które zawiera zestaw połączonych ze sobą komputerów z systemem Windows Server, czy to lokalnie, czy z dowolnym dostawcą chmury. Sieć szkieletowa usług udostępnia pakiet instalacyjny do tworzenia klastrów sieci szkieletowej usług o nazwie autonomiczny pakiet Windows Server. Tradycyjne klastry sieci szkieletowej usług na platformie Azure są dostępne jako usługa zarządzana, podczas gdy autonomiczne klastry sieci szkieletowej usług są samoobsługowe. Aby uzyskać więcej informacji na temat różnic, zobacz [Porównywanie platformy Azure i autonomicznych klastrów sieci szkieletowej usług.](./service-fabric-deploy-anywhere.md)

W tym artykule oszukuje się kroki tworzenia klastra autonomicznej sieci szkieletowej usług.

> [!NOTE]
> Ten autonomiczny pakiet systemu Windows Server jest dostępny na rynku bezpłatnie i może być używany do wdrożeń produkcyjnych. Ten pakiet może zawierać nowe funkcje sieci szkieletowej usług, które są w "Podgląd". Przewiń w dół do "[Podgląd funkcji zawartych w tym pakiecie](#previewfeatures_anchor)." listę funkcji podglądu. Możesz [pobrać kopię umowy EULA już](https://go.microsoft.com/fwlink/?LinkID=733084) teraz.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Uzyskaj pomoc techniczną dla pakietu Sieci szkieletowej usług dla systemu Windows Server
* Zapytaj społeczność o samodzielny pakiet sieci szkieletowej usług dla systemu Windows Server na [forum sieci szkieletowej usług Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Otwórz bilet do [profesjonalnej pomocy technicznej dla sieci szkieletowej usług](https://support.microsoft.com/oas/default.aspx?prid=16146).  Dowiedz się więcej o profesjonalnej pomocy technicznej firmy Microsoft [tutaj](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Możesz również uzyskać pomoc techniczną dla tego pakietu w ramach [pomocy technicznej Microsoft Premier](https://support.microsoft.com/en-us/premier).
* Aby uzyskać więcej informacji, zobacz [opcje pomocy technicznej usługi Azure Service Fabric.](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)
* Aby zebrać dzienniki do celów pomocy technicznej, uruchom [moduł zbierający dzienniki autonomiczne sieci szkieletowej usług](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Pobieranie pakietu usługi Service Fabric dla systemu Windows Server
Aby utworzyć klaster, użyj pakietu Sieć szkieletowa usług dla systemu Windows Server (Windows Server 2012 R2 i nowsza) znajdującego się tutaj: <br>
[Link do pobrania — Pakiet autonomiczny sieci szkieletowej usług — Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Szczegółowe informacje na temat zawartości opakowania można [znaleźć tutaj](service-fabric-cluster-standalone-package-contents.md).

Pakiet środowiska uruchomieniowego sieci szkieletowej usług jest automatycznie pobierany w momencie tworzenia klastra. W przypadku wdrażania z komputera, który nie jest podłączony do Internetu, pobierz pakiet środowiska wykonawczego z pasma z tego miejsca: <br>
[Link do pobrania — Środowisko uruchomieniowe sieci szkieletowej usług — Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Znajdź przykłady konfiguracji klastra autonomicznego pod adresem: <br>
[Przykłady konfiguracji autonomicznego klastra](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Tworzenie klastra
W ramach pakietu instalacyjnego jest instalowanych kilka przykładowych plików konfiguracji klastra. *ClusterConfig.Unsecure.DevCluster.json* to najprostsza konfiguracja klastra: niezabezpieczony klaster z trzema węzłami działającymi na pojedynczym komputerze.  Inne pliki konfiguracji zawierają opis klastrów dla jednej lub wielu maszyn, zabezpieczonych za pomocą certyfikatów X.509 lub zabezpieczeń systemu Windows.  Nie musisz modyfikować żadnych domyślnych ustawień konfiguracji w tym samouczku, ale przyjrzyj się plikowi konfiguracji i zapoznaj się z ustawieniami.  Sekcja **węzły** opisuje trzy węzły w klastrze: nazwę, adres IP, [typ węzła, domenę błędów i domenę uaktualnień](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  Sekcja **właściwości** definiuje [zabezpieczenia, poziom niezawodności, zbieranie diagnostyki i typy węzłów](service-fabric-cluster-manifest.md#cluster-properties) klastra.

Klaster utworzony w tym artykule jest niezabezpieczony.  Każda osoba może połączyć się z nim anonimowo i przeprowadzić operacje związane z zarządzaniem. Dlatego też klastry produkcyjne należy zawsze zabezpieczać przy użyciu certyfikatów X.509 lub zabezpieczeń systemu Windows.  Zabezpieczenia są konfigurowane tylko w momencie tworzenia klastra. Nie można włączyć zabezpieczeń później, gdy klaster jest już utworzony. Aktualizacja pliku konfiguracyjnego umożliwia [zabezpieczenie certyfikatu](service-fabric-windows-cluster-x509-security.md) lub [zabezpieczenia systemu Windows](service-fabric-windows-cluster-windows-security.md). Dalsze informacje na temat zabezpieczeń klastra usługi Service Fabric można znaleźć w temacie [Secure a cluster](service-fabric-cluster-security.md) (Zabezpieczanie klastra).

### <a name="step-1-create-the-cluster"></a>Krok 1: Tworzenie klastra

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Scenariusz A: Tworzenie niezabezpieczonego lokalnego klastra programistycznego
Sieci szkieletowej usług można wdrożyć w klastrze deweloperskim z jednym komputerze za pomocą pliku *ClusterConfig.Unsecure.DevCluster.json* zawartego w [przykładach](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Rozpakuj autonomiczny pakiet do komputera, skopiuj przykładowy plik konfiguracyjny do komputera lokalnego, a następnie uruchom skrypt *CreateServiceFabricCluster.ps1* za pośrednictwem sesji programu PowerShell administratora z autonomicznego folderu pakietu.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Zobacz sekcję Ustawienia środowiska w [witrynie Planowanie i przygotuj wdrożenie klastra,](service-fabric-cluster-standalone-deployment-preparation.md) aby uzyskać szczegółowe informacje na temat rozwiązywania problemów.

Po zakończeniu uruchamiania scenariuszy programistycznych można usunąć klaster sieci szkieletowej usług z komputera, odwołując się do kroków w sekcji "[Usuń klaster](#removecluster_anchor)". 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Scenariusz B: Tworzenie klastra wielokołowego
Po przejściu przez kroki planowania i przygotowania szczegółowe w [planowaniu i przygotowaniu wdrożenia klastra](service-fabric-cluster-standalone-deployment-preparation.md)można przystąpić do tworzenia klastra produkcyjnego przy użyciu pliku konfiguracji klastra.

Administrator klastra wdrażający i konfigurujący klaster musi mieć uprawnienia administratora na komputerze. Usługi Service Fabric nie można zainstalować na kontrolerze domeny.

1. Skrypt *TestConfiguration.ps1* w pakiecie autonomicznym jest używany jako analizator najlepszych rozwiązań, który weryfikuje, czy można wdrożyć klaster w danym środowisku. Listę wymagań wstępnych i wymagań dotyczących środowiska można znaleźć w temacie [Deployment preparation](service-fabric-cluster-standalone-deployment-preparation.md) (Przygotowywanie środowiska). Uruchom skrypt, aby zweryfikować, czy można utworzyć klaster programowania:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Powinny pojawić się dane wyjściowe podobne do następujących: Jeśli dolne pole "Przekazane" jest zwracane jako "True", testy poczytalności zostały przekazane i klaster wydaje się być wdrażalny na podstawie konfiguracji wejściowej.

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

2. Utwórz klaster: Uruchom skrypt *CreateServiceFabricCluster.ps1,* aby wdrożyć klaster sieci szkieletowej usług na każdym komputerze w konfiguracji. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Ślady wdrożenia są zapisywane na maszynie lub maszynie wirtualnej, na której uruchomiono skrypt programu PowerShell CreateServiceFabricCluster.ps1. Znajdują się w podfolderze DeploymentTraces w katalogu, w którym uruchomiono skrypt. Aby sprawdzić, czy usługa Service Fabric została prawidłowo wdrożona na maszynie, poszukaj zainstalowanych plików w katalogu FabricDataRoot, zgodnie z sekcją FabricSettings w pliku konfiguracji klastra (domyślnie c:\ProgramData\SF). Ponadto w Menedżerze zadań możesz zobaczyć uruchomione procesy FabricHost.exe i Fabric.exe.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Scenariusz C: Tworzenie klastra w trybie offline (rozłączony z Internetem)
Pakiet środowiska uruchomieniowego sieci szkieletowej usług jest automatycznie pobierany podczas tworzenia klastra. Podczas wdrażania klastra na komputerach niepodłączonych do Internetu należy pobrać pakiet środowiska uruchomieniowego sieci szkieletowej usług oddzielnie i podać ścieżkę do niego podczas tworzenia klastra.
Pakiet środowiska wykonawczego można pobrać oddzielnie, z innego komputera podłączonego do Internetu, w [Download Link - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Skopiuj pakiet środowiska wykonawczego do miejsca, z którego wdrażasz klaster w trybie offline, i utwórz klaster, uruchamiając `CreateServiceFabricCluster.ps1` go z dołączonym `-FabricRuntimePackagePath` parametrem, jak pokazano w tym przykładzie: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.json* i *.\MicrosoftAzureServiceFabric.cab* są ścieżkami odpowiednio do konfiguracji klastra i pliku cab środowiska wykonawczego.

### <a name="step-2-connect-to-the-cluster"></a>Krok 2: Łączenie się z klastrem
Połącz się z klastrem, aby sprawdzić, czy klaster jest uruchomiony i dostępny. Moduł ServiceFabric programu PowerShell został zainstalowany w środowisku uruchomieniowym.  Można połączyć się z klastrem z jednego z węzłów klastra lub z komputera zdalnego za pomocą środowiska uruchomieniowego sieci szkieletowej usług.  Polecenie cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) umożliwia ustanowienie połączenia z klastrem.

Aby połączyć się z niezabezpieczonym klastrem, uruchom następujące polecenie programu PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Przykład:
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

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Krok 3: Wizualizacja klastra przy użyciu Eksploratora sieci szkieletowej usług
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) to odpowiednie narzędzie do wizualizowania klastra i zarządzania aplikacjami.  Eksplorator sieci szkieletowej usług to usługa uruchamiana w klastrze, do której uzyskujesz dostęp za pomocą przeglądarki, przechodząc do pliku [http://localhost:19080/Explorer](http://localhost:19080/Explorer).

Pulpit nawigacyjny klastra zawiera omówienie klastra, w tym podsumowanie kondycji węzła i aplikacji. Widok węzła przedstawia fizyczny układ klastra. Dla danego węzła można sprawdzić, które aplikacje mają kod wdrożony w tym węźle.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Dodawanie i usuwanie węzłów
Możesz dodawać i usuwać węzły w klastrze autonomicznym usługi Service Fabric stosownie do zmieniających się potrzeb biznesowych. Aby uzyskać szczegółowe instrukcje, zobacz [Add or Remove nodes to a Service Fabric standalone cluster (Dodawanie i usuwanie węzłów w klastrze autonomicznym usługi Service Fabric)](service-fabric-cluster-windows-server-add-remove-nodes.md).

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Usuwanie klastra
Aby usunąć klaster, uruchom skrypt *RemoveServiceFabricCluster.ps1* programu PowerShell z folderu pakietu i przekaż ścieżkę do pliku konfiguracji JSON. Możesz opcjonalne określić lokalizację dziennika usuwania.

Ten skrypt można uruchomić na dowolnym komputerze, który ma dostęp administratora do wszystkich maszyn, które są wymienione jako węzły w pliku konfiguracji klastra. Komputer, na który jest uruchamiany ten skrypt, nie musi być częścią klastra.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Zebrane dane telemetryczne i jak zrezygnować z nich
Domyślnie produkt zbiera dane telemetryczne dotyczące użycia sieci szkieletowej usług w celu ulepszenia produktu. Analizator najlepszych praktyk, który działa w ramach instalacji, sprawdza łączność z programem [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Jeśli nie jest osiągalny, konfiguracja kończy się niepowodzeniem, chyba że zrezygnujesz z telemetrii.

1. Potok telemetrii próbuje przekazać [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) następujące dane do raz dziennie. Jest to przekazywanie najlepszych starań i nie ma wpływu na funkcje klastra. Dane telemetryczne są wysyłane tylko z węzła, który uruchamia menedżera trybu failover podstawowego. Żadne inne węzły nie wysyłają danych telemetrycznych.
2. Dane telemetryczne składają się z następujących elementów:

* Liczba usług
* Liczba typów usług
* Liczba wniosków
* Liczba aktualizacji aplikacji
* Liczba jednostek trybu failover
* Liczba jednostek InBuildFailoverUnits
* Liczba jednostek niezdrowych
* Liczba replik
* Liczba InBuildReplicas
* Liczba wstrzymanychreplicas
* Liczba offlineReplicas
* Pospolitykła Długość wyżu
* Wyszukiwanie w kolejce zapytania
* Funkcja failoverUnitQueueLength
* CommitQueueLength
* Liczba węzłów
* IsContextComplete: Prawda/Fałsz
* ClusterId: Jest to identyfikator GUID generowany losowo dla każdego klastra
* ServiceFabricVersion (Wersja serwisowa)
* Adres IP maszyny wirtualnej lub maszyny, z której są przekazywane dane telemetryczne

Aby wyłączyć dane telemetryczne, dodaj następujące *właściwości* w konfiguracji klastra: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Funkcje w wersji zapoznawczej zawarte w tym pakiecie
Brak.


> [!NOTE]
> Począwszy od nowej [wersji ga autonomicznego klastra dla systemu Windows Server (wersja 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)można uaktualnić klaster do przyszłych wersji, ręcznie lub automatycznie. Szczegółowe informacje można znaleźć w dokumencie [uaktualniania autonomicznej wersji klastra sieci szkieletowej usług.](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md)
* [Ustawienia konfiguracji autonomicznego klastra systemu Windows](service-fabric-cluster-manifest.md)
* [Dodawanie lub usuwanie węzłów do autonomicznego klastra sieci szkieletowej usług](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Uaktualnianie autonomicznej wersji klastra sieci szkieletowej usług](service-fabric-cluster-upgrade-windows-server.md)
* [Tworzenie autonomicznego klastra sieci szkieletowej usług z maszynami wirtualnymi platformy Azure z systemem Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Zabezpieczanie autonomicznego klastra w systemie Windows przy użyciu zabezpieczeń systemu Windows](service-fabric-windows-cluster-windows-security.md)
* [Zabezpieczanie autonomicznego klastra w systemie Windows przy użyciu certyfikatów X509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
