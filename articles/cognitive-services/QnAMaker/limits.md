---
title: Limity i granice — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker ma meta limity dla części bazy wiedzy knowledge base i usługi. Należy zachować wiedzy w ramach tych limitów w celu testowania i publikowania.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/29/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 27526f4940cb7ab538992f3506c1a35a81cec9bc
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165021"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Usługa QnA Maker wiedzy limity i granice

Podane poniżej limity QnA Maker są kombinacją limitów [warstwy cenowej Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) i [QNA Maker ograniczeń warstwy cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Musisz znać oba zestawy limitów, aby zrozumieć, jak wiele baz wiedzy można utworzyć dla każdego zasobu i jak duże mogą rosnąć poszczególne bazy wiedzy.

## <a name="knowledge-bases"></a>Bazy wiedzy

Maksymalna liczba baz wiedzy zależy od [limitów warstwy Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Warstwy usługi Azure Search** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**WYSOKA GĘSTOŚĆ S3**|
|---|---|---|---|---|---|----|
|Dozwolona maksymalna liczba opublikowanych baz wiedzy|2|14|49|199|199|2,999|

 Na przykład jeśli warstwa ma 15 dozwolonych indeksów, można opublikować 14 baz wiedzy (1 indeks na opublikowaną bazę wiedzy). Piętnasty indeks, `testkb`, jest używany dla wszystkich baz wiedzy na potrzeby tworzenia i testowania. 

## <a name="extraction-limits"></a>Limity wyodrębniania

### <a name="maximum-number-of-files"></a>Maksymalna liczba plików

Maksymalna liczba plików, które można wyodrębnić, i maksymalny rozmiar pliku opiera się na **[QNA Maker limitów warstwy cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Maksymalna liczba linków głębokiego adresu URL

Maksymalna liczba linków głębokiego, które mogą być przeszukiwane w celu wyodrębnienia bazami ze strony adresu URL, to **20**.

## <a name="metadata-limits"></a>Limity metadanych

### <a name="by-azure-search-pricing-tier"></a>Według Azure Search warstwy cenowej

Maksymalna liczba pól metadanych na bazę wiedzy zależy od **[limitów warstwy Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Warstwy usługi Azure Search** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**WYSOKA GĘSTOŚĆ S3**|
|---|---|---|---|---|---|----|
|Pola metadanych maksymalnej dla danej usługi QnA Maker (we wszystkich artykułów bazy wiedzy)|1000|100 *|1000|1000|1000|1000|

### <a name="by-name-and-value"></a>Według nazwy i wartości

W poniższej tabeli wymieniono długość i dopuszczalne znaki nazwy i wartości metadanych.

|Element|Dozwolone znaki|Dopasowanie wzorca wyrażenia regularnego|Maks. liczba znaków|
|--|--|--|--|
|Name|Udostępnia<br>alfanumeryczne (litery i cyfry)<br>`_`podkreślenia|`^[a-zA-Z0-9_]+$`|100|
|Value|Zezwala na wszystko z wyjątkiem<br>`:`średnikami<br>`|`(potok pionowy)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limity zawartości bazy wiedzy
Ogólne ograniczenia dotyczące zawartości w bazie wiedzy knowledge base:
* Długość tekstu odpowiedzi: 25,000
* Długość tekstu pytania: 1000
* Długość tekstu klucza/wartości metadanych: 100
* Obsługiwane znaki dla nazwy metadanych: Alfabety, cyfry i`_`  
* Obsługiwane znaki dla wartości metadanych: Wszystkie z `:` wyjątkiem i`|` 
* Długość nazwy pliku: 200
* Obsługiwane formaty plików: "tsv", ".pdf", ".txt", ".docx", "xlsx".
* Maksymalna liczba pytań alternatywnych: 300
* Maksymalna liczba par pytań i odpowiedzi: Zależy od wybranej **[warstwy Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** . Para pytania i odpowiedzi mapuje do dokumentu na Azure Search indeksu. 
* Strona adresu URL/HTML: 1 000 000 znaków

## <a name="create-knowledge-base-call-limits"></a>Utwórz limity wywołania w bazie wiedzy Knowledge base:
Reprezentują one tworzenie limity dla każdego działania w bazie wiedzy knowledge base. oznacza to kliknięcie *tworzenie bazy wiedzy* lub wywołanie interfejsu API CreateKnowledgeBase.
* Maksymalna liczba pytań alternatywnych na odpowiedź: 300
* Maksymalna liczba adresów URL: 10
* Maksymalna liczba plików: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizowanie limitów wywołanie bazy wiedzy
Te reprezentują limity dla każdej akcji aktualizacji; oznacza to kliknięcie *Zapisz i szkolenie* lub wywołanie interfejsu API UpdateKnowledgeBase.
* Długość każdej nazwy źródła: 300
* Maksymalna liczba dodanych lub usuniętych pytań alternatywnych: 300
* Maksymalna liczba dodanych lub usuniętych pól metadanych: 10
* Maksymalna liczba adresów URL, które można odświeżyć: 5

## <a name="next-steps"></a>Następne kroki

Dowiedz się, kiedy i jak zmienić warstwy usług:

* [QNA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): Jeśli potrzebujesz więcej plików źródłowych lub większych dokumentów w bazie wiedzy, poza bieżącą warstwą, Uaktualnij warstwę cenową usługi QnA Maker Service.
* [App Service](how-to/upgrade-qnamaker-service.md#upgrade-app-service): Gdy baza wiedzy musi obpracować więcej żądań z aplikacji klienckiej, należy uaktualnić warstwę cenową usługi App Service.
* [Azure Search](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): Planując posiadanie wielu baz wiedzy, należy uaktualnić warstwę cenową usługi Azure Search.
