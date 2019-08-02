---
title: Odwołanie do składni SQLRuleAction na platformie Azure | Microsoft Docs
description: Szczegóły dotyczące gramatyki SQLRuleAction.
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
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "60307482"
---
# <a name="sqlruleaction-syntax"></a>Składnia elementu SQLRuleAction

*SqlRuleAction* jest wystąpieniem klasy [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) i reprezentuje zestaw akcji utworzonych w składni opartej na języku SQL, która jest wykonywana dla [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
W tym artykule przedstawiono szczegółowe informacje na temat gramatyki akcji reguły SQL.  
  
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
  
-   `<scope>`jest opcjonalnym ciągiem wskazującym zakres `<property_name>`. Prawidłowe wartości to `sys` lub `user`. Wartość wskazuje zakres systemowy, `<property_name>` gdzie jest publiczną nazwą właściwości [klasy BrokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) `sys` `user`wskazuje zakres użytkownika, `<property_name>` gdzie jest kluczem słownika [klasy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . `user`zakres jest zakresem domyślnym, `<scope>` Jeśli nie jest określony.  
  
### <a name="remarks"></a>Uwagi  

Próba uzyskania dostępu do nieistniejącej właściwości systemu jest błędem, podczas gdy próba uzyskania dostępu do nieistniejącej właściwości użytkownika nie jest błędem. Zamiast tego nieistniejąca Właściwość użytkownika jest obliczana wewnętrznie jako nieznana wartość. Nieznana wartość jest traktowana specjalnie podczas obliczania operatora.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenty  
 `<regular_identifier>`jest ciągiem przedstawionym przez następujące wyrażenie regularne:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Oznacza to dowolny ciąg, który rozpoczyna się od litery, po której następuje jeden lub więcej podkreśleń/liter/cyfr.  
  
 `[:IsLetter:]`oznacza dowolny znak Unicode, który jest kategoryzowany jako litera Unicode. `System.Char.IsLetter(c)`zwraca `true` wartość `c` , jeśli jest literą Unicode.  
  
 `[:IsDigit:]`oznacza dowolny znak Unicode, który jest kategoryzowany jako cyfra dziesiętna. `System.Char.IsDigit(c)`zwraca `true` wartość `c` , jeśli jest cyfrą Unicode.  
  
 A `<regular_identifier>` nie może być zastrzeżonym słowem kluczowym.  
  
 `<delimited_identifier>`jest dowolnym ciągiem, który jest ujęty w nawiasy kwadratowe ([]). Prawy nawias kwadratowy jest reprezentowany jako dwa prawy nawias kwadratowy. Poniżej przedstawiono przykłady `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>`to dowolny ciąg, który jest ujęty w znaki podwójnego cudzysłowu. Podwójny cudzysłów w identyfikatorze jest reprezentowany jako dwa podwójne cudzysłowy. Nie zaleca się używania identyfikatorów w cudzysłowie, ponieważ można je łatwo pomylić z stałą typu String. Jeśli to możliwe, należy użyć oddzielonego identyfikatora. Oto przykład `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>znaczne  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi
  
 `<pattern>`musi być wyrażeniem, które jest oceniane jako ciąg. Jest używany jako wzorzec dla operatora LIKE.      Może zawierać następujące symbole wieloznaczne:  
  
-   `%`:  Dowolny ciąg składający się z zero lub więcej znaków.  
  
-   `_`: Dowolny pojedynczy znak.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi
  
 `<escape_char>`musi być wyrażeniem, które jest oceniane jako ciąg o długości 1. Jest używany jako znak ucieczki dla operatora LIKE.  
  
 Na przykład `property LIKE 'ABC\%' ESCAPE '\'` pasuje `ABC%` do ciągu, który rozpoczyna się `ABC`od.  
  
## <a name="constant"></a>Stałe  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenty  
  
-   `<integer_constant>`jest ciągiem liczb, które nie są ujęte w cudzysłów i nie zawierają punktów dziesiętnych. Wartości są przechowywane jako `System.Int64` wewnętrznie i zgodne z tym samym zakresem.  
  
     Poniżej przedstawiono przykłady długich stałych:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`jest ciągiem liczb, które nie są ujęte w cudzysłów i zawierają separator dziesiętny. Wartości są przechowywane jako `System.Double` wewnętrznie i zgodne z tym samym zakresem/dokładnością.  
  
     W przyszłych wersjach ten numer może być przechowywany w innym typie danych do obsługi dokładnej semantyki liczb, dlatego nie należy polegać na tym, że jest `System.Double` `<decimal_constant>`to typ danych bazowych.  
  
     Poniżej przedstawiono przykłady stałych dziesiętnych:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`to liczba zapisywana w notacji wykładniczej. Wartości są przechowywane jako `System.Double` wewnętrznie i zgodne z tym samym zakresem/dokładnością. Poniżej przedstawiono przykłady przybliżonych stałych liczbowych:  
  
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
  
Stałe logiczne są reprezentowane przez słowa kluczowe `TRUE` lub `FALSE`. Wartości są przechowywane jako `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Uwagi
  
Stałe ciągów są ujęte w znaki pojedynczego cudzysłowu i zawierają wszystkie prawidłowe znaki Unicode. Pojedynczy cudzysłów osadzony w stałej ciągu jest reprezentowany jako dwa znaki pojedynczego cudzysłowu.  
  
## <a name="function"></a>funkcja  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Uwagi  

Funkcja zwraca obiekt **System. GUID** wygenerowany przez `System.Guid.NewGuid()` metodę. `newid()`  
  
Funkcja zwraca wartość właściwości, `name`do której odwołuje się. `property(name)` `name` Wartość może być dowolnym prawidłowym wyrażeniem zwracającym wartość ciągu.  
  
## <a name="considerations"></a>Zagadnienia do rozważenia

- ZESTAW służy do tworzenia nowej właściwości lub aktualizowania wartości istniejącej właściwości.
- Usuń służy do usuwania właściwości.
- Ustaw wykonuje niejawną konwersję, jeśli jest to możliwe, gdy typ wyrażenia i typ istniejącej właściwości są różne.
- Akcja kończy się niepowodzeniem, jeśli odwołuje się do nieistniejących właściwości systemu.
- Akcja nie powiedzie się, jeśli istnieją odwołania do nieistniejących właściwości użytkownika.
- Nieistniejąca Właściwość użytkownika jest szacowana jako "nieznana" wewnętrznie, przy użyciu tej samej semantyki [](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) jako sqlfilter podczas oceniania operatorów.

## <a name="next-steps"></a>Następne kroki

- [Klasa SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Klasa sqlfilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
