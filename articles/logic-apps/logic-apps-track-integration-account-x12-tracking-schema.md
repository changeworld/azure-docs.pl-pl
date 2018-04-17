---
title: Śledzenie schematy B2B X12 monitorowania - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Użyj X12 śledzenia schematów, aby monitorować wiadomości B2B transakcji na koncie Azure integracji.
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5a43b9bdf522b6b26f27c082f5cb623f7a76a8b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="start-or-enable-tracking-of-x12-messages-to-monitor-success-errors-and-message-properties"></a>Początkowy lub Włącz śledzenie X12 komunikaty sukcesu monitora, błędy i właściwości wiadomości
Można użyć tych X12 śledzenia schematów na koncie Azure integracji ułatwiają monitorowanie transakcji między firmami (B2B):

* X12 transakcji ustawić schematu śledzenia
* X12 transakcji ustawić potwierdzenia śledzenia schematu
* X12 interchange schematu śledzenia
* X12 interchange potwierdzenia śledzenia schematu
* X12 funkcjonalności grupy schematu śledzenia
* X12 funkcjonalności grupy potwierdzenia śledzenia schematu

## <a name="x12-transaction-set-tracking-schema"></a>X12 transakcji ustawić schematu śledzenia
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "",
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | X12 komunikatów nadawcę partnera. (Opcjonalnie) |
| receiverPartnerName | Ciąg | X12 komunikatów Nazwa partnera odbiorcy. (Opcjonalnie) |
| senderQualifier | Ciąg | Wyślij kwalifikator partnera. (Wymagane) |
| senderIdentifier | Ciąg | Wyślij identyfikator partnera. (Wymagane) |
| receiverQualifier | Ciąg | Odbieranie kwalifikator partnera. (Wymagane) |
| receiverIdentifier | Ciąg | Wyświetlony identyfikator partnera. (Wymagane) |
| agreementName | Ciąg | Nazwa X12 umowy, do którego zostaną rozwiązane wiadomości. (Opcjonalnie) |
| kierunek | wyliczenia | Kierunek przepływu wiadomości odbierać lub wysyłać. (Wymagane) |
| interchangeControlNumber | Ciąg | Wymiany formantu. (Opcjonalnie) |
| functionalGroupControlNumber | Ciąg | Numer funkcjonalności formantu. (Opcjonalnie) |
| transactionSetControlNumber | Ciąg | Transakcja Ustaw numer formantu. (Opcjonalnie) |
| correlationMessageId | Ciąg | Identyfikator korelacji wiadomości. Kombinacja {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Opcjonalnie) |
| messageType | Ciąg | Transakcja ustawić lub typ dokumentu. (Opcjonalnie) |
| isMessageFailed | Wartość logiczna | Czy X12 wiadomości nie powiodło się. (Wymagane) |
| isTechnicalAcknowledgmentExpected | Wartość logiczna | Czy techniczne potwierdzenia jest skonfigurowany w X12 umowy. (Wymagane) |
| isFunctionalAcknowledgmentExpected | Wartość logiczna | Określa, czy funkcjonalności potwierdzenia jest skonfigurowany w X12 umowy. (Wymagane) |
| needAk2LoopForValidMessages | Wartość logiczna | Określa, czy pętli AK2 jest wymagany dla prawidłowego elementu message. (Wymagane) |
| segmentsCount | Liczba całkowita | Liczba segmentów w X12 zestawu transakcji. (Opcjonalnie) |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 transakcji ustawić potwierdzenia śledzenia schematu
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | X12 komunikatów nadawcę partnera. (Opcjonalnie) |
| receiverPartnerName | Ciąg | X12 komunikatów Nazwa partnera odbiorcy. (Opcjonalnie) |
| senderQualifier | Ciąg | Wyślij kwalifikator partnera. (Wymagane) |
| senderIdentifier | Ciąg | Wyślij identyfikator partnera. (Wymagane) |
| receiverQualifier | Ciąg | Odbieranie kwalifikator partnera. (Wymagane) |
| receiverIdentifier | Ciąg | Wyświetlony identyfikator partnera. (Wymagane) |
| agreementName | Ciąg | Nazwa X12 umowy, do którego zostaną rozwiązane wiadomości. (Opcjonalnie) |
| kierunek | wyliczenia | Kierunek przepływu wiadomości odbierać lub wysyłać. (Wymagane) |
| interchangeControlNumber | Ciąg | Wymiany numer formantu funkcjonalności potwierdzenia. Wypełnia wartości tylko na stronie wysyłania, gdzie otrzymania funkcjonalności potwierdzenia dla wiadomości wysyłanych do partnera. (Opcjonalnie) |
| functionalGroupControlNumber | Ciąg | Liczba kontroli grupy funkcjonalnej funkcjonalności potwierdzenia. Wypełnia wartości tylko na stronie wysyłania, gdzie otrzymania funkcjonalności potwierdzenia dla wiadomości wysyłanych do partnera. (Opcjonalnie) |
| isaSegment | Ciąg | Segment ISA wiadomości. Wypełnia wartości tylko na stronie wysyłania, gdzie otrzymania funkcjonalności potwierdzenia dla wiadomości wysyłanych do partnera. (Opcjonalnie) |
| gsSegment | Ciąg | Segment GS wiadomości. Wypełnia wartości tylko na stronie wysyłania, gdzie otrzymania funkcjonalności potwierdzenia dla wiadomości wysyłanych do partnera. (Opcjonalnie) |
| respondingfunctionalGroupControlNumber | Ciąg | Odpowiada numer formantu wymiany. (Opcjonalnie) |
| respondingFunctionalGroupId | Ciąg | Odpowiada identyfikator grupy funkcjonalne, który mapuje AK101 w potwierdzeniu. (Opcjonalnie) |
| respondingtransactionSetControlNumber | Ciąg | Odpowiada transakcji Ustaw numer formantu. (Opcjonalnie) |
| respondingTransactionSetId | Ciąg | Odpowiada transakcji ustawiony identyfikator, który mapuje AK201 w potwierdzeniu. (Opcjonalnie) |
| statusCode | Wartość logiczna | Transakcja Ustaw kod stanu potwierdzenia. (Wymagane) |
| segmentsCount | wyliczenia | Kod stanu potwierdzenia. Dozwolone wartości to **zaakceptowane**, **odrzucone**, i **AcceptedWithErrors**. (Wymagane) |
| processingStatus | wyliczenia | Stan przetwarzania potwierdzenia. Dozwolone wartości to **odebrane**, **Generated**, i **wysłane**. (Wymagane) |
| correlationMessageId | Ciąg | Identyfikator korelacji wiadomości. Kombinacja {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Opcjonalnie) |
| isMessageFailed | Wartość logiczna | Czy X12 wiadomości nie powiodło się. (Wymagane) |
| ak2Segment | Ciąg | Potwierdzenia w grupie funkcjonalne odebranych transakcji. (Opcjonalnie) |
| ak3Segment | Ciąg | Raporty błędów w segmencie danych. (Opcjonalnie) |
| ak5Segment | Ciąg | Raporty, czy transakcja ustawić zidentyfikowanych w segmencie AK2 jest zaakceptowane lub odrzucone i dlaczego. (Opcjonalnie) |

## <a name="x12-interchange-tracking-schema"></a>X12 interchange schematu śledzenia
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | X12 komunikatów nadawcę partnera. (Opcjonalnie) |
| receiverPartnerName | Ciąg | X12 komunikatów Nazwa partnera odbiorcy. (Opcjonalnie) |
| senderQualifier | Ciąg | Wyślij kwalifikator partnera. (Wymagane) |
| senderIdentifier | Ciąg | Wyślij identyfikator partnera. (Wymagane) |
| receiverQualifier | Ciąg | Odbieranie kwalifikator partnera. (Wymagane) |
| receiverIdentifier | Ciąg | Wyświetlony identyfikator partnera. (Wymagane) |
| agreementName | Ciąg | Nazwa X12 umowy, do którego zostaną rozwiązane wiadomości. (Opcjonalnie) |
| kierunek | wyliczenia | Kierunek przepływu wiadomości odbierać lub wysyłać. (Wymagane) |
| interchangeControlNumber | Ciąg | Wymiany formantu. (Opcjonalnie) |
| isaSegment | Ciąg | Segment ISA wiadomości. (Opcjonalnie) |
| isTechnicalAcknowledgmentExpected | Wartość logiczna | Czy techniczne potwierdzenia jest skonfigurowany w X12 umowy. (Wymagane) |
| isMessageFailed | Wartość logiczna | Czy X12 wiadomości nie powiodło się. (Wymagane) |
| isa09 | Ciąg | X12 dokumentu wymiany daty. (Opcjonalnie) |
| isa10 | Ciąg | X12 dokumentu czasu wymiany. (Opcjonalnie) |
| isa11 | Ciąg | X12 interchange kontroli identyfikator standardów. (Opcjonalnie) |
| isa12 | Ciąg | Formant interchange X12 numer wersji. (Opcjonalnie) |
| isa14 | Ciąg | X12 żądania potwierdzenia. (Opcjonalnie) |
| isa15 | Ciąg | Wskaźnik do testu lub produkcji. (Opcjonalnie) |
| isa16 | Ciąg | Element separatora. (Opcjonalnie) |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 interchange potwierdzenia śledzenia schematu
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | X12 komunikatów nadawcę partnera. (Opcjonalnie) |
| receiverPartnerName | Ciąg | X12 komunikatów Nazwa partnera odbiorcy. (Opcjonalnie) |
| senderQualifier | Ciąg | Wyślij kwalifikator partnera. (Wymagane) |
| senderIdentifier | Ciąg | Wyślij identyfikator partnera. (Wymagane) |
| receiverQualifier | Ciąg | Odbieranie kwalifikator partnera. (Wymagane) |
| receiverIdentifier | Ciąg | Wyświetlony identyfikator partnera. (Wymagane) |
| agreementName | Ciąg | Nazwa X12 umowy, do którego zostaną rozwiązane wiadomości. (Opcjonalnie) |
| kierunek | wyliczenia | Kierunek przepływu wiadomości odbierać lub wysyłać. (Wymagane) |
| interchangeControlNumber | Ciąg | Wymiany numer kontroli technicznej potwierdzenia otrzymania przez partnerów. (Opcjonalnie) |
| isaSegment | Ciąg | Segment ISA na potwierdzenie technicznych, odebrany od partnerów. (Opcjonalnie) |
| respondingInterchangeControlNumber |Ciąg | Wymiany numer kontroli technicznej potwierdzenia odebrane z partnerami. (Opcjonalnie) |
| isMessageFailed | Wartość logiczna | Czy X12 wiadomości nie powiodło się. (Wymagane) |
| statusCode | wyliczenia | Wymiany kod stanu potwierdzenia. Dozwolone wartości to **zaakceptowane**, **odrzucone**, i **AcceptedWithErrors**. (Wymagane) |
| processingStatus | wyliczenia | Stan potwierdzenia. Dozwolone wartości to **odebrane**, **Generated**, i **wysłane**. (Wymagane) |
| TA102 | Ciąg | Wymiany daty. (Opcjonalnie) |
| ta103 | Ciąg | Wymiany czasu. (Opcjonalnie) |
| ta105 | Ciąg | Wymiany Uwaga kodu. (Opcjonalnie) |

## <a name="x12-functional-group-tracking-schema"></a>X12 funkcjonalności grupy schematu śledzenia
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | X12 komunikatów nadawcę partnera. (Opcjonalnie) |
| receiverPartnerName | Ciąg | X12 komunikatów Nazwa partnera odbiorcy. (Opcjonalnie) |
| senderQualifier | Ciąg | Wyślij kwalifikator partnera. (Wymagane) |
| senderIdentifier | Ciąg | Wyślij identyfikator partnera. (Wymagane) |
| receiverQualifier | Ciąg | Odbieranie kwalifikator partnera. (Wymagane) |
| receiverIdentifier | Ciąg | Wyświetlony identyfikator partnera. (Wymagane) |
| agreementName | Ciąg | Nazwa X12 umowy, do którego zostaną rozwiązane wiadomości. (Opcjonalnie) |
| kierunek | wyliczenia | Kierunek przepływu wiadomości odbierać lub wysyłać. (Wymagane) |
| interchangeControlNumber | Ciąg | Wymiany formantu. (Opcjonalnie) |
| functionalGroupControlNumber | Ciąg | Numer funkcjonalności formantu. (Opcjonalnie) |
| gsSegment | Ciąg | Komunikat GS segment. (Opcjonalnie) |
| isTechnicalAcknowledgmentExpected | Wartość logiczna | Czy techniczne potwierdzenia jest skonfigurowany w X12 umowy. (Wymagane) |
| isFunctionalAcknowledgmentExpected | Wartość logiczna | Określa, czy funkcjonalności potwierdzenia jest skonfigurowany w X12 umowy. (Wymagane) |
| isMessageFailed | Wartość logiczna | Czy X12 wiadomości nie powiodło się. (Wymagane)|
| gs01 | Ciąg | Kod identyfikatora funkcjonalności. (Opcjonalnie) |
| gs02 | Ciąg | Kod aplikacji nadawcy. (Opcjonalnie) |
| gs03 | Ciąg | Kod aplikacji odbiornika. (Opcjonalnie) |
| gs04 | Ciąg | Data grupy funkcjonalne. (Opcjonalnie) |
| gs05 | Ciąg | Czas grupy funkcjonalne. (Opcjonalnie) |
| gs07 | Ciąg | Kod właściwej agencji. (Opcjonalnie) |
| gs08 | Ciąg | Identyfikator wersji/wydania/branży kodu. (Opcjonalnie) |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 funkcjonalności grupy potwierdzenia śledzenia schematu
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | X12 komunikatów nadawcę partnera. (Opcjonalnie) |
| receiverPartnerName | Ciąg | X12 komunikatów Nazwa partnera odbiorcy. (Opcjonalnie) |
| senderQualifier | Ciąg | Wyślij kwalifikator partnera. (Wymagane) |
| senderIdentifier | Ciąg | Wyślij identyfikator partnera. (Wymagane) |
| receiverQualifier | Ciąg | Odbieranie kwalifikator partnera. (Wymagane) |
| receiverIdentifier | Ciąg | Wyświetlony identyfikator partnera. (Wymagane) |
| agreementName | Ciąg | Nazwa X12 umowy, do którego zostaną rozwiązane wiadomości. (Opcjonalnie) |
| kierunek | wyliczenia | Kierunek przepływu wiadomości odbierać lub wysyłać. (Wymagane) |
| interchangeControlNumber | Ciąg | Wymiany numer formant, który wypełnia na stronie wysyłającej po otrzymaniu potwierdzenia techniczne od partnerów. (Opcjonalnie) |
| functionalGroupControlNumber | Ciąg | Numer kontroli grupy funkcjonalnej potwierdzenia technicznych, który wypełnia na stronie wysyłającej po otrzymaniu potwierdzenia techniczne od partnerów. (Opcjonalnie) |
| isaSegment | Ciąg | Identyczny wymiany kontrolować numerem, ale tylko w szczególnych przypadkach wypełnione. (Opcjonalnie) |
| gsSegment | Ciąg | Grupą funkcjonalne kontrolować numerem, ale tylko w szczególnych przypadkach wypełnione. (Opcjonalnie) |
| respondingfunctionalGroupControlNumber | Ciąg | Numer kontroli oryginalnego grupy funkcjonalne. (Opcjonalnie) |
| respondingFunctionalGroupId | Ciąg | Identyfikator mapy do AK101 w grupie funkcjonalne potwierdzenia. (Opcjonalnie) |
| isMessageFailed | Wartość logiczna | Czy X12 wiadomości nie powiodło się. (Wymagane) |
| statusCode | wyliczenia | Kod stanu potwierdzenia. Dozwolone wartości to **zaakceptowane**, **odrzucone**, i **AcceptedWithErrors**. (Wymagane) |
| processingStatus | wyliczenia | Stan przetwarzania potwierdzenia. Dozwolone wartości to **odebrane**, **Generated**, i **wysłane**. (Wymagane) |
| ak903 | Ciąg | Liczba odebranych zestawy transakcji. (Opcjonalnie) |
| ak904 | Ciąg | Liczba transakcji zestawów przyjęte w grupie zidentyfikowane funkcjonalne. (Opcjonalnie) |
| ak9Segment | Ciąg | Czy grupy funkcjonalne, określonych w segmencie AK1 jest zaakceptowane lub odrzucone i dlaczego. (Opcjonalnie) |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [monitorowanie wiadomości B2B](logic-apps-monitor-b2b-message.md).
* Dowiedz się więcej o [AS2 śledzenia schematy](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md).
* Dowiedz się więcej o [B2B niestandardowych schematów śledzenia](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md).
* Dowiedz się więcej o [śledzenie wiadomości B2B w analizy dzienników](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Dowiedz się więcej o [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md).  
