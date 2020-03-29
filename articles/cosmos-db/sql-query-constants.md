---
title: Stałe SQL w usłudze Azure Cosmos DB
description: Dowiedz się, jak stałe kwerendy SQL w usłudze Azure Cosmos DB są używane do reprezentowania określonej wartości danych
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: cca62c358037dbe99fd16746ee081b1540161df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873424"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Stałe zapytań SQL usługi Azure Cosmos DB SQL  

 Stała, znana również jako wartość literału lub skalarna, jest symbolem reprezentującym określoną wartość danych. Format stałej zależy od typu danych wartości, które reprezentuje.  
  
 **Obsługiwane typy danych skalarnych:**  
  
|**Typ**|**Kolejność wartości**|  
|-|-|  
|**Niezdefiniowane**|Pojedyncza wartość: **niezdefiniowana**|  
|**Null**|Pojedyncza wartość: **null**|  
|**Wartość logiczna**|Wartości: **false**, **true**.|  
|**Numer**|Podwójna precyzja zmiennoprzecinkowej liczby, standard IEEE 754.|  
|**Ciąg**|Sekwencja zero lub więcej znaków Unicode. Ciągi muszą być ujęte w cudzysłowie pojedyncze lub podwójne.|  
|**Tablicy**|Sekwencja zero lub więcej elementów. Każdy element może być wartością dowolnego typu danych skalarnych, z wyjątkiem **niezdefiniowanego**.|  
|**Obiektu**|Nieurządzony zestaw par zero lub więcej nazw/wartości. Nazwa jest ciągiem Unicode, wartość może być dowolnego typu danych skalarnych, z wyjątkiem **undefined**.|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Składni
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="arguments"></a><a name="bk_arguments"></a>Argumenty
  
* `<undefined_constant>; Undefined`  
  
  Reprezentuje niezdefiniowana wartość typu Undefined.  
  
* `<null_constant>; null`  
  
  Reprezentuje wartość **null** typu **Null**.  
  
* `<boolean_constant>`  
  
  Reprezentuje stałą typu Boolean.  
  
* `false`  
  
  Reprezentuje **wartość false** typu Boolean.  
  
* `true`  
  
  Reprezentuje **prawdziwą** wartość typu Boolean.  
  
* `<number_constant>`  
  
  Reprezentuje stałą.  
  
* `decimal_literal`  
  
  Literały dziesiętne to liczby reprezentowane przy użyciu notacji dziesiętnej lub notacji naukowej.  
  
* `hexadecimal_literal`  
  
  Szesnastkowe literały to liczby reprezentowane przy użyciu prefiksu "0x", po którym następuje jedna lub więcej cyfr szesnastkowych.  
  
* `<string_constant>`  
  
  Reprezentuje stałą typu String.  
  
* `string _literal`  
  
  Literały ciągów to ciągi Unicode reprezentowane przez sekwencję zero lub więcej znaków Unicode lub sekwencji ucieczki. Literały ciągów są ujęte w pojedyncze cudzysłowy (apostrof: ' ) lub cudzysłowy (cudzysłów: ").  
  
  Dozwolone są następujące sekwencje uchyłków:  
  
|**Sekwencja ucieczki**|**Opis**|**znak Unicode**|  
|-|-|-|  
|\\'|apostrof (')|U+0027|  
|\\"|cudzysłów (")|U+0022|  
|\\\ |rewers\\solidus ( )|U+005C|  
|\\/|solidus (/)|U+002F|  
|\b|Backspace|U+0008|  
|\f|pasza formularzy|U+000C|  
|\n|posuw liniowy|U+000A|  
|\r|powrót karetki|U +000D|  
|\t|Zakładka|U+0009|  
|\uXXXX|Znak Unicode zdefiniowany przez 4 cyfry szesnastkowe.|U+XXXX|  

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelowanie danych dokumentów](modeling-data.md)
