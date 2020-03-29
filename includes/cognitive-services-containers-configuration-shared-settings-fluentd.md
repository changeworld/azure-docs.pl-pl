---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67712395"
---
Fluentd jest źródłowym modułem zbierającym dane do ujednoliconego rejestrowania. Ustawienia `Fluentd` zarządzają połączeniem kontenera z [serwerem Fluentd.](https://www.fluentd.org) Kontener zawiera dostawcy rejestrowania Fluentd, który umożliwia kontenerowi zapisywanie dzienników i, opcjonalnie, dane metryki na serwerze Fluentd.

W poniższej tabeli opisano ustawienia `Fluentd` konfiguracji obsługiwane w sekcji.

| Nazwa | Typ danych | Opis |
|------|-----------|-------------|
| `Host` | Ciąg | Adres IP lub nazwa hosta DNS serwera Fluentd. |
| `Port` | Liczba całkowita | Port serwera Fluentd.<br/> Wartość domyślna to 24224. |
| `HeartbeatMs` | Liczba całkowita | Interwał bicia serca w milisekundach. Jeśli ruch zdarzeń nie został wysłany przed wygaśnięciem tego interwału, puls jest wysyłany do serwera Fluentd. Wartość domyślna to 60000 milisekund (1 minuta). |
| `SendBufferSize` | Liczba całkowita | Przestrzeń buforu sieciowego w bajtach przydzielona do operacji wysyłania. Wartość domyślna to 32768 bajtów (32 kilobajtów). |
| `TlsConnectionEstablishmentTimeoutMs` | Liczba całkowita | Limit czasu w milisekundach w celu ustanowienia połączenia SSL/TLS z serwerem Fluentd. Wartość domyślna to 10000 milisekund (10 sekund).<br/> Jeśli `UseTLS` jest ustawiona na false, ta wartość jest ignorowana. |
| `UseTLS` | Wartość logiczna | Wskazuje, czy kontener powinien używać protokołu SSL/TLS do komunikowania się z serwerem Fluentd. Wartość domyślna to false. |