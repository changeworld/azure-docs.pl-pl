---
title: Obsługa języków — QnA Maker
titleSuffix: Azure Cognitive Services
description: Język wiedzy ma wpływ na zdolność usługi QnA Maker automatycznie wyodrębnić pytań i odpowiedzi na podstawie źródła, a także przydatność wyników, udostępnia usługi QnA Maker w odpowiedzi na zapytania użytkowników. Lista kultury, naturalny języki obsługiwane przez narzędzie QnA Maker bazy wiedzy. Nie należy mieszać języki obsługiwane w tej samej bazie wiedzy knowledge base.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 496f7c75e6633089d4ca88a9e3cd7c76ee415780
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922412"
---
# <a name="language-support-for-qna-maker"></a>Obsługa języka dla usługi QnA Maker

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

## <a name="primary-language-detection"></a>Podstawowe wykrywanie języka

Podstawowy język używany do wykrywania jest ustawiona dla zasobów usługi QnA Maker i wszystkich baz wiedzy utworzone na taki zasób, podczas pierwszego dokumentu lub adresu URL jest dodawany do pierwszego bazy wiedzy. Nie można zmienić język. 

Jeśli użytkownik planuje się obsługiwać wiele języków, muszą mieć nowy zasób usługi QnA Maker dla każdego języka. Dowiedz się, jak [tworzenie opartych na języku usługi QnA Maker wiedzy](../how-to/language-knowledge-base.md).  

Sprawdź, podstawowy język wykonując następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  
1. Wyszukaj i wybierz zasób usługi Azure Search, utworzone w ramach zasobu usługi QnA Maker. Nazwa zasobu usługi Azure Search rozpocznie się o takiej samej nazwie, co zasób usługi QnA Maker i będzie miał typ **usługi wyszukiwania**. 
1. Z **Przegląd** strony zasobu wyszukiwania, wybierz opcję **indeksów**. 
1. Wybierz **testkb** indeksu.
1. Wybierz **pola** kartę. 
1. Widok **analizatora** kolumny **pytania** i **odpowiedzi** pola. 


## <a name="query-matching-and-relevance"></a>Zapytania pasujący i istotność
Usługa QnA Maker jest zależna od [analizatory języka](https://docs.microsoft.com/rest/api/searchservice/language-support) w usłudze Azure search za dostarczanie wyników. Specjalne funkcje ponownie klasyfikacji są dostępne dla En-* języki, które umożliwiają lepsze istotności.

Możliwości usługi Azure Search jest równoważny w obsługiwanych językach, usługa QnA Maker ma dodatkowe tematycznie znajdująca się nad wynikami wyszukiwania platformy Azure. W tym modelu oceniania używamy pewne specjalne funkcje semantycznego i opartego na programie word w en-*, które nie są jeszcze dostępne dla innych języków. Firma Microsoft nie udostępnić te funkcje, ponieważ należą one do wewnętrznego funkcjonowania usługi QnA Maker oceniania. 

Usługa QnA Maker [automatycznie wykrywa język bazy wiedzy knowledge base](#primary-language-detection) podczas tworzenia i ustawia odpowiednio analizatora. Można utworzyć baz wiedzy w następujących językach. 

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
