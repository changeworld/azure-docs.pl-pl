---
title: Tworzenie funkcji definicji interfejsu użytkownika
description: W tym artykule opisano funkcje używane podczas konstruowania definicji interfejsu użytkownika dla aplikacji zarządzanych platformy Azure
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6e56c5e528a17d42a75da54158f00857a917645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248452"
---
# <a name="createuidefinition-functions"></a>Tworzenie funkcji zdefiniowanie UiDefinition
Ta sekcja zawiera podpisy dla wszystkich obsługiwanych funkcji CreateUiDefinition.

Aby użyć funkcji, należy otoczyć deklarację nawiasami kwadratowymi. Przykład:

```json
"[function()]"
```

Ciągi i inne funkcje mogą być odwoływane jako parametry dla funkcji, ale ciągi muszą być otoczone w pojedynczych cudzysłowów. Przykład:

```json
"[fn1(fn2(), 'foobar')]"
```

W stosownych przypadkach można odwoływać się do właściwości danych wyjściowych funkcji przy użyciu operatora kropki. Przykład:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Odwoływanie się do funkcji
Te funkcje mogą służyć do odwoływania się do wyjść z właściwości lub kontekstu CreateUiDefinition.

### <a name="basics"></a>Podstawy
Zwraca wartości wyjściowe elementu zdefiniowanego w kroku Podstawy.

Poniższy przykład zwraca dane wyjściowe elementu o nazwie `foo` w kroku Podstawy:

```json
"[basics('foo')]"
```

### <a name="steps"></a>kroki
Zwraca wartości wyjściowe elementu zdefiniowanego w określonym kroku. Aby uzyskać wartości wyjściowe elementów w `basics()` kroku Podstawy, należy użyć zamiast tego.

Poniższy przykład zwraca dane wyjściowe elementu o nazwie `bar` w kroku o nazwie: `foo`

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Zwraca lokalizację zaznaczoną w kroku Podstawy lub w bieżącym kontekście.

Poniższy przykład `"westus"`może zwrócić:

```json
"[location()]"
```

## <a name="string-functions"></a>Funkcje ciągów
Te funkcje mogą być używane tylko z ciągami JSON.

### <a name="concat"></a>concat
Łączy jeden lub więcej ciągów.

Na przykład, jeśli wartość `element1` `"bar"`wyjściowa if , `"foobar!"`w tym przykładzie zwraca ciąg:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>Podciąg
Zwraca podciąg określonego ciągu. Podciąg rozpoczyna się od określonego indeksu i ma określoną długość.

Poniższy przykład `"ftw"`zwraca:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>Zastąp
Zwraca ciąg, w którym wszystkie wystąpienia określonego ciągu w bieżącym ciągu są zastępowane innym ciągiem.

Poniższy przykład `"Everything is awesome!"`zwraca:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>Identyfikator GUID
Generuje unikatowy ciąg globalny (GUID).

Poniższy przykład `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`może zwrócić:

```json
"[guid()]"
```

### <a name="tolower"></a>Tolower
Zwraca ciąg przekonwertowany na małe litery.

Poniższy przykład `"foobar"`zwraca:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>Toupper
Zwraca ciąg przekonwertowany na wielkie litery.

Poniższy przykład `"FOOBAR"`zwraca:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Funkcje zbierania
Te funkcje mogą być używane z kolekcji, takich jak ciągi JSON, tablice i obiekty.

### <a name="contains"></a>zawiera
Zwraca, `true` jeśli ciąg zawiera określony podciąg, tablica zawiera określoną wartość lub obiekt zawiera określony klucz.

#### <a name="example-1-string"></a>Przykład 1: ciąg znaków
Poniższy przykład `true`zwraca:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy, że `element1` zwraca `[1, 2, 3]`. Poniższy przykład `false`zwraca:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy, że `element1` zwroty:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład `true`zwraca:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Zwraca liczbę znaków w ciągu, liczbę wartości w tablicy lub liczbę kluczy w obiekcie.

#### <a name="example-1-string"></a>Przykład 1: ciąg znaków
Poniższy przykład `6`zwraca:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy, że `element1` zwraca `[1, 2, 3]`. Poniższy przykład `3`zwraca:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy, że `element1` zwroty:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład `2`zwraca:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>empty
Zwraca `true` wartość, jeśli ciąg, tablica lub obiekt ma wartość null lub jest pusta.

#### <a name="example-1-string"></a>Przykład 1: ciąg znaków
Poniższy przykład `true`zwraca:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy, że `element1` zwraca `[1, 2, 3]`. Poniższy przykład `false`zwraca:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy, że `element1` zwroty:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład `false`zwraca:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Przykład 4: null i undefined
Załóżmy, że `element1` jest `null` lub niezdefiniowane. Poniższy przykład `true`zwraca:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>Pierwszym
Zwraca pierwszy znak określonego ciągu; pierwszą wartość określonej tablicy; lub pierwszy klucz i wartość określonego obiektu.

#### <a name="example-1-string"></a>Przykład 1: ciąg znaków
Poniższy przykład `"f"`zwraca:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy, że `element1` zwraca `[1, 2, 3]`. Poniższy przykład `1`zwraca:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy, że `element1` zwroty:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Poniższy przykład `{"key1": "foobar"}`zwraca:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>ostatni
Zwraca ostatni znak określonego ciągu, ostatnią wartość określonej tablicy lub ostatni klucz i wartość określonego obiektu.

#### <a name="example-1-string"></a>Przykład 1: ciąg znaków
Poniższy przykład `"r"`zwraca:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy, że `element1` zwraca `[1, 2, 3]`. Poniższy przykład `2`zwraca:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy, że `element1` zwroty:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład `{"key2": "raboof"}`zwraca:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>wziąć
Zwraca określoną liczbę znaków ciągłych od początku ciągu, określoną liczbę ciągłych wartości od początku tablicy lub określoną liczbę ciągłych kluczy i wartości od początku obiektu.

#### <a name="example-1-string"></a>Przykład 1: ciąg znaków
Poniższy przykład `"foo"`zwraca:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy, że `element1` zwraca `[1, 2, 3]`. Poniższy przykład `[1, 2]`zwraca:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy, że `element1` zwroty:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład `{"key1": "foobar"}`zwraca:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>Pomiń
Pomija określoną liczbę elementów w kolekcji, a następnie zwraca pozostałe elementy.

#### <a name="example-1-string"></a>Przykład 1: ciąg znaków
Poniższy przykład `"bar"`zwraca:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Załóżmy, że `element1` zwraca `[1, 2, 3]`. Poniższy przykład `[3]`zwraca:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Załóżmy, że `element1` zwroty:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Poniższy przykład `{"key2": "raboof"}`zwraca:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Funkcje logiczne
Funkcje te mogą być używane w warunkach. Niektóre funkcje mogą nie obsługiwać wszystkich typów danych JSON.

### <a name="equals"></a>equals
Zwraca, `true` jeśli oba parametry mają ten sam typ i wartość. Ta funkcja obsługuje wszystkie typy danych JSON.

Poniższy przykład `true`zwraca:

```json
"[equals(0, 0)]"
```

Poniższy przykład `true`zwraca:

```json
"[equals('foo', 'foo')]"
```

Poniższy przykład `false`zwraca:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Zwraca, `true` jeśli pierwszy parametr jest ściśle mniejszy niż drugi parametr. Ta funkcja obsługuje tylko parametry numeru typu i ciągu.

Poniższy przykład `true`zwraca:

```json
"[less(1, 2)]"
```

Poniższy przykład `false`zwraca:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Zwraca, `true` jeśli pierwszy parametr jest mniejszy lub równy drugiemu parametrowi. Ta funkcja obsługuje tylko parametry numeru typu i ciągu.

Poniższy przykład `true`zwraca:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Zwraca, `true` jeśli pierwszy parametr jest ściśle większy niż drugi parametr. Ta funkcja obsługuje tylko parametry numeru typu i ciągu.

Poniższy przykład `false`zwraca:

```json
"[greater(1, 2)]"
```

Poniższy przykład `true`zwraca:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Zwraca, `true` jeśli pierwszy parametr jest większy lub równy drugiemu parametrowi. Ta funkcja obsługuje tylko parametry numeru typu i ciągu.

Poniższy przykład `true`zwraca:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>i
Zwraca, `true` jeśli wszystkie `true`parametry są obliczane na . Ta funkcja obsługuje tylko dwa lub więcej parametrów typu Boolean.

Poniższy przykład `true`zwraca:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Poniższy przykład `false`zwraca:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>lub
Zwraca `true` wartość, jeśli co najmniej `true`jeden z parametrów jest oceniany na . Ta funkcja obsługuje tylko dwa lub więcej parametrów typu Boolean.

Poniższy przykład `true`zwraca:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Poniższy przykład `true`zwraca:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Zwraca, `true` jeśli parametr `false`jest oceniany na . Ta funkcja obsługuje tylko parametry typu logicznego.

Poniższy przykład `true`zwraca:

```json
"[not(false)]"
```

Poniższy przykład `false`zwraca:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>Łączonej
Zwraca wartość pierwszego parametru innej niż null. Ta funkcja obsługuje wszystkie typy danych JSON.

Załóżmy `element1` i `element2` są niezdefiniowane. Poniższy przykład `"foobar"`zwraca:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Funkcje konwersji
Te funkcje mogą służyć do konwersji wartości między typami danych JSON i kodowania.

### <a name="int"></a>int
Konwertuje parametr na liczę całkowitą. Ta funkcja obsługuje parametry numeru typu i ciągu.

Poniższy przykład `1`zwraca:

```json
"[int('1')]"
```

Poniższy przykład `2`zwraca:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Konwertuje parametr na zmiennoprzecinkowy. Ta funkcja obsługuje parametry numeru typu i ciągu.

Poniższy przykład `1.0`zwraca:

```json
"[float('1.0')]"
```

Poniższy przykład `2.9`zwraca:

```json
"[float(2.9)]"
```

### <a name="string"></a>ciąg
Konwertuje parametr na ciąg. Ta funkcja obsługuje parametry wszystkich typów danych JSON.

Poniższy przykład `"1"`zwraca:

```json
"[string(1)]"
```

Poniższy przykład `"2.9"`zwraca:

```json
"[string(2.9)]"
```

Poniższy przykład `"[1,2,3]"`zwraca:

```json
"[string([1,2,3])]"
```

Poniższy przykład `"{"foo":"bar"}"`zwraca:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Konwertuje parametr na wartość logiczną. Ta funkcja obsługuje parametry numeru typu, ciągu i wartości logicznych. Podobnie jak w języku logicznym w `0` `'false'` języku `true`JavaScript, dowolna wartość z wyjątkiem lub zwraca .

Poniższy przykład `true`zwraca:

```json
"[bool(1)]"
```

Poniższy przykład `false`zwraca:

```json
"[bool(0)]"
```

Poniższy przykład `true`zwraca:

```json
"[bool(true)]"
```

Poniższy przykład `true`zwraca:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Konwertuje parametr na typ macierzysty. Innymi słowy, ta funkcja jest `string()`odwrotnością . Ta funkcja obsługuje tylko parametry ciągu typu.

Poniższy przykład `1`zwraca:

```json
"[parse('1')]"
```

Poniższy przykład `true`zwraca:

```json
"[parse('true')]"
```

Poniższy przykład `[1,2,3]`zwraca:

```json
"[parse('[1,2,3]')]"
```

Poniższy przykład `{"foo":"bar"}`zwraca:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Koduje parametr do ciągu zakodowanego base-64. Ta funkcja obsługuje tylko parametry ciągu typu.

Poniższy przykład `"Zm9vYmFy"`zwraca:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>dekodowanieBase64
Dekoduje parametr z ciągu zakodowanego base-64. Ta funkcja obsługuje tylko parametry ciągu typu.

Poniższy przykład `"foobar"`zwraca:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>Encodeuricomponent
Koduje parametr do ciągu zakodowanego w adresie URL. Ta funkcja obsługuje tylko parametry ciągu typu.

Poniższy przykład `"https%3A%2F%2Fportal.azure.com%2F"`zwraca:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>dekodowanieskładeksu
Dekoduje parametr z ciągu zakodowanego w adresie URL. Ta funkcja obsługuje tylko parametry ciągu typu.

Poniższy przykład `"https://portal.azure.com/"`zwraca:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Funkcje matematyczne
### <a name="add"></a>add
Dodaje dwie liczby i zwraca wynik.

Poniższy przykład `3`zwraca:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Sub
Odejmuje drugą liczbę od pierwszego numeru i zwraca wynik.

Poniższy przykład `1`zwraca:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>Mul
Mnoży dwie liczby i zwraca wynik.

Poniższy przykład `6`zwraca:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Dzieli pierwszą liczbę przez drugą liczbę i zwraca wynik. Wynik jest zawsze całkowitej liczby.

Poniższy przykład `2`zwraca:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>Mod
Dzieli pierwszą liczbę przez drugą liczbę i zwraca pozostałą część.

Poniższy przykład `0`zwraca:

```json
"[mod(6, 3)]"
```

Poniższy przykład `2`zwraca:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min
Zwraca małe z dwóch liczb.

Poniższy przykład `1`zwraca:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Zwraca większą z dwóch liczb.

Poniższy przykład `2`zwraca:

```json
"[max(1, 2)]"
```

### <a name="range"></a>range
Generuje sekwencję liczb całkowitych w określonym zakresie.

Poniższy przykład `[1,2,3]`zwraca:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Zwraca losową liczbę całkowitą w określonym zakresie. Ta funkcja nie generuje kryptograficznie bezpiecznych liczb losowych.

Poniższy przykład `42`może zwrócić:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>Podłogi
Zwraca największą liczbę całkowitą mniejszą lub równą określonej liczbie.

Poniższy przykład `3`zwraca:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>Ceil
Zwraca największą liczbę całkowitą większą lub równą określonej liczbie.

Poniższy przykład `4`zwraca:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Funkcje daty
### <a name="utcnow"></a>Utcnow
Zwraca ciąg w formacie ISO 8601 bieżącej daty i godziny na komputerze lokalnym.

Poniższy przykład `"1990-12-31T23:59:59.000Z"`może zwrócić:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>Addseconds
Dodaje całkowitą liczbę sekund do określonego sygnatury czasowej.

Poniższy przykład `"1991-01-01T00:00:00.000Z"`zwraca:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>Addminutes
Dodaje całkowitą liczbę minut do określonego sygnatury czasowej.

Poniższy przykład `"1991-01-01T00:00:59.000Z"`zwraca:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>Addhours
Dodaje całkowitą liczbę godzin do określonego sygnatury czasowej.

Poniższy przykład `"1991-01-01T00:59:59.000Z"`zwraca:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Następne kroki
* Aby zapoznać się z wprowadzeniem do usługi Azure Resource Manager, zobacz [omówienie usługi Azure Resource Manager](../management/overview.md).

