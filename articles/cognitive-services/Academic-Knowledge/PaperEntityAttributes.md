---
title: Atrybuty jednostki dokument — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można użyć z jednostką papieru w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 92844b5faf691b67617c9f3424a1322aa05429bb
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875739"
---
# <a name="paper-entity"></a>Jednostka papieru

<sub> * Poniżej atrybuty są specyficzne dla jednostki papieru. (Ty = "0") </sub>


Name (Nazwa)    |Opis                                        |Type       | Operacje
------- | ------------------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                                          |Int64      |Równa się
Oś      |Dokument tytułu                                        |String     |Równa się,<br/>StartsWith
L       |Kod języka dokument oddzielone "\@\@\@"          |String     |Równa się
Tak       |Rok papieru                                         |Int32      |Równa się,<br/>IsBetween
D       |Data papieru                                         |Date       |Równa się,<br/>IsBetween
DW      |Liczba cytatu                                     |Int32      |brak  
ECC     |Cytat szacowana liczba                           |Int32      |brak
AA. AuN  |Autor                                        |String     |Równa się,<br/>StartsWith
AA.AuId |Tworzenie Identyfikatora                                          |Int64      |Równa się
AA. AfN  |Nazwa przynależność do autora                            |String     |Równa się,<br/>StartsWith
AA.AfId |ID przynależność do autora                              |Int64      |Równa się
AA.S    |Kolejność Autor papieru                         |Int32      |Równa się
F.FN    |Pole o nazwie analiza                                |String     |Równa się,<br/>StartsWith
F.FId   |Pole Identyfikator badania                                  |Int64      |Równa się
J.JN    |Nazwa dziennika                                       |String     |Równa się,<br/>StartsWith
J.JId   |Identyfikator dziennika                                         |Int64      |Równa się
C.CN    |Nazwa serii konferencji                             |String     |Równa się,<br/>StartsWith
C.CId   |Identyfikator seria konferencji                               |Int64      |Równa się
Identyfikator rId     |Identyfikator przywoływanego dokumentów                              |Int64[]    |Równa się
W       |Wyrazy z tytułu dokument i abstrakcyjny                |Ciąg]   |Równa się
E       |Rozszerzone metadane (zobacz w poniższej tabeli)                |String     |brak  
        


## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
Nazwa wyróżniająca      | Nazwa wyświetlana papieru 
S       | Źródła — lista źródła papieru, posortowane według rangi statycznych w sieci web
S.Ty    | Typ źródła (1:HTML 2:Text, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S.U     | Źródłowy adres URL
VFN     | Pełna nazwa miejsca — Pełna nazwa dziennika lub konferencji
VSN     | Krótka nazwa właściwości - krótką nazwę dziennika lub konferencji
V       | Wolumin — wolumin dziennika
BV      | Nazwa dziennika
BT      | 
PB      | Skróty dziennika
I       | Problem — problem dziennika
FP      | FirstPage — pierwsza strona papieru
LP      | LastPage - ostatniej strony dokumentu
DOI     | Identyfikator cyfrowy obiektu
DW      | Konteksty cytat — lista odwołania dokument identyfikatory i odpowiadający mu kontekst w dokumencie (np. [{123: ["brązowy lisów znane umożliwiające przejście zgodnie z odwołaniem w dokumencie 123", "z opóźnieniem psy są misnomer historyczne, jak pokazano w dokumencie 123"]})
IA      | Odwrócony abstrakcyjny
IA.IndexLength| Liczba elementów w indeksie (liczba słów w abstrakcyjny)
IA.InvertedIndex| Listę słów abstrakcyjnej i ich odpowiedniej pozycji w oryginalnej abstrakcyjny (np. [{"": [0, 15, 30]}, {"brown": [1]}, {"fox":[2]}])
