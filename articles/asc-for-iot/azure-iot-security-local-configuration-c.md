---
title: Konfiguracja lokalna agenta zabezpieczeń (C)
description: Dowiedz się więcej o usłudze Azure Security Center dla konfiguracji lokalnych agentów dla języka C.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: cd344b9bebb69af210c482f46af6b2dd7edf7816
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311706"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Omówienie pliku LocalConfiguration.json — agent oparty na języku C

Agent zabezpieczeń Usługi Azure Security Center dla IoT używa konfiguracji z lokalnego pliku konfiguracji.
Agent zabezpieczeń odczytuje konfigurację raz, przy uruchamianiu agenta.
Konfiguracja znaleziona w lokalnym pliku konfiguracyjnym zawiera konfigurację uwierzytelniania i inne konfiguracje związane z agentem.
Plik zawiera konfiguracje w parach "Wartość klucza" w notacji JSON, a konfiguracje są wypełniane po zainstalowaniu agenta.

Domyślnie plik znajduje się pod adresem: /var/ASCIoTAgent/LocalConfiguration.json

Zmiany w pliku konfiguracyjnym mają miejsce po ponownym uruchomieniu agenta.

## <a name="security-agent-configurations-for-c"></a>Konfiguracje agentów zabezpieczeń dla języka C

| Nazwa konfiguracji | Możliwe wartości | Szczegóły |
|:-----------|:---------------|:--------|
| AgentId | Identyfikator GUID | Unikatowy identyfikator agenta |
| TriggerdEventsInterval | Ciąg ISO8601 | Interwał harmonogramu dla kolekcji zdarzeń wyzwalanych |
| Connectiontimeout | Ciąg ISO8601 | Upływ czasu, zanim połączenie z IoThub zostanie przesunął limit czasu |
| Authentication | JsonObject (łań.) | Konfiguracja uwierzytelniania. Ten obiekt zawiera wszystkie informacje potrzebne do uwierzytelniania w uliczce IoTHub |
| Tożsamość | "DPS", "SecurityModule", "Device" | Tożsamość uwierzytelniania — DPS, jeśli uwierzytelnianie odbywa się za pośrednictwem dps, SecurityModule jeśli uwierzytelnianie odbywa się za pomocą poświadczeń modułu zabezpieczeń lub urządzenia, jeśli uwierzytelnianie odbywa się za pomocą poświadczeń urządzenia |
| Authenticationmethod | "SasToken", "SelfSignedCertificate" | klucz tajny użytkownika do uwierzytelniania — wybierz SasToken, jeśli klucz tajny użycia jest kluczem symetrycznym, wybierz certyfikat z podpisem własnym, jeśli klucz tajny jest certyfikatem z podpisem własnym  |
| Filepath | Ścieżka do pliku (ciąg) | Ścieżka do pliku zawierającego klucz tajny uwierzytelniania |
| HostName | ciąg | Nazwa hosta usługi azure iot hub. zwykle <my-hub>.azure-devices.net |
| DeviceId | ciąg | Identyfikator urządzenia (zarejestrowany w usłudze Azure IoT Hub) |
| Dps | JsonObject (łań.) | Konfiguracje związane z DPS |
| IDScope | ciąg | Zakres identyfikatora DPS |
| Identyfikator rejestracji | ciąg  | Identyfikator rejestracji urządzenia DPS |
| Rejestrowanie | JsonObject (łań.) | Konfiguracje związane z rejestratorem agenta |
| SystemLoggerMinimumEverity | 0 <= liczba <= 4 | komunikaty dziennika równe i powyżej tej ważności będą rejestrowane w /var/log/syslog (0 jest najniższą ważnością) |
| DiagnostykaEventMinimumEverity | 0 <= liczba <= 4 | komunikaty dziennika równe i powyżej tej ważności będą wysyłane jako zdarzenia diagnostyczne (0 jest najniższą ważnością) |

## <a name="security-agent-configurations-code-example"></a>Przykład kodu konfiguracji agenta zabezpieczeń

```JSON
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [omówienie](overview.md) usługi Azure Security Center dla IoT
- Dowiedz się więcej o usłudze Azure Security Center dla [architektury](architecture.md) IoT
- Włączanie usługi Azure Security Center dla [usługi](quickstart-onboard-iot-hub.md) IoT
- Przeczytaj często zadawane pytania dotyczące usługi Azure Security Center for [IoT](resources-frequently-asked-questions.md)
- Dowiedz się, jak uzyskać dostęp do [nieprzetworzonych danych zabezpieczeń](how-to-security-data-access.md)
- Zrozumienie [rekomendacji](concept-recommendations.md)
- Opis [alertów](concept-security-alerts.md) zabezpieczeń
