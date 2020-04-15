---
title: Konfiguracja lokalna agenta zabezpieczeń (C#)
description: Dowiedz się więcej o usłudze zabezpieczeń Usługi Zabezpieczeń Azure Security Center dla IoT, lokalnym pliku konfiguracji agenta zabezpieczeń dla języka C#.
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
ms.openlocfilehash: adf0d72763e0cb1892d64c68a6dce05abbf6f582
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311676"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Opis lokalnego pliku konfiguracji (agent języka C#)

Agent zabezpieczeń Usługi Azure Security Center dla IoT używa konfiguracji z lokalnego pliku konfiguracji.

Agent zabezpieczeń odczytuje plik konfiguracyjny raz po uruchomieniu agenta. Konfiguracje znalezione w lokalnym pliku konfiguracyjnym zawierają zarówno konfigurację uwierzytelniania, jak i inne konfiguracje związane z agentem.

Agent zabezpieczeń języka C# używa wielu plików konfiguracyjnych:

- **General.config** — konfiguracje związane z agentem.
- **Authentication.config** — konfiguracja związana z uwierzytelnianiem (w tym szczegóły uwierzytelniania).
- **SecurityIotInterface.config** — konfiguracje związane z IoT.

Pliki konfiguracyjne zawierają konfigurację domyślną. Konfiguracja uwierzytelniania jest wypełniana podczas instalacji agenta, a zmiany w pliku konfiguracyjny są wprowadzane po ponownym uruchomieniu agenta.

## <a name="configuration-file-location"></a>Lokalizacja pliku konfiguracyjnego

Dla Linuksa:

- Pliki konfiguracyjne systemu `/var/ASCIoTAgent`operacyjnego znajdują się w programie .

W przypadku systemu Windows:

- Pliki konfiguracji systemu operacyjnego znajdują się w katalogu agenta zabezpieczeń.

### <a name="generalconfig-configurations"></a>Konfiguracje general.config

| Nazwa konfiguracji | Możliwe wartości | Szczegóły |
|:-----------|:---------------|:--------|
| agentId | Identyfikator GUID | Unikatowy identyfikator agenta |
| readRemoteConfigurationTimeout | przedział_czasu | Okres pobierania konfiguracji zdalnej z usługi IoT Hub. Jeśli agent nie może pobrać konfiguracji w określonym czasie, operacja przekroczy limit czasu.|
| schedulerInterval | przedział_czasu | Wewnętrzny interwał harmonogramu. |
| producentInterval | przedział_czasu | Interwał pracownika producenta zdarzeń. |
| consumerInterval | przedział_czasu | Interwał procesu roboczego konsumenta zdarzenia. |
| highPriorityQueueSizePercentage | 0 < numer < 1 | Część całkowitej pamięci podręcznej dedykowana dla wiadomości o wysokim priorytecie. |
| Loglevel | "Wył.", "Krytyczny", "Błąd", "Ostrzeżenie", "Informacje", "Debug"  | Komunikaty dziennika równe i powyżej tej ważności są rejestrowane do konsoli debugowania (Syslog w systemie Linux). |
| fileLogLevel (dziennik plików) |  "Wył.", "Krytyczny", "Błąd", "Ostrzeżenie", "Informacje", "Debug"| Komunikaty dziennika równe i powyżej tej ważności są rejestrowane do pliku (Syslog w systemie Linux). |
| diagnostykaVerbosityPoziom | "Brak", "Niektóre", "Wszystkie", | Poziom szczegółowości zdarzeń diagnostycznych. Brak - zdarzenia diagnostyczne nie są wysyłane, Niektóre — wysyłane są tylko zdarzenia diagnostyczne o wysokim znaczeniu, Wszystkie — wszystkie dzienniki są również wysyłane jako zdarzenia diagnostyczne. |
| Logfilepath | Ścieżka do pliku | Jeśli fileLogLevel > Off, dzienniki są zapisywane w tym pliku. |
| defaultEventPriority | "Wysoki", "Niski", "Wyłączony" | Domyślny priorytet zdarzenia. |

### <a name="generalconfig-example"></a>Przykład pliku General.config

```XML
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Nazwa konfiguracji | Możliwe wartości | Szczegóły |
|:-----------|:---------------|:--------|
| nazwa modułu | ciąg | Nazwa tożsamości modułu zabezpieczeń. Ta nazwa musi odpowiadać nazwie tożsamości modułu w urządzeniu. |
| deviceId | ciąg | Identyfikator urządzenia (zarejestrowany w usłudze Azure IoT Hub). || schedulerInterval | Ciąg TimeSpan | Wewnętrzny interwał harmonogramu. |
| nazwa bramyHostname | ciąg | Nazwa hosta usługi Azure Iot Hub. Zwykle <my-hub>.azure-devices.net |
| Filepath | string - ścieżka do pliku | Ścieżka do pliku zawierającego klucz tajny uwierzytelniania.|
| type | "Symetryczny klucz", "Certyfikat SelfSignedC" | Klucz tajny użytkownika do uwierzytelniania. Wybierz *opcję SymmetricKey,* jeśli klucz tajny użytkownika jest kluczem symetrycznym, wybierz *certyfikat z podpisem własnym,* jeśli klucz tajny jest certyfikatem z podpisem własnym. |
| identity | "DPS", "Moduł", "Urządzenie" | Tożsamość uwierzytelniania — DPS, jeśli uwierzytelnianie odbywa się za pośrednictwem dps, moduł, jeśli uwierzytelnianie jest dokonywane przy użyciu poświadczeń modułu lub urządzenia, jeśli uwierzytelnianie odbywa się przy użyciu poświadczeń urządzenia.
| certificateLocationKind |  "LocalFile", "Sklep" | LocalFile jeśli certyfikat jest przechowywany w pliku, przechowuj, jeśli certyfikat znajduje się w magazynie certyfikatów. |
| idScope | ciąg | Zakres identyfikatora DPS |
| registrationId (Identyfikator rejestracji) | ciąg  | Identyfikator rejestracji urządzenia DPS. |
|

### <a name="authenticationconfig-example"></a>Przykład authentication.config

```XML
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Nazwa konfiguracji | Możliwe wartości | Szczegóły |
|:-----------|:---------------|:--------|
| transportTyp | "Ampq" "Mqtt" | Typ transportu usługi IoT Hub. |
|

### <a name="securityiotinterfaceconfig-example"></a>Przykład SecurityIotInterface.config

```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [omówienie](overview.md) usługi Azure Security Center dla IoT
- Dowiedz się więcej o usłudze Azure Security Center dla [architektury](architecture.md) IoT
- Włączanie usługi Azure Security Center dla [usługi](quickstart-onboard-iot-hub.md) IoT
- Przeczytaj często zadawane pytania dotyczące usługi Azure Security Center for [IoT](resources-frequently-asked-questions.md)
- Dowiedz się, jak uzyskać dostęp do [nieprzetworzonych danych zabezpieczeń](how-to-security-data-access.md)
- Zrozumienie [rekomendacji](concept-recommendations.md)
- Opis [alertów](concept-security-alerts.md) zabezpieczeń
