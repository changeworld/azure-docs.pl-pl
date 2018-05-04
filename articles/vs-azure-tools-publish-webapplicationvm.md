---
title: Publikowanie WebApplicationVM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć aplikację sieci web do maszyny wirtualnej. Ten skrypt tworzy wymaganych zasobów w Twojej subskrypcji platformy Azure, jeśli nie istnieją.
services: visual-studio-online
author: ghogen
manager: douge
assetId: de4cec95-f73f-44d9-babd-9f47f2633cdb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: 3fdd11387096d95359fb5f578ca64720f2182c45
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publikowanie WebApplicationVM (skrypt programu Windows PowerShell)
Wdraża aplikację sieci web do maszyny wirtualnej. Skrypt tworzy wymaganych zasobów w Twojej subskrypcji platformy Azure, jeśli nie istnieją.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Konfigurowanie
Ścieżka do pliku konfiguracji JSON, który opisuje szczegóły wdrożenia.

| Aliasy | brak |
| --- | --- |
| Wymagana? |true |
| Położenie |o nazwie |
| Wartość domyślna |brak |
| Akceptowanie danych wejściowych potoku? |false |
| Akceptowanie symboli wieloznacznych? |false |

### <a name="subscriptionname"></a>Nazwa subskrypcji
Nazwa subskrypcji platformy Azure, w którym chcesz utworzyć maszynę wirtualną.

| Aliasy | brak |
| --- | --- |
| Wymagana? |false |
| Położenie |o nazwie |
| Wartość domyślna |Używa pierwszej subskrypcji w pliku subskrypcji |
| Akceptowanie danych wejściowych potoku? |false |
| Akceptowanie symboli wieloznacznych? |false |

### <a name="webdeploypackage"></a>WebDeployPackage
Ścieżka do pakietu wdrożeniowego sieci web do publikowania do maszyny wirtualnej. Ten pakiet można utworzyć za pomocą kreatora Publikowanie w sieci Web w programie Visual Studio. Zobacz [jak: utworzyć pakiet wdrożeniowy sieci Web w programie Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

| Aliasy | brak |
| --- | --- |
| Wymagana? |false |
| Położenie |o nazwie |
| Wartość domyślna |brak |
| Akceptowanie danych wejściowych potoku? |false |
| Akceptowanie symboli wieloznacznych? |false |

### <a name="allowuntrusted"></a>AllowUntrusted
Jeśli PRAWDA, Zezwalaj na korzystanie z certyfikatów, które nie są podpisane przez zaufanego głównego urzędu.

| Aliasy | brak |
| --- | --- |
| Wymagana? |false |
| Położenie |o nazwie |
| Wartość domyślna |false |
| Akceptowanie danych wejściowych potoku? |false |
| Akceptowanie symboli wieloznacznych? |false |

### <a name="vmpassword"></a>VMPassword
Poświadczenia dla konta maszyny wirtualnej. Przykład: - VMPassword @{nazwa = "admin"; Hasło = "password"}

| Aliasy | brak |
| --- | --- |
| Wymagana? |false |
| Położenie |o nazwie |
| Wartość domyślna |brak |
| Akceptowanie danych wejściowych potoku? |false |
| Akceptowanie symboli wieloznacznych? |false |

### <a name="databaseserverpassword"></a>DatabaseServerPassword
Poświadczenia bazy danych SQL platformy Azure. Przykład: - DatabaseServerPassword @{nazwa = "admin"; Hasło = "password"}

| Aliasy | brak |
| --- | --- |
| Wymagana? |false |
| Położenie |o nazwie |
| Wartość domyślna |brak |
| Akceptowanie danych wejściowych potoku? |false |
| Akceptowanie symboli wieloznacznych? |false |

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Jeśli PRAWDA, Drukuj komunikaty z skrypt do strumienia wyjściowego.

| Aliasy | brak |
| --- | --- |
| Wymagana? |false |
| Położenie |o nazwie |
| Wartość domyślna |false |
| Akceptowanie danych wejściowych potoku? |false |
| Akceptowanie symboli wieloznacznych? |false |

## <a name="remarks"></a>Uwagi
Pełny opis sposobów użycia skryptu do tworzenia środowisk do tworzenia i testowania, zobacz [za pomocą skryptów programu PowerShell systemu Windows do opublikowania deweloperów i środowisk testowych](vs-azure-tools-publishing-using-powershell-scripts.md).

Plik JSON konfiguracji określa szczegóły co ma zostać wdrożona. Zawiera informacje, które zostało określone podczas tworzenia projektu, takie jak nazwa grupy koligacji, obrazu wirtualnego dysku twardego i rozmiar maszyny wirtualnej. Także zawiera punkty końcowe na maszynie wirtualnej, baz danych, aby było możliwe, jeśli istnieje i parametry wdrażania w sieci web. Poniższy kod przedstawia przykładowy plik konfiguracji JSON:

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Można edytować plik JSON konfiguracji do zmiany, jakie są obsługiwane. Maszyny wirtualne i usługi w chmurze są wymagane, ale w sekcji bazy danych jest opcjonalna.

