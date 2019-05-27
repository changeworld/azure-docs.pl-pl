---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: ffd17f7a641e1481aa4c88f8b2eb12ec11fa7d8b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116738"
---
Fluentd jest moduł zbierający dane typu open source, ujednolicone rejestrowania. `Fluentd` Ustawienia zarządzania połączenie kontenera [Fluentd](https://www.fluentd.org) serwera. Kontener zawiera Fluentd dostawcy logowania, co pozwala na zapisywanie dzienników kontenera i, opcjonalnie, metryki danych na serwerze Fluentd.

W poniższej tabeli opisano ustawienia konfiguracji objęte `Fluentd` sekcji.

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `Host` | Ciąg | Adres IP lub nazwa hosta DNS serwera Fluentd. |
| `Port` | Liczba całkowita | Port serwera Fluentd.<br/> Wartość domyślna to 24224. |
| `HeartbeatMs` | Liczba całkowita | Interwał pulsu (w milisekundach). Jeśli żaden ruch nie jest zdarzenie zostało wysłane przed upływem tego interwału, pulsu są wysyłane do serwera Fluentd. Wartość domyślna to 60000 milisekund (1 minuta). |
| `SendBufferSize` | Liczba całkowita | Sieć miejsca w buforze, w bajtach, przydzielany dla operacji wysyłania. Wartość domyślna to 32768 bajtów (32 kilobajtów). |
| `TlsConnectionEstablishmentTimeoutMs` | Liczba całkowita | Limit czasu w milisekundach, można ustanowić połączenia SSL/TLS z serwerem Fluentd. Wartość domyślna to 10000 MS (10 sekund).<br/> Jeśli `UseTLS` jest ustawiona na wartość FAŁSZ, ta wartość jest ignorowana. |
| `UseTLS` | Wartość logiczna | Wskazuje, czy do komunikacji z serwerem Fluentd kontenera należy używać protokołów SSL/TLS. Wartość domyślna to false. |