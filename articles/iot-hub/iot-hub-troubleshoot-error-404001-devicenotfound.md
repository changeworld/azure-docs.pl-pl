---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 404001 DeviceNotFound
description: Dowiedz się, jak naprawić błąd 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960830"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

W tym artykule opisano przyczyny i rozwiązania dla **błędów DeviceNotFound 404001.**

## <a name="symptoms"></a>Objawy

Podczas komunikacji z chmury do urządzenia (C2D), takiej jak komunikat C2D, podwójna aktualizacja lub metoda bezpośrednia, operacja kończy się niepowodzeniem z powodu błędu **404001 DeviceNotFound**.

## <a name="cause"></a>Przyczyna

Operacja nie powiodła się, ponieważ nie można odnaleźć urządzenia przez centrum IoT Hub. Urządzenie nie jest zarejestrowane ani wyłączone.

## <a name="solution"></a>Rozwiązanie

Zarejestruj używany identyfikator urządzenia, a następnie spróbuj ponownie.