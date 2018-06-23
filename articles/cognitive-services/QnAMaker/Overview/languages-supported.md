---
title: Obsługiwane języki - Maker — strona główna - Azure kognitywnych usług | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie języki są obsługiwane w przypadku Maker — strona główna.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b210f59129a962046787b27d003c2872a54f6c8e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349244"
---
# <a name="supported-languages"></a>Obsługiwane języki

Język bazy wiedzy wpływa na możliwość Maker — strona główna automatycznego wyodrębniania pytania i odpowiedzi z [źródeł](../Concepts/data-sources-supported.md), a także przydatność wyników Maker — strona główna zawiera w odpowiedzi na zapytania użytkownika.

## <a name="auto-extraction"></a>Automatycznego wyodrębniania
Maker — strona główna obsługuje wyodrębniania pytań i odpowiedzi w dowolnej strony języka, ale skuteczności wyodrębniania jest znacznie wyższa dla następujących języków, ponieważ Maker — strona główna użyto słowa kluczowe do identyfikacji pytania.

|Obsługiwane języki| Ustawienia regionalne|
|-----|----|
|Polski|EN-*|
|Francuski|FR-*|
|Włoski|IT-*|
|Niemiecki|Niemcy — *|
|Hiszpański|ES-*|

## <a name="query-matching-and-relevance"></a>Zapytania pasujący i przydatności
Zależy od Maker — strona główna [analizatorów języka](https://docs.microsoft.com/en-us/rest/api/searchservice/language-support) w usłudze Azure search udostępnia wyników. Specjalne funkcje ponownie klasyfikacji są dostępne dla En-* języki, które umożliwiają lepsze przydatności.

Maker — strona główna automatycznie wykrywa język w bazie wiedzy podczas tworzenia i odpowiednio ustawia analizatora. Możesz utworzyć baz wiedzy w następujących językach. Odczyt [to](../How-To/language-knowledge-base.md) Aby uzyskać więcej informacji na temat obsługi języków Maker — strona główna.


> [!Tip]
> Analizatorów języka, gdy zostanie ustawiona, nie można zmienić. Ponadto analizatora języka dotyczy baz wiedzy w [Maker — strona główna usługi](../How-To/set-up-qnamaker-service-azure.md). Jeśli planujesz umieszczenie bazy wiedzy w innym języku, można je utworzyć w oddzielnych Maker — strona główna usług.

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
