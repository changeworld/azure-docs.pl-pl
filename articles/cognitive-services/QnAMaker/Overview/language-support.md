---
title: Obsługa języków — QnA Maker
titleSuffix: Azure Cognitive Services
description: Lista kultury, naturalny języki obsługiwane przez narzędzie QnA Maker bazy wiedzy. Nie należy mieszać języki obsługiwane w tej samej bazie wiedzy knowledge base.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/04/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 5a4938ec37cf006e5cd6342687e2c50cadd5272e
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731074"
---
# <a name="language-and-region-support-for-qna-maker"></a>Region i język pomocy technicznej dotyczące usługi QnA Maker

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

## <a name="query-matching-and-relevance"></a>Zapytania pasujący i istotność
Usługa QnA Maker jest zależna od [analizatory języka](https://docs.microsoft.com/rest/api/searchservice/language-support) w usłudze Azure search za dostarczanie wyników. Specjalne funkcje ponownie klasyfikacji są dostępne dla En-* języki, które umożliwiają lepsze istotności.

Możliwości usługi Azure Search jest równoważny w obsługiwanych językach, usługa QnA Maker ma dodatkowe tematycznie znajdująca się nad wynikami wyszukiwania platformy Azure. W tym oceniania modelu semantycznego specjalne są używane i funkcji na podstawie programu word za pomocą en-*, które nie są jeszcze dostępne dla innych języków. Firma Microsoft nie należy wprowadzać te dostępne, ponieważ należą one do wewnętrznego funkcjonowania oceniania. 

Usługa QnA Maker automatycznie wykrywa język bazy wiedzy knowledge base podczas tworzenia i ustawia odpowiednio analizatora. Można utworzyć baz wiedzy w następujących językach. Odczyt [to](../How-To/language-knowledge-base.md) więcej informacji o tym, jak usługa QnA Maker obsługuje języki.


> [!Tip]
> Analizatory języków, gdy zostanie ustawiona, nie można jej zmienić. Ponadto analizatora języków ma zastosowanie do wszystkich baz wiedzy w [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Jeśli użytkownik chce posiadać baz wiedzy w innym języku, można je utworzyć w obszarze oddzielne usługi QnA Maker.

|Obsługiwane języki|
|-----|
|Arabski|
|Armeński|, 
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
