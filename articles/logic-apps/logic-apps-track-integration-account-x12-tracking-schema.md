---
title: X12 śledzenia schematów na potrzeby obsługi wiadomości B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Utwórz X12 śledzenia schematów, które monitorują komunikatów B2B w konta integracji w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.date: 01/27/2017
ms.openlocfilehash: cfd195f2f812c8b2e09058d325d0dbb6f7a60d59
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125605"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Tworzenie schematów do śledzenia X12 wiadomości w kont integracji dla usługi Azure Logic Apps

Aby ułatwić sukcesu monitora, błędy i właściwości wiadomości dla transakcji (B2B) business-to-business, można użyć tych X12 schematy na koncie integracji śledzenia:

* X12 schemat śledzenia zestawu transakcji
* X12 schemat śledzenia potwierdzenia zestawu transakcji
* X12 wymiany schemat śledzenia
* X12 wymiany potwierdzenie schemat śledzenia
* X12 funkcjonalności grupie schemat śledzenia
* X12 funkcjonalności grupie potwierdzenie schemat śledzenia

## <a name="x12-transaction-set-tracking-schema"></a>X12 schemat śledzenia zestawu transakcji

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
| senderPartnerName | Ciąg | X12 komunikatu nazwę partnera nadawcy. (Opcjonalnie) |
| receiverPartnerName | Ciąg | X12 komunikatu nazwę partnera odbierającego firmy. (Opcjonalnie) |
| Element senderQualifier | Ciąg | Wyślij kwalifikator partnera. (Obowiązkowe) |
| Element senderIdentifier | Ciąg | Wyślij identyfikator partnera. (Obowiązkowe) |
| Element receiverQualifier | Ciąg | Odbieranie kwalifikator partnera. (Obowiązkowe) |
| Element receiverIdentifier | Ciąg | Otrzymać identyfikator partnera. (Obowiązkowe) |
| agreementName | Ciąg | Nazwa X12 umowy, do której wiadomości są rozwiązane. (Opcjonalnie) |
| kierunek | Wyliczenia | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| interchangeControlNumber | Ciąg | Numer kontrolny wymiany. (Opcjonalnie) |
| functionalGroupControlNumber | Ciąg | Numer kontrolny funkcjonalności. (Opcjonalnie) |
| transactionSetControlNumber | Ciąg | Numer kontrolny zestawu transakcji. (Opcjonalnie) |
| CorrelationMessageId | Ciąg | Identyfikator korelacji wiadomości. Kombinacja {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Opcjonalnie) |
| MessageType | Ciąg | Transakcja zestawu lub typu dokumentu. (Opcjonalnie) |
| isMessageFailed | Wartość logiczna | Czy X12 wiadomości nie powiodło się. (Obowiązkowe) |
| isTechnicalAcknowledgmentExpected | Wartość logiczna | Czy pomoc potwierdzenia jest skonfigurowana w X12 umowy. (Obowiązkowe) |
| isFunctionalAcknowledgmentExpected | Wartość logiczna | Czy funkcjonalności potwierdzenia jest skonfigurowany w X12 umowy. (Obowiązkowe) |
| needAk2LoopForValidMessages | Wartość logiczna | Czy pętla pętlę AK2 jest wymagany prawidłowy komunikat. (Obowiązkowe) |
| segmentsCount | Liczba całkowita | Liczba segmentów w X12 zestawu transakcji. (Opcjonalnie) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 schemat śledzenia potwierdzenia zestawu transakcji

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
| senderPartnerName | Ciąg | X12 komunikatu nazwę partnera nadawcy. (Opcjonalnie) |
| receiverPartnerName | Ciąg | X12 komunikatu nazwę partnera odbierającego firmy. (Opcjonalnie) |
| Element senderQualifier | Ciąg | Wyślij kwalifikator partnera. (Obowiązkowe) |
| Element senderIdentifier | Ciąg | Wyślij identyfikator partnera. (Obowiązkowe) |
| Element receiverQualifier | Ciąg | Odbieranie kwalifikator partnera. (Obowiązkowe) |
| Element receiverIdentifier | Ciąg | Otrzymać identyfikator partnera. (Obowiązkowe) |
| agreementName | Ciąg | Nazwa X12 umowy, do której wiadomości są rozwiązane. (Opcjonalnie) |
| kierunek | Wyliczenia | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| interchangeControlNumber | Ciąg | Numer kontrolny potwierdzenia funkcjonalności wymiany. Wartość wypełnia tylko w przypadku stronie wysyłania, w którym otrzymania funkcjonalności potwierdzenia dla wiadomości wysyłanych do partnera. (Opcjonalnie) |
| functionalGroupControlNumber | Ciąg | Numer kontrolny grupy funkcjonalnej funkcjonalności potwierdzenia. Wartość wypełnia tylko w przypadku stronie wysyłania, w którym otrzymania funkcjonalności potwierdzenia dla wiadomości wysyłanych do partnera. (Opcjonalnie) |
| isaSegment | Ciąg | Segment ISA wiadomości. Wartość wypełnia tylko w przypadku stronie wysyłania, w którym otrzymania funkcjonalności potwierdzenia dla wiadomości wysyłanych do partnera. (Opcjonalnie) |
| gsSegment | Ciąg | Segment GS wiadomości. Wartość wypełnia tylko w przypadku stronie wysyłania, w którym otrzymania funkcjonalności potwierdzenia dla wiadomości wysyłanych do partnera. (Opcjonalnie) |
| respondingfunctionalGroupControlNumber | Ciąg | Odpowiada numeru kontrolnego wymiany. (Opcjonalnie) |
| respondingFunctionalGroupId | Ciąg | Odpowiada identyfikator grupy funkcjonalnej, który mapuje AK101 w potwierdzeniu. (Opcjonalnie) |
| respondingtransactionSetControlNumber | Ciąg | Numer kontrolny zestawu transakcji odpowiada. (Opcjonalnie) |
| respondingTransactionSetId | Ciąg | Transakcja odpowiada Ustaw identyfikator, który mapuje AK201 w potwierdzeniu. (Opcjonalnie) |
| statusCode | Wartość logiczna | Kod stanu potwierdzenia zestawu transakcji. (Obowiązkowe) |
| segmentsCount | Wyliczenia | Kod stanu potwierdzenia. Dozwolone wartości to **zaakceptowano**, **odrzucony**, i **AcceptedWithErrors**. (Obowiązkowe) |
| processingStatus | Wyliczenia | Stan przetwarzania potwierdzenia. Dozwolone wartości to **odebrane**, **Generated**, i **wysłane**. (Obowiązkowe) |
| CorrelationMessageId | Ciąg | Identyfikator korelacji wiadomości. Kombinacja {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Opcjonalnie) |
| isMessageFailed | Wartość logiczna | Czy X12 wiadomości nie powiodło się. (Obowiązkowe) |
| ak2Segment | Ciąg | Potwierdzenie dla transakcji w grupie funkcjonalnej odebrane. (Opcjonalnie) |
| ak3Segment | Ciąg | Zgłasza błędy w segmencie danych. (Opcjonalnie) |
| ak5Segment | Ciąg | Raporty, czy zestaw określonych w segmencie pętlę AK2 transakcji zostanie zaakceptowany lub odrzucony i dlaczego. (Opcjonalnie) |
||||

## <a name="x12-interchange-tracking-schema"></a>X12 wymiany schemat śledzenia

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
| senderPartnerName | Ciąg | X12 komunikatu nazwę partnera nadawcy. (Opcjonalnie) |
| receiverPartnerName | Ciąg | X12 komunikatu nazwę partnera odbierającego firmy. (Opcjonalnie) |
| Element senderQualifier | Ciąg | Wyślij kwalifikator partnera. (Obowiązkowe) |
| Element senderIdentifier | Ciąg | Wyślij identyfikator partnera. (Obowiązkowe) |
| Element receiverQualifier | Ciąg | Odbieranie kwalifikator partnera. (Obowiązkowe) |
| Element receiverIdentifier | Ciąg | Otrzymać identyfikator partnera. (Obowiązkowe) |
| agreementName | Ciąg | Nazwa X12 umowy, do której wiadomości są rozwiązane. (Opcjonalnie) |
| kierunek | Wyliczenia | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| interchangeControlNumber | Ciąg | Numer kontrolny wymiany. (Opcjonalnie) |
| isaSegment | Ciąg | Segment ISA wiadomości. (Opcjonalnie) |
| isTechnicalAcknowledgmentExpected | Wartość logiczna | Czy pomoc potwierdzenia jest skonfigurowana w X12 umowy. (Obowiązkowe) |
| isMessageFailed | Wartość logiczna | Czy X12 wiadomości nie powiodło się. (Obowiązkowe) |
| isa09 | Ciąg | X12 dokumentu wymiany daty. (Opcjonalnie) |
| isa10 | Ciąg | X12 dokumentu czasu wymiany. (Opcjonalnie) |
| isa11 | Ciąg | X12 wymiany kontroli identyfikator standardy. (Opcjonalnie) |
| isa12 | Ciąg | X12 wymiany kontroli numer wersji. (Opcjonalnie) |
| isa14 | Ciąg | X12 zażądano potwierdzenia. (Opcjonalnie) |
| isa15 | Ciąg | Wskaźnik do testów lub produkcji. (Opcjonalnie) |
| isa16 | Ciąg | Separator elementów. (Opcjonalnie) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 wymiany potwierdzenie schemat śledzenia

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
| senderPartnerName | Ciąg | X12 komunikatu nazwę partnera nadawcy. (Opcjonalnie) |
| receiverPartnerName | Ciąg | X12 komunikatu nazwę partnera odbierającego firmy. (Opcjonalnie) |
| Element senderQualifier | Ciąg | Wyślij kwalifikator partnera. (Obowiązkowe) |
| Element senderIdentifier | Ciąg | Wyślij identyfikator partnera. (Obowiązkowe) |
| Element receiverQualifier | Ciąg | Odbieranie kwalifikator partnera. (Obowiązkowe) |
| Element receiverIdentifier | Ciąg | Otrzymać identyfikator partnera. (Obowiązkowe) |
| agreementName | Ciąg | Nazwa X12 umowy, do której wiadomości są rozwiązane. (Opcjonalnie) |
| kierunek | Wyliczenia | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| interchangeControlNumber | Ciąg | Numer kontrolny potwierdzenia technicznych, odebrane od partnerów wymiany. (Opcjonalnie) |
| isaSegment | Ciąg | Segment ISA na potwierdzenie technicznych, odebrane od partnerów. (Opcjonalnie) |
| respondingInterchangeControlNumber |Ciąg | Numer kontrolny potwierdzenia technicznych, odebrane od partnerów wymiany. (Opcjonalnie) |
| isMessageFailed | Wartość logiczna | Czy X12 wiadomości nie powiodło się. (Obowiązkowe) |
| statusCode | Wyliczenia | Wymiany kod stanu potwierdzenia. Dozwolone wartości to **zaakceptowano**, **odrzucony**, i **AcceptedWithErrors**. (Obowiązkowe) |
| processingStatus | Wyliczenia | Stan potwierdzenia. Dozwolone wartości to **odebrane**, **Generated**, i **wysłane**. (Obowiązkowe) |
| TA102 | Ciąg | Wymiany daty. (Opcjonalnie) |
| ta103 | Ciąg | Wymiany czasu. (Opcjonalnie) |
| ta105 | Ciąg | Wymiany Uwaga kodu. (Opcjonalnie) |
||||

## <a name="x12-functional-group-tracking-schema"></a>X12 funkcjonalności grupie schemat śledzenia

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
| senderPartnerName | Ciąg | X12 komunikatu nazwę partnera nadawcy. (Opcjonalnie) |
| receiverPartnerName | Ciąg | X12 komunikatu nazwę partnera odbierającego firmy. (Opcjonalnie) |
| Element senderQualifier | Ciąg | Wyślij kwalifikator partnera. (Obowiązkowe) |
| Element senderIdentifier | Ciąg | Wyślij identyfikator partnera. (Obowiązkowe) |
| Element receiverQualifier | Ciąg | Odbieranie kwalifikator partnera. (Obowiązkowe) |
| Element receiverIdentifier | Ciąg | Otrzymać identyfikator partnera. (Obowiązkowe) |
| agreementName | Ciąg | Nazwa X12 umowy, do której wiadomości są rozwiązane. (Opcjonalnie) |
| kierunek | Wyliczenia | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| interchangeControlNumber | Ciąg | Numer kontrolny wymiany. (Opcjonalnie) |
| functionalGroupControlNumber | Ciąg | Numer kontrolny funkcjonalności. (Opcjonalnie) |
| gsSegment | Ciąg | Segment komunikat GS. (Opcjonalnie) |
| isTechnicalAcknowledgmentExpected | Wartość logiczna | Czy pomoc potwierdzenia jest skonfigurowana w X12 umowy. (Obowiązkowe) |
| isFunctionalAcknowledgmentExpected | Wartość logiczna | Czy funkcjonalności potwierdzenia jest skonfigurowany w X12 umowy. (Obowiązkowe) |
| isMessageFailed | Wartość logiczna | Czy X12 wiadomości nie powiodło się. (Obowiązkowe)|
| gs01 | Ciąg | Identyfikator funkcjonalny kod. (Opcjonalnie) |
| gs02 | Ciąg | Kod aplikacji nadawcy. (Opcjonalnie) |
| gs03 | Ciąg | Kod aplikacji odbiornika. (Opcjonalnie) |
| gs04 | Ciąg | Data grupy funkcjonalnej. (Opcjonalnie) |
| gs05 | Ciąg | Czas grupy funkcjonalnej. (Opcjonalnie) |
| gs07 | Ciąg | Kod odpowiada Agencji. (Opcjonalnie) |
| gs08 | Ciąg | Identyfikator wersji/wydania/branży kod. (Opcjonalnie) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 funkcjonalności grupie potwierdzenie schemat śledzenia

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
| senderPartnerName | Ciąg | X12 komunikatu nazwę partnera nadawcy. (Opcjonalnie) |
| receiverPartnerName | Ciąg | X12 komunikatu nazwę partnera odbierającego firmy. (Opcjonalnie) |
| Element senderQualifier | Ciąg | Wyślij kwalifikator partnera. (Obowiązkowe) |
| Element senderIdentifier | Ciąg | Wyślij identyfikator partnera. (Obowiązkowe) |
| Element receiverQualifier | Ciąg | Odbieranie kwalifikator partnera. (Obowiązkowe) |
| Element receiverIdentifier | Ciąg | Otrzymać identyfikator partnera. (Obowiązkowe) |
| agreementName | Ciąg | Nazwa X12 umowy, do której wiadomości są rozwiązane. (Opcjonalnie) |
| kierunek | Wyliczenia | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| interchangeControlNumber | Ciąg | Numer kontrolny wymiany, które wypełnia po stronie wysyłania po otrzymaniu potwierdzenia pomoc od partnerów. (Opcjonalnie) |
| functionalGroupControlNumber | Ciąg | Numer kontrolny grupy funkcjonalnej potwierdzenia technicznych, które wypełnia po stronie wysyłania po otrzymaniu potwierdzenia pomoc od partnerów. (Opcjonalnie) |
| isaSegment | Ciąg | Takie same jak wymiany kontrolować liczbę, ale wypełnione tylko w szczególnych przypadkach. (Opcjonalnie) |
| gsSegment | Ciąg | Takie same jak grupa funkcjonalna kontrolować liczbę, ale wypełnione tylko w szczególnych przypadkach. (Opcjonalnie) |
| respondingfunctionalGroupControlNumber | Ciąg | Numer kontrolny grupy oryginalnego funkcjonalnej. (Opcjonalnie) |
| respondingFunctionalGroupId | Ciąg | Mapuje do AK101 w grupie funkcjonalnej potwierdzania identyfikatora. (Opcjonalnie) |
| isMessageFailed | Wartość logiczna | Czy X12 wiadomości nie powiodło się. (Obowiązkowe) |
| statusCode | Wyliczenia | Kod stanu potwierdzenia. Dozwolone wartości to **zaakceptowano**, **odrzucony**, i **AcceptedWithErrors**. (Obowiązkowe) |
| processingStatus | Wyliczenia | Stan przetwarzania potwierdzenia. Dozwolone wartości to **odebrane**, **Generated**, i **wysłane**. (Obowiązkowe) |
| ak903 | Ciąg | Liczba zestawów transakcji odebrane. (Opcjonalnie) |
| ak904 | Ciąg | Akceptowane liczby zestawów transakcji w grupie funkcjonalnej zidentyfikowane. (Opcjonalnie) |
| ak9Segment | Ciąg | Czy grupy funkcjonalnej identyfikowane w segmencie AK1 jest zaakceptowane lub odrzucone i dlaczego. (Opcjonalnie) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołu B2B

Aby uzyskać informacji na temat protokołu B2B schematy śledzenia zobacz:

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Niestandardowe schematy śledzenia B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [monitorowanie komunikatów B2B](logic-apps-monitor-b2b-message.md).
* Dowiedz się więcej o [śledzenie komunikatów B2B w usłudze Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
