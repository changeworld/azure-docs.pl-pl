---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 3f3e7c54ec59c42ed102da682fc9aa077709ef3f
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262634"
---
## <a name="prepare-an-iot-hub"></a>Przygotowywanie Centrum IoT Hub

Aby ukończyć ten przewodnik Szybki Start, potrzebujesz również usługi Azure IoT Hub w ramach subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Jeśli nie masz Centrum IoT Hub, postępuj zgodnie [z tymi instrukcjami, aby je utworzyć](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> W publicznej wersji zapoznawczej funkcje Plug and Play IoT są dostępne tylko w centrach IoT, które zostały utworzone w regionach **środkowe stany USA**, **Europa Północna**i **Japonia Wschodnia** .

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, najpierw Zaloguj się do subskrypcji platformy Azure przy użyciu `az login`. Jeśli uruchamiasz te polecenia w Azure Cloud Shell, nastąpi automatyczne logowanie.

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, wersja `az` powinna być **2.0.73** lub nowsza; Azure Cloud Shell używa najnowszej wersji. Aby sprawdzić wersję zainstalowaną na komputerze, użyj polecenia `az --version`.

Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia:

```azurecli-interactive
az extension add --name azure-iot
```

Uruchom następujące polecenie, aby utworzyć tożsamość urządzenia w centrum IoT Hub. Zastąp symbole zastępcze **YourIoTHubName** i **YourDeviceID** własną _nazwą IoT Hub_ i wybranym przez siebie _identyfikatorem urządzenia_ .

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Uruchom następujące polecenie, aby pobrać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia (Uwaga do użycia później):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```