---
title: Obsługa języków — QnA Maker
titleSuffix: Azure Cognitive Services
description: Język bazy wiedzy ma wpływ na QnA Maker możliwość autowyodrębniania pytań i odpowiedzi ze źródeł, a także znaczenia QnA Maker wyników w odpowiedzi na zapytania użytkowników. Lista kultury, naturalny języki obsługiwane przez narzędzie QnA Maker bazy wiedzy. Nie należy mieszać języki obsługiwane w tej samej bazie wiedzy knowledge base.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec09e5cd053774902083c6dd042c1ed4b293d20
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066677"
---
# <a name="language-support-for-qna-maker"></a>Obsługa języka dla QnA Maker

Język wiedzy ma wpływ na możliwości usługi QnA Maker automatycznie wyodrębnić pytań i odpowiedzi ze [źródeł](../Concepts/data-sources-supported.md), a także przydatność wyników w odpowiedzi na zapytania użytkowników zapewnia usługi QnA Maker.

## <a name="auto-extraction"></a>Automatycznego wyodrębniania
Usługa QnA Maker obsługuje wyodrębnianie pytań i odpowiedzi na stronie dowolnego języka, ale skuteczności wyodrębniania jest znacznie wyższa dla następujących języków, jak usługa QnA Maker jest używana słowa kluczowe pytania.

|Obsługiwane języki| Ustawienia regionalne|
|-----|----|
|Polski|EN-*|
|Francuski|FR — *|
|Włoski|IT — *|
|Niemiecki|de-*|
|Hiszpański|ES-*|

## <a name="primary-language-detection"></a>Wykrywanie języka podstawowego

Język podstawowy używany do wykrywania jest ustawiany dla zasobu QnA Maker i wszystkich baz wiedzy utworzonych na tym zasobie, gdy pierwszy dokument lub adres URL zostanie dodany do pierwszej bazy wiedzy. Nie można zmienić języka. 

Jeśli użytkownik planuje obsługę wielu języków, muszą mieć nowy zasób QnA Maker dla każdego języka. Dowiedz się, jak [utworzyć bazę wiedzy QNA Maker opartej na języku](../how-to/language-knowledge-base.md).  

Sprawdź język podstawowy, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  
1. Wyszukaj i wybierz zasób Azure Search utworzony w ramach zasobu QnA Maker. Nazwa zasobu Azure Search będzie rozpoczynać się od tej samej nazwy co zasób QnA Maker i będzie miała typ **Usługa wyszukiwania**. Należy pamiętać, że tylko jeden zasób QnA Maker może być połączony z jednym Azure Search zasobem.
1. Na stronie **Przegląd** zasobu wyszukiwania wybierz pozycję **indeksy**. 
1. Wybierz indeks **testkb** .
1. Wybierz kartę **pola** . 
1. Wyświetl kolumnę **analizatora** dla pól **pytania** i **odpowiedzi** . 


## <a name="query-matching-and-relevance"></a>Zapytania pasujący i istotność
Usługa QnA Maker jest zależna od [analizatory języka](https://docs.microsoft.com/rest/api/searchservice/language-support) w usłudze Azure search za dostarczanie wyników. Specjalne funkcje ponownie klasyfikacji są dostępne dla En-* języki, które umożliwiają lepsze istotności.

Możliwości usługi Azure Search jest równoważny w obsługiwanych językach, usługa QnA Maker ma dodatkowe tematycznie znajdująca się nad wynikami wyszukiwania platformy Azure. W tym modelu rangi używamy pewnych specjalnych funkcji semantycznych i opartych na programie Word w EN-*, które nie są jeszcze dostępne dla innych języków. Te funkcje nie są dostępne, ponieważ są one częścią wewnętrznej pracy rangi QnA Maker. 

QnA Maker [Autowykrywanie języka bazy wiedzy](#primary-language-detection) podczas tworzenia i odpowiednio ustawia Analizator. Można utworzyć baz wiedzy w następujących językach. 

|Obsługiwane języki|
|-----|
|Arabski|
|Armeński|
Bengalski|
|Baskijski|
|Bułgarski|
|Kataloński|
|Chinese_Simplified|
|Chinese_Traditional|
|Chorwacki|
|Czeski|
|Duński|
|Holenderski|
|Polski|
|Estoński|
|Fiński|
|Francuski|
|Galicyjski|
|Niemiecki|
|Grecki|
|Gudżarati|
|Hebrajski|
|Hindi|
|Węgierski|
|Islandzki|
|Indonezyjski|
|Irlandzki|
|Włoski|
|Japoński|
|Kannada|
|Koreański|
|Łotewski|
|Litewski|
|Malajalam|
|Malajski|
|Norweski|
|Polski|
|Portugalski|
|Pendżabski|
|Rumuński|
|Rosyjski|
|Serbian_Cyrillic|
|Serbian_Latin|
|Słowacki|
|Słoweński|
|Hiszpański|
|Szwedzki|
|Tamilski|
|Telugu|
|Tajlandzki|
|Turecki|
|Ukraiński|
|Urdu|
|Wietnamski|
