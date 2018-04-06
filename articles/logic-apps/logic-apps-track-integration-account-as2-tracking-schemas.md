---
title: Schematy śledzenia AS2 B2B monitorowania - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Użyj AS2 śledzenia schematów, aby monitorować wiadomości B2B transakcji na koncie integracji Azure.
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8605950f3cc07730ee13b91999076e3a093e8d92
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="start-or-enable-tracking-of-as2-messages-and-mdns-to-monitor-success-errors-and-message-properties"></a>Uruchom lub włączyć śledzenie wiadomości AS2 i MDNs sukcesu monitora, błędy i właściwości wiadomości
Te schematy śledzenia AS2 na koncie integracja Azure umożliwia ułatwiają monitorowanie biznesowych między firmami (B2B) transakcji:

* AS2 komunikatów śledzenia schematu
* Schemat śledzenia AS2 MDN

## <a name="as2-message-tracking-schema"></a>AS2 komunikatów śledzenia schematu
````java

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
````

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | Nazwa partnera nadawcy wiadomości AS2. (Opcjonalnie) |
| receiverPartnerName | Ciąg | Nazwa partnera AS2 komunikat odbiornika. (Opcjonalnie) |
| as2To | Ciąg | Nazwa odbiornika AS2 wiadomości z nagłówków komunikatu AS2. (Wymagane) |
| as2From | Ciąg | Nazwa nadawcy wiadomości AS2 z nagłówków komunikatu AS2. (Wymagane) |
| agreementName | Ciąg | Nazwa umowy AS2, do którego zostaną rozwiązane wiadomości. (Opcjonalnie) |
| kierunek | Ciąg | Kierunek przepływu wiadomości odbierać lub wysyłać. (Wymagane) |
| messageId | Ciąg | Identyfikator komunikatu AS2, z nagłówków komunikatu AS2 (opcjonalnie) |
| dispositionType |Ciąg | Wartość typu dyspozycji dyspozycji powiadomienia (MDN) wiadomości. (Opcjonalnie) |
| fileName | Ciąg | Nazwa pliku z nagłówka wiadomości AS2. (Opcjonalnie) |
| isMessageFailed |Wartość logiczna | Określa, czy komunikat AS2 nie powiodło się. (Wymagane) |
| isMessageSigned | Wartość logiczna | Określa, czy wiadomość AS2 została podpisana. (Wymagane) |
| isMessageEncrypted | Wartość logiczna | Określa, czy wiadomość AS2 została zaszyfrowana. (Wymagane) |
| isMessageCompressed |Wartość logiczna | Określa, czy komunikat AS2 został poddany kompresji. (Wymagane) |
| correlationMessageId | Ciąg | Identyfikator komunikatu AS2 służące do skorelowania wiadomości z MDNs. (Opcjonalnie) |
| incomingHeaders |Słownik JToken | Szczegóły nagłówka komunikatu przychodzącego AS2. (Opcjonalnie) |
| outgoingHeaders |Słownik JToken | Szczegóły nagłówka wiadomości wychodzącej AS2. (Opcjonalnie) |
| isNrrEnabled | Wartość logiczna | Użyj wartości domyślnej, jeśli wartość nie jest znany. (Wymagane) |
| isMdnExpected | Wartość logiczna | Użyj wartości domyślnej, jeśli wartość nie jest znany. (Wymagane) |
| mdnType | wyliczenia | Dozwolone wartości to **NotConfigured**, **synchronizacji**, i **Async**. (Wymagane) |

## <a name="as2-mdn-tracking-schema"></a>Schemat śledzenia AS2 MDN
````java

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
````

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | Nazwa partnera nadawcy wiadomości AS2. (Opcjonalnie) |
| receiverPartnerName | Ciąg | Nazwa partnera AS2 komunikat odbiornika. (Opcjonalnie) |
| as2To | Ciąg | Nazwa partnera, który odbiera komunikat AS2. (Wymagane) |
| as2From | Ciąg | Nazwa partnera, który wysyła wiadomości AS2. (Wymagane) |
| agreementName | Ciąg | Nazwa umowy AS2, do którego zostaną rozwiązane wiadomości. (Opcjonalnie) |
| kierunek |Ciąg | Kierunek przepływu wiadomości odbierać lub wysyłać. (Wymagane) |
| messageId | Ciąg | Identyfikator komunikatu AS2. (Opcjonalnie) |
| originalMessageId |Ciąg | Identyfikator AS2 oryginalnej wiadomości. (Opcjonalnie) |
| dispositionType | Ciąg | Wartość typu dyspozycji MDN. (Opcjonalnie) |
| isMessageFailed |Wartość logiczna | Określa, czy komunikat AS2 nie powiodło się. (Wymagane) |
| isMessageSigned |Wartość logiczna | Określa, czy wiadomość AS2 została podpisana. (Wymagane) |
| isNrrEnabled | Wartość logiczna | Użyj wartości domyślnej, jeśli wartość nie jest znany. (Wymagane) |
| statusCode | wyliczenia | Dozwolone wartości to **zaakceptowane**, **odrzucone**, i **AcceptedWithErrors**. (Wymagane) |
| micVerificationStatus | wyliczenia | Dozwolone wartości to **NotApplicable**, **zakończyło się pomyślnie**, i ****. (Wymagane) |
| correlationMessageId | Ciąg | Identyfikator korelacji. Oryginalna messaged ID (identyfikator komunikatu wiadomości, dla której skonfigurowano MDN). (Opcjonalnie) |
| incomingHeaders | Słownik JToken | Wskazuje szczegóły nagłówka komunikatu przychodzącego. (Opcjonalnie) |
| outgoingHeaders |Słownik JToken | Wskazuje szczegóły nagłówka komunikatu wychodzącego. (Opcjonalnie) |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md).    
* Dowiedz się więcej o [monitorowanie wiadomości B2B](logic-apps-monitor-b2b-message.md).   
* Dowiedz się więcej o [B2B niestandardowych schematów śledzenia](logic-apps-track-integration-account-custom-tracking-schema.md).   
* Dowiedz się więcej o [X12 śledzenia schematy](logic-apps-track-integration-account-x12-tracking-schema.md).   
* Dowiedz się więcej o [śledzenie wiadomości B2B w analizy dzienników](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
