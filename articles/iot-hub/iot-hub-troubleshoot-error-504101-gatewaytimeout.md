---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 504101 GatewayTimeout
description: Dowiedz się, jak naprawić błąd 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960674"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

W tym artykule opisano przyczyny i rozwiązania **504101 błędów GatewayTimeout** .

## <a name="symptoms"></a>Objawy

Gdy próba wywołania metody bezpośredniej z IoT Hub na urządzenie, żądanie kończy się niepowodzeniem z błędem **504101 GatewayTimeout**.

## <a name="cause"></a>Przyczyna

### <a name="cause-1"></a>Przyczyny 1

IoT Hub napotkał błąd i nie może potwierdzić, czy metoda bezpośrednia została ukończona przed upływem limitu czasu.

### <a name="cause-2"></a>Przyczyny 2

W przypadku korzystania ze starszej wersji zestawu Azure C# IoT SDK (< 1.19.0), link AMQP między urządzeniem i IoT Hub może zostać porzucony dyskretnie z powodu błędu.

## <a name="solution"></a>Rozwiązanie

### <a name="solution-1"></a>Rozwiązanie 1

Wydaj ponowną próbę.

### <a name="solution-2"></a>Rozwiązanie 2

Uaktualnij do najnowszej wersji zestawu Azure IOT C# SDK.