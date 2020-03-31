---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 74f5b22ccc822a188059b29d9c661a15cf8412bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594020"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Pobierz i zainstaluj binarny klienta Istio istioctl

W powłoce opartej na `curl` bash na MacOS użyj do `tar` pobrania wydania Istio, a następnie wyodrębnij z następującymi czynnościami:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Plik `istioctl` binarny klienta działa na komputerze klienckim i umożliwia interakcję z siatką usługi Istio. Użyj następujących poleceń, aby zainstalować `istioctl` plik binarny klienta Istio w powłoce opartej na bash na MacOS. Polecenia te kopiują `istioctl` plik binarny klienta `PATH`do standardowej lokalizacji programu użytkownika w programie .

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