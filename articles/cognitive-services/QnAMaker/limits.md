---
title: Limity i granice — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker ma meta limity dla części bazy wiedzy knowledge base i usługi. Należy zachować wiedzy w ramach tych limitów w celu testowania i publikowania.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 772153040ac76f4b7bbee55c48527a841fc69037
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084792"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Usługa QnA Maker wiedzy limity i granice
Pełną listę limitów przez narzędzie QnA Maker.

## <a name="knowledge-bases"></a>Bazy wiedzy

* Maksymalna liczba baz wiedzy na podstawie [limity warstwy usługi Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Warstwy usługi Azure Search** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**WYSOKA GĘSTOŚĆ S3**|
|---|---|---|---|---|---|----|
|Maksymalna liczba opublikowanych baz wiedzy dozwolone (maksymalna liczba indeksów – 1 (zarezerwowane dla testu)|2|14|49|199|199|2,999|

## <a name="extraction-limits"></a>Limity wyodrębniania
* Maksymalna liczba plików, które można wyodrębnić i maksymalny rozmiar pliku: zobacz [ceny QnAMaker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)
* Maksymalna liczba łącz głębokiego, które mogą być przeszukiwane do wyodrębnienia znacznie ze stron HTML — często zadawane pytania: 20

## <a name="metadata-limits"></a>Limity metadanych
* Maksymalna liczba pól metadanych w bazie wiedzy, na podstawie [limity warstwy usługi Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Warstwy usługi Azure Search** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**WYSOKA GĘSTOŚĆ S3**|
|---|---|---|---|---|---|----|
|Pola metadanych maksymalnej dla danej usługi QnA Maker (we wszystkich artykułów bazy wiedzy)|1000|100 *|1000|1000|1000|1000|

## <a name="knowledge-base-content-limits"></a>Limity zawartości bazy wiedzy
Ogólne ograniczenia dotyczące zawartości w bazie wiedzy knowledge base:
* Długość tekstu odpowiedzi: 25 000
* Długość tekstu zapytania: 1000
* Długość tekstu klucz/wartość metadanych: 100
* Obsługiwane znaki dla nazwy metadanych: litery, cyfry oraz _  
* Znaki obsługiwane w wartości metadanych: wszystkie regiony z wyjątkiem: i | 
* Długość nazwy pliku: 200
* Obsługiwane formaty plików: "tsv", ".pdf", ".txt", ".docx", "xlsx".
* Maksymalna liczba pytań alternatywnych: 100
* Maksymalna liczba par odpowiedź na pytanie: zależy od [Warstwa usługi Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) wybrane 

## <a name="create-knowledge-base-call-limits"></a>Utwórz limity wywołania w bazie wiedzy Knowledge base:
Reprezentują one tworzenie limity dla każdego działania w bazie wiedzy knowledge base. oznacza to kliknięcie *tworzenie bazy wiedzy* lub wywołanie interfejsu API CreateKnowledgeBase.
* Maksymalna liczba alternatywne pytań na odpowiedź: 100
* Maksymalna liczba adresów URL: 10
* Maksymalna liczba plików: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizowanie limitów wywołanie bazy wiedzy
Te reprezentują limity dla każdej akcji aktualizacji; oznacza to kliknięcie *Zapisz i szkolenie* lub wywołanie interfejsu API UpdateKnowledgeBase.
* Długość nazwy źródła: 300
* Maksymalna liczba alternatywne pytania dodane lub usunięte: 100
* Maksymalna liczba pól metadane dodawane lub usuwane: 10
* Maksymalna liczba adresów URL, które mogą być odświeżane: 5
