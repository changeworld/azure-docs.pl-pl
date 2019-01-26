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
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 51e494101b13b3020f79c9acfc4d853cee995d7b
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911426"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Usługa QnA Maker wiedzy limity i granice
Pełną listę limitów przez narzędzie QnA Maker.

## <a name="knowledge-bases"></a>Bazy wiedzy

* Maksymalna liczba baz wiedzy na podstawie [limity warstwy usługi Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Warstwy usługi Azure Search** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**WYSOKA GĘSTOŚĆ S3**|
|---|---|---|---|---|---|----|
|Maksymalna liczba opublikowanych baz wiedzy dozwolone|2|14|49|199|199|2,999|

 Na przykład warstwę ma 15 dozwolonych indeksów, można opublikować 14 bazy wiedzy (1 indeks na opublikowane bazy wiedzy knowledge base). Indeks piętnasty `testkb`, jest używany dla wszystkich baz wiedzy do tworzenia i testowania. 

## <a name="extraction-limits"></a>Limity wyodrębniania
* Maksymalna liczba plików, które można wyodrębnić i maksymalny rozmiar pliku: Zobacz [ceny QnAMaker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)
* Maksymalna liczba łącz głębokiego, które mogą być przeszukiwane do wyodrębnienia znacznie ze stron HTML — często zadawane pytania: 20

## <a name="metadata-limits"></a>Limity metadanych
* Maksymalna liczba pól metadanych w bazie wiedzy, na podstawie [limity warstwy usługi Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Warstwy usługi Azure Search** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**WYSOKA GĘSTOŚĆ S3**|
|---|---|---|---|---|---|----|
|Pola metadanych maksymalnej dla danej usługi QnA Maker (we wszystkich artykułów bazy wiedzy)|1000|100 *|1000|1000|1000|1000|

## <a name="knowledge-base-content-limits"></a>Limity zawartości bazy wiedzy
Ogólne ograniczenia dotyczące zawartości w bazie wiedzy knowledge base:
* Długość tekstu odpowiedzi: 25,000
* Długość tekstu zapytania: 1000
* Długość tekstu klucz/wartość metadanych: 100
* Obsługiwane znaki dla nazwy metadanych: Litery, cyfry oraz _  
* Obsługiwanych znaków w wartości metadanych: Wszystkie regiony z wyjątkiem: i | 
* Długość nazwy pliku: 200
* Obsługiwane formaty plików: "tsv", ".pdf", ".txt", ".docx", "xlsx".
* Maksymalna liczba alternatywne pytania: 100
* Maksymalna liczba par odpowiedź na pytanie: Zależy od [Warstwa usługi Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) wybrane 

## <a name="create-knowledge-base-call-limits"></a>Utwórz limity wywołania w bazie wiedzy Knowledge base:
Reprezentują one tworzenie limity dla każdego działania w bazie wiedzy knowledge base. oznacza to kliknięcie *tworzenie bazy wiedzy* lub wywołanie interfejsu API CreateKnowledgeBase.
* Maksymalna liczba alternatywne pytań na odpowiedzi: 100
* Maksymalna liczba adresów URL: 10
* Maksymalna liczba plików: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizowanie limitów wywołanie bazy wiedzy
Te reprezentują limity dla każdej akcji aktualizacji; oznacza to kliknięcie *Zapisz i szkolenie* lub wywołanie interfejsu API UpdateKnowledgeBase.
* Długość nazwy źródła: 300
* Maksymalna liczba alternatywne pytania dodane lub usunięte: 100
* Maksymalna liczba pola metadanych dodane lub usunięte: 10
* Maksymalna liczba adresów URL, które mogą być odświeżane: 5

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, kiedy i jak zmienić warstwy usługi:

* [Usługa QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): po użytkownik musi mieć więcej pytań i odpowiedzi w wiedzy, poza Twoja bieżąca warstwa Uaktualnij warstwę cenową usługi QnA Maker.
* [Wyszukiwanie](how-to/upgrade-qnamaker-service.md#upgrade-app-service) — w przypadku bazy wiedzy musi obsługiwać więcej żądań z aplikacji klienckiej, Uaktualnij usługi app service w warstwie cenowej.
* [Usługa App service](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): po użytkownik chce mieć wiele baz wiedzy, Uaktualnij warstwę cenową usługi Azure Search.