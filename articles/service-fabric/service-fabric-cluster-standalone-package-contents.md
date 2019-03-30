---
title: Pakietu autonomicznego sieci szkieletowej usługi platformy Azure dla systemu Windows Server | Dokumentacja firmy Microsoft
description: Opis i zawartość pakietu autonomicznego sieci szkieletowej usługi platformy Azure dla systemu Windows Server.
services: service-fabric
documentationcenter: .net
author: maburlik
manager: chackdan
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: facdcd162826e6f77ace098391459cba00061c4f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661619"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Zawartość pakietu autonomicznego sieci szkieletowej usługi dla systemu Windows Server
W [pobrane](https://go.microsoft.com/fwlink/?LinkId=730690) pakietu usługi Service Fabric autonomicznego, znajdziesz następujące pliki:

| **Nazwa pliku** | **Krótki opis** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Skrypt programu PowerShell, który tworzy klaster przy użyciu ustawień w ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Skrypt programu PowerShell, który usuwa klaster przy użyciu ustawień w ClusterConfig.json. |
| AddNode.ps1 |Skrypt programu PowerShell do dodawania węzła do istniejącego wdrożenia klastra na bieżącym komputerze. |
| RemoveNode.ps1 |Skrypt programu PowerShell do usuwania węzła z istniejącego wdrożenia klastra z bieżącego komputera. |
| CleanFabric.ps1 |Skrypt programu PowerShell, czyszczenia autonomicznej instalacji usługi Service Fabric poza bieżącym komputerze. Poprzednich instalacji MSI należy je usunąć za pomocą ich własnych uninstallers skojarzone. |
| TestConfiguration.ps1 |Skrypt programu PowerShell do analizowania infrastruktury, jak to określono w Cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Skrypt programu PowerShell, używane do pobierania najnowszego pakietu środowiska uruchomieniowego poza pasmem w scenariuszach, gdzie wdrażanie maszyna nie jest połączona z Internetem. |
| DeploymentComponentsAutoextractor.exe |Samowyodrębniający archiwum zawierające składniki wdrażania używane przez skrypty pakietu autonomicznego. |
| EULA_ENU.txt |Postanowienia licencyjne dotyczące użycia pakietu systemu Windows Server autonomicznego programu Microsoft Azure Service Fabric. Możesz [pobrać kopię tej umowy licencyjnej](https://go.microsoft.com/fwlink/?LinkID=733084) teraz. |
| Plik Readme.txt |Łącze do informacje o wersji i instrukcje dotyczące instalacji podstawowe. Jest podzbiorem zgodnie z instrukcjami w tym dokumencie. |
| ThirdPartyNotice.rtf |Informacja dotycząca oprogramowania innych firm, który znajduje się w pakiecie. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe, które jest uruchamiane na żądanie do zbierania i przekazywania dzienników do firmy Microsoft w celu obsługi. |
| Tools\ServiceFabricUpdateService.zip |Narzędzie umożliwiają automatyczne uaktualnianie kodu w przypadku klastrów, które nie mają dostępu do Internetu. Więcej szczegółów można znaleźć [tutaj](service-fabric-cluster-upgrade-windows-server.md)|

**Szablony** 

| **Nazwa pliku** | **Krótki opis** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Klaster przykładowy plik konfiguracji zawierający ustawienia dla niezabezpieczonym, trzema węzłami pojedynczego komputera (lub klaster maszyn wirtualnych) rozwoju, włącznie z informacjami dla każdego węzła w klastrze. |
| ClusterConfig.Unsecure.MultiMachine.json |Klaster przykładowy plik konfiguracji zawierający ustawienia dla klastra niezabezpieczone, wielu maszyny (lub maszyny wirtualnej), w tym informacje dla każdego komputera w klastrze. |
| ClusterConfig.Windows.DevCluster.json |Klastra przykładowy plik konfiguracji zawierający wszystkie ustawienia dla bezpieczny i trzema węzłami pojedynczego komputera (lub maszyny wirtualnej) klastra programowania, łącznie z informacjami dla każdego węzła, który znajduje się w klastrze. Klaster jest zabezpieczony za pomocą [tożsamości Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Klaster przykładowy plik konfiguracji zawierający wszystkie ustawienia dla bezpiecznych, wielu maszyny (lub maszyny wirtualnej) klastra przy użyciu zabezpieczeń Windows, w tym informacje dotyczące każdej maszynie, która znajduje się w zabezpieczonym klastrem. Klaster jest zabezpieczony za pomocą [tożsamości Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Klaster przykładowy plik konfiguracji zawierający wszystkie ustawienia dla bezpieczny i trzema węzłami pojedynczego komputera (lub maszyny wirtualnej) klastra programowania, w tym informacje dla każdego węzła w klastrze. Klaster jest zabezpieczony za pomocą x509 certyfikatów. |
| ClusterConfig.x509.MultiMachine.json |Klaster przykładowy plik konfiguracji zawierający wszystkie ustawienia dla klastra bezpiecznego, wielu maszyny (lub maszyny wirtualnej), w tym informacje dla każdego węzła w zabezpieczonym klastrem. Klaster jest zabezpieczony za pomocą x509 certyfikatów. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Klaster przykładowy plik konfiguracji zawierający wszystkie ustawienia dla klastra bezpiecznego, wielu maszyny (lub maszyny wirtualnej), w tym informacje dla każdego węzła w zabezpieczonym klastrem. Klaster jest zabezpieczony za pomocą [kont usługi zarządzanych przez grupę](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Przykłady konfiguracji klastra
Najnowsze wersje Szablony konfiguracji klastra można znaleźć na stronie usługi GitHub: [Przykłady konfiguracji klastra autonomicznego](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Niezależnie od pakietu środowiska uruchomieniowego
Najnowszy pakiet środowiska uruchomieniowego jest automatycznie pobierana podczas wdrażania klastra z [łącze Pobierz — środowisko uruchomieniowe usługi Service Fabric — system Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Powiązane
* [Tworzenie autonomicznego klastra usługi Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
* [Scenariusze zabezpieczeń klastra usługi Service Fabric](service-fabric-windows-cluster-windows-security.md)
