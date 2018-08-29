---
title: Schematy śledzenia AS2 dotyczących komunikatów B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Utwórz schematy śledzenia AS2, których monitorowanie komunikatów B2B w kont integracji dla usługi Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 6c4144d26042729684e507b1afaa5e3006d8a34e
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125934"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Tworzenie schematów do śledzenia komunikatów AS2 i komunikatów Mdn w ramach kont integracji dla usługi Azure Logic Apps

Aby pomóc monitorować Powodzenie, błędy i właściwości wiadomości dla transakcji biznesowych (B2B), możesz użyć tych schematy śledzenia AS2 w ramach konta integracji:

* Schemat śledzenia komunikatu AS2
* Schemat śledzenia komunikatu MDN AS2

## <a name="as2-message-tracking-schema"></a>Schemat śledzenia komunikatu AS2

```json
{
   "agreementProperties": {  
      "senderPartnerName": "",  
      "receiverPartnerName": "",  
      "as2To": "",  
      "as2From": "",  
      "agreementName": ""  
   },  
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | Nazwa partnera nadawca komunikatu AS2. (Opcjonalnie) |
| receiverPartnerName | Ciąg | Nazwa partnera odbiorca komunikatu AS2. (Opcjonalnie) |
| as2To | Ciąg | Nazwa odbiorcy komunikat AS2, z nagłówków komunikatu AS2. (Obowiązkowe) |
| użyciu nagłówków as2From | Ciąg | Nazwa nadawcy wiadomości AS2 z nagłówków komunikatu AS2. (Obowiązkowe) |
| agreementName | Ciąg | Nazwa umowy AS2, do której wiadomości są rozwiązane. (Opcjonalnie) |
| kierunek | Ciąg | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| messageId | Ciąg | Identyfikator komunikatu AS2, z nagłówków komunikatu AS2 (opcjonalnie) |
| dispositionType |Ciąg | Wartość typu dyspozycji dyspozycji powiadomienia (powiadomienia MDN) wiadomości. (Opcjonalnie) |
| fileName | Ciąg | Nazwa pliku z nagłówka komunikatu AS2. (Opcjonalnie) |
| isMessageFailed |Wartość logiczna | Czy komunikat AS2 nie powiodło się. (Obowiązkowe) |
| isMessageSigned | Wartość logiczna | Czy komunikat AS2 został podpisany. (Obowiązkowe) |
| isMessageEncrypted | Wartość logiczna | Czy został zaszyfrowany komunikatu AS2. (Obowiązkowe) |
| isMessageCompressed |Wartość logiczna | Czy komunikat AS2 był skompresowany. (Obowiązkowe) |
| correlationMessageId | Ciąg | Identyfikator komunikatu AS2 korelowanie komunikatów za pomocą komunikatów Mdn. (Opcjonalnie) |
| incomingHeaders |Słownik JToken | Przychodzące szczegóły nagłówka komunikatu AS2. (Opcjonalnie) |
| outgoingHeaders |Słownik JToken | Wychodzący szczegółowe informacje o nagłówku komunikatu AS2. (Opcjonalnie) |
| isNrrEnabled | Wartość logiczna | Jeśli wartość nie jest znana, użyj wartości domyślnej. (Obowiązkowe) |
| isMdnExpected | Wartość logiczna | Jeśli wartość nie jest znana, użyj wartości domyślnej. (Obowiązkowe) |
| mdnType | Wyliczenia | Dozwolone wartości to **NotConfigured**, **synchronizacji**, i **Async**. (Obowiązkowe) |
||||

## <a name="as2-mdn-tracking-schema"></a>Schemat śledzenia komunikatu MDN AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | Nazwa partnera nadawca komunikatu AS2. (Opcjonalnie) |
| receiverPartnerName | Ciąg | Nazwa partnera odbiorca komunikatu AS2. (Opcjonalnie) |
| as2To | Ciąg | Nazwa partnera, który otrzymuje komunikat AS2. (Obowiązkowe) |
| użyciu nagłówków as2From | Ciąg | Nazwa partnera, który wysyła komunikat AS2. (Obowiązkowe) |
| agreementName | Ciąg | Nazwa umowy AS2, do której wiadomości są rozwiązane. (Opcjonalnie) |
| kierunek |Ciąg | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| messageId | Ciąg | Identyfikator komunikatu AS2 (Opcjonalnie) |
| originalMessageId |Ciąg | Identyfikator AS2 oryginalnego komunikatu. (Opcjonalnie) |
| dispositionType | Ciąg | Wartość typu dyspozycji powiadomienia MDN. (Opcjonalnie) |
| isMessageFailed |Wartość logiczna | Czy komunikat AS2 nie powiodło się. (Obowiązkowe) |
| isMessageSigned |Wartość logiczna | Czy komunikat AS2 został podpisany. (Obowiązkowe) |
| isNrrEnabled | Wartość logiczna | Jeśli wartość nie jest znana, użyj wartości domyślnej. (Obowiązkowe) |
| statusCode | Wyliczenia | Dozwolone wartości to **zaakceptowano**, **odrzucony**, i **AcceptedWithErrors**. (Obowiązkowe) |
| micVerificationStatus | Wyliczenia | Dozwolone wartości to **NotApplicable**, **zakończyło się pomyślnie**, **i**. (Obowiązkowe) |
| correlationMessageId | Ciąg | Identyfikator korelacji. Oryginalny messaged ID (identyfikator wiadomości, dla której skonfigurowano powiadomienia MDN). (Opcjonalnie) |
| incomingHeaders | Słownik JToken | Wskazuje przychodzących szczegółowe informacje o nagłówku komunikatu. (Opcjonalnie) |
| outgoingHeaders |Słownik JToken | Wskazuje wychodzących szczegółowe informacje o nagłówku komunikatu. (Opcjonalnie) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołu B2B

Aby uzyskać informacji na temat protokołu B2B schematy śledzenia zobacz:

* [Schematy śledzenia X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Niestandardowe schematy śledzenia B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [monitorowanie komunikatów B2B](logic-apps-monitor-b2b-message.md)
* Dowiedz się więcej o [śledzenie komunikatów B2B w usłudze Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)