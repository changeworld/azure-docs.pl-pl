---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69520866"
---
W poniższych krokach użyto następującej konfiguracji:

  | | |
  |---|---|
  |Computer (Komputer)| Ubuntu Server 18.04|
  |Zależności| strongSwan (silnySwan) |


Użyj następujących poleceń, aby zainstalować wymaganą konfigurację strongSwan:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Użyj następującego polecenia, aby zainstalować interfejs wiersza polecenia platformy Azure:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Dodatkowe instrukcje dotyczące instalowania interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
