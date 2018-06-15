---
title: Odwołania do składni SQLRuleAction na platformie Azure | Dokumentacja firmy Microsoft
description: Szczegółowe informacje o SQLRuleAction gramatyki.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2018
ms.author: sethm
ms.openlocfilehash: 9ac9a2968adfdd8e1fb229ad744bc99914cdcd08
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29125823"
---
# <a name="sqlruleaction-syntax"></a>Składnia SQLRuleAction

A *SqlRuleAction* jest wystąpieniem [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) klasy i reprezentuje zestaw akcji napisane w języku SQL na podstawie składnię, która nie jest przeprowadzana [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Ten artykuł zawiera szczegółowe informacje o gramatyki akcji reguły SQL.  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 
  
## <a name="arguments"></a>Argumenty  
  
-   `<scope>`opcjonalny ciąg wskazujący zakres jest `<property_name>`. Prawidłowe wartości to `sys` lub `user`. `sys` Wartość wskazuje zakres systemu gdzie `<property_name>` jest nazwą właściwości publicznej [BrokeredMessage klasy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`Wskazuje zakres użytkowników gdzie `<property_name>` jest kluczem elementu [klasy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) słownika. `user`zakres jest zakres domyślny, jeśli `<scope>` nie jest określona.  
  
### <a name="remarks"></a>Uwagi  

Próba dostępu do właściwości systemu nieistniejącą jest wystąpił błąd podczas próby dostępu do właściwości nie istnieje użytkownik nie jest błąd. Zamiast tego właściwości użytkownika nieistniejącą wewnętrznie jest szacowana jako nieznaną wartość. Nieznana wartość jest traktowana specjalnie podczas obliczania operatora.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenty  
 `<regular_identifier>`ciąg jest reprezentowana przez następującym wyrażeniem regularnym:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Oznacza to dowolny ciąg, który rozpoczyna się od litery i następuje co najmniej jeden znak podkreślenia/list/cyfr.  
  
 `[:IsLetter:]`oznacza dowolnych znaków Unicode, który jest skategoryzowany jako list Unicode. `System.Char.IsLetter(c)`Zwraca `true` Jeśli `c` to litera Unicode.  
  
 `[:IsDigit:]`oznacza dowolnych znaków Unicode, który jest skategoryzowany jako dziesiętną wartością cyfrową. `System.Char.IsDigit(c)`Zwraca `true` Jeśli `c` jest cyfrą Unicode.  
  
 A `<regular_identifier>` nie może być zastrzeżonym słowem kluczowym.  
  
 `<delimited_identifier>`jest dowolny ciąg ujęty w lewo i prawo nawiasy kwadratowe ([]). Zamykający nawias kwadratowy jest reprezentowany jako dwa nawiasy kwadratowe prawo. Poniżej przedstawiono przykłady `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>`jest dowolny ciąg, który jest ujęta w znaki podwójnego cudzysłowu. Podwójny cudzysłów w identyfikatorze jest reprezentowany jako podwójnego cudzysłowu. Nie zaleca się użyć identyfikatory w cudzysłowach, ponieważ można łatwo pomylić z stałą typu string. Jeśli to możliwe za pomocą rozdzielanej identyfikatora. Poniżej przedstawiono przykład `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>wzorzec  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi
  
 `<pattern>`musi być wyrażeniem, której wartość jest szacowana jako ciąg. Jest używana jako wzorzec dla LIKE operator.      Może zawierać następujące znaki symboli wieloznacznych:  
  
-   `%`: Dowolny ciąg zawierający zero lub więcej znaków.  
  
-   `_`: Dowolny pojedynczy znak.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi
  
 `<escape_char>`musi być wyrażeniem, której wartość jest szacowana jako ciąg o długości 1. Jest używany jako znak ucieczki dla LIKE operator.  
  
 Na przykład `property LIKE 'ABC\%' ESCAPE '\'` odpowiada `ABC%` zamiast ciąg, który rozpoczyna się od `ABC`.  
  
## <a name="constant"></a>Stała  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenty  
  
-   `<integer_constant>`to ciąg liczb, które nie są ujęte w cudzysłów i nie zawierają miejsc dziesiętnych. Wartości są przechowywane jako `System.Int64` wewnętrznie i postępuj zgodnie z tego samego zakresu.  
  
     Poniżej przedstawiono przykłady długich stałe:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`to ciąg liczb, które nie są ujęte w cudzysłowy i zawierać separatorem dziesiętnym. Wartości są przechowywane jako `System.Double` wewnętrznie i postępuj zgodnie z tego samego zakresu/dokładności.  
  
     W przyszłych wersjach, ta liczba może być przechowywany w na inny typ danych do obsługi dokładne semantyki numer, nie należy polegać na fakcie odpowiadającego — typ danych jest `System.Double` dla `<decimal_constant>`.  
  
     Poniżej przedstawiono przykłady dziesiętną stałe:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`jest numer napisanych w notacji naukowej. Wartości są przechowywane jako `System.Double` wewnętrznie i postępuj zgodnie z tego samego zakresu/dokładności. Poniżej przedstawiono przykłady przybliżona number — stałe:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Uwagi
  
Stałe typu logicznego są reprezentowane przez słowa kluczowe `TRUE` lub `FALSE`. Wartości są przechowywane jako `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Uwagi
  
Stałe typu String są ujęte w apostrofy i zawierać żadnych prawidłowych znaków Unicode. Znak pojedynczego cudzysłowu osadzone w stałą typu string jest przedstawiona w postaci dwóch pojedynczy znaki cudzysłowu.  
  
## <a name="function"></a>funkcja  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Uwagi  

`newid()` Funkcja zwraca **System.Guid** generowane przez `System.Guid.NewGuid()` metody.  
  
`property(name)` Funkcja zwraca wartość właściwości odwołuje się `name`. `name` Wartość może być prawidłowym wyrażeniem, która zwraca wartość typu ciąg.  
  
## <a name="considerations"></a>Zagadnienia do rozważenia

- ZESTAW jest używany do utworzenia nowej właściwości lub zaktualizuj wartości właściwości istniejącej.
- Usuń służy do usuwania właściwości.
- ZESTAW wykonuje niejawna konwersja Jeśli to możliwe, jeśli różni się od wyrażenia typu i istniejący typ właściwości.
- Akcja zakończy się niepowodzeniem, jeśli właściwości systemu nieistniejącą pojawiały się odniesienia.
- Akcja się niepowodzeniem, jeśli zostały odwołań do właściwości użytkownika nie istnieje.
- Właściwość nieistniejącą użytkownika jest szacowana jako "Nieznana" wewnętrznie, po tej samej semantyki jako [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) podczas obliczania operatorów.

## <a name="next-steps"></a>Kolejne kroki

- [Klasa SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Klasa SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
