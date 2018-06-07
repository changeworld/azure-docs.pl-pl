---
title: Konfigurowanie autonomicznego klastra usługi Azure Service Fabric | Microsoft Docs
description: Utwórz autonomiczny klaster programowania z trzema węzłami działającymi na tym samym komputerze. Po ukończeniu tej konfiguracji wszystko będzie gotowe do tworzenia klastra dla wielu maszyn.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 93c921e54c69693ed02c122a2491b0942f550126
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643468"
---
# <a name="create-your-first-service-fabric-standalone-cluster"></a>Tworzenie pierwszego autonomicznego klastra usługi Service Fabric
Autonomiczny klaster usługi Service Fabric można utworzyć na wszystkich maszynach wirtualnych lub komputerach z systemem Windows Server 2012 R2 lub Windows Server 2016 lokalnie lub w chmurze. Ten przewodnik szybkiego startu pomaga utworzyć autonomiczny klaster programowania w ciągu zaledwie kilku minut.  Po zakończeniu na pojedynczym komputerze będzie dostępny klaster z trzema węzłami, do którego można wdrażać aplikacje.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Usługa Service Fabric udostępnia pakiet instalacyjny służący do tworzenia autonomicznych klastrów usługi Service Fabric.  [Pobierz pakiet instalacyjny](http://go.microsoft.com/fwlink/?LinkId=730690).  Rozpakuj pakiet instalacyjny do folderu na komputerze lub maszynie wirtualnej, na której przeprowadzasz konfigurację klastra programowania.  Zawartość pakietu instalacyjnego została szczegółowo opisana [tutaj](service-fabric-cluster-standalone-package-contents.md).

Administrator klastra wdrażający i konfigurujący klaster musi mieć uprawnienia administratora na komputerze. Usługi Service Fabric nie można zainstalować na kontrolerze domeny.

## <a name="validate-the-environment"></a>Weryfikowanie środowiska
Skrypt *TestConfiguration.ps1* w pakiecie autonomicznym jest używany jako analizator najlepszych rozwiązań, który weryfikuje, czy można wdrożyć klaster w danym środowisku. Listę wymagań wstępnych i wymagań dotyczących środowiska można znaleźć w temacie [Deployment preparation](service-fabric-cluster-standalone-deployment-preparation.md) (Przygotowywanie środowiska). Uruchom skrypt, aby zweryfikować, czy można utworzyć klaster programowania:

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>Tworzenie klastra
W ramach pakietu instalacyjnego jest instalowanych kilka przykładowych plików konfiguracji klastra. *ClusterConfig.Unsecure.DevCluster.json* to najprostsza konfiguracja klastra: niezabezpieczony klaster z trzema węzłami działającymi na pojedynczym komputerze.  Inne pliki konfiguracji zawierają opis klastrów dla jednej lub wielu maszyn, zabezpieczonych za pomocą certyfikatów X.509 lub zabezpieczeń systemu Windows.  Nie musisz modyfikować żadnych domyślnych ustawień konfiguracji w tym samouczku, ale przyjrzyj się plikowi konfiguracji i zapoznaj się z ustawieniami.  Sekcja **węzły** opisuje trzy węzły w klastrze: nazwę, adres IP, [typ węzła, domenę błędów i domenę uaktualnień](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  Sekcja **właściwości** definiuje [zabezpieczenia, poziom niezawodności, zbieranie diagnostyki i typy węzłów](service-fabric-cluster-manifest.md#cluster-properties) klastra.

Ten klaster jest niezabezpieczony.  Każda osoba może połączyć się z nim anonimowo i przeprowadzić operacje związane z zarządzaniem. Dlatego też klastry produkcyjne należy zawsze zabezpieczać przy użyciu certyfikatów X.509 lub zabezpieczeń systemu Windows.  Zabezpieczenia są konfigurowane tylko w momencie tworzenia klastra. Nie można włączyć zabezpieczeń później, gdy klaster jest już utworzony.  Dalsze informacje na temat zabezpieczeń klastra usługi Service Fabric można znaleźć w temacie [Secure a cluster](service-fabric-cluster-security.md) (Zabezpieczanie klastra).  

W celu utworzenia klastra programowania z trzema węzłami uruchom skrypt *CreateServiceFabricCluster.ps1* w sesji programu PowerShell administratora:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Pakiet środowiska uruchomieniowego usługi Service Fabric jest automatyczne pobierany i instalowany podczas tworzenia klastra.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem
Klaster programowania z trzema węzłami został uruchomiony. Moduł ServiceFabric programu PowerShell został zainstalowany w środowisku uruchomieniowym.  Można sprawdzić, czy w środowisku uruchomieniowym usługi Service Fabric klaster działa na tym samym komputerze, czy też na komputerze zdalnym.  Polecenie cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) umożliwia ustanowienie połączenia z klastrem.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
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

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Wizualizowanie klastra za pomocą narzędzia Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) to odpowiednie narzędzie do wizualizowania klastra i zarządzania aplikacjami.  Service Fabric Explorer to usługa, która działa w klastrze, otwieranego w przeglądarce, przechodząc do [ http://localhost:19080/Explorer ](http://localhost:19080/Explorer). 

Pulpit nawigacyjny klastra zawiera omówienie klastra, w tym podsumowanie kondycji węzła i aplikacji. Widok węzła przedstawia fizyczny układ klastra. Dla danego węzła można sprawdzić, które aplikacje mają kod wdrożony w tym węźle.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>Usuwanie klastra
Aby usunąć klaster, uruchom skrypt *RemoveServiceFabricCluster.ps1* programu PowerShell z folderu pakietu i przekaż ścieżkę do pliku konfiguracji JSON. Możesz opcjonalne określić lokalizację dziennika usuwania.

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

Aby usunąć środowisko uruchomieniowe usługi Service Fabric z komputera, uruchom poniższy skrypt programu PowerShell z folderu pakietu.

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>Kolejne kroki
Teraz po skonfigurowaniu autonomicznego klastra programowania możesz spróbować wykonać czynności opisane w następujących artykułach:
* [Konfigurowanie autonomicznego klastra dla wielu maszyn](service-fabric-cluster-creation-for-windows-server.md) i włączanie zabezpieczeń
* [Wdrażanie aplikacji przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md)

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png
