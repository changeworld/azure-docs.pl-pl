---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: d1416e7a6cad079be94b0a9b48d7c7c71a598cc2
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977358"
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