---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 409001 DeviceAlreadyExists
description: Dowiedz się, jak naprawić błąd 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960791"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

W tym artykule opisano przyczyny i rozwiązania dla **409001 DeviceAlreadyExists** błędów.

## <a name="symptoms"></a>Objawy

Podczas próby zarejestrowania urządzenia w Centrum IoT, żądanie kończy się niepowodzeniem z błędem **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Przyczyna

W centrum IoT hub jest już urządzenie o tym samym identyfikatorze urządzenia. 

## <a name="solution"></a>Rozwiązanie

Użyj innego identyfikatora urządzenia i spróbuj ponownie.