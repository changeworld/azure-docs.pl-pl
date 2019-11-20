---
title: Konfigurowanie środowiska deweloperskiego w systemie Windows dla mikrousług platformy Azure | Microsoft Docs
description: Zainstaluj środowisko uruchomieniowe, zestaw SDK i narzędzia oraz utwórz lokalny klaster projektowy. Po ukończeniu tej konfiguracji wszystko będzie gotowe do kompilowania aplikacji w systemie Windows.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/18/2019
ms.author: atsenthi
ms.openlocfilehash: 1eb8e5893d93ef0261d68df4629b072876096711
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185676"
---
# <a name="prepare-your-development-environment-on-windows"></a>Przygotowywanie środowiska deweloperskiego w systemie Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Aby kompilować i uruchamiać [aplikacje Service Fabric platformy Azure][1] na komputerze deweloperskim systemu Windows, zainstaluj Service Fabric środowisko uruchomieniowe, zestaw SDK i narzędzia. Należy również [włączyć wykonywanie skryptów programu Windows PowerShell](#enable-powershell-script-execution) zawartych w zestawie SDK.

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="supported-operating-system-versions"></a>Obsługiwane wersje systemu operacyjnego
Na potrzeby tworzenia aplikacji obsługiwane są następujące wersje systemu operacyjnego:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Obsługa systemu Windows 7:
> - System Windows 7 domyślnie zawiera program Windows PowerShell wyłącznie w wersji 2.0. Polecenia cmdlet programu PowerShell usługi Service Fabric wymagają programu PowerShell w wersji 3.0 lub nowszej. [Środowisko Windows PowerShell 5,0 można pobrać][powershell5-download] z centrum pobierania Microsoft.
> - Serwer proxy usługi Service Fabric nie jest dostępny w systemie Windows 7.
>

## <a name="install-the-sdk-and-tools"></a>Instalowanie zestawu SDK i narzędzi
Instalator platformy sieci Web (Instalatora WebPI) jest zalecanym sposobem instalowania zestawu SDK i narzędzi. Jeśli wystąpią błędy środowiska uruchomieniowego za pomocą Instalatora WebPI, można także znaleźć bezpośrednie linki do instalatorów w informacjach o wersji dla określonej wersji Service Fabric. Informacje o wersji znajdują się w różnych anonsach wydań na [blogu zespołu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

> [!NOTE]
> Uaktualnienia lokalnego klastra projektowego Service Fabric nie są obsługiwane.

### <a name="to-use-visual-studio-2017-or-2019"></a>Aby użyć programu Visual Studio 2017 lub 2019
Service Fabric Tools są częścią obciążeń programistycznych platformy Azure w programie Visual Studio 2017 i 2019. Włącz to obciążenie w ramach instalacji programu Visual Studio.
Ponadto należy zainstalować zestaw SDK i środowisko uruchomieniowe usługi Microsoft Azure Service Fabric przy użyciu Instalatora platformy sieci Web.

* [Zainstaluj zestaw SDK usługi Microsoft Azure Service Fabric][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Używanie programu Visual Studio 2015 (wymaga programu Visual Studio 2015 Update 2 lub nowszego)
W przypadku programu Visual Studio 2015 narzędzia usługi Service Fabric są instalowane razem z zestawem SDK i środowiskiem uruchomieniowym przy użyciu Instalatora platformy sieci Web:

* [Instalowanie zestawu SDK Microsoft Azure Service Fabric i narzędzi][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Instalowanie samego zestawu SDK
Jeśli potrzebujesz tylko zestawu SDK, możesz zainstalować ten pakiet:
* [Zainstaluj zestaw SDK usługi Microsoft Azure Service Fabric][core-sdk]

Bieżące wersje:
* Service Fabric zestawu SDK i narzędzi 4.0.457
* 7\.0.457 środowiska uruchomieniowego Service Fabric

Aby uzyskać listę obsługiwanych wersji, zobacz [Service Fabric wersje](service-fabric-versions.md)

> [!NOTE]
> Klastry pojedynczej maszyny (jednopunktowy) nie są obsługiwane w przypadku uaktualnień aplikacji lub klastra. Usuń klaster jednopunktowy i utwórz go ponownie, jeśli konieczne jest przeprowadzenie uaktualnienia klastra lub problemy z uaktualnieniem aplikacji. 

## <a name="enable-powershell-script-execution"></a>Włączanie wykonywania skryptów programu PowerShell
Platforma Service Fabric korzysta ze skryptów programu Windows PowerShell do tworzenia lokalnego klastra projektowego i do wdrażania aplikacji z programu Visual Studio. Domyślnie system Windows blokuje uruchamianie tych skryptów. Aby je włączyć, należy zmienić zasady wykonywania w programie PowerShell. Uruchom program PowerShell jako administrator i wprowadź następujące polecenie:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>Zainstaluj platformę Docker (opcjonalnie)
[Service Fabric to koordynator kontenera](service-fabric-containers-overview.md) służący do wdrażania mikrousług w klastrze maszyn. Aby uruchamiać aplikacje kontenera systemu Windows w lokalnym klastrze programistycznym, należy najpierw zainstalować Docker for Windows. Pobierz [Docker CE for Windows (stabilny)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Po zainstalowaniu i uruchomieniu programu Docker kliknij prawym przyciskiem myszy jego ikonę na pasku zadań i wybierz pozycję **Switch to Windows containers** (Przełącz na kontenery systemu Windows). Ten krok jest wymagany do uruchomienia obrazów platformy Docker opartych na systemie Windows.

## <a name="next-steps"></a>Kolejne kroki
Po skonfigurowaniu środowiska projektowego możesz zacząć kompilować i uruchamiać aplikacje.

* [Dowiedz się, jak tworzyć i wdrażać aplikacje oraz zarządzać nimi](service-fabric-tutorial-create-dotnet-app.md)
* [Informacje o modelach programowania: Reliable Services i Reliable Actors](service-fabric-choose-framework.md)
* [Przykłady kodu platformy Service Fabric w witrynie GitHub](https://aka.ms/servicefabricsamples)
* [Wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Strona kampanii usługi Service Fabric"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI — link"
[full-bundle-dev15]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI — link"
[core-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI — link"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
