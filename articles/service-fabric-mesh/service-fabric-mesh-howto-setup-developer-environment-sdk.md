---
title: Konfigurowanie środowiska deweloperskiego w systemie Windows w celu tworzenia aplikacji usługi Service Fabric Mesh | Microsoft Docs
description: Skonfigurowanie środowiska deweloperskiego w systemie Windows pozwala utworzyć aplikację usługi Service Fabric Mesh i wdrożyć ją w usłudze Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 11/29/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: bec0b9a7e34f1577f80a99f5380795c479c04bc8
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890470"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Konfigurowanie środowiska deweloperskiego w systemie Windows w celu tworzenia aplikacji usługi Service Fabric Mesh

Aby kompilować i uruchamiać aplikacje usługi Azure Service Fabric Mesh na maszynie deweloperskiej z systemem Windows, należy zainstalować środowisko uruchomieniowe, zestaw SDK oraz narzędzia usługi Service Fabric Mesh.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Obsługiwane wersje systemu operacyjnego

Na potrzeby tworzenia aplikacji obsługiwane są następujące wersje systemu operacyjnego:

* Windows 10 (Enterprise, Professional lub Education)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

Do wdrażania aplikacji usługi Service Fabric Mesh jest wymagany program Visual Studio 2017. [Zainstaluj wersję 15.6.0][download-visual-studio] lub nowszą i włącz następujące obciążenia:

* Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
* Programowanie na platformie Azure

## <a name="install-docker"></a>Zainstaluj platformę Docker

#### <a name="windows-10"></a>Windows 10

Pobierz i zainstaluj najnowszą wersję aplikacji [Docker Community Edition for Windows][download-docker] do obsługi konteneryzowanych aplikacji usługi Service Fabric używanych w usłudze Service Fabric Mesh.

Podczas instalacji wybierz opcję **Use Windows containers instead of Linux containers** (Użyj kontenerów systemu Windows zamiast kontenerów systemu Linux), gdy pojawi się odpowiedni monit.

Jeśli na maszynie nie włączono funkcji Hyper-V, w instalatorze platformy Docker pojawi się sugestia włączenia tej funkcji. Jeśli pojawi się taki monit, kliknij przycisk **OK**, aby to zrobić.

#### <a name="windows-server-2016"></a>Windows Server 2016

Jeśli nie masz włączonej roli funkcji Hyper-V, otwórz program PowerShell jako administrator i uruchom następujące polecenie, aby włączyć funkcję Hyper-V, a następnie uruchom ponownie komputer. Aby uzyskać więcej informacji, zobacz [Docker Enterprise Edition for Windows Server (Oprogramowanie Docker Enterprise Edition for Windows Server)][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Uruchom ponownie komputer.

Otwórz program PowerShell jako administrator i uruchom następujące polecenie, aby zainstalować aplikację Docker:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>Zestaw SDK i narzędzia

Zainstaluj środowisko uruchomieniowe, zestaw SDK oraz narzędzia usługi Service Fabric Mesh w kolejności podanej poniżej.

1. Zainstaluj [zestaw SDK usługi Service Fabric Mesh][download-sdkmesh] za pomocą Instalatora platformy sieci Web. Spowoduje to również zainstalowanie zestawu SDK i środowiska uruchomieniowego usługi Microsoft Azure Service Fabric.
2. Zainstaluj [rozszerzenie Visual Studio Service Fabric Mesh Tools (wersja zapoznawcza)][download-tools] z witryny Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Tworzenie klastra

> [!IMPORTANT]
> Utworzenie klastra **wymaga**  uruchomienia platformy Docker.
> Aby sprawdzić, czy platforma Docker jest uruchomiona, otwórz okno terminalu i uruchom polecenie `docker ps`. Jeśli odpowiedź nie wskazuje na wystąpienie błędu, oznacza to, że platforma Docker jest uruchomiona i można przystąpić do tworzenia klastra.

Jeśli używasz programu Visual Studio, możesz pominąć tę sekcję, ponieważ program Visual Studio automatycznie utworzy klaster lokalny, jeśli taki klaster jeszcze nie istnieje.

Aby uzyskać najlepszą wydajność debugowania tworzonych i uruchamianych aplikacji usługi Service Fabric, zaleca się utworzenie lokalnego klastra projektowego z jednym węzłem. Ten klaster musi być uruchomiony podczas każdego wdrażania lub debugowania projektu usługi Service Fabric Mesh.

Po zainstalowaniu środowiska uruchomieniowego, zestawów SDK, narzędzi programu Visual Studio i platformy Docker oraz uruchomieniu platformy Docker utwórz klaster projektowy.

1. Zamknij okno programu PowerShell.
2. Otwórz nowe okno programu PowerShell jako administrator. Ten krok jest niezbędny do załadowania zainstalowanych uprzednio modułów usługi Service Fabric.
3. Utwórz klaster projektowy za pomocą następującego polecenia programu PowerShell:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. Uruchom menedżera klastra lokalnego za pomocą następującego polecenia programu PowerShell:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Gdy narzędzie menedżera klastra usługi jest uruchomione (jest wyświetlane na pasku zadań), kliknij je prawym przyciskiem myszy, a następnie kliknij pozycję **Uruchom klaster lokalny**.

![Rysunek 1. Uruchamianie klastra lokalnego](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Możesz już tworzyć aplikacje usługi Service Fabric Mesh.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkiem [Create an Azure Service Fabric app (Tworzenie aplikacji usługi Azure Service Fabric)](service-fabric-mesh-tutorial-create-dotnetcore.md).

Znajdź odpowiedzi na [często zadawane pytania](service-fabric-mesh-faq.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/