---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 562382cc1cfb6adb7e65d76e717df4c4e2962ba7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594007"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Pobierz i zainstaluj binarny klienta Istio istioctl

W powłoce opartej na programie `Invoke-WebRequest` PowerShell w systemie Windows `Expand-Archive` użyj do pobrania wersji Istio, a następnie wyodrębnij z następującymi czynnościami:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Plik `istioctl` binarny klienta działa na komputerze klienckim i umożliwia interakcję z siatką usługi Istio. Użyj następujących poleceń, aby zainstalować `istioctl` plik binarny klienta Istio w powłoce opartej na programie PowerShell w systemie Windows. Polecenia te kopiują `istioctl` plik binarny klienta do folderu Istio, a następnie udostępniają go zarówno `PATH`natychmiast (w bieżącej powłoce), jak i na stałe (w całej powłoce restartuje) za pośrednictwem pliku . Do uruchamiania tych poleceń nie trzeba mieć uprawnień z podwyższonym poziomem uprawnień (Administrator) i nie trzeba ponownie uruchamiać powłoki.

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