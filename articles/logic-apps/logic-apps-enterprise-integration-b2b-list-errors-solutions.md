---
title: Rozwiązania typowych błędów i problemów w scenariuszach B2B
description: Znajdowanie rozwiązań typowych błędów i problemów podczas rozwiązywania scenariuszy B2B w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 38e281ce3d8117bff719b1bb572f09acbbb89669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666690"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Błędy i rozwiązania B2B dla usługi Azure Logic Apps

Ten artykuł ułatwia rozwiązywanie problemów z błędami, które mogą wystąpić w scenariuszach B2B aplikacji logiki i sugeruje odpowiednie akcje w celu skorygowania tych błędów.

## <a name="agreement-resolution"></a>Rozwiązanie umowy

### <a name="no-agreement-found"></a>Nie znaleziono porozumienia 

|   |   |  
|---|---|
| Opis błędu | Nie znaleziono porozumienia z parametrami rozwiązania umowy. | 
| Akcja użytkownika | Umowa powinna zostać dodana do konta integracyjnego z uzgodnionymi tożsamościami biznesowymi. </br>Tożsamości biznesowe powinny być zgodne z identyfikatorami komunikatów wejściowych. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Nie znaleziono porozumienia z tożsamościami

|   |   | 
|---|---|
| Opis błędu | Nie znaleziono umowy z tożsamościami: 'AS2Identity'::'Partner1' i'AS2Identity':'Partner3' | 
| Akcja użytkownika | Nieprawidłowe as2-od lub AS2-Do skonfigurowane do umowy. </br>Popraw nagłówki "AS2-From" lub umowy, aby dopasować identyfikatory AS2 w nagłówkach komunikatów AS2 z konfiguracjami umowy. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Brak nagłówków komunikatów AS2  

|   |   |  
|---|---|
| Opis błędu | Nieprawidłowe nagłówki AS2. Jeden z nagłówków "AS2-To" lub "AS2-Od" jest pusty. | 
| Akcja użytkownika | Odebrano komunikat AS2, który nie zawierał nagłówków AS2-Od lub AS2-Do lub obu. </br> Sprawdź as2 wiadomości AS2-Od i AS2-Do nagłówki i poprawić je na podstawie konfiguracji umowy. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Brak treści wiadomości AS2 i nagłówków    

|   |   |  
|---|---|
| Opis błędu | Zawartość żądania jest null lub pusta. | 
| Akcja użytkownika | Odebrano komunikat AS2, który nie zawierał treści wiadomości. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>Błąd odszyfrowywania komunikatu AS2

|   |   | 
|---|---|
| Opis błędu |  [processed/Error: deszyfrowanie nie powiodło się] | 
| Akcja użytkownika | Dodaj @base64ToBinary do AS2Message przed wysłaniem do partnera. |
|||

Przykład:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>Błąd odszyfrowywania mdn

|   |   | 
|---|---|
| Opis błędu |  [processed/Error: deszyfrowanie nie powiodło się] | 
| Akcja użytkownika | Dodaj @base64ToBinary do MDN przed wysłaniem do partnera. | 
|||

Przykład:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Brak certyfikatu podpisywania

|   |   |  
|---|---|
| Opis błędu| Certyfikat podpisywania nie został skonfigurowany dla strony AS2. </br>AS2-Od: partner1 AS2-To: partner2 | 
| Akcja użytkownika | Skonfiguruj ustawienia umowy AS2 z poprawnym certyfikatem do podpisu. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 i EDIFACT

### <a name="leading-or-trailing-space-found"></a>Znaleziono przestrzeń wiodącą lub końcową    
    
|   |   | 
|---|---|
| Opis błędu | Wystąpił błąd podczas analizowania. Zestaw transakcji EDIFACT o identyfikatorze "123456" zawarty w wymianie (bez grupy) o identyfikatorze "987654", z identyfikatorem nadawcy "Partner1", identyfikatorem odbiorcy "Partner2" jest zawieszony z następującymi błędami: <p>"Znaleziono wiodący separator spływowy" |
| Akcja użytkownika | Ustawienia umowy, które mają być skonfigurowane w celu umożliwienia spacji i spływu. </br>Edytuj ustawienia umowy, aby umożliwić odstępy między wiodącymi i końcowymi. |
|   |   |

![zezwalaj na miejsce](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Kontrola duplikatów została włączona w umowie

|   |   | 
|---|---| 
| Opis błędu | Zduplikowany numer kontrolny |
| Akcja użytkownika | Ten błąd wskazuje, że odebrana wiadomość ma zduplikowane numery kontrolne. </br>Popraw numer kontrolny i wyślij ponownie wiadomość. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Brak schematu w umowie

|   |   | 
|---|---| 
| Opis błędu | Wystąpił błąd podczas analizowania. Zestaw transakcji X12 o identyfikatorze "564220001" zawarty w grupie funkcjonalnej o identyfikatorze "56422", w zamian za identyfikator "000056422", z identyfikatorem nadawcy "12345678", identyfikator odbiornika "87654321" jest zawieszony z następującymi błędami: <p>"Wiadomość ma nieznany typ dokumentu i nie została rozwiązana z żadnym z istniejących schematów skonfigurowanych w umowie" |
| Akcja użytkownika | Skonfiguruj schemat w ustawieniach umowy.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Niepoprawny schemat w umowie

|   |   | 
|---|---| 
| Opis błędu | Komunikat ma nieznany typ dokumentu i nie został rozpoznany dla żadnego z istniejących schematów skonfigurowanych w umowie. |
| Akcja użytkownika | Skonfiguruj poprawny schemat w ustawieniach umowy. |
|   |   |

## <a name="flat-file"></a>Plik prosty

### <a name="input-message-with-no-body"></a>Komunikat wejściowy bez treści

|   |   | 
|---|---|
| Opis błędu | InvalidTemplate. Nie można przetwarzać wyrażeń języka szablonu w działaniu "Flat_File_Decoding" danych wejściowych w wierszu "1" i kolumnie "1902": "Wymagana właściwość 'content' oczekuje wartości, ale ma wartość null. Ścieżka ''.'. |
| Akcja użytkownika | Ten błąd wskazuje, że komunikat wejściowy nie zawiera treści. |
|   |   | 

