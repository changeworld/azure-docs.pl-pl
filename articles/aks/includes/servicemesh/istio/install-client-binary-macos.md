---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 74f5b22ccc822a188059b29d9c661a15cf8412bf
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594020"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Pobierz i zainstaluj dane binarne klienta Istio istioctl

W bash Shell na MacOS, użyj `curl`, aby pobrać wersję Istio, a następnie wyodrębnij z `tar` w następujący sposób:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

`istioctl` dane binarne klienta są uruchamiane na komputerze klienckim i umożliwiają współdziałanie z siatką usługi Istio. Użyj następujących poleceń, aby zainstalować dane binarne klienta Istio `istioctl` w bash Shell na MacOS. Te polecenia kopiują plik binarny klienta `istioctl` do lokalizacji standardowego programu użytkownika w `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Jeśli chcesz, aby program Istio `istioctl` dane binarne klienta, a następnie skonfiguruj go w następujący sposób:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```