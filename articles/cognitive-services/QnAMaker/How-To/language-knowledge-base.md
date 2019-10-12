---
title: Pojęcia dotyczące języka — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker obsługuje zawartość bazy wiedzy w wielu językach. Jednak każda usługa QnA Maker powinna być zarezerwowana dla jednego języka. Pierwsza baza wiedzy utworzona, przeznaczona dla konkretnej usługi QnA Maker, ustawia język tej usługi.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 849c919950c57a1df3b0fb76021de6e10254c7b4
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286376"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Obsługa języka zawartości bazy wiedzy dla QnA Maker

Język dla usługi jest wybierany podczas tworzenia pierwszej bazy wiedzy w zasobie. Wszystkie dodatkowe bazy wiedzy w zasobie muszą znajdować się w tym samym języku. 

Język określa przydatność wyników QnA Maker zapewnia odpowiedzi na zapytania użytkownika.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Jeden język dla wszystkich baz wiedzy w zasobie

QnA Maker umożliwia wybranie języka usługi QnA podczas tworzenia pierwszej bazy wiedzy. Wszystkie bazy wiedzy w zasobie QnA Maker muszą być w tym samym języku. Nie można zmienić tego języka.

Tworzenie baz wiedzy w różnych językach w jednym zasobie ma negatywny wpływ na przydatność wyników QnA Maker zapewnia odpowiedzi na zapytania użytkowników.

Przejrzyj listę [obsługiwanych języków](../overview/language-support.md#languages-supported) i sposób, w jaki Języki wpływają na [dopasowanie i zgodność](#query-matching-and-relevance). 

## <a name="select-language-when-creating-first-knowledge-base"></a>Wybierz język podczas tworzenia pierwszej bazy wiedzy

Wybór języka jest częścią czynności, które należy wykonać, aby utworzyć pierwszą bazę wiedzy w zasobie. 

![Zrzut ekranu portalu QnA Maker wybierania języka dla pierwszej bazy wiedzy](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Dopasowanie i zgodność zapytania
QnA Maker zależy od [analizatorów języka Azure Search](https://docs.microsoft.com/rest/api/searchservice/language-support) w celu uzyskania wyników. 

Azure Search możliwości są dostępne dla obsługiwanych języków, QnA Maker ma dodatkową rangę, która znajduje się powyżej wyników usługi Azure Search. W tym modelu rangi używamy niektórych specjalnych funkcji semantycznych i opartych na programie Word w następujących językach. 

|Języki z dodatkową rangą|
|--|
|Chiński|
|Czeski|
|Holenderski|
|Polski|
|Francuski|
|Niemiecki|
|Węgierski|
|Włoski|
|Japoński|
|Koreański|
|Polski|
|Portugalski|
|Hiszpański|
|Szwedzki|

Ta dodatkowa klasyfikacja jest wewnętrzną pracą rangi QnA Maker.

## <a name="verify-language"></a>Weryfikuj język

Język zasobu QnA Maker można sprawdzić na stronie Ustawienia usługi w QnA Maker.

![Zrzut ekranu portalu QnA Maker strony ustawień usługi](../media/language-support/language-knowledge-base.png) 


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie QnA bot z Azure Bot Service](../Tutorials/create-qna-bot.md)
