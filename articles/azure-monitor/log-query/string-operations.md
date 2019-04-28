---
title: Praca z ciągami zapytań dzienników usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Opisuje sposób edytowania, porównaj, wyszukiwanie i wykonywanie wielu innych operacji na ciągi w zapytaniach dzienników usługi Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 4b2763629a3036551cb3d362e609c72737436f4a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424707"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Praca z ciągami zapytań dzienników usługi Azure Monitor


> [!NOTE]
> Należy wykonać [Rozpoczynanie pracy z usługą Azure Monitor Log Analytics](get-started-portal.md) i [wprowadzenie do usługi Azure Monitor dziennika zapytań](get-started-queries.md) przed ukończenie tego samouczka.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano jak edytowanie, porównaj, wyszukiwanie i wykonywanie wielu innych operacji na ciągi.

Każdy znak w ciągu ma numer indeksu, zgodnie z jego lokalizacją. Pierwszy znak ma pod indeksem 0, następny znak jest 1, a więc jedno. Funkcje ciągów różnych Użyj numery indeksu, jak pokazano w poniższych sekcjach. Wielu z poniższych przykładów **drukowanie** polecenia dla pokazują manipulowanie ciągami, bez korzystania z określonego źródła danych.


## <a name="strings-and-escaping-them"></a>Ciągi i ich anulowania zapewnianego element
Wartości ciągu są ujęte w nawiasy przy użyciu znaków cudzysłowu pojedynczym lub podwójnym. Ukośnik odwrotny (\) służy do znaki ucieczki znak następujący przykład \t dla karty, \n dla nowego wiersza, i \" sam znak cudzysłowu.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

Aby zapobiec "\\"z działającym jako znak ucieczki, Dodaj"\@" jako prefiksu parametry:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Porównywanie ciągów

Operator       |Opis                         |Case-Sensitive|Przykład (daje `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Równa się                              |Yes           |`"aBc" == "aBc"`
`!=`           |Nierówne                          |Yes           |`"abc" != "ABC"`
`=~`           |Równa się                              |Nie            |`"abc" =~ "ABC"`
`!~`           |Nierówne                          |Nie            |`"aBc" !~ "xyz"`
`has`          |Po prawej stronie jest cały termin w po lewej stronie |Nie|`"North America" has "america"`
`!has`         |Po prawej stronie nie jest pełną termin w po lewej stronie       |Nie            |`"North America" !has "amer"` 
`has_cs`       |Po prawej stronie jest cały termin w po lewej stronie |Yes|`"North America" has_cs "America"`
`!has_cs`      |Po prawej stronie nie jest pełną termin w po lewej stronie       |Yes            |`"North America" !has_cs "amer"` 
`hasprefix`    |Po prawej stronie jest określenie prefiksu po lewej stronie         |Nie            |`"North America" hasprefix "ame"`
`!hasprefix`   |Po prawej stronie nie jest określenie prefiksu po lewej stronie     |Nie            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Po prawej stronie jest określenie prefiksu po lewej stronie         |Yes            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Po prawej stronie nie jest określenie prefiksu po lewej stronie     |Yes            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Po prawej stronie jest sufiksem termin w polu po lewej stronie         |Nie            |`"North America" hassuffix "ica"`
`!hassuffix`   |Po prawej stronie nie ma sufiks termin w po lewej stronie     |Nie            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Po prawej stronie jest sufiksem termin w polu po lewej stronie         |Yes            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Po prawej stronie nie ma sufiks termin w po lewej stronie     |Yes            |`"North America" !hassuffix_cs "icA"`
`contains`     |Występuje po prawej stronie podsekwencji po lewej stronie  |Nie            |`"FabriKam" contains "BRik"`
`!contains`    |Po prawej stronie nie występuje w po lewej stronie           |Nie            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Występuje po prawej stronie podsekwencji po lewej stronie  |Yes           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Po prawej stronie nie występuje w po lewej stronie           |Yes           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Po prawej stronie jest początkowa podsekwencję po lewej stronie|Nie            |`"Fabrikam" startswith "fab"`
`!startswith`  |Po prawej stronie nie jest początkowa podsekwencję po lewej stronie|Nie        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Po prawej stronie jest początkowa podsekwencję po lewej stronie|Yes            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Po prawej stronie nie jest początkowa podsekwencję po lewej stronie|Yes        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Po prawej stronie jest podsekwencji zamknięcia po lewej stronie|Nie             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Po prawej stronie nie jest podsekwencji zamknięcia po lewej stronie|Nie         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Po prawej stronie jest podsekwencji zamknięcia po lewej stronie|Yes             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Po prawej stronie nie jest podsekwencji zamknięcia po lewej stronie|Yes         |`"Fabrikam" !endswith "brik"`
`matches regex`|po lewej stronie zawiera dopasowanie po prawej stronie        |Yes           |`"Fabrikam" matches regex "b.*k"`
`in`           |Równa się do jednego z elementów       |Yes           |`"abc" in ("123", "345", "abc")`
`!in`          |Nie równa się do dowolnych elementów   |Yes           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Zlicza wystąpienia podciągu w ciągu. Można dopasować zwykłe ciągi, lub użyj wyrażenia regularnego. Dopasowuje ciąg zwykły nakładają się, podczas gdy nie pasuje do wyrażenia regularnego.

### <a name="syntax"></a>Składnia
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumenty:
- `text` -Ciągu wejściowego 
- `search` -Zwykły ciąg lub wyrażenie regularne pasowało wewnątrz tekstu.
- `kind` - _normalne_ | _wyrażenia regularnego_ (domyślne: normalny).

### <a name="returns"></a>Zwraca

Liczba przypadków, które można dopasować ciągu wyszukiwania w kontenerze. Zwykły ciąg znaków dopasowania mogą się nakładać podczas, gdy nie jest zgodne z wyrażeniem regularnym.

### <a name="examples"></a>Przykłady

#### <a name="plain-string-matches"></a>Zwykły ciąg znaków dopasowania

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Pasuje do wyrażenia regularnego

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>Wyodrębnij

Pobiera pasuje do wyrażenia regularnego z ciągu. Opcjonalnie również konwertuje wyodrębnione podciąg określonego typu.

### <a name="syntax"></a>Składnia

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumenty

- `regex` -Wyrażenie regularne.
- `captureGroup` -Jest dodatnia stała całkowita wskazująca grupy przechwytywania, aby wyodrębnić. 0 dla całego dopasowania 1 dla wartości dopasowane przez pierwszy "(" nawiasu")" w wyrażeniu regularnym, 2 lub więcej kolejnych nawiasów.
- `text` -Ciąg do wyszukania.
- `typeLiteral` — Literał opcjonalnie wpisz (na przykład typeof(long)). Jeśli nie dostarczono, wyodrębnionego ciągu jest konwertowana do tego typu.

### <a name="returns"></a>Zwraca
Podciąg dopasowywane captureGroup grupy przechwytywania wskazane, opcjonalnie przekonwertowane na typeLiteral.
Jeśli nie zostanie odnaleziony odpowiednik lub konwersja typu nie powiedzie się, zwraca wartość null.

### <a name="examples"></a>Przykłady

Poniższy przykład wyodrębnia ostatni oktet *ComputerIP* z rekordu pulsu:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Poniższy przykład wyodrębnia ostatni oktet, rzutuje na *rzeczywistych* wpisz (number), a następnie oblicza wartość dalej IP
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

W przykładzie poniżej ciąg *śledzenia* jest wyszukiwana w definicji "Czasu trwania". Dopasowanie jest rzutowany na *rzeczywistych* i pomnożona przez stałą czasu (1 s) *który rzutuje czasu trwania typu timespan*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>IsEmpty isnotempty, notempty

- *IsEmpty* zwraca wartość true, jeśli argument jest ciągiem pustym ani mieć wartości null (Zobacz też *isnull*).
- *isnotempty* zwraca wartość true, jeśli argument nie jest ciągiem pustym ani wartością null (Zobacz też *isnotnull*). alias: *notempty*.

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

Dzieli adresu URL na części (protokół, host, port itp.) i zwraca obiekt słownika zawierający części jako ciągi.

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

Zamienia wszystkie dopasowania wyrażenia regularnego innego ciągu. 

### <a name="syntax"></a>Składnia

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumenty

- `regex` -Wyrażenie regularne do dopasowania przez. Może on zawierać grupy przechwytywania w "("nawiasów")".
- `rewrite` Zastąpienia aparat wyrażeń regularnych dla jakiegokolwiek dopasowania przez dopasowania wyrażenia regularnego. Użyj \0 do odwoływania się do całego dopasowania, \1 dla pierwszej grupy przechwytywania, \2 i tak dalej dla grupy kolejnych przechwytywania.
- `input_text` -Wejściowy ciąg do przeszukania.

### <a name="returns"></a>Zwraca
Tekst, który po zastąpieniu wszystkie dopasowania wyrażenia regularnego ocen ponownego zapisywania. Dopasowuje się nie nakładały.

### <a name="examples"></a>Przykłady

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Może mieć następujące wyniki:

Działanie                                        |zastąpione
------------------------------------------------|----------------------------------------------------------
4663 - próbowano uzyskać dostęp do obiektu  |Identyfikator działania 4663: Nastąpiła próba dostępu do obiektu.


## <a name="split"></a>split

Dzieli ciąg danego zgodnie z określonego ogranicznika i zwraca tablicę wynikowymi podciągami.

### <a name="syntax"></a>Składnia
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumenty:

- `source` -Ciągu do podzielenia określonego ogranicznika.
- `delimiter` Ogranicznik, który będzie używany w celu podzielić ciąg źródłowy.
- `requestedIndex` -Opcjonalny indeks zaczynający się od zera. Jeśli nie dostarczono, tablicy zwracanego ciągu będą przechowywane tylko tego elementu (jeśli istnieje).


### <a name="examples"></a>Przykłady

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat —

Łączy argumenty typu string (1 – 16 obsługuje argumenty).

### <a name="syntax"></a>Składnia
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Przykłady
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Zwraca długość ciągu.

### <a name="syntax"></a>Składnia
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Przykłady
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>podciąg

Zwraca podciąg ciągu danego źródła, rozpoczynając od określonego indeksu. Opcjonalnie można określić długość podciągu żądanej.

### <a name="syntax"></a>Składnia
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumenty:

- `source` -Źródło ciąg podciąg zostaną pobrane z.
- `startingIndex` -Liczony od zera znaku pozycja początkowa żądanego podciągu.
- `length` -Opcjonalnym parametrem, który może służyć do określania Żądana długość zwracany ciąg podrzędny.

### <a name="examples"></a>Przykłady
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Konwertuje dany ciąg wszystkie małe i wielkie litery.

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



## <a name="next-steps"></a>Kolejne kroki
Kontynuuj samouczki zaawansowane:
* [Funkcje agregacji](aggregations.md)
* [Zaawansowane agregacji](advanced-aggregations.md)
* [Wykresy i diagramy](charts.md)
* [Praca z formatu JSON i struktur danych](json-data-structures.md)
* [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
* [Dołącza - krzyżowe analizy](joins.md)
