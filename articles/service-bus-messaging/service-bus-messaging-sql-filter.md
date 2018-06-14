---
title: Odwołania do składni Azure Service Bus SQLFilter | Dokumentacja firmy Microsoft
description: Szczegółowe informacje o SQLFilter gramatyki.
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
ms.openlocfilehash: ec9d728eb31eb979e82bfb53cf619f823750e65c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29132171"
---
# <a name="sqlfilter-syntax"></a>Składnia SQLFilter

A *SqlFilter* obiektu jest wystąpieniem [klasy SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)i reprezentuje wyrażenie filtru oparty na języku SQL, który jest porównywany [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). SqlFilter obsługuje standardu SQL 92.  
  
 Ten temat zawiera szczegółowe informacje o SqlFilter gramatyki.  
  
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
  
-   `<scope>`opcjonalny ciąg wskazujący zakres jest `<property_name>`. Prawidłowe wartości to `sys` lub `user`. `sys` Wartość wskazuje zakres systemu gdzie `<property_name>` jest nazwą właściwości publicznej [BrokeredMessage klasy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`Wskazuje zakres użytkowników gdzie `<property_name>` jest kluczem elementu [klasy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) słownika. `user`zakres jest zakres domyślny, jeśli `<scope>` nie jest określona.  
  
## <a name="remarks"></a>Uwagi

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
  
Ta gramatyka oznacza dowolny ciąg, który rozpoczyna się od litery i następuje co najmniej jeden znak podkreślenia/list/cyfr.  
  
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
  
     Są to przykłady stałych długa:  
  
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

Stałe typu logicznego są reprezentowane przez słowa kluczowe **TRUE** lub **FALSE**. Wartości są przechowywane jako `System.Boolean`.  
  
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
  
Należy rozważyć [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) semantyka:  
  
-   Nazwy właściwości jest rozróżniana wielkość liter.  
  
-   Operatory wykonaj C# niejawna konwersja semantyki zawsze, gdy jest to możliwe.  
  
-   Właściwości systemu są właściwości publiczne w [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) wystąpień.  
  
    Należy rozważyć `IS [NOT] NULL` semantyka:  
  
    -   `property IS NULL`jest szacowana jako `true` Jeśli właściwość nie istnieje lub jest wartość właściwości `null`.  
  
### <a name="property-evaluation-semantics"></a>Semantyka oceny właściwości  
  
-   Próba oszacować właściwości nieistniejącego systemu powoduje [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) wyjątku.  
  
-   Właściwość, która nie istnieje wewnętrznie jest szacowana jako **nieznany**.  
  
 Nieznany oceny w operatory arytmetyczne:  
  
-   Dla operatorów binarnych, jeśli po lewej lub prawej stronie argumentów jest szacowana jako **nieznany**, a następnie wynik jest **nieznany**.  
  
-   Dla operatory jednoargumentowe, jeśli argument jest szacowana jako **nieznany**, a następnie wynik jest **nieznany**.  
  
 Nieznany oceny w operatory binarne porównania:  
  
-   Jeśli po lewej lub prawej stronie argumentów jest oceniana jako **nieznany**, a następnie wynik jest **nieznany**.  
  
 Nieznany oceny w `[NOT] LIKE`:  
  
-   Jeśli wszystkie operand jest szacowana jako **nieznany**, to znajdują się **nieznany**.  
  
 Nieznany oceny w `[NOT] IN`:  
  
-   Jeśli lewy operand jest szacowana jako **nieznany**, a następnie wynik jest **nieznany**.  
  
 Nieznany oceny w **i** operator:  
  
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
  
 Nieznany oceny w **lub** operator:  
  
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
  
### <a name="operator-binding-semantics"></a>Semantyka powiązania — operator
  
-   Operatory porównania, takich jak `>`, `>=`, `<`, `<=`, `!=`, i `=` postępuj zgodnie z tej samej semantyki jako operator C# powiązanie w promocji typu danych i niejawne konwersje.  
  
-   Operatory arytmetyczne, takie jak `+`, `-`, `*`, `/`, i `%` postępuj zgodnie z tej samej semantyki jako operator C# powiązanie w promocji typu danych i niejawne konwersje.

## <a name="next-steps"></a>Kolejne kroki

- [Klasa SQLFilter (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Klasa SQLFilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.filters.sqlfilter)
- [Klasa SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)