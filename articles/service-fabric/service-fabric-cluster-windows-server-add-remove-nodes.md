---
title: Dodawanie lub usuwanie węzłów do autonomicznego klastra sieci szkieletowej usług
description: Dowiedz się, jak dodać lub usunąć węzły do klastra sieci szkieletowej usług Azure na komputerze fizycznym lub wirtualnym z systemem Windows Server, który może być lokalny lub w dowolnej chmurze.
author: dkkapur
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: f9bee35ee8e82070b4cf601139b471562ba5e10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934204"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Dodawanie węzłów do autonomicznego klastra usługi Service Fabric uruchomionego na serwerze Windows Server lub usuwanie go
Po [utworzeniu autonomicznego klastra sieci szkieletowej usług na komputerach z systemem Windows Server](service-fabric-cluster-creation-for-windows-server.md)potrzeby (biznesowe) mogą ulec zmianie i trzeba będzie dodać lub usunąć węzły do klastra. Ten artykuł zawiera szczegółowe kroki, aby to osiągnąć. Należy pamiętać, że funkcja dodawania/usuwania węzła nie jest obsługiwana w lokalnych klastrach programistycznych.

## <a name="add-nodes-to-your-cluster"></a>Dodawanie węzłów do klastra

1. Przygotuj maszynę wirtualną/maszynę, którą chcesz dodać do klastra, wykonując kroki opisane w [zaplanuj i przygotuj wdrożenie klastra sieci szkieletowej usług.](service-fabric-cluster-standalone-deployment-preparation.md)

2. Określ, do której domeny błędów i domeny uaktualnienia zamierzasz dodać tę maszynę wirtualną/komputer.

   Jeśli certyfikaty są używane do zabezpieczania klastra, oczekuje się, że certyfikaty zostaną zainstalowane w lokalnych magazynach certyfikatów w ramach przygotowań do przyłączenia węzła do klastra. Analog ma zastosowanie w przypadku korzystania z innych form zabezpieczeń.

3. Pulpit zdalny (RDP) do maszyny wirtualnej/maszyny, którą chcesz dodać do klastra.

4. Skopiuj lub [pobierz autonomiczny pakiet sieci szkieletowej usług dla systemu Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) na maszynę wirtualną/komputer i rozpakuj pakiet.

5. Uruchom program PowerShell z podwyższonymi uprawnieniami i przejdź do lokalizacji rozpakowanego pakietu.

6. Uruchom skrypt *AddNode.ps1* z parametrami opisującymi nowy węzeł do dodania. Poniższy przykład dodaje nowy węzeł o nazwie VM5, o typie NodeType0 i adres IP 182.17.34.52, do UD1 i fd:/dc1/r0. `ExistingClusterConnectionEndPoint`jest punktem końcowym połączenia dla węzła już w istniejącym klastrze, który może być adresem IP *dowolnego* węzła w klastrze. 

   Niezabezpieczone (prototypowanie):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Bezpieczne (oparte na certyfikatach):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   Po zakończeniu pracy skryptu można sprawdzić, czy nowy węzeł został dodany przez uruchomienie polecenia cmdlet [Get-ServiceFabricNode.](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps)

7. Aby zapewnić spójność między różnymi węzłami w klastrze, należy zainicjować uaktualnienie konfiguracji. Uruchom [Get-ServiceFabricClusterConfiguration,](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) aby uzyskać najnowszy plik konfiguracji i dodać nowo dodany węzeł do sekcji "Węzły". Zaleca się również, aby zawsze mieć dostęp do najnowszej konfiguracji klastra w przypadku konieczności ponownego rozmieszczenia klastra, który ma tę samą konfigurację.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. Uruchom [start-ServiceFabricClusterConfigurationUpgrade,](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) aby rozpocząć uaktualnienie.

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   Można monitorować postęp uaktualnienia w Eksploratorze sieci szkieletowej usług. Alternatywnie można uruchomić [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Dodawanie węzłów do klastrów skonfigurowanych za pomocą programu Windows Security przy użyciu gMSA
W przypadku klastrów skonfigurowanych za pomocą konta usługihttps://technet.microsoft.com/library/hh831782.aspx)zarządzanej grupy(gMSA) można dodać nowy węzeł za pomocą uaktualnienia konfiguracji:
1. Uruchom [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) na dowolnym z istniejących węzłów, aby uzyskać najnowszy plik konfiguracji i dodać szczegóły dotyczące nowego węzła, który chcesz dodać w sekcji "Węzły". Upewnij się, że nowy węzeł jest częścią tego samego konta zarządzanego grupy. To konto powinno być administratorem na wszystkich komputerach.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Uruchom [start-ServiceFabricClusterConfigurationUpgrade,](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) aby rozpocząć uaktualnienie.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Można monitorować postęp uaktualnienia w Eksploratorze sieci szkieletowej usług. Alternatywnie można uruchomić [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Dodawanie typów węzłów do klastra
Aby dodać nowy typ węzła, zmodyfikuj konfigurację, aby uwzględnić nowy typ węzła w sekcji "Typy węzłów" w sekcji "Właściwości" i rozpocznij uaktualnienie konfiguracji przy użyciu [funkcji Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). Po zakończeniu uaktualniania można dodać nowe węzły do klastra za pomocą tego typu węzła.

## <a name="remove-nodes-from-your-cluster"></a>Usuwanie węzłów z klastra
Węzeł można usunąć z klastra przy użyciu uaktualnienia konfiguracji, w następujący sposób:

1. Uruchom [Get-ServiceFabricClusterConfiguration,](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) aby uzyskać najnowszy plik konfiguracyjny i *usunąć* węzeł z sekcji "Węzły".
Dodaj parametr "NodesToBeRemoved" do sekcji "Konfiguracja" w sekcji "FabricSettings". "Wartość" powinna być oddzieloną przecinkami listą nazw węzłów węzłów, które muszą zostać usunięte.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. Uruchom [start-ServiceFabricClusterConfigurationUpgrade,](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) aby rozpocząć uaktualnienie.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Można monitorować postęp uaktualnienia w Eksploratorze sieci szkieletowej usług. Alternatywnie można uruchomić [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

> [!NOTE]
> Usunięcie węzłów może zainicjować wiele uaktualnień. Niektóre węzły są `IsSeedNode=”true”` oznaczone tagiem i można je zidentyfikować, przeprowadzając kwerendę w manifeście klastra za pomocą programu `Get-ServiceFabricClusterManifest`. Usuwanie takich węzłów może trwać dłużej niż inne, ponieważ węzły źródłowe będą musiały być przenoszone w takich scenariuszach. Klaster musi obsługiwać co najmniej 3 węzły typu węzła podstawowego.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Usuwanie typów węzłów z klastra
Przed usunięciem typu węzła należy dokładnie sprawdzić, czy istnieją węzły odwołujące się do typu węzła. Usuń te węzły przed usunięciem odpowiedniego typu węzła. Po usunięciu wszystkich odpowiednich węzłów można usunąć typ węzła z konfiguracji klastra i rozpocząć uaktualnienie konfiguracji przy użyciu [funkcji Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Zastępowanie węzłów podstawowych klastra
Zastąpienie węzłów podstawowych należy wykonać jeden węzeł po drugim, zamiast usuwania, a następnie dodawanie w partiach.


## <a name="next-steps"></a>Następne kroki
* [Ustawienia konfiguracji autonomicznego klastra systemu Windows](service-fabric-cluster-manifest.md)
* [Zabezpieczanie autonomicznego klastra w systemie Windows przy użyciu certyfikatów X509](service-fabric-windows-cluster-x509-security.md)
* [Tworzenie autonomicznego klastra sieci szkieletowej usług z maszynami wirtualnymi platformy Azure z systemem Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)

