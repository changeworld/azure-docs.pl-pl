---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: e6ca830a7224326c17003924b76192b95ba6bcb2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152008"
---
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuowanie pracy z dodatkowymi artykułami Plug and Play IoT, możesz zachować i ponownie użyć zasobów używanych w tym przewodniku Szybki Start. W przeciwnym razie możesz usunąć zasoby utworzone w tym przewodniku Szybki Start, aby uniknąć dodatkowych opłat.

Jednocześnie można usunąć zarówno centrum, jak i zarejestrowane urządzenie, usuwając całą grupę zasobów za pomocą poniższego polecenia dla interfejsu CLI platformy Azure. (Nie używaj tego, jednak jeśli te zasoby współużytkują grupę zasobów z innymi zasobami w różnych celach).

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Aby usunąć tylko usługę IoT Hub, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Aby usunąć tylko tożsamość urządzenia zarejestrowana w usłudze IoT Hub, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Można również usunąć sklonowane pliki zestawu SDK z komputera deweloperskiego.