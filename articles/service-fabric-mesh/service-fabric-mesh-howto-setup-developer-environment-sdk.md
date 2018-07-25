---
title: Konfigurowanie środowiska deweloperskiego w systemie Windows dla aplikacji usługi Service Fabric Mesh | Microsoft Docs
description: Skonfigurowanie środowiska deweloperskiego w systemie Windows pozwala utworzyć aplikację usługi Service Fabric Mesh i wdrożyć ją w usłudze Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 96549696013a2dd94741090a0a017b57a3b1e19e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125165"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>Konfigurowanie środowiska deweloperskiego w systemie Windows w celu tworzenia aplikacji usługi Service Fabric

Aby kompilować i uruchamiać aplikacje usługi Azure Service Fabric na maszynie deweloperskiej z systemem Windows, należy zainstalować środowisko uruchomieniowe, zestaw SDK oraz narzędzia usługi Service Fabric.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Obsługiwane wersje systemu operacyjnego

Na potrzeby tworzenia aplikacji obsługiwane są następujące wersje systemu operacyjnego:

* Windows 10 (Enterprise, Professional lub Education)
* Windows Server 2016

## <a name="enable-hyper-v"></a>Włączanie funkcji Hyper-V

Tworzenie aplikacji usługi Service Fabric wymaga włączenia funkcji Hyper-V. 

### <a name="windows-10"></a>Windows 10

Otwórz program PowerShell jako administrator i uruchom następujące polecenie:

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

Uruchom ponownie komputer. Aby uzyskać więcej informacji o włączaniu funkcji Hyper-V, zobacz [Install Hyper-V on Windows 10 (Instalowanie funkcji Hyper-V w systemie Windows 10)](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

### <a name="windows-server-2016"></a>Windows Server 2016

Otwórz program PowerShell jako administrator i uruchom następujące polecenie:

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Uruchom ponownie komputer. Aby uzyskać więcej informacji o włączaniu funkcji Hyper-V, zobacz [Install the Hyper-V role on Windows Server 2016 (Instalowanie roli funkcji Hyper-V w systemie Windows Server 2016)](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="visual-studio"></a>Visual Studio

Do wdrażania aplikacji usługi Service Fabric jest wymagany program Visual Studio 2017. [Zainstaluj wersję 15.6.0][download-visual-studio] lub nowszą i włącz następujące obciążenia:

- Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
- Programowanie na platformie Azure

## <a name="docker"></a>Docker

Zainstaluj platformę Docker, umożliwiającą obsługę konteneryzowanych aplikacji usługi Service Fabric używanych w usłudze Service Fabric Mesh.

### <a name="windows-10"></a>Windows 10

Pobierz i zainstaluj najnowszą wersję oprogramowania [Docker Community Edition for Windows][download-docker]. 

Podczas instalacji wybierz opcję **Use Windows containers instead of Linux containers** (Użyj kontenerów systemu Windows zamiast kontenerów systemu Linux), jeśli pojawi się odpowiedni monit. Następnie wyloguj się i zaloguj się ponownie. Po ponownym zalogowaniu może być konieczne włączenie funkcji Hyper-V, jeśli wcześniej tego nie zrobiono. Musisz włączyć funkcję Hyper-V, a następnie ponownie uruchomić komputer.

Po ponownym uruchomieniu komputera platforma Docker wyświetli monit o włączenie funkcji **kontenerów**. Włącz tę funkcję i ponownie uruchom komputer.

### <a name="windows-server-2016"></a>Windows Server 2016

Zainstaluj platformę Docker za pomocą następujących poleceń programu PowerShell. Aby uzyskać więcej informacji, zobacz [Docker Enterprise Edition for Windows Server (Oprogramowanie Docker Enterprise Edition for Windows Server)][download-docker-server].

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Uruchom ponownie komputer.

## <a name="sdk-and-tools"></a>Zestaw SDK i narzędzia

Zainstaluj środowisko uruchomieniowe, zestaw SDK oraz narzędzia usługi Service Fabric Mesh w odpowiedniej kolejności.

1. Zainstaluj [zestaw SDK usługi Service Fabric Mesh][download-sdkmesh] za pomocą Instalatora platformy sieci Web. Spowoduje to również zainstalowanie zestawu SDK i środowiska uruchomieniowego usługi Microsoft Azure Service Fabric.
2. Zainstaluj [rozszerzenie Visual Studio Service Fabric Tools (wersja zapoznawcza)][download-tools] z witryny Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Tworzenie klastra

Aby uzyskać najlepszą wydajność debugowania tworzonych i uruchamianych aplikacji usługi Service Fabric, zaleca się utworzenie lokalnego klastra projektowego z jednym węzłem. Ten klaster musi być uruchomiony podczas każdego wdrażania lub debugowania projektu usługi Service Fabric Mesh.

Utworzenie klastra **wymaga**  uruchomienia platformy Docker. Aby sprawdzić, czy platforma Docker jest uruchomiona, otwórz okno terminalu i uruchom polecenie `docker ps`. Jeśli odpowiedź nie wskazuje na wystąpienie błędu, oznacza to, że platforma Docker jest uruchomiona i można przystąpić do tworzenia klastra.

Po zainstalowaniu środowiska uruchomieniowego, zestawów SDK i narzędzi programu Visual Studio utwórz klaster projektowy.

1. Zamknij okno programu PowerShell.
2. Otwórz nowe okno programu PowerShell jako administrator. Ten krok jest niezbędny do załadowania zainstalowanych modułów usługi Service Fabric.
3. Utwórz klaster projektowy za pomocą następującego polecenia programu PowerShell:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster -UseMachineName
    ```

4. Uruchom menedżera klastra lokalnego za pomocą następującego polecenia programu PowerShell:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

Możesz już tworzyć aplikacje usługi Service Fabric Mesh.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkiem [Create an Azure Service Fabric app (Tworzenie aplikacji usługi Azure Service Fabric)](service-fabric-mesh-tutorial-create-dotnetcore.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.ServiceFabricMesh
[download-visual-studio]: https://www.visualstudio.com/downloads/
