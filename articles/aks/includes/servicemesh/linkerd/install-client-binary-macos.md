---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593738"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Pobierz i zainstaluj plik binarny klienta linkerd

W powłoce opartej na `curl` bash na MacOS użyj do pobrania wersji Linkerd w następujący sposób:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

Plik `linkerd` binarny klienta działa na komputerze klienckim i umożliwia interakcję z siatką usługi Linkerd. Użyj następujących poleceń, aby zainstalować `linkerd` plik binarny klienta Linkerd w powłoce opartej na bash na macos. Polecenia te kopiują `linkerd` plik binarny klienta `PATH`do standardowej lokalizacji programu użytkownika w programie .

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Jeśli chcesz zakończyć wiersz polecenia dla pliku `linkerd` binarnego klienta Linkerd, należy skonfigurować go w następujący sposób:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
