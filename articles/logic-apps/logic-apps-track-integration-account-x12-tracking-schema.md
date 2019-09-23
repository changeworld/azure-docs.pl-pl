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
ms.openlocfilehash: 1db324006e1e6332b5fdd8afd28ebed8a32ac707
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845770"
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
| senderPartnerName | String | X12 komunikatu nazwę partnera nadawcy. (Opcjonalnie) |
| receiverPartnerName | String | X12 komunikatu nazwę partnera odbierającego firmy. (Opcjonalnie) |
| senderQualifier | String | Wyślij kwalifikator partnera. (Obowiązkowe) |
| senderIdentifier | String | Wyślij identyfikator partnera. (Obowiązkowe) |
| receiverQualifier | String | Odbieranie kwalifikator partnera. (Obowiązkowe) |
| receiverIdentifier | String | Otrzymać identyfikator partnera. (Obowiązkowe) |
| agreementName | String | Nazwa X12 umowy, do której wiadomości są rozwiązane. (Opcjonalnie) |
| direction | Enum | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| interchangeControlNumber | String | Numer kontrolny wymiany. (Opcjonalnie) |
| functionalGroupControlNumber | String | Numer kontrolny funkcjonalności. (Opcjonalnie) |
| transactionSetControlNumber | String | Numer kontrolny zestawu transakcji. (Opcjonalnie) |
| CorrelationMessageId | String | Identyfikator korelacji wiadomości. Kombinacja {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Opcjonalnie) |
| messageType | String | Transakcja zestawu lub typu dokumentu. (Opcjonalnie) |
| isMessageFailed | Boolean | Czy X12 wiadomości nie powiodło się. (Obowiązkowe) |
| isTechnicalAcknowledgmentExpected | Boolean | Czy pomoc potwierdzenia jest skonfigurowana w X12 umowy. (Obowiązkowe) |
| isFunctionalAcknowledgmentExpected | Boolean | Czy funkcjonalności potwierdzenia jest skonfigurowany w X12 umowy. (Obowiązkowe) |
| needAk2LoopForValidMessages | Boolean | Czy pętla pętlę AK2 jest wymagany prawidłowy komunikat. (Obowiązkowe) |
| segmentsCount | Integer | Liczba segmentów w X12 zestawu transakcji. (Opcjonalnie) |
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
| senderPartnerName | String | X12 komunikatu nazwę partnera nadawcy. (Opcjonalnie) |
| receiverPartnerName | String | X12 komunikatu nazwę partnera odbierającego firmy. (Opcjonalnie) |
| senderQualifier | String | Wyślij kwalifikator partnera. (Obowiązkowe) |
| senderIdentifier | String | Wyślij identyfikator partnera. (Obowiązkowe) |
| receiverQualifier | String | Odbieranie kwalifikator partnera. (Obowiązkowe) |
| receiverIdentifier | String | Otrzymać identyfikator partnera. (Obowiązkowe) |
| agreementName | String | Nazwa X12 umowy, do której wiadomości są rozwiązane. (Opcjonalnie) |
| direction | Enum | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| interchangeControlNumber | String | Numer kontrolny potwierdzenia funkcjonalności wymiany. Wartość wypełnia tylko w przypadku stronie wysyłania, w którym otrzymania funkcjonalności potwierdzenia dla wiadomości wysyłanych do partnera. (Opcjonalnie) |
| functionalGroupControlNumber | String | Numer kontrolny grupy funkcjonalnej funkcjonalności potwierdzenia. Wartość wypełnia tylko w przypadku stronie wysyłania, w którym otrzymania funkcjonalności potwierdzenia dla wiadomości wysyłanych do partnera. (Opcjonalnie) |
| isaSegment | String | Segment ISA wiadomości. Wartość wypełnia tylko w przypadku stronie wysyłania, w którym otrzymania funkcjonalności potwierdzenia dla wiadomości wysyłanych do partnera. (Opcjonalnie) |
| gsSegment | String | Segment GS wiadomości. Wartość wypełnia tylko w przypadku stronie wysyłania, w którym otrzymania funkcjonalności potwierdzenia dla wiadomości wysyłanych do partnera. (Opcjonalnie) |
| respondingfunctionalGroupControlNumber | String | Odpowiada numeru kontrolnego wymiany. (Opcjonalnie) |
| respondingFunctionalGroupId | String | Odpowiada identyfikator grupy funkcjonalnej, który mapuje AK101 w potwierdzeniu. (Opcjonalnie) |
| respondingtransactionSetControlNumber | String | Numer kontrolny zestawu transakcji odpowiada. (Opcjonalnie) |
| respondingTransactionSetId | String | Transakcja odpowiada Ustaw identyfikator, który mapuje AK201 w potwierdzeniu. (Opcjonalnie) |
| statusCode | Boolean | Kod stanu potwierdzenia zestawu transakcji. (Obowiązkowe) |
| segmentsCount | Enum | Kod stanu potwierdzenia. Dozwolone wartości to **Accepted**, **Rejected**, i **AcceptedWithErrors**. (Obowiązkowe) |
| processingStatus | Enum | Stan przetwarzania potwierdzenia. Dozwolone wartości to **odebrane**, **Generated**, i **wysłane**. (Obowiązkowe) |
| CorrelationMessageId | String | Identyfikator korelacji wiadomości. Kombinacja {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Opcjonalnie) |
| isMessageFailed | Boolean | Czy X12 wiadomości nie powiodło się. (Obowiązkowe) |
| ak2Segment | String | Potwierdzenie dla transakcji w grupie funkcjonalnej odebrane. (Opcjonalnie) |
| ak3Segment | String | Zgłasza błędy w segmencie danych. (Opcjonalnie) |
| ak5Segment | String | Raporty, czy zestaw określonych w segmencie pętlę AK2 transakcji zostanie zaakceptowany lub odrzucony i dlaczego. (Opcjonalnie) |
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
| senderPartnerName | String | X12 komunikatu nazwę partnera nadawcy. (Opcjonalnie) |
| receiverPartnerName | String | X12 komunikatu nazwę partnera odbierającego firmy. (Opcjonalnie) |
| senderQualifier | String | Wyślij kwalifikator partnera. (Obowiązkowe) |
| senderIdentifier | String | Wyślij identyfikator partnera. (Obowiązkowe) |
| receiverQualifier | String | Odbieranie kwalifikator partnera. (Obowiązkowe) |
| receiverIdentifier | String | Otrzymać identyfikator partnera. (Obowiązkowe) |
| agreementName | String | Nazwa X12 umowy, do której wiadomości są rozwiązane. (Opcjonalnie) |
| direction | Enum | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| interchangeControlNumber | String | Numer kontrolny wymiany. (Opcjonalnie) |
| isaSegment | String | Segment ISA wiadomości. (Opcjonalnie) |
| isTechnicalAcknowledgmentExpected | Boolean | Czy pomoc potwierdzenia jest skonfigurowana w X12 umowy. (Obowiązkowe) |
| isMessageFailed | Boolean | Czy X12 wiadomości nie powiodło się. (Obowiązkowe) |
| isa09 | String | X12 dokumentu wymiany daty. (Opcjonalnie) |
| isa10 | String | X12 dokumentu czasu wymiany. (Opcjonalnie) |
| isa11 | String | X12 wymiany kontroli identyfikator standardy. (Opcjonalnie) |
| isa12 | String | X12 wymiany kontroli numer wersji. (Opcjonalnie) |
| isa14 | String | X12 zażądano potwierdzenia. (Opcjonalnie) |
| isa15 | String | Wskaźnik do testów lub produkcji. (Opcjonalnie) |
| isa16 | String | Separator elementów. (Opcjonalnie) |
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
| senderPartnerName | String | X12 komunikatu nazwę partnera nadawcy. (Opcjonalnie) |
| receiverPartnerName | String | X12 komunikatu nazwę partnera odbierającego firmy. (Opcjonalnie) |
| senderQualifier | String | Wyślij kwalifikator partnera. (Obowiązkowe) |
| senderIdentifier | String | Wyślij identyfikator partnera. (Obowiązkowe) |
| receiverQualifier | String | Odbieranie kwalifikator partnera. (Obowiązkowe) |
| receiverIdentifier | String | Otrzymać identyfikator partnera. (Obowiązkowe) |
| agreementName | String | Nazwa X12 umowy, do której wiadomości są rozwiązane. (Opcjonalnie) |
| direction | Enum | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| interchangeControlNumber | String | Numer kontrolny potwierdzenia technicznych, odebrane od partnerów wymiany. (Opcjonalnie) |
| isaSegment | String | Segment ISA na potwierdzenie technicznych, odebrane od partnerów. (Opcjonalnie) |
| respondingInterchangeControlNumber |String | Numer kontrolny potwierdzenia technicznych, odebrane od partnerów wymiany. (Opcjonalnie) |
| isMessageFailed | Boolean | Czy X12 wiadomości nie powiodło się. (Obowiązkowe) |
| statusCode | Enum | Wymiany kod stanu potwierdzenia. Dozwolone wartości to **Accepted**, **Rejected**, i **AcceptedWithErrors**. (Obowiązkowe) |
| processingStatus | Enum | Stan potwierdzenia. Dozwolone wartości to **odebrane**, **Generated**, i **wysłane**. (Obowiązkowe) |
| ta102 | String | Wymiany daty. (Opcjonalnie) |
| ta103 | String | Wymiany czasu. (Opcjonalnie) |
| ta105 | String | Wymiany Uwaga kodu. (Opcjonalnie) |
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
| senderPartnerName | String | X12 komunikatu nazwę partnera nadawcy. (Opcjonalnie) |
| receiverPartnerName | String | X12 komunikatu nazwę partnera odbierającego firmy. (Opcjonalnie) |
| senderQualifier | String | Wyślij kwalifikator partnera. (Obowiązkowe) |
| senderIdentifier | String | Wyślij identyfikator partnera. (Obowiązkowe) |
| receiverQualifier | String | Odbieranie kwalifikator partnera. (Obowiązkowe) |
| receiverIdentifier | String | Otrzymać identyfikator partnera. (Obowiązkowe) |
| agreementName | String | Nazwa X12 umowy, do której wiadomości są rozwiązane. (Opcjonalnie) |
| direction | Enum | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| interchangeControlNumber | String | Numer kontrolny wymiany. (Opcjonalnie) |
| functionalGroupControlNumber | String | Numer kontrolny funkcjonalności. (Opcjonalnie) |
| gsSegment | String | Segment komunikat GS. (Opcjonalnie) |
| isTechnicalAcknowledgmentExpected | Boolean | Czy pomoc potwierdzenia jest skonfigurowana w X12 umowy. (Obowiązkowe) |
| isFunctionalAcknowledgmentExpected | Boolean | Czy funkcjonalności potwierdzenia jest skonfigurowany w X12 umowy. (Obowiązkowe) |
| isMessageFailed | Boolean | Czy X12 wiadomości nie powiodło się. (Obowiązkowe)|
| gs01 | String | Identyfikator funkcjonalny kod. (Opcjonalnie) |
| gs02 | String | Kod aplikacji nadawcy. (Opcjonalnie) |
| gs03 | String | Kod aplikacji odbiornika. (Opcjonalnie) |
| gs04 | String | Data grupy funkcjonalnej. (Opcjonalnie) |
| gs05 | String | Czas grupy funkcjonalnej. (Opcjonalnie) |
| gs07 | String | Kod odpowiada Agencji. (Opcjonalnie) |
| gs08 | String | Identyfikator wersji/wydania/branży kod. (Opcjonalnie) |
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
| senderPartnerName | String | X12 komunikatu nazwę partnera nadawcy. (Opcjonalnie) |
| receiverPartnerName | String | X12 komunikatu nazwę partnera odbierającego firmy. (Opcjonalnie) |
| senderQualifier | String | Wyślij kwalifikator partnera. (Obowiązkowe) |
| senderIdentifier | String | Wyślij identyfikator partnera. (Obowiązkowe) |
| receiverQualifier | String | Odbieranie kwalifikator partnera. (Obowiązkowe) |
| receiverIdentifier | String | Otrzymać identyfikator partnera. (Obowiązkowe) |
| agreementName | String | Nazwa X12 umowy, do której wiadomości są rozwiązane. (Opcjonalnie) |
| direction | Enum | Kierunek przepływu wiadomości odbierania lub wysyłania. (Obowiązkowe) |
| interchangeControlNumber | String | Numer kontrolny wymiany, które wypełnia po stronie wysyłania po otrzymaniu potwierdzenia pomoc od partnerów. (Opcjonalnie) |
| functionalGroupControlNumber | String | Numer kontrolny grupy funkcjonalnej potwierdzenia technicznych, które wypełnia po stronie wysyłania po otrzymaniu potwierdzenia pomoc od partnerów. (Opcjonalnie) |
| isaSegment | String | Takie same jak wymiany kontrolować liczbę, ale wypełnione tylko w szczególnych przypadkach. (Opcjonalnie) |
| gsSegment | String | Takie same jak grupa funkcjonalna kontrolować liczbę, ale wypełnione tylko w szczególnych przypadkach. (Opcjonalnie) |
| respondingfunctionalGroupControlNumber | String | Numer kontrolny grupy oryginalnego funkcjonalnej. (Opcjonalnie) |
| respondingFunctionalGroupId | String | Mapuje do AK101 w grupie funkcjonalnej potwierdzania identyfikatora. (Opcjonalnie) |
| isMessageFailed | Boolean | Czy X12 wiadomości nie powiodło się. (Obowiązkowe) |
| statusCode | Enum | Kod stanu potwierdzenia. Dozwolone wartości to **Accepted**, **Rejected**, i **AcceptedWithErrors**. (Obowiązkowe) |
| processingStatus | Enum | Stan przetwarzania potwierdzenia. Dozwolone wartości to **odebrane**, **Generated**, i **wysłane**. (Obowiązkowe) |
| ak903 | String | Liczba zestawów transakcji odebrane. (Opcjonalnie) |
| ak904 | String | Akceptowane liczby zestawów transakcji w grupie funkcjonalnej zidentyfikowane. (Opcjonalnie) |
| ak9Segment | String | Czy grupy funkcjonalnej identyfikowane w segmencie AK1 jest zaakceptowane lub odrzucone i dlaczego. (Opcjonalnie) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołu B2B

Aby uzyskać informacji na temat protokołu B2B schematy śledzenia zobacz:

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Niestandardowe schematy śledzenia B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [monitorowanie komunikatów B2B](logic-apps-monitor-b2b-message.md).
* Dowiedz się więcej o [śledzenie komunikatów B2B w usłudze Azure Monitor dziennikach](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
