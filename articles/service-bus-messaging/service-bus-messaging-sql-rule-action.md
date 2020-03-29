---
title: Odwołanie do składni SQLRuleAction w usłudze Azure Service Bus
description: Ten artykuł zawiera odwołanie do składni SQLRuleAction. Akcje są zapisywane w składni opartej na języku SQL, która jest wykonywana względem wiadomości brokerskiej.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 37615e39577ef60cccc9df91b61a6aa24ca794d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759632"
---
# <a name="sqlruleaction-syntax-reference-for-azure-service-bus"></a>Odwołanie do składni SQLRuleAction dla usługi Azure Service Bus

*SqlRuleAction* jest wystąpieniem [klasy SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) i reprezentuje zestaw akcji napisanych w składni opartej na języku SQL, która jest wykonywana względem [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
W tym artykule wymieniono szczegółowe informacje na temat gramatyki akcji reguły SQL.  
  
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
  
-   `<scope>`jest opcjonalnym ciągiem wskazującym `<property_name>`zakres pliku . Prawidłowe `sys` wartości `user`są lub . Wartość `sys` wskazuje zakres systemowy, gdzie `<property_name>` jest nazwą właściwości publicznej [brokeredMessage Klasy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`wskazuje zakres użytkownika, gdzie `<property_name>` jest kluczem [słownika Klasy BrokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) `user`zakres jest zakresem `<scope>` domyślnym, jeśli nie jest określony.  
  
### <a name="remarks"></a>Uwagi  

Próba uzyskania dostępu do nieistniejącej właściwości systemu jest błędem, podczas gdy próba uzyskania dostępu do nieistniejącej właściwości użytkownika nie jest błędem. Zamiast tego nieistniejąca właściwość użytkownika jest wewnętrznie oceniana jako nieznana wartość. Nieznana wartość jest traktowana specjalnie podczas oceny operatora.  
  
## <a name="property_name"></a>Property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenty  
 `<regular_identifier>`jest ciągiem reprezentowanym przez następujące wyrażenie regularne:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Oznacza to, że dowolny ciąg, który zaczyna się od litery, a po nim występuje jeden lub więcej podkreślenia/litery/cyfry.  
  
 `[:IsLetter:]`oznacza dowolny znak Unicode, który jest klasyfikowany jako litera Unicode. `System.Char.IsLetter(c)``true` zwraca, `c` jeśli jest to litera Unicode.  
  
 `[:IsDigit:]`oznacza dowolny znak Unicode, który jest klasyfikowany jako cyfra dziesiętna. `System.Char.IsDigit(c)``true` zwraca, `c` jeśli jest cyfrą Unicode.  
  
 A `<regular_identifier>` nie może być zastrzeżonym słowem kluczowym.  
  
 `<delimited_identifier>`jest dowolnym ciągiem, który jest ujęty lewym/prawym nawiasem kwadratowym ([]). Prawy nawias kwadratowy jest reprezentowany jako dwa prawe nawiasy kwadratowe. Poniżej przedstawiono `<delimited_identifier>`przykłady:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>`to dowolny ciąg, który jest ujęty podwójnymi cudzysłowami. Podwójny cudzysłów w identyfikatorze jest reprezentowany jako dwa podwójne cudzysłowy. Nie zaleca się używania cytowanych identyfikatorów, ponieważ można go łatwo pomylić ze stałą ciągu. Jeśli to możliwe, użyj identyfikatora rozdzielanego. Poniżej przedstawiono `<quoted_identifier>`przykład:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>Wzór  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi
  
 `<pattern>`musi być wyrażeniem, które jest oceniane jako ciąg. Jest on używany jako wzorzec dla operatora LIKE.      Może zawierać następujące symbole wieloznaczne:  
  
-   `%`: Dowolny ciąg znaków zero lub więcej.  
  
-   `_`: Dowolny pojedynczy znak.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi
  
 `<escape_char>`musi być wyrażeniem, które jest oceniane jako ciąg długości 1. Jest on używany jako znak ucieczki dla operatora LIKE.  
  
 Na przykład `property LIKE 'ABC\%' ESCAPE '\'` `ABC%` dopasowuje, a nie `ABC`ciąg, który zaczyna się od .  
  
## <a name="constant"></a> — stała  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenty  
  
-   `<integer_constant>`jest ciągiem liczb, które nie są ujęte w cudzysłów i nie zawierają punktów dziesiętnych. Wartości są przechowywane `System.Int64` jako wewnętrznie i wykonaj ten sam zakres.  
  
     Poniżej przedstawiono przykłady stałych długich:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`jest ciągiem liczb, które nie są ujęte w cudzysłów i zawierają przecinek dziesiętnych. Wartości są przechowywane `System.Double` jako wewnętrznie i wykonaj ten sam zakres/dokładność.  
  
     W przyszłej wersji ten numer może być przechowywany w innym typie danych do obsługi dokładnej liczby semantyki, więc nie należy polegać na fakcie, że podstawowy typ danych jest `System.Double` dla `<decimal_constant>`.  
  
     Poniżej przedstawiono przykłady stałych dziesiętnych:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`jest liczbą zapisaną w notacji naukowej. Wartości są przechowywane `System.Double` jako wewnętrznie i wykonaj ten sam zakres/dokładność. Poniżej przedstawiono przykłady przybliżonych stałych liczbowych:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Uwagi
  
Stałe logiczne są reprezentowane przez `TRUE` `FALSE`słowa kluczowe lub . Wartości są przechowywane `System.Boolean`jako .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Uwagi
  
Stałe ciągów są ujęte w pojedyncze cudzysłowy i zawierają wszystkie prawidłowe znaki Unicode. Pojedynczy cudzysłów osadzony w stałej ciągu jest reprezentowany jako dwa pojedyncze cudzysłowy.  
  
## <a name="function"></a> — funkcja  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Uwagi  

Funkcja `newid()` zwraca **System.Guid** generowane `System.Guid.NewGuid()` przez metodę.  
  
Funkcja `property(name)` zwraca wartość właściwości, do `name`którego odwołuje się . Wartość `name` może być dowolnym prawidłowym wyrażeniem, które zwraca wartość ciągu.  
  
## <a name="considerations"></a>Zagadnienia do rozważenia

- Funkcja SET służy do tworzenia nowej właściwości lub aktualizowania wartości istniejącej właściwości.
- REMOVE służy do usuwania właściwości.
- SET wykonuje konwersji niejawnej, jeśli to możliwe, gdy typ wyrażenia i istniejący typ właściwości są różne.
- Akcja kończy się niepowodzeniem, jeśli odwoływano się do nieistniejących właściwości systemu.
- Akcja nie zakończy się niepowodzeniem, jeśli nie istnieją właściwości użytkownika zostały odwołane.
- Nieistniejąca właściwość użytkownika jest oceniana wewnętrznie jako "Nieznany", zgodnie z tą samą semantyką co [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) podczas oceny operatorów.

## <a name="next-steps"></a>Następne kroki

- [Klasa SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Klasa SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
