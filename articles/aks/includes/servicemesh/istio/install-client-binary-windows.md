---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: a1a608b4f4e1afe4a3cf99229561a91cc3f9fa96
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170797"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Pobierz i zainstaluj dane binarne klienta Istio istioctl

W przypadku powłoki opartej na programie PowerShell w systemie Windows użyj `Invoke-WebRequest`, aby pobrać wersję Istio, a następnie wyodrębnij ją z `Expand-Archive` w następujący sposób:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

`istioctl` dane binarne klienta są uruchamiane na komputerze klienckim i umożliwiają współdziałanie z siatką usługi Istio. Użyj następujących poleceń, aby zainstalować dane binarne klienta Istio `istioctl` w powłoce opartej na programie PowerShell w systemie Windows. Te polecenia kopiują dane binarne klienta `istioctl` do folderu Istio, a następnie udostępniają je natychmiast (w bieżącej powłoce) i trwale (w ramach ponownych uruchomień powłoki) za pośrednictwem `PATH`. Nie musisz mieć podniesionych uprawnień (Administrator) do uruchamiania tych poleceń i nie musisz ponownie uruchamiać powłoki.

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