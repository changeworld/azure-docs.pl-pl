---
title: Składnia wyrażenia w Academic Knowledge API kwerendy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać składni wyrażenia zapytania w Academic Knowledge API kognitywnych usług firmy Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 6ec338fff09954e2f14066ce2b83bc1228794af8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346908"
---
# <a name="query-expression-syntax"></a>Składnia wyrażenia zapytania

Zaobserwowano, że odpowiedź na **interpretowania** żądanie zawiera wyrażenia zapytania. Gramatyki, która interpretowane zapytania użytkownika tworzone wyrażenia zapytania dla każdego interpretacji. Wyrażenia zapytania mogą być następnie używane do wystawienia **oceny** żądania pobrania jednostki wyników wyszukiwania.

Można również utworzyć własne wyrażenia zapytania i używać ich w **oceny** żądania. Może to być przydatne, jeśli tworzysz z własnego interfejsu użytkownika, który tworzy wyrażenia zapytania w odpowiedzi na działania użytkownika. Aby to zrobić, należy znać składni wyrażeń zapytania.  

Każdy atrybut jednostki, który może być uwzględniony w wyrażeniu zapytania ma typ danych oraz zestaw operatorów zapytań możliwe. Określono zestaw atrybutów jednostki i operatory obsługiwane dla każdego atrybutu w [atrybuty obiektu](EntityAttributes.md). Zapytanie jednowartościowych wymaga do obsługi atrybutu *jest równe* operacji. Zapytanie prefiks wymaga do obsługi atrybutu *StartsWith* operacji. Kwerendy zakresu numerycznego wymaga do obsługi atrybutu *IsBetween* operacji.

Niektóre dane jednostki są przechowywane jako atrybuty złożonego wskazywany przez pojedynczego znaku kropki "." w nazwie atrybutu. Na przykład informacji o przynależności/autora jest reprezentowany jako atrybut złożonego. Zawiera składniki 4: AuN, AuId, AfN, AfId. Te składniki są osobne fragmenty danych, które tworzą wartość atrybutu pojedynczej jednostki.


**Atrybut ciągu: Pojedyncza wartość** (w tym odpowiedników synonimy)  
Analizy czasowej = "indeksowania analizą semantycznego ukryty"  
Złożone (AA. AuN = "dochodzić dumais")

**Atrybut ciągu: Dokładnie jednej wartości** (zgodna tylko z wartościami kanonicznymi)  
Analizy czasowej == "indeksowania analizą semantycznego ukryty"  
Złożone (AA. AuN == "susan t dumais")
     
**Atrybut ciągu: Wartość prefiksu**   
Analizy czasowej = "indeksowania według ukryty seman"...  
Złożone (AA. AuN =... "dochodzić du")

**Liczbowa atrybutu: Pojedyncza wartość**  
Y = 2010
 
**Atrybut numeryczny: Wartość zakresu**  
Y &GT; 2005  
Y &GT; = 2005  
Y &LT; 2010  
Y &LT; = 2010  
Y =\[2010, 2012\) (zawiera wartość tylko lewej granicy: 2010, 2011)  
Y =\[2010, 2012\] (obejmuje zarówno wartości granicznych: 2010, 2011, 2012)
 
**Liczbowych atrybutu: Wartość prefiksu**  
Y = "19"... (wszystkie wartość liczbową będącą rozpoczyna się od 19) 
 
**Atrybutu daty: Pojedyncza wartość**  
D = "2010--02 04"

**Atrybut Data: Wartość zakresu**  
D &GT; "2010-02-03"  
D = ["2010-02-03", "2010-02-05"]

**I/lub zapytania:**  
I (Y = 1985, analizy czasowej = "systemami elektronicznych disordered")  
Lub (analizy czasowej = "elektronicznego systemami disordered", analizy czasowej = "odporność na uszkodzenia zasady i praktyki")  
And(OR(Y=1985,Y=2008), analizy czasowej = "systemami elektronicznych disordered")
 
**Złożone kwerendy:**  
Zapytania składników atrybutu złożonego należy dołączyć część wyrażenia zapytania, który odwołuje się do atrybutu złożonego w funkcji Composite(). 

Na przykład zapytanie dla dokumentów przez autora, należy użyć następującej kwerendy:
```
Composite(AA.AuN='mike smith')
```
<br>Aby sprawdzić dla dokumentów przez konkretnego autora, podczas autora w szczególności instytucji, użyj następującej kwerendy:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Funkcja Composite() wiąże ze sobą dwie części złożonego atrybutu. Oznacza to, że tylko uzyskujemy dokumenty gdy autorów jest "Jan Nowak" podczas był on w Harvard. 

Dla dokumentów przez konkretnego autora w przynależności z autorami (innych) z konkretnym instytucji kwerendy, użyj następującego zapytania:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>W tej wersji ponieważ Composite() jest stosowany do autora i przynależności indywidualnie przed And(), uzyskujemy wszystkie dokumenty, gdzie autorów jest "Jan Kowalski", a jeden z przynależności autorów "Harvard". To dźwięki, podobnie jak w poprzednim przykładzie kwerendy, ale nie jest to samo.

Ogólnie rzecz biorąc, rozważmy następujący przykład: mamy złożonego atrybutu C, która ma dwa składniki, A i B. Jednostka może mieć wiele wartości dla C. Oto nasze jednostek:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>Zapytanie 
```
Composite(And(C.A=1, C.B=2))
```

<br>jest zgodna tylko jednostki, które mają wartość dla języka C, gdy składnik C.A jest 1 i składnik C.B jest 2. Tylko E1 odpowiada to zapytanie.

Zapytanie 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>Dopasowuje jednostek, które będzie mieć wartość C, gdzie C.A to 1, a także ma wartości dla języka C gdzie C.B to 2. Zarówno E1 i E2 pasuje do tego zapytania.

Uwaga:  
- Nie można odwoływać się część złożonego atrybutu poza funkcją Composite().
- Nie można odwoływać się części dwóch różnych atrybutów złożonego w tej samej funkcji Composite().
- Nie można odwoływać się atrybut, który nie jest częścią złożonego atrybutu wewnątrz funkcji Composite().
