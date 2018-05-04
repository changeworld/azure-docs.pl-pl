---
title: Pakietu autonomicznego sieci szkieletowej usług platformy Azure dla systemu Windows Server | Dokumentacja firmy Microsoft
description: Opis i zawartość pakietu autonomicznego sieci szkieletowej usług Azure dla systemu Windows Server.
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: maburlik;aljo
ms.openlocfilehash: 948b1bb2ce5a19007906262a4efb0ecca8e40482
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Zawartość pakietu autonomicznego sieci szkieletowej usług dla systemu Windows Server
W [pobrane](http://go.microsoft.com/fwlink/?LinkId=730690) pakiet usługi sieć szkieletowa autonomiczny, dostępne są następujące pliki:

| **Nazwa pliku** | **Krótki opis** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Skrypt programu PowerShell, który jest tworzony klaster przy użyciu ustawień w pliku ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Skrypt programu PowerShell, która usuwa klastra przy użyciu ustawień w pliku ClusterConfig.json. |
| AddNode.ps1 |Skrypt programu PowerShell do dodawania węzła do istniejącego wdrożyć klaster na bieżącym komputerze. |
| RemoveNode.ps1 |Skrypt programu PowerShell dla usuwania węzła z istniejącego wdrożyć klaster z bieżącego komputera. |
| CleanFabric.ps1 |Skrypt programu PowerShell do czyszczenia autonomiczny instalacji usługi sieć szkieletowa poza bieżącym komputerze. Poprzednie instalacje MSI powinny zostać usunięte przy użyciu własnych uninstallers skojarzone. |
| TestConfiguration.ps1 |Skrypt programu PowerShell do analizowania infrastruktury, jak określono w Cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Skrypt programu PowerShell, używany do pobierania najnowszy pakiet obsługi poza pasmem w scenariuszach, gdzie wdrażanie komputer nie jest połączony z Internetem. |
| DeploymentComponentsAutoextractor.exe |Archiwum samowyodrębniający zawierający składniki wdrażania używany przez skrypty pakietu autonomicznego. |
| EULA_ENU.txt |Postanowienia licencyjne dotyczące użycia pakietu systemu Windows Server autonomicznej usługi sieć szkieletowa usług Microsoft Azure. Możesz [Pobierz kopię umowy licencyjnej](http://go.microsoft.com/fwlink/?LinkID=733084) teraz. |
| Readme.txt |Łącze informacjach o wersji i instrukcje dotyczące instalacji podstawowych. Jest ona podzbiorem instrukcje w tym dokumencie. |
| ThirdPartyNotice.rtf |Powiadomienia o oprogramowania innych firm, które znajduje się w pakiecie. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe uruchamiany na żądanie do gromadzenia i przekazywania dzienników śledzenia do firmy Microsoft w celu obsługi. |
| Tools\ServiceFabricUpdateService.zip |Narzędzie używane do włączenia automatycznego uaktualniania kodu dla klastrów, które nie mają dostępu do Internetu. Więcej szczegółów można znaleźć [tutaj](service-fabric-cluster-upgrade-windows-server.md)|

**Szablony** 
| **Nazwa pliku** | **Krótki opis** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Klaster przykładowy plik konfiguracji zawierający ustawienia dla niezabezpieczonym, trzech węzłów pojedynczego komputera (lub maszyny wirtualnej) programowanie klastra, w tym informacje dla każdego węzła w klastrze. |
| ClusterConfig.Unsecure.MultiMachine.json |Klaster przykładowy plik konfiguracji zawierający ustawienia dla klastra niezabezpieczoną, wielu (lub maszyny wirtualnej), łącznie z informacjami dla każdej maszyny w klastrze. |
| ClusterConfig.Windows.DevCluster.json |Klastra przykładowy plik konfiguracji zawierający ustawienia bezpiecznego, trzech węzłów pojedynczego komputera (lub maszyny wirtualnej) programowanie klastrze, w tym informacje dla każdego węzła w klastrze. Klaster jest zabezpieczony za pomocą [tożsamości systemu Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Klastra przykładowy plik konfiguracji zawierający ustawienia bezpieczny, wielu (lub maszyny wirtualnej) klastra używającego zabezpieczeń systemu Windows, w tym informacje o każdej maszynie, która znajduje się w klastrze bezpiecznego. Klaster jest zabezpieczony za pomocą [tożsamości systemu Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Klastra przykładowy plik konfiguracji zawierający ustawienia bezpiecznego, trzech węzłów pojedynczego komputera (lub maszyny wirtualnej) programowanie klastrze, w tym informacje dla każdego węzła w klastrze. Klaster jest zabezpieczone przy użyciu x509 certyfikatów. |
| ClusterConfig.x509.MultiMachine.json |Klastra przykładowy plik konfiguracji zawierający ustawienia klastra bezpieczny, wielu (lub maszyny wirtualnej), łącznie z informacjami dla każdego węzła w klastrze bezpiecznego. Klaster jest zabezpieczone przy użyciu x509 certyfikatów. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Klastra przykładowy plik konfiguracji zawierający ustawienia klastra bezpieczny, wielu (lub maszyny wirtualnej), łącznie z informacjami dla każdego węzła w klastrze bezpiecznego. Klaster jest zabezpieczone przy użyciu [kont usług zarządzanych grupy](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Przykłady konfiguracji klastra
Najnowsze wersje szablonów konfiguracji klastra można znaleźć na stronie GitHub: [przykłady konfiguracji klastra autonomiczny](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Niezależnie od pakietu środowiska wykonawczego
Najnowszy pakiet środowiska uruchomieniowego jest automatycznie pobierana podczas wdrażania klastra z [łącze Pobierz — środowisko uruchomieniowe usługi sieć szkieletowa - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Powiązane
* [Tworzenie klastra sieci szkieletowej usług Azure autonomiczny](service-fabric-cluster-creation-for-windows-server.md)
* [Scenariusze zabezpieczeń klastra sieci szkieletowej usług](service-fabric-windows-cluster-windows-security.md)
