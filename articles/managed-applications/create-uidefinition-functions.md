---
title: Azure Managed Application create funkcje definicji interfejsu użytkownika | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje do użycia podczas konstruowania definicji interfejsu użytkownika usługi Azure Managed Applications
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 80fd593eecf189d516a8c9d7ef2a94ec9f23fc39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587924"
---
# <a name="createuidefinition-functions"></a>Funkcje CreateUiDefinition
Ta sekcja zawiera podpisy dla wszystkich obsługiwanych funkcji CreateUiDefinition.

Aby korzystać z funkcji, należy ująć deklaracji z nawiasami kwadratowymi. Na przykład:

```json
"[function()]"
```

Ciągi i inne funkcje, które mogą być przywoływane jako parametrów dla funkcji, ale parametry muszą być ujęte w apostrofy. Na przykład:

```json
"[fn1(fn2(), 'foobar')]"
```

Gdy to stosowne, mogą przywoływać właściwości danych wyjściowych przez funkcję za pomocą operatora kropki. Na przykład:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Funkcje odwołań
Te funkcje można odwoływać się do danych wyjściowych z właściwości lub kontekst CreateUiDefinition.

### <a name="basics"></a>Podstawy
Zwraca wartości danych wyjściowych elementu, który jest zdefiniowany w kroku podstawy.

Poniższy przykład zwraca dane wyjściowe elementu o nazwie `foo` w kroku podstawy:

```json
"[basics('foo')]"
```

### <a name="steps"></a>kroki
Zwraca wartości danych wyjściowych elementu, który jest zdefiniowany w określonym punkcie. Aby uzyskać wartości danych wyjściowych elementów w kroku podstawy, użyj `basics()` zamiast tego.

Poniższy przykład zwraca dane wyjściowe elementu o nazwie `bar` w kroku o nazwie `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Zwraca lokalizację wybrana w kroku podstawy lub w bieżącym kontekście.

Poniższy przykład może zwrócić `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Funkcje ciągów
Te funkcje należy używać tylko z ciągami formatu JSON.

### <a name="concat"></a>concat
Łączy jeden lub więcej ciągów.

Na przykład jeśli wartość danych wyjściowych `element1` Jeśli `"bar"`, a następnie w tym przykładzie zwraca ciąg `"foobar!"`:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>podciąg
Zwraca podciąg określonego ciągu. Podciąg rozpoczyna się od określonego indeksu i ma określony czas.

Poniższy przykład zwraca `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>Zastąp
Zwraca ciąg, w której wszystkie wystąpienia określonego ciągu w ciągu bieżącej zastępuje się innym ciągiem.

Poniższy przykład zwraca `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>Identyfikator GUID
Generuje ciąg globalnie unikatowy (identyfikator globalny GUID).

Poniższy przykład może zwrócić `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Zwraca ciąg przekonwertowany na małe litery.

Poniższy przykład zwraca `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Zwraca ciąg przekonwertowany na wielkie litery.

Poniższy przykład zwraca `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Kolekcja funkcji
Te funkcje mogą być używane z kolekcjami, takich jak ciągi JSON, tablic i obiektów.

### <a name="contains"></a>zawiera
Zwraca `true` ciąg zawiera podany podciąg, tablica zawiera określoną wartość, czy obiekt zawiera określony klucz.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy `element1` zwraca `[1, 2, 3]`. Poniższy przykład zwraca `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy `element1` zwraca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład zwraca `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>Długość
Zwraca liczbę znaków w ciąg, liczba wartości w tablicy lub liczba kluczy w obiekcie.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy `element1` zwraca `[1, 2, 3]`. Poniższy przykład zwraca `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy `element1` zwraca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład zwraca `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>pusty
Zwraca `true` przypadku ciągów, tablicy lub obiektu o wartości null ani być pusta.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy `element1` zwraca `[1, 2, 3]`. Poniższy przykład zwraca `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy `element1` zwraca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład zwraca `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Przykład 4: wartości null i niezdefiniowane
Załóżmy `element1` jest `null` lub niezdefiniowaną. Poniższy przykład zwraca `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>pierwszy
Zwraca pierwszy znak określony ciąg; Pierwsza wartość określonej tablicy; lub pierwszy klucz i wartość określonego obiektu.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy `element1` zwraca `[1, 2, 3]`. Poniższy przykład zwraca `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy `element1` zwraca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Poniższy przykład zwraca `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>ostatni
Zwraca ostatni znak określony ciąg, ostatnie wartości określonej tablicy lub klucz ostatnio i wartość określonego obiektu.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy `element1` zwraca `[1, 2, 3]`. Poniższy przykład zwraca `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy `element1` zwraca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład zwraca `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>Wypełnij
Zwraca określoną liczbę znaków sąsiadujących z początku ciągu, określoną liczbę wartości sąsiadujących z początku tablicy lub określoną liczbę ciągłych kluczy i wartości od samego początku obiektu.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy `element1` zwraca `[1, 2, 3]`. Poniższy przykład zwraca `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy `element1` zwraca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład zwraca `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>pomiń
Pomija określoną liczbę elementów w kolekcji, a następnie zwraca pozostałe elementy.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy `element1` zwraca `[1, 2, 3]`. Poniższy przykład zwraca `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy `element1` zwraca:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Poniższy przykład zwraca `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Funkcje logiczne
Te funkcje można używać w Stanach warunkowych. Niektóre funkcje mogą nie obsługiwać wszystkich typów danych JSON.

### <a name="equals"></a>równa się
Zwraca `true` Jeśli oba parametry mają ten sam typ i wartość. Ta funkcja obsługuje wszystkie typy danych JSON.

Poniższy przykład zwraca `true`:

```json
"[equals(0, 0)]"
```

Poniższy przykład zwraca `true`:

```json
"[equals('foo', 'foo')]"
```

Poniższy przykład zwraca `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>mniej
Zwraca `true` , jeśli pierwszy parametr jest mniejsza niż drugi parametr. Ta funkcja obsługuje tylko parametry typu number i ciąg.

Poniższy przykład zwraca `true`:

```json
"[less(1, 2)]"
```

Poniższy przykład zwraca `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Zwraca `true` Jeśli pierwszy parametr jest mniejszy niż drugi parametr. Ta funkcja obsługuje tylko parametry typu number i ciąg.

Poniższy przykład zwraca `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>większa
Zwraca `true` Jeśli pierwszy parametr jest większa niż drugi parametr. Ta funkcja obsługuje tylko parametry typu number i ciąg.

Poniższy przykład zwraca `false`:

```json
"[greater(1, 2)]"
```

Poniższy przykład zwraca `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Zwraca `true` , jeśli pierwszy parametr jest większa lub równa wartości drugiego parametru. Ta funkcja obsługuje tylko parametry typu number i ciąg.

Poniższy przykład zwraca `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>i
Zwraca `true` Jeśli wszystkie parametry mają `true`. Ta funkcja obsługuje tylko dwóch lub więcej parametrów typu wartość logiczna.

Poniższy przykład zwraca `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Poniższy przykład zwraca `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>lub
Zwraca `true` , gdy co najmniej jeden z parametrów jest `true`. Ta funkcja obsługuje tylko dwóch lub więcej parametrów typu wartość logiczna.

Poniższy przykład zwraca `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Poniższy przykład zwraca `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>nie
Zwraca `true` Jeśli parametr daje w wyniku `false`. Ta funkcja obsługuje tylko parametrów typu wartość logiczna.

Poniższy przykład zwraca `true`:

```json
"[not(false)]"
```

Poniższy przykład zwraca `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>COALESCE
Zwraca wartość pierwszego parametru inną niż null. Ta funkcja obsługuje wszystkie typy danych JSON.

Załóżmy `element1` i `element2` są niezdefiniowane. Poniższy przykład zwraca `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Funkcje konwersji
Te funkcje można przekonwertować wartości z zakresu od kodowania i typy danych JSON.

### <a name="int"></a>int
Konwertuje parametr na liczbę całkowitą. Ta funkcja obsługuje parametry typu number i ciąg.

Poniższy przykład zwraca `1`:

```json
"[int('1')]"
```

Poniższy przykład zwraca `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Konwertuje parametr liczb zmiennoprzecinkowych. Ta funkcja obsługuje parametry typu number i ciąg.

Poniższy przykład zwraca `1.0`:

```json
"[float('1.0')]"
```

Poniższy przykład zwraca `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>string
Konwertuje parametr na ciąg. Ta funkcja obsługuje parametry wszystkich typów danych JSON.

Poniższy przykład zwraca `"1"`:

```json
"[string(1)]"
```

Poniższy przykład zwraca `"2.9"`:

```json
"[string(2.9)]"
```

Poniższy przykład zwraca `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

Poniższy przykład zwraca `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Konwertuje parametr na wartość logiczną. Ta funkcja obsługuje parametry typu number, string i Boolean. Podobnie jak wartości logicznych w języku JavaScript, wszystkie wartości z wyjątkiem `0` lub `'false'` zwraca `true`.

Poniższy przykład zwraca `true`:

```json
"[bool(1)]"
```

Poniższy przykład zwraca `false`:

```json
"[bool(0)]"
```

Poniższy przykład zwraca `true`:

```json
"[bool(true)]"
```

Poniższy przykład zwraca `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>Analizy
Konwertuje parametr typu natywnego. Innymi słowy, ta funkcja jest przeciwieństwem `string()`. Ta funkcja obsługuje tylko parametry typu string.

Poniższy przykład zwraca `1`:

```json
"[parse('1')]"
```

Poniższy przykład zwraca `true`:

```json
"[parse('true')]"
```

Poniższy przykład zwraca `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

Poniższy przykład zwraca `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Koduje parametr na ciąg zakodowany base-64. Ta funkcja obsługuje tylko parametry typu string.

Poniższy przykład zwraca `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Dekoduje parametr z ciąg zakodowany base-64. Ta funkcja obsługuje tylko parametry typu string.

Poniższy przykład zwraca `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Koduje parametr na ciąg zakodowany w adresie URL. Ta funkcja obsługuje tylko parametry typu string.

Poniższy przykład zwraca `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Dekoduje parametr z ciąg zakodowany w adresie URL. Ta funkcja obsługuje tylko parametry typu string.

Poniższy przykład zwraca `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Funkcje matematyczne
### <a name="add"></a>add
Dodaje dwie liczby i zwraca wynik.

Poniższy przykład zwraca `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Sub
Odejmuje drugą liczbę od pierwszej liczby i zwraca wynik.

Poniższy przykład zwraca `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Mnoży dwie liczby i zwraca wynik.

Poniższy przykład zwraca `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Dzieli liczbę pierwszy, drugi liczbą i zwraca wynik. Wynik jest zawsze liczbą całkowitą.

Poniższy przykład zwraca `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>dzielenie modulo
Dzieli liczbę pierwszy, drugi liczbą i zwraca resztę z dzielenia.

Poniższy przykład zwraca `0`:

```json
"[mod(6, 3)]"
```

Poniższy przykład zwraca `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min.
Zwraca mały dwóch liczb.

Poniższy przykład zwraca `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>maks.
Zwraca większy z dwóch liczb.

Poniższy przykład zwraca `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>Zakres
Generuje sekwencję liczb całkowitych w obrębie określonego zakresu.

Poniższy przykład zwraca `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>RAND
Zwraca losową liczbę całkowitą z określonego zakresu. Ta funkcja nie generuje kryptograficznie bezpieczne liczb losowych.

Poniższy przykład może zwrócić `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>FLOOR
Zwraca największą liczbę całkowitą mniejszą niż określona liczba.

Poniższy przykład zwraca `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>Ceil —
Zwraca największą liczbę całkowitą, większy lub równy podanej liczbie.

Poniższy przykład zwraca `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Funkcje daty
### <a name="utcnow"></a>utcNow
Zwraca ciąg w formacie ISO 8601 bieżącą datę i godzinę na komputerze lokalnym.

Poniższy przykład może zwrócić `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Dodaje całkowitą liczbę sekund do określonej sygnatury czasowej.

Poniższy przykład zwraca `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Dodaje całkowitą liczbę minut do określonej sygnatury czasowej.

Poniższy przykład zwraca `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Dodaje całkowitą liczbę godzin do określonej sygnatury czasowej.

Poniższy przykład zwraca `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

