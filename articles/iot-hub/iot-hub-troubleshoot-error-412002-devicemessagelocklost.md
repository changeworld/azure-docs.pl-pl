---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 412002 DeviceMessageLockLost
description: Dowiedz się, jak naprawić błąd 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960765"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

W tym artykule opisano przyczyny i rozwiązania dla **412002 DeviceMessageLockLost** błędów.

## <a name="symptoms"></a>Objawy

Podczas próby wysłania komunikatu z chmury do urządzenia żądanie kończy się niepowodzeniem z błędem **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Przyczyna

Gdy urządzenie odbiera komunikat chmury do urządzenia z kolejki (na przykład przy użyciu) [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)wiadomość jest zablokowana przez Centrum IoT na czas trwania limitu czasu blokady jednej minuty. Jeśli urządzenie próbuje ukończyć komunikat po upływie limitu czasu blokady, Usługa IoT Hub zgłasza ten wyjątek.

## <a name="solution"></a>Rozwiązanie

Jeśli Usługa IoT Hub nie otrzyma powiadomienia w czasie trwania limitu czasu blokady jednej minuty, ustawia komunikat z powrotem do stanu *w kolejce.* Urządzenie może ponownie podjąć próbę odebrania wiadomości. Aby zapobiec wystąpieniu błędu w przyszłości, należy zaimplementować logikę po stronie urządzenia, aby zakończyć komunikat w ciągu jednej minuty od odebrania wiadomości. Nie można zmienić tego jednominutowego przesuwu.