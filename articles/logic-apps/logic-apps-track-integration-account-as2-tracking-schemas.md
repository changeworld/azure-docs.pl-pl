---
title: Schematy śledzenia AS2 dla komunikatów B2B
description: Tworzenie schematów śledzenia AS2, które monitorują komunikaty B2B na kontach integracji dla Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 515d7cfc985ee9929f70de2c862170ff79ae4d60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792809"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Tworzenie schematów śledzenia komunikatów AS2 i MDNs na kontach integracji dla Azure Logic Apps

Aby ułatwić monitorowanie sukcesu, błędów i właściwości komunikatów dla transakcji typu B2B (Business-to-Business), możesz użyć tych schematów śledzenia AS2 na koncie integracji:

* Schemat śledzenia komunikatów AS2
* Schemat śledzenia powiadomienia MDN AS2

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
| senderPartnerName | Ciąg | Nazwa partnera nadawcy wiadomości AS2. Obowiązkowe |
| receiverPartnerName | Ciąg | Nazwa partnera odbiorcy komunikatu AS2. Obowiązkowe |
| as2To | Ciąg | Nazwa odbiorcy komunikatu AS2 z nagłówków wiadomości AS2. Wypełnione |
| as2From | Ciąg | Nazwa nadawcy wiadomości AS2 z nagłówków wiadomości AS2. Wypełnione |
| umowaname | Ciąg | Nazwa umowy AS2, do której komunikaty są rozwiązywane. Obowiązkowe |
| kierunek | Ciąg | Kierunek przepływu wiadomości, odbieranie lub wysyłanie. Wypełnione |
| Identyfikatora | Ciąg | Identyfikator komunikatu AS2 z nagłówków wiadomości AS2 (opcjonalnie) |
| rozmieśćtype |Ciąg | Wartość typu dyspozycji powiadomienia o dyspozycji wiadomości (powiadomienia MDN). Obowiązkowe |
| fileName | Ciąg | Nazwa pliku, z nagłówka komunikatu AS2. Obowiązkowe |
| isMessageFailed |Wartość logiczna | Czy komunikat AS2 nie powiódł się. Wypełnione |
| isMessageSigned | Wartość logiczna | Czy wiadomość AS2 została podpisana. Wypełnione |
| isMessageEncrypted | Wartość logiczna | Czy komunikat AS2 został zaszyfrowany. Wypełnione |
| isMessageCompressed |Wartość logiczna | Czy komunikat AS2 został skompresowany. Wypełnione |
| correlationMessageId | Ciąg | Identyfikator komunikatu AS2 w celu skorelowania komunikatów z usługą MDNs. Obowiązkowe |
| incomingHeaders |Słownik JToken | Szczegóły przychodzącego nagłówka komunikatu AS2. Obowiązkowe |
| outgoingHeaders |Słownik JToken | Szczegóły wychodzącego nagłówka komunikatu AS2. Obowiązkowe |
| isNrrEnabled | Wartość logiczna | Użyj wartości domyślnej, jeśli wartość jest nieznana. Wypełnione |
| isMdnExpected | Wartość logiczna | Użyj wartości domyślnej, jeśli wartość jest nieznana. Wypełnione |
| mdnType | Wyliczenie | Dozwolone wartości to **NotConfigured**, **Sync**i **Async**. Wypełnione |
||||

## <a name="as2-mdn-tracking-schema"></a>Schemat śledzenia powiadomienia MDN AS2

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
| senderPartnerName | Ciąg | Nazwa partnera nadawcy wiadomości AS2. Obowiązkowe |
| receiverPartnerName | Ciąg | Nazwa partnera odbiorcy komunikatu AS2. Obowiązkowe |
| as2To | Ciąg | Nazwa partnera, który odbiera komunikat AS2. Wypełnione |
| as2From | Ciąg | Nazwa partnera, który wysyła wiadomość AS2. Wypełnione |
| umowaname | Ciąg | Nazwa umowy AS2, do której komunikaty są rozwiązywane. Obowiązkowe |
| kierunek |Ciąg | Kierunek przepływu wiadomości, odbieranie lub wysyłanie. Wypełnione |
| Identyfikatora | Ciąg | Identyfikator komunikatu AS2. Obowiązkowe |
| originalMessageId |Ciąg | Identyfikator oryginalnego komunikatu AS2. Obowiązkowe |
| rozmieśćtype | Ciąg | POWIADOMIENIA MDN wartość typu dyspozycji. Obowiązkowe |
| isMessageFailed |Wartość logiczna | Czy komunikat AS2 nie powiódł się. Wypełnione |
| isMessageSigned |Wartość logiczna | Czy wiadomość AS2 została podpisana. Wypełnione |
| isNrrEnabled | Wartość logiczna | Użyj wartości domyślnej, jeśli wartość jest nieznana. Wypełnione |
| Stanu | Wyliczenie | Dozwolone wartości to **akceptowane**, **odrzucone**i **AcceptedWithErrors**. Wypełnione |
| micVerificationStatus | Wyliczenie | Dozwolone wartości to **NotApplicable (program**, **sukces**i **Niepowodzenie**. Wypełnione |
| correlationMessageId | Ciąg | Identyfikator korelacji. Oryginalny identyfikator wiadomości (Identyfikator komunikatu dla wiadomości, dla której skonfigurowano powiadomienia MDN). Obowiązkowe |
| incomingHeaders | Słownik JToken | Wskazuje szczegóły nagłówka komunikatu przychodzącego. Obowiązkowe |
| outgoingHeaders |Słownik JToken | Wskazuje szczegóły nagłówka komunikatu wychodzącego. Obowiązkowe |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołu B2B

Aby uzyskać informacje o schematach śledzenia protokołu B2B, zobacz:

* [Schematy śledzenia X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Niestandardowe schematy śledzenia B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Następne kroki

* Informacje o [monitorowaniu komunikatów B2B](logic-apps-monitor-b2b-message.md)
* Informacje [na temat śledzenia komunikatów B2B w dziennikach Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)