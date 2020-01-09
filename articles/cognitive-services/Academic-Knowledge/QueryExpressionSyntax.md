---
title: Składnia wyrażenia zapytania — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać składni wyrażeń zapytania w Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 4056ecba7ac80436952228da9e1b74dc7382448c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448949"
---
# <a name="query-expression-syntax"></a>Składnia wyrażenia zapytania

Zaobserwowano, że odpowiedź na żądanie **interpretera** zawiera wyrażenie zapytania. Gramatyka, która interpretuje zapytanie użytkownika, utworzyła wyrażenie zapytania dla każdej interpretacji. Za pomocą wyrażenia zapytania można wydać żądanie **oszacowania** , aby pobrać wyniki wyszukiwania jednostek.

Możesz również skonstruować własne wyrażenia zapytania i używać ich w żądaniu **szacowania** . Może to być przydatne, jeśli tworzysz własny interfejs użytkownika, który tworzy wyrażenie zapytania w odpowiedzi na akcje użytkownika. W tym celu należy znać składnię wyrażeń zapytań.  

Każdy atrybut jednostki, który może zostać uwzględniony w wyrażeniu zapytania, ma określony typ danych i zestaw możliwych operatorów zapytań. Zestaw atrybutów jednostki i obsługiwane operatory dla każdego atrybutu jest określony w [atrybutach jednostki](EntityAttributes.md). Zapytanie o pojedynczej wartości wymaga, aby atrybut obsługiwał operację *Equals* . Zapytanie prefiksu wymaga, aby atrybut obsługiwał operację *StartsWith* . Zapytania zakresu liczbowego wymagają atrybutu do obsługi operacji *isBetween* .

Niektóre dane jednostki są przechowywane jako atrybuty złożone, jak wskazano kropka "." w nazwie atrybutu. Na przykład informacje o autorze/przynależności są reprezentowane jako atrybut złożony. Zawiera 4 składniki: AuN, AuId, AfN, AfId. Te składniki to oddzielne fragmenty danych, które tworzą wartość atrybutu pojedynczej jednostki.

Uwaga: wszystkie wyrażenia zapytania muszą zawierać małe litery i bez znaków specjalnych.

**Atrybut String: Single Value** (zawiera dopasowania do synonimów)  
TI = "indeksowanie przez nieindeksowaną analizę semantyki"  
Złożona (AA. AuN = "pozwu Dumais")

**Atrybut String: dokładna pojedyncza wartość** (dopasowuje tylko wartości kanoniczne)  
TI = = "indeksowanie przez nieindeksowaną analizę semantyki"  
Złożona (AA. AuN = = "Susan t Dumais")
     
**Atrybut String: wartość prefiksu**   
TI = "indeksowanie przez ukryte SEMAN"...  
Złożona (AA. AuN = "pozwu du"...)

**Atrybut liczbowy: pojedyncza wartość**  
Y=2010
 
**Atrybut liczbowy: wartość zakresu**  
Y>2005  
Y>=2005  
Y<2010  
Y<=2010  
Y =\[2010, 2012\) (obejmuje tylko lewą wartość granicy: 2010, 2011)  
Y =\[2010, 2012\] (zawiera obie wartości graniczne: 2010, 2011, 2012)
 
**Atrybut liczbowy: wartość prefiksu**  
T = "19"... (wartość liczbowa rozpoczynająca się od 19) 
 
**Date — atrybut: Single Value**  
D='2010-02-04'

**Atrybut daty: wartość zakresu**  
D > "2010-02"  
D = ["2010-02-03", "2010-02-05"]

**I/lub zapytania:**  
I (Y = 1985, TI = "nieuporządkowane systemy elektroniczne")  
Lub (TI = "nieuporządkowane systemy elektroniczne", TI = "zasady odporności na uszkodzenia")  
And (lub (Y = 1985, Y = 2008), TI = "nieuporządkowane systemy elektroniczne")
 
**Zapytania złożone:**  
Aby wykonać zapytanie o składniki atrybutu złożonego, należy ująć część wyrażenia zapytania, która odwołuje się do atrybutu złożonego w funkcji złożonej (). 

Na przykład, aby wykonać zapytanie o dokumenty według nazwy autora, należy użyć następującego zapytania:
```
Composite(AA.AuN='mike smith')
```
<br>Aby wykonać zapytanie o dokumenty przez określonego autora, gdy autor był w danej instytucji, należy użyć następującego zapytania:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Funkcja złożona () łączy jednocześnie dwie części atrybutu złożonego. Oznacza to, że pobieramy tylko dokumenty, w których jeden z autorów nosi "Jan Kowalski", gdy był on w Harvard. 

Aby wykonać zapytanie o dokumenty przez określonego autora w przynależności do (innych) autorów z określonej instytucji, należy użyć następującego zapytania:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>W tej wersji, ponieważ złożony () jest stosowany do autora i przynależności indywidualnie przed i (), otrzymamy wszystkie dokumenty, w których jeden z autorów ma wartość "Jan Kowalski" i jeden z przynależności autorów jest "Harvard". Ten dźwięk jest podobny do poprzedniego przykładu zapytania, ale nie jest to samo.

Ogólnie rzecz biorąc, rozważmy poniższy przykład: mamy atrybut złożony C, który ma dwa składniki A i B. Jednostka może mieć wiele wartości dla języka C. Oto nasze jednostki:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>Zapytanie 
```
Composite(And(C.A=1, C.B=2))
```

<br>Dopasowuje tylko jednostki, które mają wartość dla C, gdzie składnik C. A jest 1, a składnik C. B wynosi 2. Tylko E1 pasuje do tego zapytania.

Zapytanie 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>dopasowuje jednostki, które mają wartość C, gdzie C. A jest 1 i ma również wartość C, gdzie C. B wynosi 2. Oba te zapytania są zgodne z E1 i E2.

Uwaga:  
- Nie można odwołać się do części złożonego atrybutu poza funkcją złożoną ().
- Nie można odwoływać się do części dwóch różnych atrybutów złożonych wewnątrz tej samej funkcji złożonej ().
- Nie można odwołać się do atrybutu, który nie jest częścią złożonego atrybutu wewnątrz funkcji złożonej ().
