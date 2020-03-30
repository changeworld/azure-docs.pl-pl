---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: b310de560f9791e1fc49d54dfbf0789c38d37f57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594019"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Pobierz i zainstaluj binarny klienta Istio istioctl

W powłoce opartej na bash na Linuksie lub [Podsystemie Windows dla Linuksa][install-wsl]użyj `curl` do pobrania wydania Istio, a następnie wyodrębnić z `tar` następującymi wersjami:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Plik `istioctl` binarny klienta działa na komputerze klienckim i umożliwia interakcję z siatką usługi Istio. Użyj następujących poleceń, aby zainstalować `istioctl` plik binarny klienta Istio w powłoce opartej na bash na Linuksie lub [Podsystemie Windows dla systemu Linux][install-wsl]. Polecenia te kopiują `istioctl` plik binarny klienta `PATH`do standardowej lokalizacji programu użytkownika w programie .

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Jeśli chcesz zakończyć wiersz polecenia dla pliku `istioctl` binarnego klienta Istio, należy skonfigurować go w następujący sposób:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10