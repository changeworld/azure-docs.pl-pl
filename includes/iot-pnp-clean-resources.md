---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75453695"
---
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować dodatkowe artykuły typu Plug and Play usługi IoT, możesz zachować i ponownie użyć zasobów użytych w tym przewodniku Szybki start. W przeciwnym razie można usunąć zasoby utworzone w tym przewodniku Szybki start, aby uniknąć dodatkowych opłat.

Można usunąć zarówno koncentratora i zarejestrowane urządzenie jednocześnie, usuwając całą grupę zasobów za pomocą następującego polecenia dla interfejsu wiersza polecenia platformy Azure. (Nie należy jednak używać tego, jeśli te zasoby współużytkują grupę zasobów z innymi zasobami, które masz do różnych celów).

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Aby usunąć tylko centrum IoT, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Aby usunąć tylko tożsamość urządzenia zarejestrowaną w centrum IoT hub, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Można również usunąć sklonowane przykładowe pliki z komputera dewelopera.