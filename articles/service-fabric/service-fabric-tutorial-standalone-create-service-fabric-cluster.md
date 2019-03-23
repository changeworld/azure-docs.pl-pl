---
title: 'Samouczek: instalowanie klienta autonomicznego usługi Service Fabric — Azure Service Fabric | Microsoft Docs'
description: W tym samouczku dowiesz się, jak zainstalować klienta autonomicznego usługi Service Fabric w klastrze utworzonym w poprzedniej części samouczka.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 9563faf5e72bdef2e43afe26d5e8ae0f3d1c67ad
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370418"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>Samouczek: Instalowanie i tworzenie klastra usługi Service Fabric

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. Ta seria samouczków przedstawia proces tworzenia klastra autonomicznego hostowanego w usłudze AWS i instalowania w nim aplikacji.

Ten samouczek jest drugą częścią serii. W tym samouczku przedstawiono kroki tworzenia klastra autonomicznego usługi Service Fabric.

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Pobieranie i instalowanie pakietu autonomicznego usługi Service Fabric
> * Tworzenie klastra usługi Service Fabric
> * Łączenie się z klastrem usługi Service Fabric

## <a name="download-the-service-fabric-for-windows-server-package"></a>Pobieranie pakietu usługi Service Fabric dla systemu Windows Server

Usługa Service Fabric udostępnia pakiet instalacyjny służący do tworzenia autonomicznych klastrów usługi Service Fabric.  [Pobierz pakiet instalacyjny](https://go.microsoft.com/fwlink/?LinkId=730690) na komputer lokalny.  Po pomyślnym pobraniu pakietu skopiuj go za pośrednictwem połączenia protokołu RDP do wystąpienia usługi EC2 i wklej go na Pulpicie.

Zaznacz plik zip, otwórz menu kontekstowe i wybierz kolejno pozycje **Wyodrębnij wszystko** > **Wyodrębnij**.  Podczas wyodrębniania plików na pulpicie zostanie wygenerowany folder o takiej samej nazwie, jaką miał plik zip.

Możesz uzyskać bardziej szczegółowe informacje dotyczące [zawartości pakietu instalacyjnego](service-fabric-cluster-standalone-package-contents.md).

## <a name="set-up-your-configuration-file"></a>Konfigurowanie pliku konfiguracji

Ponieważ tworzysz klaster z trzema węzłami z systemem Windows, musisz zmodyfikować plik `ClusterConfig.Unsecure.MultiMachine.json`.

Następnie zmień trzy wiersze ipAddress, znajdujące się w wierszach 8, 15 i 22 w pliku, na adresy IP poszczególnych wystąpień.

Zaktualizowane węzły będą wyglądać następująco:

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

Następnie należy zaktualizować kilka właściwości.  W wierszu 34 zmodyfikuj parametry połączenia magazynu diagnostycznego. Po modyfikacji powinny wyglądać następująco: `"connectionstring": "C:\\ProgramData\\SF\\DiagnosticsStore"`

Na koniec w sekcji `nodeTypes` konfiguracji należy dodać nową sekcję na potrzeby mapowania portów efemerycznych używanych przez system Windows.  Plik konfiguracji powinien wyglądać następująco:

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>Weryfikowanie środowiska

Skrypt *TestConfiguration.ps1* w pakiecie autonomicznym jest używany jako analizator najlepszych rozwiązań, który weryfikuje, czy można wdrożyć klaster w danym środowisku. Listę wymagań wstępnych i wymagań dotyczących środowiska można znaleźć w temacie [Deployment preparation](service-fabric-cluster-standalone-deployment-preparation.md) (Przygotowywanie środowiska). Uruchom skrypt, aby zweryfikować, czy można utworzyć klaster programowania:

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

Powinny pojawić się następujące dane wyjściowe. Jeśli w polu „Passed” na końcu zwracana jest wartość `True`, oznacza to, że test poprawności zakończył się pozytywnie, a konfiguracja wejściowa powinna umożliwiać wdrożenie klastra.

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>Tworzenie klastra

Po pomyślnym zweryfikowaniu konfiguracji klastra uruchom skrypt *CreateServiceFabricCluster.ps1*, aby wdrożyć klaster usługi Service Fabric na maszynach wirtualnych wskazanych w pliku konfiguracji.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

Jeśli wszystko przebiegnie poprawnie, zostaną zwrócone następujące dane wyjściowe:

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> Ślady wdrożenia są zapisywane na maszynie lub maszynie wirtualnej, na której uruchomiono skrypt programu PowerShell CreateServiceFabricCluster.ps1. Znajdują się w podfolderze DeploymentTraces w katalogu, w którym uruchomiono skrypt. Aby sprawdzić, czy usługa Service Fabric została prawidłowo wdrożona na maszynie, poszukaj zainstalowanych plików w katalogu FabricDataRoot, zgodnie z sekcją FabricSettings w pliku konfiguracji klastra (domyślnie c:\ProgramData\SF). Ponadto w Menedżerze zadań możesz zobaczyć uruchomione procesy FabricHost.exe i Fabric.exe.
>
>

### <a name="bring-up-service-fabric-explorer"></a>Otwórz narzędzie Service Fabric Explorer

Teraz możesz łączyć się z klastrem przy użyciu narzędzia Service Fabric Explorer bezpośrednio z poziomu jednej z maszyn za pośrednictwem protokołu http:\//localhost:19080/Explorer/index.html lub zdalnie za pośrednictwem protokołu http:\/ /<  *IPAddressofaMachine*>: 19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Dodawanie i usuwanie węzłów

Możesz dodawać i usuwać węzły w klastrze autonomicznym usługi Service Fabric stosownie do zmieniających się potrzeb biznesowych. Aby uzyskać szczegółowe instrukcje, zobacz [Add or Remove nodes to a Service Fabric standalone cluster (Dodawanie i usuwanie węzłów w klastrze autonomicznym usługi Service Fabric)](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="next-steps"></a>Następne kroki

W drugiej części serii omówiono równoległe przekazywanie dużej ilości danych losowych na konto magazynu, w tym następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie parametrów połączenia
> * Kompilowanie aplikacji
> * Uruchamianie aplikacji
> * Weryfikowanie liczby połączeń

Przejdź do trzeciej części serii, aby dowiedzieć się, jak zainstalować aplikację w utworzonym klastrze.

> [!div class="nextstepaction"]
> [Install the application into the service fabric cluster (Instalowanie aplikacji w klastrze usługi Service Fabric)](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
