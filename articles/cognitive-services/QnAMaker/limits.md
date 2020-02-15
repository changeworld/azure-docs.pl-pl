---
title: Limity i granice — QnA Maker
description: Usługa QnA Maker ma meta limity dla części bazy wiedzy knowledge base i usługi. Należy zachować wiedzy w ramach tych limitów w celu testowania i publikowania.
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 7fdf45a4a22f6d9ffe123f5998592739402be55f
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252012"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Usługa QnA Maker wiedzy limity i granice

Podane poniżej limity QnA Maker są kombinacją [limitów warstwy cenowej platformy Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) i [limity warstwy cenowej QNA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Musisz znać oba zestawy limitów, aby zrozumieć, jak wiele baz wiedzy można utworzyć dla każdego zasobu i jak duże mogą rosnąć poszczególne bazy wiedzy.

## <a name="knowledge-bases"></a>Bazy wiedzy

Maksymalna liczba baz wiedzy zależy od [limitów warstwy wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Warstwa usługi Azure Wyszukiwanie poznawcze** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**WYJŚCIE S3 HD**|
|---|---|---|---|---|---|----|
|Dozwolona maksymalna liczba opublikowanych baz wiedzy|2|14|49|199|199|2,999|

 Na przykład jeśli warstwa ma 15 dozwolonych indeksów, można opublikować 14 baz wiedzy (1 indeks na opublikowaną bazę wiedzy). Piętnasty indeks, `testkb`, jest używany dla wszystkich baz wiedzy na potrzeby tworzenia i testowania.

## <a name="extraction-limits"></a>Limity wyodrębniania

### <a name="file-naming-constraints"></a>Ograniczenia nazewnictwa plików

Nazwy plików nie mogą zawierać następujących znaków:

|Nie używaj znaku|
|--|
|`'` pojedynczego cudzysłowu|
|`"` podwójnego cudzysłowu|

### <a name="maximum-file-size"></a>Maksymalna wielkość pliku

|Format|Maksymalny rozmiar pliku (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maksymalna liczba plików

Maksymalna liczba plików, które można wyodrębnić, i maksymalny rozmiar pliku opiera się na **[QNA Maker limitów warstwy cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Maksymalna liczba linków głębokiego adresu URL

Maksymalna liczba linków głębokiego, które mogą być przeszukiwane w celu wyodrębnienia bazami ze strony adresu URL, to **20**.

## <a name="metadata-limits"></a>Limity metadanych

Metadane są przechowywane i porównywane małymi literami.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Według warstwy cenowej usługi Azure Wyszukiwanie poznawcze

Maksymalna liczba pól metadanych na bazę wiedzy zależy od **[limitów warstwy wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Warstwa usługi Azure Wyszukiwanie poznawcze** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**WYJŚCIE S3 HD**|
|---|---|---|---|---|---|----|
|Pola metadanych maksymalnej dla danej usługi QnA Maker (we wszystkich artykułów bazy wiedzy)|1000|100 *|1000|1000|1000|1000|

### <a name="by-name-and-value"></a>Według nazwy i wartości

W poniższej tabeli wymieniono długość i dopuszczalne znaki nazwy i wartości metadanych.

|Element|Dozwolone znaki|Dopasowanie wzorca wyrażenia regularnego|Maks. liczba znaków|
|--|--|--|--|
|Name (Nazwa)|Udostępnia<br>alfanumeryczne (litery i cyfry)<br>`_` (podkreślenie)|`^[a-zA-Z0-9_]+$`|100|
|Wartość|Zezwala na wszystko z wyjątkiem<br>`:` (dwukropek)<br>`|` (potok pionowy)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limity zawartości bazy wiedzy
Ogólne ograniczenia dotyczące zawartości w bazie wiedzy knowledge base:
* Długość tekstu odpowiedzi: 25 000
* Długość tekstu zapytania: 1000
* Długość tekstu klucz/wartość metadanych: 100
* Obsługiwane znaki dla nazwy metadanych: alfabety, cyfry i `_`
* Obsługiwane znaki dla wartości metadanych: wszystkie z wyjątkiem `:` i `|`
* Długość nazwy pliku: 200
* Obsługiwane formaty plików: "tsv", ".pdf", ".txt", ".docx", "xlsx".
* Maksymalna liczba pytań alternatywnych: 300
* Maksymalna liczba par pytań i odpowiedzi: zależy od wybranej **[warstwy wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** . Para pytań i odpowiedzi mapuje do dokumentu na indeks Wyszukiwanie poznawcze platformy Azure.
* Adres URL/strona HTML: 1 000 000 znaków

## <a name="create-knowledge-base-call-limits"></a>Utwórz limity wywołania w bazie wiedzy Knowledge base:
Reprezentują one limity dla każdej akcji tworzenia bazy wiedzy; oznacza to, że kliknięcie pozycji *Utwórz KB* lub wywołanie interfejsu API usługi.
* Maksymalna liczba pytań alternatywnych na odpowiedź: 300
* Maksymalna liczba adresów URL: 10
* Maksymalna liczba plików: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizowanie limitów wywołanie bazy wiedzy
Reprezentują one limity dla każdej akcji aktualizacji; oznacza to, że kliknięcie przycisku *Zapisz i pouczenie* lub wywołanie interfejsu API UpdateKnowledgeBase.
* Długość nazwy źródła: 300
* Maksymalna liczba dodanych lub usuniętych pytań alternatywnych: 300
* Maksymalna liczba pól metadane dodawane lub usuwane: 10
* Maksymalna liczba adresów URL, które mogą być odświeżane: 5

## <a name="next-steps"></a>Następne kroki

Dowiedz się, kiedy i jak zmienić [warstwy cenowe usług](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).
