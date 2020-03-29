---
title: Schematy śledzenia AS2 dla wiadomości B2B
description: Tworzenie schematów śledzenia w celu monitorowania komunikatów AS2 w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906961"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Tworzenie schematów śledzenia wiadomości AS2 w usłudze Azure Logic Apps

Aby ułatwić monitorowanie sukcesu, błędów i właściwości wiadomości dla transakcji typu "business-to-business" (B2B), można użyć tych schematów śledzenia AS2 na koncie integracji:

* Schemat śledzenia komunikatów AS2
* Schemat śledzenia powiadomienia o usposobieniu wiadomości AS2 (MDN)

## <a name="as2-message-tracking-schema"></a>Schemat śledzenia komunikatów AS2

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
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| nazwa partnera nadawcy | Nie | Ciąg | Nazwa partnera nadawcy wiadomości AS2 |
| nazwa odbiornikaPartnerName | Nie | Ciąg | Nazwa partnera odbiornika komunikatów AS2 |
| as2To | Tak | Ciąg | Nazwa odbiornika komunikatów AS2 z nagłówków wiadomości AS2 |
| as2Od | Tak | Ciąg | Nazwa nadawcy wiadomości AS2 z nagłówków wiadomości AS2 |
| nazwa umowy | Nie | Ciąg | Nazwa umowy AS2, do której wiadomości są rozpoznawane |
| kierunek | Tak | Ciąg | Kierunek przepływu wiadomości, który jest `receive``send` |
| Messageid | Nie | Ciąg | Identyfikator wiadomości AS2 z nagłówków wiadomości AS2 |
| dispositionType | Nie | Ciąg | Wartość typu usposobienia usposobienia usposobienia wiadomości (MDN) |
| fileName | Nie | Ciąg | Nazwa pliku z nagłówka wiadomości AS2 |
| isMessageFailed | Tak | Wartość logiczna | Czy komunikat AS2 nie powiódł się |
| isMessageSigned | Tak | Wartość logiczna | Czy wiadomość AS2 została podpisana |
| isMessageEncrypted | Tak | Wartość logiczna | Czy wiadomość AS2 została zaszyfrowana |
| isMessageKompresowany | Tak | Wartość logiczna | Czy wiadomość AS2 została skompresowana |
| korelacjaMessageId | Nie | Ciąg | Identyfikator komunikatu AS2, aby skorelować wiadomości z mdn |
| przychodząceGers | Nie | Słownik JToken | Szczegóły nagłówka przychodzącego nagłówka wiadomości AS2 |
| outgoingHeaders (odsuwnik) | Nie | Słownik JToken | Szczegóły nagłówka wychodzącego nagłówka wiadomości AS2 |
| isNrrEnabled ( | Tak | Wartość logiczna | Czy użyć wartości domyślnej, jeśli wartość nie jest znana |
| isMdnWytowane | Tak | Wartość logiczna | Czy użyć wartości domyślnej, jeśli wartość nie jest znana |
| mdnType | Tak | Wyliczenie | Dozwolone wartości: `NotConfigured` `Sync`, , i`Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>Schemat śledzenia AS2 MDN

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

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| nazwa partnera nadawcy | Nie | Ciąg | Nazwa partnera nadawcy wiadomości AS2 |
| nazwa odbiornikaPartnerName | Nie | Ciąg | Nazwa partnera odbiornika komunikatów AS2 |
| as2To | Tak | Ciąg | Nazwa partnera, który odbiera wiadomość AS2 |
| as2Od | Tak | Ciąg | Nazwa partnera, który wysyła wiadomość AS2 |
| nazwa umowy | Nie | Ciąg | Nazwa umowy AS2, do której wiadomości są rozpoznawane |
| kierunek | Tak | Ciąg | Kierunek przepływu wiadomości, który jest `receive``send` |
| Messageid | Nie | Ciąg | Identyfikator wiadomości AS2 |
| originalMessageId | Nie | Ciąg | Identyfikator oryginalnej wiadomości AS2 |
| dispositionType | Nie | Ciąg | Wartość typu dyspozycji MDN |
| isMessageFailed | Tak | Wartość logiczna | Czy komunikat AS2 nie powiódł się |
| isMessageSigned | Tak | Wartość logiczna | Czy wiadomość AS2 została podpisana |
| isNrrEnabled ( | Tak | Wartość logiczna | Czy użyć wartości domyślnej, jeśli wartość nie jest znana |
| Statuscode | Tak | Wyliczenie | Dozwolone wartości: `Accepted` `Rejected`, , i`AcceptedWithErrors` |
| micVerificationStatus | Tak | Wyliczenie | Dozwolone wartości:`NotApplicable` `Succeeded`, , i`Failed` |
| korelacjaMessageId | Nie | Ciąg | Identyfikator korelacji, który jest identyfikatorem oryginalnej wiadomości, która ma skonfigurowany mdn |
| przychodząceGers | Nie | Słownik JToken | Szczegóły nagłówka wiadomości przychodzącej |
| outgoingHeaders (odsuwnik) | Nie | Słownik JToken | Szczegóły nagłówka wiadomości wychodzącej |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołów B2B

Aby uzyskać informacje na temat schematów śledzenia protokołów B2B, zobacz:

* [Schematy śledzenia X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schematy śledzenia niestandardowego B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie komunikatów B2B przy użyciu dzienników usługi Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)