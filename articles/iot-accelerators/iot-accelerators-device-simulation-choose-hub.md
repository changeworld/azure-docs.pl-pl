---
title: Korzystanie z istniejącego centrum IoT hub z rozwiązaniem device simulation — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania akceleratora rozwiązań symulacji urządzeń do używania istniejącego centrum IoT Hub.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889194"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Use an existing IoT hub with the Device Simulation solution accelerator (Używanie istniejącego centrum IoT Hub z akceleratorem rozwiązania do symulacji urządzenia)

Podczas wdrażania symulacji urządzenia można również wdrożyć centrum IoT hub do użycia w symulacji. Ta opcja wdraża [koncentrator IoT warstwy S2 z pojedynczą jednostką skalowania.](../iot-hub/iot-hub-scaling.md) Jeśli wdrożysz to opcjonalne centrum IoT, nadal można wybrać miejsce docelowe innego Centrum IoT dla uruchomienia symulacji.

Jeśli nie chcesz wdrażać opcjonalnego centrum IoT Hub, musisz użyć własnego koncentratora dla wszystkich uruchomionych symulacji.

Jeśli nie masz centrum IoT hub, zawsze możesz utworzyć nowy z [witryny Azure Portal.](https://portal.azure.com)

Aby użyć istniejącego koncentratora IoT, potrzebujesz ciągu połączenia dla zasad dostępu współużytkowego **iothubowner.** Ten ciąg połączenia można uzyskać z [witryny Azure portal:](https://portal.azure.com)

1. Na stronie konfiguracji centrum w portalu kliknij pozycję **Zasady dostępu udostępnionego**.

1. Kliknij **iothubowner**.

1. Skopiuj podstawowy lub pomocniczy ciąg połączenia.

[![Pobieranie parametrów połączenia](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Użyj ciągu połączenia skopiowanego podczas konfigurowania symulacji:

![Konfigurowanie symulacji](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Następne kroki

W tym przewodniku in how-to dowiesz się, jak używać istniejącego centrum IoT w symulacji. Następnie możesz dowiedzieć się, jak [utworzyć zaawansowany model urządzenia](iot-accelerators-device-simulation-advanced-device.md) dla symulacji.
