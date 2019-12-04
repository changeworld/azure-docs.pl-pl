---
title: Schematy śledzenia X12 dla komunikatów B2B
description: Tworzenie schematów śledzenia X12, które monitorują komunikaty B2B na kontach integracji dla Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 6f2356600f5b6a637da731c650b26d968092e2f6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791711"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Tworzenie schematów śledzenia komunikatów X12 na kontach integracji dla Azure Logic Apps

Aby ułatwić monitorowanie sukcesu, błędów i właściwości komunikatów dla transakcji typu B2B (Business-to-Business), możesz użyć tych schematów śledzenia X12 na koncie integracji:

* Schemat śledzenia zestawu transakcji X12
* Schemat śledzenia potwierdzania zestawu transakcji X12
* Schemat śledzenia X12 Interchange
* Schemat śledzenia potwierdzania wymiany X12
* Schemat śledzenia grup funkcjonalnych X12
* Schemat śledzenia potwierdzania grup funkcjonalnych X12

## <a name="x12-transaction-set-tracking-schema"></a>Schemat śledzenia zestawu transakcji X12

```json
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
```

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | Nazwa partnera nadawcy wiadomości X12. Obowiązkowe |
| receiverPartnerName | Ciąg | Nazwa partnera odbiorcy komunikatu X12. Obowiązkowe |
| senderQualifier | Ciąg | Wyślij kwalifikator partnera. Wypełnione |
| senderIdentifier | Ciąg | Wyślij identyfikator partnera. Wypełnione |
| receiverQualifier | Ciąg | Odbierz kwalifikator partnera. Wypełnione |
| receiverIdentifier | Ciąg | Odbierz identyfikator partnera. Wypełnione |
| umowaname | Ciąg | Nazwa umowy X12, do której komunikaty są rozwiązywane. Obowiązkowe |
| kierunek | Wyliczenie | Kierunek przepływu wiadomości, odbieranie lub wysyłanie. Wypełnione |
| interchangeControlNumber | Ciąg | Numer kontrolny wymiany. Obowiązkowe |
| functionalGroupControlNumber | Ciąg | Numer kontroli funkcjonalnej. Obowiązkowe |
| transactionSetControlNumber | Ciąg | Numer kontrolny zestawu transakcji. Obowiązkowe |
| correlationMessageId | Ciąg | Identyfikator komunikatu korelacji. Kombinacja {Agreementname} {*GroupControlNumber*} {TransactionSetControlNumber}. Obowiązkowe |
| messageType | Ciąg | Zestaw transakcji lub typ dokumentu. Obowiązkowe |
| isMessageFailed | Wartość logiczna | Czy komunikat X12 nie powiódł się. Wypełnione |
| isTechnicalAcknowledgmentExpected | Wartość logiczna | Czy potwierdzenie techniczne jest skonfigurowane w umowie X12. Wypełnione |
| isFunctionalAcknowledgmentExpected | Wartość logiczna | Czy potwierdzenie funkcjonalne jest skonfigurowane w umowie X12. Wypełnione |
| needAk2LoopForValidMessages | Wartość logiczna | Czy pętla AK2 jest wymagana dla prawidłowej wiadomości. Wypełnione |
| segmentsCount | Liczba całkowita | Liczba segmentów w zestawie transakcji X12. Obowiązkowe |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Schemat śledzenia potwierdzania zestawu transakcji X12

```json
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
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | Nazwa partnera nadawcy wiadomości X12. Obowiązkowe |
| receiverPartnerName | Ciąg | Nazwa partnera odbiorcy komunikatu X12. Obowiązkowe |
| senderQualifier | Ciąg | Wyślij kwalifikator partnera. Wypełnione |
| senderIdentifier | Ciąg | Wyślij identyfikator partnera. Wypełnione |
| receiverQualifier | Ciąg | Odbierz kwalifikator partnera. Wypełnione |
| receiverIdentifier | Ciąg | Odbierz identyfikator partnera. Wypełnione |
| umowaname | Ciąg | Nazwa umowy X12, do której komunikaty są rozwiązywane. Obowiązkowe |
| kierunek | Wyliczenie | Kierunek przepływu wiadomości, odbieranie lub wysyłanie. Wypełnione |
| interchangeControlNumber | Ciąg | Numer kontroli wymiany dla potwierdzenia funkcjonalności. Wartość jest wypełniana tylko dla strony wysyłającej, gdy zostanie odebrane potwierdzenie funkcjonalne dla wiadomości wysyłanych do partnera. Obowiązkowe |
| functionalGroupControlNumber | Ciąg | Numer kontroli grupy funkcjonalnej potwierdzania funkcjonalności. Wartość jest wypełniana tylko dla strony wysyłającej, gdy zostanie odebrane potwierdzenie funkcjonalne dla wiadomości wysyłanych do partnera. Obowiązkowe |
| isaSegment | Ciąg | Segment ISA wiadomości. Wartość jest wypełniana tylko dla strony wysyłającej, gdy zostanie odebrane potwierdzenie funkcjonalne dla wiadomości wysyłanych do partnera. Obowiązkowe |
| gsSegment | Ciąg | Segment GS komunikatu. Wartość jest wypełniana tylko dla strony wysyłającej, gdy zostanie odebrane potwierdzenie funkcjonalne dla wiadomości wysyłanych do partnera. Obowiązkowe |
| respondingfunctionalGroupControlNumber | Ciąg | Odpowiada numeru kontroli wymiany. Obowiązkowe |
| respondingFunctionalGroupId | Ciąg | Odpowiadający identyfikator grupy funkcjonalnej, który jest mapowany do AK101 w potwierdzeniu. Obowiązkowe |
| respondingtransactionSetControlNumber | Ciąg | Numer kontrolny zestawu transakcji odpowiadający. Obowiązkowe |
| respondingTransactionSetId | Ciąg | Identyfikator zestawu transakcji odpowiadający AK201 w potwierdzeniu. Obowiązkowe |
| Stanu | Wartość logiczna | Kod stanu potwierdzenia zestawu transakcji. Wypełnione |
| segmentsCount | Wyliczenie | Kod stanu potwierdzenia. Dozwolone wartości to **akceptowane**, **odrzucone**i **AcceptedWithErrors**. Wypełnione |
| processingStatus | Wyliczenie | Stan przetwarzania potwierdzenia. Dozwolone wartości są **odbierane**, **generowane**i **wysyłane**. Wypełnione |
| correlationMessageId | Ciąg | Identyfikator komunikatu korelacji. Kombinacja {Agreementname} {*GroupControlNumber*} {TransactionSetControlNumber}. Obowiązkowe |
| isMessageFailed | Wartość logiczna | Czy komunikat X12 nie powiódł się. Wypełnione |
| ak2Segment | Ciąg | Potwierdzenie dla zestawu transakcji w odebranej grupie funkcjonalnej. Obowiązkowe |
| ak3Segment | Ciąg | Raportuje błędy w segmencie danych. Obowiązkowe |
| ak5Segment | Ciąg | Określa, czy zestaw transakcji zidentyfikowany w segmencie AK2 jest akceptowany, czy odrzucany, i dlaczego. Obowiązkowe |
||||

## <a name="x12-interchange-tracking-schema"></a>Schemat śledzenia X12 Interchange

```json
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
```

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | Nazwa partnera nadawcy wiadomości X12. Obowiązkowe |
| receiverPartnerName | Ciąg | Nazwa partnera odbiorcy komunikatu X12. Obowiązkowe |
| senderQualifier | Ciąg | Wyślij kwalifikator partnera. Wypełnione |
| senderIdentifier | Ciąg | Wyślij identyfikator partnera. Wypełnione |
| receiverQualifier | Ciąg | Odbierz kwalifikator partnera. Wypełnione |
| receiverIdentifier | Ciąg | Odbierz identyfikator partnera. Wypełnione |
| umowaname | Ciąg | Nazwa umowy X12, do której komunikaty są rozwiązywane. Obowiązkowe |
| kierunek | Wyliczenie | Kierunek przepływu wiadomości, odbieranie lub wysyłanie. Wypełnione |
| interchangeControlNumber | Ciąg | Numer kontrolny wymiany. Obowiązkowe |
| isaSegment | Ciąg | Segment ISA komunikatów. Obowiązkowe |
| isTechnicalAcknowledgmentExpected | Wartość logiczna | Czy potwierdzenie techniczne jest skonfigurowane w umowie X12. Wypełnione |
| isMessageFailed | Wartość logiczna | Czy komunikat X12 nie powiódł się. Wypełnione |
| isa09 | Ciąg | Data wymiany dokumentu X12. Obowiązkowe |
| isa10 | Ciąg | Czas wymiany dokumentu X12. Obowiązkowe |
| isa11 | Ciąg | Identyfikator standardów kontroli wymiany X12. Obowiązkowe |
| isa12 | Ciąg | Numer wersji programu X12 Interchange Control. Obowiązkowe |
| isa14 | Ciąg | Zażądano potwierdzenia X12. Obowiązkowe |
| isa15 | Ciąg | Wskaźnik dla testu lub produkcji. Obowiązkowe |
| isa16 | Ciąg | Separator elementu. Obowiązkowe |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Schemat śledzenia potwierdzania wymiany X12

```json
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
```

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | Nazwa partnera nadawcy wiadomości X12. Obowiązkowe |
| receiverPartnerName | Ciąg | Nazwa partnera odbiorcy komunikatu X12. Obowiązkowe |
| senderQualifier | Ciąg | Wyślij kwalifikator partnera. Wypełnione |
| senderIdentifier | Ciąg | Wyślij identyfikator partnera. Wypełnione |
| receiverQualifier | Ciąg | Odbierz kwalifikator partnera. Wypełnione |
| receiverIdentifier | Ciąg | Odbierz identyfikator partnera. Wypełnione |
| umowaname | Ciąg | Nazwa umowy X12, do której komunikaty są rozwiązywane. Obowiązkowe |
| kierunek | Wyliczenie | Kierunek przepływu wiadomości, odbieranie lub wysyłanie. Wypełnione |
| interchangeControlNumber | Ciąg | Numer kontrolny wymiany potwierdzania technicznego otrzymane od partnerów. Obowiązkowe |
| isaSegment | Ciąg | Segment ISA dla potwierdzenia technicznego otrzymanego od partnerów. Obowiązkowe |
| respondingInterchangeControlNumber |Ciąg | Numer kontroli wymiany dla potwierdzenia technicznego otrzymanego od partnerów. Obowiązkowe |
| isMessageFailed | Wartość logiczna | Czy komunikat X12 nie powiódł się. Wypełnione |
| Stanu | Wyliczenie | Kod stanu potwierdzenia wymiany. Dozwolone wartości to **akceptowane**, **odrzucone**i **AcceptedWithErrors**. Wypełnione |
| processingStatus | Wyliczenie | Stan potwierdzenia. Dozwolone wartości są **odbierane**, **generowane**i **wysyłane**. Wypełnione |
| ta102 | Ciąg | Data wymiany. Obowiązkowe |
| ta103 | Ciąg | Czas wymiany. Obowiązkowe |
| ta105 | Ciąg | Kod wymiany notatki. Obowiązkowe |
||||

## <a name="x12-functional-group-tracking-schema"></a>Schemat śledzenia grup funkcjonalnych X12

```json
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
```

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | Nazwa partnera nadawcy wiadomości X12. Obowiązkowe |
| receiverPartnerName | Ciąg | Nazwa partnera odbiorcy komunikatu X12. Obowiązkowe |
| senderQualifier | Ciąg | Wyślij kwalifikator partnera. Wypełnione |
| senderIdentifier | Ciąg | Wyślij identyfikator partnera. Wypełnione |
| receiverQualifier | Ciąg | Odbierz kwalifikator partnera. Wypełnione |
| receiverIdentifier | Ciąg | Odbierz identyfikator partnera. Wypełnione |
| umowaname | Ciąg | Nazwa umowy X12, do której komunikaty są rozwiązywane. Obowiązkowe |
| kierunek | Wyliczenie | Kierunek przepływu wiadomości, odbieranie lub wysyłanie. Wypełnione |
| interchangeControlNumber | Ciąg | Numer kontrolny wymiany. Obowiązkowe |
| functionalGroupControlNumber | Ciąg | Numer kontroli funkcjonalnej. Obowiązkowe |
| gsSegment | Ciąg | Segment GS komunikatu. Obowiązkowe |
| isTechnicalAcknowledgmentExpected | Wartość logiczna | Czy potwierdzenie techniczne jest skonfigurowane w umowie X12. Wypełnione |
| isFunctionalAcknowledgmentExpected | Wartość logiczna | Czy potwierdzenie funkcjonalne jest skonfigurowane w umowie X12. Wypełnione |
| isMessageFailed | Wartość logiczna | Czy komunikat X12 nie powiódł się. Wypełnione|
| gs01 | Ciąg | Kod identyfikatora funkcjonalności. Obowiązkowe |
| gs02 | Ciąg | Kod nadawcy aplikacji. Obowiązkowe |
| gs03 | Ciąg | Kod odbiorcy aplikacji. Obowiązkowe |
| gs04 | Ciąg | Data grupy funkcjonalnej. Obowiązkowe |
| gs05 | Ciąg | Czas grupy funkcjonalnej. Obowiązkowe |
| gs07 | Ciąg | Właściwy kod agencji. Obowiązkowe |
| gs08 | Ciąg | Wersja/wersja/kod identyfikatora branżowego. Obowiązkowe |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Schemat śledzenia potwierdzania grup funkcjonalnych X12

```json
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
```

| Właściwość | Typ | Opis |
| --- | --- | --- |
| senderPartnerName | Ciąg | Nazwa partnera nadawcy wiadomości X12. Obowiązkowe |
| receiverPartnerName | Ciąg | Nazwa partnera odbiorcy komunikatu X12. Obowiązkowe |
| senderQualifier | Ciąg | Wyślij kwalifikator partnera. Wypełnione |
| senderIdentifier | Ciąg | Wyślij identyfikator partnera. Wypełnione |
| receiverQualifier | Ciąg | Odbierz kwalifikator partnera. Wypełnione |
| receiverIdentifier | Ciąg | Odbierz identyfikator partnera. Wypełnione |
| umowaname | Ciąg | Nazwa umowy X12, do której komunikaty są rozwiązywane. Obowiązkowe |
| kierunek | Wyliczenie | Kierunek przepływu wiadomości, odbieranie lub wysyłanie. Wypełnione |
| interchangeControlNumber | Ciąg | Numer kontrolny wymiany, który jest wypełniany po stronie wysyłania w przypadku otrzymania potwierdzenia technicznego od partnerów. Obowiązkowe |
| functionalGroupControlNumber | Ciąg | Numer kontroli grupy funkcjonalnej potwierdzania technicznego, który jest wypełniany po stronie nadawcy w przypadku otrzymania potwierdzenia technicznego od partnerów. Obowiązkowe |
| isaSegment | Ciąg | Analogicznie jak numer kontrolny wymiany, ale wypełniane tylko w określonych przypadkach. Obowiązkowe |
| gsSegment | Ciąg | Taki sam jak numer kontroli grupy funkcjonalnej, ale wypełniany tylko w określonych przypadkach. Obowiązkowe |
| respondingfunctionalGroupControlNumber | Ciąg | Numer kontrolny oryginalnej grupy funkcjonalnej. Obowiązkowe |
| respondingFunctionalGroupId | Ciąg | Mapuje do AK101 w identyfikator grupy funkcjonalnej potwierdzenia. Obowiązkowe |
| isMessageFailed | Wartość logiczna | Czy komunikat X12 nie powiódł się. Wypełnione |
| Stanu | Wyliczenie | Kod stanu potwierdzenia. Dozwolone wartości to **akceptowane**, **odrzucone**i **AcceptedWithErrors**. Wypełnione |
| processingStatus | Wyliczenie | Stan przetwarzania potwierdzenia. Dozwolone wartości są **odbierane**, **generowane**i **wysyłane**. Wypełnione |
| ak903 | Ciąg | Liczba odebranych zestawów transakcji. Obowiązkowe |
| ak904 | Ciąg | Liczba zestawów transakcji zaakceptowanych w określonej grupie funkcjonalnej. Obowiązkowe |
| ak9Segment | Ciąg | Czy grupa funkcjonalna identyfikowana w segmencie AK1 jest akceptowana, odrzucana i dlaczego. Obowiązkowe |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołu B2B

Aby uzyskać informacje o schematach śledzenia protokołu B2B, zobacz:

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Niestandardowe schematy śledzenia B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [monitorowaniu komunikatów B2B](logic-apps-monitor-b2b-message.md).
* Informacje [na temat śledzenia komunikatów B2B w dziennikach Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
