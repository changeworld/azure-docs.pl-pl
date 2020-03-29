---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 400027 ConnectionForcefullyClosedOnNewConnection
description: Dowiedz się, jak naprawić błąd 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960531"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

W tym artykule opisano przyczyny i rozwiązania dla błędów **ConnectionForcefullyClosedOnNewConnection 400027.**

## <a name="symptoms"></a>Objawy

Operacja bliźniaczej reprezentacji urządzenia do chmury (np. właściwości odczytu lub aktualizacji zgłoszonych) lub bezpośrednie wywołanie metody kończy się niepowodzeniem z kodem błędu **400027**.

## <a name="cause"></a>Przyczyna

Inny klient utworzył nowe połączenie z Centrum IoT przy użyciu tych samych poświadczeń, więc Usługa IoT Hub zamknęła poprzednie połączenie. Usługa IoT Hub nie zezwala na łączenie się więcej niż jednego klienta przy użyciu tego samego zestawu poświadczeń.

## <a name="solution"></a>Rozwiązanie

Upewnij się, że każdy klient łączy się z Centrum IoT przy użyciu własnej tożsamości.