---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147720"
---
### <a name="code-walkthrough"></a>Instruktaż dotyczący kodu

W tej sekcji opisano niektóre z kluczowych części w przykładowym kodzie i wyjaśniono, jak odnoszą się do akceleratora rozwiązania monitorowania zdalnego.

Poniższy fragment kodu pokazuje, jak zdefiniowano zgłaszanych właściwości, które opisują możliwości urządzenia. Te właściwości obejmują:

- Lokalizacja urządzenia, aby włączyć akcelerator rozwiązań dodać urządzenie do mapy.
- Bieżąca wersja oprogramowania układowego.
- Lista metod obsługiwanych przez urządzenie.
- Schemat komunikaty telemetryczne wysyłane przez urządzenie.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

Przykład obejmuje **serializeToJson** funkcja, która wykonuje serializację tej struktury danych, za pomocą biblioteki Parson.

Przykład obejmuje kilka funkcji wywołania zwrotnego, które drukują informacje w konsoli jako klient korzysta z akceleratora rozwiązań:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

Poniższy fragment kodu przedstawia **device_method_callback** funkcji. Ta funkcja określa akcję wykonywaną, gdy wywołanie metody są odebrane z akceleratora rozwiązań. Funkcja otrzymuje odwołanie do **Chłodnica** danych struktury w **userContextCallback** parametru. Wartość **userContextCallback** jest ustawiona, gdy funkcja wywołania zwrotnego jest skonfigurowana w **głównego** funkcji:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Gdy akcelerator rozwiązań wywołuje metodę aktualizacji oprogramowania układowego, przykładzie deserializuje ładunek JSON i uruchamia wątku w tle do ukończenia procesu aktualizacji. Poniższy fragment kodu przedstawia **do_firmware_update** , które jest uruchamiane w wątku:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

Poniższy fragment kodu pokazuje, jak klient wysyła komunikaty telemetryczne do akceleratora rozwiązań. Właściwości wiadomości obejmują schemat komunikat ułatwiające akcelerator rozwiązań, wyświetlanie danych telemetrycznych na pulpicie nawigacyjnym:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

**Głównego** funkcji w przykładzie:

- Inicjuje się i kończy pracę w podsystemie zestawu SDK.
- Inicjuje **Chłodnica** strukturę danych.
- Wysyła zgłaszane właściwości do akceleratora rozwiązań.
- Umożliwia skonfigurowanie funkcji wywołania zwrotnego metody urządzenia.
- Wysyła symulowane dane telemetryczne wartości do akceleratora rozwiązań.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
