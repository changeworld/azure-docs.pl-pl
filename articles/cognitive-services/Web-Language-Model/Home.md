---
title: Omówienie interfejsu Web Language Model API — Azure Cognitive Services | Microsoft Docs
description: Interfejs Web Language Model API w usługach Microsoft Cognitive Services udostępnia najnowocześniejsze narzędzia do przetwarzania języka naturalnego.
services: cognitive-services
author: piyushbehre
manager: yanbo
ms.service: cognitive-services
ms.component: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: pibehre
ms.openlocfilehash: dc5dc0519e33e024014033ac5260004482b419c2
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096959"
---
# <a name="what-is-the-web-language-model-api-preview"></a>Czym jest interfejs Web Language Model API? (Wersja zapoznawcza)

Interfejs Microsoft Web Language Model API jest usługą w chmurze opartą na protokole REST, która zapewnia najnowocześniejsze narzędzia do przetwarzana języka naturalnego. Dzięki temu interfejsowi API aplikacja może wykorzystać moc danych big data za pośrednictwem modeli językowych przeszkolonych na korpusach w skali Internetu zebranych przez usługę Bing na rynku en-US.

Te wygładzone n-gramowe modele językowe wycofania, obsługujące łańcuchy Markowa maksymalnie 5. rzędu, zostały przeszkolone na podstawie następujących korpusów:

- Tekst podstawowy strony internetowej
- Tekst tytułu strony internetowej
- Tekst zakotwiczenia strony internetowej
- Tekst zapytania wyszukiwania w Internecie

Interfejs Web Language Model API obsługuje maksymalnie 4 operacje wyszukiwania:

1. Wspólne (log10) prawdopodobieństwo wystąpienia sekwencji wyrazów.
2. Warunkowe (log10) prawdopodobieństwo wystąpienia jednego wyrazu w sekwencji poprzedzających go wyrazów.
3. Lista wyrazów (zakończeń), które z największym prawdopodobieństwem nastąpią po danej sekwencji wyrazów.
4. Dzielenie ciągów wyrazów, które nie zawierają spacji.

## <a name="getting-started"></a>Wprowadzenie

1. Zasubskrybuj usługę.
2. Pobierz zestaw [SDK](https://www.github.com/microsoft/cognitive-weblm-windows).
3. Uruchom przykładowy kod zestawu SDK.
4. Zapoznaj się z [dokumentacją interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104), aby uzyskać wszystkie szczegóły punktów końcowych, w tym fragmenty kodu w różnych językach.

## <a name="underlying-technology"></a>Technologia bazowa

Poniższy dokument zawiera szczegółowe informacje na temat rozwoju tych modeli językowych i powinien być cytowany w publikacjach badawczych korzystających z tej usługi:

- [An Overview of Microsoft Web N-gram Corpus and Applications](http://research.microsoft.com/apps/pubs/default.aspx?id=130762) (Omówienie aplikacji i n-gramowego korpusu internetowego firmy Microsoft), NAACL HLT 2010

Kliknij [tutaj](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0), aby uzyskać listę artykułów, w których cytowana jest ta praca.
