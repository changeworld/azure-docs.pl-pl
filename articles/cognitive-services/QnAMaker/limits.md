---
title: Limity Maker — strona główna - kognitywnych usług platformy Azure | Dokumentacja firmy Microsoft
description: Limity Maker — strona główna
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 4d2bafad08ffab76743cb802733a5d2f01a97f06
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "35356028"
---
# <a name="qna-maker-limits"></a>Limity Maker — strona główna
Pełną listę limity między Maker — strona główna.

## <a name="knowledge-bases"></a>Baz wiedzy

* Maksymalna liczba baz wiedzy na podstawie [limity warstwy usługi Azure Search](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)

|**Warstwy usługi Azure Search** | **W warstwie bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maksymalna liczba opublikowanych baz wiedzy dozwolonych (maksymalna liczba indeksów — 1 (zarezerwowane dla testu)|2|14|49|199|199|2999|

## <a name="extraction-limits"></a>Limity wyodrębniania
* Maksymalna liczba plików, które można wyodrębnić i maksymalny rozmiar pliku: zobacz [QnAMaker ceny](https://azure.microsoft.com/en-in/pricing/details/cognitive-services/qna-maker/)
* Maksymalna liczba łącz dokładnego, które mogą być przeszukiwane w celu wyodrębnienia QnAs ze stron HTML — często zadawane pytania: 20

## <a name="metadata-limits"></a>Limity metadanych
* Maksymalna liczba pól metadanych w bazie wiedzy, na podstawie [limity warstwy usługi Azure Search](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)

|**Warstwy usługi Azure Search** | **W warstwie bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maksymalna metadanych pól na Maker — strona główna usługi (za pośrednictwem wszystkich KB/s)|1000|100 *|1000|1000|1000|1000|

## <a name="knowledge-base-content-limits"></a>Limity zawartości wiedzy
Limity ogólnej zawartości w bazie wiedzy knowledge base:
* Długość tekstu odpowiedzi: 250000
* Długość tekstu zapytania: 1000
* Długość tekstu klucza i wartości metadanych: 100
* Obsługiwane znaki dla nazwy metadanych: małych liter, cyfr i _  
* Obsługiwane znaki dla wartości metadanych: wszystkie z wyjątkiem: i | 
* Długość nazwy pliku: 200
* Obsługiwane formaty plików: ".tsv", "PDF", "txt", ".docx", "xlsx".
* Maksymalna liczba pytań alternatywny: 100
* Maksymalna liczba par odpowiedź na pytanie: zależy od [warstwy usługi Azure Search](https://docs.microsoft.com/en-in/azure/search/search-limits-quotas-capacity#document-limits) wybrany 

## <a name="create-knowledge-base-call-limits"></a>Utwórz limity wywołania bazy wiedzy Knowledge base:
Te stanowią limity dla każdego utworzone bazy wiedzy knowledge base działania. oznacza to, klikając pozycję *utworzyć KB* lub wywołanie interfejsu API CreateKnowledgeBase.
* Maksymalna liczba alternatywny pytań na odpowiedzi: 100
* Maksymalna liczba adresów URL: 10
* Maksymalna liczba plików: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizowanie limitów wywołania wiedzy
Reprezentuje limity dla każdej akcji update; oznacza to, klikając pozycję *Zapisz i uczenia* lub wywołanie interfejsu API UpdateKnowledgeBase.
* Długość nazwy źródła: 300
* Maksymalna liczba pytań alternatywny pytania dodane lub usunięte: 100
* Maksymalna liczba pól metadanych dodane lub usunięte: 10
* Maksymalna liczba adresów URL, które mogą być odświeżane: 5
