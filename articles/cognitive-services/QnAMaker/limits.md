---
title: Limity i granice — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker ma limity meta dla części bazy wiedzy i usługi. Ważne jest, aby zachować bazę wiedzy w ramach tych limitów w celu testowania i publikowania.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 4effd14029eaaee1e1c22cdb814096820e19e089
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794027"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limity i granice bazy wiedzy QnA Maker

Podane poniżej limity QnA Maker są kombinacją [limitów warstwy cenowej platformy Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) i [limity warstwy cenowej QNA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Musisz znać oba zestawy limitów, aby zrozumieć, jak wiele baz wiedzy można utworzyć dla każdego zasobu i jak duże mogą rosnąć poszczególne bazy wiedzy.

## <a name="knowledge-bases"></a>Bazy wiedzy

Maksymalna liczba baz wiedzy zależy od [limitów warstwy wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Warstwa usługi Azure Wyszukiwanie poznawcze** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**WYJŚCIE S3 HD**|
|---|---|---|---|---|---|----|
|Dozwolona maksymalna liczba opublikowanych baz wiedzy|2|14|49|199|199|2 999|

 Na przykład jeśli warstwa ma 15 dozwolonych indeksów, można opublikować 14 baz wiedzy (1 indeks na opublikowaną bazę wiedzy). Piętnasty indeks, `testkb`, jest używany dla wszystkich baz wiedzy na potrzeby tworzenia i testowania. 

## <a name="extraction-limits"></a>Limity wyodrębniania

### <a name="maximum-number-of-files"></a>Maksymalna liczba plików

Maksymalna liczba plików, które można wyodrębnić, i maksymalny rozmiar pliku opiera się na **[QNA Maker limitów warstwy cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Maksymalna liczba linków głębokiego adresu URL

Maksymalna liczba linków głębokiego, które mogą być przeszukiwane w celu wyodrębnienia bazami ze strony adresu URL, to **20**.

## <a name="metadata-limits"></a>Limity metadanych

### <a name="by-azure-cognitive-search-pricing-tier"></a>Według warstwy cenowej usługi Azure Wyszukiwanie poznawcze

Maksymalna liczba pól metadanych na bazę wiedzy zależy od **[limitów warstwy wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Warstwa usługi Azure Wyszukiwanie poznawcze** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**WYJŚCIE S3 HD**|
|---|---|---|---|---|---|----|
|Maksymalna liczba pól metadanych na usługę QnA Maker (w ramach wszystkich artykułów bazy wiedzy)|1000|100 *|1000|1000|1000|1000|

### <a name="by-name-and-value"></a>Według nazwy i wartości

W poniższej tabeli wymieniono długość i dopuszczalne znaki nazwy i wartości metadanych.

|Element|Dozwolone znaki|Dopasowanie wzorca wyrażenia regularnego|Maks. liczba znaków|
|--|--|--|--|
|Nazwa|Udostępnia<br>alfanumeryczne (litery i cyfry)<br>`_` (podkreślenie)|`^[a-zA-Z0-9_]+$`|100|
|Wartość|Zezwala na wszystko z wyjątkiem<br>`:` (dwukropek)<br>`|` (potok pionowy)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limity zawartości bazy wiedzy
Ogólne limity zawartości w bazie wiedzy:
* Długość tekstu odpowiedzi: 25 000
* Długość tekstu pytania: 1 000
* Długość tekstu klucza/wartości metadanych: 100
* Obsługiwane znaki dla nazwy metadanych: alfabety, cyfry i `_`  
* Obsługiwane znaki dla wartości metadanych: wszystkie z wyjątkiem `:` i `|` 
* Długość nazwy pliku: 200
* Obsługiwane formaty plików: ". tsv", "PDF", ". txt", "docx", "xlsx".
* Maksymalna liczba pytań alternatywnych: 300
* Maksymalna liczba par pytań i odpowiedzi: zależy od wybranej **[warstwy wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** . Para pytań i odpowiedzi mapuje do dokumentu na indeks Wyszukiwanie poznawcze platformy Azure. 
* Adres URL/strona HTML: 1 000 000 znaków

## <a name="create-knowledge-base-call-limits"></a>Utwórz limity wywołań bazy wiedzy:
Reprezentują one limity dla każdej akcji tworzenia bazy wiedzy; oznacza to, że kliknięcie pozycji *Utwórz KB* lub wywołanie interfejsu API usługi.
* Maksymalna liczba pytań alternatywnych na odpowiedź: 300
* Maksymalna liczba adresów URL: 10
* Maksymalna liczba plików: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizuj limity wywołań bazy wiedzy
Reprezentują one limity dla każdej akcji aktualizacji; oznacza to, że kliknięcie przycisku *Zapisz i pouczenie* lub wywołanie interfejsu API UpdateKnowledgeBase.
* Długość poszczególnych nazw źródłowych: 300
* Maksymalna liczba dodanych lub usuniętych pytań alternatywnych: 300
* Maksymalna liczba dodanych lub usuniętych pól metadanych: 10
* Maksymalna liczba adresów URL, które można odświeżyć: 5

## <a name="next-steps"></a>Następne kroki

Dowiedz się, kiedy i jak zmienić [warstwy cenowe usług](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).
