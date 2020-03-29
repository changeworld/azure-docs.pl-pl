---
title: Odwołanie do składni sqlfiltr usługi Azure Service Bus | Dokumenty firmy Microsoft
description: Ten artykuł zawiera szczegółowe informacje na temat gramatyki sqlfilter. SqlFilter obsługuje podzbiór standardu SQL-92.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: spelluru
ms.openlocfilehash: d5a8e165fcee23c5feecd5935983dd77d3ec6c30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759667"
---
# <a name="sqlfilter-syntax"></a>Składnia elementu SQLFilter

Obiekt *SqlFilter* jest wystąpieniem [klasy SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)i reprezentuje wyrażenie filtru oparte na języku SQL, które jest oceniane względem [brokeredmessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). SqlFilter obsługuje podzbiór standardu SQL-92.  
  
 W tym temacie wymieniono szczegółowe informacje na temat gramatyki programu SqlFilter.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
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
  
-   `<scope>`jest opcjonalnym ciągiem wskazującym `<property_name>`zakres pliku . Prawidłowe `sys` wartości `user`są lub . Wartość `sys` wskazuje zakres systemowy, gdzie `<property_name>` jest nazwą właściwości publicznej [brokeredMessage klasy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`wskazuje zakres użytkownika, gdzie `<property_name>` jest kluczem [słownika klasy BrokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) `user`zakres jest zakresem `<scope>` domyślnym, jeśli nie jest określony.  
  
## <a name="remarks"></a>Uwagi

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
  
Ta gramatyka oznacza dowolny ciąg, który zaczyna się od litery, a po nim występuje jeden lub więcej podkreślenia/litery/cyfry.  
  
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
  
     Oto przykłady długich stałych:  
  
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

Stałe logiczne są reprezentowane przez słowa kluczowe **PRAWDA** lub **FAŁSZ**. Wartości są przechowywane `System.Boolean`jako .  
  
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
  
Należy wziąć pod uwagę następujące semantyka [SqlFilter:](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)  
  
-   Nazwy właściwości są niewrażliwe na wielkości liter.  
  
-   Operatory wykonaj C# niejawna semantyka konwersji, gdy jest to możliwe.  
  
-   Właściwości systemu są właściwości publiczne widoczne w [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) wystąpień.  
  
    Należy wziąć `IS [NOT] NULL` pod uwagę następujące semantyka:  
  
    -   `property IS NULL`jest oceniana tak, `true` jakby właściwość nie istniała lub `null`wartość właściwości jest .  
  
### <a name="property-evaluation-semantics"></a>Semantyka oceny właściwości  
  
- Próba oceny nieistniejącej właściwości systemu zgłasza wyjątek [FilterException.](/dotnet/api/microsoft.servicebus.messaging.filterexception)  
  
- Właściwość, która nie istnieje, jest wewnętrznie oceniana jako **nieznana.**  
  
  Nieznana ocena w operatorach arytmetycznych:  
  
- Dla operatorów binarnych, jeśli lewa i/lub prawa strona operandów jest oceniana jako **nieznana,** wynik jest **nieznany.**  
  
- W przypadku operatorów bezemisowych, jeśli operand jest oceniany jako **nieznany,** wynik jest **nieznany**.  
  
  Nieznana ocena w operatorach porównania binarnego:  
  
- Jeśli lewa i/lub prawa strona operandów jest oceniana jako **nieznana,** wynik jest **nieznany.**  
  
  Nieznana `[NOT] LIKE`ocena w :  
  
- Jeśli dowolny operand jest oceniany jako **nieznany,** wynik jest **nieznany.**  
  
  Nieznana `[NOT] IN`ocena w :  
  
- Jeśli lewy operand jest oceniany jako **nieznany,** wynik jest **nieznany**.  
  
  Nieznana ocena w **operatorze AND:**  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Nieznana ocena w **operatorze OR:**  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Semantyka wiązania operatora
  
-   Operatory porównania, `<`takie `<=` `!=`jak `>` `=` , `>=`, , , i postępuj zgodnie z tą samą semantyki jako powiązanie operatora C# w promocjach typu danych i konwersjach niejawnych.  
  
-   Operatory arytmetyczne, `-` `*`takie `/`jak `%` `+`, , i postępuj zgodnie z tą samą semantyką co powiązanie operatora C# w promocjach typu danych i konwersjach niejawnych.

## <a name="next-steps"></a>Następne kroki

- [Klasa SQLFilter (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Klasa SQLFilter (standard.NET)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Klasa SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
