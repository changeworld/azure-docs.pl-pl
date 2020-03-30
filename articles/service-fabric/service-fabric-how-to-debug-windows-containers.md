---
title: Debugowanie kontenerów systemu Windows przy użyciu usługi Service Fabric i programu VS
description: Dowiedz się, jak debugować kontenery systemu Windows w sieci szkieletowej usługi Azure przy użyciu programu Visual Studio 2019.
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 2a00a352d09562ffe46dc8e6e63a5d4963ac3a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127604"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Jak: Debugowanie kontenerów systemu Windows w sieci szkieletowej usługi Azure przy użyciu programu Visual Studio 2019

Za pomocą programu Visual Studio 2019 można debugować aplikacje platformy .NET w kontenerach jako usługi sieci szkieletowej usług. W tym artykule pokazano, jak skonfigurować środowisko, a następnie debugować aplikację platformy .NET w kontenerze uruchomionym w lokalnym klastrze sieci szkieletowej usług.

## <a name="prerequisites"></a>Wymagania wstępne

* W systemie Windows 10 postępuj zgodnie z tym przewodnikiem Szybki start, aby [skonfigurować system Windows 10 w celu uruchamiania kontenerów systemu Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* W systemie Windows Server 2016 wykonaj ten przewodnik Szybki start, aby [skonfigurować system Windows 2016 do uruchamiania kontenerów systemu Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Konfigurowanie lokalnego środowiska sieci szkieletowej usług przez [obserwowanie przygotowywania środowiska programistycznego w systemie Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Konfigurowanie środowiska deweloperskiego do debugowania kontenerów

1. Upewnij się, że usługa Platformy Docker for Window jest uruchomiona przed kontynuowaniem następnego kroku.

1. Aby obsługiwać rozpoznawanie dns między kontenerami, musisz skonfigurować lokalny klaster programistyczny przy użyciu nazwy komputera. Te kroki są również niezbędne, jeśli chcesz adres usług za pośrednictwem odwrotnego serwera proxy.
   1. Otwórz program PowerShell jako administrator
   2. Zazwyczaj `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`przejdź do folderu konfiguracji klastra zestawu SDK .
   3. Uruchamianie skryptu`DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Służy do `-CreateOneNodeCluster` konfigurowania klastra z jednym węzłem. Wartość domyślna spowoduje utworzenie lokalnego klastra z pięcioma węzłami.
      >

      Aby dowiedzieć się więcej o usłudze DNS w sieci szkieletowej usług, zobacz [Usługa DNS w sieci szkieletowej usług Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Aby dowiedzieć się więcej na temat używania odwrotnego serwera proxy sieci usług z usług uruchomionych w kontenerze, zobacz [Odwróć usługę specjalną serwera proxy dla usług uruchomionych w kontenerach](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Znane ograniczenia podczas debugowania kontenerów w sieci szkieletowej usług

Poniżej znajduje się lista znanych ograniczeń z debugowania kontenerów w sieci szkieletowej usług i możliwych rozdzielczości:

* Korzystanie z localhost dla ClusterFQDNorIP nie obsługuje rozpoznawania DNS w kontenerach.
    * Rozwiązanie: Konfigurowanie lokalnego klastra przy użyciu nazwy komputera (patrz wyżej)
* Uruchamianie systemu Windows10 na maszynie wirtualnej nie będzie otrzymywać odpowiedzi DNS z powrotem do kontenera.
    * Rozwiązanie: wyłączanie odciążania sum kontrolnych UDP dla protokołu IPv4 na karcie sieciowej maszyn wirtualnych
    * Uruchomienie systemu Windows10 spowoduje obniżenie wydajności sieci na komputerze.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Rozwiązywanie usług w tej samej aplikacji przy użyciu nazwy usługi DNS nie działa w systemie Windows10, jeśli aplikacja została wdrożona przy użyciu aplikacji Docker Compose
    * Rozwiązanie: Używanie nazwy servicename.applicationname do rozpoznawania punktów końcowych usługi
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Jeśli używasz adresu IP dla ClusterFQDNorIP, zmiana podstawowego adresu IP na hoście spowoduje przerwanie funkcji DNS.
    * Rozwiązanie: Ponownie utworzyć klaster przy użyciu nowego podstawowego adresu IP na hoście lub użyć nazwy komputera. To pęknięcie jest zgodnie z projektem.
* Jeśli nazwa FQDN utworzona przez klaster nie jest rozwiązywalna w sieci, usługa DNS zakończy się niepowodzeniem.
    * Rozwiązanie: Ponownie utworzyć klaster lokalny przy użyciu podstawowego adresu IP hosta. Ta awaria jest zgodnie z projektem.
* Podczas debugowania kontenera dzienniki docker będą dostępne tylko w oknie wyjściowym programu Visual Studio, a nie za pośrednictwem interfejsów API sieci szkieletowej usług, w tym Eksploratora sieci szkieletowej usług

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Debugowanie aplikacji platformy .NET uruchomionej w kontenerach platformy docker w sieci szkieletowej usług

1. Uruchom program Visual Studio jako administrator.

1. Otwórz istniejącą aplikację .NET lub utwórz nową.

1. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj -> Container Orchestrator Support -> Service Fabric**

1. Naciśnij **klawisz F5,** aby rozpocząć debugowanie aplikacji.

    Program Visual Studio obsługuje typy projektów konsoli i ASP.NET dla platformy .NET i .NET Core.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat możliwości sieci szkieletowej usług i kontenerów, zobacz [omówienie kontenerów sieci szkieletowej usług.](service-fabric-containers-overview.md)
