---
title: Pojęcia językowe - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker obsługuje zawartość bazy wiedzy w wielu językach. Jednak każda usługa QnA Maker powinna być zarezerwowana dla jednego języka. Pierwsza utworzona baza wiedzy, kierowana na określoną usługę QnA Maker, ustawia język tej usługi.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220634"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Obsługa języka zawartości bazy wiedzy dla programu QnA Maker

Język usługi jest wybierany podczas tworzenia pierwszej bazy wiedzy w zasobie. Wszystkie dodatkowe bazy wiedzy w zasobie muszą być w tym samym języku.

Język określa trafność wyników, które program QnA Maker zapewnia w odpowiedzi na zapytania użytkowników.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Jeden język dla wszystkich baz wiedzy w zasobach

QnA Maker pozwala wybrać język usługi QnA, tworząc pierwszą bazę wiedzy. Dla wszystkich baz wiedzy w zasobie QnA Maker wszystkie muszą być w tym samym języku. Tego języka nie można zmienić.

Tworzenie baz wiedzy w różnych językach w jednym zasobie negatywnie wpływa na trafność wyników, które program QnA Maker zapewnia w odpowiedzi na zapytania użytkowników.

Przejrzyj listę [obsługiwanych języków](../overview/language-support.md#languages-supported) oraz wpływ języków na [dopasowanie i trafność.](#query-matching-and-relevance)

## <a name="select-language-when-creating-first-knowledge-base"></a>Wybieranie języka podczas tworzenia pierwszej bazy wiedzy

Wybór języka jest częścią kroków tworzenia pierwszej bazy wiedzy w zasobie.

![Zrzut ekranu z portalu QnA Maker przedstawiający wybór języka dla pierwszej bazy wiedzy](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Dopasowywanie i trafność kwerend
Program QnA Maker zależy od [analizatorów języka usługi Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/language-support) w celu zapewnienia wyników.

Podczas gdy możliwości usługi Azure Cognitive Search są na równi dla obsługiwanych języków, QnA Maker ma dodatkowy ranking, który znajduje się powyżej wyników wyszukiwania platformy Azure. W tym modelu ranker używamy niektórych specjalnych funkcji semantycznych i opartych na programach word w następujących językach.

|Języki z dodatkowym rangerem|
|--|
|Chiński|
|Czeski|
|Niderlandzki|
|Polski|
|Francuski|
|Niemiecki|
|węgierski|
|Włoski|
|Japoński|
|Koreański|
|Polski|
|Portugalski|
|Hiszpański|
|szwedzki|

Ten dodatkowy ranking jest wewnętrzną pracą rangi QnA Maker.

## <a name="verify-language"></a>Weryfikowanie języka

Język zasobu QnA Maker można zweryfikować na stronie ustawień usługi w kreatorze QnA.

![Zrzut ekranu przedstawiający portal programu QnA Maker na stronie Ustawienia usługi](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)
