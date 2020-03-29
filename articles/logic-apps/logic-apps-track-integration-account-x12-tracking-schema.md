---
title: Schematy śledzenia X12 dla wiadomości B2B
description: Tworzenie schematów śledzenia w celu monitorowania wiadomości X12 dla aplikacji Logika Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905308"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Tworzenie schematów do śledzenia wiadomości X12 w usłudze Azure Logic Apps

Aby ułatwić monitorowanie sukcesu, błędów i właściwości wiadomości dla transakcji typu "business-to-business" (B2B), można użyć tych schematów śledzenia X12 na koncie integracji:

* Schemat śledzenia zestawu transakcji X12
* Schemat śledzenia zestawu potwierdzeń transakcji X12
* Schemat śledzenia wymiany X12
* Schemat śledzenia potwierdzenia wymiany X12
* Schemat śledzenia grupy funkcjonalnej X12
* Schemat śledzenia potwierdzenia grupy funkcjowej X12

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
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| nazwa partnera nadawcy | Nie | Ciąg | Nazwa partnera nadawcy wiadomości X12 |
| nazwa odbiornikaPartnerName | Nie | Ciąg | Nazwa partnera odbiornika wiadomości X12 |
| senderQualifier | Tak | Ciąg | Wyślij kwalifikator partnera |
| identyfikator nadawcy | Tak | Ciąg | Wysyłanie identyfikatora partnera |
| odbiornikQualifier | Tak | Ciąg | Otrzymaj kwalifikator partnerski |
| odbiornikIdentyfikator | Tak | Ciąg | Odbierz identyfikator partnera |
| nazwa umowy | Nie | Ciąg | Nazwa umowy X12, do której wiadomości są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który jest `receive``send` |
| interchangeControlNumber | Nie | Ciąg | Numer kontroli wymiany |
| functionalGroupControlNumber | Nie | Ciąg | Funkcjonalny numer sterowania |
| transactionSetControlNumber | Nie | Ciąg | Numer kontroli zestawu transakcji |
| KorelacjaMessageId | Nie | Ciąg | Identyfikator komunikatu korelacji, który jest kombinacją {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| Messagetype | Nie | Ciąg | Zestaw transakcji lub typ dokumentu |
| isMessageFailed | Tak | Wartość logiczna | Określa, czy komunikat X12 nie powiódł się |
| isTechnicalAcknowledgmentWyczekane | Tak | Wartość logiczna | Czy potwierdzenie techniczne jest skonfigurowane w umowie X12 |
| isFunctionalAcknowledgmentWyczekane | Tak | Wartość logiczna | Czy potwierdzenie funkcjonalności jest skonfigurowane w umowie X12 |
| needAk2LoopForValidMessages | Tak | Wartość logiczna | Czy pętla AK2 jest wymagana dla prawidłowej wiadomości |
| segmentyLiczać | Nie | Liczba całkowita | Liczba segmentów w zestawie transakcji X12 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>Schemat śledzenia zestawu potwierdzeń transakcji X12

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

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| nazwa partnera nadawcy | Nie | Ciąg | Nazwa partnera nadawcy wiadomości X12 |
| nazwa odbiornikaPartnerName | Nie | Ciąg | Nazwa partnera odbiornika wiadomości X12 |
| senderQualifier | Tak | Ciąg | Wyślij kwalifikator partnera |
| identyfikator nadawcy | Tak | Ciąg | Wysyłanie identyfikatora partnera |
| odbiornikQualifier | Tak | Ciąg | Otrzymaj kwalifikator partnerski |
| odbiornikIdentyfikator | Tak | Ciąg | Odbierz identyfikator partnera |
| nazwa umowy | Nie | Ciąg | Nazwa umowy X12, do której wiadomości są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który jest `receive``send` |
| interchangeControlNumber | Nie | Ciąg | Numer kontroli wymiany potwierdzenia funkcjonalnego. Wartość wypełnia tylko dla strony wysyłania, gdzie funkcjonalne potwierdzenie jest odbierane dla wiadomości wysyłanych do partnera. |
| functionalGroupControlNumber | Nie | Ciąg | Numer kontroli grupy funkcjonalnej potwierdzenia funkcjonalnego. Wartość wypełnia się tylko dla strony wysyłania, po której odbierane jest potwierdzenie funkcjonalne dla wiadomości wysyłanych do partnera |
| isaSegment | Nie | Ciąg | segment ISA wiadomości. Wartość wypełnia się tylko dla strony wysyłania, po której odbierane jest potwierdzenie funkcjonalne dla wiadomości wysyłanych do partnera |
| gsSegment | Nie | Ciąg | segment GS wiadomości. Wartość wypełnia się tylko dla strony wysyłania, po której odbierane jest potwierdzenie funkcjonalne dla wiadomości wysyłanych do partnera |
| respondingfunctionalGroupControlNumber | Nie | Ciąg | Odpowiadający numer kontroli wymiany |
| respondingFunctionalGroupId | Nie | Ciąg | Odpowiadający identyfikator grupy funkcjonalnej, który mapuje do AK101 w potwierdzeniu |
| respondingtransactionSetControlNumber | Nie | Ciąg | Odpowiadający numer kontroli zestawu transakcji |
| respondingTransactionSetId | Nie | Ciąg | Identyfikator transakcji odpowiadającej, który mapuje wartość AK201 w |
| Statuscode | Tak | Wartość logiczna | Kod stanu potwierdzenia zestawu transakcji |
| segmentyLiczać | Tak | Wyliczenie | Kod stanu potwierdzenia z tymi `Accepted`dozwolonymi wartościami: , `Rejected`i`AcceptedWithErrors` |
| przetwarzanieStatus | Tak | Wyliczenie | Status przetwarzania potwierdzenia z tymi dozwolonymi `Received` `Generated`wartościami: , i`Sent` |
| KorelacjaMessageId | Nie | Ciąg | Identyfikator komunikatu korelacji, który jest kombinacją {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| isMessageFailed | Tak | Wartość logiczna | Określa, czy komunikat X12 nie powiódł się |
| ak2Segment | Nie | Ciąg | Potwierdzenie transakcji ustawionej w otrzymanej grupie funkcjonalnej |
| ak3Segment | Nie | Ciąg | Zgłasza błędy w segmencie danych |
| ak5Segment | Nie | Ciąg | Informuje, czy zestaw transakcji zidentyfikowany w segmencie AK2 jest akceptowany lub odrzucany oraz dlaczego |
|||||

## <a name="x12-interchange-tracking-schema"></a>Schemat śledzenia wymiany X12

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

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| nazwa partnera nadawcy | Nie | Ciąg | Nazwa partnera nadawcy wiadomości X12 |
| nazwa odbiornikaPartnerName | Nie | Ciąg | Nazwa partnera odbiornika wiadomości X12 |
| senderQualifier | Tak | Ciąg | Wyślij kwalifikator partnera |
| identyfikator nadawcy | Tak | Ciąg | Wysyłanie identyfikatora partnera |
| odbiornikQualifier | Tak | Ciąg | Otrzymaj kwalifikator partnerski |
| odbiornikIdentyfikator | Tak | Ciąg | Odbierz identyfikator partnera |
| nazwa umowy | Nie | Ciąg | Nazwa umowy X12, do której wiadomości są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który jest `receive``send` |
| interchangeControlNumber | Nie | Ciąg | Numer kontroli wymiany |
| isaSegment | Nie | Ciąg | Komunikat ISA segment |
| isTechnicalAcknowledgmentWyczekane | Wartość logiczna | Czy potwierdzenie techniczne jest skonfigurowane w umowie X12  |
| isMessageFailed | Tak | Wartość logiczna | Określa, czy komunikat X12 nie powiódł się |
| isa09 | Nie | Ciąg | X12 data wymiany dokumentów |
| isa10 | Nie | Ciąg | Czas wymiany dokumentów X12 |
| isa11 | Nie | Ciąg | Identyfikator standardów kontroli wymiany X12 |
| isa12 | Nie | Ciąg | Numer wersji kontroli wymiany X12 |
| isa14 | Nie | Ciąg | Wymagane jest potwierdzenie X12 |
| isa15 | Nie | Ciąg | Wskaźnik badania lub produkcji |
| isa16 | Nie | Ciąg | Separator elementów |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>Schemat śledzenia potwierdzenia wymiany X12

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

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| nazwa partnera nadawcy | Nie | Ciąg | Nazwa partnera nadawcy wiadomości X12 |
| nazwa odbiornikaPartnerName | Nie | Ciąg | Nazwa partnera odbiornika wiadomości X12 |
| senderQualifier | Tak | Ciąg | Wyślij kwalifikator partnera |
| identyfikator nadawcy | Tak | Ciąg | Wysyłanie identyfikatora partnera |
| odbiornikQualifier | Tak | Ciąg | Otrzymaj kwalifikator partnerski |
| odbiornikIdentyfikator | Tak | Ciąg | Odbierz identyfikator partnera |
| nazwa umowy | Nie | Ciąg | Nazwa umowy X12, do której wiadomości są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który jest `receive``send` |
| interchangeControlNumber | Nie | Ciąg | Numer kontroli wymiany potwierdzenia technicznego otrzymanego od partnerów |
| isaSegment | Nie | Ciąg | segment ISA dla potwierdzenia technicznego otrzymanego od partnerów |
| odpowiadającInterchangeControlNumber | Nie | Ciąg | Numer kontroli wymiany dla potwierdzenia technicznego otrzymanego od partnerów |
| isMessageFailed | Tak | Wartość logiczna | Określa, czy komunikat X12 nie powiódł się |
| Statuscode | Tak | Wyliczenie | Kod stanu potwierdzenia wymiany z tymi `Accepted` `Rejected`dozwolonymi wartościami: , i`AcceptedWithErrors` |
| przetwarzanieStatus | Tak | Wyliczenie | Status potwierdzenia z tymi dozwolonymi wartościami: `Received`, `Generated`i`Sent` |
| ta102 ( ta102 ) | Nie | Ciąg | Data wymiany |
| ta103 ( ta103 ) | Nie | Ciąg | Czas wymiany |
| ta105 ( ta105 ) | Nie | Ciąg | Kod noty wymiany |
|||||

## <a name="x12-functional-group-tracking-schema"></a>Schemat śledzenia grupy funkcjonalnej X12

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

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| nazwa partnera nadawcy | Nie | Ciąg | Nazwa partnera nadawcy wiadomości X12 |
| nazwa odbiornikaPartnerName | Nie | Ciąg | Nazwa partnera odbiornika wiadomości X12 |
| senderQualifier | Tak | Ciąg | Wyślij kwalifikator partnera |
| identyfikator nadawcy | Tak | Ciąg | Wysyłanie identyfikatora partnera |
| odbiornikQualifier | Tak | Ciąg | Otrzymaj kwalifikator partnerski |
| odbiornikIdentyfikator | Tak | Ciąg | Odbierz identyfikator partnera |
| nazwa umowy | Nie | Ciąg | Nazwa umowy X12, do której wiadomości są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, odbieranie lub wysyłanie |
| interchangeControlNumber | Nie | Ciąg | Numer kontroli wymiany |
| functionalGroupControlNumber | Nie | Ciąg | Funkcjonalny numer sterowania |
| gsSegment | Nie | Ciąg | Wiadomość segment GS |
| isTechnicalAcknowledgmentWyczekane | Tak | Wartość logiczna | Czy potwierdzenie techniczne jest skonfigurowane w umowie X12 |
| isFunctionalAcknowledgmentWyczekane | Tak | Wartość logiczna | Czy potwierdzenie funkcjonalności jest skonfigurowane w umowie X12 |
| isMessageFailed | Tak | Wartość logiczna | Określa, czy komunikat X12 nie powiódł się |
| gs01 | Nie | Ciąg | Kod identyfikatora funkcjonalnego |
| gs02 | Nie | Ciąg | Kod nadawcy aplikacji |
| gs03 | Nie | Ciąg | Kod odbiornika aplikacji |
| gs04 | Nie | Ciąg | Data grupy funkcjonalnej |
| gs05 | Nie | Ciąg | Czas grupy funkcjonalnej |
| gs07 | Nie | Ciąg | Kodeks odpowiedzialnej agencji |
| gs08 | Nie | Ciąg | Kod identyfikatora dla wersji, wydania lub branży |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>Schemat śledzenia potwierdzenia grupy funkcjowej X12

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

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| nazwa partnera nadawcy | Nie | Ciąg | Nazwa partnera nadawcy wiadomości X12 |
| nazwa odbiornikaPartnerName | Nie | Ciąg | Nazwa partnera odbiornika wiadomości X12 |
| senderQualifier | Tak | Ciąg | Wyślij kwalifikator partnera |
| identyfikator nadawcy | Tak | Ciąg | Wysyłanie identyfikatora partnera |
| odbiornikQualifier | Tak | Ciąg | Otrzymaj kwalifikator partnerski |
| odbiornikIdentyfikator | Tak | Ciąg | Odbierz identyfikator partnera |
| nazwa umowy | Nie | Ciąg | Nazwa umowy X12, do której wiadomości są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który jest `receive``send` |
| interchangeControlNumber | Nie | Ciąg | Numer kontroli wymiany, który wypełnia się po stronie wysyłania po otrzymaniu potwierdzenia technicznego od partnerów |
| functionalGroupControlNumber | Nie | Ciąg | Numer kontroli grupy funkcjonalnej potwierdzenia technicznego, który wypełnia się po stronie wysyłania po otrzymaniu potwierdzenia technicznego od partnerów |
| isaSegment | Nie | Ciąg | Tak samo jak numer kontroli wymiany, ale wypełniany tylko w określonych przypadkach |
| gsSegment | Nie | Ciąg | Taki sam jak numer kontroli grupy funkcjonalnej, ale wypełniany tylko w określonych przypadkach |
| respondingfunctionalGroupControlNumber | Nie | Ciąg | Numer kontrolny oryginalnej grupy funkcjonalnej |
| respondingFunctionalGroupId | Nie | Ciąg | Mapowanie do AK101 w identyfikatorze grupy funkcjonalnej potwierdzenia |
| isMessageFailed | Wartość logiczna | Określa, czy komunikat X12 nie powiódł się |
| Statuscode | Tak | Wyliczenie | Kod stanu potwierdzenia z tymi `Accepted`dozwolonymi wartościami: , `Rejected`i`AcceptedWithErrors` |
| przetwarzanieStatus | Tak | Wyliczenie | Status przetwarzania potwierdzenia z tymi dozwolonymi `Received` `Generated`wartościami: , i`Sent` |
| ak903 | Nie | Ciąg | Liczba otrzymanych zestawów transakcji |
| ak904 | Nie | Ciąg | Liczba zestawów transakcji zaakceptowanych w zidentyfikowanej grupie funkcjonalnej |
| ak9Segment | Nie | Ciąg | Czy grupa funkcjonalna zidentyfikowana w segmencie AK1 jest akceptowana czy odrzucana, |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołów B2B

Aby uzyskać informacje na temat schematów śledzenia protokołów B2B, zobacz:

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schematy śledzenia niestandardowego B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie komunikatów B2B przy użyciu dzienników usługi Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)