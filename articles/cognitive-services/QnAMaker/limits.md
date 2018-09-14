---
title: Limity i granice — QnA Maker
titleSuffix: Azure Cognitive Services
description: Pełną listę limitów przez narzędzie QnA Maker.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: nstulasi
ms.openlocfilehash: da1de0c36033823bd8f615411eed183ffe9701b6
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574552"
---
# <a name="qna-maker-limits"></a>Limity usługi QnA Maker
Pełną listę limitów przez narzędzie QnA Maker.

## <a name="knowledge-bases"></a>Bazy wiedzy

* Maksymalna liczba baz wiedzy na podstawie [limity warstwy usługi Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Warstwy usługi Azure Search** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**WYSOKA GĘSTOŚĆ S3**|
|---|---|---|---|---|---|----|
|Maksymalna liczba opublikowanych baz wiedzy dozwolone (maksymalna liczba indeksów – 1 (zarezerwowane dla testu)|2|14|49|199|199|2999|

## <a name="extraction-limits"></a>Limity wyodrębniania
* Maksymalna liczba plików, które można wyodrębnić i maksymalny rozmiar pliku: zobacz [ceny QnAMaker](https://azure.microsoft.com/en-in/pricing/details/cognitive-services/qna-maker/)
* Maksymalna liczba łącz głębokiego, które mogą być przeszukiwane do wyodrębnienia znacznie ze stron HTML — często zadawane pytania: 20

## <a name="metadata-limits"></a>Limity metadanych
* Maksymalna liczba pól metadanych w bazie wiedzy, na podstawie [limity warstwy usługi Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Warstwy usługi Azure Search** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**WYSOKA GĘSTOŚĆ S3**|
|---|---|---|---|---|---|----|
|Pola metadanych maksymalnej dla danej usługi QnA Maker (we wszystkich artykułów bazy wiedzy)|1000|100 *|1000|1000|1000|1000|

## <a name="knowledge-base-content-limits"></a>Limity zawartości bazy wiedzy
Ogólne ograniczenia dotyczące zawartości w bazie wiedzy knowledge base:
* Długość tekstu odpowiedzi: 250000
* Długość tekstu zapytania: 1000
* Długość tekstu klucz/wartość metadanych: 100
* Obsługiwane znaki dla nazwy metadanych: litery, cyfry oraz _  
* Znaki obsługiwane w wartości metadanych: wszystkie regiony z wyjątkiem: i | 
* Długość nazwy pliku: 200
* Obsługiwane formaty plików: "tsv", ".pdf", ".txt", ".docx", "xlsx".
* Maksymalna liczba pytań alternatywnych: 100
* Maksymalna liczba par odpowiedź na pytanie: zależy od [Warstwa usługi Azure Search](https://docs.microsoft.com/en-in/azure/search/search-limits-quotas-capacity#document-limits) wybrane 

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
