---
title: Dodaj lub usuń węzły do klastra usługi sieć szkieletowa autonomiczny | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać lub usunąć węzły do klastra usługi sieć szkieletowa usług Azure na fizyczne lub maszyny wirtualnej z systemem Windows Server, który może być lokalnym lub w dowolnej chmury.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: b1ffe3510cf4f5e73b05572a482f49d529fca60d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Dodawanie lub usuwanie węzłów do autonomicznej klastra sieci szkieletowej usług w systemie Windows Server
Po utworzeniu [utworzone autonomicznej klastra sieci szkieletowej usług na komputerach z systemem Windows Server](service-fabric-cluster-creation-for-windows-server.md)musisz dodać lub usunąć węzły do klastra i potrzeby (biznesowe) mogą ulec zmianie. Ten artykuł zawiera szczegółowy opis kroków można to osiągnąć. Należy pamiętać, że dodawania i usuwania węzła funkcjonalność nie jest obsługiwana w klastrach rozwoju lokalnego.

## <a name="add-nodes-to-your-cluster"></a>Dodaj węzły do klastra

1. Przygotowanie wirtualna maszyny mają zostać dodane do klastra, wykonując czynności opisane w temacie [planowanie i przygotowanie wdrożenia klastra sieci szkieletowej usług](service-fabric-cluster-creation-for-windows-server.md)
2. Zidentyfikuj które domeny błędów i zamierzasz dodać tej maszyny Wirtualnej/maszyny do domeny uaktualnienia
3. Pulpitu zdalnego (RDP) do maszyny Wirtualnej/maszyny, które mają zostać dodane do klastra
4. Kopiowanie lub [Pobierz autonomiczny dla usługi sieci szkieletowej systemu Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) do maszyny Wirtualnej/maszyny i Rozpakuj pakiet
5. Uruchom program Powershell z podwyższonym poziomem uprawnień i przejdź do lokalizacji rozpakowanej pakietu
6. Uruchom *AddNode.ps1* skryptu z parametrami opisujące nowy węzeł do dodania. W poniższym przykładzie dodaje nowy węzeł o nazwie VM5, z typem NodeType0 i adresów IP 182.17.34.52, UD1 i fd: / dc1/r0. *ExistingClusterConnectionEndPoint* jest punktu końcowego połączenia dla węzła już istniejącego klastra, które mogą być adres IP *żadnych* węzła w klastrze.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    Po zakończeniu skryptu można sprawdzić, czy nowy węzeł został dodany przez uruchomienie [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) polecenia cmdlet.

7. Aby zapewnić spójność w różnych węzłach w klastrze, musisz zainicjować uaktualnienie konfiguracji. Uruchom [Get ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) pobrać najnowszy plik konfiguracji, a następnie Dodaj węzeł nowo dodanego do sekcji "Węzłów". Zalecane jest również zawsze mieć najnowsze dostępne w przypadku konieczność redploy klastra z taką samą konfiguracją konfiguracji klastra.

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
    Można monitorować postępy uaktualniania w narzędziu Service Fabric Explorer. Alternatywnie można uruchomić [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Dodaj węzły do klastrów, skonfigurowaną przy użyciu usługi zarządzane przez grupę zabezpieczeń systemu Windows
W przypadku klastrów skonfigurowano Account(gMSA) usług zarządzanych grupy (https://technet.microsoft.com/library/hh831782.aspx), można dodać nowego węzła przy użyciu uaktualnienia konfiguracji:
1. Uruchom [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) na żadnym z istniejących węzłów, aby pobrać najnowszy plik konfiguracji i dodać szczegółowe informacje o nowym węźle ma zostać dodany w sekcji "Węzłów". Upewnij się, że nowy węzeł jest częścią tego samego konta zarządzane przez grupę. To konto powinno mieć uprawnienia administratora na wszystkich komputerach.

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
    Można monitorować postępy uaktualniania w narzędziu Service Fabric Explorer. Alternatywnie można uruchomić [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Dodaj typy węzła do klastra
Aby dodać nowy typ węzła, zmodyfikuj konfigurację do uwzględnienia nowego typu węzła w "elementów NodeType" w obszarze "Właściwości" i rozpocząć konfigurację aktualizację, używając [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). Po zakończeniu uaktualniania, można dodać nowe węzły do klastra z tym typem węzła.

## <a name="remove-nodes-from-your-cluster"></a>Usuń węzły z klastra
Można usunąć węzła z klastra przy użyciu uaktualniania konfiguracji, w następujący sposób:

1. Uruchom [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) można pobrać najnowszy plik konfiguracji i *Usuń* węzła z sekcji "Węzłów".
Dodaj parametr "NodesToBeRemoved" w sekcji "Ustawienia" w sekcji "FabricSettings". "Wartość" powinna być rozdzielana przecinkami lista nazwy węzła węzłów, które muszą zostać usunięte.

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
    Można monitorować postępy uaktualniania w narzędziu Service Fabric Explorer. Alternatywnie można uruchomić [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> Usuwanie węzłów może zainicjować kilku uaktualnień. Niektóre węzły są oznaczone ikoną z `IsSeedNode=”true”` tagu i mogą zostać zidentyfikowane przez wysyłanie zapytań do klastra manifestu przy użyciu `Get-ServiceFabricClusterManifest`. Usunięcie tych węzłów może trwać dłużej niż inne, ponieważ węzłów inicjatora będą musieli można przenosić w takich scenariuszach. Klaster musi obsługiwać co najmniej 3 węzłach typu węzła podstawowego.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Usuń typy węzłów z klastra
Przed usunięciem typu węzła, sprawdź czy ma żadnych węzłów odwołuje się do typu węzła. Usuń te węzły przed usunięciem odpowiedniego typu węzła. Po usunięciu wszystkich odpowiadających im węzłów, można usunąć NodeType z konfiguracji klastra i rozpocząć konfigurację aktualizację, używając [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Zastąp głównej węzłów w klastrze
Zastąpienie węzłów głównej powinny być wykonywane jeden węzeł po kolei, zamiast usuwania, a następnie dodanie w partiach.


## <a name="next-steps"></a>Kolejne kroki
* [Ustawienia konfiguracji dla autonomicznych klastra systemu Windows](service-fabric-cluster-manifest.md)
* [Secure autonomiczny klastra w systemie Windows przy użyciu X509 certyfikatów](service-fabric-windows-cluster-x509-security.md)
* [Tworzenie klastra usługi sieć szkieletowa autonomiczny z systemem Windows maszynach wirtualnych platformy Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)

