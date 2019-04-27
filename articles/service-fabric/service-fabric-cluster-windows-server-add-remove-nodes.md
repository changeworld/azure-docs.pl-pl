---
title: Dodawanie lub usuwanie węzłów do klastra usługi Service Fabric autonomicznego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodawać lub usuwać węzły do klastra usługi Azure Service Fabric na fizyczne lub maszyny wirtualnej z systemem Windows Server, które mogą być konfigurowane lokalnie lub w chmurze.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 585d918026ca40bc1a04c55e2bac454492c55936
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711037"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Dodawanie lub usuwanie węzłów do klastra usługi Service Fabric autonomicznego w systemie Windows Server
Po utworzeniu [utworzony klaster usługi Service Fabric autonomiczne maszyny z systemem Windows Server](service-fabric-cluster-creation-for-windows-server.md), może zmienić Twoje potrzeby (biznesowe) i konieczne będzie dodawać lub usuwać węzły do klastra. Ten artykuł zawiera szczegółowy opis kroków, aby to osiągnąć. Należy pamiętać, że dodawania i usuwania węzłów funkcji nie jest obsługiwany w klastrach rozwoju lokalnego.

## <a name="add-nodes-to-your-cluster"></a>Dodaj węzły do klastra

1. Przygotowywanie maszyny Wirtualnej maszyny ma zostać dodany do klastra, wykonując czynności opisane w temacie [planowanie i przygotowywanie wdrożenia klastra usługi Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
2. Określenie, które domeny błędów i domeny uaktualnień, które zamierzasz dodać tej maszyny Wirtualnej/maszyny
3. Usług pulpitu zdalnego (RDP) do maszyny Wirtualnej/komputer, na którym chcesz dodać do klastra
4. Kopiowanie lub [Pobierz pakietu autonomicznego dla usługi Service Fabric dla systemu Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) do maszyn wirtualnych/maszyna i Rozpakuj pakiet
5. Uruchom program Powershell z podwyższonym poziomem uprawnień i przejdź do lokalizacji pakietu rozpakowany
6. Uruchom *AddNode.ps1* skrypt z parametrami, opisujące nowy węzeł do dodania. Poniższy przykład dodaje nowy węzeł o nazwie VM5, typem NodeType0 i adresów IP 182.17.34.52, UD1 i fd: / dc1/r0. *ExistingClusterConnectionEndPoint* jest punkt końcowy połączenia dla węzła już istniejącego klastra, który może być adres IP *wszelkie* węzła w klastrze.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    Po zakończeniu działania skryptu uruchamiania można sprawdzić, czy nowy węzeł został dodany, uruchamiając [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) polecenia cmdlet.

7. W celu zapewnienia spójności w różnych węzłach w klastrze, należy zainicjować uaktualnienie konfiguracji. Uruchom [Get ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) Pobierz najnowszy plik konfiguracji i Dodaj węzeł nowo dodane do sekcji "Węzły". Zalecane jest również zawsze mieć najnowsze dostępne w przypadku, że będzie konieczne ponowne wdrożenie klastra z taką samą konfiguracją konfiguracji klastra.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. Uruchom [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) aby rozpocząć uaktualnianie.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Można monitorować postępy uaktualniania w narzędziu Service Fabric Explorer. Alternatywnie, możesz uruchomić [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Dodaj węzły do klastrów skonfigurowanych z użyciem zabezpieczeń Windows przy użyciu gMSA
Dla klastrów skonfigurowanych z użyciem Account(gMSA) usług zarządzanych grupy (https://technet.microsoft.com/library/hh831782.aspx), można dodać nowego węzła przy użyciu uaktualnienia konfiguracji:
1. Uruchom [Get ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) na żadnym z istniejących węzłów, Pobierz najnowszy plik konfiguracji, a następnie dodać szczegóły dotyczące nowego węzła, które chcesz dodać w sekcji "Węzły". Upewnij się, że nowy węzeł jest częścią tego samego konta zarządzanych przez grupę. To konto powinno być administratorem na wszystkich komputerach.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Uruchom [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) aby rozpocząć uaktualnianie.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Można monitorować postępy uaktualniania w narzędziu Service Fabric Explorer. Alternatywnie, możesz uruchomić [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Dodaj typy węzłów do klastra
Aby dodać nowy typ węzła, należy zmodyfikować konfigurację tak, aby uwzględnić nowy typ węzła w "elementy NodeType" w obszarze "Właściwości" i rozpocząć konfigurację uaktualnianie za pomocą [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). Po zakończeniu uaktualniania, możesz dodać nowe węzły do klastra przy użyciu tego typu węzła.

## <a name="remove-nodes-from-your-cluster"></a>Usuwanie węzłów z klastra
Można je usunąć węzeł z klastra przy użyciu uaktualnienia konfiguracji, w następujący sposób:

1. Uruchom [Get ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) można pobrać najnowszy plik konfiguracji i *Usuń* węzła z sekcji "Węzły".
Dodano parametr "NodesToBeRemoved", do sekcji "Setup" w sekcji "Element FabricSettings". "value" powinny być rozdzielone przecinkami listę nazw węzła węzłów, które muszą zostać usunięte.

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
2. Uruchom [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) aby rozpocząć uaktualnianie.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Można monitorować postępy uaktualniania w narzędziu Service Fabric Explorer. Alternatywnie, możesz uruchomić [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> Usuwanie węzłów może zainicjować kilku uaktualnień. Niektóre węzły są oznaczone `IsSeedNode=”true”` tagu i mogą być identyfikowane przez wysyłanie zapytań do klastra manifestu za pomocą `Get-ServiceFabricClusterManifest`. Usunięcie tych węzłów może trwać dłużej niż inne, ponieważ węzły obiektów początkowych będą musieli można przenosić w takich scenariuszach. Klaster musi zachować co najmniej 3 węzłów typu węzła podstawowego.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Usuń typy węzłów z klastra
Przed usunięciem typ węzła, double. Sprawdź, czy istnieją wszystkie węzły, które odwołuje się do typu węzła. Przed usunięciem odpowiedni typ węzła, należy usunąć te węzły. Po usunięciu wszystkich odpowiednich węzłów można usunąć elementu NodeType z konfiguracji klastra i rozpocząć konfigurację uaktualnianie za pomocą [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Zastąp podstawowe węzły klastra
Zastąpienie podstawowe węzły powinna być wykonywane jeden węzeł po drugiej, zamiast usuwania, a następnie dodanie w partiach.


## <a name="next-steps"></a>Kolejne kroki
* [Ustawienia konfiguracji dla autonomicznego klastra Windows](service-fabric-cluster-manifest.md)
* [Zabezpieczanie klastra autonomicznego w Windows przy użyciu X509 certyfikatów](service-fabric-windows-cluster-x509-security.md)
* [Tworzenie klastra autonomicznego usługi Service Fabric z systemem Windows maszyn wirtualnych platformy Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)

