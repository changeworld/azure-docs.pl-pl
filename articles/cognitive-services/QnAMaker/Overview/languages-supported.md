---
title: Obsługiwane języki — QnA Maker
titlesuffix: Azure Cognitive Services
description: Język wiedzy ma wpływ na zdolność usługi QnA Maker automatycznie wyodrębnić pytań i odpowiedzi na podstawie źródła, a także przydatność wyników, udostępnia usługi QnA Maker w odpowiedzi na zapytania użytkowników.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: ee04733064ec4e3d131b800fe1f18b27e5127fe8
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45572944"
---
# <a name="supported-languages"></a>Obsługiwane języki

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
