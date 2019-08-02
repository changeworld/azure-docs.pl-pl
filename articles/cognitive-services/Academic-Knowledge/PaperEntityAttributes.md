---
title: Atrybuty jednostki papieru — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Informacje o atrybutach, których można używać z jednostką papieru w Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 4b9431469a7925d26003ad9c34f6b401e5767f6d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704942"
---
# <a name="paper-entity"></a>Jednostka papieru

<sub>* Poniższe atrybuty są specyficzne dla jednostki papieru. (Ty = ' 0 ')</sub>


Name (Nazwa)    |Opis                                        |Type       | Operacje
------- | ------------------------------------------------- | --------- | ----------------------------
Id      |Identyfikator jednostki                                          |Int64      |Równa się
Zas      |Tytuł papieru                                        |String     |Ubiegł<br/>StartsWith
L       |Kod języka papieru oddzielony znakiem "\@"\@\@          |String     |Równa się
T       |Rok papieru                                         |Int32      |Ubiegł<br/>IsBetween
D       |Data papieru                                         |Date       |Ubiegł<br/>IsBetween
CC      |Liczba cytatów                                     |Int32      |brak  
ECC     |Szacowana Liczba cytatów                           |Int32      |brak
AA. AuN  |Autor                                        |String     |Ubiegł<br/>StartsWith
AA. AuId |Identyfikator autora                                          |Int64      |Równa się
AA. AfN  |Nazwa przynależności autora                            |Ciąg     |Ubiegł<br/>StartsWith
AA. AfId |Identyfikator przynależności autora                              |Int64      |Równa się
AA. WOLUMIN    |Zamówienie autora dla dokumentu                         |Int32      |Równa się
F.FN    |Pole nazwy badania                                |String     |Ubiegł<br/>StartsWith
F.FId   |Pole identyfikatora badania                                  |Int64      |Równa się
J.JN    |Nazwa dziennika                                       |String     |Ubiegł<br/>StartsWith
J.JId   |Identyfikator dziennika                                         |Int64      |Równa się
C.CN    |Nazwa serii konferencji                             |String     |Ubiegł<br/>StartsWith
C. CId   |Identyfikator serii konferencji                               |Int64      |Równa się
Objęte     |IDENTYFIKATORY dokumentów przywoływanych                              |Int64[]    |Równa się
Ś       |Słowa z tytułu papieru i streszczenia                |Ciąg []   |Równa się
E       |Rozszerzone metadane (patrz tabela poniżej)                |String     |brak  
        


## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
NAZWA WYRÓŻNIAJĄCA      | Nazwa wyświetlana papieru 
N       | Źródła — lista źródeł sieci Web papieru posortowana według rangi statycznej
S. ty    | Typ źródła (1: HTML, 2: tekst, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS)
S. U     | Źródłowy adres URL
VFN     | Pełna nazwa na miejscu — pełna nazwa dziennika lub konferencji
VSN     | Miejsce na krótką nazwę — krótka nazwa arkusza lub konferencji
V       | Wolumin w programie Journal
BV      | Nazwa dziennika
BT      | 
PB      | Skróty dziennika
I       | Problem — problem z arkuszem
FP      | FirstPage — pierwsza strona z papieru
LP      | LastPage — Ostatnia strona dokumentu
DOI     | Identyfikator obiektu cyfrowego
CC      | Konteksty cytatu — Lista identyfikatorów papieru, do których odwołuje się odwołanie i odpowiedni kontekst w papierze (np. [{123: ["brązowy Foxes są znane do przechodzenia zgodnie z opisem w dokumencie 123", "psy z opóźnieniem to historyczna Misnomer, jak pokazano w dokumencie 123"]})
IA      | Odwrócony abstrakcyjny
IA. IndexLength| Liczba elementów w indeksie (liczba wyrazów abstrakcyjnych)
IA.InvertedIndex| Lista wyrazów abstrakcyjnych i odpowiadających im pozycji w oryginalnym streszczeniu (np. [{": [0, 15, 30]}, {" brązowy ": [1]}, {" Fox ": [2]}])
