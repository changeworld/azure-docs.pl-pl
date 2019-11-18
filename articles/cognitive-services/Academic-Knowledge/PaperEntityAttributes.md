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
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123083"
---
# <a name="paper-entity"></a>Jednostka papieru

> [!NOTE]
> Poniższe atrybuty są specyficzne dla jednostki papieru. (Ty = ' 0 ')

Nazwa | Opis | Typ | Operacje
--- | --- | --- | ---
AA. AfId | Identyfikator przynależności autora | Int64 | Równa się
AA. AfN | Nazwa przynależności autora | Ciąg | Równa się, StartsWith
AA. AuId | Identyfikator autora | Int64 | Równa się
AA. AuN | Znormalizowana nazwa autora | Ciąg | Równa się, StartsWith
AA. DAuN | Oryginalna nazwa autora | Ciąg | Brak
AA. DAfN | Oryginalna nazwa przynależności | Ciąg | Brak
AA. Wolumin | Pozycja liczbowa na liście autora | Int32 | Równa się
BT | Typ dokumentu BibTex ("a": artykuł dotyczący arkusza "b": książka, "c": rozdział, "p": papier konferencyjny) | Ciąg | Brak
BV | Nazwa miejsca BibTex | Ciąg | Brak
C. CId | Identyfikator serii konferencji | Int64 | Równa się
C.CN | Nazwa serii konferencji | Ciąg | Równa się, StartsWith
CC | Liczba cytatów | Int32 | Brak  
CitCon | Konteksty cytatu</br></br>Lista identyfikatorów papieru, do których się odwołuje, i odpowiadający jej kontekst w papierze (np. [{123: ["brązowy Foxes są znane do przechodzenia, jak pokazano w papierze 123", "psy z opóźnieniem to historyczna Misnomer, jak przedstawiono w dokumencie 123"]}) | Niestandardowy | Brak
D | Data publikacji w formacie RRRR-MM-DD | Date | Equals, isBetween
NAZWA WYRÓŻNIAJĄCA | Oryginalny tytuł papieru | Ciąg | Brak
DOI | Identyfikator obiektu cyfrowego | Ciąg | Równa się, StartsWith
E | Rozszerzone metadane</br></br>**Ważne**: ten atrybut jest przestarzały i jest obsługiwany tylko w przypadku starszych aplikacji. Żądanie tego atrybutu pojedynczo (tj. Attributes = ID, TI, E) spowoduje zwrócenie wszystkich atrybutów rozszerzonych metadanych w *serializowanym ciągu JSON*</br></br>Wszystkie atrybuty zawarte w rozszerzonych metadanych są teraz dostępne jako atrybut najwyższego poziomu i mogą być żądane jako takie (tj. atrybuty = ID, TI, DOI, IA) | [Wydłuż](#extended) | Brak
ECC | Szacowana Liczba cytatów | Int32 | Brak
F. DFN | Oryginalne pole nazwy badania | Ciąg | Brak
F.FId | Pole identyfikatora badania | Int64 | Równa się
F.FN | Znormalizowane pole nazwy badania | Ciąg | Równa się, StartsWith
FP | Pierwsza strona papieru w publikacji | Ciąg | Równa się
I | Problem z publikacją | Ciąg | Równa się
IA | Odwrócony abstrakcyjny | [InvertedAbstract](#invertedabstract) | Brak
Identyfikator | Identyfikator papieru | Int64 | Równa się
J.JId | Identyfikator dziennika | Int64 | Równa się
J.JN | Nazwa dziennika | Ciąg | Równa się, StartsWith
LP | Ostatnia strona papieru w publikacji | Ciąg | Równa się
PB | Wydawca | Ciąg | Brak
Zmiennoprzecinkow | Typ publikacji (0: nieznany, 1: Artykuł dziennika, 2: patent, 3: papier konferencyjny, 4: rozdział książki, 5: książka, 6: wpis odwołania do książki, 7: DataSet, 8: repozytorium | Ciąg | Równa się
Objęte | Lista identyfikatorów papieru, do których istnieją odwołania | Int64[] | Równa się
S | Lista źródłowych adresów URL papieru, posortowana według istotności | [Źródło](#source)[] | Brak
Zas | Znormalizowany tytuł | Ciąg | Równa się, StartsWith
V | Wolumin publikacji | Ciąg | Równa się
VFN | Pełna nazwa miejsca w dzienniku lub konferencji | Ciąg | Brak
VSN | Krótka nazwa miejsca w dzienniku lub konferencji | Ciąg | Brak
W | Unikatowe słowa w tytule | Ciąg [] | Równa się
Tak | Rok publikacji | Int32 | Equals, isBetween

## <a name="extended"></a>Wydłuż
> [!IMPORTANT]
> Ten atrybut jest przestarzały i jest obsługiwany tylko w przypadku starszych aplikacji. Żądanie tego atrybutu pojedynczo (tj. Attributes = ID, TI, E) spowoduje zwrócenie wszystkich atrybutów rozszerzonych metadanych w *serializowanym ciągu JSON*</br></br>Wszystkie atrybuty zawarte w rozszerzonych metadanych są teraz dostępne jako atrybut najwyższego poziomu i mogą być żądane jako takie (tj. atrybuty = ID, TI, DOI, IA)

> [!IMPORTANT]
> Obsługa żądania pojedynczych atrybutów rozszerzonych przy użyciu "E" zakres, tj. "E. DN", jest przestarzały. Mimo że jest to technicznie obsługiwane, żądanie pojedynczych atrybutów rozszerzonych przy użyciu "E". zakres spowoduje zwrócenie wartości atrybutu w dwóch miejscach w odpowiedzi JSON jako część obiektu "E" i jako atrybut najwyższego poziomu.

Nazwa | Opis | Typ | Operacje
--- | --- | --- | ---
BT | Typ dokumentu BibTex ("a": artykuł dotyczący arkusza "b": książka, "c": rozdział, "p": papier konferencyjny) | Ciąg | Brak
BV | Nazwa miejsca BibTex | Ciąg | Brak
CC | Konteksty cytatu</br></br>Lista identyfikatorów papieru, do których się odwołuje, i odpowiadający jej kontekst w papierze (np. [{123: ["brązowy Foxes są znane do przechodzenia, jak pokazano w papierze 123", "psy z opóźnieniem to historyczna Misnomer, jak przedstawiono w dokumencie 123"]}) | Niestandardowy | Brak
NAZWA WYRÓŻNIAJĄCA | Oryginalny tytuł papieru | Ciąg | Brak
DOI | Identyfikator obiektu cyfrowego | Ciąg | Brak
FP | Pierwsza strona papieru w publikacji | Ciąg | Brak
I | Problem z publikacją | Ciąg | Brak
IA | Odwrócony abstrakcyjny | [InvertedAbstract](#invertedabstract) | Brak
LP | Ostatnia strona papieru w publikacji | Ciąg | Brak
PB | Wydawca | Ciąg | Brak
S | Lista źródłowych adresów URL papieru, posortowana według istotności | [Źródło](#source)[] | Brak
V | Wolumin publikacji | Ciąg | Brak
VFN | Pełna nazwa miejsca w dzienniku lub konferencji | Ciąg | Brak
VSN | Krótka nazwa miejsca w dzienniku lub konferencji | Ciąg | Brak

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> Nie można bezpośrednio zażądać atrybutów InvertedAbstract jako zwracanego atrybutu. Jeśli potrzebujesz pojedynczego atrybutu, musisz zażądać atrybutu "IA" najwyższego poziomu, tj., aby uzyskać IA. Atrybuty żądania IndexLength = IA

Nazwa | Opis | Typ | Operacje
--- | --- | --- | ---
IndexLength | Liczba elementów w indeksie (liczba wyrazów abstrakcyjnych) | Int32 | Brak
InvertedIndex | Lista wyrazów abstrakcyjnych i odpowiadających im pozycji w oryginalnym streszczeniu (np. [{": [0, 15, 30]}, {" brązowy ": [1]}, {" Fox ": [2]}]) | Niestandardowy | Brak

## <a name="source"></a>Element źródłowy

> [!IMPORTANT]
> Atrybutów źródłowych nie można żądać bezpośrednio jako atrybutu zwracanego. Jeśli potrzebujesz pojedynczego atrybutu, musisz zażądać atrybutu najwyższego poziomu "S", tj., aby uzyskać atrybuty żądania = S

Nazwa | Opis | Typ | Operacje
--- | --- | --- | ---
Br | Typ adresu URL źródła (1: HTML, 2: tekst, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS) | Ciąg | Brak
U | Źródłowy adres URL | Ciąg | Brak
