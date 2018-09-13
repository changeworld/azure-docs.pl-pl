---
title: Papier atrybutów jednostki w interfejsu Academic Knowledge API | Dokumentacja firmy Microsoft
description: Dowiedz się, atrybuty, które można użyć z jednostką papieru w interfejsu Academic Knowledge API w usługach Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 75efba2c2ce8842f233f766876ca00844338fb25
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35624007"
---
# <a name="paper-entity"></a>Jednostka papieru

<sub> * Poniżej atrybuty są specyficzne dla jednostki papieru. (Ty = "0") </sub>


Name (Nazwa)    |Opis                                        |Typ       | Operacje
------- | ------------------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                                          |Int64      |Równa się
Oś      |Dokument tytułu                                        |Ciąg     |Równa się,<br/>StartsWith
L       |Dokument języka kodu oddzielone "\@@@"            |Ciąg     |Równa się
Tak       |Rok papieru                                         |Int32      |Równa się,<br/>IsBetween
D       |Data papieru                                         |Date       |Równa się,<br/>IsBetween
DW      |Liczba cytatu                                     |Int32      |brak  
ECC     |Cytat szacowana liczba                           |Int32      |brak
AA. AuN  |Autor                                        |Ciąg     |Równa się,<br/>StartsWith
AA. AuId |Tworzenie Identyfikatora                                          |Int64      |Równa się
AA. AfN  |Nazwa przynależność do autora                            |Ciąg     |Równa się,<br/>StartsWith
AA. AfId |ID przynależność do autora                              |Int64      |Równa się
AA. S    |Kolejność Autor papieru                         |Int32      |Równa się
F.FN    |Pole o nazwie analiza                                |Ciąg     |Równa się,<br/>StartsWith
F.FId   |Pole Identyfikator badania                                  |Int64      |Równa się
J.JN    |Nazwa dziennika                                       |Ciąg     |Równa się,<br/>StartsWith
J.JId   |Identyfikator dziennika                                         |Int64      |Równa się
C.CN    |Nazwa serii konferencji                             |Ciąg     |Równa się,<br/>StartsWith
C.CId   |Identyfikator seria konferencji                               |Int64      |Równa się
Identyfikator rId     |Identyfikator przywoływanego dokumentów                              |Int64]    |Równa się
W       |Wyrazy z tytułu dokument i abstrakcyjny                |Ciąg]   |Równa się
E       |Rozszerzone metadane (zobacz w poniższej tabeli)                |Ciąg     |brak  
        


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
PB      | Abreviations dziennika
I       | Problem — problem dziennika
FP      | FirstPage — pierwsza strona papieru
LP      | LastPage - ostatniej strony dokumentu
DOI     | Identyfikator cyfrowy obiektu
DW      | Konteksty cytat — lista odwołania dokument identyfikatory i odpowiadający mu kontekst w dokumencie (np. [{123: ["brązowy lisów znane umożliwiające przejście zgodnie z odwołaniem w dokumencie 123", "z opóźnieniem psy są misnomer historyczne, jak pokazano w dokumencie 123"]})
IA      | Odwrócony abstrakcyjny
IA. IndexLength| Liczba elementów w indeksie (liczba słów w abstrakcyjny)
IA. InvertedIndex| Listę słów abstrakcyjnej i ich odpowiedniej pozycji w oryginalnej abstrakcyjny (np. [{"": [0, 15, 30]}, {"brown": [1]}, {"fox":[2]}])
