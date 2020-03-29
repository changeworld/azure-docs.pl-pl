---
title: Granice i granice - QnA Maker
description: QnA Maker posiada meta-limity dla części bazy wiedzy i usług. Ważne jest, aby utrzymać swoją bazę wiedzy w tych granicach, aby przetestować i opublikować.
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 6375a6c6efc0c7016d9947e04e9479385aa80af5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273348"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker podstawy wiedzy granice i granice

Limity programu QnA Maker podane poniżej są kombinacją [limitów warstw cenowych usługi Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) i [limitów warstw cenowych QnA Maker.](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/) Musisz znać oba zestawy limitów, aby zrozumieć, ile baz wiedzy można utworzyć na zasób i jak duża może się rozwijać każda baza wiedzy.

## <a name="knowledge-bases"></a>Bazy wiedzy

Maksymalna liczba baz wiedzy jest oparta na [limitach warstw usługi Azure Cognitive Search.](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Warstwa usługi Azure Cognitive Search** | **Bezpłatna** | **Podstawowa (Basic)** |**S1** | **S2**| **S3** |**S3 (wysoka gęstość)**|
|---|---|---|---|---|---|----|
|Maksymalna dozwolona liczba opublikowanych baz wiedzy|2|14|49|199|199|2,999|

 Na przykład jeśli warstwa ma 15 dozwolonych indeksów, można opublikować 14 baz wiedzy (1 indeks na opublikowaną bazę wiedzy). Piętnasty `testkb`indeks, jest używany dla wszystkich baz wiedzy do tworzenia i testowania.

## <a name="extraction-limits"></a>Limity ekstrakcji

### <a name="file-naming-constraints"></a>Ograniczenia nazewnictwa plików

Nazwy plików mogą nie zawierać następujących znaków:

|Nie używaj znaków|
|--|
|Oferta pojedyncza`'`|
|Podwójna wycena`"`|

### <a name="maximum-file-size"></a>Maksymalna wielkość pliku

|Format|Maksymalny rozmiar pliku (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maksymalna liczba plików

Maksymalna liczba plików, które można wyodrębnić i maksymalny rozmiar pliku jest oparta na **[limitach warstwy cenowej QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**.

### <a name="maximum-number-of-deep-links-from-url"></a>Maksymalna liczba głębokich linków z adresu URL

Maksymalna liczba głębokich linków, które mogą być indeksowane do wyodrębniania QnAs ze strony ADRESU URL jest **20**.

## <a name="metadata-limits"></a>Limity metadanych

Metadane są prezentowane jako para klucza `product:windows 10`tekstowego:wartość, na przykład . Jest przechowywany i porównywany w małe litery.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Według warstwy cenowej usługi Azure Cognitive Search

Maksymalna liczba pól metadanych na bazę wiedzy jest oparta na **[limitach warstwy usługi Azure Cognitive Search.](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)**

|**Warstwa usługi Azure Cognitive Search** | **Bezpłatna** | **Podstawowa (Basic)** |**S1** | **S2**| **S3** |**S3 (wysoka gęstość)**|
|---|---|---|---|---|---|----|
|Maksymalna liczba pól metadanych na usługę QnA Maker (we wszystkich kb)|1000|100*|1000|1000|1000|1000|

### <a name="by-name-and-value"></a>Według nazwy i wartości

Długość i dopuszczalne znaki dla nazwy i wartości metadanych są wymienione w poniższej tabeli.

|Element|Dozwolone znaki|Dopasowanie wzorca regex|Maksymalne znaki|
|--|--|--|--|
|Nazwa (klucz)|Pozwala<br>alfanumeryczne (litery i cyfry)<br>`_`(podkreślenie)<br> Nie może zawierać spacji.|`^[a-zA-Z0-9_]+$`|100|
|Wartość|Pozwala na wszystko, z wyjątkiem<br>`:`(dwukropek)<br>`|`(rura pionowa)<br>Dozwolona jest tylko jedna wartość.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limity zawartości bazy wiedzy
Ogólne limity zawartości bazy wiedzy:
* Długość tekstu odpowiedzi: 25 000
* Długość tekstu pytania: 1000
* Długość tekstu klucza/wartości metadanych: 100
* Obsługiwane znaki dla nazwy metadanych: alfabety, cyfry i`_`
* Obsługiwane znaki dla wartości metadanych: Wszystkie z wyjątkiem `:` i`|`
* Długość nazwy pliku: 200
* Obsługiwane formaty plików: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Maksymalna liczba alternatywnych pytań: 300
* Maksymalna liczba par pytań i odpowiedzi: zależy od wybranej **[warstwy usługi Azure Cognitive Search.](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** Para pytań i odpowiedzi jest mapowana do dokumentu w indeksie usługi Azure Cognitive Search.
* Strona URL/HTML: 1 milion znaków

## <a name="create-knowledge-base-call-limits"></a>Tworzenie limitów połączeń bazy wiedzy:
Reprezentują one limity dla każdego tworzenia akcji bazy wiedzy; oznacza to, że kliknij przycisk *Utwórz KB* lub wywołanie createKnowledgeBase INTERFEJSU API.
* Maksymalna liczba alternatywnych pytań na odpowiedź: 300
* Maksymalna liczba adresów URL: 10
* Maksymalna liczba plików: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizowanie limitów połączeń bazy wiedzy Knowledge base
Reprezentują one limity dla każdej akcji aktualizacji; oznacza to, że kliknięcie *przycisku Zapisz i trenuj* lub wywołanie interfejsu API UpdateKnowledgeBase.
* Długość każdej nazwy źródła: 300
* Maksymalna liczba dodatkowych pytań dodanych lub usuniętych: 300
* Maksymalna liczba dodanych lub usuniętych pól metadanych: 10
* Maksymalna liczba adresów URL, które można odświeżyć: 5

## <a name="next-steps"></a>Następne kroki

Dowiedz się, kiedy i jak zmienić [warstwy cenowe usług](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku).
