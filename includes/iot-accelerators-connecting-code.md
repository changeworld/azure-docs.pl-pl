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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183666"
---
### <a name="code-walkthrough"></a>Przewodnik po kodzie

W tej sekcji opisano niektóre z kluczowych części przykładowego kodu i wyjaśniono, jak odnoszą się one do akceleratora rozwiązania zdalnego monitorowania.

Poniższy fragment kodu pokazuje, jak zgłaszane właściwości, które opisują możliwości urządzenia są zdefiniowane. Właściwości te obejmują:

- Lokalizacja urządzenia, aby umożliwić akceleratorowi rozwiązania, aby dodać urządzenie do mapy.
- Bieżąca wersja oprogramowania układowego.
- Lista metod, które obsługuje urządzenie.
- Schemat komunikatów telemetrycznych wysyłanych przez urządzenie.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

Przykład zawiera **funkcję serializeToJson,** która serializuje tę strukturę danych przy użyciu biblioteki Parson.

Przykład zawiera kilka funkcji wywołania zwrotnego, które drukują informacje do konsoli, gdy klient wchodzi w interakcję z akceleratorem rozwiązania:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

Poniższy fragment kodu przedstawia funkcję **device_method_callback.** Ta funkcja określa akcję do podjęcia po odebraniu wywołania metody z akceleratora rozwiązania. Funkcja odbiera odwołanie do struktury danych **chiller** w **userContextCallback** parametru. Wartość **userContextCallback** jest ustawiana, gdy funkcja wywołania zwrotnego jest skonfigurowana w funkcji **głównej:**

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Gdy akcelerator rozwiązania wywołuje metodę aktualizacji oprogramowania układowego, przykładowy deserializuje ładunek JSON i uruchamia wątek w tle, aby zakończyć proces aktualizacji. Poniższy fragment kodu pokazuje **do_firmware_update,** który działa w wątku:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

Poniższy fragment kodu pokazuje, jak klient wysyła komunikat telemetrii do akceleratora rozwiązania. Właściwości komunikatu obejmują schemat komunikatu, aby pomóc akceleratorowi rozwiązania wyświetlać dane telemetryczne na pulpicie nawigacyjnym:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

**Główna** funkcja w próbce:

- Inicjuje i wyłącza podsystem SDK.
- Inicjuje strukturę danych **agregatu chłodniczego.**
- Wysyła zgłoszone właściwości do akceleratora rozwiązania.
- Konfiguruje funkcję wywołania zwrotnego metody urządzenia.
- Wysyła symulowane wartości telemetrii do akceleratora rozwiązania.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
