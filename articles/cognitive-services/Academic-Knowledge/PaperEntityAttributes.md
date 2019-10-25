---
title: Atrybuty jednostki papieru — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Informacje o atrybutach, których można używać z jednostką papieru w Academic Knowledge API.
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c300a6477daa5759a68d5d11d40b1a71b46bd808
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793835"
---
# <a name="paper-entity"></a>Jednostka papieru

<sub>* Poniższe atrybuty są specyficzne dla jednostki papieru. (Ty = ' 0 ')</sub>

Nazwa | Opis | Typ | Operations
--- | --- | --- | ---
AA. AfId | Identyfikator przynależności autora | Int64 | Równa się
AA. AfN | Nazwa przynależności autora | Ciąg | Równa się, StartsWith
AA. AuId | Identyfikator autora | Int64 | Równa się
AA. AuN | Znormalizowana nazwa autora | Ciąg | Równa się, StartsWith
AA. DAuN | Oryginalna nazwa autora | Ciąg | Brak
AA. DAfN | Oryginalna nazwa przynależności | Ciąg | Brak
AA. Wolumin | Pozycja liczbowa na liście autora | Elementem | Równa się
CC | Liczba cytatów | Elementem | Brak  
C. CId | Identyfikator serii konferencji | Int64 | Równa się
C.CN | Nazwa serii konferencji | Ciąg | Równa się, StartsWith
D | Data publikacji w formacie RRRR-MM-DD | Data | Equals, isBetween
Adres | Rozszerzone metadane (patrz tabela poniżej) | Ciąg | ND  
ECC | Szacowana Liczba cytatów | Elementem | Brak
F. DFN | Oryginalne pole nazwy badania | Ciąg | Brak
F. FId | Pole identyfikatora badania | Int64 | Równa się
Język F. FN | Znormalizowane pole nazwy badania | Ciąg | Równa się, StartsWith
Identyfikator | Identyfikator papieru | Int64 | Równa się
J. JId | Identyfikator dziennika | Int64 | Równa się
J. JN | Nazwa dziennika | Ciąg | Równa się, StartsWith
Zmiennoprzecinkow | Typ publikacji (0: nieznany, 1: Artykuł dziennika, 2: patent, 3: papier konferencyjny, 4: rozdział książki, 5: książka, 6: wpis odwołania do książki, 7: DataSet, 8: repozytorium | Ciąg | Równa się
Objęte | Lista identyfikatorów papieru, do których istnieją odwołania | Int64 [] | Równa się
Zas | Znormalizowany tytuł | Ciąg | Równa się, StartsWith
W | Unikatowe słowa w tytule | Ciąg [] | Równa się
Tak | Rok publikacji | Elementem | Equals, isBetween

## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Nazwa | Opis               
--- | ---
BT | Typ dokumentu BibTex ("a": artykuł dotyczący arkusza "b": książka, "c": rozdział, "p": papier konferencyjny)
BV | Nazwa miejsca BibTex
CC | Konteksty cytatu — Lista identyfikatorów papieru, do których odwołuje się odwołanie i odpowiedni kontekst w papierze (np. [{123: ["brązowy Foxes są znane do przechodzenia zgodnie z opisem w dokumencie 123", "psy z opóźnieniem to historyczna Misnomer, jak pokazano w dokumencie 123"]})
WYRÓŻNIAJĄC | Oryginalny tytuł papieru
DOI | Identyfikator obiektu cyfrowego
PREFIKS | Pierwsza strona papieru w publikacji
I | Problem z publikacją
IA | Odwrócony abstrakcyjny
IA. IndexLength | Liczba elementów w indeksie (liczba wyrazów abstrakcyjnych)
IA. InvertedIndex | Lista wyrazów abstrakcyjnych i odpowiadających im pozycji w oryginalnym streszczeniu (np. [{": [0, 15, 30]}, {" brązowy ": [1]}, {" Fox ": [2]}])
LP | Ostatnia strona papieru w publikacji
PB | Publisher
S | Źródła — lista źródeł sieci Web papieru posortowana według rangi statycznej
S. ty | Typ źródła (1: HTML, 2: tekst, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS)
S. U | Źródłowy adres URL
V | Wolumin publikacji
VFN | Pełna nazwa miejsca w dzienniku lub konferencji
VSN | Krótka nazwa miejsca w dzienniku lub konferencji
