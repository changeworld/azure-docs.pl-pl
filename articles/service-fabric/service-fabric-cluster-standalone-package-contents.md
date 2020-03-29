---
title: Samodzielny pakiet sieci szkieletowej usługi Azure dla systemu Windows Server
description: Opis i zawartość autonomicznego pakietu sieci szkieletowej usługi Azure dla systemu Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1bb73fa69717f067139067f127a0d50af4878d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451846"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Zawartość autonomicznego pakietu sieci szkieletowej usług dla systemu Windows Server
W [pobranym](https://go.microsoft.com/fwlink/?LinkId=730690) pakiecie autonomicznej sieci szkieletowej usług znajdują się następujące pliki:

| **Nazwa pliku** | **Krótki opis** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Skrypt programu PowerShell, który tworzy klaster przy użyciu ustawień w pliku ClusterConfig.json. |
| UsuńServiceFabricCluster.ps1 |Skrypt programu PowerShell, który usuwa klaster przy użyciu ustawień w pliku ClusterConfig.json. |
| AddNode.ps1 |Skrypt programu PowerShell do dodawania węzła do istniejącego wdrożonego klastra na bieżącym komputerze. |
| UsuńNodę.ps1 |Skrypt programu PowerShell do usuwania węzła z istniejącego wdrożonego klastra z bieżącego komputera. |
| CleanFabric.ps1 |Skrypt programu PowerShell do czyszczenia autonomicznej instalacji sieci szkieletowej usług poza bieżącym komputerze. Poprzednie instalacje MSI powinny zostać usunięte przy użyciu własnych skojarzonych deinstalatorów. |
| TestConfiguration.ps1 |Skrypt programu PowerShell do analizowania infrastruktury, jak określono w cluster.json. |
| PobierzServiceFabricRuntimePackage.ps1 |Skrypt programu PowerShell używany do pobierania najnowszego pakietu środowiska wykonawczego poza pasmem w scenariuszach, w których komputer wdrażający nie jest połączony z Internetem. |
| DeploymentComponentsAutoextractor.exe |Samorozdysobneczące archiwum zawierające składniki wdrażania używane przez skrypty pakietu autonomicznego. |
| EULA_ENU.txt |Postanowienia licencyjne dotyczące korzystania z autonomicznego pakietu usługi Microsoft Azure Service Fabric systemu Windows Server. Możesz [pobrać kopię umowy EULA już](https://go.microsoft.com/fwlink/?LinkID=733084) teraz. |
| Readme.txt |Łącze do informacji o wersji i podstawowych instrukcji instalacji. Jest to podzbiór instrukcji w tym dokumencie. |
| Strona trzeciaNotice.rtf |Powiadomienie o oprogramowaniu innych firm, które znajduje się w pakiecie. |
| Narzędzia\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe, który jest uruchamiany na żądanie do zbierania i przekazywania dzienników śledzenia do firmy Microsoft w celu pomocy technicznej. |
| Narzędzia\ServiceFabricUpdateService.zip |Narzędzie służące do automatycznego uaktualniania kodu dla klastrów, które nie mają dostępu do Internetu. Więcej szczegółów można znaleźć [tutaj](service-fabric-cluster-upgrade-windows-server.md)|

**Szablony** 

| **Nazwa pliku** | **Krótki opis** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Przykładowy plik konfiguracji klastra zawierający ustawienia klastra deweloperskiego niezabezpieczonego, trzywęzłowego, jednoobrobowego (lub maszynowego), w tym informacje dotyczące każdego węzła w klastrze. |
| ClusterConfig.Unsecure.MultiMachine.json |Przykładowy plik konfiguracji klastra zawierający ustawienia klastra niezabezpieczonego, wieloobrobowego (lub maszyny wirtualnej), w tym informacje dotyczące każdego komputera w klastrze. |
| ClusterConfig.Windows.DevCluster.json |Przykładowy plik konfiguracji klastra zawierający wszystkie ustawienia klastra deweloperskiego bezpiecznego, trzywęzłowego, jednopłerowego (lub maszyny wirtualnej), w tym informacje dotyczące każdego węzła znajdującego się w klastrze. Klaster jest zabezpieczony przy użyciu [tożsamości systemu Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Przykładowy plik konfiguracji klastra zawierający wszystkie ustawienia klastra bezpiecznego, wielokołowego (lub maszyny wirtualnej) przy użyciu zabezpieczeń systemu Windows, w tym informacje dotyczące każdego komputera znajdującego się w bezpiecznym klastrze. Klaster jest zabezpieczony przy użyciu [tożsamości systemu Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Przykładowy plik konfiguracji klastra zawierający wszystkie ustawienia klastra deweloperskiego bezpiecznego, trzywęzłowego, jednopłerowego (lub maszyny wirtualnej), w tym informacje dotyczące każdego węzła w klastrze. Klaster jest zabezpieczony za pomocą certyfikatów x509. |
| ClusterConfig.x509.MultiMachine.json |Przykładowy plik konfiguracji klastra zawierający wszystkie ustawienia klastra bezpiecznego, wielokołowego (lub maszyny wirtualnej), w tym informacje dotyczące każdego węzła w bezpiecznym klastrze. Klaster jest zabezpieczony za pomocą certyfikatów x509. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Przykładowy plik konfiguracji klastra zawierający wszystkie ustawienia klastra bezpiecznego, wielokołowego (lub maszyny wirtualnej), w tym informacje dotyczące każdego węzła w bezpiecznym klastrze. Klaster jest zabezpieczony przy użyciu [grupowych kont usług zarządzanych](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Przykłady konfiguracji klastra
Najnowsze wersje szablonów konfiguracji klastra można znaleźć na stronie GitHub: [Przykłady konfiguracji klastra autonomicznego](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Niezależny pakiet środowiska wykonawczego
Najnowszy pakiet środowiska wykonawczego jest pobierany automatycznie podczas wdrażania klastra z [Download Link - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Powiązane
* [Tworzenie autonomicznego klastra sieci szkieletowej usług Azure](service-fabric-cluster-creation-for-windows-server.md)
* [Scenariusze zabezpieczeń klastra sieci szkieletowej usług](service-fabric-windows-cluster-windows-security.md)
