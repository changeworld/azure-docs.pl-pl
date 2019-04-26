---
title: Dokumentacja składni elementu SQLRuleAction na platformie Azure | Dokumentacja firmy Microsoft
description: Szczegółowe informacje na temat elementu SQLRuleAction gramatyki.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: 0f9365b72da1cec81eed82756097d32b1d72ca71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307482"
---
# <a name="sqlruleaction-syntax"></a>Składnia elementu SQLRuleAction

A *elementu SqlRuleAction* jest wystąpieniem [elementu SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) klasy i reprezentuje zestaw akcji, napisane w języku SQL na podstawie składni, która nie jest przeprowadzana [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
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
  
-   `<scope>` jest opcjonalny ciąg wskazujący zakres `<property_name>`. Prawidłowe wartości to `sys` lub `user`. `sys` Wartość wskazuje zakres systemu gdzie `<property_name>` jest nazwą właściwości publicznej [klasy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` Określa zakres użytkowników gdzie `<property_name>` jest kluczem [klasy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) słownika. `user` zakres jest domyślny zakres, jeśli `<scope>` nie zostanie określony.  
  
### <a name="remarks"></a>Uwagi  

Próba dostępu do właściwości systemu nie istnieje, występuje błąd, podczas próby dostępu do właściwości nieistniejącego użytkownika nie jest błąd. Zamiast tego właściwość nieistniejącego użytkownika wewnętrznie zostanie ocenione jako Nieznana wartość. Nieznana wartość jest traktowany specjalnie podczas obliczania operatora.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenty  
 `<regular_identifier>` ciąg reprezentowanego przez następujące wyrażenie regularne:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Oznacza to, dowolny ciąg, który zaczyna się od litery i następuje co najmniej jeden znak podkreślenia/list/cyfr.  
  
 `[:IsLetter:]` oznacza, że dowolny znak Unicode, który jest dzielony na kategorie litery Unicode. `System.Char.IsLetter(c)` Zwraca `true` Jeśli `c` to litera Unicode.  
  
 `[:IsDigit:]` oznacza, że dowolny znak Unicode, który jest dzielony na kategorie, wyświetlaną cyfrą dziesiętną. `System.Char.IsDigit(c)` Zwraca `true` Jeśli `c` jest cyfrą Unicode.  
  
 Element `<regular_identifier>` nie może być zastrzeżonym słowem kluczowym.  
  
 `<delimited_identifier>` jest dowolny ciąg, który jest ujęty w nawiasy kwadratowe ([]) lewa i prawa. Prawy nawias kwadratowy jest przedstawiana jako dwa prawo nawiasami kwadratowymi. Poniżej przedstawiono przykłady `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` jest dowolny ciąg, który jest ujęty w cudzysłów. Podwójny cudzysłów w identyfikatorze jest przedstawiana jako dwa podwójne cudzysłowy. Nie zaleca się użyć identyfikatory w cudzysłowach, ponieważ można łatwo pomylić z stałą typu string. Jeśli to możliwe za pomocą rozdzielany identyfikatora. Oto przykład `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>Wzorzec  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi
  
 `<pattern>` musi być wyrażeniem, które jest oceniane jako ciąg. Jest używana jako wzorzec dla LIKE operator.      Może zawierać następujące znaki symboli wieloznacznych:  
  
-   `%`:  Dowolny ciąg zawierający zero lub więcej znaków.  
  
-   `_`: Dowolny pojedynczy znak.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi
  
 `<escape_char>` musi być wyrażeniem, które jest oceniane jako ciąg o długości 1. Jest używany jako znak ucieczki LIKE operatora.  
  
 Na przykład `property LIKE 'ABC\%' ESCAPE '\'` odpowiada `ABC%` zamiast ciąg, który rozpoczyna się od `ABC`.  
  
## <a name="constant"></a>Stałe  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenty  
  
-   `<integer_constant>` jest to ciąg liczb, które nie są ujęte w znaki cudzysłowu i nie zawiera punktu dziesiętnego. Wartości są przechowywane jako `System.Int64` wewnętrznie i postępuj zgodnie z tym samym zakresie.  
  
     Poniżej przedstawiono przykłady długie stałe:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` jest to ciąg liczb, które nie są ujęte w znaki cudzysłowu i zawiera separator dziesiętny. Wartości są przechowywane jako `System.Double` wewnętrznie i postępuj zgodnie z tym samym zakresem/dokładności.  
  
     W przyszłych wersjach tego numeru mogą być przechowywane w różne typy danych do obsługi dokładnie semantykę liczb, więc nie należy polegać na fakcie bazowego typu danych jest `System.Double` dla `<decimal_constant>`.  
  
     Poniżej przedstawiono przykłady stałe dziesiętne:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` jest liczba napisanych w notacji naukowej. Wartości są przechowywane jako `System.Double` wewnętrznie i postępuj zgodnie z tym samym zakresem/dokładności. Poniżej przedstawiono przykłady przybliżony number — stałe:  
  
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
  
Wartość logiczna stałe są reprezentowane przez słowa kluczowe `TRUE` lub `FALSE`. Wartości są przechowywane jako `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Uwagi
  
Stałe typu String są ujęte w znaki pojedynczego cudzysłowu i zawierać żadnych prawidłowych znaków Unicode. Znak pojedynczego cudzysłowu osadzone w stałą typu string jest reprezentowany jako dwa pojedyncze cudzysłowy.  
  
## <a name="function"></a>funkcja  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Uwagi  

`newid()` Funkcja zwraca **System.Guid** generowane przez `System.Guid.NewGuid()` metody.  
  
`property(name)` Funkcja zwraca wartość właściwości odwołuje się `name`. `name` Wartość może być dowolne prawidłowe wyrażenie zwracające wartość ciągu.  
  
## <a name="considerations"></a>Zagadnienia do rozważenia

- ZESTAW jest używany do tworzenia nowej właściwości lub zaktualizuj wartość istniejącej właściwości.
- Usuń umożliwia usunięcie właściwości powoduje.
- ZESTAW wykonuje niejawnej konwersji, jeśli jest to możliwe przy użyciu wyrażenia tego typu i istniejącego typu właściwości różnią się.
- Akcja zakończy się niepowodzeniem, jeśli przywoływane właściwości systemu nie istnieje.
- Działanie się nie powieść, jeśli przywoływane właściwości użytkownika nie istnieje.
- Właściwość nieistniejącego użytkownika jest oceniane jako "Nieznana" wewnętrznie, zgodnie z tą samą semantyką jako [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) podczas obliczania operatorów.

## <a name="next-steps"></a>Kolejne kroki

- [Klasa elementu SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter class](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
