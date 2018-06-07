---
title: Debugowanie kontenerów systemu Windows z sieci szkieletowej usług i VS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można debugować kontenery systemu Windows w sieci szkieletowej usług Azure przy użyciu programu Visual Studio 2017 r.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/14/2018
ms.author: mikhegn
ms.openlocfilehash: bca33fe187668d38d4451b2de5b9e54d86e40ba9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655755"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2017"></a>Porady: debugowanie kontenerów systemu Windows w sieci szkieletowej usług Azure przy użyciu programu Visual Studio 2017 r.

Z programu Visual Studio 2017 aktualizacji 7 (15.7) można debugować aplikacji .NET w kontenerach jako usługi sieci szkieletowej usług. W tym artykule przedstawiono sposób konfigurowania środowiska, a następnie debugowanie aplikacji .NET w kontenerze uruchomiona w klastrze usługi sieć szkieletowa usług lokalnych.

## <a name="prerequisites"></a>Wymagania wstępne

* W systemie Windows 10, postępuj zgodnie z tego przewodnika Szybki Start do [skonfigurować systemu Windows 10, aby uruchomić kontenery systemu Windows](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* W systemie Windows Server 2016, postępuj zgodnie z tego przewodnika Szybki Start do [skonfigurować 2016 systemu Windows do uruchamiania kontenery systemu Windows](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Konfigurowanie środowiska sieci szkieletowej usług lokalnych, wykonując [przygotowywanie środowiska projektowego w systemie Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Skonfiguruj środowisko dewelopera debugowanie kontenerów

1. Upewnij się, że działa Docker usługi okna przed przejściem do następnego kroku.

1. Aby zapewnić obsługę rozpoznawania nazw DNS między kontenerów, musisz skonfigurować klaster lokalny rozwój, przy użyciu nazwy komputera.
    1. Otwórz program PowerShell jako administrator
    1. Przejdź do folderu instalacyjnego zestawu SDK klastra, zwykle `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`
    1. Uruchom skrypt `DevClusterSetup.ps1` z parametrem `-UseMachineName`

    ``` PowerShell
      C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1 -UseMachineName
    ```

    > [!NOTE]
    > Można użyć `-CreateOneNodeCluster` Konfiguracja klastra z jednym węzłem. Wartość domyślna utworzy pięcioma węzłami klastra lokalnego.
    >

    Aby dowiedzieć się więcej na temat usługi DNS w sieci szkieletowej usług, zobacz [usługa DNS w sieci szkieletowej usług Azure](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-dnsservice).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Znane ograniczenia podczas debugowania kontenerów w sieci szkieletowej usług

Poniżej znajduje się lista znane ograniczenia z kontenerami debugowania w sieci szkieletowej usług i możliwe rozwiązania:

* Przy użyciu nazwy localhost dla ClusterFQDNorIP nie będzie obsługiwał rozpoznawania nazw DNS w kontenerach.
    * Rozwiązanie: Konfigurowanie lokalnego klastra przy użyciu nazwy komputera (zobacz powyżej)
* Uruchamianie Windows10 na maszynie wirtualnej nie otrzyma odpowiedź DNS do kontenera.
    * Rozwiązanie: Wyłącz Odciążanie sumy kontrolnej protokołu UDP dla protokołu IPv4 na karcie Sieciowej maszyny wirtualnej
    * Należy pamiętać, że to spowoduje zmniejszenie wydajności sieci na maszynie.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Usługi w tej samej aplikacji przy użyciu systemu DNS do rozpoznawania nazwy usługi nie działa na Windows10, jeśli aplikacja została wdrożona przy użyciu rozwiązania Docker Compose
    * Rozwiązanie: Rozpoznawać punktów końcowych usługi przy użyciu servicename.applicationname
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Jeśli przy użyciu adresu IP dla ClusterFQDNorIP, zmiana podstawowego adresu IP na hoście spowoduje uszkodzenie funkcji DNS.
    * Rozwiązanie: Utwórz ponownie klaster przy użyciu nowego podstawowego adresu IP na hoście lub użyj nazwy komputera. Jest to celowe.
* Jeśli nazwy FQDN klastra został utworzony z nie jest rozpoznawany w sieci, DNS zakończy się niepowodzeniem.
    * Rozwiązanie: Utwórz ponownie klaster lokalny przy użyciu podstawowego adresu IP hosta. Jest to celowe.
* Debugowanie kontenera, dzienniki docker tylko będą dostępne w oknie danych wyjściowych programu Visual Studio, nie za pośrednictwem interfejsów API sieci szkieletowej usług, w tym Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Debugowanie aplikacji .NET w kontenerach docker systemem sieci szkieletowej usług

1. Uruchom program Visual Studio jako administrator.

1. Otwórz istniejącą aplikację .NET lub Utwórz nową.

1. Kliknij prawym przyciskiem myszy projekt i wybierz **Obsługa kontenerów Orchestrator -> Dodaj -> sieci szkieletowej usług**

1. Naciśnij klawisz **F5** można rozpocząć debugowania aplikacji.

    Visual Studio obsługuje konsoli i typów projektów programu ASP.NET, .NET i .NET Core.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o możliwościach usługi Service Fabric i kontenerów, wykonaj to łącze: [omówienie kontenery sieci szkieletowej usług](service-fabric-containers-overview.md).