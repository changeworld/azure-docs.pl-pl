---
title: Błędy i rozwiązania dla scenariuszy B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Znajdowanie błędów i rozwiązania dla scenariuszy B2B w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: f0591b47ce7ba6837f300088c856c0098fb66710
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60998830"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Błędy B2B i rozwiązania dla usługi Azure Logic Apps

Ten artykuł ułatwia rozwiązywanie problemów z błędami, które może się zdarzyć w przypadku scenariuszy B2B aplikacji logiki, a także sugeruje odpowiednie akcje dotyczące poprawiania tych błędów.

## <a name="agreement-resolution"></a>Rozpoznanie umowy

### <a name="no-agreement-found"></a>Nie znaleziono umowy 

|   |   |  
|---|---|
| Opis błędu | Nie znaleziono umowy o parametry rozpoznawania umowy. | 
| Akcja użytkownika | Umowy należy dodać do konta integracji z tożsamościami ustalonym biznesowej. </br>Tożsamości biznesowe powinien być zgodny komunikat wejściowy identyfikatorów. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Nie znaleziono umów z tożsamościami

|   |   | 
|---|---|
| Opis błędu | Nie znaleziono umów z tożsamościami: 'AS2Identity':: "Partner1" i 'AS2Identity':: "Partner3" | 
| Akcja użytkownika | Nieprawidłowy AS2-z lub AS2 — do skonfigurowanego dla umowy. </br>Popraw komunikat AS2 "AS2-od" lub "AS2-do" nagłówków lub umowy odpowiadają identyfikatorom AS2 w nagłówkach komunikatu AS2 z konfiguracjami umowy. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Brak nagłówków komunikatów AS2  

|   |   |  
|---|---|
| Opis błędu | Nieprawidłowy nagłówek AS2. Jedną z "AS2-do" lub "AS2-od" nagłówki jest pusty. | 
| Akcja użytkownika | Odebrano komunikat AS2, który nie zawiera AS2-z lub AS2 — aby lub obu nagłówków. </br> W komunikacie AS2 AS2-z i AS2-nagłówków i prawidłowe je na podstawie umowy konfiguracji. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Brak treści komunikatu AS2 i nagłówki    

|   |   |  
|---|---|
| Opis błędu | Zawartość żądania ma wartość null lub jest pusty. | 
| Akcja użytkownika | Odebrano komunikat AS2, który nie zawiera treści wiadomości. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>Błąd odszyfrowywania komunikat AS2

|   |   | 
|---|---|
| Opis błędu |  [przetworzonych/błąd: odszyfrowanie nie powiodło się] | 
| Akcja użytkownika | Dodaj @base64ToBinary do AS2Message przed wysłaniem do partnera. |
|||

Na przykład:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>Błąd odszyfrowywania powiadomienia MDN

|   |   | 
|---|---|
| Opis błędu |  [przetworzonych/błąd: odszyfrowanie nie powiodło się] | 
| Akcja użytkownika | Dodaj @base64ToBinary do powiadomienia MDN przed wysłaniem do partnera. | 
|||

Na przykład:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Brakuje certyfikatu podpisywania

|   |   |  
|---|---|
| Opis błędu| Certyfikat podpisywania nie został skonfigurowany dla strony AS2. </br>AS2 — od: partner1 AS2 — do: partner2 | 
| Akcja użytkownika | Konfigurowanie ustawień umowy AS2, za pomocą poprawny certyfikat dla podpisu. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 i EDIFACT

### <a name="leading-or-trailing-space-found"></a>Wiodącą lub końcową spację, znaleziono    
    
|   |   | 
|---|---|
| Opis błędu | Napotkano błąd podczas analizowania. Zestawu transakcji EDIFACT z Identyfikatorem "123456"znajdujący się w wymianie (bez grupy) o identyfikatorze "987654", identyfikatorem nadawcy "Partner1", Identyfikatorem odbiorcy "Partner2" Wstrzymano z następującymi błędami: <p>"Wiodący separator końcowych, znaleziono" |
| Akcja użytkownika | Umowa ustawienia można skonfigurować, aby umożliwić początkowe i końcowe miejsca. </br>Edytowanie ustawień umowy, aby zapewnić miejsce początkowe i końcowe. |
|   |   |

![Zezwalaj na miejsce](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Sprawdzanie duplikatów została włączona w układzie

|   |   | 
|---|---| 
| Opis błędu | Zduplikowany numer kontrolny |
| Akcja użytkownika | Ten błąd wskazuje, że odebranego komunikatu ma numerów kontrolnych duplikatów. </br>Popraw numeru kontrolnego i ponownie wysłać wiadomość. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Brak schematu w układzie

|   |   | 
|---|---| 
| Opis błędu | Napotkano błąd podczas analizowania. X12 zestawu transakcji z Identyfikatorem "564220001' zawarty w grupie funkcjonalnej o identyfikatorze"56422', w wymianie z Identyfikatorem "000056422", identyfikatorem nadawcy "12345678", Identyfikatorem odbiorcy "87654321" Wstrzymano z następującymi błędami: <p>"Wiadomość ma nieznany typ dokumentu i nie został rozpoznany do dowolnego z istniejących schematów skonfigurowane w umowie" |
| Akcja użytkownika | Skonfiguruj schematu w ustawieniach umowy.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Nieprawidłowy schemat w układzie

|   |   | 
|---|---| 
| Opis błędu | Komunikat ma nieznany typ dokumentu i nie został rozpoznany do dowolnego z istniejących schematów skonfigurowane w umowie. |
| Akcja użytkownika | Skonfiguruj poprawnego schematu w ustawieniach umowy. |
|   |   |

## <a name="flat-file"></a>Plik prosty

### <a name="input-message-with-no-body"></a>Komunikat wejściowy o bez treści

|   |   | 
|---|---|
| Opis błędu | InvalidTemplate. Nie można przetworzyć wyrażeń języka szablonu w akcji "Flat_File_Decoding" danych wejściowych w wierszu "1" i kolumnie "1902": ' Wymagana właściwość "content" oczekuje, że wartość, a uzyskano o wartości null. Ścieżka ".". |
| Akcja użytkownika | Ten błąd wskazuje, że komunikat wejściowy nie zawiera treści. |
|   |   | 

