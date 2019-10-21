---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 2a1249d134c23f47f939913fa1c9887d2a8e1f63
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72601060"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Pobieranie i Instalowanie konsolidatora dwuskładnikowego klienta konsolidatora

W przypadku powłoki opartej na programie PowerShell w systemie Windows należy użyć `Invoke-WebRequest`, aby pobrać konsolidator w następujący sposób:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

@No__t_0 dane binarne klienta są uruchamiane na komputerze klienckim i umożliwiają współdziałanie z konsolidatorem łączącej usługi. Użyj następujących poleceń, aby zainstalować `linkerd` dane binarne klienta w środowisku programu PowerShell w systemie Windows. Te polecenia kopiują dane binarne klienta `linkerd` do folderu konsolidatora, a następnie udostępniają je natychmiast (w bieżącej powłoce) i trwale (w ramach ponownych uruchomień powłoki) za pośrednictwem `PATH`. Nie musisz mieć podniesionych uprawnień (Administrator) do uruchamiania tych poleceń i nie musisz ponownie uruchamiać powłoki.

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