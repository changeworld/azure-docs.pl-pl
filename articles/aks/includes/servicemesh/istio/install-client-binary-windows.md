---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1c63d02a2207a396183be4fdcc44074977d37b1a
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530370"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Pobierz i zainstaluj dane binarne klienta Istio istioctl

W przypadku powłoki opartej na programie PowerShell w systemie Windows użyj `Invoke-WebRequest`, aby pobrać wersję Istio, a następnie wyodrębnij ją z `Expand-Archive` w następujący sposób:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.3.2"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

@No__t_0 dane binarne klienta są uruchamiane na komputerze klienckim i umożliwiają współdziałanie z siatką usługi Istio. Użyj następujących poleceń, aby zainstalować dane binarne klienta Istio `istioctl` w powłoce opartej na programie PowerShell w systemie Windows. Te polecenia kopiują dane binarne klienta `istioctl` do folderu Istio, a następnie udostępniają je natychmiast (w bieżącej powłoce) i trwale (w ramach ponownych uruchomień powłoki) za pośrednictwem `PATH`. Nie musisz mieć podniesionych uprawnień (Administrator) do uruchamiania tych poleceń i nie musisz ponownie uruchamiać powłoki.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```