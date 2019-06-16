---
title: Składnia wyrażeń — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać składni wyrażeń zapytania w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 95c2e9d3f54f967b3ebb434c742f69251b80573e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61336760"
---
# <a name="query-expression-syntax"></a>Składnia wyrażenia zapytania

Zaobserwowaliśmy, że odpowiedź na **interpretacji** żądanie zawiera wyrażenie zapytania. Gramatyki, który interpretowany kwerendy użytkownika utworzony w wyrażeniu kwerendy dla każdego interpretacji. Następnie można użyć wyrażenia zapytania do wystawienia **oceny** żądanie pobrania wyników wyszukiwania jednostek.

Można również utworzyć własne wyrażenia zapytania i używać ich w **oceny** żądania. Może to być przydatne, jeśli tworzysz własny interfejs użytkownika, co powoduje utworzenie w wyrażeniu kwerendy w odpowiedzi na działania użytkownika. Aby to zrobić, musisz wiedzieć składni wyrażeń zapytania.  

Każdy atrybut jednostki, które mogą być zawarte w wyrażeniu zapytania ma na określony typ danych i zestaw operatorów, to możliwe. Określono zestaw atrybutów jednostki i obsługiwane operatory dla każdego atrybutu w [atrybutów jednostki](EntityAttributes.md). Zapytanie jednowartościowych wymaga, aby atrybut do obsługi *jest równa* operacji. Zapytanie prefiks wymaga, aby atrybut do obsługi *StartsWith* operacji. Kwerendy zakresu liczbowego wymaga, aby atrybut do obsługi *IsBetween* operacji.

Niektóre dane jednostki są przechowywane jako atrybuty złożonego, wskazane przez pojedynczego znaku kropki "." w nazwie atrybutu. Na przykład informacji o przynależności/autor jest reprezentowany jako atrybut złożonego. Zawiera 4 składniki: AuN, AuId, AfN, AfId. Te składniki są oddzielne fragmenty danych, które stanowią wartość atrybutu pojedynczej jednostki.


**Atrybut ciągu: Pojedyncza wartość** (w tym odpowiedników synonimy)  
Oś = "indeksowania przez ukrytego analizy semantycznej"  
Złożone (AA. AuN = "dochodzić dumais")

**Atrybut ciągu: Dokładnie jedną wartość** (jest zgodna tylko z wartościami kanonicznymi)  
Oś == "indeksowania przez ukrytego analizy semantycznej"  
Złożone (AA. AuN == "dumais susan t")
     
**Atrybut ciągu: Wartość prefiksu**   
Oś = "indeksowania przez ukrytego seman"...  
Złożone (AA. AuN =... "dochodzić jednostka bazy danych")

**Atrybut liczbowego: Pojedyncza wartość**  
Y=2010
 
**Atrybut liczbowego: Wartość zakresu**  
Y>2005  
Y>=2005  
Y<2010  
Y<=2010  
Y =\[2010, 2012\) (zawiera tylko lewej granicy, wartość: 2010, 2011)  
Y =\[2010, 2012\] (obejmuje zarówno wartości granic: 2010, 2011, 2012)
 
**Atrybut liczbowego: Wartość prefiksu**  
Y = "19"... (wszystkie wartość liczbową rozpoczyna się od 19) 
 
**Atrybut Data: Pojedyncza wartość**  
D='2010-02-04'

**Atrybut Data: Wartość zakresu**  
D>'2010-02-03'  
D = ["2010-02-03", "2010-02-05"]

**I/lub zapytania:**  
I (Y = 1985, limit = "disordered elektroniczne systemy")  
Lub (Ti = "elektroniczne systemy disordered", limit = "odporności na uszkodzenia zasady i praktyki")  
And(OR(Y=1985,Y=2008), limit = "disordered elektroniczne systemy")
 
**Złożone kwerendy:**  
Zapytania składników atrybutu złożonego należy ująć części wyrażenie zapytania, które odwołuje się do atrybutu złożonego w funkcji Composite(). 

Na przykład aby wysłać zapytanie dla dokumentów, imię i nazwisko autora, użyj następującego zapytania:
```
Composite(AA.AuN='mike smith')
```
<br>Aby wysłać zapytanie dla dokumentów przez określonego autora podczas autora w szczególności instytucji, użyj następującego zapytania:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Funkcja Composite() łączy dwie części atrybut złożonego ze sobą. Oznacza to, że tylko uzyskujemy dokumentów gdzie jest on autorzy "Jan Kowalski" podczas zajmował w Harvard. 

Aby wysłać zapytanie dla dokumentów przez określonego autora w przynależności z autorami, (inne) z określoną instytucji, użyj następującego zapytania:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>W tej wersji ponieważ Composite() została zastosowana do autora i przynależność do indywidualnie przed And(), uzyskujemy wszystkich dokumentów, gdzie jest jeden z autorzy "Jan Kowalski" i jest jeden z przynależności autorów "Harvard". Ta funkcja sprawa podobny do poprzedniego przykładu zapytania, ale nie jest tak samo.

Ogólnie rzecz biorąc Rozważmy następujący przykład: Mamy atrybut złożony języka C, który ma dwa składniki, A i B. Jednostka może mieć wiele wartości dla języka C. Oto nasze jednostki:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>Zapytanie 
```
Composite(And(C.A=1, C.B=2))
```

<br>Dopasowuje tylko jednostek, które mają wartość dla języka C, gdzie składnika C.A wynosi 1, a składnik C.B to 2. Tylko E1 pasuje do tego zapytania.

Zapytanie 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>pasuje do jednostek, które mają wartość dla języka C, w którym C.A wynosi 1, a także ma wartości dla języka C gdzie C.B wynosi 2. E1 i E2 pasujących do tego zapytania.

Uwaga:  
- Nie można odwoływać się częścią złożonego atrybutu poza funkcją Composite().
- Nie można odwoływać się części dwóch różnych atrybutów złożonego w tej samej funkcji Composite().
- Nie można odwoływać się atrybut, który nie jest częścią złożonego atrybutu wewnątrz funkcji Composite().
