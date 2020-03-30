---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: a3340eb9e53afa83c35109bad7d22f81413dd644
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80234264"
---
## <a name="prepare-an-iot-hub"></a>Przygotowywanie centrum IoT

Aby ukończyć ten przewodnik Szybki start, potrzebujesz również centrum Azure IoT hub w ramach subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem. Jeśli nie masz centrum IoT, postępuj zgodnie z [tymi instrukcjami, aby go utworzyć.](../articles/iot-hub/iot-hub-create-using-cli.md)

Jeśli korzystasz z interfejsu wiersza polecenia platformy Azure lokalnie, najpierw zaloguj się do subskrypcji platformy Azure przy użyciu `az login`. Jeśli używasz tych poleceń w usłudze Azure Cloud Shell, zalogujesz się automatycznie.

Jeśli używasz interfejsu wiersza polecenia `az` platformy Azure lokalnie, wersja powinna być **2.0.73** lub nowsza; usługa Azure Cloud Shell używa najnowszej wersji. Użyj `az --version` polecenia, aby sprawdzić wersję zainstalowaną na komputerze.

Uruchom następujące polecenie, aby dodać rozszerzenie IoT usługi Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia:

```azurecli-interactive
az extension add --name azure-iot
```

Uruchom następujące polecenie, aby utworzyć tożsamość urządzenia w centrum IoT hub. Zastąp symbole zastępcze **YourIoTHubName** i **YourDeviceID** własną _nazwą Centrum IoT_ i wybranym _identyfikatorem urządzenia._

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Uruchom następujące polecenie, aby uzyskać _parametry połączenia urządzenia_ dla zarejestrowanego właśnie urządzenia (uwaga do późniejszego użycia):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
