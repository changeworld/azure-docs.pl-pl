---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b7d832ba375925d30a976dfde63a776b5d0742bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593737"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Pobierz i zainstaluj plik binarny klienta linkerd

W powłoce opartej na programie `Invoke-WebRequest` PowerShell w systemie Windows użyj do pobrania wersji Linkerd w następujący sposób:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

Plik `linkerd` binarny klienta działa na komputerze klienckim i umożliwia interakcję z siatką usługi Linkerd. Użyj następujących poleceń, aby zainstalować `linkerd` plik binarny klienta Linkerd w powłoce opartej na programie PowerShell w systemie Windows. Polecenia te kopiują `linkerd` plik binarny klienta do folderu Linkerd, a następnie udostępniają go zarówno `PATH`natychmiast (w bieżącej powłoce), jak i na stałe (w całej powłoce restartuje) za pośrednictwem pliku . Do uruchamiania tych poleceń nie trzeba mieć uprawnień z podwyższonym poziomem uprawnień (Administrator) i nie trzeba ponownie uruchamiać powłoki.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```