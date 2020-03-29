---
title: Praca z ciągami w kwerendach dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób edytowania, porównywania, wyszukiwania i wykonywania różnych innych operacji na ciągach w kwerendach dziennika usługi Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: a394fee7178b2e3e167c8bd905ab175b25d1d813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397470"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Praca z ciągami w kwerendach dziennika usługi Azure Monitor


> [!NOTE]
> Przed ukończeniem tego samouczka należy wykonać wprowadzenie [do usługi Azure Monitor Log Analytics](get-started-portal.md) i wprowadzenie do [zapytań dziennika usługi Azure Monitor.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano sposób edytowania, porównywania, wyszukiwania i wykonywania różnych innych operacji na ciągach.

Każdy znak w ciągu ma numer indeksu, zgodnie z jego lokalizacją. Pierwszy znak jest w indeksie 0, następny znak jest 1 i tak dalej. Różne funkcje ciągu używają numerów indeksów, jak pokazano w poniższych sekcjach. Wiele z poniższych przykładów używa polecenia **print,** aby zademonstrować manipulację ciągami bez użycia określonego źródła danych.


## <a name="strings-and-escaping-them"></a>Struny i ucieczka od nich
Wartości ciągów są zawijane za pomocą pojedynczych lub podwójnych znaków cudzysłowu. Ukośnik\\odwrotny ( ) służy do ucieczki znaków do znaku następującego po \" nim, takich jak \t dla karty, \n dla nowej linii i sam znak cudzysłowu.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Aby zapobiec\\" " działanie jako znak\@ucieczki, dodaj " " jako prefiks do ciągu:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Porównanie ciągów

Operator       |Opis                         |Rozróżniana wielkość liter|Przykład `true`(plony)
---------------|------------------------------------|--------------|-----------------------
`==`           |Równa się                              |Tak           |`"aBc" == "aBc"`
`!=`           |Nie równa się                          |Tak           |`"abc" != "ABC"`
`=~`           |Równa się                              |Nie            |`"abc" =~ "ABC"`
`!~`           |Nie równa się                          |Nie            |`"aBc" !~ "xyz"`
`has`          |Prawa strona jest całym terminem po lewej stronie |Nie|`"North America" has "america"`
`!has`         |Prawa strona nie jest pełnoprawnym terminem po lewej stronie       |Nie            |`"North America" !has "amer"` 
`has_cs`       |Prawa strona jest całym terminem po lewej stronie |Tak|`"North America" has_cs "America"`
`!has_cs`      |Prawa strona nie jest pełnoprawnym terminem po lewej stronie       |Tak            |`"North America" !has_cs "amer"` 
`hasprefix`    |Po prawej stronie jest terminem przedrostek po lewej stronie         |Nie            |`"North America" hasprefix "ame"`
`!hasprefix`   |Prawa strona nie jest przedrostkiem terminu po lewej stronie     |Nie            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Po prawej stronie jest terminem przedrostek po lewej stronie         |Tak            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Prawa strona nie jest przedrostkiem terminu po lewej stronie     |Tak            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Po prawej stronie jest terminem sufiks po lewej stronie         |Nie            |`"North America" hassuffix "ica"`
`!hassuffix`   |Prawa strona nie jest terminem sufiks po lewej stronie     |Nie            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Po prawej stronie jest terminem sufiks po lewej stronie         |Tak            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Prawa strona nie jest terminem sufiks po lewej stronie     |Tak            |`"North America" !hassuffix_cs "icA"`
`contains`     |Prawa strona występuje jako podsekwencja lewej strony  |Nie            |`"FabriKam" contains "BRik"`
`!contains`    |Prawa strona nie występuje po lewej stronie           |Nie            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Prawa strona występuje jako podsekwencja lewej strony  |Tak           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Prawa strona nie występuje po lewej stronie           |Tak           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Prawa strona jest początkową podsequence lewej strony|Nie            |`"Fabrikam" startswith "fab"`
`!startswith`  |Prawa strona nie jest początkową podsequence lewej strony|Nie        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Prawa strona jest początkową podsequence lewej strony|Tak            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Prawa strona nie jest początkową podsequence lewej strony|Tak        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Prawa strona jest zamykającym podsekwencją lewej strony|Nie             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Prawa strona nie jest zamykającą podsekwencją po lewej stronie|Nie         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Prawa strona jest zamykającym podsekwencją lewej strony|Tak             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Prawa strona nie jest zamykającą podsekwencją po lewej stronie|Tak         |`"Fabrikam" !endswith "brik"`
`matches regex`|po lewej stronie znajduje się dopasowanie do prawej strony        |Tak           |`"Fabrikam" matches regex "b.*k"`
`in`           |Równa się jednemu z elementów       |Tak           |`"abc" in ("123", "345", "abc")`
`!in`          |Nie jest równa żadnemu z elementów   |Tak           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>liczba

Zlicza wystąpienia podciągu w ciągu. Można dopasować zwykłych ciągów lub użyć wyrażenia regularnego. Dopasowania ciągów zwykłych mogą nakładać się, podczas gdy dopasowania wyrażenia regularnego nie.

### <a name="syntax"></a>Składnia
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumenty:
- `text`- Ciąg wejściowy 
- `search`- Zwykły ciąg lub wyrażenie regularne, aby dopasować tekst wewnętrzny.
- `kind` - _normalny_ | _regex_ (domyślnie: normalny).

### <a name="returns"></a>Zwraca

Ile razy ciąg wyszukiwania można dopasować w kontenerze. Dopasowania ciągów zwykłych mogą nakładać się, podczas gdy dopasowania wyrażenia regularnego nie.

### <a name="examples"></a>Przykłady

#### <a name="plain-string-matches"></a>Dopasowania ciągów zwykłych

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Regex spotkań

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>Wyodrębnić

Pobiera dopasowanie dla wyrażenia regularnego z danego ciągu. Opcjonalnie konwertuje również wyodrębnione podciąg na określony typ.

### <a name="syntax"></a>Składnia

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumenty

- `regex`- Wyrażenie regularne.
- `captureGroup`- Dodatnia stała całkowita wskazująca grupę przechwytywania do wyodrębnienia. 0 dla całego dopasowania, 1 dla wartości dopasowanej przez pierwszy '('nawias')' w wyrażeniu regularnym, 2 lub więcej dla kolejnych nawiasów.
- `text`- Ciąg do wyszukiwania.
- `typeLiteral`- Opcjonalny typ literał (na przykład typeof(long)). Jeśli pod warunkiem, wyodrębnione podciąg jest konwertowany do tego typu.

### <a name="returns"></a>Zwraca
Podciąg dopasowany do wskazanej grupy przechwytywania captureGroup, opcjonalnie konwertowane na typeLiteral.
Jeśli nie ma dopasowania lub konwersja typu kończy się niepowodzeniem, zwróć wartość null.

### <a name="examples"></a>Przykłady

Poniższy przykład wyodrębnia ostatni oktet *ComputerIP* z rekordu pulsu:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Poniższy przykład wyodrębnia ostatni oktet, rzuca go na *rzeczywisty* typ (numer) i oblicza następną wartość IP
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

W poniższym przykładzie ciąg *Trace* jest wyszukiwany pod kątem definicji "Czas trwania". Dopasowanie jest rzutowe na *rzeczywiste* i pomnożone przez stałą czasu (1 s), *która rzuca czas trwania, aby wpisać timespan*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>niedozrozumia, isnotempty, notempty

- *isempty* zwraca wartość true, jeśli argument jest pusty ciąg lub null (patrz również *isnull*).
- *isnotempty* zwraca wartość true, jeśli argument nie jest pustym ciągiem lub wartością null (patrz również *isnotnull*). alias: *notempty*.

### <a name="syntax"></a>Składnia

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Przykłady

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

Dzieli adres URL na jego części (protokół, host, port itp.) i zwraca obiekt słownika zawierający części jako ciągi.

### <a name="syntax"></a>Składnia

```
parseurl(urlstring)
```

### <a name="examples"></a>Przykłady

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Wynik będzie:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>Zastąp

Zastępuje wszystkie dopasowania wyrażenia regularnego innym ciągiem. 

### <a name="syntax"></a>Składnia

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumenty

- `regex`- Wyrażenie regularne do dopasowania przez. Może zawierać grupy przechwytywania w '('nawiasy')'.
- `rewrite`- Wymiana regex dla każdego meczu dokonane przez pasujące regex. Użyj \0, aby odwołać się do całego dopasowania, \1 dla pierwszej grupy przechwytywania, \2 i tak dalej dla kolejnych grup przechwytywania.
- `input_text`- Ciąg wejściowy do wyszukiwania w.

### <a name="returns"></a>Zwraca
Tekst po zastąpieniu wszystkich dopasowań regex z oceną przepisać. Dopasowania nie nakładają się na siebie.

### <a name="examples"></a>Przykłady

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Może mieć następujące wyniki:

Działanie                                        |Zastąpione
------------------------------------------------|----------------------------------------------------------
4663 - Podjęto próbę uzyskania dostępu do obiektu  |Identyfikator działania 4663: Podjęto próbę uzyskania dostępu do obiektu.


## <a name="split"></a>split

Dzieli dany ciąg zgodnie z określonym ogranicznikiem i zwraca tablicę wynikowych podciągów.

### <a name="syntax"></a>Składnia
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumenty:

- `source`- Ciąg do podziału zgodnie z określonym ogranicznikiem.
- `delimiter`- Ogranicznik, który będzie używany w celu podzielenia ciągu źródłowego.
- `requestedIndex`- Opcjonalny indeks oparty na wartości zero. Jeśli pod warunkiem, zwracana tablica ciąg będzie zawierać tylko ten element (jeśli istnieje).


### <a name="examples"></a>Przykłady

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat (strcat)

Łączy argumenty ciągu (obsługuje 1-16 argumentów).

### <a name="syntax"></a>Składnia
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Przykłady
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>Strlen

Zwraca długość ciągu.

### <a name="syntax"></a>Składnia
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Przykłady
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>Podciąg

Wyodrębnia podciąg z danego ciągu źródłowego, począwszy od określonego indeksu. Opcjonalnie można określić długość żądanego podciągu.

### <a name="syntax"></a>Składnia
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumenty:

- `source`- Ciąg źródłowy, z których zostanie pobrany podciąg.
- `startingIndex`- Pozycja znaku początkowego od zera żądanego podciągu.
- `length`- Opcjonalny parametr, który może służyć do określenia żądanej długości zwracanego podciągu.

### <a name="examples"></a>Przykłady
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Konwertuje dany ciąg na wszystkie małe lub wielkie litery.

### <a name="syntax"></a>Składnia
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Przykłady
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>Następne kroki
Kontynuuj zaawansowane samouczki:
* [Funkcje agregacji](aggregations.md)
* [Agregacje zaawansowane](advanced-aggregations.md)
* [Wykresy i diagramy](charts.md)
* [Praca z JSON i strukturami danych](json-data-structures.md)
* [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
* [Sprzężenia - analiza krzyżowa](joins.md)
