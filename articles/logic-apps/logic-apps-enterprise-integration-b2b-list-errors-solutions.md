---
title: 'Logiki aplikacji B2B listę błędów i rozwiązania: usługi Azure App Service | Dokumentacja firmy Microsoft'
description: Logiki aplikacji B2B listę błędów i rozwiązania
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 8503792d57dd7ed64434d5a306afce850ced77db
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298342"
---
# <a name="logic-apps-b2b-list-of-errors-and-solutions"></a>Logiki aplikacji B2B listę błędów i rozwiązania  
Ten artykuł pomaga rozwiązywania problemów, które może się tak dziać w scenariusze B2B aplikacji logiki, a także sugeruje odpowiednie działania w przypadku naprawiania te błędy.


## <a name="agreement-resolution"></a>Rozdzielczość umowy

### <a name="no-agreement-found"></a>* Nie znaleziono umowę. 

|   |   |  
|---|---|
| Opis błędu | Nie znaleziono z parametrami rozpoznawania umowy umowę.|    
| Akcja użytkownika | Umowy powinno być dodane do konta integracji z tożsamości firm uzgodnione.</br> Tożsamości firm powinien być zgodny z identyfikatorami komunikatu wejściowego|  
|   |   |

### <a name="-no-agreement-found-with-identities"></a>* Nie znaleziono z tożsamościami umowę.

|   |   | 
|---|---|
| Opis błędu | Nie znaleziono z tożsamościami umowę: "AS2Identity":: "Partner1" i "AS2Identity":: "Partner3"| 
| Akcja użytkownika | Nieprawidłowy AS2-z lub AS2 — do skonfigurowanych dla umowy. </br> Komunikatów AS2 AS2-z lub AS2-nagłówków lub umowy do dopasowania identyfikatorów AS2 w AS2 komunikatu nagłówków z konfiguracjami umowy |
|   |   |     

## <a name="as2"></a>AS2

### <a name="-missing-as2-message-headers"></a>* Brak AS2 nagłówki komunikatów  

|   |   |  
|---|---|
| Opis błędu| Nieprawidłowy nagłówek AS2. Jedną z "AS2 — do" lub "AS2 — od" nagłówki są puste| 
| Akcja użytkownika | Odebrano komunikat AS2, która nie zawiera AS2-z lub AS2 — aby lub obu nagłówków. </br> Sprawdź komunikat AS2 AS2-z i AS2-nagłówków i prawidłowe je na podstawie umowy konfiguracji |
|  |  | 


### <a name="-missing-as2-message-body-and-headers"></a>* Brakuje treści komunikatu AS2 i nagłówków    

|   |   |  
|---|---|
| Opis błędu| Zawartość żądania ma wartość null lub pusty | 
| Akcja użytkownika | Odebrano komunikat AS2, która nie zawiera treść komunikatu |
|  |  | 

### <a name="-as2-message-decryption-failure"></a>* Błąd odszyfrowywania wiadomości AS2

|   |   | 
|---|---|
| Opis błędu |  [przetwarzane błąd: nie powiodło się odszyfrowywania] | 
| Akcja użytkownika | Dodaj @base64ToBinary do AS2Message przed wysłaniem do partnera 
```java
            "HTTP": {
                "inputs": {
                    "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
                    "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
                    "method": "POST",
                    "uri": "xxxxx.xxx"
                },
                
``` 

### <a name="-mdn-decryption-failure"></a>* Błąd odszyfrowywania MDN

|   |   | 
|---|---|
| Opis błędu |  [przetwarzane błąd: nie powiodło się odszyfrowywania] | 
| Akcja użytkownika | Dodaj @base64ToBinary do MDN przed wysłaniem do partnera 
```java
            "Response": {
                "inputs": {
                    "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
                    "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
                    "statusCode": 200
                },
                
``` 

### <a name="-missing-signing-certificate"></a>* Brakuje certyfikatu podpisywania

|   |   |  
|---|---|
| Opis błędu| Nie skonfigurowano certyfikatu podpisywania firmy AS2. </br> AS2 — z: partner1 AS2 — do: partner2 | 
| Akcja użytkownika | Skonfiguruj ustawienia umowy AS2 z prawidłowego certyfikatu do podpisu |
|  |  | 

## <a name="x12-and-edifact"></a>X12 i EDIFACT

### <a name="-leading-or-trailing-space-found"></a>* Początkowe lub końcowe miejsca znaleziono    
    
|   |   | 
|---|---|
| Opis błędu | Napotkano błąd podczas analizowania. Transakcja Edifact zestawu o identyfikatorze "123456"zawarte w interchange (bez grupy) o identyfikatorze "987654", identyfikator nadawcy "Partner1", identyfikator odbiorcy "Partner2" zostanie wstrzymane z następujących błędów: wiodące końcowe separatora, znaleziono |
| Akcja użytkownika | Ustawienia umowy, aby umożliwiać spacji wiodących i końcowych miejsca. </br> Edytowanie umowy ustawienia umożliwiające programowi miejsca wiodące i końcowe |
|   |   |

![Zezwalaj na miejsce](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="-duplicate-check-has-enabled-in-the-agreement"></a>* Zduplikowane wyboru włączył umowy

|   |   | 
|---|---| 
| Opis błędu | Zduplikowany numer kontrolny |
| Akcja użytkownika | Ten błąd wskazuje, że odebranego komunikatu ma numery zduplikowane kontroli. </br> Popraw liczbę kontroli i ponownie wysłać wiadomość |
|   |   |

### <a name="-missing-schema-in-the-agreement"></a>* Brak schematu umowy

|   |   | 
|---|---| 
| Opis błędu | Napotkano błąd podczas analizowania. X12 zestawu transakcji o identyfikatorze '564220001' zawarte w grupy funkcjonalnej o identyfikatorze "56422', w wymiany o identyfikatorze"000056422"o identyfikatorze nadawcy" 12345678", identyfikator odbiorcy" 87654321"zostanie wstrzymane z następujących błędów" wiadomość ma nieznany typ dokumentu ND nie zostało rozwiązane do żadnego z istniejących schematów skonfigurowanych w umowie" |
| Akcja użytkownika | Konfigurowanie schematu w ustawieniach umowy  |
|   |   |

### <a name="-incorrect-schema-in-the-agreement"></a>* Niepoprawny schemat umowy

|   |   | 
|---|---| 
| Opis błędu | Typ dokumentu komunikatu jest nieznany i nie został rozpoznany jako jeden z istniejących schematów skonfigurowanych w umowie. |
| Akcja użytkownika | Skonfiguruj poprawny schemat w ustawieniach umowy  |
|   |   |

## <a name="flat-file"></a>Plik prosty

### <a name="-input-message-with-no-body"></a>* Komunikat wejściowy o nie treści

|   |   | 
|---|---|
| Opis błędu | InvalidTemplate. Nie można wyrażeń języka szablonu procesu w danych wejściowych "Flat_File_Decoding" akcji w wierszu "1" i kolumnie "1902": "Wymagana właściwość"content"oczekuje, ale wartość otrzymano wartość null. Ścieżka ".". |
| Akcja użytkownika | Ten błąd oznacza, że komunikat wejściowy nie zawiera jednostkę |
|   |   | 

## <a name="learn-more"></a>Dowiedz się więcej
[Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md)